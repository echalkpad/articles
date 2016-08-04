# The Incomplete Collection of Node.js Performance Tips -- Node and Beyond

[Original URL](https://medium.com/node-and-beyond/the-incomplete-collection-of-node-js-performance-tips-94cc712661bd)

> This is a (non-comprehensive) collection of practical advice on how to get more performance out of your Node.js application. While the points I am making below are just some of the more obvious...

![](https://cdn-images-1.medium.com/max/800/1*1dI8vwqQTqVVhDy75ugc9A.png)

This is a (non-comprehensive) collection of practical advice on how to get more performance out of your Node.js application. While the points I am making below are just some of the more obvious _**_ performance boosting tips, I will try to keep a more detailed list maintained on GitHub (if people like it). But let's jump right into it:

Node.js is single threaded (duh). So an instance of Node runs in a single thread, not taking any advantage of modern multi-core CPUs. Luckily, Node's cluster module allows you to easily launch a whole cluster of Node child processes to better handle the load. (And child processes share the same server ports.)

Node's documentation gives an excellent and easy to read starting point on how to get started with clusters: <https://nodejs.org/api/cluster.html>

_Beyond better performance, clusters will have the added benefit of improving the resilience of your application. The 'master' process knows when a child processes crashes, and routes traffic to other child processes until the crashed process gets restarted._

An often overlooked or forgotten middleware is you can use with express is compression. It uses **gzip** to compress your http payload sent to the client.

```
var app = require('express')();
var compression = require('compression');
app.use(compression());
```

_Make sure that you load compression_ **_before_** _any other middleware._

Unlike most server-based applications, Node.js apps will often be designed to run permanently. If that is the case for you, consider setting your most used variables & objects just once at initialization and keep reusing them for any incoming requests down the line. Think of something like "the top 5 articles of the week" your users will see on your frontpage.<br>
Do you really need to update that content _every_ time a user visits your page? If not, you can cache it:

```
app.set('view cache', true);
```

Do you make use of http PUT/DELETE requests? If not, disable _methodOverride_. If you don't even use POST requests (maybe your app does everything via sockets) you can even get rid of the _bodyParser_ too.

Take a close look which middleware you actually need, and which you can work without of.

And some middleware might only be needed in a specific environment mode. For those cases you can do something like this:

```
if (app.get('env') !== 'production') {
 app.use(morgan);
}
```

Express sessions are saved in memory. Your memory footprint grows with every additional user connected. This can add quite a bit of overhead to your application.

Try to limit your use of session variables. Better to use hashed tokens and fetch user data from an alternative session store such as MongoDB or Redis.

_But if you are brave enough you might even want to consider to not store any state server-side at all, but dive into client side sessions. For that, take a look at this:_ [_https://github.com/mozilla/node-client-sessions_](https://github.com/mozilla/node-client-sessions)

Probably the single biggest advantage of Node.js (besides server side javascript execution itself of course) is it's asynchronous nature.

Being single-threaded, your Node.js application can easily getblocked by synchronous code execution taking too long to return. If incoming requests can't get processed anymore, your application is rendered de-facto useless.

Think about some especially time, memory, or CPU intensive tasks. For instance accessing, reading or writing large files. If there is no absolutely, critical, unavoidable reason, you should **_always_** use async over sync.

But keep asynchronous support also in mind when choosing external libraries and components. Make sure they are not potentially blocking your application.

Revisit your codebase and find chained async callbacks and think if you can run those functions parallel. For instance: You need to read 2 files before executing a callback. This can be done way better than chaining callbacks.

**The _async_ module** allows you to run functions parallel without the need of chaining them in callbacks. Here the code example for async.parallel:

```
async.parallel([
 function(){ ... },
 function(){ ... }
], callback);
```

Awesome, right? You get one callback executed after all parallel running functions finished. Take a look at the documentation for more details: <https://github.com/caolan/async>

**Bluebird and Q** are known first and foremost for giving us promises. But they also let us execute code parallel returning a promise (um.. callback-ish) after all promises (um .. promisified functions) returned.<br>
Let's look at an example for bluebird:

```
Bluebird.all([promiseFunction1,promiseFunction2]) 
 .then(function(data) {
 // will execute after all promisified functions returned
 });
```

**ES 6** has a native support for promises and likewise provide a _.all()_ method. Depending on your Node version, you might have already support for it.

```
Promise.all([promiseFunction1, promiseFunction1])
 .then(function(data) {
 // will execute after all promisified functions returned
 });
```

_If for whatever reason you do not want to use any of the options above, you could also use following approach. But this does not scale well for obvious reasons. Really, you shouldn't do that._

If you come from a traditional web development side to Node.js, you are probably used to working with libraries such as [underscore](http://underscorejs.org/) or [lodash](https://lodash.com/) to take advantage of higher order functions (think of __.each_, __.map_, etc..)

Those libraries are written with the **front-end** in mind, and that means they include a sizable chunk of code that makes sure everything works as it should in a variety of browser environment: From Chrome to Safari, from Firefox to IE, plus .. polyfills (Yeah, we are looking at you IE).

Which is **great**, but also means those libraries contain a lot of additional code that you do not need -- not server-side anyways. So before adding yet another module and adding to your resource footprint, try taking advantage of V8 native functions instead, such as _forEach_, _map_, etc.

If you are working with MongoDB as your database of choice, I have two easy recommendations that could improve your performance quite a bit. (And you should be able to fairly easy retrofit your already existing apps)

1. limit queries in 'scope' whenever possible, and

2. add indices to collections which are queried often

Imagine you want to display the last 10 posts of a user whenever the user visits your site. First, it is obvious that will want to index your collections accordingly, but you should also make sure to limit your MongoDB queries in 'scope'. This can mean 2 things: First, to limit the fields you want returned, and secondly (maybe more importantly) to limit the number of documents returned. Take a look at these example snippets from the MongoDB documentation:

```
db.user.find({ type: 'customer' }, { item: 10, qty: 1 } );

db.user.find({ type: 'customer' }).limit(10);
```

Makes sense? Also dig into the MongoDB's documentation on creating and working with indices in Node: <http://docs.mongodb.org/getting-started/node/indexes/>

_Btw: I highly recommend Mongo University's course: "_[_MongoDB for Node.js Developers_](https://university.mongodb.com/courses/M101JS/about)_" Believe me, it's_ **_super_** *****awesome_**_!*

Instead of using server side rendering such Express' Jade templating, move to client side rendering using [React](http://facebook.github.io/react/), [AngularJS](https://angularjs.org/) or [Backbone.js](http://backbonejs.org/) and have the server provide only the dynamic content really needed. 'Outsourcing' rendering to the client can **seriously** improve your application's performance.

Wherever possible you should avoid using Node.js for serving your static assets. Those include anything from Images, Videos or CSS files.

To improve both, performance of your application and client latency, I recommend a combination of a separate standard web server, such as Nginx, plus taking advantage of Content Delivery Networks (CDNs) to cache and provide those static assets as close to the user as possible.

_Personally I have had excellent experiences with Rackspace for both.<br>
Check them out:_ [_www.rackspace.com_](http://www.rackspace.com)

There is a lot to be said for the beauty of clean code following [fancy style guides](https://github.com/airbnb/javascript). But there is more to be said for reducing load and latency -- at least in production.

Make it a habit to use [Gulp](http://gulpjs.com) or [Grunt](http://gruntjs.com) to run tasks to [uglify your javascript](https://github.com/mishoo/UglifyJS2/) and [purify your CSS](https://github.com/purifycss). I personally am becoming a bit of a fan of webpack to bundle everything together as well: <http://webpack.github.io/>

This is quite new territory for me, but it is probably fair to say that it's a good idea to keep close look at your application's performance during development. I haven't tested them all, (though I made good experiences with New Relic) but here the recommendations I got from others:

_Sidenote: Node.js applications in production mode run with higher performance than in development. You can run your app in production mode to get a sense of the performance once your application is deployed._

> If you found this post in any way useful, please leave a ♥ .

> Thanks :)
