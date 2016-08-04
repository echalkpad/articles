# How to animate "box-shadow" with silky smooth performance

[Original URL](http://tobiasahlin.com/blog/how-to-animate-box-shadow/)

> How do you animate the box-shadow property in CSS without causing re-paints on every frame, and heavily impacting the performance of your page? Short answer: you don't. Animating a change of...

How do you animate the `box-shadow` property in CSS without causing re-paints on every frame, and heavily impacting the performance of your page? Short answer: you don't. Animating a change of `box-shadow` _will_ hurt performance.

There's an easy way of mimicking the same effect, however, with minimal re-paints, that should let your animations run at a solid 60 FPS: animate the `opacity` of a pseudo-element.

## Demo

[![Recording of box-shadow demo in action](http://tobiasahlin.com/static/animate-box-shadow/demo.gif)](http://tobiasahlin.com/demo/animate-box-shadow/)

[Have a look at the demo](http://tobiasahlin.com/demo/animate-box-shadow/) and compare the two different techniques we'll be exploring. If the two examples look the same to you, that's the point. The only difference is how we apply and animate the shadow. On the left we're animating `box-shadow` on `hover`, and on the right we're adding a pseudo-element with `:after`, applying the shadow to that, and animating the `opacity` of that element.

If you bring up your developer tools and hover one of these items, you should see something similar to this (green bars are paints; less is better):

[![Animation performance when hovering the different boxes](http://tobiasahlin.com/static/animate-box-shadow/animation-performance.png)](http://tobiasahlin.com/demo/animate-box-shadow/)

There are clearly more re-paints when hovering the cards on the left side (animating `box-shadow`), compared to hovering the cards on the right side (which animate the `opacity` of their pseudo-element).

Why are we seeing this effect? There are [very few CSS properties](http://csstriggers.com) that can be animated without constantly triggering repaints for every frame, namely `opacity` and `transform`. We minimize the amount of repaints (and work that your browser has to do) by sticking to only changing these two properties during the animation.

This is the **critical difference** between the two techniques, stripping out all of the other layout styles:

```
/* The slow way */
.make-it-slow {
 box-shadow: 0 1px 2px rgba(0,0,0,0.15);
 transition: box-shadow 0.3s ease-in-out:
}

/* Transition to a bigger shadow on hover */
.make-it-slow:hover {
 box-shadow: 0 5px 15px rgba(0,0,0,0.3);
}

/* The fast way */
.make-it-fast {
 box-shadow: 0 1px 2px rgba(0,0,0,0.15);
}

/* Pre-render the bigger shadow, but hide it */
.make-it-fast:after {
 box-shadow: 0 5px 15px rgba(0,0,0,0.3);
 opacity: 0;
 transition: opacity 0.3s ease-in-out:
}

/* Transition to showing the bigger shadow on hover */
.make-it-fast:hover:after {
 opacity: 1;
}
```

In the example that performs better we have two layers: one for the box, and one for the shadow, and only animate the `opacity` property of the shadow layer.

## Breaking it down

With the fundamentals in place, let's look at how to create [the 3D card effect showcased in the demo](http://tobiasahlin.com/demo/animate-box-shadow/). The first step is to move the shadow to a pseudo-element, like we did above. Let's also add all of the layout code to create the card:

```
/* All HTML you need is <div class="box"></div> */

/* Create a simple white box, and add the shadow for the initial state */
.box {
 position: relative;
 display: inline-block;
 width: 100px;
 height: 100px;
 border-radius: 5px;
 background-color: #fff;
 box-shadow: 0 1px 2px rgba(0,0,0,0.15);
 transition: all 0.3s ease-in-out;
}

/* Create the hidden pseudo-element */
/* include the shadow for the end state */
.box:after {
 content: '';
 position: absolute;
 width: 100%;
 height: 100%;
 opacity: 0;
 border-radius: 5px;
 box-shadow: 0 5px 15px rgba(0,0,0,0.3);
 transition: opacity 0.3s ease-in-out;
}
```

Note that we're adding a `transition` to both the `.box`, and `.box:after`, since we're going to animate both of these elements: `transform` for `.box`, and `opacity` for `.box:after`.

These styles give us a white box with a subtle `box-shadow`. The stronger shadow from `.box:after` is completely hidden at this point, and you can't interact with the box:

To create the same effect as in the [demo](http://tobiasahlin.com/demo/animate-box-shadow/), now all we need to do is to scale up the `.box` on hover, and fade in the pseudo-element and its shadow:

```
/* Scale up the box */
.box:hover {
 transform: scale(1.2, 1.2);
}

/* Fade in the pseudo-element with the bigger shadow */
.box:hover:after {
 opacity: 1;
}
```

That's it! Hover the box to preview the effect:

To summarize, here's all the CSS, with all vendor prefixes, and some custom easing for additional ✨👌:

```
.box {
 position: relative;
 display: inline-block;
 width: 100px;
 height: 100px;
 background-color: #fff;
 border-radius: 5px;
 box-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
 border-radius: 5px;
 -webkit-transition: all 0.6s cubic-bezier(0.165, 0.84, 0.44, 1);
 transition: all 0.6s cubic-bezier(0.165, 0.84, 0.44, 1);
}

.box:after {
 content: "";
 border-radius: 5px;
 position: absolute;
 top: 0;
 left: 0;
 width: 100%;
 height: 100%;
 box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
 opacity: 0;
 -webkit-transition: all 0.6s cubic-bezier(0.165, 0.84, 0.44, 1);
 transition: all 0.6s cubic-bezier(0.165, 0.84, 0.44, 1)
}

.box:hover {
 -webkit-transform: scale(1.25, 1.25);
 transform: scale(1.25, 1.25)
}

.box:hover:after {
 opacity: 1
}
```

That's certainly a lot of CSS to achieve the same effect as simply animating `box-shadow`, just with improved performance. Why bother?

Even if your desktop likely handles animating `box-shadow` without any issues, your phone may not, and even your desktop may start to stutter when animating a more complex layout.

Keep transitions and animations to only `transform` and `opacity`, and you're certain to achieve the best possible performance, and with that, the best possible user experience.
