# Getting your C++ to the Web with Node.js

[Original URL](http://blog.scottfrees.com/getting-your-c-to-the-web-with-node-js)

> So, you've got some old C or C++ code, playing an important role in your business - but you desperately want to get that functionality on the web - maybe just internally to streamline your workflow,...

So, you've got some old C or C++ code, playing an important role in your business - but you desperately want to get that functionality on the web - maybe just internally to streamline your workflow, or maybe publically in order to fulfill some other goal. The problem, of course, is that C++ isn't exactly easy to put onto the web - so what are your options?

This post is kicking off a series dedicated to showing you how to take your legacy (or new) C/C++ code to the web by integrating it with [Node.js](https://nodejs.org). In a previous series I detailed one option - Node.js Addons - but in this series I'm covering a few more, along with giving you some guidance on which method might be best in your situation.

## Can't I just write a C++ web site?

Well... yes - you could! People have been writing parts of web applications in C++ for a very long time - using CGI. If you want to take a look, check out the notes at the end of the post for some starting points. CGI isn't the most popular thing on the web these days though, it lacks a lot of productivity enhancements that makes web development so great today. More importantly, it introduces some significant performance and scalability issues. On the other hand, C++ has come a long way over the past few years in terms of expressiveness, and the C++14 standard has enabled a some really cool projects focused on writing modern MVC-styled web apps in pure C++. If that's your thing, check out [Silicon](http://siliconframework.org/).

The majority of web developers aren't C++ programmers, and frankly, unless ultra high performance from your web tier is critical, you are probably better off using languages providing a higher level of abstraction. The common players on the web being Ruby, Go, Node.js, Python, PHP along with many more...

## Why Node.js?

The first answer is... I like it. Seriously though, Node.js has a number of advantages. For one, it **integrates** really nicely with C++ in several different ways - which this post will outline. In general, Node.js also has a lot of benefits that dovetail with why you'd be using C++ in the first place - it's highly portable, it promotes performance at scale, and has a thriving [ecosystem](https://www.npmjs.com/).

## Why not just rewrite the C++?

Ah... every developer's first instinct - "Let's rewrite this old code written in language X because language Y is so much cooler | better | faster | easier!". First off - if you have some legacy C++ code that is simple, small, and doesn't really need to be high performance - this might very well be the best answer. However, if you are in that category, you probably aren't reading this - you're likely almost done rewriting your C++ code.

There are a few practical reasons not to rewrite code. **First**, you might not have the code! Believe it or not, if you are working for a company that uses legacy tools to support their business, source code for these tools are often lost to time (I've seen this a bunch of times when working with engineering firms). A derivative of this is when your legacy code uses third-parth dependencies, which cannot be rewritten or modified.

**Second**, C/C++ can be complex - and if it's old, it might be really hard to decipher. Are you a web developer that is also a C++ guru? Are you _positive_ you can completely recreate the precise inputs/outputes of this program? If it's a critical line of business tool, you are putting a **ton** of risk on your plate.

The **third reason** not to rewrite your C++ is because it might really want to be in C++! While Node.js has decent performance, it's simply not C/C++. If your application has extreme performance criteria, you aren't going to beat C++.

## C++ and Node.js - your options

I've come up with three general ways of integrating C++ code with a Node.js application - although there are lots of variations within each category:

1. **Automation** - call your C++ as a standalone app in a child process.
2. **Shared library** - pack your C++ routines in a shared library (dll) and call those routines from Node.js directly.
3. **Node.js Addon** - compile your C++ code as a native Node.js module/addon.

Each of these options have their advantages and disadvantages, they primarily differ in the degree in which you need to modify your C++, the performance hit you are willing to take when calling C++, and your familiarity / comfort in dealing with Node.js and the V8 API.

### How to choose

The most obvious question to ask first is **do you have access to the C++ source**, _or just the binary?_ Without source code, you need to hope the C++ program is either a command line program or a dll/lib shared library. If you are looking at a program written with only a graphical user interface... well then you are in a world of pain. It's likely you are going to need to rewrite your application in order to make it work on the web.

### Option 1 - Automation

If your C++ runs as a standalone from a command line, you don't need the source code to take advantage of Option 1 - the **automation option**. You can run your C++ program unaltered, using Node's [child process](https://nodejs.org/api/child_process.html) API. This option works for bringing just about anything to the web - it really doesn't make a difference what language your command line program is written in if you are simply running it. If you are reading this hoping to get C code, Fortran code, or some other language onto the web - then this option is worth reading. I'll discuss this in the next post.

The automation option is not only for those _without_ the underlying C++ code. If you have C++ code that either is currently, or could easily be turned into, a command line program - then this option is reasonable if you can live with the performance, and you don't really want to get into the hassles of language integration.

### Option 2 - Shared Library / DLL

If you are dealing with a C++ dll/lib, or you have the C++ source code and can make modest modifications in order to create a shared library, then the **shared library** approach might work well for you. I'll detail how you can do this using the [Foreign Function Interface](https://github.com/node-ffi/node-ffi) module in the third post of this series. This option gives you more fine-grain control of how you integrate C++ into Node, because calls to C++ routines can be interleaved with Node.js code itself. While this approach brings you closer to full integration, you still have to deal with type conversions and blocking when calling C++. It's a great option if you want better integration, without investing a lot of time dealing with V8.

### Option 3 - Node.js Addon

If you have the C++ source code, and you are willing to learn a bit about **Node.js Addons**, then a third option is creating a native Node.js module out of your C++. While this is the more challenging approach, you gain a ton of flexibility and performance. You also have the option to call your C++ asynchronously so you don't block your web application's event-loop while your C++ is crunching numbers. The fourth post in this series will cover this option using the [Nan library](https://github.com/nodejs/nan). I've also written about addons in previous posts (see Notes below). While we're at it, I'm also working on a book that will cover this in much more detail - [check it out](http://scottfrees.com/ebooks/nodecpp/)!

## A running example - Prime Numbers

Throughout the next three posts in this series I'll be showing you examples of how to implement each of the options above. I want to use the same basic example in each. Prime numbers are extremely important for lots of stuff (i.e. cryptography), and their generation tends to be really computationally expensive. A quick search online will direct you mostly towards C and C++ implementations - and the really efficient ones are _complicated_. Looking at their source, you'll instantly recognize that you probably don't' want to rewrite them - unless you are just looking for a challenge - which is fine:).

One of the more effective algorithms is called [Sieve of Eratosthenes](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes). There is a really popular C++ suite, [primseieve](http://primesieve.org/) - but it's pretty complicated to build. Instead, I found a simpler implementation that is more suitable for our purposes. You can find the source code for it [here](http://wwwhomes.uni-bielefeld.de/achim/prime_sieve.html), but it's also in the [git repository](https://github.com/freezer333/cppwebify-tutorial) for this blog series.

![Sieve of Eratosthenes](https://upload.wikimedia.org/wikipedia/commons/b/b9/Sieve_of_Eratosthenes_animation.gif)

_Animation of Sieve of Eratosthenes - <https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes>_

## Getting Started - a simple Node.js Web app

Throughout this series I'll use the **exact** same Node.js web application. It's pretty bare-bones, there is a single HTML page with some JavaScript (AngularJS) that asks the web server for prime numbers under a user specified value. The web server responds with a JSON object containing the primes - computed using one of the several techniques I'll implement.

I assume the reader has some basic understanding of a Node.js web app. I created the app with Express on the backend and AngularJS on the frontend, but I stayed away from any complexity and eye/candy as to not distract from the purpose of these tutorials. It's also a great setup for an API into your C++ code - just ditch the UI!

To get started - clone my github repository and check out tag "start".

```
> git clone https://github.com/freezer333/cppwebify-tutorial.git
> cd cppwebify-tutorial
> git checkout start
```

You can poke around the web app yourself - but the relevant bits are the front end - found in /web/views and the backend, found in /index.js and /routes.

Let's take a quick look at /index.js. The first ten lines or so are just boilerplate express code:

```
var express = require('express');
var app = express();
var bodyParser = require('body-parser');

app.use(express.static('public'));
app.set('view engine', 'jade');

app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));
```

The next line line is building an array of "types" - which will eventually hold entries for each example in the post series. For now, we only have one - a Node.js primesieve implementation.

```
var types = [
 {
 title: "pure_node",
 description: "Execute a really primitive implementation of prime sieve in Node.js"
 }];
```

Each of the entries in types will correspond to a route, found in the /routes directory. These are loaded dynamically from index.js and the web server is started by the final hand full of lines.

```
types.forEach(function (type) {
 app.use('/'+type.title, require('./routes/' + type.title));
});

app.get('/', function (req, res) {
 res.render('index', { routes: types});
});

var server = app.listen(3000, function () {
 console.log('Web server listing at http://localhost:%s', server.address().port);
});
```

To launch the web server, navigate to the `/web` directory in your terminal and type the following:

```
> npm install
... dependencies will be installed
> node index
```

You need Node 0.12 or greater to be installed).

Now point your browser to `http://localhost:3000`. You'll get the index page, which lists the implementation options. For now, you'll just have one option - "pure_node". Click on it, and you'll see a page with a single number box. Type `100` and submit - and the Node.js implementation of primesieve will run and return all prime numbers under 100.

![Results for primes under 100](https://raw.githubusercontent.com/freezer333/cppwebify/master/posts/img001.png)

The primesieve implementation in Node.js is found in `routes/pure_node.js`. By comparison to the C implementation we'll use throughout the remainder of this series, it's mind-numbingly simple - but it gets the job done! The code that handles the actual response is the router's post method:

```
router.post('/', function(req, res) {
 var under = parseInt(req.body.under); // from the user

 var primes = find_primes(under);

 res.setHeader('Content-Type', 'application/json');
 res.end(JSON.stringify({
 results: primes // return an array of primes to the browser
 }));
});
```

## Up next...

This post is kicking off a series where we'll dive into all three options for integrating C++ into Node.js - (1) automation, (2) shared library, and (3) addons. I've outlined why you'd choose each one, and shown you the basic example I'll be extending throughout the series. In the next post, we'll take a closer look at automation option.

## Notes

If you are looking for additional information about anything I talked about above, here are some links that might help you. Let me know if I'm missing anything!

## Staying with C++

## Why not rewrite legacy code?

## Previous Posts on Node.js Addons
