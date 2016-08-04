# Space.js

[Original URL](http://www.slashie.org/articles/space.js/)

> Creating content on the Internet is not just about the contents. It's about reaching out with a message, telling a story, trying to translate an emotion into bits and communicating them to the...

Creating content on the Internet is not just about the contents. It's about reaching out with a message, telling a story, trying to translate an emotion into bits and communicating them to the user, efficiently.

I wanted the end user to feel a stronger connection to my content. I feel that the _medium_ is often overlooked, sure - the message is the _core_, but what's wrong with silver platters? I love mediums that help narrate the experience. The way an old mechanical Polaroid cameras printed picture feels in your hands, or perhaps the note produced from the purposeful humming sounds of a typewriter. Imagine holding an old hardcover book, it doesn't present you information, it tells you its story, it tells you it's secrets. Medium is important.

![Both](http://www.slashie.org/articles/space.js/img/camera1.jpg)

We generally use web pages as our canvases and paint content on the x– and y-axises. This is our tangible abstraction of reality. Here we are going to explore what happens when we introduce z-axis to content presentation. Instead of stripping down reality to a plane, we are going to simplify it, keeping our three physical dimensions but only their minimal essence.

![](http://www.slashie.org/articles/space.js/img/axisinformation.png)

For our messages to communicate across **efficiently**, _we need to create a powerful connection between the user and our medium_. Today we are going to explore a new way of presenting stories on the web. And for this I've created an open-source and free to use JavaScript library i call _space.js_. It's basically parallax technologies used for narration. Think of it as a bicycle for content presentation.

![](http://www.slashie.org/articles/space.js/img/spacejs.png)

Now would be a great time to head over to [**the demos**](http://www.slashie.org/space.js) to try it out for yourself!

[![](http://www.slashie.org/articles/space.js/img/demosthumb.jpg)](http://www.slashie.org/space.js)

## Using the library

The library is 

<span class="caps">HTML</span>

-driven, which means that you don't need to write a single line of JavaScript to use it on your site and still have a lot of flexibility!

The core of the library is to divide our 

<span class="caps">HTML</span>

 into frames, or _space-frames_ as we call them her (to not conflict the common class name "frame").

### Creating a frame

```
<div class="space-frame">[contents]</div>
```

I would also **strongly** recommend using an inner-frame inside the space-frame, which provides some helpful 

<span class="caps">CSS</span>

 to make things centered both vertically and horizontally inside the frame.

```
<div class="space-frame">
 <section class="space-inner-frame">
 [contents]
 </section>
</div>
```

### Customize Duration

If we want we can provide a custom duration for our frames with the data-duration attribute, which multiplies the default duration of the transition.

```
<section class="space-frame" data-duration="1.4">...</section>
<section class="space-frame" data-duration="0.6">...</section>
```

### Customize transition

Space.js has a default default transition - which is to enter by fading in and exit by scaling up and fading out. We can also provide a custom transition override to the library from predefined transitions. (We can also create our own transitions from scratch, but we'll get to that later.)

```
<section class="space-frame" data-transition="rotate360">...</section>
```

Multiple values are supported!

```
<section class="space-frame" data-transition="rotate360 fadeOut slideInLeft">...</section>
```

### Custom entry and exit

If we really want to get into detail, we can provide how we wish the frame to enter (first half of the frame duration) and exit (second half).

```
<section class="space-frame" data-enter="fadeIn" data-exit="fadeOut zoomOut">...</section>
```

## What a complete frame could look like

```
<div class="space-frame" data-enter="fadeIn" data-exit="zoomOut fadeOut" data-duration="1.3">
 <section class="space-inner-frame">
 <div style="background-image:url(img/splash.png); padding:150px 200px;" class="bg">
 <section>
 <p>Demo 1</p>
 <h1>The Gallery</h1>
 </section>
 </div>
 </section>
</div>
```

[Full 

<span class="caps">API</span>

 on GitHub](https://github.com/gopatrik/space.js)

## Behind the scenes.

The process of the library is pretty straight forward. When we have divided our 

<span class="caps">HTML</span>

 into frames, we assume each frame to initially cover the height and width of the window, which is equivalent to `data-duration="1"`. Each frame is set to have the same scroll-duration as it would have if we would've normally scrolled past it. So we measure how high the view port of the users browser window is let's say 800px, then we count the number of frames there are in the 

<span class="caps">HTML</span>

, let's say 20\. To simulate a proper scrolling mechanism the body-height is set to #frames _their height, in this case 800px_ 20 frames = 16000px.

We then hide all frames. Because of superior performance advantages when animating a `position: fixed;` object, we need to keep them fixed, which means we have to hide all frames that should not be showing at a particular height in the document.

The function scrollTop() tells us how far we are from the top of the page. We can use it to check what frame should be active and at what stage that frames transition is in. Instead of scrolling past an element we change its 

_<span class="caps">CSS</span>_

 according to when it _should be visible_, calculated from the value we get from scrollTop().

We then create a listener for the for the scroll event on the document using the `ScrollController` provided in the library. Which accepts a method as an argument, and when we receive the scroll event it keeps running that method. In this case the _method_ is our animate(), which animates our frames. The ScrollController will then run the animation method at an interval of something around 60 fps while we are scrolling to not overload client. It's also of **most importance** that we use `requestAnimationFrame(...)` to make sure our renderings are synced up with the clients browser, otherwise we might lose a lot of performance, which is of essence here.

The `animate()` method does **two things**.

1. Calculate and set the _current frame_ through a simple comparison of how far we have scrolled (`scrollTop()`) and which frame should be showing at that particular time.
2. See how far we have scrolled into the _current frame_, and calculate all delta values of the transition based on that. E.g. if we fade something in; `'opacity':{from:0, to:1}` and have scrolled 36% into of that element, it would be given the opacity 0.36.

The framework uses a **linear easing** algorithm for the transition, as per much experimentation, this is what feels most natural when the user _controls_ the whole transition speed with his or her fingers. After all, why should the content move at different speeds when your motion remains constant?

### Transitions

I love being able to construct as much as possible through the 

<span class="caps">HTML</span>

. That's why I made sure there are pre-defined transitions in the library, so that they can be accessed as keys from the `data-transition`, `data-enter` and `data-exit` attributes in the 

<span class="caps">HTML</span>

. It seems the most efficient properties to transition on are **opacity**, **scale**, **translate3d** and **rotation**.

Basically they look like this:

```
zoomOut:{
 'scale': {from: 1, to: 0}
},
slideInBottom: {
 'translate3d':{
 from:{y:700},
 to: {y:0}
 }
},
slideOutDown: {
 'translate3d':{
 from:{y:0},
 to: {y:700}
 }
},
rotate3dOut: {
 'rotate3d':{
 from:{x:0, y:0, z:0, angle:0},
 to: {x:1, y:-1,z:0, angle:90}
 }
}
```

You can add your own transitions with the `` `addTransitions`` method. Make sure it is done after the library is loaded.

```
<script src="space.js"></script>

<script type="text/javascript">
 var transitions = {
 rotate720: {
 'rotate':{from:0, to:720}
 },
 fadeOutHalf: {
 'opacity':{from:1, to:0.5}
 }
 };
 Space.addTransitions(transitions);
</script>
```

Check out the full [source code](https://github.com/gopatrik/space.js) for more details!

## Demos

[![](http://www.slashie.org/articles/space.js/img/demosthumb.jpg)](http://www.slashie.org/space.js)

## Last words

Note that this is not intended to be used everywhere. Every type of content has a preferred medium. I mainly wanted to inspire you to not feel conformed to the two-dimensional world of the Internet.

Hope you're feeling inspired! Now tell us your story!

**Thanks for reading!** Let me know if you want me to elaborate on something!

## ** Spread the love

For every tweet I promise to do a cheerful little little hop.

[Tweet](https://twitter.com/share)
