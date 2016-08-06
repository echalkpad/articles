# A Primer on the React Ecosystem: Part 1 of 3

[Original URL](http://patternhatch.com/2016/07/06/a-primer-on-the-react-ecosystem-part-1-of-3/)

> Updates 2016.07.05: First publication Introduction I've been using the React ecosystem extensively at work during the past eight months. We've built apps to help us monitor our trading...

**Updates**<br>
2016.07.05: First publication

**Introduction**<br>
I've been using the React ecosystem extensively at work during the past eight months. We've built apps to help us monitor our trading positions, edit our pre-trade risk checks, and control our strategies.

It's proven a fantastic ecosystem to work with given its composable nature and approachable learning curve. With React, it's become almost fun to deliver a consumer-grade UI for Enterprise use.

I thought I'd share what I've learned so far through a primer. Over three parts, I'm going to show you how to:

- set up an environment for React app development,
- create React components and have them respond to data changes,
- manage app state with Redux.

If you're approaching the React ecosystem for the first time, my hope is that by the end of this primer you'll be comfortable enough to start building apps of your own.

**In This Installment**<br>
In Part One, we'll be setting up a basic environment for developing React-based apps. By the end of this post, you'll have a working environment which builds your app and dynamically reloads whenever you make a change. We'll also have created our first React component.

**Prerequisites**<br>
I assume you're familiar with [Node](https://nodejs.org) and [NPM](https://www.npmjs.com/) and have them installed. You should also be familiar with Javascript ES6\. You don't need to be an expert in ES6, but know the major additions such as [arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) and [destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment).

As of this writing, I am using Node 6.2.0 and NPM 3.8.9.

**The Code**<br>
You can find all the code for Part One [here](https://github.com/nitinpunjabi/respotify/tree/part01). Each part will have its own branch with the [master](https://github.com/nitinpunjabi/respotify/tree/master) branch containing all the code written so far.

**Project Description**<br>
We're going to build an app which hooks up to the [Spotify](https://www.spotify.com) backend. Our app will allow a user to:

- retrieve the albums of an artist,
- retrieve the tracks for a given album,
- play the opening 30 seconds of a track.

Here's a mockup:

<embed src="http://patternhatch.com/wp-content/uploads/2016/06/mockup-300x250.png%20300w,%20http://patternhatch.com/wp-content/uploads/2016/06/mockup-500x417.png%20500w,%20http://patternhatch.com/wp-content/uploads/2016/06/mockup-150x125.png%20150w,%20http://patternhatch.com/wp-content/uploads/2016/06/mockup-400x334.png%20400w,%20http://patternhatch.com/wp-content/uploads/2016/06/mockup-200x167.png%20200w,%20http://patternhatch.com/wp-content/uploads/2016/06/mockup.png%20712w" class="alignnone size-full wp-image-546" width="712">

Using Spotify's backend will allow us to stay focused on frontend development.

**Project Creation**<br>
Let's start by creating our project.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">mkdir </span>
  <span class="crayon-e">respotify</span>
</p>
  <p>
  <span class="crayon-e">cd </span>
  <span class="crayon-e">respotify</span>
</p>
  <p>
  <span class="crayon-e">npm </span>
  <span class="crayon-v">init</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">y</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

This should create a _package.json_ file in your project's root directory.

Next, create a _src_ directory in your project's root directory. In the _src_ directory, create _index.html_:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-n">&lt;!DOCTYPE html&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;html&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;head&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;title&gt;</span>
  <span class="crayon-i">Respotify</span>
  <span class="crayon-r">&lt;/title&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/head&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;body&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;h1&gt;</span>
  <span class="crayon-i">Respotify</span>
  <span class="crayon-r">&lt;/h1&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;div </span>
  <span class="crayon-e">id</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“container”</span>
  <span class="crayon-r">&gt;</span>
  <span class="crayon-r">&lt;/div&gt;</span>
</p>
  <p>
  <span class="crayon-i">
  <span class="crayon-ta">&lt;script </span>
  <span class="crayon-e">src</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“/bundle.js”</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-ta">&lt;/script&gt;</span>
</span>
</p>
  <p>
  <span class="crayon-r">&lt;/body&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/html&gt;</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

_index.html_ is the entry point for our app. There are two lines of interest here:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-r">&lt;div </span>
  <span class="crayon-e">id</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“container”</span>
  <span class="crayon-r">&gt;</span>
  <span class="crayon-r">&lt;/div&gt;</span>
</p>
  <p>
  <span class="crayon-ta">&lt;script </span>
  <span class="crayon-e">src</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“/bundle.js”</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-ta">&lt;/script&gt;</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**[Line 8]** Our React components will be rendered inside the _container_ div. This will become clear later in this post.

**[Line 9]** The build process we're going to put together will combine all our JS files into a single _bundle.js_ file, which is then pulled in by _index.html_ and executed.

Next, create _index.js_:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">console</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">log</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘Hello, world!’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

_index.js_ is the primary Javascript file for our app. For now, we'll just have it log something to the console to ensure our build process works.

At this point, your project should look like this:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">respotify</span>
</p>
  <p>
  <span class="crayon-o">-</span>
  <span class="crayon-v">src</span>
</p>
  <p>
  <span class="crayon-o">–</span>
  <span class="crayon-v">index</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">html</span>
</p>
  <p>
  <span class="crayon-o">–</span>
  <span class="crayon-v">index</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">js</span>
</p>
  <p>
  <span class="crayon-o">–</span>
  <span class="crayon-t">package</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">json</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**Webpack**<br>
We'll now start putting together our build environment from scratch. You may have heard of React boilerplate projects such as [this](https://github.com/kriasoft/react-starter-kit) which provide complete development setups. While they're useful, I feel it's instructive to know how to create at least a basic setup yourself so that you know the major things that are happening under the hood.

Our build environment starts with [Webpack](https://webpack.github.io/). At its simplest, Webpack bundles your assets for you. And while there are a number of bundlers out there, Webpack has gained traction in the React community for other features it offers; a few of which we'll be using.

Let's start by installing Webpack:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">npm </span>
  <span class="crayon-e">install </span>
  <span class="crayon-v">webpack</span>
  <span class="crayon-o">–</span>
  <span class="crayon-v">save</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">dev</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Once the installation is complete, check your _package.json_ file for whether Webpack is under your _devDependencies._

We want Webpack to do two things:

1. Take our application code and generate static assets from the code.
2. Start a development server to serve up the assets.

Since Webpack is driven by configuration, we'll start there. Create a file called _webpack.config.js_ in the project's root folder:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">webpack</span>
  <span class="crayon-o">=</span>
  <span class="crayon-e">require</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘webpack’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">path</span>
  <span class="crayon-o">=</span>
  <span class="crayon-e">require</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘path’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">PATHS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">app</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘./src/index.js’</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">dist</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">path</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">join</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">__dirname</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">‘dist’</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">module</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">exports</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">entry</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">javascript</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">PATHS</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">app</span>
</p>
  <p>
  <span class="crayon-v">output</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">path</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">PATHS</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">dist</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">publicPath</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘/’</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">filename</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘bundle.js’</span>
</p>
  <p>
  <span class="crayon-v">devServer</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">contentBase</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">PATHS</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">dist</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Let's go through this.

At the top, we pull in the Webpack and Path modules, then define a few path constants for our app.

**[Line 9]** This is where our Webpack configuration begins.

**[Line 10]** This is the [entry](https://webpack.github.io/docs/configuration.html#entry) point for what will eventually become our _bundle.js_ file. In our case, we have a single JS file (_index.js_) serving as our entry point. Webpack will process _index.js_ as well as any other JS file _index.js_ declares as a dependency, and the dependencies of those dependencies and so on, eventually combining them into a single _bundle.js_ file. Imagine a graph of dependencies collapsing into a single node.

**[Line 13]** Here's where we set our [output](https://webpack.github.io/docs/configuration.html#output) configuration. In this case, we're telling Webpack to:

- output the results of its compilation to a _dist_ folder,
- set the URL of the output files to the home path,
- call the result of its compilation _bundle.js._

**[Line 18]** Here we're specifying that Webpack's [dev server](https://webpack.github.io/docs/configuration.html#devserver) should serve assets from the _dist_ folder.

Now try running this command in your terminal (make sure you're in your project's root directory and node is on your PATH):

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">node </span>
  <span class="crayon-v">node_modules</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">webpack</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">bin</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">webpack</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">js</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

You should see a _dist_ folder created in your project's root directory with a _bundle.js_ file in it.

If you look inside _bundle.js_, you'll see some Webpack-specific functionality along with our console.log statement near the bottom.

At this point, your project directory structure should look like this:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">Respotify</span>
</p>
  <p>
  <span class="crayon-o">-</span>
  <span class="crayon-v">dist</span>
</p>
  <p>
  <span class="crayon-o">–</span>
  <span class="crayon-v">bundle</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">js</span>
</p>
  <p>
  <span class="crayon-o">-</span>
  <span class="crayon-v">node_modules</span>
</p>
  <p>
  <span class="crayon-o">-</span>
  <span class="crayon-v">src</span>
</p>
  <p>
  <span class="crayon-o">–</span>
  <span class="crayon-v">index</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">html</span>
</p>
  <p>
  <span class="crayon-o">–</span>
  <span class="crayon-v">index</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">js</span>
</p>
  <p>
  <span class="crayon-o">–</span>
  <span class="crayon-t">package</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">json</span>
</p>
  <p>
  <span class="crayon-o">–</span>
  <span class="crayon-v">webpack</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">config</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">js</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Now what we also want is to serve this _bundle.js_ file, referenced by _index.html_, which is currently **not** in the _dist_ folder. So what we need is to have _index.html_ copied to the _dist_ folder.

To do that, we'll use the file-loader package. Install it like so:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">npm </span>
  <span class="crayon-e">install </span>
  <span class="crayon-v">file</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">loader</span>
  <span class="crayon-o">–</span>
  <span class="crayon-v">save</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">dev</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

We'll modify our _webpack.config.js_ file to include a reference to our _index.html_ file. We'll also include a _module_ object to specify our first [loader](https://webpack.github.io/docs/loaders.html). In essence, loaders are preprocessors which run on files as you require or load them. In this case, we're using the [file-loader](https://github.com/webpack/file-loader) loader to copy _index.html_ to our output (_dist_) directory. Add the following highlighted lines to your _webpack.config.js_ file:

 |
 | 

Now if we run the previous command again:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">node </span>
  <span class="crayon-v">node_modules</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">webpack</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">bin</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">webpack</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">js</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

You should see _index.html_ copied over into the _dist_ directory. The _dist_ directory should now look like this:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">dist</span>
</p>
  <p>
  <span class="crayon-o">–</span>
  <span class="crayon-v">bundle</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">js</span>
</p>
  <p>
  <span class="crayon-o">–</span>
  <span class="crayon-v">index</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">html</span>
</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

As we create our app, we'll need to keep re-compiling _bundle.js_. It's tedious to run the Webpack command each time. What if we could just have the build process run automatically on each change and our browser refresh to reflect them?

That's where [Webpack Dev Server](https://webpack.github.io/docs/webpack-dev-server.html) comes into play. Let's start by installing it:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">npm </span>
  <span class="crayon-e">install </span>
  <span class="crayon-v">webpack</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">dev</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">server</span>
  <span class="crayon-o">–</span>
  <span class="crayon-v">save</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">dev</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Once installed, start the server with this command:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">node </span>
  <span class="crayon-v">node_modules</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">webpack</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">dev</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">server</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">bin</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">webpack</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">dev</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">server</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">js</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

That'll launch a dev server at <http://localhost:8080/webpack-dev-server/>.

If you visit that URL, you should see our web app:<br>

[<embed src="http://patternhatch.com/wp-content/uploads/2016/07/respotify_initial_dev-300x176.png%20300w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_initial_dev-768x450.png%20768w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_initial_dev-500x293.png%20500w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_initial_dev-150x88.png%20150w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_initial_dev-400x235.png%20400w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_initial_dev-200x117.png%20200w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_initial_dev.png%20771w" class="alignnone size-full wp-image-630" width="771">](http://patternhatch.com/wp-content/uploads/2016/07/respotify_initial_dev.png)

Try changing the h1 tag in _index.html_ to something else and saving it. The Webpack dev server should automatically detect the change and reload. On each reload, you should also see the console log output from _index.js_. So what we have now is a dev server which serves our assets and reloads when it detects a change.

Let's round out this section by creating a few NPM scripts in our _package.json_ file to make things more convenient.

Add the highlighted lines to the _scripts_ object in _package.json_:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-s">“scripts”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-s">“start”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“node node_modules/webpack-dev-server/bin/webpack-dev-server.js”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“build”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“node node_modules/webpack/bin/webpack.js”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“test”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“echo \”Error: no test specified\" &amp;&amp; exit 1"</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

If you haven't stopped the webpack dev server, stop it. Then try launching the Webpack dev server with:

To run the build process manually, use:

At this point, we have a basic build process going. Next, we'll augment our build process to allow us to use the latest ES6 features.

**Babel**<br>
[ES6 support](https://kangax.github.io/compat-table/es6/) varies amongst browsers. So how can you use all the ES6 features without worrying about compatibility issues? One solution is to transpile your ES6 code to ES5, and that's exactly what we'll be doing.

We'll be using [Babel](http://babeljs.io/) for the job. We want Babel to do two things:

- Transpile our ES6 to ES5.
- Transpile our [JSX](https://facebook.github.io/react/docs/jsx-in-depth.html) to Javascript. JSX is a React DSL which resembles HTML. You'll learn all about it when we get to React.

Start by installing these Babel packages:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">npm </span>
  <span class="crayon-e">install </span>
  <span class="crayon-v">babel</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">core </span>
  <span class="crayon-v">babel</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">loader </span>
  <span class="crayon-v">babel</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">preset</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">es2015 </span>
  <span class="crayon-v">babel</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">preset</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">react</span>
  <span class="crayon-o">–</span>
  <span class="crayon-v">save</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">dev</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Let's go through what each package does:

Before we get to incorporating Babel, let's make a change to _index.js_ to include some ES6 code. Modify _index.js_ to look like this:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">greeting</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-r">name</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">console</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">log</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">
  <code>&lt;/span&gt;&lt;span class="crayon-v"&gt;Hello&lt;/span&gt;&lt;span class="crayon-sy"&gt;,&lt;/span&gt;&lt;span class="crayon-sy"&gt;$&lt;/span&gt;&lt;span class="crayon-sy"&gt;{&lt;/span&gt;&lt;span class="crayon-r"&gt;name&lt;/span&gt;&lt;span class="crayon-sy"&gt;}&lt;/span&gt;&lt;span class="crayon-o"&gt;!&lt;/span&gt;&lt;span class="crayon-sy"&gt;</code>
</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">greeting</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘world’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Now execute _npm run build_, and look at the bottom of _bundle.js_. You should see the preceding function there in its original ES6 form. So no transpilation is taking place. With that in mind, let's incorporate Babel.

First, we'll add the babel-loader to our _webpack.config.js_ file. Recall how we already use the file-loader to copy our _index.html_ file to the _dist_ folder. We'll add a second loader object now for Babel. Add the highlighted lines to your _module_ object:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">module</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">loaders</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">[</span>
</p>
  <p>
  <span class="crayon-v">test</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">/</span>
  <span class="crayon-sy">\</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">html</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-o">/</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">loader</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“file?name=[name].[ext]”</span>
</p>
  <p>
  <span class="crayon-v">test</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">/</span>
  <span class="crayon-sy">\</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">js</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-o">/</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">exclude</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">node_modules</span>
  <span class="crayon-o">/</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">loaders</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-s">“babel-loader”</span>
  <span class="crayon-sy">]</span>
</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Here, we're asking Webpack to apply the babel-loader to all files with a .js extension which aren't in node_modules.

Next, create a _.babelrc_ file in your project's root directory:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-s">“presets”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-s">“react”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">“es2015”</span>
  <span class="crayon-sy">]</span>
</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Here we're asking Babel to use the [presets](https://babeljs.io/docs/plugins/) we installed earlier.

That's it.

Execute _npm run build_ again and check out _bundle.js_. Our _greeting_ function should now be in its ES5 equivalent.

Ok, we're almost done. What we want next is the ability to reload React components whenever we make a change, without losing state information.

**Hot Module Replacement**<br>
Let's start by installing [React](https://facebook.github.io/react/) and [ReactDOM](https://facebook.github.io/react/docs/top-level-api.html#reactdom), and then the [React Hot Loader](https://gaearon.github.io/react-hot-loader/). Note that we're installing React and ReactDOM as regular dependencies, and React Hot Loader as a dev dependency.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">npm </span>
  <span class="crayon-e">install </span>
  <span class="crayon-e">react </span>
  <span class="crayon-v">react</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">dom</span>
  <span class="crayon-o">–</span>
  <span class="crayon-v">save</span>
</p>
</div>
 | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">npm </span>
  <span class="crayon-e">install </span>
  <span class="crayon-v">react</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">hot</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">loader</span>
  <span class="crayon-o">–</span>
  <span class="crayon-v">save</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">dev</span>
</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Next, have Webpack use the hot loader. Open _webpack.config.js_ and modify the babel-loader like so:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">loaders</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-s">“react-hot”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">“babel-loader”</span>
  <span class="crayon-sy">]</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

It's important that _react-hot_ gets **prepended** as webpack processes from right to left.

Next, open up _package.json_ and edit the _start_ npm script to look like this:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-s">“start”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“node node_modules/webpack-dev-server/bin/webpack-dev-server.js –hot –inline”</span>
  <span class="crayon-sy">,</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

At this point, we've further augmented our dev setup to reload React components without losing state. But what does that actually mean? To answer that, we'll need to test drive our setup by creating a React component.

**Our First React Component**<br>
Create a file in your _src_ directory called _greeting.js:_

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">React </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">“react”</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">export </span>
  <span class="crayon-st">default</span>
  <span class="crayon-v">React</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">createClass</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">render</span>
  <span class="crayon-o">:</span>
  <span class="crayon-t">function</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">return</span>
  <span class="crayon-sy">(</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">div </span>
  <span class="crayon-e">className</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“greeting”</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-v">Hello</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">name</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-o">!</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">div</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

We've just created our first React component. Let's examine this line-by-line.

**[Line 1]** At the top, we import React.

**[Line 3]** We create a React component class with the [React.createClass](https://facebook.github.io/react/docs/top-level-api.html#react.createclass) function, passing it a specification object describing our component.

**[Line 4]** Every React component must have a _render_ method, which returns the component's markup.

**[Lines 5-9]** Inside the _render_ method, we return a block of what looks like HTML–A div element of class _greeting_ wrapping a string. The string itself concatenates 'Hello, ' with a reference to some value.

We're going to dive into the details of Lines 5-9, but first, let's get this component on the screen.

Open _index.js_ and modify it like so:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">React </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">“react”</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">ReactDOM </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">“react-dom”</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">Greeting </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘./greeting’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">ReactDOM</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">render</span>
  <span class="crayon-sy">(</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">Greeting </span>
  <span class="crayon-r">name</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“World”</span>
  <span class="crayon-o">/</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">document</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">getElementById</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘container’</span>
  <span class="crayon-sy">)</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

We once again start by importing React. We then import ReactDOM which is the API for rendering to the DOM. We also import our _greeting_ component.

We then call ReactDOM's render method, passing it two arguments:

1. The component we want to render.
2. Where we wish to render the component.

In our case, we want to render our _greeting_ component in a div with id _container_. Recall that this _container_ div is in _index.html_.

Note what the first argument looks like. We pass our _greeting_ component as if it's an HTML element. We also include a key value pair where the key is _name_ and the value is "World". _name_ then becomes a **property** of this particular _greeting_ component.

In essence, what we're stating in _index.js_ is this:

**Render a _greeting_ component, assigning it a _name_ property with value "World", and render this component inside a div with id _container_.**

Now when the _greeting_ component renders, it should retrieve the _name_ property and include its value in the string. Restart the dev server and verify the component renders as expected:<br>

[<embed src="http://patternhatch.com/wp-content/uploads/2016/07/respotify_first_component-300x177.png%20300w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_first_component-500x295.png%20500w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_first_component-400x236.png%20400w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_first_component-200x118.png%20200w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_first_component.png%20725w" class="alignnone size-full wp-image-633" width="725">](http://patternhatch.com/wp-content/uploads/2016/07/respotify_first_component.png)

To test the hot reload, try wrapping the string from _greeting.js_ in h1 tags like so:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-v">h1</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-v">Hello</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">name</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-o">!</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">h1</span>
  <span class="crayon-o">&gt;</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Save it and you should see the component update in place without requiring a full reload.

Now let's take a step back and revisit this fragment from _greeting.js_:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-st">return</span>
  <span class="crayon-sy">(</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">div </span>
  <span class="crayon-e">className</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“greeting”</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-v">Hello</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">name</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-o">!</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">div</span>
  <span class="crayon-o">&gt;</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The markup we're returning is actually JSX, a React-specific DSL that's convenient for describing tree structures, much like HTML. Our Babel setup then takes this JSX and transpiles it to its equivalent plain Javascript. This leads to two important things to keep in mind:

1. JSX is **not** an extension to Javascript.
2. JSX is not necessary. You can use plain Javascript method calls instead.

To demonstrate the second point, let's use the [Babel REPL](http://babeljs.io/repl/). On the REPL, make sure the _ES2015_ and _React_ preset boxes are checked. Copy the entire contents of _greeting.js_ into the editor box on the left, and note the transpiled output of the _render_ method on the right. A fragment such as this:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-r">&lt;div </span>
  <span class="crayon-e">className</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“greeting”</span>
  <span class="crayon-r"> /&gt;</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

gets transpiled to:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">_react2</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-st">default</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">createElement</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“div”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">className</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“greeting”</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The preceding plain Javascript code is something you can use directly. However, I recommend sticking with JSX as it makes React components easier to both read and write. The fact that it looks like HTML also helps lessen the learning curve. Of course, since JSX is really Javascript, there are a few differences such as JSX's _className_ vs HTML's _class_. We'll be diving into more JSX in future installments. For now, here's Facebook's [guide](https://facebook.github.io/react/docs/jsx-in-depth.html) for more information.

Let's turn our attention now to this line inside the div element:

In JSX, anything enclosed in curly braces is a Javascript expression. So here, we are accessing this component's _props_ object, and in turn, accessing the _name_ property on the _props_.

Recall this line from _index.js_:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">Greeting </span>
  <span class="crayon-r">name</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“World”</span>
  <span class="crayon-o">/</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">,</span>
</p>
</div>
 | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

When you instantiate a component, you can pass a number of key-value pairs representing that component's _state_. So when we instantiated our _greeting_ component, we also specified that it should have a piece of state called _name_, and the value of that state should be "World".

Inside the actual _greeting_ component, that state is then available through the component's _props_ object, using the same _name_ label. When the expression inside the curly braces then gets evaluated, it results in "Hello, World" getting rendered to the browser.

One key thing to keep in mind is that props are immutable. In other words, once state is passed down to a component, the receiving component can no longer change it. This can help with making your component easier to reason about and debug, as well as isolating state management to only a few modules.

Try changing the value of _name_ to your own name in _index.js._ You should see Webpack detect the change and automatically reload, resulting in the _greeting_ component receiving the new state.

If you've followed along and got everything working, then congratulations. You just created a build process and development environment for React-based apps which bundles, transpiles, and hot-reloads. In addition, you created a React component. Keep in mind that you can add a lot more to an environment such as minification, selective bundling, and enabling and disabling various tool features based on where you're deploying to. But for our purposes, this suffices.

In the next installment, we'll dive further into React by creating more components and building out our Spotify client. We'll also add linting to help keep our code tidy.

Expect the next installment to come in about two weeks from the date of this publication. Until then, if you have any comments, suggestions, or corrections, please feel free to post them in the comments or shoot me an [email](http://patternhatch.com/about/).
