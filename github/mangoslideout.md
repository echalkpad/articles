# Mango/slideout

[Original URL](https://github.com/Mango/slideout)

> A touch slideout navigation menu for your mobile web apps. Features Dependecy-free. Simple markup. Native scrolling. Easy customization. CSS transforms & transitions. Just 4 Kb! Demo Check out...

> A touch slideout navigation menu for your mobile web apps.

## [](https://github.com/Mango/slideout#features)Features

- Dependecy-free.
- Simple markup.
- Native scrolling.
- Easy customization.
- CSS transforms & transitions.
- Just 4 Kb!

## [](https://github.com/Mango/slideout#demo)Demo

[Check out the demo](https://mango.github.io/slideout/) to see it in action (on your mobile or emulate touches on your browser).

[![Slideout.js demo](https://camo.githubusercontent.com/9975d7a2331494043067998480ace0f1d2157e86/68747470733a2f2f692e696d6775722e636f6d2f415767776c56572e676966)](https://camo.githubusercontent.com/9975d7a2331494043067998480ace0f1d2157e86/68747470733a2f2f692e696d6775722e636f6d2f415767776c56572e676966)

## [](https://github.com/Mango/slideout#installation)Installation

```
$ npm install slideout

$ bower install https://github.com/Mango/slideout.git

$ component install mango/slideout
```

## [](https://github.com/Mango/slideout#usage)Usage

Implementing Slideout.js into your project is easy.

First of all, you'll need to create your markup. You should have a menu (`#menu`) and a main content (`#panel`) into your body.

```
<nav id="menu">
 <header>
 <h2>Menu</h2>
 </header>
</nav>

<main id="panel">
 <header>
 <h2>Panel</h2>
 </header>
</main>
```

Add the Slideout.js styles (index.css) in your web application.

```
html,
body {
 width: 100%;
 height: 100%;
}

.slideout-menu {
 position: fixed;
 left: 0;
 top: 0;
 bottom: 0;
 right: 0;
 z-index: 0;
 width: 256px;
 overflow-y: scroll;
 -webkit-overflow-scrolling: touch;
 display: none;
}

.slideout-panel {
 position:relative;
 z-index: 1;
}

.slideout-open,
.slideout-open body {
 overflow: hidden;
}

.slideout-open .slideout-menu {
 display: block;
}
```

Then you just include Slideout.js and create a new instace with some options:

```
<script src="dist/slideout.min.js"></script>
<script>
 var slideout = new Slideout({
 'panel': document.getElementById('panel'),
 'menu': document.getElementById('menu'),
 'padding': 256,
 'tolerance': 70
 });
</script>
```

### [](https://github.com/Mango/slideout#full-example)Full example

```
<!doctype html>
<html lang="en">
 <head>
 <meta charset="utf-8">
 <title>Slideout Demo</title>
 <meta http-equiv="cleartype" content="on">
 <meta name="MobileOptimized" content="320">
 <meta name="HandheldFriendly" content="True">
 <meta name="apple-mobile-web-app-capable" content="yes">
 <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
 <style>
 html,
 body {
 width: 100%;
 height: 100%;
 }

 .slideout-menu {
 position: fixed;
 left: 0;
 top: 0;
 bottom: 0;
 right: 0;
 z-index: 0;
 width: 256px;
 overflow-y: scroll;
 -webkit-overflow-scrolling: touch;
 display: none;
 }

 .slideout-panel {
 position:relative;
 z-index: 1;
 }

 .slideout-open,
 .slideout-open body {
 overflow: hidden;
 }

 .slideout-open .slideout-menu {
 display: block;
 }
 </style>
 </head>
 <body>

 <nav id="menu">
 <h2>Menu</h2>
 </nav>

 <main id="main">
 <header>
 <button>☰</button>
 <h2>Panel</h2>
 </header>
 </main>

 <script src="dist/slideout.min.js"></script>
 <script>
 var slideout = new Slideout({
 'panel': document.getElementById('panel'),
 'menu': document.getElementById('menu'),
 'padding': 256,
 'tolerance': 70
 });
 </script>

 </body>
</html>
```

## [](https://github.com/Mango/slideout#browser-support)Browser Support

- Chrome (IOS, Android, desktop)
- Firefox (Android, desktop)
- Safari (IOS, Android, desktop)
- Opera (desktop)
- IE 10+ (desktop)

## [](https://github.com/Mango/slideout#api)API

### [](https://github.com/Mango/slideout#slideoutoptions)Slideout(options)

Create a new instance of `Slideout`.

- `options` (Object) - Options to customize a new instance of Slideout.
- `options.panel` (HTMLElement) - The DOM element that contains all your application content (`.slideout-panel`).
- `options.menu` (HTMLElement) - The DOM element that contains your menu application (`.slideout-menu`).
- `[options.duration]` (Number) - The time (milliseconds) to open/close the slideout. Default: `300`.
- `[options.fx]` (String) - The CSS effect to use when animating the opening and closing of the slideout. Default: `ease`.
- `[options.padding]` (Number) - Default: `256`.
- `[options.tolerance]` (Number) - Default: `70`.

  var slideout = new Slideout({ 'panel': document.getElementById('main'), 'menu': document.getElementById('menu'), 'padding': 256, 'tolerance': 70 });

### [](https://github.com/Mango/slideout#slideoutopen)Slideout.open();

Opens the slideout menu.

### [](https://github.com/Mango/slideout#slideoutclose)Slideout.close();

Closes the slideout menu.

### [](https://github.com/Mango/slideout#slideouttoggle)Slideout.toggle();

Toggles (open/close) the slideout menu.

### [](https://github.com/Mango/slideout#slideoutisopen)Slideout.isOpen();

Returns `true` if the slideout is currently open, and `false` if it is closed.

```
slideout.isOpen(); // true or false
```

## [](https://github.com/Mango/slideout#npm-scripts)npm-scripts

```
$ npm run build

$ npm run dist

$ npm test
```

## [](https://github.com/Mango/slideout#with--by)With ❤ by

## [](https://github.com/Mango/slideout#license)License

MIT license. Copyright © 2015 [Mango](http://getmango.com).
