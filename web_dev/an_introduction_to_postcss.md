# An Introduction to PostCSS

[Original URL](http://www.sitepoint.com/an-introduction-to-postcss/)

> Most developers who spend their time working with CSS are familiar with preprocessors such Less, Sass, and Stylus. These tools have become a vital part of the web development ecosystem. Writing...

Most developers who spend their time working with CSS are familiar with preprocessors such Less, Sass, and Stylus. These tools have become a vital part of the web development ecosystem. Writing styles for a website without using features like nesting, variables, or mixins now seems cumbersome and awkward. Although each of them is a great and extremely useful product, let's take a step back and consider if using preprocessors in such a way is indeed the best approach.

I see a couple of problems with tradition preprocessors:

- **They are not extendable.** Whichever preprocessor you choose, you are limited to the set of features that it provides. If you need anything on top of that, you'll need to add it as a separate step in your build process. If you feel like writing your own extension, you're on your own.
- **You can't leave anything out.** Some of the features preprocessors provide such as Sass's `@extend` [may be detrimental](http://www.sitepoint.com/avoid-sass-extend/) to you, and you might want to leave them out completely. Unfortunately, while you can avoid using them, you can't remove that part of the tool to minimize code.
- **They push out CSS standards.** You might say that each of the preprocessors has become a standard of its own. Regrettably, they don't aim at being compatible with the W3C standards, which means that they cannot be used as polyfills for early testing of the newer W3C standards.

This is where PostCSS comes in.

## What is PostCSS?

[PostCSS](https://github.com/postcss/postcss) is not a preprocessor per se; it doesn't transform CSS. As a matter of fact, it doesn't do much by itself at all. What it does is provide a CSS parser and a framework for creating plugins that can analyse, lint, handle assets, optimise, create fallbacks, and otherwise transform parsed CSS. PostCSS parses CSS into an abstract syntax tree (AST), passes it through a series of plugins, and then concatenates back into a string. If you're familiar with JavaScript tooling, then you can think of PostCSS as [Babel](http://babeljs.io/) for CSS.

There are currently more than 200 plugins for PostCSS, many of which are listed [on the PostCSS GitHub page](https://github.com/postcss/postcss), while others can be found on the useful PostCSS directory [postcss.parts](http://postcss.parts/). PostCSS can be integrated in most the build tools including Gulp, Grunt, webpack or npm.

So how does PostCSS tackle the problems we listed earlier?

- **Each plugin is installed separately.** This means you choose which ones you need and in what order they should be applied. Usually, plugins can be additionally configured using some set of options.
- **You can write your own plugins.** Each PostCSS plugin receives parsed CSS as an input parameter, analyses or modifies it, and returns it in the same manner. This means that plugins don't need to handle parsing CSS and converting it back into a string. So the ability to build your own plugins is not as difficult as you might think.
- **PostCSS can be used to polyfill real W3C features.** There are a lot of plugins that aim to implement features from new W3C specifications. This will enable you to write code that respects standards and is likely to be compatible with future versions of CSS.

## Using PostCSS

Theory is great, but let's move on to some juicy practice. Let's install PostCSS and see what it can actually do. We won't go into much detail about setting up proper project builds, since that's a topic that deserves an article of its own. Instead we'll run PostCSS directly from the command line. You can find more info on using PostCSS with your favourite build tool [on its Github page](https://github.com/postcss/postcss).

## Installing PostCSS

PostCSS is installed via node and npm, so make sure you have those installed before you start. To install PostCSS globally on your system run:

```
npm install -g postcss-cli
```

You can make sure it's working by running:

```
postcss --help
```

This will give you a list of parameters that the CLI accepts. You can also find these in the [postcss-cli documenation](https://github.com/code42day/postcss-cli).

## Running PostCSS

Now that we have PostCSS installed, let's give it something to work with. Create a `styles.css` file in your project folder and add some CSS. For example, define a flexbox container:

```
.container {
 display: flex;
}
```

Flexbox is a great feature, but it does require vendor prefixes to run on certain browsers. I would hate to maintain these manually. Fortunately, [Autoprefixer](https://github.com/postcss/autoprefixer), one of the most popular PostCSS plugins does exactly that. It automatically adds vendor prefixes based on the information provided via [Can I use](http://caniuse.com/). I'll show you how to use it to keep our vendor prefixes up to date.

To install Autoprefixer run:

```
npm install -g autoprefixer
```

Next, navigate to your project folder and create a `dist` folder that will contain the processed CSS:

```
mkdir dist
```

Then run PostCSS:

```
postcss -u autoprefixer styles.css -d dist
```

What this says is: _run Autoprefixer on `styles.css` and save the output to `dist/styles.css`_. Now if you open `dist/styles.css` you will see your CSS with all of the required vendor prefixes:

```
.container {
 display: -webkit-box;
 display: -webkit-flex;
 display: -ms-flexbox;
 display: flex;
}
```

If you run PostCSS with a `-w` flag, this will also start a watcher process and automatically rebuild `styles.css` each time the file is modified.

## Plugin Configuration

We can configure Autoprefixer to add prefixes according to the browsers we plan on supporting. This can be done using the `browsers` option. When running PostCSS via the CLI, plugin configuration needs to be defined in a separate .json file, for example, `postcss.json`. Let's create the file in the current folder and configure Autoprefixer to support the 2 most recent versions of each browser.

```
{
 "autoprefixer": {
 "browsers": ["last 2 versions"]
 }
}
```

We can now re-run the PostCSS watcher with the new configuration file:

```
postcss styles.css -u autoprefixer -c postcss.json -d dist -w
```

## Enabling Source Maps

Source maps are essential for debugging compiled CSS. PostCSS can generate inline source maps in the output file if you add the `--map` option (or the `-m` flag).

## Even More Plugins

PostCSS has an amazing number of plugins for linting, quality checks, fallback, old browser support, inlining assets, generating sprites, optimisation, new syntax support, and future CSS features. As mentioned, you can find a structured catalogue of plugins at [postcss.parts](http://postcss.parts/).

To give you a sample, here are a few more plugins that demonstrate the power of PostCSS.

### The Custom Properties Plugin for CSS Variables

The [postcss-custom-properties](https://github.com/postcss/postcss-custom-properties) plugin aims to implement support for the [W3C custom properties specification](https://www.w3.org/TR/css-variables/) (aka [native variables](http://www.sitepoint.com/css-variables-can-preprocessors-cant/)). It allows you to define custom properties in a selector with arbitrary values and reference these in other places in the stylesheet.

This serves a similar purpose to Less and Sass variables: to store common values and eliminate code duplication. The main difference is that the scoping mechanism is a bit different; similar to regular properties, CSS custom properties propagate along the element cascade, instead of being block scoped.

Here's an example of how this plugin works. The following code:

```
:root {
 --container-width: 800px;
}

.container {
 width: var(--container-width);
}
```

Will compile to:

```
.container {
 width: 800px;
}
```

### The Custom Selectors Plugin

The [postcss-custom-selectors](https://github.com/postcss/postcss-custom-selectors) plugin implements the [Custom Selector specification](https://drafts.csswg.org/css-extensions/#custom-selectors). This allows you to pre-define selectors and reference them later in the code. For example, we can save all headers in one selector and re-use this as a variable:

```
@custom-selector :--headings h1, h2, h3, h4, h5, h6;

:--headings {
 color: mediumblue;
}
```

Which will compile as follows:

```
h1,
h2,
h3,
h4,
h5,
h6 {
 color: mediumblue;
}
```

### The Russian Stylesheets Plugin

Ever wanted to learn Russian but were too busy writing CSS? No worries, you can now do both by writing CSS in Russian! Just use the [Russian Stylesheets](https://github.com/Semigradsky/postcss-russian-stylesheets) plugin. Check this out:

```
h1 {
 размер-шрифта: 20пикселей;
 цвет: красный;
 цвет-фона: белый;
 вес-шрифта: жирный;
}
```

Which is translated into:

```
h1 {
 font-size: 20px;
 color: red;
 background-color: white;
 font-weight: bold;
}
```

Well... I never said all the plugins were equally useful!

## Wrapping Things Up

Preprocessors like Less and Sass are great. They have contributed much to our development process and are definitely not something we can easily leave behind. But, I feel that that the time has come to stop and reconsider whether those tools are the right way to do things in the long run.

We don't just need new features writing stylesheets, we also need modularity, additional exposure of the new standards, as well as flexible build processes. PostCSS provides just that and might be a game changer in the CSS world.
