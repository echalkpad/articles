# JavaScript Animation

[Original URL](https://davidwalsh.name/intro-javascript-animation)

> Despite what many developers believe, CSS-based animation is not the only performant way to animate on the web. There's also JavaScript--and JS has some incredible advantages. By abandoning...

Despite what many developers believe, CSS-based animation is not the only performant way to animate on the web. There's also JavaScript--and JS has some incredible advantages.

By _abandoning_ JavaScript for CSS, like many developers do, we are bloating our stylesheets, sacrificing animation timing control, and forgoing physics-based motion.

Don't get me wrong. CSS animations play an important role on the web – both in isolation and in combination with JavaScript animations. In particular, CSS is perfect for simple animations pertaining to basic user interactions, such as hover and focus states.

JavaScript, however, is usually the better option for almost everything else since it's much more versatile.

You might not believe me that JavaScript animations are as fast as CSS animations. You've probably tried animating with jQuery and had your animations jitter and jump, and assumed it was a problem inherent to JavaScript as a language.

**It's not.** Don't confuse the two, JavaScript and jQuery are separate beasts.

jQuery is a library consisting of functions that essentially layer on top of plain JavaScript. jQuery wasn't designed to be a high-performance animation engine. Instead, it was designed to ease the headaches of DOM manipulation--a job it does amazingly well.

Luckily, there are open-source animation libraries that allow you to bypass jQuery to harness more of the raw power of plain JS. With these animation libraries, you can establish a manageable animation workflow and layer on features that drastically improve the motion design on your sites.

I'm going to get you up to speed with the [Velocity.js](http://julian.com/research/velocity/) JavaScript animation engine so that your site's animations can be punchy, smooth, and easy to implement.

## What is Velocity.js?

Velocity is a dedicated animation library. It's simple to use, it's incredibly powerful, it's widely adopted by major companies (including [Tumblr](https://www.tumblr.com/), [Microsoft](http://windows.com) and [WhatsApp](https://www.whatsapp.com/)), and it's free under the [MIT license](http://en.wikipedia.org/wiki/MIT_License).

Velocity mimics the syntax of jQuery--and combines perfectly with it (although it can be used _without jQuery_ as well), so learning it should be no problem for you. Because of jQuery's ubiquity, I'll be showing you Velocity as it's used in combination with jQuery.

Note: For a quick 80/20 of speeding up all your animations, you could simply replace all your current jQuery "animate()" function calls with "velocity()" function calls, since Velocity mirrors the syntax and the basic functionality of jQuery's animate function (while expanding on it). Even with just that quick change, your site can receive a significant performance boost.

But there's also so much more to learn with Velocity. Before we dive into how to specifically use Velocity, let's quickly learn what's possible with it so that you can grasp its value.

Below are the things you can do with JavaScript animation that simply cannot be achieved with CSS-based animation.

## Page Scrolling

A recent trend in web design is to have long, single-page sites where links cause the page to smoothly scroll down to the appropriate page section, rather than navigate to a different page entirely. This is one of the most popular uses for JS animations, as CSS cannot perform scrolling.

In Velocity, you simply run the `velocity` function with the `scroll` command on the element you wish to scroll to:

```
$element.velocity("scroll", 1000);
```

This function causes the browser to scroll down to the selected element (`jQuery` object, `$element`) using a duration of `1000ms`. With jQuery alone, this would be a much more complicated, multi-line function.

Don't let this code overwhelm you if you don't understand what it means; we're going to walk through it slowly after we run through a couple more neat features.

## Animation Reversal

In jQuery, to revert back to the original state before the animation started, you have to manually animate the properties back to their original values. For example:

## With jQuery

```
// Animate a 50px wide element to a width of 100px over 400ms
$element.animate({ width: "100px" }, 400);

// Animate it back to its original width value of 50px over 400ms
$element.animate({ width: "50px" }, 400);
```

In contrast, with Velocity, it's a simple matter of running the reverse command:

```
// Animate a 50px wide element to a width of 100px over 400ms
$element.velocity({ width: "100px" }, 400);
// Reverse previous animation - animate back to a width of 50px over 400ms
$element.velocity("reverse");
```

This will reverse the animation to the selected element's original state prior to the animation--no need to specify the values manually.

## Physics-Based Motion

Real world motion is neither linear, nor is it smooth. It has fast parts, slow parts, friction, and even the concept of weight. With JavaScript based animations, you can mimic real-world physics in your animations, making things seem more natural. Linear motion looks robotic (lifeless) and slightly uncomfortable to watch.

To mimic real-world motion, Velocity offers an easing type based on spring physics, taking a tension value (`default: 500`) and a friction value (`default: 20`) as its parameters (see [Velocity documentation](http://julian.com/research/velocity/) for more information).

```
// Animate to a width of 100px with spring physics over the default 400ms. Spring tension is set to 1000, and friction is set to 50.
$element.velocity({ width: "100px", [ 1000, 40 ]);
```

A high tension value increases total speed and bounciness of the animation. A lower friction value increases the speed at the tail-end of the animation (high friction causes the animation to decelerate faster). Tweaking these values will make each animation unique, realistic, and interesting.

The example above would be very fast and bouncy (high tension), but would decelerate quickly and be slow near the end of the animation (high friction)

## Okay great, so how do I use it?

Enough of hearing about what it _can_ do, let's find out how you can start using it today. As I mentioned earlier, Velocity can be used independently, but combining it with jQuery leads to the quickest results, as jQuery makes selecting elements far easier.

The first step is to download Velocity.js from[here](http://www.velocityjs.org). Then include it in your HTML file in a script tag before the closing body tag of your page. For example:

```
<script src="//code.jquery.com/jquery-2.1.1.min.js"></script>
<script src="//cdn.jsdelivr.net/velocity/1.1.0/velocity.min.js"></script>
```

Note: If you're using jQuery, make sure to include jQuery before Velocity, as Velocity will adapt its API based on whether jQuery is present.

Once included, you can basically start using it in place of jQuery's "animate" function. However, there's several additions that make Velocity much more powerful than that, such as animation reversals, transforms, spring physics, chaining, and color animations (all of which I'll be covering here). But it doesn't end there, check out [the Velocity docs](http://julian.com/research/velocity/) for more info.

Let's learn how to actually use Velocity's syntax!

### Arguments

Velocity accepts one or more arguments. The first argument is the only mandatory argument. It can either be the name of a command (as in the example for "scroll" above), or it can be a data object that maps CSS properties to the animation's final values.

```
// Data object setting the animation final values of CSS properties opacity, and left to 50%, and 500px respectively.
$element.velocity({ opacity: "50%", left: "500px"});
```

The second argument is a data object of animation options, such as duration, easing, delay, and complete (callback function that executes after the animation _completes_).

### Data Object

```
// Animates an element to an opacity of 50% over 200ms after a 200ms delay.
$element.velocity({ opacity: "50%" }, { delay: 200, duration 200 });
```

Alternatively, as a shortcut, you can pass comma-separated values for the duration, easing, and complete function in any order. If, however, you wish to have an additional option, such as delay, you must instead use a data object, and only a data object--you can't mix and match.

### Comma Separated

```
// Animates an element to a left position of 50px with a 500ms duration, an easing of "ease-in-out", and with an alert message firing at the end of the animation.
$element.velocity({ left: 50px }, 500, "ease-in-out", function () { alert(‘done') } );
// Won't work: mixes data object and comma separated values
$element.velocity({ left: "50px" }, 500, "ease-in-out", function () { alert("done") }, { delay: 200 });
```

Note: The second argument is optional because there are default values for the most important properties: `duration: 400ms`, and `easing: "swing"`. All the other animation options are optional.

Also note that you can only pass in a single numeric value per CSS property, so `padding: "10px 15px"` will not work. Instead, you must set each one separately: `{ paddingLeft: "10px", paddingTop: "15px" … }`. Not only is it more legible and explicit, it also means you can specify easing types for each sub-property, rather than having one global easing type, giving you unparalleled customization of your animations.

Note: CSS properties with multiple words (`font-size` and `padding-left`) must be written in camelcase (`fontSize` and `paddingLeft`), not hyphenated.

See the Pen [Velocity.js - Basics: Arguments](http://codepen.io/julianshapiro/pen/BjwtC/) by Julian Shapiro ([@julianshapiro](http://codepen.io/julianshapiro)) on [CodePen](http://codepen.io).

**Property values:**If a unit type is not provided, one is assumed (`ms`, `px`, and `deg` are generally assumed), but being explicit is better so you can discern the unit types at a glance when reading back through your code. If there's anything other than a numerical value, then quotes must be used. For example, `duration: 500;` would be allowed, but `duration: 500px;` would not, quotations would have to be used around `500px`.

JavaScript animation also allows for four value operators to be used: value operators +, -, *, and /, allowing you to add, subtract, multiply, and divide from the current value of the property:

```
$element.velocity({
    width: "+= 50px", // Adds 50px to the current width value
    height: "/= 2" // divides the current height value by two
});
```

Using these shorthand features retains all the animation logic within Velocity's animation engine. This keeps the code concise and legible (removing manual value calculations), and also helps to improve performance by providing Velocity with more information about your animation intentions. The more animation logic contained within Velocity, the better Velocity can optimize the performance of the code.

See the Pen [Velocity.js - Basics: Values](http://codepen.io/julianshapiro/pen/fjbct/) by Julian Shapiro ([@julianshapiro](http://codepen.io/julianshapiro)) on [CodePen](http://codepen.io).

### Chaining

Chaining Velocity calls one after another on the same element queues the animations to execute one after the other:

```
$element
    .velocity({ width: "500px", height: "300px"})
    .velocity({ opacity: 0 });
```

This would cause the element to animate to a width of `500px` and height of `300px`, and after completing that animation, it would fade to an opacity of `0%`. As mentioned previously, this allows you to make complex, multi-stage animations with timing guaranteed to happen exactly as planned--one after another--without any manual calculations.

See the Pen [Velocity.js - Basics: Chaining](http://codepen.io/julianshapiro/pen/hyqlF/) by Julian Shapiro ([@julianshapiro](http://codepen.io/julianshapiro)) on [CodePen](http://codepen.io).

### Easing

I've mentioned the word easing a few times in this article, and maybe you're wondering what that means. Easing's are mathematical functions that determine the speed of the animation at different stages throughout its duration. For example, an "ease-in-out" easing gradually accelerates at the beginning of the animation, and gradually decelerates near the end of the animation. A "linear" easing has a constant speed throughout the duration, and looks very jarring and robotic.

You specify easing with the easing option:

```
// Animate an element to a top position of 100px with an easing of "ease-in-out"
$element.velocity({ top: 100 }, { easing: "ease-in-out" });
```

See the Pen [Velocity.js - Option: Easing (jQuery UI)](http://codepen.io/julianshapiro/pen/bAiIt/) by Julian Shapiro ([@julianshapiro](http://codepen.io/julianshapiro)) on [CodePen](http://codepen.io).

Easing is also how you use spring physics as mentioned previously. You can either use the default values (tension of 500, friction of 20) by passing a value of "spring," or you can specify them manually:

```
// Animates an element to a width of 500px with spring physics with a tension of 250, and a friction of 10 (low tension, low friction).
$element.velocity({ width: 500 }, { easing: [ 250, 10 ] });
```

Easings get much more complicated, but for brevity's sake, I'll stop here. Read Velocity's documentation for more information.

See the Pen [Velocity.js - Option: Easing (Spring Physics)](http://codepen.io/julianshapiro/pen/fgjaF/) by Julian Shapiro ([@julianshapiro](http://codepen.io/julianshapiro)) on [CodePen](http://codepen.io).

### Color

Velocity allows color animations for the following CSS properties: color, backgroundColor, borderColor, and outlineColor. These color properties only accept hex strings as inputs (`#000000` for black, `#3b5998` for Facebook blue). You can however tweak the individual red, green, blue values (0 to 255), and even its opacity with the alpha value (0 to 1). Simply append the color CSS properties' names with "Red," "Green," "Blue," or "Alpha."

```
$element.velocity({
    // Animate borderColor to the hex color for "fogdog" (orange)
    borderColor: "#f06d06",
    // Animate the background to an opacity (alpha) of 80%
    backgroundColorAlpha: 0.8,
    // Animate the blue component of the element's text color to 200
    colorBlue: 200
});
```

See the Pen [Velocity.js - Feature: Colors](http://codepen.io/julianshapiro/pen/wlEtB/) by Julian Shapiro ([@julianshapiro](http://codepen.io/julianshapiro)) on [CodePen](http://codepen.io).

### Scrolling

As mentioned earlier, the scroll command causes the page to scroll to the top edge of the an element. You can pass in the same options as the other animations, as well as chain with other animation calls.

```
$element
    // scroll to the top edge of the element over 1000ms
    .velocity("scroll", { duration: 1000 })
    // fade in after scroll animation
    .velocity({ opacity: 1 });
```

By default, page scrolling occurs on the y-axis. To change to horizontal scrolling, use the axis option:

```
// Scrolls the browser to the left edge of the element
$element.velocity("scroll", { axis: "x" });
```

A common effect is to have a offset from the top edge when scrolling, so the content isn't at the very top of the browser and hard to read. To achieve this, or the opposite effect in Velocity, use the offset option:

```
// Scroll to a position 100px above the element's top edge
$element.velocity("scroll", { duration: 1000, offset: "-100px" });

// Scroll to a position 100px below the element's top edge
$element.velocity("scroll", { duration: 1000, offset: "100px" });
```

See the Pen [Velocity.js - Command: Scroll w/ Container Option](http://codepen.io/julianshapiro/pen/kBuEi/) by Julian Shapiro ([@julianshapiro](http://codepen.io/julianshapiro)) on [CodePen](http://codepen.io).

## Transforms

Want to combine the power of CSS animations and JS animations? Layer in some CSS transforms, allowing you to do 2D and 3D visual manipulations like translation, scaling, and rotation. Note that these do not affect the element's location in the site, nor the locations of elements around it.

Velocity supports the following transforms:

- `translateX`: Move an element along the x-axis (left-right)
- `translateY`: Move an element along the y-axis (up-down)
- `rotateZ`: Rotate an element about the z-axis (clockwise-counterclockwise)
- `rotateX`: Rotate an element about the x-axis (up-down)
- `rotateY`: Rotate an element about the y-axis (left-right)
- `scaleX`: Multiply the width dimension of an element
- `scaleY`: Multiply the height dimension of an element

<!--  -->

```
$element.velocity({
    rotateZ: "90deg", // rotate clockwise 90 degrees
    scaleX: 2.0 // double the width
});
```

See the Pen [Velocity.js - Feature: Transforms](http://codepen.io/julianshapiro/pen/FIwfv/) by Julian Shapiro ([@julianshapiro](http://codepen.io/julianshapiro)) on [CodePen](http://codepen.io).

## Going Forward

Animations breathe life and interactivity into an otherwise static page, and JavaScript-based animations are the best way to add them.

With CSS animations, you're limited in functionality to the infrequent updates of the CSS standard. With JavaScript, there are dozens of new libraries, plugins, and updates being added to the open-source world every single day--expanding the possibilities of your animations through the use of new tools.

Velocity has more features than those demonstrated here, and I encourage you to check out its [documentation](http://julian.com/research/velocity/). Now go experiment! :)

### Bonus!

Final, double bonus: Check out [this tutorial](https://www.youtube.com/watch?v=CdwvR6a39Tg&hd=1) on using Velocity's UI pack for improving user interfaces. And check this [Velocity demo gallery](http://codepen.io/collection/tIjGb/).
