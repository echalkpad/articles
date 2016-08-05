# Gulp as a Development Web Server - Tuts+ Code Tutorial

[Original URL](https://code.tutsplus.com/tutorials/gulp-as-a-development-web-server--cms-20903)

> The task runner Gulp.js is getting more and more popular lately. It can be used for many things, like concatenating JavaScript files or minifying images. If you are new to Gulp.js, I recommend you to...

The task runner [Gulp.js](http://gulpjs.com) is getting more and more popular lately. It can be used for many things, like concatenating JavaScript files or minifying images. If you are new to Gulp.js, I recommend you to start off with [Managing Your Build Tasks With Gulp.js](http://code.tutsplus.com/tutorials/managing-your-build-tasks-with-gulpjs--net-36910).

In this tutorial, you will learn how to use Gulp.js, in order to run a local web server with built-in, `livereload` support.

## The Old Way

Let's assume we want to develop a single page application. The entry point for this app is a simple `index.html` file. Our goal is to access this file through the browser, by going to `localhost`. In the past, perhaps you _setup an Apache or Nginx server_. Well, that's not needed anymore.

## A Better Way

Today, an implementation in JavaScript exists for almost everything, even for a web server. The most popular one is called [Connect](https://github.com/senchalabs/connect). We will be using it, by utilizing a Gulp.js plugin called [gulp-connect](https://github.com/AveVlad/gulp-connect).

In the following sections, we will setup a local web server for a single page application, so I'll assume you have already setup the basic files, like the `gulpfile.js` file.

## Initial Setup

We start off by installing the Connect plugin with the command below. Everything we need will be installed with this single command.

```
npm install --save-dev gulp-connect
```

_Tip_: `npm install --save-dev` can be abbreviated with `npm i -D`.

Now, let's define a task for the web server. Our `gulpfile.js` should then look like this:

```
var gulp = require('gulp'),
 connect = require('gulp-connect');

gulp.task('webserver', function() {
 connect.server();
});

gulp.task('default', ['webserver']);
```

We can now start the web server by simply running `gulp` in the terminal. We can open up `localhost:8080` in our browser, where we should see `index.html`. The web server mounts the file structure of the folder, where the `gulpfile.js` lives in, to the root of `localhost:8080`. The server will run until you stop the task by pressing **Ctrl + c** on your keyboard. Great!

You can find the source code for this and all following examples on [Github](https://github.com/schickling/gulp-webserver-article). Each example contains all necessary files to reproduce a working setup. You just need to run `npm install` in the respective directory.

### Adding `livereload` Support

Setting up the basic web server was easy, right? So let's continue to get `livereload` running. We need to do two things: first tell the web server to start with `livereload` support and secondly, we tell `livereload` when to refresh the page with new content.

The first step is simple. Just set the `livereload` property to `true`. The `webserver` task then looks like this:

```
gulp.task('webserver', function() {
 connect.server({
 livereload: true
 });
});
```

The second step depends on your use case. In this example, we will setup automatic compilation of LESS files to a CSS stylesheet and inject it into the browser. Let's break down this example into its parts. We need a "watcher", which checks if some LESS files got changed. This watcher should then trigger the LESS compiler, which outputs a CSS file. This CSS file should then be injected via `livereload`.

For this example, we will use the [gulp-less](https://github.com/plus3network/gulp-less) plugin. You can install it by running `npm install --save-dev gulp-less`. The watcher comes already shipped with Gulp.js. Our app file structure should roughly look like this:

```
.
├── node_modules
│   └── ...
├── styles
│   └── main.less
├── gulpfile.js
├── index.html
└── package.json
```

In the `watch` task, Gulp.js listens for changes in all `*.less` files in the `styles` folder and triggers the `less` task. Here, the `main.less` file is the entry point for LESS. After each compile step, the result will be automatically injected into the browser. The code for the `gulpfile.js` looks like the following, feel free to _copy and paste_ it, to your project.

```
var gulp = require('gulp'),
 connect = require('gulp-connect'),
 less = require('gulp-less');

gulp.task('webserver', function() {
 connect.server({
 livereload: true
 });
});

gulp.task('less', function() {
 gulp.src('styles/main.less')
 .pipe(less())
 .pipe(gulp.dest('styles'))
 .pipe(connect.reload());
});

gulp.task('watch', function() {
 gulp.watch('styles/*.less', ['less']);
})

gulp.task('default', ['less', 'webserver', 'watch']);
```

Let's rerun `gulp` in the terminal and open up again `localhost:8080` in the browser. We can now make some changes to a LESS file in the `styles` folder. It will be instantly compiled and refreshed in the browser. Note that _you don't need any browser extensions_. `livereload` works out of the box.

### A Few Adjustments

Please keep in mind that the previous `gulpfile.js` is just a minimal demonstration of how to use Gulp.js as a web server with `livereload`. I highly recommend playing around with some other plugins. You should also try to rearrange the task structure and use the non-builtin [gulp-watch](https://github.com/floatdrop/gulp-watch) plugin, which allows you to process just the changed files. This becomes important if you're working with a bigger code base. Later in this tutorial, we will see what an alternative task structure might look like.

#### Changing the Hostname & Port

The `gulp-connect` plugin itself has many configuration options. For example, you can modify the web server port or hostname. You can even use a custom hostname with port `80` (default is `localhost:8080`).

```
connect.server({
 port: 80,
 host: 'gulp.dev'
});
```

To get this working, we need to have `gulp.dev` in our hosts file and run the command `sudo gulp`. Administrator rights are needed in order to use port `80`.

### Some Advanced Features

You can go even further using the connect plugin for spawning multiple web servers at the same time. This becomes useful, for example, if you want to run a development web server and run integration tests, _simultaneously_.

`gulp-connect` also provides the possibility to use several directories as a root folder. For example, when you're using CoffeeScript and you want to save the compiled JavaScript files in a temporary folder, you can then mount this folder without polluting your source folder.

For more examples, please checkout the [GitHub repository](https://github.com/AveVlad/gulp-connect).

## Refactoring Our Code

In the previous example we setup a minimal `gulpfile.js` to compile LESS files to CSS and inject them immediately into the browser. It worked, but we can do better. It might be problematic that we mixed the _compile_ and _`livereload`_ steps into one task. So, let's split them up and watch for changes in the generated files. For that, we will be using the previously mentioned `gulp-watch` plugin.

Let's go even a step further and add a CoffeeScript compile step. With this additional step, the new structure should be more clear. We install the new plugins in one batch via:

```
npm install --save-dev gulp-watch gulp-coffee 
```

And `require` them in the top of our `gulpfile.js`. In the following setup, I assume you already have some `.coffee` files in a directory called `scripts`. For an example setup, please checkout [this repo](https://github.com/schickling/gulp-webserver-article/tree/master/03-livereload-refactored). The refactored `gulpfile.js` could look like the following. We'll go through the changes, step by step.

```
var gulp = require('gulp'),
 connect = require('gulp-connect'),
 watch = require('gulp-watch'),
 less = require('gulp-less'),
 coffee = require('gulp-coffee');

gulp.task('webserver', function() {
 connect.server({
 livereload: true,
 root: ['.', '.tmp']
 });
});

gulp.task('livereload', function() {
 gulp.src(['.tmp/styles/*.css', '.tmp/scripts/*.js'])
 .pipe(watch())
 .pipe(connect.reload());
});

gulp.task('less', function() {
 gulp.src('styles/main.less')
 .pipe(less())
 .pipe(gulp.dest('.tmp/styles'));
});

gulp.task('coffee', function() {
 gulp.src('scripts/*.coffee')
 .pipe(coffee())
 .pipe(gulp.dest('.tmp/scripts'));
});

gulp.task('watch', function() {
 gulp.watch('styles/*.less', ['less']);
 gulp.watch('scripts/*.coffee', ['coffee']);
})

gulp.task('default', ['less', 'coffee', 'webserver', 'livereload', 'watch']);
```

The biggest change we see is the additional `livereload` task. This task just watches (via the `gulp-watch` plugin) for changes in the compiled files and refreshes them in the browser. The custom `watch()` function allows us to just reload the changed files, whereas the built-in `gulp.watch()` command would reload all files and not just the changed ones.

Because of this single additional task, we don't need a `.pipe(connect.reload())` command after each compile step. So we separated the tasks by their concerns, which is always a good idea in software development.

We also noticed that the compiled files aren't saved in their respective source folder any more. They are now stored in a temporary folder named `.tmp`. The contents of this folder are just generated files and won't pollute the `styles` or `scripts` directories any longer. It's also recommended to exclude this folder from your revision control system. All we have to do to get this working, is to also mount `.tmp` as a root folder, which is done by

```
root: ['.', '.tmp']
```

This refactored setup should work just like the one before. But it's way cleaner and easier to extend.

## Conclusion

You've just learned how to use Gulp.js as a web server.

You can combine this technique with many other use cases, like testing or building your single page application. Note that this web server is just meant for local development. For production purposes, you should use a more performant solution like Nginx or a CDN.

Notice that all the presented features are also possible with Grunt or similar projects. Gulp.js just provides a _very simple and elegant_ way of doing things.
