# A Primer on the React Ecosystem: Part 2 of 3

[Original URL](http://patternhatch.com/2016/08/02/a-primer-on-the-react-ecosystem-part-2-of-3/)

> Updates 2016.08.02: First publication. Part One Recap In Part One, we set up a basic build environment using Webpack and Babel. We then test drove this environment by creating a simple React...

**Updates**<br>
2016.08.02: First publication.

**Part One Recap**<br>
In [Part One](http://patternhatch.com/2016/07/06/a-primer-on-the-react-ecosystem-part-1-of-3/), we set up a basic build environment using [Webpack](https://webpack.github.io/) and [Babel](http://babeljs.io/). We then test drove this environment by creating a simple React component. We also looked at the basics of component rendering and [JSX](https://facebook.github.io/react/docs/jsx-in-depth.html), the React DSL used to specify React components.

**In This Installment**<br>
In Part Two, we'll enhance our build environment, then start building our Spotify client using React. Our aim is to let a user:

- retrieve an artist's albums,
- retrieve an album's tracks,
- play a preview for a given track.

By the end of this post, you'll have a greater understanding of React components and how to create and structure them to work together.

**The Code**<br>
We'll be picking up where we left off in Part One. The code for Part One is [here](https://github.com/nitinpunjabi/respotify/tree/part01). If you didn't follow along previously and wish to do so now, then I suggest getting the code. Bear in mind you'll want the code from the _part01_ branch. To clone it through Git, use this command:

 | <div class="crayon-pre">
  <p><span class="crayon-r">git</span>clone<span class="crayon-e"> -b</span>part01https<span class="crayon-o">:</span><span class="crayon-o">/</span><span class="crayon-o">/</span>github<span class="crayon-sy">.</span>com<span class="crayon-o">/</span>nitinpunjabi<span class="crayon-o">/</span>respotify<span class="crayon-sy">.</span>git</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The code for the end result of Part Two is [here](https://github.com/nitinpunjabi/respotify/tree/part02).

**Linting**<br>
Before we begin creating the UI, we'll enhance our build process with [linting](http://stackoverflow.com/questions/8503559/what-is-linting). Linting will help enforce some discipline in how we write our Javascript and JSX code and help keep things tidy. Whenever we save our work, the linter will scan our code and flag anything that violates rules we specify. For the job of linting, we'll be using [ESLint](http://eslint.org/). The rules we'll use will be from Airbnb. I like Airbnb's [ruleset](https://github.com/airbnb/javascript/tree/master/packages/eslint-config-airbnb) as it's fairly strict, but of course, you're free to turn off any of them or add more if you wish.

Let's start by installing the dependencies. Rather than installing each individually, take the block below and paste it into your _package.json_ under _devDependencies_:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-s">“eslint”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“^2.13.1”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“eslint-config-airbnb”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“^9.0.1”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“eslint-loader”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“^1.4.0”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“eslint-plugin-import”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“^1.10.2”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“eslint-plugin-jsx-a11y”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“^1.5.5”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“eslint-plugin-react”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“^5.2.2”</span>
  <span class="crayon-sy">,</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

We're doing this because, at the time of this writing, there are compatibility issues with ESLint 3.0 and the other ESLint packages. By specifying the exact version numbers, we can avoid these problems while they're being sorted out.

Here's a brief description of each package we're installing:

Once you add the preceding lines, your _devDependencies_ in _package.json_ should look like this:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-s">“devDependencies”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-s">“babel-core”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“^6.10.4”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“babel-loader”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“^6.2.4”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“babel-preset-es2015”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“^6.9.0”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“babel-preset-react”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“^6.11.1”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“eslint”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“^2.13.1”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“eslint-config-airbnb”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“^9.0.1”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“eslint-loader”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“^1.4.0”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“eslint-plugin-import”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“^1.10.2”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“eslint-plugin-jsx-a11y”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“^1.5.5”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“eslint-plugin-react”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“^5.2.2”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“file-loader”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“^0.9.0”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“react-hot-loader”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“^1.3.0”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“webpack”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“^1.13.1”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“webpack-dev-server”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“^1.14.1”</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

If the version numbers on your other packages are different, it'll likely **not** cause a problem. If you wish, you can use the [_package.json_ file from Part Two](https://github.com/nitinpunjabi/respotify/blob/part02/package.json) directly.

Now run:

at your terminal to get the new packages installed locally.

Next, we need to incorporate linting into our Webpack build. Recall from Part One when we [incorporated loaders into our Webpack build](http://patternhatch.com/2016/07/06/a-primer-on-the-react-ecosystem-part-1-of-3/#webpack). Loaders are simply plugins which preprocess our files. We currently have loaders to copy our _index.html_ file and translate our ES6 and JSX code to ES5\. We'll now add a loader for linting. Add the highlighted lines to the _module_ object in your _webpack.config.js file_:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">module</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">preLoaders</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">[</span>
</p>
  <p>
  <span class="crayon-v">test</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">/</span>
  <span class="crayon-sy">\</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">js</span>
  <span class="crayon-sy">?</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-o">/</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">loaders</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-s">“eslint-loader”</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">exclude</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">node_modules</span>
  <span class="crayon-o">/</span>
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
  <span class="crayon-s">“react-hot”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">“babel-loader”</span>
  <span class="crayon-sy">]</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Note how our other loaders are under a _loaders_ object while our linting loader is going under a _preLoaders_ object. Webpack provides three loader hooks: _preLoaders_, _loaders_, and _postLoaders_, which are executed in that order. It's good practice to put linting in the _preLoaders_ because if it fails, we'll know about it first.

We're also going to add this right above the _modules_ object:

This reports all ESLint errors under a warning designation. This way, if our linter picks up something questionable, our dev server will still go ahead and build. Is that a good thing? Not necessarily. But given this is a tutorial, I didn't want our environment to be so punishing and stop working just because of, let's say, a missing space.

Your _webpack.config.js_ file should now look like this:

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
  <span class="crayon-v">html</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘./src/index.html’</span>
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
  <span class="crayon-v">app</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">html</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">PATHS</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">html</span>
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
  <p>
  <span class="crayon-v">eslint</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">emitWarning</span>
  <span class="crayon-o">:</span>
  <span class="crayon-t">true</span>
</p>
  <p>
  <span class="crayon-v">module</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">preLoaders</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">[</span>
</p>
  <p>
  <span class="crayon-v">test</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">/</span>
  <span class="crayon-sy">\</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">js</span>
  <span class="crayon-sy">?</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-o">/</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">loaders</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-s">“eslint-loader”</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">exclude</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">node_modules</span>
  <span class="crayon-o">/</span>
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
  <span class="crayon-s">“react-hot”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">“babel-loader”</span>
  <span class="crayon-sy">]</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The last step is to specify that we want to use the Airbnb ruleset. Create _.eslintrc_ in your project's root directory and populate it like so:

The _.eslintrc_ file is where you [specify the linting rules](http://eslint.org/docs/user-guide/configuring) you want to enforce. In our case, since we installed the Airbnb ruleset earlier and don't wish to add anymore, all we need to do is refer to it.

We just augmented our build process further with linting. Let's test it out.

Start by deleting _greeting.js_ from your _src_ directory. We no longer need it.

Next, clear out _index.js_ completely and put in this line.

The preceding line should be the only line in _index.js_. Save the file, then start your server with _npm run start_.

In your terminal, you should see output similar to this:

 | <div class="crayon-pre">
  <p>1<span class="crayon-o">:</span>2errorNewlinerequiredatendoffilebut<span class="crayon-k">not</span>foundeol<span class="crayon-o">-</span>last</p>
  <p>1<span class="crayon-o">:</span>7error<span class="crayon-s">‘foo’</span>isdefinedbutneverusedno<span class="crayon-o">-</span>unused<span class="crayon-o">-</span>vars</p>
  <p>1<span class="crayon-o">:</span>13errorStringsmustusesinglequotequotes</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Each error message comprises four parts:

1. Where in the file it's occuring.
2. The severity of the message.
3. A description of the problem.
4. The actual name of the ESLint rule.

Despite these errors, our app should still come up at:<br>
<http://localhost:8080/webpack-dev-server/>

The only tell-tale is the status bar message at the top indicating there were _Warnings while compiling_:<br>

[<embed src="http://patternhatch.com/wp-content/uploads/2016/07/warnings-300x61.png%20300w,%20http://patternhatch.com/wp-content/uploads/2016/07/warnings-768x156.png%20768w,%20http://patternhatch.com/wp-content/uploads/2016/07/warnings-500x101.png%20500w,%20http://patternhatch.com/wp-content/uploads/2016/07/warnings-400x81.png%20400w,%20http://patternhatch.com/wp-content/uploads/2016/07/warnings.png%20769w" class="alignnone size-full wp-image-693" width="769">](http://patternhatch.com/wp-content/uploads/2016/07/warnings.png)

If we had **not** specified that ESLint errors should be emitted as warnings, you would see the error messages in the terminal also appear in the browser like this:<br>

[<embed src="http://patternhatch.com/wp-content/uploads/2016/07/errors-300x96.png%20300w,%20http://patternhatch.com/wp-content/uploads/2016/07/errors-768x245.png%20768w,%20http://patternhatch.com/wp-content/uploads/2016/07/errors-500x160.png%20500w,%20http://patternhatch.com/wp-content/uploads/2016/07/errors-150x48.png%20150w,%20http://patternhatch.com/wp-content/uploads/2016/07/errors-400x128.png%20400w,%20http://patternhatch.com/wp-content/uploads/2016/07/errors-200x64.png%20200w,%20http://patternhatch.com/wp-content/uploads/2016/07/errors.png%20771w" class="alignnone size-full wp-image-695" width="771">](http://patternhatch.com/wp-content/uploads/2016/07/errors.png)

Let's take a look at the errors. We can see that the Airbnb rules flag double-quoted strings, variables which are defined but never used, and files that aren't ended with new-lines.

Modify _index.js_ to look like this and save (remember to include the blank line at the end):

 | <div class="crayon-pre">
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-e">foo</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">‘bar’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">foo</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">toUpperCase</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

After changing the double quotes to single quotes, using _foo_ in some manner, and ending the file with a newline, all linting errors should go away.

Turning off a rule is simple. Note the ESLint rule name, and override it in your _.eslintrc_ file. So for example, suppose you wanted to turn off the rule for double-quoted strings. From the preceding message description, we note that the rule name is _quotes_, and then add the highlighted lines to _.eslintrc_:__

 |
 | 

Here we add a _rules_ object and inside set the severity of the _quotes_ rule to zero. If you restart your dev server now and use double-quoted strings again, you won't see any errors. Personally, I like having that rule in place so I'm going to remove that override.

However, there are two rules I'm going to turn off, and that's the _arrow-body-style_ and _no-param-reassign_ rules. The Airbnb rules flag situations where your arrow function can be more compact and forego having a body (_arrow-body-style_ rule). But sometimes, I like being more explicit.

The rules also flag instances where passed-in parameters are being modified (_no-param-reassign_ rule), but I do this for certain inline functions which affect styling such as in the _Track_ component we're going to build.

This is what my _.eslintrc_ file looks like:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-s">“extends”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“airbnb”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“rules”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-s">“arrow-body-style”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">0</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“no-param-reassign”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">0</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

All linting warnings should now be gone. With that, we're ready to build our app.

**Breaking Down the UI**<br>
At the end of Part One, we created just a single component to test drive our setup. In practice, almost every React UI will be composed of multiple components. For example, in a weather app, you may have one component displaying the name of the place, another component displaying the current temperature, and another component displaying a graph of a five-day forecast.

Because of this, it's good practice to break down a UI to its constituent components before starting development. There are no hard and fast rules on how to break down your UI, but like most things in programming, there are best-practices and guidelines. Here they are:

1. Your component should do one thing. If it starts growing and taking on more responsibilities, it's time to break it down further.
2. Information should flow top-down. That is, parent components should pass data down to child components.

The second point may not make such sense at the moment, but will become clear as we start building.

Ok, so here's the mock from Part One:<br>

[<embed src="http://patternhatch.com/wp-content/uploads/2016/06/mockup-300x250.png%20300w,%20http://patternhatch.com/wp-content/uploads/2016/06/mockup-500x417.png%20500w,%20http://patternhatch.com/wp-content/uploads/2016/06/mockup-150x125.png%20150w,%20http://patternhatch.com/wp-content/uploads/2016/06/mockup-400x334.png%20400w,%20http://patternhatch.com/wp-content/uploads/2016/06/mockup-200x167.png%20200w,%20http://patternhatch.com/wp-content/uploads/2016/06/mockup.png%20712w" class="alignnone size-full wp-image-546" width="712">](http://patternhatch.com/wp-content/uploads/2016/06/mockup.png)

Taking inventory of this mock, we have:

1. A search bar.
2. A list of albums.
3. One or more albums in the list of albums.
4. A list of tracks.
5. One or more tracks in the list of tracks.

So we have five distinct components (multiple albums are just instances of the same component. Same goes for tracks).

Using these distinctions to break down the UI leads to this:<br>

[<embed src="http://patternhatch.com/wp-content/uploads/2016/07/respotify_breakdown-300x250.png%20300w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_breakdown-500x417.png%20500w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_breakdown-150x125.png%20150w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_breakdown-400x334.png%20400w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_breakdown-200x167.png%20200w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_breakdown.png%20712w" class="alignnone size-full wp-image-672" width="712">](http://patternhatch.com/wp-content/uploads/2016/07/respotify_breakdown.png)

Here we have the:

- **<span>Search Bar</span>**

- **<span>Album List</span>**

- **<span>Individual Albums</span>**

- **<span>Track List</span>**

- **<span>Individual Tracks</span>**

There are two things to note:

1. Notice how instances of the _Individual Albums_ component are inside the _Album List_ component. And instances of the _Individual Tracks_ component are inside the _Track List_ component. When we see component B inside component A, we say component B is _nested_ in component A. Another way to put it is component B is a _child_ of component A.
2. We actually have **six** components here. The sixth component is the yellow box in the breakdown, and it serves as the container for the entire app. It's good practice to have a topmost component serving as the entry-point of the UI where you can initialize state and specify how child components should be arranged.

Visualized as a tree, our app now looks like this:<br>

[<embed src="http://patternhatch.com/wp-content/uploads/2016/07/respotify_tree_visualization-300x146.png%20300w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_tree_visualization-500x243.png%20500w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_tree_visualization-150x73.png%20150w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_tree_visualization-400x195.png%20400w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_tree_visualization-200x97.png%20200w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_tree_visualization.png%20734w" class="alignnone size-full wp-image-674" width="734">](http://patternhatch.com/wp-content/uploads/2016/07/respotify_tree_visualization.png)

Let's start gaining some clarity on all this by building our app, starting with the _App_ container. As we progress, there will be times when I jump back to a previous component to enhance it, or divert into an explanation because of a new feature not covered in previous parts. If you feel something wasn't clear, post a comment or send me an [email](http://patternhatch.com/about/).

**The App Container**<br>
Before we begin, make sure your _src_ directory looks like this and that _index.js_ is **empty**.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e"> -src</span>
</p>
  <p><span class="crayon-o">–</span>index<span class="crayon-sy">.</span>html</p>
  <p><span class="crayon-o">–</span>index<span class="crayon-sy">.</span>js</p>
</div>
 | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Now populate _index.js_ as follows:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">React </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘react’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">ReactDOM </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘react-dom’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-t">class</span>
  <span class="crayon-e">App </span>
  <span class="crayon-r">extends</span>
  <span class="crayon-v">React</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">Component</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">render</span>
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
  <span class="crayon-v">div</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-v">p</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-v">Hi</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">p</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">div</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-v">ReactDOM</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">render</span>
  <span class="crayon-sy">(</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-v">App</span>
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
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<span>If you’re copying and pasting the code, then double-click on the codebox first. That’ll switch the codebox to ‘plain view’, which will eliminate extraneous spaces on blank lines and make it easier to lift out. You may need to add the final blank line yourself.</span>

As usual, we start by importing the _React_ and _ReactDOM_ libraries at the top.

**[Line** **4]** We create our _App_ component class by extending the _React.Component_ base class. This is the standard [ES6-style](https://facebook.github.io/react/docs/reusable-components.html#es6-classes) for component classes. This is in contrast to how we created our component in Part One, where we used the [ES5-style](https://facebook.github.io/react/docs/top-level-api.html#react.createclass) _React.createClass_ method. From here on, we'll be using ES6-style exclusively.

**[Lines 5-11]** Recall from Part One that every class-based React component must have a _render_ method. Here, we define our _render_ method to return some markup. In this case, just some text.

**[Lines 14-17]** We specify that we want an instance of the _App_ component attached to a DOM element of id _container_. This element can be found in _index.html_. In other words, we want _App_ to appear on our main page inside the _container_ div.

You're probably getting a complaint in the terminal from ESLint to the tune of this:

 | <div class="crayon-pre">
  <p>4<span class="crayon-o">:</span>1errorComponentshouldbewrittenasapurefunctionreact<span class="crayon-o">/</span>prefer<span class="crayon-o">-</span>stateless<span class="crayon-o">-</span>function</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Don't worry about it for now. We're going to go over the [different styles of component creation](http://patternhatch.com/2016/08/02/a-primer-on-the-react-ecosystem-part-2-of-3/#twostyles) later on in this post, and this error will go away as we grow our _App_ container.

Our app should now appear like this:

[<embed src="http://patternhatch.com/wp-content/uploads/2016/07/respotify_app_container_02-300x176.png%20300w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_app_container_02-768x450.png%20768w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_app_container_02-500x293.png%20500w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_app_container_02-150x88.png%20150w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_app_container_02-400x235.png%20400w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_app_container_02-200x117.png%20200w,%20http://patternhatch.com/wp-content/uploads/2016/07/respotify_app_container_02.png%20771w" class="alignnone size-full wp-image-683" width="771">](http://patternhatch.com/wp-content/uploads/2016/07/respotify_app_container_02.png)

Great, we've just created our first component using ES6 syntax. Let's move on to the search bar.

**Search Bar**<br>
Create a _components_ folder in the _src_ directory. Inside the _components_ folder, create _SearchBar.jsx:_

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">React </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘react’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-t">class</span>
  <span class="crayon-e">SearchBar </span>
  <span class="crayon-r">extends</span>
  <span class="crayon-v">React</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">Component</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">render</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">return</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-v">input</span>
  <span class="crayon-o">/</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">export </span>
  <span class="crayon-st">default</span>
  <span class="crayon-v">SearchBar</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Let's look at the new stuff here.

**[Line 5]** _input_ is a React [form component](https://facebook.github.io/react/docs/forms.html) representing an input box.

**[Line 9]** We export our _SearchBar_ component, making it the default export. In other words, when someone **imports** our _SearchBar_ component, they'll be able to reference it directly.

For the file, I'm using the _.jsx_ extension and PascalCase to signal that this is a React component. This isn't necessary and some prefer to signal this using only PascalCase (SearchBar.js) or even including _react_ in the name (e.g. searchbar.react.js). Go with what you prefer as long as it's consistent.

Let's get the _SearchBar_ component on screen.

Recall from the UI breakdown that the _App_ component contains all the other components. In other words, all the other components are **nested** inside the App component. So what we need to do is **nest** the _SearchBar_ component inside the _App_ component by adding the highlighted lines to _index.js_:

 |
 | 

**[Line 3]** We import our _SearchBar_ component.

**[Line 10]** We instantiate the _SearchBar_ component inside _App_'s _render_ method.

If you decided to follow my convention and use the _.jsx_ extension, then something's broken now. Your browser page is probably a sea of red looking like this:<br>

[<embed src="http://patternhatch.com/wp-content/uploads/2016/07/searchbar_file_resolution_errors-e1469404052127-300x72.png%20300w,%20http://patternhatch.com/wp-content/uploads/2016/07/searchbar_file_resolution_errors-e1469404052127-150x36.png%20150w,%20http://patternhatch.com/wp-content/uploads/2016/07/searchbar_file_resolution_errors-e1469404052127-400x96.png%20400w,%20http://patternhatch.com/wp-content/uploads/2016/07/searchbar_file_resolution_errors-e1469404052127-200x48.png%20200w,%20http://patternhatch.com/wp-content/uploads/2016/07/searchbar_file_resolution_errors-e1469404052127.png%20424w" class="alignnone size-full wp-image-684" width="424">](http://patternhatch.com/wp-content/uploads/2016/07/searchbar_file_resolution_errors-e1469404052127.png)

<br>
When we import a component without specifying an extension, Webpack attempts to use a number of extensions to resolve it. What it does **not** try by default is _.jsx_. This is easily solved by modifying our _webpack.config.js_ file with the highlighted lines:

 |
 | 

**[Line 29]** We modify our eslint-loader to lint both _.js_ and _.jsx_ files.

**[Line 40]** We modify our babel-loader and hot-loader to handle both _.js_ and _.jsx_ files.

**[Lines 46-48]** We add an additional object to our config, instructing Webpack to resolve files with no extension, a _.js_ extension, and a _.jsx_ extension.

Restart your dev server. Webpack will now be able to find your _SearchBar.jsx_ file and your screen should look like this:<br>

[<embed src="http://patternhatch.com/wp-content/uploads/2016/07/searchbar_first_appearance-300x175.png%20300w,%20http://patternhatch.com/wp-content/uploads/2016/07/searchbar_first_appearance-768x449.png%20768w,%20http://patternhatch.com/wp-content/uploads/2016/07/searchbar_first_appearance-500x292.png%20500w,%20http://patternhatch.com/wp-content/uploads/2016/07/searchbar_first_appearance-150x88.png%20150w,%20http://patternhatch.com/wp-content/uploads/2016/07/searchbar_first_appearance-400x234.png%20400w,%20http://patternhatch.com/wp-content/uploads/2016/07/searchbar_first_appearance-200x117.png%20200w,%20http://patternhatch.com/wp-content/uploads/2016/07/searchbar_first_appearance.png%20771w" class="alignnone size-full wp-image-685" width="771">](http://patternhatch.com/wp-content/uploads/2016/07/searchbar_first_appearance.png)

So now we have a _SearchBar_ component nested in our _App_ container component. Assuming you haven't turned off the _prefer-stateless-function_ rule in ESLint, you're probably seeing the same _Component should be written as a pure function_ error for _SearchBar_. Keep going, we'll be resolving that in a few minutes.

Now, we can type into the input box, but we also need to capture the value we input. To capture user input and actions, React offers a means to pass [Event Handlers](https://facebook.github.io/react/docs/interactivity-and-dynamic-uis.html) to components. These event handlers are merely callback functions the component will call in response to certain user actions. We pass in these functions by assigning them to certain component _props_.

For our purposes, we're interested in the _input_ component's _[onChange](https://facebook.github.io/react/docs/forms.html#interactive-props)_ prop. The event handler assigned to _onChange_ will be called any time the value in the input box changes

Let's start by adding and modifying the highlighted lines in _SearchBar.jsx_:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">React </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘react’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-t">class</span>
  <span class="crayon-e">SearchBar </span>
  <span class="crayon-r">extends</span>
  <span class="crayon-v">React</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">Component</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">handleInputChange</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">event</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">console</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">log</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">event</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">target</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">value</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">render</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">return</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">input </span>
  <span class="crayon-r">onChange</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">handleInputChange</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-o">/</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">export </span>
  <span class="crayon-st">default</span>
  <span class="crayon-v">SearchBar</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**[Lines 4-6]** This is our event handler to be called any time the value in the input box changes.

**[Line 9]** We modify the way we instantiate the _input_ component by assigning our _handleInputChange_ event handler to the _onChange_ prop. _handleInputChange_ will now be called each time the input box value changes.

If you open your console and start typing, you should see output similar to the following:<br>

[<embed src="http://patternhatch.com/wp-content/uploads/2016/07/capture_onchange-300x124.png%20300w,%20http://patternhatch.com/wp-content/uploads/2016/07/capture_onchange-768x318.png%20768w,%20http://patternhatch.com/wp-content/uploads/2016/07/capture_onchange-500x207.png%20500w,%20http://patternhatch.com/wp-content/uploads/2016/07/capture_onchange-150x62.png%20150w,%20http://patternhatch.com/wp-content/uploads/2016/07/capture_onchange-400x165.png%20400w,%20http://patternhatch.com/wp-content/uploads/2016/07/capture_onchange-200x83.png%20200w,%20http://patternhatch.com/wp-content/uploads/2016/07/capture_onchange.png%20771w" class="alignnone size-full wp-image-686" width="771">](http://patternhatch.com/wp-content/uploads/2016/07/capture_onchange.png)

At this point, we're handling and logging the input, but we're still not capturing it; we're not saving it somewhere so that we can use it for a search term. Before we do that, we need to go over _state_.

**Initializing and Setting State**<br>
Recall from Part One that when we created our [first component](http://patternhatch.com/2016/07/06/a-primer-on-the-react-ecosystem-part-1-of-3/#component), we also passed it some [state](https://facebook.github.io/react/docs/interactivity-and-dynamic-uis.html#components-are-just-state-machines). To recap, we instantiated our _greeting_ component like this:

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

We said, "Create a _greeting_ component and pass it a piece of state called _name_ with value 'World'".

Inside the _greeting_ component, that state was then available through the _props_ object:

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

This _props_ object was immutable; the value of _this.props.name_ could not be changed.

In a sense, the _greeting_ component was "dumb". It was simply told what to render. It had no say in it and couldn't change anything. What we saw in Part One is how state can be **passed** to a component for consumption. Now, we'll see how a component can manage its **own** state.

We need to capture the value in our _SearchBar_'s _input_ component since it dictates what we search for. In other words, what the user types is a piece of _state_ we need to capture and keep updating.

The first step is to set _SearchBar_'s _state_ when it's first created (i.e. to initialize it). When creating components ES6-style, we initialize a component's _state_ in its _constructor_. The second step is to update the state to the latest value of the input box each time the value changes.

Add the highlighted lines to _SearchBar.jsx_:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">React </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘react’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-t">class</span>
  <span class="crayon-e">SearchBar </span>
  <span class="crayon-r">extends</span>
  <span class="crayon-v">React</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">Component</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-r">constructor</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-r">super</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">state</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">searchTerm</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">’’</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">handleInputChange</span>
  <span class="crayon-o">=</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">handleInputChange</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">bind</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">handleInputChange</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">event</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">setState</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">searchTerm</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">event</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">target</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">value</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">render</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">return</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">input </span>
  <span class="crayon-r">onChange</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">handleInputChange</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-o">/</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">export </span>
  <span class="crayon-st">default</span>
  <span class="crayon-v">SearchBar</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**[Line 4]** We declare a _constructor_ function and have it accept the component's _props_ as an argument.

**[Line 5]** We call SearchBar's parent constructor through the _super_ call. There are two important things to note here. First, whenever we explicitly declare a child constructor, we **have** to call _super_. If we don't, the _[this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)_ keyword will be undefined. Second, we pass _props_ as an argument to the _super_ call. Doing this gives us access to _props_ throughout our _SearchBar_ component through _this.props_.

Check it out yourself. If you delete the _super_ call, you'll see a terminal error stating _'this' is not allowed before super()_. If you include the _super_ call without passing it _props_, then try to log _this.props_, _this.props_ be _undefined._

**[Lines 6-8]** We set our initial _state_, which consists of a key called _searchTerm_ assigned an empty string to start.

**[Line 9]** We explicitly bind _this_ to the _handleInputChange_ event handler_._ This will require further explanation which I'll get to next.

**[Lines 13-15]** Each time the input box value changes, _handleInputChange_ will be called, which is where we set the new state. To set the state, we call the _[setState](https://facebook.github.io/react/docs/component-api.html#setstate)_ function defined in _SearchBar_'s parent, _React.Component,_ and assign the latest value to _searchTerm_.

A bit more about the explicit _this_ binding on **[Line 9]**. You may know that the value of _this_ inside a function depends on **how** that function was called; it depends on what was on the **left-side** of the function at the call site. Because we are passing _handleInputChange_ down to the _input_ component, we want to ensure that when _input_ invokes _handleInputChange_, the _this_ value points to _SearchBar._ In other words, we want _handleInputChange_ to be invoked **as if** _SearchBar_ was on the left-side of it. That way, the _this_ value will contain all the properties we expect such as _props_ and the _setState_ function.

Explicitly binding _this_ is required for ES6-style components (but not for components created ES5-style). Without the explicit binding, _setState_ inside _handleInputChange_ will fail to resolve, leading to this error:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">Uncaught </span>
  <span class="crayon-v">TypeError</span>
  <span class="crayon-o">:</span>
  <span class="crayon-e">Cannot </span>
  <span class="crayon-e">read </span>
  <span class="crayon-m">property</span>
  <span class="crayon-s">‘setState’</span>
  <span class="crayon-e">of </span>
  <span class="crayon-v">undefined</span>
</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

We'll be doing more explicit binding of _this_ as we build out our other components. For coverage about all the intricacies of _this_, I recommend Kyle Simpson's excellent [You Don't Know JS](https://github.com/getify/You-Dont-Know-JS) series.

So that's all it takes to update a component's internal state. From here on out, each time the value in the input box changes, _handleInputChange_ will be called, which in turn will update our _searchTerm_ state to the latest input box value.

Before we move on, note that we should always update state using the _setState_ function, and never change it directly (e.g. this.state.searchTerm = "don't do this"). Mutating state directly bypasses React's state management system and can result in unpredictable behavior.

Finally, the ESLint error previously showing for _SearchBar_ (_Component should be written as a pure function_) should now be gone. You'll understand why when we look at the two ways to create ES6-style components.

Alright, so now we have a _SearchBar_ component which tracks a piece of its internal state. We can now use it to fire a search request at Spotify. Let's take a look at the API methods we'll be using.

**Using the Spotify API**<br>
We want to use the Spotify API to:

1. retrieve an artist's albums,
2. retrieve an album's tracks,
3. play a preview clip of a track.

Fortunately, at the time of this writing, Spotify does **not** require us to register for an API key. Let's take a look at what Spotify provides by sending it a few search queries.

I'll be using the excellent [Postman](https://www.getpostman.com/) for Chrome to try out the API. I highly recommend Postman, but using an online service like [hurl.it](https://www.hurl.it/), your browser directly, or even [curl](https://curl.haxx.se/) will work.

To retrieve an artist's albums, I set up Postman like so and click _Send_:<br>

[<embed src="http://patternhatch.com/wp-content/uploads/2016/07/postman_search_album-300x65.png%20300w,%20http://patternhatch.com/wp-content/uploads/2016/07/postman_search_album-500x108.png%20500w,%20http://patternhatch.com/wp-content/uploads/2016/07/postman_search_album-400x87.png%20400w,%20http://patternhatch.com/wp-content/uploads/2016/07/postman_search_album-200x43.png%20200w,%20http://patternhatch.com/wp-content/uploads/2016/07/postman_search_album.png%20756w" class="alignnone size-full wp-image-687" width="756">](http://patternhatch.com/wp-content/uploads/2016/07/postman_search_album.png)

If you're not using Postman, just copy and paste this into your browser bar:<br>
<https://api.spotify.com/v1/search?q=My%20Bloody%20Valentine&type=album>

What we get back is a JSON payload listing the artist's albums, with each album accompanied by the album name, ID, and album cover at multiple dimensions, among other information.

In a browser, the JSON response payload looks like this:<br>

[<embed src="http://patternhatch.com/wp-content/uploads/2016/07/json_albums_payload-300x252.png%20300w,%20http://patternhatch.com/wp-content/uploads/2016/07/json_albums_payload-768x646.png%20768w,%20http://patternhatch.com/wp-content/uploads/2016/07/json_albums_payload-500x421.png%20500w,%20http://patternhatch.com/wp-content/uploads/2016/07/json_albums_payload-150x126.png%20150w,%20http://patternhatch.com/wp-content/uploads/2016/07/json_albums_payload-400x337.png%20400w,%20http://patternhatch.com/wp-content/uploads/2016/07/json_albums_payload-200x168.png%20200w,%20http://patternhatch.com/wp-content/uploads/2016/07/json_albums_payload.png%20769w" class="alignnone size-full wp-image-698" width="769">](http://patternhatch.com/wp-content/uploads/2016/07/json_albums_payload.png)

We can see that the albums are in the _items_ array.

If you take one of the URLs from an album's _images_ array such as this:<br>
<https://i.scdn.co/image/9b81a34f8aed2f82bb9287764eb520d2af1e6172>

and paste it into the browser bar, you should get the cover.

Now let's try retrieving the tracks for an album. Take one of the album IDs in the payload. I'll be using the ID of the first album in the _items_ array: _3GH4IiI6jQAIvnHVdb5FB6_.

To retrieve an album's tracks, use this request:<br>
<https://api.spotify.com/v1/albums/3GH4IiI6jQAIvnHVdb5FB6>

What we get back is a JSON payload with information about the album. The payload also includes an object called _tracks_ , which has an _items_ array holding the tracks:<br>

[<embed src="http://patternhatch.com/wp-content/uploads/2016/07/json_tracks_payload-300x267.png%20300w,%20http://patternhatch.com/wp-content/uploads/2016/07/json_tracks_payload-500x446.png%20500w,%20http://patternhatch.com/wp-content/uploads/2016/07/json_tracks_payload-400x356.png%20400w,%20http://patternhatch.com/wp-content/uploads/2016/07/json_tracks_payload-200x178.png%20200w,%20http://patternhatch.com/wp-content/uploads/2016/07/json_tracks_payload.png%20762w" class="alignnone size-full wp-image-699" width="762">](http://patternhatch.com/wp-content/uploads/2016/07/json_tracks_payload.png)

<br>
One key of interest accompanying each track is the _preview_url_. If we copy and paste one of these such as:<br>
<https://p.scdn.co/mp3-preview/3e01581bd39f90b2248770cbf52dae0553ccf2ab>

into our browser bar, the browser should (hopefully) play a 30-second clip of the track.

Ok, so we now know how to get albums, get tracks, and play a preview of each track. Summarizing the API calls needed:

- Retrieve an artist's albums: _<https://api.spotify.com/v1/search?q=>

  <span>{artist}</span>

  &type=album_
- Retrieve an album's tracks: _<https://api.spotify.com/v1/albums/>

  <span>{albumId}</span>_

- Play a preview: Hit the _preview_url_ of the track we're interested in.

Great, let's start moving this functionality into our app.

**Fetching data**<br>
To fetch data from Spotify, we're going to use a library called [axios](https://github.com/mzabriskie/axios), a [promise](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise)-based HTTP client. Start by installing it like so:

Next, in your _src_ directory, create a directory called _api_.

Inside the _api_ directory, create a file called _musicApi.js._ We're going to put our calls to Spotify in _musicApi.js_, isolating them in one place. Your project's _src_ directory structure should now look like this:

 | <div class="crayon-pre">
  <p><span class="crayon-o">-</span>src</p>
  <p>
  <span class="crayon-e"> -api</span>
</p>
  <p><span class="crayon-o">–</span>musicApi<span class="crayon-sy">.</span>js</p>
  <p>
  <span class="crayon-e"> -components</span>
</p>
  <p><span class="crayon-o">–</span>SearchBar<span class="crayon-sy">.</span>jsx</p>
  <p><span class="crayon-o">–</span>index<span class="crayon-sy">.</span>html</p>
  <p><span class="crayon-o">–</span>index<span class="crayon-sy">.</span>js</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Populate _musicApi.js_:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">axios </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘axios’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-t">function</span>
  <span class="crayon-e">fetch</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">request</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">callback</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">axios</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">get</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">request</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-sy">.</span>
  <span class="crayon-st">then</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">response</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">callback</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">response</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">data</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">export </span>
  <span class="crayon-t">function</span>
  <span class="crayon-e">getAlbums</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">artist</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">callback</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">request</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy"><code>&lt;/span&gt;&lt;span class="crayon-v"&gt;https&lt;/span&gt;&lt;span class="crayon-o"&gt;:&lt;/span&gt;&lt;span class="crayon-c"&gt;//api.spotify.com/v1/search?q=${artist}&amp;amp;type=album</code>;</span>
</p>
  <p>
  <span class="crayon-e">fetch</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">request</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">callback</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">export </span>
  <span class="crayon-t">function</span>
  <span class="crayon-e">getTracks</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">albumId</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">callback</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">request</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy"><code>&lt;/span&gt;&lt;span class="crayon-v"&gt;https&lt;/span&gt;&lt;span class="crayon-o"&gt;:&lt;/span&gt;&lt;span class="crayon-c"&gt;//api.spotify.com/v1/albums/${albumId}</code>;</span>
</p>
  <p>
  <span class="crayon-e">fetch</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">request</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">callback</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Our API is straight-forward, but let's do a quick walkthrough. At the top, we import the _axios_ library.

**[Lines 3-8]** We declare a generic _fetch_ function which takes a request to send, and a callback to pass the response to. We pass the request to axios' _get_ method, which returns a promise. Once that promise resolves (i.e. data is available), we call our callback with it. Axios stores the actual payload content in the response's _data_ object.

**[Lines 10-13]** In this function, we use a [template literal](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Template_literals) to form our album request before calling _fetch_ with it.

**[Lines 15-18]** Here we do the same for tracks.

Let's take stock of where we are. We have an _App_ container, a _SearchBar_ component, and an API which can help us retrieve music from Spotify.

Let's move on to creating our _AlbumList_ component.

**Album List**<br>
In your _component_ directory, create _AlbumList.jsx_:































 | <div class="crayon-pre">
  <p>
</p>
  <p>
</p>
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">React </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘react’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
</p>
  <p>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">AlbumList</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
</p>
  <p>
</p>
  <p><span class="crayon-m">const</span><span class="crayon-v">albums</span><span class="crayon-o">=</span><span class="crayon-v">props</span><span class="crayon-sy">.</span><span class="crayon-v">albums</span><span class="crayon-sy">.</span><span class="crayon-e">map</span><span class="crayon-sy">(</span><span class="crayon-sy">(</span><span class="crayon-v">album</span><span class="crayon-sy">)</span><span class="crayon-o">=</span><span class="crayon-o">&gt;</span><span class="crayon-o">&lt;</span><span class="crayon-e">li</span><span class="crayon-o">&gt;</span><span class="crayon-sy">{</span>&lt;span class="crayo</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
n-v">album

<span class="crayon-sy">.</span>

<span class="crayon-v">name</span>

<span class="crayon-sy">}</span>

<span class="crayon-o">&lt;</span>

<span class="crayon-o">/</span>

<span class="crayon-v">li</span>

<span class="crayon-o">&gt;</span>

<span class="crayon-sy">)</span>

<span class="crayon-sy">;</span>





<span class="crayon-st">return</span>

<span class="crayon-sy">(</span>





<span class="crayon-o">&lt;</span>

<span class="crayon-e">ul</span>

<span class="crayon-o">&gt;</span>





<span class="crayon-sy">{</span>

<span class="crayon-v">albums</span>

<span class="crayon-sy">}</span>





<span class="crayon-o">&lt;</span>

<span class="crayon-o">/</span>

<span class="crayon-v">ul</span>

<span class="crayon-o">&gt;</span>





<span class="crayon-v">AlbumList</span>

<span class="crayon-sy">.</span>

<span class="crayon-v">propTypes</span>

<span class="crayon-o">=</span>

<span class="crayon-sy">{</span>





<span class="crayon-v">albums</span>

<span class="crayon-o">:</span>

<span class="crayon-v">React</span>

<span class="crayon-sy">.</span>

<span class="crayon-v">PropTypes</span>

<span class="crayon-sy">.</span>

<span class="crayon-t">array</span>

<span class="crayon-sy">.</span>

<span class="crayon-v">isRequired</span>

<span class="crayon-sy">,</span>





<span class="crayon-e">export </span>

<span class="crayon-st">default</span>

<span class="crayon-v">AlbumList</span>

<span class="crayon-sy">;</span>



**[Line 3]** We define a function called _AlbumList_ which takes a _props_ argument.

**[Line 4]** _props_ will have an _albums_ property which holds the array of albums we want to render. We map over the albums, creating an array of _<li>_ elements, with each holding an album name.

**[Lines 6-10]** We return a _<ul>_ element, with our list of album names inside.****

**[Lines 13-15]** This is our first encounter with _propTypes_. In development mode, React checks the _props_ that are passed into a component. For each _props_ property, React attempts to look it up in the component's _propTypes_ object to ensure that (a) the prop is expected and (b) the prop is of the correct type. In this case, we're specifying that the _AlbumList_ component is expecting a _props_ property called _albums_, that it's an array, and that it's required.

Your app will still work **without** this _propTypes_ object, but you'll see errors in your browser console as well as from ESLint.

Now clearly the _AlbumList_ component has been created differently:

- It does not extend _React.Component_.
- It's a function, not a class.
- It does not have a _render_ method.
- It's passed _props_ explicitly.

There are reasons for this. Let's briefly go over the difference between a component such as _SearchBar_, and a component such as _AlbumList_.

**Two Styles of Component Creation**<br>
There are two main ways to create a component in React:

1. As a stateful component.
2. As a stateless, functional component.

A stateful component is exactly what it sounds like: it tracks some form of internal state. In addition, by extending _React.Component_, it also must have a _render_ method, and may optionally implement other methods such as [Lifecycle](https://facebook.github.io/react/docs/component-specs.html#lifecycle-methods) callbacks.

Lifecycle callbacks are handy when you want to be notified when a component is about to be [rendered](https://facebook.github.io/react/docs/component-specs.html#mounting-componentwillmount), or [updated](https://facebook.github.io/react/docs/component-specs.html#updating-componentwillupdate), or at another step in its lifecycle. A stateful component also has access to the _this_ keyword through which it accesses its _props_ (this is assuming you handled _this_ correctly in the constructor as mentioned earlier).

_SearchBar.jsx_ is an example of a stateful component. Taking a look at it as it currently is:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">React </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘react’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-t">class</span>
  <span class="crayon-e">SearchBar </span>
  <span class="crayon-r">extends</span>
  <span class="crayon-v">React</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">Component</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-r">constructor</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-r">super</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">state</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">searchTerm</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">’’</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">handleInputChange</span>
  <span class="crayon-o">=</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">handleInputChange</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">bind</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">handleInputChange</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">event</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">setState</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">searchTerm</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">event</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">target</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">value</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">render</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">return</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">input </span>
  <span class="crayon-r">onChange</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">handleInputChange</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-o">/</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">export </span>
  <span class="crayon-st">default</span>
  <span class="crayon-v">SearchBar</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

We can see that it tracks the value of the input box as a piece of internal state, implements a _render_ method, and also uses the _this_ keyword to pass the _handleInputChange_ method down to the _input_ component.

In contrast, a functional, stateless component is a pure function which doesn't track anything internally. When given X, it always outputs Y.

_AlbumList_ is an example of a functional, stateless component. It doesn't need to track state, it doesn't need access to the _this_ keyword, and its sole responsibility is to render an array of albums passed in through its _props_.

How do you choose between creating a stateful and a stateless component?

My view is that state in general is like a useful virus that needs to be contained. A little bit helps us build what we need to build. But a lot of it, spread out, leads to buggy code that's hard to reason about. So in general, favor creating functional, stateless components as:

- being functional and stateless, they are easier to reason about, test, and debug.
- _props_ are explicit and there is no need to wrestle with the intricacies of the _this_ keyword.
- they are leaner than their stateful counterparts.

This is why ESLint is still complaining about how our _App_ component should be written as a pure function, since it currently doesn't track any state and can be technically written in the preferred stateless form. We'll keep it as a stateful component as we're going to track state in it soon.

There are a few advanced exceptions where you may still want to make something a class-based (stateful) component even without tracking any internal state. For example, one lifecycle method available is _[shouldComponentUpdate](https://facebook.github.io/react/docs/component-specs.html#updating-shouldcomponentupdate)_, which allows you to write custom code to check whether a component should update at all. If you can write a fast check here, you may get [improved performance](https://facebook.github.io/react/docs/advanced-performance.html), but to start with that is often premature optimization.

Let's wire this stuff up and get something showing on the screen.

**Retrieving Albums**<br>
It's finally time to retrieve albums and display them on screen. _AlbumList_ is ready to receive an array to render, so no changes needed there for now.

What we need to do is use _SearchBar_'s input box value as a search term for our _musicApi_. Let's start by modifying _index.js_ by adding the highlighted lines:

 |
 | 

**[Lines 4-5]** We import our _AlbumList_ component and all the functions from _musicApi.js_.

**[Lines 8-9]** We create a constructor and call _super_. Notice that we don't pass in the _props_ argument because, being the overall container, _App_ doesn't require access to it. However, we still call _super_ in order to gain access to _this_. Without calling _super_, _this_ remains undefined.

**[Lines 10-12]** We initialize a piece of state called _albums_, assigning it an empty array as its value. This state will hold the list of albums retrieved for the current artist.

**[Line 13-14]** We explicitly bind _this_ to the _getAlbums_ callback we intend to pass to the _SearchBar_ component, and the _processAlbums_ callback we intend to pass to _musicApi_.

**[Lines 17-19]** This is the function we'll pass down to our _SearchBar_ component as one of its _props_. When a user hits enter in the input box, _App_'s _getAlbums_ function will be invoked. _getAlbums_ in turn will call _musicApi_'s _getAlbums_ function.

**[Lines 21-25]** This is the function we pass our _musicApi_ to call back on once it retrieves the albums.

**[Line 30]** We pass _App_'s _getAlbums_ function down as a prop to _SearchBar._

**[Line 31]** We pass our array of albums down as a prop to _AlbumList_. Since the initial value of the _albums_ state is an empty array, nothing will be rendered.

Because our _App_ component now tracks some state, the ESLint error should now be gone.

Let's make the necessary changes to _SearchBar.jsx_. Add or modify the highlighted lines:

 |
 | 

**[Lines 10]** Explicitly bind _this_ to the _handleKeyPress_ event handler.

**[Lines 19-23]** _handleKeyPress_ will detect when the 'Enter' key is pressed. Upon that happening, the passed-in _getAlbums_ function will be called with the _searchTerm_.

**[Line 26]** We pass our _handleKeyPress_ event handler to the _input_ component.

**[Lines 30-32]** We specify that our component expects a prop called _getAlbums_, the value of which is a function, and that it's required.

Let's give the search feature a try.

If you don't already have it running, fire up your dev server. Enter an artist into the search box and hit the _Enter_ key. Your browser should now render a list of albums in response:<br>

[<embed src="http://patternhatch.com/wp-content/uploads/2016/07/first_search-300x210.png%20300w,%20http://patternhatch.com/wp-content/uploads/2016/07/first_search-768x537.png%20768w,%20http://patternhatch.com/wp-content/uploads/2016/07/first_search-500x350.png%20500w,%20http://patternhatch.com/wp-content/uploads/2016/07/first_search-150x105.png%20150w,%20http://patternhatch.com/wp-content/uploads/2016/07/first_search-400x280.png%20400w,%20http://patternhatch.com/wp-content/uploads/2016/07/first_search-200x140.png%20200w,%20http://patternhatch.com/wp-content/uploads/2016/07/first_search.png%20771w" class="alignnone size-full wp-image-707" width="771">](http://patternhatch.com/wp-content/uploads/2016/07/first_search.png)

To summarize the logic flow, here's what's happening:

1. User types a search term into the _input_ box.
2. User hits the _Enter_ key.
3. _SearchBar_, through its _props_, calls _getAlbums_ in _index.js_, which leads to _getAlbums_ in _musicApi.js_ getting called.
4. _musicApi_ makes the call to Spotify. When it receives a response, it passes the response to the callback. In this case, the callback is _processAlbums_ in _index.js_.
5. _processAlbums_ updates the piece of internal state called _albums_, assigning it the array of _items_ in the payload.
6. This new state is passed to _AlbumList_, which then re-renders the list of album names.

Despite everything working, if you look at your browser's console output, you should see something like this:

 | <div class="crayon-pre">
  <p><span class="crayon-r">Warning</span><span class="crayon-o">:</span>Eachchild<span class="crayon-k">in</span>anarrayoriteratorshouldhaveaunique<span class="crayon-s">“key”</span>prop<span class="crayon-sy">.</span></p>
  <p><span class="crayon-r">Check</span>therendermethodof<span class="crayon-sy">
  <code>&lt;/span&gt;AlbumList&lt;span class="crayon-sy"&gt;</code>
</span><span class="crayon-sy">.</span></p>
  <p><span class="crayon-r">See</span>https<span class="crayon-o">:</span><span class="crayon-o">/</span><span class="crayon-o">/</span>fb<span class="crayon-sy">.</span>me<span class="crayon-o">/</span>react<span class="crayon-o">-</span>warning<span class="crayon-o">-</span>keys<span class="crayon-k">for</span><span class="crayon-r">more</span>information<span class="crayon-sy">.</span></p>
  <p><span class="crayon-k">in</span>li<span class="crayon-sy">(</span>createdbyAlbumList<span class="crayon-sy">)</span></p>
  <p><span class="crayon-k">in</span>AlbumList<span class="crayon-sy">(</span>createdbyApp<span class="crayon-sy">)</span></p>
  <p><span class="crayon-k">in</span>div<span class="crayon-sy">(</span>createdbyApp<span class="crayon-sy">)</span></p>
  <p><span class="crayon-k">in</span>App</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

When we render lists of children as we do in _AlbumList_, we should supply each list item a unique key. React then uses these keys for [reconciliation](https://facebook.github.io/react/docs/multiple-components.html#dynamic-children) during state changes, reordering, or filtering. This subsequently leads to better performance. In the absence of explicit keys, React uses the array index as the key (but it'll still complain anyway). We'll remedy this when we create the _Album_ component.

Before we build more components, let's go over how React re-renders the album list as you make new searches.

**The Virtual DOM**<br>
What makes a [Single-Page Application (SPA)](https://en.wikipedia.org/wiki/Single-page_application) like ours appealing is the fluidity of interaction. Whenever you invoke a new search, the album list updates without requiring a full page reload. Likewise, when you use an app like Gmail and pull up an email, the inbox gets 'hidden' and replaced by the message, while the sidebar and other parts of the page remain. This leads to a snappy, desktop-like experience.

Delivering this experience requires figuring out which part of the DOM to update when a change or interaction occurs. Different Javascript frameworks use different strategies. Ember uses [data-binding](https://guides.emberjs.com/v1.10.0/object-model/bindings/), Angular 1 uses [dirty checking](https://docs.angularjs.org/guide/scope), and React uses the [Virtual DOM](https://facebook.github.io/react/docs/displaying-data.html#reactive-updates), which we'll briefly cover.

When a component's _render_ method is first called, it outputs a model of your DOM called a _Virtual DOM,_ not the actual DOM elements themselves. The _Virtual DOM_ is a Javascript data structure representing what your DOM should look like. React then takes this representation and generates the actual DOM elements from it.

From then on, any time there's a state change in your component (i.e. _setState_ is called), the component's _render_ method will be invoked, and a **new** _Virtual DOM_ created. This **new** _Virtual DOM_ is then **diff'd** against the **previous** _Virtual DOM_. The results of this diff operation represent the actual DOM changes to make. Your DOM is then 'patched' with these changes, resulting in a change in the display.

Let's walk through how this applies to our app in its current state:

1. At startup, our album list is an empty array. When our _App_'s _render_ method is called, the _Virtual DOM_ has no data representing our list of albums, resulting in nothing being displayed.
2. After a search is executed and results returned from our _musicApi_, _setState_ in _index.js_ is called, where the _albumList_ state is set to the array of albums returned.
3. React detects this state change and calls _render_, which outputs a new _Virtual DOM_ representation.
4. This new _Virtual DOM_ is diff'd against the previous _Virtual DOM_, and it's determined that the album list has changed (from an empty array to something).
5. A DOM patch is generated and takes effect, resulting in our list of albums getting rendered.

Subsequent artist searches follow the same flow.

The implications of this are that:

1. Only the parts of the DOM which need to change are changed.
2. The DOM changes are batched for greater efficiency.
3. For you as the app developer, DOM updates are automatic and taken care of.

Now that we have a handle on how React re-renders when there's a state update, let's look to improve the look of our app.

**A Detour Through Aesthetics**<br>
In this section, we'll spruce up our app with better layout and styling. Nothing fancy. Just enough to get it to demo quality.

First, let's get rid of displaying albums with text and use album covers instead. Make the following change to _AlbumList.jsx_:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">React </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘react’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">AlbumList</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">albums</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">albums</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">map</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">album</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">return</span>
  <span class="crayon-sy">(</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-v">li</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">img</span>
</p>
  <p>
  <span class="crayon-v">src</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">album</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">images</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-cn">1</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">url</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-v">alt</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">album</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">name</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">li</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-st">return</span>
  <span class="crayon-sy">(</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">ul</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">albums</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">ul</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-v">AlbumList</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">propTypes</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">albums</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">React</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">PropTypes</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-t">array</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">isRequired</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-e">export </span>
  <span class="crayon-st">default</span>
  <span class="crayon-v">AlbumList</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

After the change, our app should look like this:<br>

[<embed src="http://patternhatch.com/wp-content/uploads/2016/07/album_covers-258x300.png%20258w,%20http://patternhatch.com/wp-content/uploads/2016/07/album_covers-768x894.png%20768w,%20http://patternhatch.com/wp-content/uploads/2016/07/album_covers-500x582.png%20500w,%20http://patternhatch.com/wp-content/uploads/2016/07/album_covers-400x465.png%20400w,%20http://patternhatch.com/wp-content/uploads/2016/07/album_covers-200x233.png%20200w,%20http://patternhatch.com/wp-content/uploads/2016/07/album_covers.png%20771w" class="alignnone size-full wp-image-708" width="771">](http://patternhatch.com/wp-content/uploads/2016/07/album_covers.png)

For layout, we'll be using [Bootstrap](http://getbootstrap.com/). We won't be using much of Bootstrap, so to keep it simple, we're going to just import Bootstrap directly from a CDN in our _index.html_:

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
  <span class="crayon-r">&lt;link </span>
  <span class="crayon-e">rel</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“stylesheet”</span>
  <span class="crayon-e">href</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“<a href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css”">https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css”</a></span>
  <span class="crayon-r">&gt;</span>
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
 | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Next, we'll apply some basic styles to our _SearchBar_ and _AlbumList_ components. With React, you have the option of using plain CSS styling, or [_inline styling_](https://facebook.github.io/react/tips/inline-styles.html). The former is the same thing we've been doing for ages. You simply create a _style.css_ file, import it in your _index.html_ file, and then refer to the selectors.

Inline styling is styling using Javascript. With inline styling, you create a Javascript object representing your styles, then pass that object as a prop to your component.

I actually prefer using an explicit css file_._ The technique is well-understood, more reusable, and industry standard. However, because this is a tutorial, I'm going to use inline styling to just show you a different way to approach styling React components. This way, you'll know both approaches and can choose for yourself.

Let's start with the _SearchBar_ component. Make the highlighted additions and changes to _SearchBar.jsx_:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">React </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘react’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-t">class</span>
  <span class="crayon-e">SearchBar </span>
  <span class="crayon-r">extends</span>
  <span class="crayon-v">React</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">Component</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-r">constructor</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-r">super</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">state</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">searchTerm</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">’’</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">handleInputChange</span>
  <span class="crayon-o">=</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">handleInputChange</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">bind</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">handleKeyPress</span>
  <span class="crayon-o">=</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">handleKeyPress</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">bind</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">handleInputChange</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">event</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">setState</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">searchTerm</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">event</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">target</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">value</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">handleKeyPress</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">event</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">if</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">event</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">key</span>
  <span class="crayon-o">===</span>
  <span class="crayon-s">‘Enter’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">getAlbums</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">state</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">searchTerm</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">render</span>
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
  <span class="crayon-v">style</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">SearchBar</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">styles</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">div</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-v">h3</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-e">Search </span>
  <span class="crayon-st">for</span>
  <span class="crayon-e">an </span>
  <span class="crayon-v">Artist</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">h3</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">input</span>
</p>
  <p>
  <span class="crayon-r">onChange</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">handleInputChange</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-v">onKeyPress</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">handleKeyPress</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-v">style</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">SearchBar</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">styles</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">input</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">div</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-v">SearchBar</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">propTypes</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">getAlbums</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">React</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">PropTypes</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">func</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">isRequired</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">SearchBar</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">styles</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">div</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">margin</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">30</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">textAlign</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘center’</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">input</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">width</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘60%’</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-e">export </span>
  <span class="crayon-st">default</span>
  <span class="crayon-v">SearchBar</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Examing this from the **bottom-up**:

**[Lines 43-51]** We add a _styles_ object to our _SearchBar_ component. The _styles_ object itself has styling for a div, and the input box. Naming the styling objects _div_ and _input_ is entirely my choice. However, notice how the Javascript keys in the _div_ and _input_ objects look like their CSS counterparts, except they're in camelCase (e.g. textAlign vs text-align).

**[Line 29-33]** We pass the _input_ component its _style_ prop, specifying that its width should be 60% of its container's width.

**[Line 28]** Just a label for the input box.

**[Line 27]** We now enclose the _input_ component in a div, passing the div a _style_ prop the value of which is the _div_ styling object. In other words, we are specifying that this div should have a margin of 30px and its contents should be centered.

In addition, 

<span>
  <strong>remove</strong>
</span>

the _<h1>_ from _index.html_ **[Line 8]***.*

If you look at your app now, you should see the styling take effect:<br>

[<embed src="http://patternhatch.com/wp-content/uploads/2016/07/centered_searchbar-300x73.png%20300w,%20http://patternhatch.com/wp-content/uploads/2016/07/centered_searchbar-768x187.png%20768w,%20http://patternhatch.com/wp-content/uploads/2016/07/centered_searchbar-500x122.png%20500w,%20http://patternhatch.com/wp-content/uploads/2016/07/centered_searchbar-400x98.png%20400w,%20http://patternhatch.com/wp-content/uploads/2016/07/centered_searchbar-200x49.png%20200w,%20http://patternhatch.com/wp-content/uploads/2016/07/centered_searchbar.png%20771w" class="alignnone size-full wp-image-709" width="771">](http://patternhatch.com/wp-content/uploads/2016/07/centered_searchbar.png)

To summarize, if you want to style React components inline, create an object representing the style you want to apply, then pass that object as a _style_ prop to the target component.

Let's also apply some styling to _AlbumList.jsx_:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">React </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘react’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">AlbumList</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">albums</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">albums</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">map</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">album</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">return</span>
  <span class="crayon-sy">(</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-v">li</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">img</span>
</p>
  <p>
  <span class="crayon-v">src</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">album</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">images</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-cn">1</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">url</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-v">alt</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">album</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">name</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">li</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
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
  <span class="crayon-s">“col-md-4”</span>
  <span class="crayon-v">style</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">AlbumList</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">styles</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">div</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">ul </span>
  <span class="crayon-v">style</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">AlbumList</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">styles</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">ul</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">albums</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">ul</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">div</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-v">AlbumList</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">propTypes</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">albums</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">React</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">PropTypes</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-t">array</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">isRequired</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">AlbumList</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">styles</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">div</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">width</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">370</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">marginLeft</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">30</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">textAlign</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘right’</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">maxHeight</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘85vh’</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">overflowY</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘auto’</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">ul</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">listStyle</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘none’</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-e">export </span>
  <span class="crayon-st">default</span>
  <span class="crayon-v">AlbumList</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Again working our way from the bottom-up:

**[Lines 28-39]** We create a _styles_ object holding the styles we want to apply to the _div_ and the _ul_ elements.

**[Line 17]** The ul styling is passed as a _style_ prop down to the ul component.

**[Lines 16, 20]** The ul component is wrapped in a div, which is also supplied a _style_ prop. In addition, we use a bit of Bootstrap to place our album div in four columns.

The end effect is to render our list of albums with its own scrollbar:

[<embed src="http://patternhatch.com/wp-content/uploads/2016/07/searchbar_scroll-300x214.png%20300w,%20http://patternhatch.com/wp-content/uploads/2016/07/searchbar_scroll-768x548.png%20768w,%20http://patternhatch.com/wp-content/uploads/2016/07/searchbar_scroll-500x357.png%20500w,%20http://patternhatch.com/wp-content/uploads/2016/07/searchbar_scroll-150x107.png%20150w,%20http://patternhatch.com/wp-content/uploads/2016/07/searchbar_scroll-400x285.png%20400w,%20http://patternhatch.com/wp-content/uploads/2016/07/searchbar_scroll-200x143.png%20200w,%20http://patternhatch.com/wp-content/uploads/2016/07/searchbar_scroll.png%20771w" class="alignnone size-full wp-image-710" width="771">](http://patternhatch.com/wp-content/uploads/2016/07/searchbar_scroll.png)

This way, as we scroll, the list of tracks on the right will remain in view.

Now that we have our app looking good enough for a demo, let's move on to creating the individual _Album_ component.

**Album**<br>
Currently we render our albums directly inside the _AlbumList_ component. What we want is to have an actual _Album_ component take care of it.

Start by creating _Album.jsx_ in your _components_ folder:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">React </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘react’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">Album</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">return</span>
  <span class="crayon-sy">(</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-v">li</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">img</span>
</p>
  <p>
  <span class="crayon-v">src</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">album</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">images</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-cn">1</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">url</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-v">alt</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">album</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">name</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-v">style</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">Album</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">styles</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">img</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">li</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-v">Album</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">propTypes</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">album</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">React</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">PropTypes</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-t">object</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">isRequired</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">Album</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">styles</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">img</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">marginBottom</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘1em’</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-e">export </span>
  <span class="crayon-st">default</span>
  <span class="crayon-v">Album</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Hopefully by now the patterns are starting to set in and things are looking familiar.

**[Line 3]** We create the _Album_ component as a stateless, functional component receiving explicit _props_. The _props_ will contain one album to render.

**[Lines 4-12]** We return a list item representing the album. The item itself, an _img_ element, includes some styling.

**[Lines 15-17]** We set up the _propTypes_ specifying a _props_ value called _album_ is expected, and that it's an object.

**[Lines 19-23]** We specify some styling for our _img_ element.

We can now refactor _AlbumList.jsx_ as follows:

 |
 | 

**[Line 2]** We import our _Album_ component.

**[Lines 4-5]** For each album, we instantiate an _Album_ component and pass it the album object as a prop. We also now pass each _Album_ component a unique key in the form of the album id. With this addition, the warning you see in your browser console should now be gone.

Our app should look identical to the way it was before the refactoring save for some spacing between the albums.

Let's move on to retrieving, rendering, and playing tracks.

**Track List**<br>
From here on out, we're going to move pretty quick. Everything we've learned building the _AlbumList_ and _Album_ components applies directly to the _TrackList_ and _Track_ components we're going to build.

Let's start by creating _TrackList.jsx_ in the _components_ folder:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">React </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘react’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">Track </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘./Track’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">TrackList</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">tracks</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">tracks</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">map</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">track</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">Track </span>
  <span class="crayon-v">key</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">track</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">id</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-v">track</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">track</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-o">/</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
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
  <span class="crayon-s">“col-md-3”</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">ul </span>
  <span class="crayon-v">style</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">listStyle</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘none’</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">tracks</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">ul</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">div</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-v">TrackList</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">propTypes</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">tracks</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">React</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">PropTypes</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-t">array</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">isRequired</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-e">export </span>
  <span class="crayon-st">default</span>
  <span class="crayon-v">TrackList</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**[Line 2]** We import our _Track_ component which, of course, doesn't exist yet.

**[Line 4]** The _TrackList_ component is created as a stateless, functional component.

**[Line 5]** For each track, we instantiate a _Track_ component.

**[Line 8-12]** We set our _div_ to span three columns, within which we have a _ul_ component, passing it a style **directly** rather than declaring a style object. The _ul_ component wraps our array of _Track_ components.

**[Lines 16-18]** We tell React this component expects an array in its props called _tracks._

Next, the _Track_ component.

**Track**<br>
In the components folder, create _Track.jsx:_

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">React </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘react’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-e">mouseOverColor</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">‘#ADD8E6’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-e">mouseOutColor</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">‘white’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">Track</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">return</span>
  <span class="crayon-sy">(</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">li</span>
</p>
  <p>
  <span class="crayon-v">style</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">Track</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">styles</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">li</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-v">onMouseOver</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">e</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">e</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">target</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">style</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">backgroundColor</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">mouseOverColor</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-v">onMouseOut</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">e</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">e</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">target</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">style</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">backgroundColor</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">mouseOutColor</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">track</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">name</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">li</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-v">Track</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">propTypes</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">track</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">React</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">PropTypes</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-t">object</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">isRequired</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">Track</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">styles</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">li</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">fontSize</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘1.5em’</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">padding</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘0.2em’</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">listStyleType</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘none’</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">backgroundColor</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">mouseOutColor</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-e">export </span>
  <span class="crayon-st">default</span>
  <span class="crayon-v">Track</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Similar to the _AlbumList_, _Album_, and _TrackList_ components, the _Track_ component is also a stateless, functional component with inline styling and propTypes.

I just want to point out **[Lines 10-11]**. The _li_ component can take event handlers for mouse over and mouse out events. Here, we're specifying that we want the list item to be a shade of blue when the mouse is over it, and white when the mouse is not.

Alright, so now that we have a _TrackList_ and _Track_ component, we need to retrieve the tracks themselves.

**Retrieving Tracks**<br>
We currently retrieve albums by passing a _getAlbums_ callback to our _SearchBar_ component. When the user hits enter, _SearchBar_ invokes _getAlbums_, which in turn leads to albums being retrieved from Spotify.

In a similar vein, we're going to pass a _getTracks_ callback to our _Album_ component. When the user clicks on an album, _getTracks_ will be invoked, which will lead to tracks being retrieved from Spotify. Because the _Album_ component is a child of the _AlbumList_ component, we'll need to first pass _getTracks_ to _AlbumList_. _AlbumList_ will then pass the callback further down to _Album._

Modify _index.js_ as follows:

 |
 | 

**[Line 5]** We import our _TrackList_ component.

**[Line 13]** We introduce a new piece of state called _tracks_. Similar to the _albums_ state, _tracks_ will hold the array of tracks for the currently selected album.

**[Lines 17-18]** These are the callbacks we're going to use. Since we're going to pass them elsewhere, we explicitly bind _this._

**[Lines 25-27]** This is the callback we're going to pass to the _AlbumList_ component. When it's invoked, it'll call _musicApi_'s _getTracks_.

**[Line 32]** We set our _tracks_ state to a blank array when a user retrieves albums for another artist. This way we won't have albums for one artist listed on the left, with tracks from another artist on the right.

**[Lines 36-40]** This is the callback we're going to pass to _musicApi_ when we retrieve tracks. When tracks are received from Spotify, this callback will be invoked and our _tracks_ state updated.

**[Line 46]** We modify the _AlbumList_ instantiation to include a _getTracks_ prop, the value of which is our _getTracks_ callback.

**[Line 47]** We instantiate a _TrackList_ component, passing it an array of tracks as a prop.

Next, we need to have _AlbumList_ pass the _getTracks_ callback down to _Album._ This is done with a simple modification in _AlbumList.jsx_:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">React </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘react’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">Album </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘./Album’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">AlbumList</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">albums</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">albums</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">map</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">album</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">Album </span>
  <span class="crayon-v">key</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">album</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">id</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-v">album</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">album</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-v">getTracks</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">getTracks</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-o">/</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
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
  <span class="crayon-s">“col-md-3”</span>
  <span class="crayon-v">style</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">AlbumList</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">styles</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">div</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">ul </span>
  <span class="crayon-v">style</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">AlbumList</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">styles</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">ul</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">albums</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">ul</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">div</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-v">AlbumList</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">propTypes</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">albums</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">React</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">PropTypes</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-t">array</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">isRequired</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">getTracks</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">React</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">PropTypes</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">func</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">isRequired</span>
  <span class="crayon-sy">,</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**[Lines 5-6]** All that's happening here is that we're taking the _getTracks_ prop passed down from _App_, and passing it further down to _Album_.

**[Line 19]** We declare that the _AlbumList_ component expects a _getTracks_ function prop. Even though we're just passing it down, we still need to declare it since we reference it.

Lastly, we'll modify _Album.jsx_ to invoke the _getTracks_ callback in response to a click:































 | <div class="crayon-pre">
  <p>
</p>
  <p>
</p>
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">React </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘react’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
</p>
  <p>
</p>
  <p><span class="crayon-m">const</span><span class="crayon-v">Album</span><span class="crayon-o">=</span><span class="crayon-sy">(</span><span class="crayon-v">props</span><span class="crayon-sy">)</span><span class="crayon-o">=</span><span class="crayon-o">&gt;</span>&lt;span cl</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
ass="crayon-sy">{





<span class="crayon-st">return</span>

<span class="crayon-sy">(</span>





<span class="crayon-o">&lt;</span>

<span class="crayon-v">li</span>

<span class="crayon-o">&gt;</span>





<span class="crayon-o">&lt;</span>

<span class="crayon-e">img</span>





<span class="crayon-v">src</span>

<span class="crayon-o">=</span>

<span class="crayon-sy">{</span>

<span class="crayon-v">props</span>

<span class="crayon-sy">.</span>

<span class="crayon-v">album</span>

<span class="crayon-sy">.</span>

<span class="crayon-v">images</span>

<span class="crayon-sy">[</span>

<span class="crayon-cn">1</span>

<span class="crayon-sy">]</span>

<span class="crayon-sy">.</span>

<span class="crayon-v">url</span>

<span class="crayon-sy">}</span>





<span class="crayon-v">alt</span>

<span class="crayon-o">=</span>

<span class="crayon-sy">{</span>

<span class="crayon-v">props</span>

<span class="crayon-sy">.</span>

<span class="crayon-v">album</span>

<span class="crayon-sy">.</span>

<span class="crayon-v">name</span>

<span class="crayon-sy">}</span>





<span class="crayon-v">style</span>

<span class="crayon-o">=</span>

<span class="crayon-sy">{</span>

<span class="crayon-v">Album</span>

<span class="crayon-sy">.</span>

<span class="crayon-v">styles</span>

<span class="crayon-sy">.</span>

<span class="crayon-v">img</span>

<span class="crayon-sy">}</span>





<span class="crayon-r">onClick</span>

<span class="crayon-o">=</span>

<span class="crayon-sy">{</span>

<span class="crayon-sy">(</span>

<span class="crayon-sy">)</span>

<span class="crayon-o">=</span>

<span class="crayon-o">&gt;</span>

<span class="crayon-v">props</span>

<span class="crayon-sy">.</span>

<span class="crayon-e">getTracks</span>

<span class="crayon-sy">(</span>

<span class="crayon-v">props</span>

<span class="crayon-sy">.</span>

<span class="crayon-v">album</span>

<span class="crayon-sy">.</span>

<span class="crayon-v">id</span>

<span class="crayon-sy">)</span>

<span class="crayon-sy">}</span>





<span class="crayon-o">&lt;</span>

<span class="crayon-o">/</span>

<span class="crayon-v">li</span>

<span class="crayon-o">&gt;</span>





<span class="crayon-v">Album</span>

<span class="crayon-sy">.</span>

<span class="crayon-v">propTypes</span>

<span class="crayon-o">=</span>

<span class="crayon-sy">{</span>





<span class="crayon-v">album</span>

<span class="crayon-o">:</span>

<span class="crayon-v">React</span>

<span class="crayon-sy">.</span>

<span class="crayon-v">PropTypes</span>

<span class="crayon-sy">.</span>

<span class="crayon-t">object</span>

<span class="crayon-sy">.</span>

<span class="crayon-v">isRequired</span>

<span class="crayon-sy">,</span>





<span class="crayon-v">getTracks</span>

<span class="crayon-o">:</span>

<span class="crayon-v">React</span>

<span class="crayon-sy">.</span>

<span class="crayon-v">PropTypes</span>

<span class="crayon-sy">.</span>

<span class="crayon-v">func</span>

<span class="crayon-sy">.</span>

<span class="crayon-v">isRequired</span>

<span class="crayon-sy">,</span>





<span class="crayon-v">Album</span>

<span class="crayon-sy">.</span>

<span class="crayon-v">styles</span>

<span class="crayon-o">=</span>

<span class="crayon-sy">{</span>





<span class="crayon-v">img</span>

<span class="crayon-o">:</span>

<span class="crayon-sy">{</span>





<span class="crayon-v">marginBottom</span>

<span class="crayon-o">:</span>

<span class="crayon-s">‘1em’</span>

<span class="crayon-sy">,</span>





<span class="crayon-e">export </span>

<span class="crayon-st">default</span>

<span class="crayon-v">Album</span>

<span class="crayon-sy">;</span>



**[Line 10]** We assign an inline function to the _img_ component's _onClick_ prop. Now when a user clicks an album image, _getTracks_ will be called.

**[Line 18]** Standard _propType_ declaration.

Alright, let's give it a try. If you click on an album cover now, you should see a list of tracks render on the right (make sure your browser window is wide enough for the albums and tracks to be side-by-side):<br>

[<embed src="http://patternhatch.com/wp-content/uploads/2016/08/track_list-282x300.png%20282w,%20http://patternhatch.com/wp-content/uploads/2016/08/track_list-768x816.png%20768w,%20http://patternhatch.com/wp-content/uploads/2016/08/track_list-500x531.png%20500w,%20http://patternhatch.com/wp-content/uploads/2016/08/track_list-400x425.png%20400w,%20http://patternhatch.com/wp-content/uploads/2016/08/track_list.png%20770w" class="alignnone size-full wp-image-711" width="770">](http://patternhatch.com/wp-content/uploads/2016/08/track_list.png)

Now that we can retrieve tracks, the final step in this post is to play a preview of a track when the user clicks on it.

**Track Preview**<br>
The same callback pattern we've been using so far applies to playing track previews.

We're going to pass a _playPreview_ callback down to the _TrackList_ component. _TrackList_ in turn will pass the _playPreview_ callback down to the _Track_ component. In the _Track_ component, we'll capture user click events and invoke the _playPreview_ callback accordingly.

Let's start by modifying _index.js_:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">React </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘react’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">ReactDOM </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘react-dom’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">SearchBar </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘./components/SearchBar’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">AlbumList </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘./components/AlbumList’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">TrackList </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘./components/TrackList’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">import</span>
  <span class="crayon-o">*</span>
  <span class="crayon-st">as</span>
  <span class="crayon-e">musicApi </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘./api/musicApi’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-t">class</span>
  <span class="crayon-e">App </span>
  <span class="crayon-r">extends</span>
  <span class="crayon-v">React</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">Component</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-r">constructor</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-r">super</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">state</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">albums</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">tracks</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">currentPreview</span>
  <span class="crayon-o">:</span>
  <span class="crayon-t">null</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">getAlbums</span>
  <span class="crayon-o">=</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">getAlbums</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">bind</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">processAlbums</span>
  <span class="crayon-o">=</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">processAlbums</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">bind</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">getTracks</span>
  <span class="crayon-o">=</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">getTracks</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">bind</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">processTracks</span>
  <span class="crayon-o">=</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">processTracks</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">bind</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">playPreview</span>
  <span class="crayon-o">=</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">playPreview</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">bind</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">getAlbums</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">artist</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">musicApi</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">getAlbums</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">artist</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">processAlbums</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">getTracks</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">albumId</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">musicApi</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">getTracks</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">albumId</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">processTracks</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">processAlbums</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">payload</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">setState</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">albums</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">payload</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">albums</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">items</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">tracks</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">processTracks</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">payload</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">setState</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">tracks</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">payload</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">tracks</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">items</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">playPreview</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">previewUrl</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">if</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">state</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">currentPreview</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">curAudioObject</span>
  <span class="crayon-o">=</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">state</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">currentPreview</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">curAudioObject</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">pause</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">newAudioObject</span>
  <span class="crayon-o">=</span>
  <span class="crayon-r">new</span>
  <span class="crayon-e">Audio</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">previewUrl</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">setState</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">currentPreview</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">newAudioObject</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">newAudioObject</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">play</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">render</span>
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
  <span class="crayon-v">div</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">SearchBar </span>
  <span class="crayon-v">getAlbums</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">getAlbums</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-o">/</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">AlbumList </span>
  <span class="crayon-v">albums</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">state</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">albums</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-v">getTracks</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">getTracks</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-o">/</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">TrackList </span>
  <span class="crayon-v">tracks</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">state</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">tracks</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-v">playPreview</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">playPreview</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-o">/</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">div</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-v">ReactDOM</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">render</span>
  <span class="crayon-sy">(</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-v">App</span>
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
 | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**[Line 14]** The _currentPreview_ state will represent the current preview we're playing in the form of an _Audio_ object.

**[Line 20]** We explicitly bind the _playPreview_ callback we intend to pass to _TrackList._

**[Line 44-56]** In the _playPreview_ callback, we first check whether we have a preview currently playing. If so, we pause it. We then create a new _Audio_ object for the preview we currently want and assign it as the new _currentPreview_ state before playing it.

**[Line 63]** We pass the _playPreview_ callback down to _TrackList._

You've probably guessed what we need to do in _TrackList.jsx_:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">React </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘react’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">Track </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘./Track’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">TrackList</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">tracks</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">tracks</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">map</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">track</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">Track </span>
  <span class="crayon-v">track</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">track</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-v">playPreview</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">playPreview</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-o">/</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
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
  <span class="crayon-s">“col-md-3”</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">ul </span>
  <span class="crayon-v">style</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">listStyle</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘none’</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">tracks</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">ul</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">div</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-v">TrackList</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">propTypes</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">tracks</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">React</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">PropTypes</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-t">array</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">isRequired</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">playPreview</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">React</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">PropTypes</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">func</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">isRequired</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-e">export </span>
  <span class="crayon-st">default</span>
  <span class="crayon-v">TrackList</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**[Lines 5-6]** We pass the _playPreview_ callback down to _Track_.

**[Line 19]** We set our _propTypes_ expectations accordingly.

Finally, in _Track.jsx:_

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">React </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘react’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-e">mouseOverColor</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">‘#ADD8E6’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-e">mouseOutColor</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">‘white’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">Track</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">return</span>
  <span class="crayon-sy">(</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">li</span>
</p>
  <p>
  <span class="crayon-v">style</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">Track</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">styles</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">li</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-v">onMouseOver</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">e</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">e</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">target</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">style</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">backgroundColor</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">mouseOverColor</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-v">onMouseOut</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">e</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">e</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">target</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">style</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">backgroundColor</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">mouseOutColor</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-r">onClick</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">playPreview</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">track</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">preview_url</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">props</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">track</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">name</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">li</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-v">Track</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">propTypes</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">track</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">React</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">PropTypes</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-t">object</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">isRequired</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">playPreview</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">React</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">PropTypes</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">func</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">isRequired</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">Track</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">styles</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">li</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">fontSize</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘1.5em’</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">padding</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘0.2em’</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">listStyleType</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘none’</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">backgroundColor</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">mouseOutColor</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-e">export </span>
  <span class="crayon-st">default</span>
  <span class="crayon-v">Track</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**[Line 12]** We pass an inline function to the _li_ element's _onClick_ prop. When a list item gets clicked, the _playPreview_ callback will be invoked.

**[Line 21]** Declare _getTracks_ in the _propTypes_.

Alright, time to take our app for a spin. Search for an artist's albums, retrieve the tracks by clicking an album cover, and then click on a track in the list. You should hear a 30-second preview come through.

**Onward**<br>
In this post, we learned the finer points of creating React components and composing them to create a front-end app. If you followed along, then congratulations on getting a React app up and running.

In the next installment, we'll explore how to improve our state management with the _[Redux](http://redux.js.org/)_ library. In the meantime, if you have any comments, suggestions, or corrections, please feel free to post them in the comments section or shoot me an [email](http://patternhatch.com/about/).
