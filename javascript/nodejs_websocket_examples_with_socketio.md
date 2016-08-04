# Node.js Websocket Examples with Socket.io

[Original URL](http://stackabuse.com/node-js-websocket-examples-with-socket-io/)

> What are Websockets? Over the past few years, a new type of communication started to emerge on the web and in mobile apps, called websockets. This protocol has been long-awaited and was finally...

## What are Websockets?

Over the past few years, a new type of communication started to emerge on the web and in mobile apps, called [websockets](https://en.wikipedia.org/wiki/WebSocket). This protocol has been long-awaited and was finally standardized by the IETF in 2011, paving the way for widespread use.

This new protocol opens up a much faster and more efficient line of communication to the client. Like HTTP, websockets run on top of a TCP connection, but they're much faster because we don't have to open a new connection for each time we want to send a message since the connection is kept alive for as long as the server or client wants.

Even better, since the connection never dies we finally have full-duplex communication available to us, meaning we can _push data to the client instead of having to wait for them to ask for data from the server_. This allows for data to be communicated back and forth, which is ideal for things like real-time chat applications, or even games.

## How do Websockets Work?

At its core, a websocket is just a TCP connection that allows for full-duplex communication, meaning either side of the connection can send data to the other, even at the same time.

To establish this connection, the protocol actually initiates the handshake as a normal HTTP request, but then gets 'upgraded' using the [upgrade request](https://en.wikipedia.org/wiki/HTTP/1.1_Upgrade_header) HTTP header, like this:

```
GET /ws/chat HTTP/1.1 
Host: chat.example.com 
Upgrade: websocket 
Connection: Upgrade 
Sec-WebSocket-Key: q1PZLMeDL4EwLkw4GGhADm== 
Sec-WebSocket-Protocol: chat, superchat 
Sec-WebSocket-Version: 15 
Origin: http://example.com 
```

The server then sends back an HTTP 101 "Switching Protocols" response, acknowledging that the connection is going to be upgraded. Once the this connection has been made, it switches to a bidirectional binary protocol, at which point the application data can be sent.

All the protocol has to do to keep the connection open is send some ping/pong packets, which tells the other side that they're still there. To close the connection, a simple "close connection" packet is sent.

## Some Websocket Examples

Of the many different websocket libraries for Node.js available to us, I chose to use [socket.io](https://github.com/socketio/socket.io) throughout this article because it seems to be the most popular and is, in my opinion, the easiest to use. While each library has its own unique API, they also have many similarities since they're all built on top of the same protocol, so hopefully you'll be able to translate the code below to any library you want to use.

For the HTTP server, I'll be using [Express](http://expressjs.com/), which is the most popular Node server out there. Keep in mind that you can also just use the plain [http](https://nodejs.org/api/http.html) module if you don't need all of the features of Express. Although, since most applications will use Express, that's what we'll be using as well.

**Note**: Throughout these examples I have removed much of the boilerplate code, so some of this code won't work out of the box. In most cases you can refer to the first example to get the boilerplate code.

### Establishing the Connection

In order for a connection to be established between the client and server, the server must do two things:

1. Hook in to the HTTP server to handle websocket connections
2. Serve up the `socket.io.js` client library as a static resource

In the code below, you can see item (1) being done on the 3rd line. Item (2) is done for you (by default) by the `socket.io` library and is served on the path `/socket.io/socket.io.js`. By default, all websocket connections and resources are served within the `/socket.io` path.

**Server**

```
var app = require('express')(); 
var server = require('http').Server(app); 
var io = require('socket.io')(server);

app.get('/', function(req, res) { 
 res.sendFile(__dirname + '/index.html');
});

server.listen(8080); 
```

The client needs to do two things as well:

1. Load the library from the server
2. Call `.connect()` to the server address and websocket path

**Client**

```
<script src="/socket.io/socket.io.js"></script> 
<script> 
 var socket = io.connect('/');
</script> 
```

If you navigate your browser to `http://localhost:8080` and inspect the HTTP requests behind the scenes using your browser's developer tools, you should be able to see the handshake being executed, including the GET requests and resulting HTTP 101 Switching Protocols response.

### Sending Data from Server to Client

Okay, now on to some of the more interesting parts. In this example we'll be showing you the most common way to send data from the server to the client. In this case, we'll be sending a message to a channel, which can be subscribed to and received by the client. So, for example, a client application might be listening on the 'announcements' channel, which would contain notifications about system-wide events, like when a user joins a chat room.

On the server this is done by waiting for the new connection to be established, then by calling the `socket.emit()` method to send a message to all connected clients.

**Server**

```
io.on('connection', function(socket) { 
 socket.emit('announcements', { message: 'A new user has joined!' });
});
```

**Client**

```
<script src="/socket.io/socket.io.js"></script> 
<script> 
 var socket = io.connect('/');
 socket.on('announcements', function(data) {
 console.log('Got announcement:', data.message);
 });
</script> 
```

### Sending Data from Client to Server

But what would we do when we want to send data the other way, from client to server? It is very similar to the last example, using both the `socket.emit()` and `socket.on()` methods.

**Server**

```
io.on('connection', function(socket) { 
 socket.on('event', function(data) {
 console.log('A client sent us this dumb message:', data.message);
 });
});
```

**Client**

```
<script src="/socket.io/socket.io.js"></script> 
<script> 
 var socket = io.connect('/');
 socket.emit('event', { message: 'Hey, I have an important message!' });
</script> 
```

### Counting Connected Users

This is a nice example to learn since it shows a few more features of `socket.io` (like the `disconnect` event), it's easy to implement, and it is applicable to many webapps. We'll be using the `connection` and `disconnect` events to count the number of active users on our site, and we'll update all users with the current count.

**Server**

```
var numClients = 0;

io.on('connection', function(socket) { 
 numClients++;
 io.emit('stats', { numClients: numClients });

 console.log('Connected clients:', numClients);

 socket.on('disconnect', function() {
 numClients--;
 io.emit('stats', { numClients: numClients });

 console.log('Connected clients:', numClients);
 });
});
```

**Client**

```
<script src="/socket.io/socket.io.js"></script> 
<script> 
 var socket = io.connect('/');
 socket.on('stats', function(data) {
 console.log('Connected clients:', data.numClients);
 });
</script> 
```

A much simpler way to track the user count on the server would be to just use this:

```
var numClients = io.sockets.clients().length; 
```

But apparently there are [some issues](https://github.com/socketio/socket.io/issues/463) surrounding this, so you might have to keep track of the client count yourself.

### Rooms and Namespaces

Chances are as your application grows in complexity, you'll need more customization with your websockets, like sending messages to a specific user or set of users. Or maybe you want need strict separation of logic between different parts of your app. This is where rooms and namespaces come in to play.

**Note**: These features are not part of the websocket protocol, but added on top by `socket.io`.

By default, `socket.io` uses the root namespace (`/`) to send and receive data. Programmatically, you can access this namespace via `io.sockets`, although many of its methods have shortcuts on `io`. So these two calls are equivalent:

```
io.sockets.emit('stats', { data: 'some data' }); 
io.emit('stats', { data: 'some data' }); 
```

To create your own namespace, all you have to do is the following:

```
var iosa = io.of('/stackabuse'); 
iosa.on('connection', function(socket){ 
 console.log('Connected to Stack Abuse namespace'):
});
iosa.emit('stats', { data: 'some data' }); 
```

Also, the client must connect to your namespace explicitly:

```
<script src="/socket.io/socket.io.js"></script> 
<script> 
 var socket = io('/stackabuse');
</script> 
```

Now any data sent within this namespace will be separate from the default `/` namespace, regardless of which channel is used.

Going even further, within each namespace you can join and leave 'rooms'. These rooms provide another layer of separation on top of namespaces, and since a client can _only be added to a room on the server side_, they also provide some extra security. So if you want to make sure users aren't snooping on certain data, you can use a room to hide it.

To be added to a room, you must `.join()` it:

```
io.on('connection', function(socket){ 
 socket.join('private-message-room');
});
```

Then from there you can send messages to everyone belonging to the given room:

```
io.to('private-message-room').emit('some event'); 
```

And finally, call `.leave()` to stop getting event messages from a room:

```
socket.leave('private-message-room'); 
```

## Conclusion

This is just one library that implements the websockets protocol, and there are many more out there, all with their own unique features and strengths. I'd advise trying out some of the others (like [node-websockets](https://www.npmjs.com/package/websocket)) so you get a feel for what's out there.

Within just a few lines, you can create some pretty powerful applications, so I'm curious to see what you can come up with!

_Have some cool ideas, or already created some apps using websockets? Let us know in the comments!_
