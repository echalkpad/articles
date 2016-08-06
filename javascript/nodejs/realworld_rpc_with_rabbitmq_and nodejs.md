
[Original URL](https://facundoolano.wordpress.com/2016/06/26/real-world-rpc-with-rabbitmq-and-node-js/)

> tl;dr: use the direct reply-to feature to implement RPC in RabbitMQ. I'm currently working on a platform that relies heavily on RPC over RabbitMQ to move incoming requests through a...

_<span>tl;dr: use the <a href="https://www.rabbitmq.com/direct-reply-to.html">direct reply-to</a> feature to implement RPC in RabbitMQ.</span>_

I'm currently working on a platform that relies heavily on RPC over RabbitMQ to move incoming requests through a chain of Node.JS worker processes. The high level setup for RPC is well described in RabbitMQ's documentation; let's steal their diagram:

![python-six](https://facundoolano.files.wordpress.com/2016/06/python-six.png?w=594)

We grew our RPC client code based on the [JavaScript tutorial](https://www.rabbitmq.com/tutorials/tutorial-six-javascript.html), using the [amqp.node](https://github.com/squaremo/amqp.node) module. The first --admittedly naive-- implementation just created a new connection, channel and queue per request and killed the connection after getting a reply:

```
const sendRPCMessage = (settings, message, rpcQueue) =>
 amqp.connect(settings.url, settings.socketOptions)
 .then((conn) => conn.createChannel())
 .then((channel) => channel.assertQueue('', settings.queueOptions)
 .then((replyQueue) => new Promise((resolve, reject) => {
 const correlationId = uuid.v4();
 const msgProperties = {
 correlationId,
 replyTo: replyQueue.queue
 };

 function consumeAndReply (msg) {
 if (!msg) return reject(Error.create('consumer cancelled by rabbitmq'));

 if (msg.properties.correlationId === correlationId) {
 resolve(msg.content);
 }
 }

 channel.consume(replyQueue.queue, consumeAndReply, {noAck: true})
 .then(() => channel.sendToQueue(rpcQueue, new Buffer(message), msgProperties));
 })));
```

That got us a long way during development but obviously failed to perform under non-trivial loads. What was more shocking is that it got dramatically worse when running it on a RabbitMQ cluster.

So we needed to refactor our client code. The problem is that [most](https://www.rabbitmq.com/tutorials/tutorial-six-javascript.html) [examples](https://github.com/squaremo/amqp.node/blob/master/examples/tutorials/rpc_client.js) show how to send one-off RPC messages, but aren't that clear on how the approach would be used at scale on a long-lived process. We obviously needed to reuse the connection but how about the channel? Should I create a new callback queue per incoming request or a single one per client?

## Using a single reply-to queue per client

Based on the tutorial, I understood that the sensible approach was to reuse the queue from which the client consumed the RPC replies:

> In the method presented above we suggest creating a callback queue for every RPC request. That's pretty inefficient, but fortunately there is a better way – let's create a single callback queue per client. That raises a new issue, having received a response in that queue it's not clear to which request the response belongs. That's when the correlation_id property is used.

We were already checking the `correlationId`, and just needed to create the reply-to queue in advance:

```
const createClient = (settings) =>
 amqp.connect(settings.url, settings.socketOptions)
 .then((conn) => conn.createChannel())
 .then((channel) => channel.assertQueue('', settings.queueOptions)
 .then((replyQueue) => {
 channel.replyQueue = replyQueue.queue;
 return channel;
 }));
```

I thought that would be enough to make sure the right consumer got the right message, but in practice I found that each message was always delivered to the first consumer. Therefore, I needed to cancel the consumer after the reply was processed:

```
const sendRPCMessage = (channel, message, rpcQueue) =>
 new Promise((resolve, reject) => {
 const correlationId = uuid.v4();
 const msgProperties = {
 correlationId,
 replyTo: channel.replyQueue
 };

 function consumeAndReply (msg) {
 if (!msg) return reject(Error.create('consumer cancelled by rabbitmq'));

 if (msg.properties.correlationId === correlationId) {
 channel.cancel(correlationId)
 .then(() => resolve(resolve(msg.content)));
 }
 }

 channel.consume(channel.replyQueue, consumeAndReply, {
 noAck: true,
 // use the correlationId as a consumerTag to cancel the consumer later
 consumerTag: correlationId
 })
 .then(() => channel.sendToQueue(rpcQueue, new Buffer(message), msgProperties));
 });
```

Enough? Only if the client processed one request at a time. As soon as I added some concurrency I saw that some of the messages were not handled at all. They were picked up by the wrong consumer, which ignored them because of the `correlationId` check, so they were lost. I needed to do something about unexpected message handling.

## Requeuing unexpected messages

I tried using `nack` when a consumer received a reply to a message with an unexpected `correlationId`:

```
function consumeAndReply (msg) {
 if (!msg) return reject(Error.create('consumer cancelled by rabbitmq'));

 if (msg.properties.correlationId === correlationId) {
 channel.ack(msg);
 channel.cancel(correlationId)
 .then(() => resolve(resolve(msg.content)));
 } else {
 channel.nack(msg);
 }
}
```

Now the messages seemed to be handled, eventually. Only they weren't: when I increased the load I saw message loss again. Further inspection revealed that the consumers were entering a weird infinite loop:

```
Consumer A gets message B; message B requeued
Consumer B gets message C; Message C requeued
Consumer C gets message A; Message A requeued
```

Repeated ad-infinitum. The same behaviour was reproduced using every possible combination of `nack`, `reject` and `sendToQueue` to send back the message.

[Googling the issue](http://rabbitmq.1065348.n5.nabble.com/Push-to-back-of-Queue-on-NAck-td28421.html), I read about the possibility of using [Dead letter exchanges](https://www.rabbitmq.com/dlx.html) to handle those cases. But having to manually requeue unexpected messages felt weird enough; introducing a new exchange and queue sounded like a lot of effort to handle what should be a pretty standard use case for RPC. Better to take a step back.

## Using a new reply-to queue per request

So I went back to a reply-to queue per request. This was marginally better than our initial approach since now at least we were recycling the connection and the channel. What's more, that appeared to be the standard way to do RPC in RabbitMQ according to the [few](http://stackoverflow.com/a/31705146/993769) [spots](https://github.com/rudijs/amqp.node-rpc-factory) where I found non-tutorial implementation details, so, as Paul Graham would say, we wouldn't get in trouble for using it.

And it worked well for us as long as we run a single RabbitMQ instance. When we moved to a RabbitMQ cluster the performance was pretty much the same as when we were creating connections like there was no tomorrow.

## Using direct reply-to

We were seriously considering dropping the RabbitMQ cluster altogether (which meant turning our broker into a single point of failure), when I came across the link to the [direct reply-to documentation](https://www.rabbitmq.com/direct-reply-to.html). The first interesting thing there was that it confirmed why we were seeing such bad performance when running a RabbitMQ cluster:

> The client can declare a single-use queue for each request-response pair. But this is inefficient; even a transient unmirrored queue can be expensive to create and then delete (compared with the cost of sending a message). This is especially true in a cluster as all cluster nodes need to agree that the queue has been created, even if it is unmirrored.

Direct reply-to uses a pseudo-queue instead, avoiding the queue declaration cost. And fortunately it was fairly straightforward to implement:

```
const createClient = (settings) => amqp.connect(settings.url, settings.socketOptions)

const sendRPCMessage = (client, message, rpcQueue) => conn.createChannel()
 .then((channel) => new Promise((resolve, reject) => {
 const replyToQueue = 'amq.rabbitmq.reply-to';
 const timeout = setTimeout(() => channel.close(), 10000);

 const correlationId = uuid.v4();
 const msgProperties = {
 correlationId,
 replyTo: replyToQueue
 };

 function consumeAndReply (msg) {
 if (!msg) return reject(Error.create('consumer cancelled by rabbitmq'));

 if (msg.properties.correlationId === correlationId) {
 resolve(msg.content);
 clearTimeout(timeout);
 channel.close();
 }
 }

 channel.consume(replyToQueue, consumeAndReply, {noAck: true})
 .then(() => channel.sendToQueue(rpcQueue, new Buffer(content), msgProperties))
 });
```

This worked just as we expected, even in the cluster. As the code shows, we still create a new channel per request and we need to handle its closing, even if the response never comes. Reusing a single channel per client didn't seem to [play along well](https://github.com/squaremo/amqp.node/issues/259) with direct reply-to, but so far we haven't found this to be an issue.

## Like this:

<span class="button">
  <span>Like</span>
</span>

 

<span class="loading">Loading…</span>

[]()

## _Related_
