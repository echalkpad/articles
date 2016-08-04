# The Hitchhiker's Guide to Modern JavaScript Tooling

[Original URL](http://reactkungfu.com/2015/07/the-hitchhikers-guide-to-modern-javascript-tooling/)

> A lot of developers coming to JavaScript world attracted by React.js are confused with the tooling used to produce modern JavaScript code. Webpack, Babel, ESLint, Mocha, Karma, Grunt... what...

![](http://reactkungfu.com/assets/images/the-hitchhikers-guide-to-modern-javascript-tooling/header.jpg)

A lot of developers coming to JavaScript world attracted by React.js are confused with the tooling used to produce modern JavaScript code. Webpack, Babel, ESLint, Mocha, Karma, Grunt... what should I use and which tool is doing what? JavaScript newcomers are often people coming from communities like Ruby and Java where opinionated, full-stack solutions exist. Frameworks like Ruby on Rails provide a lot of features out of the box - in fact the problem you may have with JavaScript is caused by that. You do not think about the building tools for your code - your template language processors, asset pipeline, cache middleware and a lot other things are pre-configured for you and they work transparently.

JavaScript tooling often consists of small tools, utilities and libraries that combined builds your code to be used in a browser. They allow you to rebuild your project after changes, run your test suite, hot reload your code and so on. You may be lost in this world - I was lost when I first tried to build my JavaScript stack for working with ES2015 code.

In fact this is a problem that you wish to have - system composed of small parts is much more maintainable and flexible than a big monolith that popular framework provides to you. But starting with such granular tooling can be hard.

I'd like to give you a quick overview about what popular tools do - and whether you need them or not.

## Unique needs of your modern JavaScript codebase

Working with modern JavaScript codebase is a little different than working with, for example, Ruby codebase. It is because environments (or _build targets_) can vary in a significant way. It is also because your primary environments like browsers cannot take your code and just run it. Since ES2015 is not implemented fully in popular browsers, your code needs to be prepared first. This process is called _transpilation_ and there are tools called _transpilers_ which takes care of it for you.

There is also a problem with _modularization_ of your code. JavaScript does not come with built-in solutions to provide some kind of _modules_ and an API to do it. These things are environment-specific - Node.js comes with its own module system (based on the CommonJS specification), browsers implements certain _ideas_ for modules - like AMD, CommonJS, System, UMD and so on. They specify _how_ you require and define modules in JavaScript. There are tools called _module bundlers_ that take your code written with the specific module system and creates a static JavaScript file from it, ready for the browser.

Those two things are quite unique while working with JavaScript - most languages come with their own set of modularization techniques included. Most languages are also directly interpreted/compiled to run your programs - and it is not the case with the modern JavaScript which must be transpiled before.

Having this in mind, let's jump to the list of popular tools and what they do.

### [Babel.js](http://babeljs.io)

Babel.js is a _transpiler_ - it takes your code written in a ECMAScript 2015 standard and produces the code in the older standard that browsers can run. It also allows you to enable experimental ECMAScript 2016 (a.k.a. ECMAScript 7 or ES7) features and has a built-in JSX transpiler. It can take JSX syntax that React uses and produce the JavaScript code out of it.

There are other transpilers for the newest ECMAScript standard available - like [Traceur](https://github.com/google/traceur-compiler), [es6-transpiler](https://github.com/termi/es6-transpiler) and [much more](https://github.com/addyosmani/es6-tools). Babel.js is so far the most adopted solution to the problem of _transpiling_ your code.

JavaScript also comes with a variety of dialects - and there are transpilers for them. Languages like [PureScript](http://www.purescript.org), [CoffeeScript](http://coffeescript.org), [TypeScript](http://www.typescriptlang.org) and much more comes with their own transpilers. You can use them if you don't want to use the ECMAScript 2015 to write your code and still run it in a browser on the client side.

### [Webpack](http://webpack.github.io)

Webpack is a _module bundler_. It takes your development codebase which is modularized and produces an output which combines your modules into files that can be understood by browsers.

Webpack comes with a _development server_ which allows you to host your code in development environment. Such server is also watching your files and rebundles your code every time you make a change.

_Module bundlers_ also maintain a graph of dependencies between files. This characteristic is a reason why other compilation steps like _transpilation_ are usually integrated with such tool. Webpack does such integrations using _loaders_. You can transpile your code, uglify it, remove dead code from your codebase and so on.

Webpack is also capable for bundling assets other than JavaScript - you can include and transpile stylesheets, optimize static assets and so on. All such integrations are possible thanks to _loaders_ feature that Webpack provides.

Webpack has many alternatives but for now is a way to go - it's the most popular tool to perform module bundling in React communities. Other tools which do the same are [Browserify](http://browserify.org), [jspm](http://jspm.io), [SystemJS](https://github.com/systemjs/systemjs) and so on.

### [Gulp](http://gulpjs.com)

Gulp is a _build system_ or a _task runner_. It works on a higher level than module bundlers. While module bundlers operate on modules and dependency trees, Gulp works on your file structure - and can perform tasks on them. It is a very similar utility to `make` in UNIX environments. It is basically a way to run other tools in a sequence.

In Gulp you define _tasks_ that you can run manually. You define on what files those tasks need to be run and what needs to be done about them.

Gulp operates on _streams_ abstraction. The build process is a set of streams - gulp provides a stream of files and _pipes_ this stream with another stream - like `"uglify"` stream which takes file stream as an input and produces uglified files as an output. Then you can pipe it to another stream which does the same on the uglified files. And so on.

So basically this tool is all about _orchestrating_ processes to work on a set of files. Such tools can transpile your code, lint it, optimize your assets, uglify it, compile documentation from comments, produce source maps, run tests, deploy your code... they are very generic - and they work on the _files_. Gulp itself doesn't know nothing about _modules_ or what your files mean - but it can ask other tools to perform tasks on such files.

Gulp also comes with basic watchers - you can watch a set of files and perform tasks on them when they change.

Gulp is one of the newer build systems available on the market. There are more seasoned tools like [Grunt](http://gruntjs.com).

Build systems can do a lot of things that _module bundlers_ can do. But they will propably do it in a less optimised way since all they do is working on files - they do not know abstractions that _module bundlers_ know. Also you can run your _module bundler_ inside your _build system_. So it's a higher level tool than module bundlers. Take it into consideration while deciding what should be used in your environment.

### [npm](https://www.npmjs.com)

NPM is a _package manager_. It does the same job as your system package managers do, but for JavaScript. It is a tool for downloading all pieces of your environment. NPM takes care of downloading packages, resolving dependencies for them and providing a package abstraction around your project. So when another developer wants to work with your codebase all he need to do is to issue the `npm install` command and all dependencies will install automatically. In such package you can also include license info, name, keywords, version, description and many other metadata about your code. If you are developing a library, `npm` also helps you to publish it later and make it available for all developers that work within the Node.js environment.

So if you want to install another cool library to use, or another loader for Webpack, or the webpack itself - `npm` is a tool that handles it for you.

There are no alternatives for `npm` I know for so far.

### [Bower](http://bower.io)

Bower is a _package manager_ for static assets. While `npm` allows you to install packages which can be used to _build_ your code, Bower can help you if you want a stand-alone library without building it or requiring it. All it does it downloads a handful of JS files, stylesheets and images - you can import them to your browser straight away and work with them.

When you build your environment Bower can provide you the drop-in static files that you can use. It shines when you _don't_ want to build your code, but work on the static assets. It's great for simple sites and prototypes - fetch a library and use it instantly on the browser with all assets needed.

If you are using Rails-based build environment, [Rails-Assets](https://rails-assets.org) is a proxy between your RubyGems and Bower - so you can use Bower packages in your codebase by including it in your `Gemfile`.

[![](http://reactkungfu.com/assets/images/rbe-cover.png)](https://arkency.dpdcart.com/cart/add?product_id=113689&method_id=120078) Struggling with creating dynamic user interfaces? Want to ease the pain of building interfaces? Learn React.js and modern JavaScript by examples you love and know!

- 210 pages
- 12 widgets like password strength meter or slack channel switcher - implementation & theory. Impress your friends and boss by creating cool widgets in a matter of **hours**!
- All snippets in ECMAScript 2015 - learn the most modern JavaScript flavor and boost your productivity.
- 2 full applications presented and implemented in a step-by-step basis - learn how to structure modern JavaScript frontends
- tips & tricks of React.js experts with over one year experience in this technology

Code repositories for all examples included. Use **LEARNREACT** code to get 30% off!

[Grab your copy!](https://arkency.dpdcart.com/cart/add?product_id=113689&method_id=120078)

### [Mocha](http://mochajs.org)

Mocha is a _test framework_. All it does is running your tests. It also provides an API for setting test cases - so splitting your tests to pieces.

It is capable of running tests in the browser or in the Node.js environment. It can also run tests for your browser code without running the browser at all - using a headless browser like [PhantomJS](http://phantomjs.org).

You can use whatever _test library_ you like. Mocha does not assume what kind of assertions you do in your tests.

You can configure Mocha to perform tasks before you test your code - like run Webpack first and then test and so on.

One of the most notable alternative to Mocha is [Karma](http://karma-runner.github.io/0.13/index.html). Both do quite similar tasks and are rather loved by community.

### [Jasmine](http://jasmine.github.io/2.3/introduction.html)

Jasmine is a library for testing. It provides an API for assertions or expectations about your code. It works on a lower layer than Mocha, which runs your tests - Jasmine defines how tests are created. It is the most popular library for testing JavaScript code.

There are alternatives like [Chai](http://chaijs.com). There are also more specialized libraries that can help with your tests like [Sinon.js](http://sinonjs.org) for stubbing.

### [ESLint](http://eslint.org)

ESLint is a linter for ECMAScript 2015 code. While transpilers can tell you that something is syntactically wrong with your code, linters tell you that your code is not following best practices. It is a great help when you want to maintain high code quality. It can also be annoying because it assumes a certain style guide for your code - but it is highly configurable so you can change behaviour or even disable some checks. You can integrate ESLint with your editor - like Sublime Text (using SublimeLinter) or vim (using syntastic), so your code will be checked frequently against the principles of the good ES2015 code.

There are plugins for ESLint that can also check best JSX practices or React practices in general - like [eslint-plugin-react](https://www.npmjs.com/package/eslint-plugin-react).

It is a great tool for maintaining a good quality of code. It can be also used to establish some kind of guideline in the project if you are working in team - so code produced by many people can be checked against best practices. Not to mention it can be integrated to your continous deployment server so bad quality code will be stopped from entering the production environment. Neat!

There is a little outdated alternative called [JSLint](http://www.jslint.com) which does exact the same thing.

While useful, ESLint is highly optional and you do not need to use it if you don't want to.

### [Yeoman](http://yeoman.io)

As has been said before, stitching those all tools together can be a hard task for beginners. Yeoman is all about simplifying this process. It is a _scaffolding tool. You find a list of features you want to have and find a Yeoman generator that provides those features. After issuing one command you have a pre-configured environment with features you need.

It is also a great tool if you established a project structure and want to reuse it to make other projects. You just need to create a Yeoman generator out of it and then you can bootstrap a project with similar config in a matter of seconds.

Whew, that is a lot of tools! This comes with strengths and weaknesses. The biggest weakness of such _small tools_ approach is that it is hard to learn what to use and how to configure it. The strength is that if you don't like a tool, you can easily replace it - there are alternatives for most tools I've described here.

If you think there is a tool that is very popular and worth considering to be added to such list, [let us know](http://twitter.com/reactkungfu). I hope it would be a great start to understand JavaScript build systems and tools used in general.

Good luck with your adventure with modern JavaScript!

[![](http://reactkungfu.com/assets/images/rbe-cover.png)](https://arkency.dpdcart.com/cart/add?product_id=113689&method_id=120078) Struggling with creating dynamic user interfaces? Want to ease the pain of building interfaces? Learn React.js and modern JavaScript by examples you love and know!

- 210 pages
- 12 widgets like password strength meter or slack channel switcher - implementation & theory. Impress your friends and boss by creating cool widgets in a matter of **hours**!
- All snippets in ECMAScript 2015 - learn the most modern JavaScript flavor and boost your productivity.
- 2 full applications presented and implemented in a step-by-step basis - learn how to structure modern JavaScript frontends
- tips & tricks of React.js experts with over one year experience in this technology

Code repositories for all examples included. Use **LEARNREACT** code to get 30% off!

[Grab your copy!](https://arkency.dpdcart.com/cart/add?product_id=113689&method_id=120078)

[comments powered by](http://disqus.com)
