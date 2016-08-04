# ConnorAtherton/walkway

[Original URL](https://github.com/ConnorAtherton/walkway)

> I loved the animations for the polygon ps4 review a few months back and decided to create a small library to re-create them. How to use Create a new Walkway instance with a supplied options object....

I loved the animations [for the polygon ps4 review](http://www.polygon.com/a/ps4-review) a few months back and decided to create a small library to re-create them.

## [](https://github.com/ConnorAtherton/walkway#how-to-use)How to use

Create a new `Walkway` instance with a supplied options object. When you want to start animating call `.draw` on the returned instance providing an optional callback that will be called when drawing is complete.

```
// Create a new instance
var svg = new Walkway(options);
// Draw when ready, providing an optional callback
svg.draw(callback);

// Options passed in as an object, see options below.
var svg = new Walkway({ selector: '#test'});

// Overwriting defaults
var svg = new Walkway({
 selector: '#test',
 duration: '2000',
 // can pass in a function or a string like 'easeOutQuint'
 easing: function (t) {
 return t * t;
 }
});

svg.draw();

// If you don't want to change the default options you can
// also supply the constructor with a selector string.
var svg = new Walkway('#test');

svg.draw(function () {
 console.log('Animation finished');
});
```

### [](https://github.com/ConnorAtherton/walkway#options)Options

- **selector** (_mandatory_) - The selector of the parent element (usually will be a specific svg element)
- **duration** - Time the animation should run for, in ms. Default is 400.
- **easing** - Name of the easing function used for drawing. Default is 'easeInOutCubic'. You can also supply your own function that will be passed the progress and should return a value in the range of [0, 1];

### [](https://github.com/ConnorAtherton/walkway#easing)Easing

All credit for the built-in easing functions go to [gre](https://github.com/gre) from [this gist](https://gist.github.com/gre/1650294).

## [](https://github.com/ConnorAtherton/walkway#things-to-do)Things to do

Currently, this only works on `path` and `line` elements. Walkway will also work with `polyline` elements when I have a few hours free.

### [](https://github.com/ConnorAtherton/walkway#demo-coming-soon)Demo coming soon
