# Parallel Processing in JS

[Original URL](https://advancedweb.hu/2016/08/09/parallel-processing-in-js/)

> Motivation For a long time, Javascript was missing any kind of processing threads. While the single-threaded model added to developer comfort, it also made the platform unable to do serious and time...

## Motivation

For a long time, Javascript was missing any kind of processing threads. While the single-threaded model added to developer comfort, it also made the platform unable to do serious and time consuming calculations, and the only way to circumvent it was to do it on a remote server.

Luckily, with the introduction and widespread adoption of Web Workers, we can now do resource-intensive calculations on background threads. On the down side, the specification had to fit into the current ecosystem, and it feels quite awkward at times. If you came from languages where threading is supported from the beginning, you might find the amount of restrictions surprising. It's far from just instantiating a new Thread and everything you write into that will be processed in parallel.

This post is an introduction to Web Workers, how and when you can use them, and their peculiarities. I'll also cover how to use them in WebPack, and the possible pitfalls.

## Web Workers

Code ([html](https://github.com/sashee/parallel-processing-in-js/blob/master/worker.html), [js](https://github.com/sashee/parallel-processing-in-js/blob/master/worker.js)) and [demo](https://sashee.github.io/parallel-processing-in-js/worker.html) are available.

Web Workers are probably the only way to achieve true multi-processing in Javascript. To create a worker, you need to instantiate it with the code it will run.

```
const worker = new Worker("worker.js");
```

This will create a Worker instance with the desired code. You can then communicate with the worker using postMessage, same as you would with IFrames. Since there are no cross-origin problems in play, there is no need to verify origins.

And in the worker's code, you can listen to these events.

```
onmessage = (e) => {
    // e.data will contain the value passed
};
```

This works both ways, so you can postMessage some data back from the worker's code into your main program.

```
// In the worker's code
postMessage(result);

// In the main thread
worker.onmessage = (e) => {
    // e.data will contain the value passed
};
```

These are the basics to get started with workers.

### Error handling

You have multiple methods to handle errors in the worker's code. You can catch and pass them via the postMessage channel. That would require some extra coding, but this is the most versatile and safest way.

The other way is to use the _onerror_ handler. This catches all exceptions that are not handled inside the worker itself and lets the caller code decide how to proceed. To set up error handling, all you need is to attach a handler.

```
worker.onerror = (e) => {
    // e is the exception
};
```

To ease debugging, there are some extra fields in the exception object. There are _filename_, _lineno_, and _colno_ properties that indicate where things went wrong.

### Cleanup

Cleaning up workers after they are not needed is crucial. They spawn real OS-level threads, and they can easily kill the browser process if you spawn too many of them simultaneously.

You have two ways to kill a worker process: inside the worker, or from outside. I think it's best to handle the lifecycle from the main page, but there might be certain situations you might think otherwise.

To kill a worker, simply call its _terminate()_ method. This will abruptly kill it, freeing all resources it is using. If it was processing something, that would halt too.

If you want the worker to manage its lifecycle, just call the _stop()_ method from the worker code.

Either way, the worker is stopped, and should not leave anything behind.

If you are using one-shot workers, that are doing some computations and then get discarded, make sure you are terminating them in the onerror handler too. Failing to do so will introduce hard-to-find leaks in your code.

```
worker.onerror = (e) => {
    worker.terminate();
    reject(e);
};
worker.onmessage = (e) => {
    worker.terminate();
    resolve(e.data);
}
```

### Inline Workers

[Code](https://github.com/sashee/parallel-processing-in-js/blob/master/inline-worker.html) and [demo](https://sashee.github.io/parallel-processing-in-js/inline-worker.html) are available.

Moving the worker code out to a separate file is making some simple cases more difficult than they should be. Luckily, the workers can be instantiated using a Blob, and you can make them however you'd like.

To make an inline worker, just create a Blob with the desired code, make an Object URL from it, and you can give it the worker constructor.

```
<!-- http://stackoverflow.com/a/6454685/2032154 -->
<script id="worker" type="javascript/worker">
    // Put your worker code here
</script>

const code = URL.createObjectURL(new Blob([
    document.getElementById("worker").textContent
]));
const worker = new Worker(code);
```

Since you are creating a global ObjectURL, don't forget to get rid of it when it's not needed. Generally, you'd revoke it when you terminate the worker instance.

```
worker.terminate();
URL.revokeObjectURL(code);
```

### Workers in Workers

In theory, you can spawn subworkers inside a worker, and it should work the same as you create one from the main thread. There is even an [example](https://html.spec.whatwg.org/multipage/workers.html#delegation) in the spec on how to do it. But unfortunately there is a long-standing [Chrome bug](https://bugs.chromium.org/p/chromium/issues/detail?id=31666), that prevents this use case. It might get fixed at some point, but this ticket was opened back in 2010, and there has been little progress since. You should not rely on this feature.

### Passing data

[Code](https://github.com/sashee/parallel-processing-in-js/blob/master/data-passing.html) and [demo](https://sashee.github.io/parallel-processing-in-js/data-passing.html) are available.

There are a few edge cases that you should be aware of when passing data to and from the worker. Passing simple values like numbers, strings, and arrays work as you'd expect. You can pass simple structures and they get serialized/deserialized properly. In effect, you should not resort to serializing objects into JSON just to keep the structure; in fact, postMessage uses a [structured clone algorithm](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Structured_clone_algorithm), which can process a few more types, like RegExps and Blobs, and circular references.

That said, you should still limit what you pass to the simplest types as possible. There is no way you could pass functions, and even the supported types have some limitations; those would easily manifest themselves as hard-to-debug bugs. If you could define your API to handle only strings, numbers, arrays, and objects, you are less likely to face these kinds of problems.

#### Circular references

If you have a complex object, there can be circular references in it. If you try to serialize it into JSON, you'll get a _TypeError: Converting circular structure to JSON_.

```
let a = {};
let b = {a};
a.b = b;
JSON.stringify({a,b}); // Error
```

But you can safely pass the same object to the postMessage, and you can use it inside the worker.

#### Transferable objects

To prevent concurrent modification, everything you pass to postMessage is copied to the other side. This makes sure that you can not modify the same object from two places in parallel.

But if you want to pass large amounts of data around, you'll quickly experience how slow these copy operations are. For example, if you are doing image-related calculations, you're likely to pass whole images; making the copies might easily be the bottleneck.

Fortunately, there are transferable object, and you can, well, transfer them instead of copy them. One such transferable object is an _ArrayBuffer_, which can contain just about any raw data.

If you transfer an object, the thread that originally owned it loses access. It makes sure that while the data is not copied, no concurrent modifications can happen.

The postMessage syntax is quite awkward regarding transferables. You need to pass the data as previous as the first argument, but you need to pass an array of transferables as the second one.

```
const ab = new ArrayBuffer(100);
console.log(ab.byteLength); // 100
worker.postMessage(ab, [ab]);
console.log(ab.byteLength); // 0
```

Make sure you pass the transferable in the second argument; if you forget, the data will be copied over.

```
const ab = new ArrayBuffer(100);
console.log(ab.byteLength); // 100
worker.postMessage(ab);
console.log(ab.byteLength); // 100
```

### Webpack

[Code](https://github.com/sashee/parallel-processing-in-js/blob/master/webpack/src/app.js) and [demo](https://sashee.github.io/parallel-processing-in-js/webpack/dist/index.html) are available.

To use Web Workers with Webpack, you can use the [worker-loader](https://github.com/webpack/worker-loader). Just add it to the _devDependencies_ section at your package.json, run _npm install_, and it's all set up.

To use a worker, simply _require_ it.

```
const workerCode = require("worker!./worker.js");

...

const worker = new workerCode();
```

This will instantiate the worker, and you can use it the same way as without Webpack.

To instantiate an inline worker, all you need to do is add the _inline_ query parameter to the loader.

[Code](https://github.com/sashee/parallel-processing-in-js/blob/master/webpack/src/inline.js) and [demo](https://sashee.github.io/parallel-processing-in-js/webpack/dist/inline.html) are available.

```
const workerCode = require("worker?inline!./worker.js");

...

const worker = new workerCode();
```

You can even import and use any modules inside the workers.

```
import fibonacci from "./fibonacci.js";

...

const result = fibonacci(num);
```

#### The worse parts

Getting the workers up and running in Webpack is quite easy. But there are a few obstacles you need to be aware of when you use this approach.

First, there seems to be **no way to move out the common parts** of the code. If you have a worker that depends on a piece of code, then it will be included no matter whether other parts of your codebase also use it. And if you have multiple workers using the same library, then it will be included in all of them.

You might think that if you dump worker-loader and specify a new entry point then use the [CommonsChunkPlugin](https://webpack.github.io/docs/code-splitting.html), it will take care of this. But unfortunately workers are not like a browser window, and some features are not available that the resulting code would require.

Also, using inline workers do no better in this regard. The shared code is still present in multiple places inside the bundle.

And second, **inline workers [leak ObjectURLs](https://github.com/webpack/worker-loader/blob/master/createInlineWorker.js#L16)**. They are created, but never freed. This might not seem a big problem, but if you use a lot of one-shot workers, it may affect performance.

Based on the above observations, my advice is to use normal workers, and look out what you import into them. And also make sure you send appropriate cache headers, so that the browser does not need to download the code more than once.

## IFrames

Web Workers are very similar to IFrames, and they might give the impression that using them would also result in parallel processing. But since IFrames have access to non-threadsafe APIs, like the DOM, the browser cannot spawn new threads for them. Click [here](https://sashee.github.io/parallel-processing-in-js/iframe.html) for a demonstration.

Cross-origin IFrames are quite different. They do not have access to most of the APIs, and they can communicate only via postMessage, same as Web Workers. This, in theory, allows browsers to run these IFrames on different threads and that would result in parallel processing.

But in practice, they are still single-threaded, and the browser doesn't give them any special handling. To see it in action, click [here](https://sashee.github.io/parallel-processing-in-js/iframe-crossorigin.html).

Web Workers solve the long-standing issue in Javascript on parallel processing. Albeit its syntax is a bit awkward and it poses quite a few limitations, the support is solid and does its job well. On the other hand, while there are basic tooling, they are not mature enough to be reliable. Seems like this technology is far from being mainstream, as there is only niche use-case.

That said, if you need parallel processing, you don't need to wait for something else. Learn its rough edges, use the tools you need, and you can drastically improve the user experience.
