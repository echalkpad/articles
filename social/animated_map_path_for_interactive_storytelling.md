# Animated Map Path for Interactive Storytelling

[Original URL](http://tympanus.net/codrops/2015/12/16/animated-map-path-for-interactive-storytelling/)

> An interactive journey where a Canvas map path is animated according to the content that is scrolled on the page.

An interactive journey where a Canvas map path is animated according to the content that is scrolled on the page.

![](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/themes/codropstheme03/images/advertisement.jpg)

[![StorytellingMap](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/uploads/2015/12/StorytellingMap.jpg)](http://tympanus.net/Development/StorytellingMap/)

![](http://tympanus.net/Development/StorytellingMap/img/journey/1.jpg)<br>
![](http://tympanus.net/Development/StorytellingMap/img/journey/2.jpg)<br>
![](http://tympanus.net/Development/StorytellingMap/img/journey/3.jpg)<br>
![](http://tympanus.net/Development/StorytellingMap/img/journey/4.jpg)<br>
![](http://tympanus.net/Development/StorytellingMap/img/journey/5.jpg)<br>
![](http://tympanus.net/Development/StorytellingMap/img/journey/6.jpg)<br>
![](http://tympanus.net/Development/StorytellingMap/img/journey/7.jpg)<br>
![](http://tympanus.net/Development/StorytellingMap/img/journey/8.jpg)<br>
![](http://tympanus.net/Development/StorytellingMap/img/journey/9.jpg)<br>
![](http://tympanus.net/Development/StorytellingMap/img/journey/10.jpg)<br>
![](http://tympanus.net/Development/StorytellingMap/img/journey/11.jpg)

[View demo](http://tympanus.net/Development/StorytellingMap/) [Download source](http://tympanus.net/Development/StorytellingMap/StorytellingMap.zip)

Today we'd like to share an experimental demo with you. This demo is an interactive map that will animate a map path while scrolling the page. The main idea is to connect the story being told with the path itself. The journey can also contain images that will indicate where they have been taken with a semi-transparent cone. It will appear as soon as the image is in the visible viewport. A little plane icon is used in the last bit of the path in order to indicate that the journey was made on an aircraft.

**Attention:** This is a highly experimental project and it might not work in all browsers. **Currently there is no IE support.**

**Browser Support:**

- <span class="bs-browser">Chrome</span>

  <span class="bs-info">Supported</span>

- <span class="bs-browser">Firefox</span>

  <span class="bs-info">Supported</span>

- <span class="bs-browser">Internet Explorer</span>

  <span class="bs-info">Not supported</span>

- <span class="bs-browser">Safari</span>

  <span class="bs-info">Supported</span>

- <span class="bs-browser">Opera</span>

  <span class="bs-info">Supported</span>

**The photography is courtesy of [Dan Rubin](http://instagram.com/danrubin/) who has travelled to Iceland and has taken some incredible shots that we used in the demo. Find him on [Instagram](http://instagram.com/danrubin), [Twitter](https://twitter.com/danrubin), [Flickr](https://www.flickr.com/photos/danrubin) and [VSCO](http://danrubin.vsco.co/).**

To make this map, we chose [`canvas`](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API) for rendering for performance reasons. It could be done purely with SVG, but at the time of writing, SVG rendering performance isn't quite there yet, while `canvas` is hardware accelerated on most platforms.

That's not to say we won't use SVG at all, though. On the contrary: to draw on the canvas, we need to get the map graphics from somewhere, and maps are perfectly suited for vector formats like SVG. Moreover, we also need to get trail and point data, and SVG is a great candidate to store that information.

So now let's see how we can use both together.

## []()SVG + Canvas

First, we need to prepare our map. What we will need from it, for this project:

- The trail path;
- The path of the "camera";
- Points of interest, with names;
- And of course, the map itself.

Both paths are simple... well, `<path>` elements, that can be drawn with any Bézier curve tool or similar.

![paths](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/uploads/2015/12/paths.png)

It's important that they should be actual `<path>` elements instead of other primitives such as `<polygon>`, `<line>`, etc. If you are using Illustrator, create a **Compound Path** by selecting the object and going to `Edit > Compound Path > Make` or by pressing <kbd>Command/Ctrl</kbd>+<kbd>8</kbd> to convert the object to `<path>`

Points of interest can be stored using `<circle>` elements, created with the regular ellipse tool. The names of the points can be set as the name of their layers, and you can retrieve them later with `getAttribute('id')`.<br>
Speaking of layer names, don't forget to set the name of the layers of these "special" graphics, because we'll need them in order to access the layers later by code.

![layers](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/uploads/2015/12/layers.png)

Finally, the map itself can be just any graphic. You can draw anything you want on top of it too, like additional points of interest, terrain, parallels, etc.

![](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/themes/codropstheme03/images/advertisement.jpg)

Drawing SVG on `canvas` is simple, but comes with a caveat: you can only draw straight from actual .svg files, you can't modify the SVG with code before drawing. Therefore, you have to hide all the special layers of the SVG (camera path and the like) before saving. Loading the SVG both as a regular `<img>` for drawing in canvas and as a `<svg>` element for accessing data means that we have to load it two times and hope the browser cache saves us from having to download it twice.

The code could look like this (using ES2015 and [Promises](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise)):

```
 let mapFile='map.svg'
 let mapSVG, mapImage

 ajaxGet(mapFile).then(response=>{ // Load the SVG with an Ajax request.
 mapSVG=Array.from( // Makes a new array from...
 new DOMParser()
 .parseFromString(response,'image/svg+xml')
 .childNodes // ...the NodeList we get from the parsed SVG file.
 ) // It returns a handful of nodes, like 
 .filter(node=>{ // but we only need the svg element, so we filter that
 let tag=node.tagName // array looking for any elements ...
 if(typeof tag=='undefined')
 return false
 return tag.toLowerCase()==='svg' // ...that have the tag name svg...
 })[0] // ...and we get the first (and usually only) one.

 loadImage(mapFile).then(img=>{ // Then we load the image again,
 mapImage=img // this time as an `img` element.

 ...
```

The performance gains for using `canvas` will be nullified, though, if we draw from the SVG on every frame, especially if your map is complex. There are some techniques you can use to avoid that.

The simplest one is creating a hidden canvas and drawing the SVG there once. Then, you draw on the main canvas _from that hidden canvas_ instead of drawing from the SVG: drawing from a bitmap source is much faster.

That might come with some problems though: one of the biggest reasons for using vector graphics is being able to zoom without losing resolution, and that is lost if we draw on a canvas that's too small. Using a huge canvas for drawing on every frame, though, can get pretty slow.<br>
A technique we can employ to mitigate both issues is keeping a high resolution copy of the map, and using a reasonably sized buffer canvas for moving – and refresh that buffer only when we move or zoom too much.

![buffer](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/uploads/2015/12/buffer.gif)

Doing this allows us to only draw from the high resolution image when we need it, and doesn't restrict us to using low resolutions.

### []()Paths

We can move through an SVG path using a built-in `<path>` function: `getPointAtLength`. It returns the position of a point on an arbitrary length along the path. We can get the total length of the path using `getTotalLength`.

```
 let path = mapSVG.querySelector('#camera-path path') // Gets the path element
let length = mapSVG.getTotalLength() // Gets the length of the path

// To get, for example, the position of a point halfway through the path
path.getPointAtLength(length*0.5) // returns {x: ..., y: ...}
```

It would be useful to be able to tell how far along the path each point of interest is located. Unfortunately, there's no `getLengthAtPoint` function (of course, because there's no guarantee that a point crosses the path at all).<br>
We can approximate that, however, getting a length that is more or less close to the point, using simple trigonometry and some iterations:

![length](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/uploads/2015/12/length.gif)

We can use the same `getPointAtLength` function to draw the trail on the canvas, running the function a hundred of times, storing the points in an array, and then drawing lines from that array. Alternately, we can parse the `d` attribute from the `<path>` element and use similar `canvas` drawing commands.

Hopefully these techniques will give you a bit of insight into how something like this can be achieved. Now, let's have a look at the results of our implementation.

## The Map in Action

At the beginning of the journey, we show the map and when we start scrolling, the path animation begins.

![StorytellingMap01](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/uploads/2015/12/StorytellingMap01.png)

When linking an image in the story with the path, we show a cone that indicates where the picture was taken:

![StorytellingMap02](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/uploads/2015/12/StorytellingMap02.jpg)

There are points that can be zoomed and paused at, in order to show a gallery or other relevant parts of the story:

![StorytellingMap03](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/uploads/2015/12/StorytellingMap03.png)

For the final bit, we use a little plane icon to indicate that this part of the journey was taken on an aircraft:

![StorytellingMap04](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/uploads/2015/12/StorytellingMap04.png)

**We hope you enjoy this experiment and find it inspiring!**

_GitHub link coming soon!_

[View demo](http://tympanus.net/Development/StorytellingMap/) [Download source](http://tympanus.net/Development/StorytellingMap/StorytellingMap.zip)
