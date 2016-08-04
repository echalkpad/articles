# PostCSS Quickstart Guide: Gulp Setup

[Original URL](http://webdesign.tutsplus.com/tutorials/postcss-quickstart-guide-gulp-setup--cms-24543)

> In the last tutorial we covered how you can get started with PostCSS instantly using CodePen or Prepros. These options are great, but restrictive in that you don't get to control which plugins...

In the [last tutorial](http://webdesign.tutsplus.com/tutorials/postcss-quickstart-guide-instant-setup-options--cms-24536) we covered how you can get started with PostCSS instantly using CodePen or Prepros. These options are great, but restrictive in that you don't get to control which plugins are available for you to use.

In this tutorial we'll go through how you can use PostCSS with the task runner [Gulp](http://gulpjs.com/), allowing you to decide for yourself which plugins you want to use and hence really tap into the plugin ecosystem.

**Note**: If you've never worked with command line or task runners before, I recommend that before you begin this tutorial you check out our free series: [The Command Line for Web Design](http://webdesign.tutsplus.com/series/the-command-line-for-web-design--cms-777).

## Prerequisites

Given we'll be working with Gulp, we'll assume you already have the prerequisites for its use installed:

If you're not sure you have these installed, please follow the tutorial [The Command Line for Web Design: Taming 3rd Party Packages](http://webdesign.tutsplus.com/tutorials/the-command-line-for-web-design-taming-3rd-party-packages--cms-23451) as it will take you through getting these prerequisites in place.

Please also ensure you understand the basics of Gulp use by following the tutorial [The Command Line for Web Design: Automation with Gulp](http://webdesign.tutsplus.com/tutorials/the-command-line-for-web-design-automation-with-gulp--cms-23642). Additionally, follow the instructions in the tutorial's "Setup Project for Gulp" section. Before you move on you should have a project folder with:

- A "gulpfile.js" (Gulpfile)
- A "package.json" file
- Gulp installed in the "node_modules" folder and set as a dev dependency for your project

## Basic Gulp PostCSS Setup

Inside your project folder create two subfolders, one named "src" and one named "dest". The "src" folder is going to hold your unprocessed CSS files, while the "dest" folder will have your PostCSS processed files written into it.

The next thing you'll need to do is install the [gulp-postcss](https://github.com/postcss/gulp-postcss) plugin into your project - we'll be using this to handle our PostCSS processing.

In a terminal/command prompt pointed at your project folder, run the command:

```
npm install --save-dev gulp-postcss
```

After the installation completes your project structure should look like this:

![](https://cms-assets.tutsplus.com/uploads/users/53/posts/24543/image/gulpproject.png)

Now open up your Gulpfile for editing and create variables to call in both the "gulp" and "gulp-postcss" modules by adding the following code:

```
var gulp = require('gulp');
var postcss = require('gulp-postcss');
```

We can now setup a task to read a source CSS file and process it through PostCSS.

Add the following:

```
gulp.task('css', function () {
 var processors = [
 ];
 return gulp.src('./src/*.css')
 .pipe(postcss(processors))
 .pipe(gulp.dest('./dest'));
});
```

Let's break down what we have in the code above.

In the first line we've setup a gulp task named `css`. This task executes a function, and inside that function we've created an array named `processors`. Right now that array is empty, but in a moment we'll fill that with the PostCSS plugins we want to use.

After the `processors` array we've specified the files we want to target for processing: any CSS files in the "src" folder.

In the first of the two lines using the `pipe()` function, we're setting PostCSS to execute via the function `postcss()`. As an argument through that function we're passing our `processors` array, which, later, will tell PostCSS which plugins we want to use.

Finally, with the second of the two `pipe()` functions, we're having our processed code written into a new CSS file in our "dest" folder.

### Run a Test Compile

Go ahead and create a new "style.css" file in your "src" folder and add some test CSS to it such as:

```
.test {
 background: black;
}
```

Now in your terminal/command prompt, still pointed at your project folder, run the command:

```
gulp css
```

This will run the task you just setup, and as a result you should now find a new "style.css" file inside your "dest" folder.

When you open up this new file, you'll see identical code in it to that of your source file. The code hasn't changed because we haven't used any PostCSS plugins yet, and as you'll know from a previous tutorial, it's the plugins which perform the actual CSS manipulations.

## Add PostCSS Plugins

We'll now add a selection of PostCSS plugins and packs: [Autoprefixer](https://github.com/postcss/autoprefixer) (adds vendor prefixes), [cssnext](http://cssnext.io/setup/) (enables future syntax) and [precss](https://github.com/jonathantneal/precss) (extends with Sass-like functionality).

Run the following commands to install each plugin into your project:

```
npm install autoprefixer --save-dev
npm install cssnext --save-dev
npm install precss --save-dev
```

**Note**: The `cssnext` and `precss` installations may take a little while as they are packs of multiple plugins.

Next we'll define variables to load in each one into our project. Add the following code under the two existing variables at the top of your Gulpfile:

```
var autoprefixer = require('autoprefixer');
var cssnext = require('cssnext');
var precss = require('precss');
```

Then we'll add these three plugins to the `processors` array in our gulp task. Update the array to the following:

```
 var processors = [
 autoprefixer,
 cssnext,
 precss
 ];
```

With the three plugins added to our processors array, PostCSS will know we wish to apply each one to our source CSS.

We're now ready to add some test code to our "src/style.css" file and check that everything is working. Delete what you already have in the file and add this CSS instead:

```
/* Testing autoprefixer */

.autoprefixer {
 display: flex;
}

/* Testing cssnext */

.cssnext {
 background: color(red alpha(-10%));
}

/* Testing precss */

.precss {
 @if 3 < 5 {
 background: green;
 }
 @else {
 background: blue;
 }
}
```

Run the `gulp css` command again now, and the resulting file in your "dest" folder should have the following content:

```
/* Testing autoprefixer */

.autoprefixer {
 display: -webkit-box;
 display: -webkit-flex;
 display: -ms-flexbox;
 display: flex;
}

/* Testing cssnext */

.cssnext {
 background: rgba(255, 0, 0, 0.9);
}

/* Testing precss */

.precss {
 background: green
}
```

As per the above, you should see vendor prefixes have been added to the first class by Autoprefixer, an `rgba()` color has been output by cssnext in the second class, and the `@if @else` check has been evaluated by PreCSS in the third class.

### Setting Plugin Options

**Note**: if you want to configure options for a plugin, add a pair of brackets after its name in the preprocessors array and pass the options there. For example, you might specify the browser list you want Autoprefixer to work off, like so:

```
 var processors = [
 autoprefixer({browsers: ['last 1 version']}),
 cssnext,
 precss
 ];
```

## Sharing Your Project

The beauty of PostCSS is in its ability to be configured with any combination of plugins. The challenge this brings forward, however, is ensuring that other people who wish to work on a project have the same setup of PostCSS plugins. Thanks to [npm](https://www.npmjs.org/), this challenge is handled through its system of dependency management.

Because you are using the `--save-dev` flag every time you install a plugin into your project, it will be added to your "project.json" file as a dev dependency. This means if you want to share your project with others, they can run the command `npm install` on the package you share with them and have all the same plugins automatically installed.

To learn more about how dependency management works with NPM check out the tutorial [The Command Line for Web Design: Taming 3rd Party Packages](http://webdesign.tutsplus.com/tutorials/the-command-line-for-web-design-taming-3rd-party-packages--cms-23451).

## Let's Recap

To summarize the above:

- Create an npm project with Gulp installed and a Gulpfile inside
- Install the gulp-postcss plugin
- Setup your Gulpfile to load the gulp and gulp-postcss plugins
- Create a gulp task to compile your CSS
- Within the task, setup a `processors` array
- Pipe your source CSS through the `postcss()` function, with the `processors` array passed as an argument

From there, you can follow the same essential steps to enable any PostCSS plugin in your project:

- Install the plugin into your project with<br>
  `npm install <plugin_name> --save-dev`
- Define a variable to load the plugin in your Gulpfile e.g.<br>
  `var autoprefixer = require('autoprefixer');`
- Add that variable name into your `preprocessors` array.

Check out [the Github repo](https://github.com/tutsplus/postcss-quickstart-guide-gulp-setup) for starter files and completed examples.

## Coming Up Next: Grunt + PostCSS

In [the next tutorial](http://webdesign.tutsplus.com/tutorials/postcss-quickstart-guide-grunt-setup--cms-24545) we'll cover how to setup a PostCSS project in the other of the big two task runners: Grunt. See you there!
