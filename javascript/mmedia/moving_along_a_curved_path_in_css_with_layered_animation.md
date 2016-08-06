# Moving along a curved path in CSS with layered animation

[Original URL](http://tobiasahlin.com/blog/curved-path-animations-in-css/)

> CSS animations and transitions are great for animating something from point A to B. That is, if you want to animate along a straight path. No matter how much you bend your bezier curves, you can't...

CSS animations and transitions are great for animating something from point A to B. That is, if you want to animate along a straight path. No matter how much you bend your [bezier curves](https://developer.mozilla.org/en/docs/Web/CSS/timing-function), you can't make something move along a curved path by applying an `animation` or a `transition` to an object. You can overshoot with custom timing functions, and produce spring-like effects, but the relative movement along the X and Y-axis will always be identical.

However, instead of turning to JavaScript for producing more natural looking motion, there's an easy way to work around this limitation: layered animation. By using two or more objects to drive an animation, we get fine grained control over an object's path, and can apply one timing function for the movement along the X-axis, and another one for the Y-axis.

## The problem

Before we dive into the solution, let's look closer at the problem. CSS `animations` and `transitions` restrict us to animating along straight paths. How? By always taking the shortest path from point A to point B. That's great--this is what we're after in most cases--but we lack a way of telling CSS to "take a nicer path" rather than "take the shortest path".

The most straight-forward way of animating between two points in CSS (with hardware acceleration) is to use `transform` to `translate` an object over time. This produces movement along a linear path. In this `@keyframes` block, we're going back and forth between (0,0) and (100,-100), as seen in the example above:

```
@keyframes straightLine {
 50% {
 transform: translate3D(100px, -100px, 0);
 }
}

.dot {
 animation: straightLine 2.5s infinite linear;
}
```

This isn't complicated, but let's stop here for a moment. To understand the solution to the problem we need to, at least visually, take the animation apart.

At `0%` we start out at (0,0), and at `50%` we use `translate3D(100px, -100px, 0)` to move to (100,-100), then back again. Put another way, we move the object `100px` to the right, and `100px` upwards, and these two translations combined move the object at an angle.

## The solution: one timing function per axis

So how do we create a curved path like the one showcased in the earlier example? To create a path that doesn't go in a straight line, **we want the movement speed along the X-axis and Y-axis to be out of sync**.

The previous examples all used `linear` timing functions, but if we add a container around the object we want to animate, we can apply one timing function for the X-axis, and another one for the Y-axis. Below, we're using `ease-in` for the X-axis, and `ease-out` for the Y-axis:

## The implementation: one object per axis

Unfortunately, we can't just stack `transform` animations: only the last animation to be declared would run. So, how do we actually combine two animations? We put one object inside another, and run one animation on the container element, and a different one on the child element.

In all the examples above where you've seen the dot move along a curved path, you've seen two separate elements being animated, but the container has been completely transparent. To clearly see how the two objects interact in the curved examples, we can change the container to render a bordered box:

The dot sits inside the bordered box, and follows the box's motion along the X-axis, while it itself moves up and down along the Y-axis. Remove the border of the box, and we've got our curved path. Rather than adding two objects in our HTML, though, we can produce a pseudo-element. If we have this in our HTML:

We can add a pseudo-element like so:

```
.dot {
 /* Container. Animate along the X-axis */
}

.dot::after {
 /* Render dot, and animate along Y-axis */
}
```

Then we need two separate animation blocks: one for the X-axis, and one for the Y-axis. Notice how one uses `ease-in`, while the other one uses `ease-out`:

```
.dot {
 /* Some layout code… */
 animation: xAxis 2.5s infinite ease-in;
}

.dot::after {
 /* Render dot */
 animation: yAxis 2.5s infinite ease-out;
}

@keyframes xAxis {
 50% {
 animation-timing-function: ease-in;
 transform: translateX(100px);
 }
}

@keyframes yAxis {
 50% {
 animation-timing-function: ease-out;
 transform: translateY(-100px);
 }
}
```

Put together, with vender-prefixes for WebKit, and with some custom bezier curves instead of `ease-in` and `ease-out`, we can produce the example showcased at the very beginning of the post:

```
.demo-dot {
 -webkit-animation: xAxis 2.5s infinite cubic-bezier(0.02, 0.01, 0.21, 1);
 animation: xAxis 2.5s infinite cubic-bezier(0.02, 0.01, 0.21, 1);
}

.demo-dot::after {
 content: '';
 display: block;
 width: 20px;
 height: 20px;
 border-radius: 20px;
 background-color: #fff;
 -webkit-animation: yAxis 2.5s infinite cubic-bezier(0.3, 0.27, 0.07, 1.64);
 animation: yAxis 2.5s infinite cubic-bezier(0.3, 0.27, 0.07, 1.64);
}

@-webkit-keyframes yAxis {
 50% {
 -webkit-animation-timing-function: cubic-bezier(0.02, 0.01, 0.21, 1);
 animation-timing-function: cubic-bezier(0.02, 0.01, 0.21, 1);
 -webkit-transform: translateY(-100px);
 transform: translateY(-100px);
 }
}

@keyframes yAxis {
 50% {
 -webkit-animation-timing-function: cubic-bezier(0.02, 0.01, 0.21, 1);
 animation-timing-function: cubic-bezier(0.02, 0.01, 0.21, 1);
 -webkit-transform: translateY(-100px);
 transform: translateY(-100px);
 }
}

@-webkit-keyframes xAxis {
 50% {
 -webkit-animation-timing-function: cubic-bezier(0.3, 0.27, 0.07, 1.64);
 animation-timing-function: cubic-bezier(0.3, 0.27, 0.07, 1.64);
 -webkit-transform: translateX(100px);
 transform: translateX(100px);
 }
}

@keyframes xAxis {
 50% {
 -webkit-animation-timing-function: cubic-bezier(0.3, 0.27, 0.07, 1.64);
 animation-timing-function: cubic-bezier(0.3, 0.27, 0.07, 1.64);
 -webkit-transform: translateX(100px);
 transform: translateX(100px);
 }
}
```

Which brings us back to where we started:

You may have noticed that we've been using `@keyframes` blocks in all of the examples so far, but that's purely because we happened to need several keyframes to move the dot back and forth. Layered animation works just as well with the `transition` property, if you only need to animate from point A to point B.

If you have an absolutely positioned element, you can achieve a curved path by animating the `left` and `bottom` properties, of a single element, and avoid the need of a container element. There's a good reason to avoid doing so, however: it performs significantly worse, and will trigger a redraw for every frame of the animation. Using layered animation with a pseudo-element, and animating the hardware accelerated `translate` property, produces a great looking animation while making sure it also performs well.
