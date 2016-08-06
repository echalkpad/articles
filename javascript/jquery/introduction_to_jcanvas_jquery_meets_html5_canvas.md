# Introduction to jCanvas: jQuery Meets HTML5 Canvas

[Original URL](http://www.sitepoint.com/introduction-to-jcanvas-jquery-meets-html5-canvas/)

> HTML5 lets you draw graphics straight into your web page using the <canvas> element and its related JavaScript API. In this post, I'm going to introduce you to jCanvas, a free and open...

HTML5 lets you draw graphics straight into your web page using the [`<canvas>` element](https://html.spec.whatwg.org/multipage/scripting.html#the-canvas-element) and its related [JavaScript API](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API).

In this post, I'm going to introduce you to [jCanvas](http://projects.calebevans.me/jcanvas/), a free and open source jQuery-based library for the HTML5 Canvas API.

If you develop with jQuery, jCanvas makes it easier and quicker to code cool canvas drawings and interactive applications using jQuery syntax.

## What is jCanvas?

The jCanvas website explains:

> jCanvas is a JavaScript library, written using jQuery and for jQuery, that wraps around the HTML5 canvas API, adding new features and capabilities, many of which are customizable. Capabilities include layers, events, drag-and-drop, animation, and much more.

> The result is a flexible API wrapped up in a sugary, jQuery-esque syntax that brings power and ease to the HTML5 canvas.

jCanvas enables you to do everything you can do with the native Canvas API and more. If you prefer, you can also use native HTML5 Canvas API methods with jCanvas. The [draw() method](http://projects.calebevans.me/jcanvas/docs/drawManually/) serves just this purpose. Furthermore, you can easily extend jCanvas with your own methods and properties using its [extend() feature](http://projects.calebevans.me/jcanvas/docs/extending/).

## Adding jCanvas to Your Project

To include jCanvas in your project, [download the script](http://projects.calebevans.me/jcanvas/downloads/) from the official website or [the GitHub page](https://github.com/caleb531/jcanvas), then include it in your project folder. As mentioned, jCanvas needs jQuery to work, so be sure to include that too.

Your project's script files will look something like this:

```
<script src="js/jquery.min.js></script>
<script src="js/jcanvas.min.js></script>
<script src="js/script.js></script>
```

The last one would be where you put your custom JavaScript using the jCanvas API. Now let's take jCanvas for a test drive.

## Setting up the HTML Document

To follow along with the examples, start by adding a `<canvas>` element tag to a basic HTML5 document.

```
<canvas id="myCanvas" width="600" height="300">
 <p>This is fallback content 
 for users of assistive technologies 
 or of browsers that don't have 
 full support for the Canvas API.</p>
</canvas>
```

Here are a few points of interest about the code snippet above.

- By default, the dimensions of the `<canvas>` **drawing surface** are 300px by 150px. You can modify this default size in the width and height attributes inside the element's markup.
- Adding an `id` attribute is not required but will be an easy way to access the element with JavaScript.
- Content inside the `<canvas>` element is just a bitmap image. This makes it **inaccessible** to users of assistive technologies. Also, browsers that don't have [support for the Canvas API](http://caniuse.com/#feat=canvas) will not be able to access its content or interact with it in any way. Therefore, while [techniques aiming to make `<canvas>` more accessible](http://www.w3.org/Talks/2014/0510-canvas-a11y/#1) are yet to be made available, adding some **fallback content** for this category of users is the recommended practice.

If you were to use the native Canvas API, your JavaScript would look something like this:

```
var canvas = document.getElementById('myCanvas'),
 context = canvas.getContext('2d');
```

The variable `context` in the code above stores a reference to the **2D context** property of the Canvas object. It's this property that enables you to access all other properties and methods exposed by the HTML5 Canvas API.

If you'd like to learn more about the topic, [HTML5 Canvas Tutorial: An Introduction](http://www.sitepoint.com/html5-canvas-tutorial-introduction/) by Ivaylo Gerchev is a great read.

jCanvas methods and properties already contain a reference to the 2D context, therefore you can jump straight into drawing.

## Drawing Shapes with jCanvas

Most jCanvas methods accept a map of property-value pairs that you can list in any order you like.

Let's start by drawing a rectangle shape.

### The Rectangle Shape

This is how you draw a rectangle shape using the [`drawRect()` method](http://projects.calebevans.me/jcanvas/docs/rectangles) of the jCanvas object.

```
// Store the canvas object into a variable
var $myCanvas = $('#myCanvas');

// rectangle shape 
$myCanvas.drawRect({
 fillStyle: 'steelblue',
 strokeStyle: 'blue',
 strokeWidth: 4,
 x: 150, y: 100,
 fromCenter: false,
 width: 200,
 height: 100
});
```

The snippet above caches the canvas object into a variable called `$myCanvas`. The properties inside the `drawRect()` method are mostly self-explanatory, but here's a brief rundown:

- `fillStyle` sets the rectangle's background color;
- `strokeStyle` sets its border color;
- `strokeWidth` sets the shape's border width;
- `x` and `y` set the coordinates corresponding to the rectangle's **horizontal** and **vertical** position inside the canvas. A value of _0_ for _x_ and of _0_ for _y_, i.e., (0, 0), corresponds to the top left corner of the canvas. The `x` coordinates increase to the right and the `y` coordinates increase towards the bottom of the canvas. When drawing the rectangle, by default, jCanvas takes the `x` and `y` coordinates to lie at the center of the shape.
- To change this so that `x` and `y` correspond to the rectangle's top left corner, set the `fromCenter` property to `false`.
- Finally, the `width` and `height` properties set the dimensions of the rectangle.

![Rectangle drawn on canvas with explanation of canvas coordinates system](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/01/1453920207canvas-coordinates.png)

Here is a demo with the rectangle shape:

See the Pen [jCanvas example: Rectangle](http://codepen.io/SitePoint/pen/NxMxLd/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

### Arcs and Circles

Arcs are portions of the rim of a circle. With jCanvas, drawing arcs is just a matter of setting the desired values for a few properties inside the [`drawArc()` method](http://projects.calebevans.me/jcanvas/docs/arcs/):

```
$myCanvas.drawArc({
 strokeStyle: 'steelblue',
 strokeStyle: 'blue',
 strokeWidth: 4,
 x: 300, y: 100,
 radius: 50,
 // start and end angles in degrees
 start: 0, end: 200
});
```

Drawing arcs involves setting a value for the `radius` property as well as the `start` and `end` angles in degrees. If you'd like the direction of your arc to be counterclockwise, add the `ccw` property to the code above and set it to `true`.

Here's a CodePen demo of the above code:

See the Pen [jCanvas example: Arc](http://codepen.io/SitePoint/pen/eJrJoK/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

Drawing a circle is as easy as omitting both `start` and `end` properties from the `drawArc()` method.

For instance, here's how you can draw a simple graphic of a smiling face using only arc shapes:

```
$myCanvas.drawArc({
 // draw the face
 fillStyle: 'yellow',
 strokeStyle: '#333',
 strokeWidth: 4,
 x: 300, y: 100,
 radius: 80
}).drawArc({
 // draw the left eye
 fillStyle: '#333',
 strokeStyle: '#333',
 x: 250, y: 70,
 radius: 5
}).drawArc({
 // draw the right eye
 fillStyle: '#333',
 strokeStyle: '#333',
 x: 350, y: 70,
 radius: 5
}).drawArc({
 // draw the nose
 strokeStyle: '#333',
 strokeWidth: 4,
 ccw: true,
 x: 300, y: 100,
 radius: 30,
 start: 0,
 end: 200
}).drawArc({
 // draw the smile
 strokeStyle: '#333',
 strokeWidth: 4,
 x: 300, y: 135,
 radius: 30,
 start: 90,
 end: 280
});
```

Remember that jCanvas is based on the jQuery library, therefore you can **chain jCanvas methods** the same way you can chain jQuery methods.

Here's how the code above renders in the browser:

See the Pen [jCanvas example: Smiling Face](http://codepen.io/SitePoint/pen/QyryRM/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

## Drawing Lines and Paths

You can quickly draw lines with jCanvas using the [`drawLine()` method](http://projects.calebevans.me/jcanvas/docs/lines/) and plotting a series of points to which your lines are going to connect.

```
$myCanvas.drawLine({
 strokeStyle: 'steelblue',
 strokeWidth: 10,
 rounded: true,
 closed: true,
 x1: 100, y1: 28,
 x2: 50, y2: 200,
 x3: 300, y3: 200,
 x4: 200, y4: 109
});
```

The code above sets the `rounded` and `closed` properties to `true`, thereby rounding the corners of the line and closing the traced path.

See the Pen [jCanvas example: Connected lines](http://codepen.io/SitePoint/pen/mVLVZV/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

You can also draw flexible paths using the [drawPath() method](http://projects.calebevans.me/jcanvas/docs/paths/). Think of a path as one or more connected lines, arcs, curves, or vectors.

The `drawPath()` method accepts a map of points and a map of `x` and `y` coordinates for the **sub-paths** inside each point. You also need to specify the **type of path** you're going to draw, e.g., line, arc, etc.

Here's how you can draw a pair of connected horizontally and vertically pointing arrows using `drawPath()` and [draw arrows()](http://projects.calebevans.me/jcanvas/docs/arrows/), the latter of which is a handy jCanvas method that enables you to quickly draw an arrow shape on the canvas:

```
$myCanvas.drawPath({
 strokeStyle: '#000',
 strokeWidth: 4,
 x: 10, y: 10,
 p1: {
 type: 'line',
 x1: 100, y1: 100,
 x2: 200, y2: 100
 },
 p2: {
 type: 'line',
 rounded: true,
 endArrow: true,
 arrowRadius: 25,
 arrowAngle: 90,
 x1: 200, y1: 100,
 x2: 290, y2: 100
 },
 p3: {
 type: 'line',
 rounded: true,
 endArrow: true,
 arrowRadius: 25,
 arrowAngle: 90,
 x1: 100, y1: 100,
 x2: 100, y2: 250
 }
});
```

The `x` and `y` coordinates of each sub-path are relative to the `x` and `y` coordinates of the entire path.

And here's the result:

See the Pen [jCanvas example: Connected Arrows](http://codepen.io/SitePoint/pen/OMZMKb/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

## Drawing Text

You can quickly draw text on the canvas with the aptly named [drawText() method](http://projects.calebevans.me/jcanvas/docs/text/). The main properties you need for this to work are:

- `text`. Set this property to the text content you'd like to display on the canvas: e.g. `'Hello world'`
- `fontSize`. The value for this property determines the size of the text on canvas. You can set the value for this property with a number, which jCanvas interprets as a value in pixels: `fontSize: 30`. Alternatively, you can use points, but in such a case you need to specify the measurement inside quotes: `fontSize: '30pt'`
- `fontFamily` allows you to specify a typeface for your text image: `'Verdana, sans-serif'`.

Here's the sample code:

```
$myCanvas.drawText({
 text: 'Canvas is fun',
 fontFamily: 'cursive',
 fontSize: 40,
 x: 290, y: 150,
 fillStyle: 'lightblue',
 strokeStyle: 'blue',
 strokeWidth: 1
});
```

And this is what it looks like inside the `<canvas>` element on the web page:

See the Pen [jCanvas example: Drawing text](http://codepen.io/SitePoint/pen/KVRzPZ/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

## Drawing Images

You can import and manipulate images using the [drawImage() method](http://projects.calebevans.me/jcanvas/docs/images/). Here's an example:

```
$myCanvas.drawImage({
 source: 'imgs/cat.jpg',
 x: 250, y: 100,
 fromCenter: false,
 shadowColor: '#222',
 shadowBlur: 3,
 rotate: 40
});
```

And this is how the code above renders:

See the Pen [jCanvas example: Importing and manipulating an image](http://codepen.io/SitePoint/pen/ZQoWYj/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

You can view and fiddle around with all the examples above [combined into a single CodePen demo here](http://codepen.io/SitePoint/pen/ZQoWQM).

## Canvas Layers

If you've ever used an image editor application like Photoshop or Gimp, you're already familiar with layers. The cool part of working with layers is that you gain the ability to manipulate each image on your canvas individually, by drawing it on its own layer.

jCanvas offers a powerful [layer API](http://projects.calebevans.me/jcanvas/docs/layerAPI/) that adds flexibility to your canvas-based designs.

Here's an overview of how to use jCanvas layers.

### Adding Layers

You can only draw one object on each layer. You can add layers to your jCanvas project in either of two ways:

- Use the `addLayer()` method followed by the `drawLayers()` method
- Set the `layer` property to `true` inside any of the drawing methods

Here's how you apply the first technique to draw a blue rectangle.

```
$myCanvas.addLayer({
 type: 'rectangle',
 fillStyle: 'steelblue',
 fromCenter: false,
 name: 'blueRectangle',
 x: 50, y: 50,
 width: 400, height: 200
}).drawLayers();
```

See the Pen [PZeNGp](http://codepen.io/SitePoint/pen/PZeNGp/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

And here's how you apply the second method to draw the same rectangle:

```
$myCanvas.drawRect({
 fillStyle: 'steelblue',
 layer: true,
 name: 'blueRectangle',
 fromCenter: false,
 x: 50, y: 50,
 width: 400, height: 200
});
```

See the Pen [zrjqKb](http://codepen.io/SitePoint/pen/zrjqKb/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

As you can see, with each of the above, we get the same result.

The important point to notice in both code samples above is that the layer has a **name** that you set via the `name` property. This makes it easy to refer to this layer in code and do all sorts of cool things with it, like changing its index value, animating it, removing it, etc.

Let's see how we can do this.

### Animating Layers

You can quickly add animations to your layer-based drawings with jCanvas using the [animateLayer() method](http://projects.calebevans.me/jcanvas/docs/animateLayers/). This method accepts the following parameters:

- The layer's _index_ or `name`
- An object with key-value pairs of properties to animate
- The animation's _duration_ in milliseconds. This is an optional parameter, if you don't set it, it defaults to _400_
- The _easing_ of the animation. This is also an optional parameter, if you don't set it, it defaults to `swing`
- An optional callback function that runs when the animation completes.

Let's see the `animateLayer()` method in action. We'll draw a semi-transparent orange circle on a layer, then animate its position, color, and opacity properties:

```
// Draw circle
$myCanvas.drawArc({
 name: 'orangeCircle',
 layer: true,
 x: 50, y: 50,
 radius: 100,
 fillStyle: 'orange',
 opacity: 0.5
});

// Animate the circle layer 
$myCanvas.animateLayer('orangeCircle', {
 x: 150, y: 150,
 radius: 50,
}, 1000, function(layer) { // Callback function
 $(this).animateLayer(layer, {
 fillStyle: 'darkred',
 x: 250, y: 100,
 opacity: 1
 }, 'slow', 'ease-in-out');
});
```

Check out the demo below to see the animation:

See the Pen [jCanvas example: Animating Layers](http://codepen.io/SitePoint/pen/xZjVqv/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

You can view all the three previous examples [combined in this CodePen demo](http://codepen.io/SitePoint/pen/KVRzvB).

### Draggable Layers

One more cool feature I'd like to draw your attention to is the ability to turn a regular jCanvas layer into a [draggable layer](http://projects.calebevans.me/jcanvas/docs/draggableLayers/) by simply setting the `draggable` property of the layer to `true`.

Here's how:

```
$myCanvas.drawRect({
 layer: true,
 draggable: true,
 bringToFront: true,
 name: 'blueSquare',
 fillStyle: 'steelblue',
 x: 250, y: 150,
 width: 100, height: 100,
 rotate: 80,
 shadowX: -1, shadowY: 8,
 shadowBlur: 2,
 shadowColor: 'rgba(0, 0, 0, 0.8)'
})
.drawRect({
 layer: true,
 draggable: true,
 bringToFront: true,
 name: 'redSquare',
 fillStyle: 'red',
 x: 190, y: 100,
 width: 100, height: 100,
 rotate: 130,
 shadowX: -2, shadowY: 5,
 shadowBlur: 3,
 shadowColor: 'rgba(0, 0, 0, 0.5)'
});
```

The snippet above draws two draggable rectangle layers by incorporating: `draggable: true`. Also, note the use of the `bringToFront` property, which ensures that when you drag a layer, it automatically gets pushed to the front of all other existing layers.

Finally, the code rotates the layers and sets a box shadow, just to show how you can quickly add these effects to your jCanvas drawings.

The result looks like this:

![Example of draggable rectangle layers](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/01/1453920211draggable-layers.png)

If you'd like your app to do something before, during, or after moving a draggable layer, jCanvas makes it easy to accomplish this by supporting callback functions during the relevant events:

- **`dragstart`** triggers when you start dragging the layer
- **`drag`** fires as you drag the layer
- **`dragstop`** triggers when you stop dragging the layer
- **`dragcancel`** occurs when you drag the layer off the border of the drawing surface of the canvas.

Let's say you'd like to display a message on the page after the user finishes dragging a layer. You can reuse the code snippet above by adding a callback function to the `dragstop` event, like so:

```
$myCanvas.drawRect({
 layer: true,

 // Rest of the code as shown above...

 // Callback function
 dragstop: function(layer) {
 var layerName = layer.name;
 el.innerHTML = 'The ' + layerName + ' layer has been dropped.';
 }
})
.drawRect({
 layer: true,

 // Rest of the code...

 // Callback function
 dragstop: function(layer) {
 var layerName = layer.name;
 el.innerHTML = 'The ' + layerName + ' layer has been dropped.';
 }
});
```

After dragging each square, you'll see a message on the page telling you which color square you just dropped. Try it out in the demo below:

See the Pen [Draggable jCanvas Layers with Callback Function](http://codepen.io/SitePoint/pen/OMZNQx/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

## Conclusion

In this post I've introduced you to jCanvas, a new jQuery-based library that works with the HTML5 Canvas API. I've illustrated some of jCanvas methods and properties that quickly enable you to draw on the canvas surface, add visual effects, animations, and draggable layers.

There's so much more that you can do with jCanvas. You can [visit the jCanvas documentation](http://projects.calebevans.me/jcanvas/docs/) for detailed guidance and examples, which you can quickly test in [the sandbox](http://projects.calebevans.me/jcanvas/sandbox/) on the jCanvas website.

To accompany this article, I've put together a basic painting application on CodePen using the jCanvas `drawLine()` method:

See the Pen [jCanvas Painting App](http://codepen.io/SitePoint/pen/XXqdEL/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

Feel free to make it better by adding more features and sharing your results with the SitePoint community.
