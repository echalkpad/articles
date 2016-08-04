# Creating a Custom Bootstrap Build With SCSS

[Original URL](https://www.codementor.io/development-process/tutorial/create-custom-bootstrap-build-with-scss)

> Have you ever tried to customize the font size for your Bootstrap-powered website? It's not something you can easily do without recompiling Bootstrap's CSS. If you change the font size...

Have you ever tried to customize the font size for your [Bootstrap](http://getbootstrap.com/)-powered website?

It's not something you can easily do without recompiling Bootstrap's CSS. If you change the font size on your page, you'll want that change to cascade through. A change in the base font size should recalculate header font sizes and margins/padding for text elements like paragraph tags.

Let's look at how to create a custom [Bootstrap SASS](https://github.com/twbs/bootstrap-sass) build without maintaining our own fork of Bootstrap.

## Using Bootstrap with Bower

We're going to use `bower` and `gulp` to compile Bootstrap, so we'll need [Node.js](http://nodejs.org/).

First let's make a `package.json` file for Node to see. Either use `npm init -y` or create a `package.json` file containing just an empty JSON object (`{}`).

Now let's install `gulp`, `gulp-bower`, and `gulp-sass`:<br>
`bash $ npm install --save-dev gulp gulp-bower gulp-sass`<br>
Our `package.json` file should now look something like this:

```
{
 "devDependencies": {
 "gulp": "^3.8.11",
 "gulp-bower": "0.0.10",
 "gulp-sass": "^1.3.3"
 }
}
```

Now let's use Bower to install bootstrap. This will allow us to import Bootstrap into our SCSS code and compile it down to CSS manually.

Create a `bower.json` file using `bower init` or by manually creating one:

```
{
 "name": "custom-bootstrap-example",
 "authors": [
 "Lillian Langston <lillian@example.com>"
 ],
 "private": true,
 "ignore": [
 "**/.*",
 "node_modules",
 "bower_components",
 "test",
 "tests"
 ],
 "dependencies": {
 }
}
```

Now let's install `bootstrap-sass` with Bower.

```
$ bower install --save bootstrap-sass
```

Our `bower.json` file should now have `bootstrap-sass` listed as a dependency:

```
"dependencies": {
 "bootstrap-sass": "~3.3.3"
}
```

Now we can make an SCSS file that includes bootstrap into our project. Let's call our SCSS file `css/app.scss`:

```
@import "bootstrap";
@import "bootstrap/theme";
```

Now let's use gulp to compile our `app.scss`, which includes Bootstrap SASS:

```
var gulp = require('gulp');
var sass = require('gulp-sass');

var config = {
 bootstrapDir: './bower_components/bootstrap-sass',
 publicDir: './public',
};

gulp.task('css', function() {
 return gulp.src('./css/app.scss')
 .pipe(sass({
 includePaths: [config.bootstrapDir + '/assets/stylesheets'],
 }))
 .pipe(gulp.dest(config.publicDir + '/css'));
});

gulp.task('fonts', function() {
 return gulp.src(config.bootstrapDir + '/assets/fonts/**/*')
 .pipe(gulp.dest(config.publicDir + '/fonts'));
});

gulp.task('default', ['css', 'fonts']);
```

Now when we run `gulp`, our compiled Bootstrap CSS should appear in the `public/css` directory:

```
$ gulp
$ ls public/css
app.css
```

## Customizing the Font Size

Now let's look at how we can go about customizing the font size in Bootstrap.

Notice that the value of the `$font-size-base` variable in the [`_variables.scss` file](https://github.com/twbs/bootstrap-sass/blob/master/assets/stylesheets/bootstrap/_variables.scss#L52). `variables.scss` is used for calculating a variety of other important variables. For example 8 of the lines below rely on `$font-size-base`:

```
$font-size-base: 14px !default;
$font-size-large: ceil(($font-size-base articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 1.25)) !default; // ~18px
$font-size-small: ceil(($font-size-base articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 0.85)) !default; // ~12px

$font-size-h1: floor(($font-size-base articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 2.6)) !default; // ~36px
$font-size-h2: floor(($font-size-base articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 2.15)) !default; // ~30px
$font-size-h3: ceil(($font-size-base articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 1.7)) !default; // ~24px
$font-size-h4: ceil(($font-size-base articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 1.25)) !default; // ~18px
$font-size-h5: $font-size-base !default;
$font-size-h6: ceil(($font-size-base articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 0.85)) !default; // ~12px
```

Notice those `!default` flags? That `!default` flag means the variables will be set _only_ if they don't have a value already.

All of the variables assigned in Bootstrap SASS's `_variables.scss` file have a `!default` flag. That means we can override those variables by assigning our own values before we import Bootstrap.

Let's copy Bootstrap's `_variables.scss` file and make our own custom version:

```
$ cp bower_components/bootstrap-sass/assets/stylesheets/bootstrap/_variables.scss css/_variables.scss
```

Now we need to reference our custom variables module from our `app.scss` file.

```
@import "variables";
@import "bootstrap";
@import "bootstrap/theme";
```

Remember to import our `variables` module _before_ we import Bootstrap! If we import it afterward, our changes customizations won't be applied.

Now let's change `$font-size-base` to `16px` in `css/_variables.scss`:

```
$font-size-base: 16px;
```

Now if we recompile our CSS, we should see our larger font size reflected throughout our application:

```
$ gulp
```

## Try it Out!

I made a sample project to demonstrate how easy it is to customize Bootstrap variables before building [Bootstrap SASS][].

[Check out the sample project on Github](https://github.com/treyhunner/custom-bootstrap-example)

This tutorial is also available [on my blog](http://treyhunner.com/2015/02/creating-a-custom-bootstrap-build/).
