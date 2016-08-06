# Modular JavaScript: A Beginners Guide to SystemJS & jspm

[Original URL](https://www.sitepoint.com/modular-javascript-systemjs-jspm/)

> This article was peer reviewed by Adrian Sandu and Mark Brown. Thanks to all of SitePoint's peer reviewers for making SitePoint content the best it can be! Over the past few years, the...

_This article was peer reviewed by [Adrian Sandu](http://www.sitepoint.com/author/asandu) and [Mark Brown](http://www.sitepoint.com/author/mbrown/). Thanks to all of SitePoint's peer reviewers for making SitePoint content the best it can be!_

Over the past few years, the JavaScript programming language has exploded in popularity. It has become the go-to language for developing both rich web applications, as well as hybrid mobile applications. And as JavaScript projects are becoming increasingly complex, developers are experiencing new requirements of the language. One of these is modularity.

As far as I can see, there are two aspects in which modularity has to be achieved:

- Modules that we author
- External modules that are installed as dependencies

ES6 brings a [standard module syntax](http://www.2ality.com/2014/09/es6-modules-final.html) to JavaScript and a [loader specification](https://github.com/whatwg/loader/). This is a good step forward, however at the time of writing, there are [no browsers that can natively load ES6 modules](https://www.nczonline.net/blog/2016/04/es6-module-loading-more-complicated-than-you-think/). This means that if you want to use modules today, you'll need to use a module bundler.

_For an overview of the current landscape see: [Understanding JavaScript Modules: Bundling & Transpiling](http://www.sitepoint.com/javascript-modules-bundling-transpiling/)_

And what's more, we don't have a package manager that allows us to download a package and include it in our application. Package managers (such as Bower and npm) help us download front-end dependencies without having to visit a projects' website, but that's as far as it goes.

In this article, we will see how [jspm](http://jspm.io/) and [SystemJS](https://github.com/systemjs/systemjs) can be used to overcome these problems.

## What are jspm and SystemJS?

The JavaScript Package Manager (aka jspm) is a package manager that works on top of the SystemJS universal module loader. It is not an entirely new package manager with its own set of rules, rather it works on top of existing package sources. Out of the box, it works with GitHub and npm. As most of the Bower based packages are based on GitHub, we can install the those packages using jspm as well. It has a [registry](http://kasperlewau.github.io/registry/#/) that lists most of the commonly used front-end packages for easier installation. Like npm, it can be used to differentiate the packages as development and production packages during installation.

SystemJS is a module loader that can import modules at run time in any of the popular formats used today (CommonJS, UMD, AMD, ES6). It is built on top of the [ES6 module loader polyfill](https://github.com/ModuleLoader/es6-module-loader) and is smart enough to detect the format being used and handle it appropriately. SystemJS can also transpile ES6 code (with [Babel](http://www.babeljs.io) or [Traceur](https://github.com/google/traceur-compiler)) or other languages such as [TypeScript](http://www.typescriptlang.org) and [CoffeeScript](http://coffeescript.org) using plugins. You configure these things within `System.config({ ... })` before importing your module.

jspm uses SystemJS to manage packages and their dependencies, so we need not worry about putting the packages in the right order to load them.

Now that we know what jspm and SystemJS are, let's see how to use them.

## Setting up Our Environment

If you haven't already, you'll need to have [Node.js](https://nodejs.org/en/) installed. A particularly easy way to do this is to use a version manager (such as nvm) and is detailed in [this quick tip](http://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/). Once you're up and running with Node, you can install jspm globally by running the following from the command line:

```
npm install -g jspm
```

Now we can use the [jspm command line interface](https://github.com/jspm/jspm-cli). Let's set up a project:

```
mkdir new-project && cd new-project
npm init -y
npm install jspm --save-dev
```

This creates a directory named `new-project`, initializes an npm project and installs jspm locally. This is the [recommended way of doing things](http://jspm.io/docs/getting-started.html), as it locks down the jspm version for a project and ensures upgrades to the global jspm will not alter the behavior of your application.

Another advantage of this approach is that if your project is deployed through a continuous integration build, you can configure the build process to use the local jspm package instead of having to install jspm globally on the server as well.

You can use `jspm -v` to confirm the local version.

```
$ jspm -v
0.16.34
Running against local jspm install.
```

To use jspm in a project, run the following command:

```
jspm init
```

You will be prompted for a number of settings, hit enter to accept the default values or type a different value to change them. The following screenshot shows an instance when the command is executed with default settings:

![Running the jspm init command in the terminal](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/05/1462369073jspm_init_terminal.png)

## Directory Structure and Configuration

This creates a file named `config.js` in the root of the project, as well as a folder named `jspm_packages`. If you take a peek inside the `jspm_packages` folder you'll see:

- A `github` sub-directory
- An `npm` sub-directory
- The main files for the SystemJS module loader

The reason that jspm is creating `github` and `npm` directories is that it piggybacks on top of these registries, simply aliasing the npm or GitHub package requested. Additionally, the `github` directory contains a SystemJS plugin to load JSON files from GitHub and the `npm` directory contains the Babel-related packages we will need to transpile our ES6 code

The `config.js` file is mainly for SystemJS. If you open it, you will see that it has configuration options set for the paths of the packages from different sources, options for Babel and name maps for the packages to make it easy to refer them. This file gets updated automatically updated when a new package is installed using jspm.

The command also updates the package.json file and adds a section for jspm. The Babel packages installed with the `init` command are added to `devDependencies` section of the project.

```
"jspm": {
 "devDependencies": {
 "babel": "npm:babel-core@^5.8.24",
 "babel-runtime": "npm:babel-runtime@^5.8.24",
 "core-js": "npm:core-js@^1.1.4"
 }
}
```

Any new package installed using the jspm command with or without the `--save` option will get added to the`dependencies` section and so it becomes a production dependency. Installing with the `--save-dev` option makes the dependency a development time dependency and saves it to the `devDependencies` section.

Finally, jspm adds an entry to the `map` section of the `config.js` file for every package installed using the `jspm` command. The mapped name can be used to load the library in any JavaScript file throughout your project. Any package dependencies are added to the map section as well. Following is a fragment from map section of the `config.js` file that shows how packages of jquery and jquery-ui are mapped and how the dependency is defined:

```
"jquery": "npm:jquery@2.2.0",
"jquery-ui": "github:components/jqueryui@1.11.4",
"github:components/jqueryui@1.11.4": {
 "jquery": "npm:jquery@2.2.0"
}
```

As a point of interest, you can also generate these mappings automatically when you install a module:

```
jspm install jq=jquery
```

This would allow you to write the following elsewhere in your app:

```
var $ = require('jq');
```

## jspm and SystemJS in Action

To accompany this article, I have made a simple Wikipedia search sample. You can find the [GitHub repo here](https://github.com/sitepoint-editors/JSPM-SystemJS-Demos). It is setup using the default options of the `jspm init` command and consequently it uses Babel as ES6 transpiler. It also uses the libraries jQuery and Bootstrap which are installed using jspm. As bootstrap needs jQuery, jspm creates a mapping in the `config.js` file to load jQuery before loading bootstrap:

```
"github:twbs/bootstrap@3.3.6": {
 "jquery": "github:components/jquery@2.2.0"
}
```

As the name of the repo suggests, it queries the Wikipedia search API and displays the data received in the view. It uses jQuery's `$.ajax()` to make a call to the API, displays the results on a page and has a link to show a snippet of the article in a modal popup. The sample has three JavaScript files under the `scripts` folder:

- `search.js`: This file uses an AMD style module. It as loads jQuery as a dependency and makes an Ajax call to the Wikipedia search API. The module returns a function that any other consuming module can call with a parameter.
- `summaryModal.js`: This file uses an ES6 style module. It loads Bootstrap's JavaScript library. It then exports a function that accepts a piece of escaped HTML and unescapes the HTML text before displaying it in a modal popup.
- `display.js`: This file uses a CommonJS style module. It loads jQuery and both of the above files as dependencies. It calls the method exposed by `search.js` to get results and then renders this data in an HTML table on the UI. Additionally, it makes use of the method exposed by `summaryModal.js` to show a modal on click of a link button on the table.

As already mentioned, SystemJS understands all module systems in JavaScript. We can load all three of the above files using SystemJS.

To make these scripts run, we need to load `system.js` and `config.js` on the HTML page. After that we will load the `display.js` file using the SystemJS module loader. As this file refers to other files in the repo and these files load the libraries needed, we don't need to load any other file.

```
<script src="jspm_packages/system.js"></script>
<script src="config.js"></script>
<script>
 System.import("scripts/display.js");
</script>
```

SystemJS transpiles the ES6 file on the fly using Babel and runs it in the browser. Here's a (slightly simplified) demo of what this looks like:

However, transpiling on the fly is bad for performance and shouldn't be used in production applications. Don't forget that jspm is a module bundler, so let's bundle it.

## Bundling

We can create a bundle for the entire application using the following command:

```
jspm bundle scripts/display.js build.js
```

This command does the following:

- Combines all files and the libraries in a single file named `build.js`
- Converts the module systems used by the files to SystemJS style modules
- Creates a source map file that is loaded during debugging

Now we need to load the `build.js` file into `index.html` file. Here is the updated set of references:

```
<script src="jspm_packages/system.js"></script>
<script src="config.js"></script>
<script src="build.js"></script>
<script>
 System.import("scripts/display.js");
</script>
```

Please note that we didn't remove the import statement to load the module `display.js`. This statement doesn't load a file anymore, instead it loads a SystemJS module that is already available in the `build.js` file.

You can follow instructions in the project's README file to run it. You can browse the code in the individual files in the developer tools and debug through them.

## Conclusion

The combination of jspm and SystemJS provides a unified way of installing and loading dependencies. This tool not only makes the job of managing dependencies easier but also opens up the door to use the future module system on today's browsers. As I have demonstrated, jspm is easy to setup and use, two reasons which are contributing to its rising popularity.

Are you using jspm in your projects? Why? Why not? Let me know in the comments below.
