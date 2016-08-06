# Writing Next Generation Reusable JavaScript Modules in ECMAScript 6 – Smashing Magazine

[Original URL](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/)

> Are you excited to take advantage of new JavaScript language features but not sure where to start, or how? You're not alone! I've spent the better part of the last year and a half...

Are you excited to **take advantage of new JavaScript language features** but not sure _where_ to start, or _how_? You're not alone! I've spent the better part of the last year and a half trying to ease this pain. During that time there have been some amazing quantum leaps in JavaScript tooling.

These leaps have made it possible for you and me to dive head first into writing fully ES6 modules, without compromising on the essentials like testing, linting and (most importantly) the ability for others to easily consume what we write.

[![Writing Next Generation Reusable JavaScript Modules](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/02/js-opt.png)](https://github.com/lukehoban/es6features)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#1">1</a>
</sup>

<br>
ECMAScript 6 is here. An [overview of fancy and useful features in the new version of JavaScript](https://github.com/lukehoban/es6features)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#2">2</a>
</sup>

. In this post, we're going to focus on **how to create a JavaScript package written in ES6** that's usable in a site or app regardless of whether you're using CommonJS, asynchronous module definition (AMD) or plain browser global modules.

> **Wait, is it ES6 or ES2015?** My habits certainly prefer ES6, but the name was recently and officially changed to ES2015\. However, there's a greater level of awareness of ES6, which is how I will refer to it in this post.

> I'd also like to give special thanks to [Doug Neiner](https://twitter.com/dougneiner)

> <sup>
>   <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#3">3</a>
> </sup>

>  and [Ryan Niemeyer](https://twitter.com/rpniemeyer)

> <sup>
>   <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#4">4</a>
> </sup>

>  – both have shared this journey into better ES6 tooling. This post wouldn't have been possible without them.

In parts 1 and 2 of this series, we'll look at some of the tools that make this possible. Today we'll cover writing, transpiling and packaging our library; and in part 2 we'll focus on linting, formatting and testing (using JSCS, ESLint, mocha, Chai, Karma and Istanbul). Meet your new best friends for part 1:

- [Babel](http://babeljs.io/)

  <sup>
    <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#8">8</a>
    <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#5">5</a>
  </sup>

   (which just celebrated its first birthday) has made the process of transpiling ES6 to ES5 not only simple, but _pleasant_.
- [webpack](http://webpack.github.io/)

  <sup>
    <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#23">23</a>
    <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#9">9</a>
    <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#6">6</a>
  </sup>

   silenced every aspect of the "module wars" on my team by letting us consume _everything_ (CommonJS, AMD and ES6) with aplomb. It turns out that webpack also does a fantastic job of packaging standalone ES6 libraries – a fact we will look at closely during this post.
- [Gulp](http://gulpjs.com/)

  <sup>
    <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#7">7</a>
  </sup>

   is a powerful tool for automating build-related tasks.

## The Goal [Link](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#the-goal)

### Write In ES6, Use In ES5 [Link](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#write-in-es6-use-in-es5)

We're going to talk about writing ES6 client-side _libraries_, not bundling entire sites or apps. (This is really any reusable bit of code you'd like to share between projects, whether it's an open source software project or something you use internally at work between applications.) _"Wait a second"_, you might be thinking. _"Won't it be a while until the browser range I have to support can handle ES6?"_

That's correct! However, I mentioned [Babel](http://babeljs.io/)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#8">8</a>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#5">5</a>
</sup>

 above because we're going to use it to convert our ES6 to ES5, making it a practical option to use today for most situations.

### Make It Easy For Anyone To Consume [Link](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#make-it-easy-for-anyone-to-consume)

The second part of our goal is to write a module that we could use in most common module ecosystems. Die-hard AMD fan? You get a module. CommonJS plus browserify the only song you sing? And _you_ get a module. Not sure what the AMD versus CommonJS fuss is about, and you just want to drop the `<script>` tag on the page and go? _You_ get a module too! It's a bit like an Oprah module giveaway – where the part of Oprah is played by [webpack](http://webpack.github.io/)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#23">23</a>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#9">9</a>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#6">6</a>
</sup>

. webpack will help package our module in a special wrapper called a [universal module definition (UMD)](https://github.com/umdjs/umd)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#22">22</a>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#10">10</a>
</sup>

, making it possible to consume in any of the above scenarios.

## Setting Up Our Project [Link](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#setting-up-our-project)

Over the next few minutes, we'll be working towards the [resulting code here](https://github.com/ifandelse/legoQuotes/releases/tag/v1.0.0)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#11">11</a>
</sup>

. I usually start a project off with _src/_, _spec/_ and _lib/_ folders. In our _src/_ folder, you'll see a contrived but fun set of example modules that, when used together, let us retrieve a random quote from a [Lego Movie](http://www.imdb.com/title/tt1490017/)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#12">12</a>
</sup>

 character. While the behavior is fairly useless, this example makes use of [classes](http://www.2ality.com/2015/02/es6-classes-final.html)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#13">13</a>
</sup>

, [modules](http://www.2ality.com/2014/09/es6-modules-final.html)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#14">14</a>
</sup>

, `const15`, [destructuring](http://www.2ality.com/2015/01/es6-destructuring.html)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#16">16</a>
</sup>

, a [generator](http://www.2ality.com/2015/03/es6-generators.html)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#17">17</a>
</sup>

 and more – all features we'd like to safely transpile to ES5.

The main focus of this post is to discuss how to use Babel and webpack to transpile and package an ES6 library. However, I also wanted to take a brief look at our example code so that you can see that we are indeed using ES6.

**Note:** Don't worry if you're new to ES6 syntax. These examples are simple enough to follow.

## The LegoCharacter Class [Link](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#the-legocharacter-class)

In our _LegoCharacter.js_ module, we see the following (be sure to read the comments for more explanation):

```
// LegoCharacter.js
// Let's import only the getRandom method from utils.js
import { getRandom } from "./utils";

// the LegoCharacter class is the default export of the module, similar
// in concept to how many node module authors would export a single value
export default class LegoCharacter {
 // We use destructuring to match properties on the object
 // passed into separate variables for character and actor
 constructor( { character, actor } ) {
 this.actor = actor;
 this.name = character;
 this.sayings = [
 "I haven't been given any funny quotes yet."
 ];
 }
 // shorthand method syntax, FOR THE WIN
 // I've been making this typo for years, it's finally valid syntax :)
 saySomething() {
 return this.sayings[ getRandom( 0, this.sayings.length - 1 ) ];
 }
}
```

Pretty boring on its own – this class is meant to be extended, which we do in our _Emmet.js_ module:

```
// emmet.js
import LegoCharacter from "./LegoCharacter";

// Here we use the extends keyword to make
// Emmet inherit from LegoCharacter
export default class Emmet extends LegoCharacter {
 constructor() {
 // super lets us call the LegoCharacter's constructor
 super( { actor: "Chris Pratt", character: "Emmet" } );
 this.sayings = [
 "Introducing the double-decker couch!",
 "So everyone can watch TV together and be buddies!",
 "We're going to crash into the sun!",
 "Hey, Abraham Lincoln, you bring your space chair right back!",
 "Overpriced coffee! Yes!"
 ];
 }
}
```

Both _LegoCharacter.js_ and _emmet.js_ are separate _files_ in our project – this will be case for each module in our example. Depending on how you've been writing JavaScript, this might seem a bit foreign to you. By the time we're done, though, we'll have a 'built' version that combines them together.

## The index.js [Link](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#the-index-js)

Our _index.js_ – another file in our project – is the main entry point for our library. It imports a few Lego character classes, creates instances of them and provides a generator function to `yield` a random quote any time a caller asks for one:

```
// index.js
// Notice that lodash isn't being imported via a relative path
// but all the other modules are. More on that in a bit :)
import _ from "lodash";
import Emmet from "./emmet";
import Wyldstyle from "./wyldstyle";
import Benny from "./benny";
import { getRandom } from "./utils";

// Taking advantage of new scope controls in ES6
// once a const is assigned, the reference cannot change.
// Of course, transpiling to ES5, this becomes a var, but
// a linter that understands ES6 can warn you if you
// attempt to re-assign a const value, which is useful.
const emmet = new Emmet();
const wyldstyle = new Wyldstyle();
const benny = new Benny();
const characters = { emmet, wyldstyle, benny };

// Pointless generator function that picks a random character
// and asks for a random quote and then yields it to the caller
function* randomQuote() {
 const chars = _.values( characters );
 const character = chars[ getRandom( 0, chars.length - 1 ) ];
 yield `${character.name}: ${character.saySomething()}`;
}

// Using object literal shorthand syntax, FTW
export default {
 characters,
 getRandomQuote() {
 return randomQuote().next().value;
 }
};
```

In a nutshell, the _index.js_ module imports lodash, the classes for our three Lego characters and a utility function. It then creates instances of our Lego characters and exports them (makes them available to consuming code) as well as the `getRandomQuote` method. If all goes well, when this code is transpiled to ES5 it should still do exactly the same thing.

## OK. Now What? [Link](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#ok-now-what)

We have all this shiny new JavaScript, but how do we transpile it to ES5? First, let's install Babel using [npm](https://docs.npmjs.com/getting-started/installing-npm-packages-globally)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#18">18</a>
</sup>

:

```
npm install -g babel
```

Installing Babel globally gives us a `babel` [command line interface (CLI) option](http://babeljs.io/docs/usage/cli/)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#19">19</a>
</sup>

. If we navigate to our project's root directory and type this, we can transpile the modules to ES5 and drop them in the _lib/_ directory:

```
babel ./src -d ./lib/
```

Looking at our _lib_ folder, we'll see these files listed:

```
LegoCharacter.js
benny.js
emmet.js
index.js
utils.js
wyldstyle.js
```

Remember how I mentioned above that we were putting each of our ES6 modules into its own file? Babel has taken each of those files, converted them to ES5 and written them to the same file structure in our _lib_ folder. A quick glance at these files might tell you a couple of things:

- First, these files could be consumed in node right now, as long as the _[babel/register](http://babeljs.io/docs/usage/require/)

  <sup>
    <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#20">20</a>
  </sup>_

   runtime dependency was required first. You'll see an example of these running in node before the end of this post. (Transpiling typically involves a runtime dependency even though many – but not all – of these features are now available natively in [node v4](https://nodejs.org/en/docs/es6/)

  <sup>
    <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#21">21</a>
  </sup>

  .)
- Second, we still have some work to do so that these files can be packaged into _one_ file and wrapped in a [universal module definition (UMD)](https://github.com/umdjs/umd)

  <sup>
    <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#22">22</a>
    <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#10">10</a>
  </sup>

   and used in a browser.

## Enter webpack [Link](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#enter-webpack)

Odds are you've heard of [webpack](http://webpack.github.io/)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#23">23</a>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#9">9</a>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#6">6</a>
</sup>

, whose description calls it "a bundler for JavaScript and friends." The most common use case for webpack is to act as a bundler and loader for a website, enabling you to bundle your JavaScript, as well as other assets like CSS and templates, into one (or more) files. webpack has an amazing ecosystem of "loaders," which are transformations applied to files loaded by webpack. While building a UMD isn't the _most common_ use case for webpack, it turns out we can use a webpack loader to load our ES6 modules and transpile them to ES5, and webpack's bundling process to build out a single output file of our example project.

Used extensively in webpack, loaders can do things like transpile ES6 to ES5, Less to CSS, load JSON files, render templates and _much_ more. Loaders take a `test` pattern to use for matching files that they should transform. Many loaders can take additional configuration options as well, which we'll be making use of. (Curious as to what other loaders exist? Check out [this list](http://webpack.github.io/docs/list-of-loaders.html)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#24">24</a>
</sup>

.)

Let's go ahead and install webpack globally (which gives us a webpack [CLI](http://webpack.github.io/docs/cli.html)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#25">25</a>
</sup>

):

```
npm install -g webpack
```

Next we can install [babel-loader](https://www.npmjs.com/package/babel-loader)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#26">26</a>
</sup>

 to our local project. This loader enables webpack to load our ES6 modules and transpile them to ES5\. We can install it, and save it to our package.json's `devDependencies` by running this:

```
npm install --save-dev babel-loader
```

Before we can use webpack, though, we need to create a webpack configuration file that tells webpack what we want it to do with our source files. Usually named _webpack.config.js_, a webpack configuration file is a node.js module that exports a set of configuration values telling webpack what to do.

Here's our initial _webpack.config.js_ file. I've commented the code heavily, and we'll also discuss some of the important details below:

```
module.exports = {
 // entry is the "main" source file we want to include/import
 entry: "./src/index.js",
 // output tells webpack where to put the bundle it creates
 output: {
 // in the case of a "plain global browser library", this
 // will be used as the reference to our module that is
 // hung off of the window object.
 library: "legoQuotes",
 // We want webpack to build a UMD wrapper for our module
 libraryTarget: "umd",
 // the destination file name
 filename: "lib/legoQuotes.js"
 },
 // externals let you tell webpack about external dependencies
 // that shouldn't be resolved by webpack.
 externals: [
 {
 // We're not only webpack that lodash should be an
 // external dependency, but we're also specifying how
 // lodash should be loaded in different scenarios
 // (more on that below)
 lodash: {
 root: "_",
 commonjs: "lodash",
 commonjs2: "lodash",
 amd: "lodash"
 }
 }
 ],
 module: {
 loaders: [
 // babel loader, testing for files that have a .js extension
 // (except for files in our node_modules folder!).
 {
 test: /\.js$/,
 exclude: /node_modules/,
 loader: "babel",
 query: {
 compact: false // because I want readable output
 }
 }
 ]
 }
};
```

So let's look at a couple of key values in our configuration.

## Output [Link](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#output)

A webpack configuration file can be given an `output` object that describes how webpack should build and package the source. In our example above, we're telling webpack to output a UMD library to our _lib/_ directory.

## Externals [Link](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#externals)

You might have noticed our example library uses lodash. We want our built library to be smart enough to require lodash from an external source, rather than have to include lodash itself in the output. The `externals` option in a webpack config file lets you specify the dependencies that should remain external. In the case of lodash, its global property key (`_`) is not the same as its name ("lodash"), so our configuration above tells webpack how to require lodash for each given module scenario (CommonJS, AMD and browser root).

You'll notice that our babel-loader just calls itself "babel" for the loader name. This is a webpack naming convention: where the module name is "myLoaderName-loader", webpack treats it as "myLoaderName."

We're testing for any file ending in _.js_, except for files that live under our _node_modules/_ folder. The `compact` option we're passing to the babel loader turns off whitespace compression because I'd like our unminified built source to be readable. (We'll add a minified build in a moment.)

If we run `webpack` in our console at the project root, it will see our _webpack.config.js_ file and build our library, giving us output similar to this:

```
» webpack
Hash: f33a1067ef2c63b81060
Version: webpack 1.12.1
Time: 758ms
 Asset Size Chunks Chunk Names
lib/legoQuotes.js 12.5 kB 0 [emitted] main
 + 7 hidden modules
```

If we look in our _lib/_ folder, we'll see a newly minted _legoQuotes.js_ file. This time, the contents are wrapped in webpack's UMD, which we can see in this snippet:

```
(function webpackUniversalModuleDefinition(root, factory) {
 if(typeof exports === 'object' && typeof module === 'object')
 module.exports = factory(require("lodash"));
 else if(typeof define === 'function' && define.amd)
 define(["lodash"], factory);
 else if(typeof exports === 'object')
 exports["legoQuotes"] = factory(require("lodash"));
 else
 root["legoQuotes"] = factory(root["_"]);
})(this, function(__WEBPACK_EXTERNAL_MODULE_1__) {

// MODULE CODE HERE

});
```

This UMD performs one kind of CommonJS check, then an AMD check, then another style of CommonJS and finally it falls back to plain browser globals. You can see how it knew to look for lodash as "lodash" if we were in a CommonJS or AMD environment, and to look for `_` on the window (root) if we were dealing with plain browser globals.

## What Happened, Exactly? [Link](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#what-happened-exactly)

When we ran `webpack` in our console, it looked for the default name of a configuration file (_webpack.config.js_), and read the configuration. From there it saw that the _src/index.js_ file was our main entry point, and began loading it and its dependencies (except for lodash, which we told webpack was external). Each of these dependencies is a _.js_ file, so the babel loader would be run on the file, transpiling it from ES6 to ES5 as it was loaded. From there, all files loaded were written to a single output file, _legoQuotes.js_, which is dropped into the _lib_ folder.

Looking at the module code you'll see that our ES6 source has indeed been transpiled to ES5\. For example, our `LegoCharacter` class is now an ES5 constructor function:

```
// around line 179
var LegoCharacter = (function () {
 function LegoCharacter(_ref) {
 var character = _ref.character;
 var actor = _ref.actor;
 _classCallCheck(this, LegoCharacter);
 this.actor = actor;
 this.name = character;
 this.sayings = ["I haven't been given any funny quotes yet."];
 }

 _createClass(LegoCharacter, [{
 key: "saySomething",
 value: function saySomething() {
 return this.sayings[(0, _utils.getRandom)(0, this.sayings.length - 1)];
 }
 }]);

 return LegoCharacter;
})();
```

## [](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#its-usable)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#27">27</a>
</sup>

It's Usable! [Link](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#27its-usable)

At this point we could include this built file in both a browser (IE9+, as a general rule) and node and it would work in either, as long as the babel runtime dependency is included.

If we wanted to use this in the browser, it would look something like this:

```
<!-- index.html -->
<!DOCTYPE html>
<html>
<head>
 <meta charset="utf-8">
 <meta http-equiv="X-UA-Compatible" content="IE=edge">
 <title>Lego Quote Module Example</title>
 <link rel="stylesheet" href="style.css">
</head>
<body>
 <div class="container">
 <blockquote id="quote"></blockquote>
 <button id="btnMore">Get Another Quote</button>
 </div>
 <script src="../node_modules/lodash/index.js"></script>
 <script src="../node_modules/babel-core/browser-polyfill.js"></script>
 <script src="../lib/legoQuotes.js"></script>
 <script src="./main.js"></script>
</body>
</html>
```

You can see we've included our _legoQuotes.js_ file (just below babel's _browser-polyfill.js_ file) just like any other `<script>` tag (above). Our _main.js_ file, which uses our legoQuotes library, looks like this:

```
// main.js
( function( legoQuotes ) {
 var btn = document.getElementById( "btnMore" );
 var quote = document.getElementById( "quote" );

 function writeQuoteToDom() {
 quote.innerHTML = legoQuotes.getRandomQuote();
 }

 btn.addEventListener( "click", writeQuoteToDom );
 writeQuoteToDom();
} )( legoQuotes );
```

To use it in node, it would look like this:

```
require("babel/polyfill");
var lego = require("./lib/legoQuotes.js");
console.log(lego.getRandomQuote());
// > Wyldstyle: Come with me if you want to not die.
```

## Moving To Gulp [Link](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#moving-to-gulp)

Both Babel's and webpack's CLIs are very useful, but it's common to use a task runner such as Gulp to handle executing these kinds of tasks. This consistency can pay off if you participate in many projects, as the main CLI commands you'll need to remember consist of `gulp someTaskName`. I don't think there's a right or wrong answer here, for the most part. If you prefer the CLIs, use them. Using Gulp is simply one possible way to go about it.

### [](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#build-task)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#28">28</a>
</sup>

Setting Up A Build Task [Link](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#28setting-up-a-build-task)

First, let's install gulp:

```
npm install -g gulp
```

Next, let's create a gulp file that can run what we have done so far. We'll use the [webpack-stream](https://www.npmjs.com/package/webpack-stream)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#29">29</a>
</sup>

 gulp plugin, which I've installed by running `npm install --save-dev webpack-stream`. This plugin can consume our _webpack.config.js_ file and allow webpack to play nicely with gulp.

```
// gulpfile.js
var gulp = require( "gulp" );
var webpack = require( "webpack-stream" );

gulp.task( "build", function() {
 return gulp.src( "src/index.js" )
 .pipe( webpack( require( "./webpack.config.js" ) ) )
 .pipe( gulp.dest( "./lib" ) )
} );
```

Since I'm using gulp to source our _index.js_ and to write to the output directory, I've tweaked the _webpack.config.js_ file by removing `entry` and updating the `filename`. I've also added a [`devtool`](http://webpack.github.io/docs/configuration.html#devtool)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#30">30</a>
</sup>

 prop, set to the value of `#inline-source-map` (this will write a source map at the end of the file in a comment):

```
// webpack.config.js
module.exports = {
 output: {
 library: "legoQuotes",
 libraryTarget: "umd",
 filename: "legoQuotes.js"
 },
 devtool: "#inline-source-map",
 externals: [
 {
 lodash: {
 root: "_",
 commonjs: "lodash",
 commonjs2: "lodash",
 amd: "lodash"
 }
 }
 ],
 module: {
 loaders: [
 {
 test: /\.js$/,
 exclude: /node_modules/,
 loader: "babel",
 query: {
 compact: false
 }
 }
 ]
 }
};
```

### [](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#minifying)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#31">31</a>
</sup>

What About Minifying? [Link](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#31what-about-minifying)

I'm glad you asked! One approach to minifying can be done using the [gulp-uglify](https://www.npmjs.com/package/gulp-uglify)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#32">32</a>
</sup>

 plugin, along with [gulp-sourcemaps](https://www.npmjs.com/package/gulp-sourcemaps)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#33">33</a>
</sup>

 (since we'd like a source map for our min file) and [gulp-rename](https://www.npmjs.com/package/gulp-rename)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#34">34</a>
</sup>

 (which lets us target a different file name so we don't overwrite our non-minified build). I've added both to our project via:

```
npm install --save-dev gulp-uglify gulp-sourcemaps gulp-rename
```

In this approach, our unminified source will still have an inline source map, but our usage of gulp-sourcemaps below will cause the minified file's source map to be written as a separate file (with a comment in the minified file pointing to the source map file):

```
// gulpfile.js
var gulp = require( "gulp" );
var webpack = require( "webpack-stream" );
var sourcemaps = require( "gulp-sourcemaps" );
var rename = require( "gulp-rename" );
var uglify = require( "gulp-uglify" );

gulp.task( "build", function() {
 return gulp.src( "src/index.js" )
 .pipe( webpack( require( "./webpack.config.js" ) ) )
 .pipe( gulp.dest( "./lib" ) )
 .pipe( sourcemaps.init( { loadMaps: true } ) )
 .pipe( uglify() )
 .pipe( rename( "legoQuotes.min.js" ) )
 .pipe( sourcemaps.write( "./" ) )
 .pipe( gulp.dest( "lib/" ) );
} );
```

If we run `gulp build` in our console, we should see something similar to:

```
» gulp build
[19:08:25] Using gulpfile ~/git/oss/next-gen-js/gulpfile.js
[19:08:25] Starting 'build'...
[19:08:26] Version: webpack 1.12.1
 Asset Size Chunks Chunk Names
legoQuotes.js 23.3 kB 0 [emitted] main
[19:08:26] Finished 'build' after 1.28 s
```

Our _lib/_ directory will now contain three files: _legoQuotes.js_, _legoQuotes.min.js_ and _legoQuotes.min.js.map_. Not only does everyone attending the ~~Oprah~~ webpack show get a module, but they also get a sourcemap to make debugging the minified file a possibility.

## [](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#banner-plugin)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#35">35</a>
</sup>

Webpack Banner Plugin [Link](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#35webpack-banner-plugin)

If you need to include a licence comment header at the top of your built files, webpack makes it easy. I've updated our _webpack.config.js_ file to include the `BannerPlugin36`. I don't like hardcoding the banner's information if I don't need to, so I've imported the _package.json_ file to get the library's information. I also converted the _webpack.config.js_ file to ES6, and am using a [template string](http://www.2ality.com/2015/01/template-strings-html.html)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#37">37</a>
</sup>

 to render the banner. Towards the bottom of the _webpack.config.js_ file you can see I've added a `plugins` property, with the `BannerPlugin` as the only plugin we're currently using:

```
// webpack.config.js
import webpack from "webpack";
import pkg from "./package.json";
var banner = `
 ${pkg.name} - ${pkg.description}
 Author: ${pkg.author}
 Version: v${pkg.version}
 Url: ${pkg.homepage}
 License(s): ${pkg.license}
`;

export default {
 output: {
 library: pkg.name,
 libraryTarget: "umd",
 filename: `${pkg.name}.js`
 },
 devtool: "#inline-source-map",
 externals: [
 {
 lodash: {
 root: "_",
 commonjs: "lodash",
 commonjs2: "lodash",
 amd: "lodash"
 }
 }
 ],
 module: {
 loaders: [
 {
 test: /\.js$/,
 exclude: /node_modules/,
 loader: "babel",
 query: {
 compact: false
 }
 }
 ]
 },
 plugins: [
 new webpack.BannerPlugin( banner )
 ]
};
```

_(Note: It's worth mentioning that by converting my webpack.config.js file to ES6, I can't run it via the webpack CLI any longer.)_

Our updated _gulpfile.js_ includes two additions: the requiring of the babel register hook (on line 1) and the passing of options to the gulp-uglify plugin:

```
// gulpfile.js
require("babel/register");
var gulp = require( "gulp" );
var webpack = require( "webpack-stream" );
var sourcemaps = require( "gulp-sourcemaps" );
var rename = require( "gulp-rename" );
var uglify = require( "gulp-uglify" );

gulp.task( "build", function() {
 return gulp.src( "src/index.js" )
 .pipe( webpack( require( "./webpack.config.js" ) ) )
 .pipe( gulp.dest( "./lib" ) )
 .pipe( sourcemaps.init( { loadMaps: true } ) )
 .pipe( uglify( {
 // This keeps the banner in the minified output
 preserveComments: "license",
 compress: {
 // just a personal preference of mine
 negate_iife: false
 }
 } ) )
 .pipe( rename( "legoQuotes.min.js" ) )
 .pipe( sourcemaps.write( "./" ) )
 .pipe( gulp.dest( "lib/" ) );
} );
```

## [](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#whats-next)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#38">38</a>
</sup>

What's Next? [Link](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#38whats-next)

We're a good way into our journey! So far we've stepped through a quick evolution of using babel and webpack's CLIs to build our library, then moved on to use gulp (and related plugins) to handle the build for us. The [code related to this post](https://github.com/ifandelse/legoQuotes/releases/tag/v1.0.0)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#39">39</a>
</sup>

 includes an _example/_ directory with both a browser- and node-based example of our working transpiled module. In our next post, we'll look at using ESLint and JSCS for linting and formatting, mocha and chai to write tests, Karma to run those tests and istanbul to measure our test coverage. In the meantime, you might want to check out "[Designing Better JavaScript APIs](https://www.smashingmagazine.com/2012/10/designing-javascript-apis-usability/)

<sup>
  <a href="https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/#40">40</a>
</sup>

," a fantastic article that can help guide you in writing better modules.

_(rb, jb, ml, og)_

[↑ Back to top](https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/) [Tweet it](https://twitter.com/intent/tweet?original_referer=https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/&source=tweetbutton&text=Writing%20Next%20Generation%20Reusable%20JavaScript%20Modules%20in%20ECMAScript%206&url=https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/&via=smashingmag)[Share on Facebook](http://www.facebook.com/sharer/sharer.php?u=https://www.smashingmagazine.com/2016/02/writing-next-generation-reusable-javascript-modules/)
