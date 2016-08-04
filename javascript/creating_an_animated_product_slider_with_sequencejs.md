# Creating an Animated Product Slider with Sequence.js

[Original URL](https://davidwalsh.name/creating-animated-product-slider-sequencejs)

> In this tutorial, we're going to create a simple animated content slider with Sequence.js. We'll base our demo on the Sequence.js Theme, Mono. For the sake of brevity, our demo won't include...

In this tutorial, we're going to create a simple animated content slider with [Sequence.js](http://www.sequencejs.com/).

We'll base our demo on the Sequence.js Theme, [Mono](http://www.sequencejs.com/themes/mono). For the sake of brevity, our demo won't include everything that Mono does but you'll learn all of the Sequence.js key concepts, as well as having a good base to work from and start creating your own unique animated applications.

Take a look at the [demo here](http://codepen.io/IanLunn/pen/c9e3b2523c8a7d0ce4072c36d3150c42), as well as some of the applications we've built in the [Sequence.js Theme Store](http://www.sequencejs.com/themes/).

See the Pen [Sequence.js - Mono](http://codepen.io/IanLunn/pen/qObKVR/) by Ian Lunn ([@IanLunn](http://codepen.io/IanLunn)) on [CodePen](http://codepen.io).

## What is Sequence.js

Sequence.js provides all of the functionality needed for animated step-based applications such as sliders and presentations. Its built-in CSS framework allows you to animate content as steps become active and inactive using simple _in_ and _out_ class names.

By describing content transitions in CSS, you can rely on hardware acceleration for smooth animation and media queries to make a responsive application using a workflow you are already familiar with.

Sequence.js is supported in modern browsers with a traditional side-to-side fallback theme for Internet Explorer 8 and 9.

Sequence.js can be used free for personal use or with the purchase of a [license](http://www.sequencejs.com/licenses/) when used commercially.

## Project Setup

To start, [download the demo files here](https://github.com/IanLunn/sequencejs-tutorial-mono/archive/master.zip) and unzip into your preferred location. These are the starters files that we'll add this tutorial's code to.

The zip contains the following folders and files:

- `css/demo.css` - A stylesheet for our demo
- `images` - Contains the images we'll add shortly
- `scripts` - Contains the scripts required for Sequence.js
- `index.html` - The HTML page
- `CREDITS.md` - Credits for some of the third-party assets we'll use

_If you'd rather download the very latest version of Sequence.js, head to the [download page](http://www.sequencejs.com/download/) where you can download a zip or find instructions for installing via NPM or Bower._

Open `index.html` and you'll find the following basic HTML template:

```
<!DOCTYPE html>
<html lang="en">
<head>
 <meta charset="utf-8">
 <meta name="viewport" content="width=device-width, initial-scale=1.0">
 <title>Sequence.js - Getting Started</title>
 <link href="css/demo.css" rel="stylesheet" media="all">
</head>
<body>

</body>
</html>
```

Here I've added a page title and referenced the style sheet `css/demo.css`. I've also added a `<meta name="viewport">` tag that tells the browser to make the viewport the same width as the device, with a scale of 100%; this sets up the page ready to be responsive across a wide range of devices.

## Adding Content and Structure

Let's begin adding the basic Sequence.js structure and some content to our page.

In `index.html` find the `<body>` element and inside it add the following:

```
<div id="sequence" class="seq">
 <ul class="seq-canvas">
 <li class="step1">
 <img class="feature" src="images/bananas.png" alt="A cartoon illustration of a bunch of bananas" width="389" height="300" />
 <h2 class="title">Bananas</h2>
 </li>
 <li class="step2">
 <img class="feature" src="images/coconut.png" alt="A cartoon illustration of half a coconut" width="325" height="300" />
 <h2 class="title">Coconut</h2>
 </li>
 <li class="step3">
 <img class="feature" src="images/orange.png" alt="A cartoon illustration of a round orange" width="350" height="300" />
 <h2 class="title">Orange</h2>
 </li>
 </ul>
</div>
```

In the above code, we've added the Sequence.js basic structure and some content; an image and a title. Let's ignore the content and look at the Sequence.js structure.

```
<div id="sequence" class="seq">
 <ul class="seq-canvas">
 <li class="step1">
 ...
 </li>
 <li class="step2">
 ...
 </li>
 <li class="step3">
 ...
 </li>
 </ul>
</div>
```

When adding Sequence.js to a page, it must be made up of a _container_, _canvas_, and _steps_.

The `<div id="sequence" class="seq">` element acts as the container. We've given it an ID which we'll use to attach the Sequence.js script to shortly. The `seq` class will be used to apply styles. Inside the container is the canvas element and steps. Any other elements such as navigation can also be placed inside the container.

The canvas `<ul class="seq-canvas">` holds the steps. This can be any element providing it has the class `seq-canvas`. The canvas can be automatically animated by Sequence.js so that the active step is always in view. We'll experiment with this shortly.

Finally, the `<li>` elements break up our content into steps. You might like to think of steps as slides in a presentation for example – we call them steps in Sequence.js because they do a whole lot more than just slide!

As with the canvas, the steps can be any element you wish, providing they are the immediate descendants of the canvas. So you could use a canvas/steps structure such as:

```
<section class="seq-canvas">
 <article>
 ...Step 1...
 </article>
 <article>
 ...Step 2...
 </article>
 <article>
 ...Step 3...
 </article>
</section>
```

The choice is yours.

_Note: I've given the steps class names `step1`, `step2`, and so on. This isn't required but we will use it in our demo to make the steps a different color._

Inside a step is where content is placed. You're not limited to the content or HTML elements you can use.

## Adding Sequence.js

Now the content and structure is in place, we need to add the Sequence.js scripts.

Before the closing `</body>` in the HTML, add the following scripts:

```
<script src="scripts/imagesloaded.pkgd.min.js"></script>
<script src="scripts/hammer.min.js"></script>
<script src="scripts/sequence.min.js"></script>
<script src="scripts/demo.js"></script>
```

The scripts are as follows:

- [imagesLoaded](http://imagesloaded.desandro.com/) - Third-party providing preloader functionality for Sequence.js
- [Hammer.js](http://hammerjs.github.io/) - Third-party providing touch functionality for Sequence.js
- sequence.min.js - The main Sequence.js script that provides the animation framework and other functionality
- demo.js - A Sequence.js options file for our demo

The third-party scripts are optional and only need be added if you want to use preloader and touch support. The options file is where we'll attach Sequence.js to our HTML, change options, and if you wish, add custom functionality.

Open `scripts/demo.js` and add the following:

```
var sequenceElement = document.getElementById("sequence");

var options = {

}

var mySequence = sequence(sequenceElement, options);
```

This is the basic code required to initiate Sequence.js on a webpage. The first line gets the element we want to have Sequence.js applied to (the container element with ID of `sequence`), the second changes Sequence.js options, and the third initiates Sequence.js on our chosen element.

It is a good idea to enable the [keyNavigation](http://www.sequencejs.com/documentation/#keynavigation) option when developing with Sequence.js so you can navigate between slides via your keyboards left/right arrows. To do this, add the following to the `options` object:

```
var options = {
 keyNavigation: true
}
```

Save the file.

## Container Layout

Now our structure and content are in place, let's add some basic styles.

In `css/demo.css`, add the following:

```
body,
html,
.seq {

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #1 */
 width: 100%;
 height: 100%;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #2 */
 margin: 0;
 padding: 0;
}

.seq {

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #1 */
 position: relative;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #2 */
 overflow: hidden;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #3 */
 font-family: sans-serif;
}

.seq-canvas,
.seq-canvas > articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii {

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #1 */
 height: 100%;
 width: 100%;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #2 */
 margin: 0;
 padding: 0;
 list-style: none;
}
```

The `body, html, .seq` elements:

1. Are made fullscreen via `width` and `height`
2. Have their `margin` and `padding` reset for better browser consistency

The `.seq` container element:

1. Is positioned so all absolute elements within are relative to it
2. Prevents any elements inside of it from showing outside should they overflow
3. Is given a basic font

The canvas `.seq.canvas` and steps `.seq-canvas > *`:

1. Are made fullscreen via `width` and `height`
2. Have some styles reset including removing the disc applied to `<li>` elements by default

These are the basic styles we'll use for our demo and we'll make it look nice later. For the most part, they're the styles you can use for starting out with any Sequence.js application. Our demo is going to take up the full browser window so if you plan on adding a Sequence.js application to a content webpage, you probably don't want to do this. Instead you could remove the `.body, .html, .seq` ruleset entirely and apply `width`/`height` properties to the `.seq` ruleset instead. For example, the following would make the container full width but with a defined height:

```
.seq {
 position: relative;
 overflow: hidden;
 width: 100%;
 height: 600px;
 font-family: sans-serif;
}
```

You could use `height: 90vh` to make the container 90% of the viewport height, use a [CSS technique to maintain aspect ratio](https://css-tricks.com/snippets/sass/maintain-aspect-ratio-mixin/) (we use this for our official [Sequence.js themes](http://www.sequencejs.com/themes/)), and so on. There are few restrictions when it comes to using CSS with Sequence.js. The choice is yours.

## Canvas Layout and Animation

_We're not going to use canvas animation for this tutorial but the following is included to demonstrate how easy it is to automatically animate the canvas in Sequence.js. If you want to skip ahead, continue reading and following instructions from [No Canvas Animation](https://davidwalsh.name/creating-animated-product-slider-sequencejs#no-canvas-animation-steps-in-the-same-place)._

![The canvas being automatically animated bottom-to-top](https://davidwalsh.name/demo/sequence-js-images/canvas-animation-default.gif)

Let's preview what we have so far in a browser. If everything is set up correctly, you can press your keyboard's left/right arrow keys and Sequence.js will automatically animate the canvas so the active step always appears in the container.

By default, the [`canvasAnimation`](http://www.sequencejs.com/documentation/#canvas-animation) option in Sequence.js is `true`. With this option enabled, Sequence.js will automatically animate the canvas by getting a step's offset position and moving the canvas so that the active step's top left corner meets the container's top left corner. This means you can position your steps top-to-bottom, side-by-side, or in a custom layout and the canvas will automatically move to show the active step in the container.

_Note: CSS transforms do not effect an element's offset position so it is best to layout steps via `position` and `top`, `bottom`, `left`, `right` properties. Sequence.js is smart enough to be able to take these property values and animate the canvas using a CSS transform which has better performance when animated._

_Note 2: The speed at which the canvas animates can be changed by specifying a millisecond value for the [`animateCanvasDuration`](http://www.sequencejs.com/documentation/#animatecanvasduration) option._

Let's experiment with the layout to see automatic canvas animation in progress.

We made each step take up the full width and height of a container. Because of this, the browser by default wraps them so they appear below each other and the canvas animates up and down to show each active step.

To make the steps sit side-by-side instead, you could use the following:

```
.seq-canvas {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #1 */
 white-space: nowrap;
}

.seq-canvas > articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #1 */
 display: inline-block;
 vertical-align: top;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #2 */
 white-space: normal;
}
```

In the `.seq-canvas` ruleset the canvas is prevented from wrapping elements (pushing them onto a new line).

The `.seq-canvas > *` ruleset:

1. Makes the steps sit side-by-side now the canvas will let them do it
2. Resets the white-space back to normal so the steps don't inherit `nowrap` from the canvas

![The canvas animating side-to-side](https://davidwalsh.name/demo/sequence-js-images/canvas-animation-side-by-side.gif)

### Custom Layout

How about a custom layout? Try the following:

```
.seq-canvas > articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii {
 position: absolute;
}

.step2 {
 top: 2000px;
 left: 2000px;
}

.step3 {
 top: 4000px;
 left: 4000px;
}
```

Here the steps are positioned `absolute` and then given `top` and `left` properties to position them wherever we please.

![Canvas animation - custom](https://davidwalsh.name/demo/sequence-js-images/canvas-animation-custom.gif)

### No Canvas Animation (Steps in the Same Place)

For this tutorial we won't actually use canvas animation, instead we'll just animate content (which we'll look at shortly). To disable canvas animation, place all of the steps in the same place by adding the following to `css/demo.css`:

```
.seq-canvas > articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #1 */
 position: absolute;
}
```

Because Sequence.js knows the steps are all in the same place now, it won't animate the canvas. As good practice though, it's worth turning off canvas animation in the options too. Update the `options` object in `scripts/demo.js` like so:

```
options = {
 keyNavigation: true,
 animateCanvas: false
}
```

![The steps positioned in the same place](https://davidwalsh.name/demo/sequence-js-images/no-canvas-animation.png)

The application will look pretty bad at this point. As we've now positioned all of the steps in the same place, the content from each step is overlapping. In Animating Content, we'll fix this by hiding content in inactive steps and only showing content when its step is active.

## Adding Content Styles

Now we've looked at what's possible with canvas animation, let's improve the look of our demo before moving onto Animating Content.

When a step becomes active, the container element `<div id="sequence" class="seq">` is given a class name "seq-stepX", the X being replaced by the step number, for example `.seq-step1`.

To change the background color each time the step changes, add at the bottom of `css/demo.css`:

```
.seq-step1 {
 background-color: #2A93BC;
}

.seq-step2 {
 background-color: #6BC85E;
}

.seq-step3 {
 background-color: #45367E;
}
```

How about we animate that too? Update the `.seq` ruleset to the following:

```
.seq {

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #1 */
 position: relative;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #2 */
 overflow: hidden;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #3 */
 font-family: sans-serif;
 color: white;
 text-align: center;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #4 */
 background-color: #2A93BC;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #5 */
 transition-duration: .5s;
 transition-property: background-color;
}
```

_Note: For brevity we won't use browser prefixes in this tutorial for elements such as `transition-duration` but at the time of writing, you should use the -webkit- prefix to make the demo work in Safari and iOS browsers._

Here we've:

1. Set the text color to white and center aligned it
2. Made the background color initially the same color as step 1 (because the application starts on step 1)
3. Applied a half second transition to the background color

![Animating the background color](https://davidwalsh.name/demo/sequence-js-images/background-animation.gif)

When you navigate through the application now, the background color will transition to a new color with each step.

Our content is still overlapping but this nicely demonstrates another feature of Sequence.js. By default, its [`moveActiveStepToTop`](http://www.sequencejs.com/documentation/#moveactivesteptotop) option is enabled, which gives the active step a higher `z-index` value than the rest so it always appears on top.

We want the application to be responsive so it nicely fits on the page regardless of the browser width. At the bottom of `css/demo.css`, add the following:

```
.feature {
 width: 70%;
 max-width: 100%;
 height: auto;
}

@media only screen and (min-width: 460px) and (min-height: 520px) {
 .feature {
 width: 100%;
 }
}
```

This will make the images responsive; filling their container by 70% on devices less than 460px wide and 520px high, and 100% on bigger devices, but never breaking aspect ratio or exceeding the images original width.

_Note: This isn't a perfect solution. In the final demo, the pagination overlaps the image when the browser height is less than 360px for example. In the Mono theme that this demo is based on, I used multiple media queries and based the images height on the viewport via the `vh` unit where appropriate. To keep this tutorial simple, we won't cover that here._

Let's also add some whitespace around the images by updating the `.seq-canvas > *` ruleset as follows:

```
.seq-canvas > articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #1 */
 position: absolute;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #2 */
 padding: 32px;
 box-sizing: border-box;
}
```

This adds a 32px space around the edge of each step. However, because in another ruleset we set the steps to have 100% width, the browser sees this as being 100% plus 64px (32px either side), which makes the content overflow the window and no longer centered. The `box-sizing` property tells the browser to include padding in its calculation of width to work around this.

![Making the application responsive](https://davidwalsh.name/demo/sequence-js-images/responsive.png)

Now finally, let's vertically align the content. As you may know, vertically centering in CSS is a little painful. You can use whatever method you prefer but as I'd like this demo to support Internet Explorer 8+, I'll use the "Ghost Element" technique as explained in [Centering in the Unknown](https://css-tricks.com/centering-in-the-unknown/).

This requires wrapping our content in a `<div>`, so in `index.html` change each step's HTML so they now appear like this:

```
<li class="step1 valign">
 <div class="vcenter">
 <img class="feature" src="images/bananas.png" alt="A cartoon illustration of a bunch of bananas" width="389" height="300" />
 <h2 class="title">Bananas</h2>
 </div>
</li>
<li class="step2 valign">
 <div class="vcenter">
 <img class="feature" src="images/coconut.png" alt="A cartoon illustration of half a coconut" width="325" height="300" />
 <h2 class="title">Coconut</h2>
 </div>
</li>
<li class="step3 valign">
 <div class="vcenter">
 <img class="feature" src="images/orange.png" alt="A cartoon illustration of a round orange" width="350" height="300" />
 <h2 class="title">Orange</h2>
 </div>
</li>
```

Save the file, then to `css/demo.css`, add the following:

```
.valign:before {
 content: "";
 height: 100%;
}

.valign:before,
.valign > .vcenter {
 display: inline-block;
 vertical-align: middle;
}

.valign {
 font-size: 0;
}

.valign > .vcenter {
 font-size: 16px;
}
```

![Content vertically and horizontally centered](https://davidwalsh.name/demo/sequence-js-images/content-vertically-aligned.png)

The content at the moment still looks broken but your application should now look like the above.

## Animating Content

Sequence.js is most powerful when animating content. Content can be animated using CSS transitions and the classes `seq-in` and `seq-out` to describe the _in_ and _out_ positions for content when a step becomes active and inactive.

To demonstrate this, when a step becomes active, let's make the title of each step go from transparent in its _start_ position, opaque in its _in_ position, and then back to transparent in its _out_ position.

At the bottom of `css/demo.css` add the following:

```
.title {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #1 */
 margin: 0;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #2 */
 opacity: 0;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #3 */
 transition-duration: .5s;
 transition-property: opacity;
}
```

This is considered the _start_ position for an element. It has no specific CSS class unlike `seq-in` and `seq-out` because it is the default position.

We've specified:

1. The title should have no `margin` (a cosmetic change)
2. The title should be transparent and if that changes,
3. Transition over a half second period

Now, below this ruleset, add:

```
.seq-in .title {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #1 */
 opacity: 1;
}
```

When a step becomes active, Sequence.js will give it the class `seq-in` and the title element will transition from `opacity: 0` to `opacity: 1` over a half second period.

![Animating content in](https://davidwalsh.name/demo/sequence-js-images/animating-content-in.gif)

Try it in the browser. Each time you hit the right arrow on your keyboard, the next step becomes active and the title fades in.

When the active step is navigated away from and is no longer active, the `seq-in` class is replaced with `seq-out`. We could use this to make the content element fade out but actually that is already occurring. As the `seq-in` class is removed when the step becomes inactive, the default styles from the _start_ position are applied again.

Use the `seq-out` class when you'd like to apply a different animation for the transition to the _out_ position or maintain the same style as applied via the `seq-in` class.

Let's get a little more advanced. Update the `.title` ruleset with the following:

```
.title {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #1 */
 margin: 0;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #2 */
 opacity: 0;
 transform: translateX(50px) translateZ(0);

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #3 */
 transition-duration: .5s;
 transition-property: opacity, transform;
}
```

We've updated the transition so now the title starts 50px to the right of its original position and when this changes, will also cause a transition along with the opacity.

_Note: We're making use of the translateZ(0) trick to cause the browser to use [hardware acceleration](http://www.html5rocks.com/en/tutorials/speed/high-performance-animations/) for the transition. This makes animations smoother, especially on mobile devices._

Now update the _in_ position:

```
.seq-in .title {
 opacity: 1;
 transform: translateX(0) translateZ(0);
}
```

Take a look in the browser again...

![Animating multiple properties on content](https://davidwalsh.name/demo/sequence-js-images/animating-content-multiple-properties.gif)

When a step becomes active the title now fades and floats into position from the side. When a step becomes inactive, the title returns to its _start_ position but that kinda looks weird right? Here's where we can use the `seq-out` class to make the title move to the left so navigating through our application seems more like a natural progression.

Add the following:

```
.seq-out .title {
 opacity: 0;
 transform: translateX(-50px) translateZ(0);
}
```

![Animating content out](https://davidwalsh.name/demo/sequence-js-images/animating-content-out.gif)

Now when a step becomes inactive the title animates to the left instead.

Let's apply the same technique to the images. Add the following to `css/demo.css`:

```
.feature {
 transform: translateZ(0) scale(0);
 transition-duration: .5s;
 transition-property: transform, opacity;
}

.seq-in .feature {
 transform: translateZ(0) scale(1);
}

.seq-out .feature {
 transform: translateZ(0) scale(1);
 opacity: 0;
}
```

Save the file.

With these rules we achieve the following:

1. Scale the image down to nothing (so it can't be seen) in the _start_ position and make transitions occur over half a second
2. Scale the image to full size in the _in_ position
3. Maintain the full size scale but fade-out in the _out_ position

![Animating content complete](https://davidwalsh.name/demo/sequence-js-images/animating-content-complete.gif)

Now the images will have a zoom-in effect when their step becomes active and fade out when they become inactive.

Although what I've shown here is very basic, this simple animation framework provided by Sequence.js is incredibly powerful. Animate multiple properties and elements in 2D and 3D space, make them move, fade, scale, rotate, and more. What you can achieve is only limited by your imagination.

Take a look at some of the examples we've created in the [Sequence.js Theme Store](http://www.sequencejs.com/themes/).

Hopefully you're excited to try out some of your own ideas at this point. Feel free to experiment but continue reading to learn more about Sequence.js, and in particular, the most important concept to make sure your application works fully, Watching Animated Elements.

### Watching Animated Elements

An important concept to learn when using Sequence.js is [_watching_ animated content](http://www.sequencejs.com/documentation/#watch-elements).

As transitions are specified via CSS, Sequence.js can't be sure which elements will be animating and when they'll end. We need to let it know which elements it should watch.

Watching elements is required for many Sequence.js features to work such as [autoPlay](http://www.sequencejs.com/documentation/#autoplay), [phaseThreshold](http://www.sequencejs.com/documentation/#phasethreshold) (a customizable delay between the _in_ and _out_ transitions), [reverseWhenNavigatingBackwards](http://www.sequencejs.com/documentation/#reversewhennavigatingbackwards), and more. For example, when autoPlay is enabled Sequence.js will automatically navigate to the next step once the current step has finished animating after a period of X milliseconds (by default 5000ms/5 seconds as defined by the [autoPlayInterval option](http://www.sequencejs.com/documentation/#autoplayinterval)). Without watching elements, Sequence.js will navigate to the next slide every 5 seconds regardless of the length of animations. So, if your animations last for longer than 5 seconds, the next step will be navigated to before the current one has even finished.

Watching elements is as simple as adding the attribute `data-seq` to the element(s) we want to watch.

_Tip: It isn't always necessary to watch elements. For example, you might want content to transition in and out over 1 second, but have a background image slowly transition for much longer and not prevent Sequence.js from automatically navigating to the next step via autoPlay. The general rule of thumb is that if the transition is applied to content you should watch it. If it's just a decorative transition, you don't need to._

Now we have our content transitions in place, let's watch the elements they're applied to.

In `index.html`, find each instance of the `<img class="feature" />` and `<h2 class="title">` elements, and add the `data-seq` attribute to them like so:

```
<img data-seq src="images/bananas.png" alt="A cartoon illustration of a bunch of bananas" width="389" height="300" />
```

and:

```
<h2 data-seq class="title">
```

With these elements now watched (6 in total; 2 for each step), when we navigate to a step, Sequence.js will be able to determine how long they transition for. If autoPlay is enabled in this scenario for example, when the step becomes active, Sequence.js will wait half a second for the longest transition to finish before starting its 5 second countdown for autoPlay to automatically navigate to the next step.

![Showing the phaseThreshold between steps when elements are watched](https://davidwalsh.name/demo/sequence-js-images/demonstrating-phasethreshold-true.gif)

Save what you have so far and preview in the browser again. Notice that the next step now takes longer to animate in. We've told Sequence.js it should watch the image and title elements so it is waiting for the current step's transitions to end before starting the next. This threshold between the _in_ and _out_ phases is known as the Phase Threshold and can be changed via the [phaseThreshold option](http://www.sequencejs.com/documentation/#phasethreshold).

### Handling Phase Threshold

The phase threshold is the duration between the current step animating out and the next step animating in. By default, the `phaseThreshold` option is set to `true`, meaning the next step will animate in only once the current one has animated out (providing watched elements are correctly set up). Instead, let's make both phases (the _in_ and _out_ transitions) occur at the same time.

In `scripts/demo.js`, in the `options` object, add `phaseThreshold: false`, like so:

```
var options = {
 keyNavigation: true,
 animateCanvas: false,
 phaseThreshold: false
}
```

The current step will now animate out at the same time the next step will animate in. This achieves the same as what we had prior to watching elements because Sequence.js couldn't determine how long the transitions would last and just started both the _in_ and _out_ phases at the same time.

The `phaseThreshold` option can also be given a custom time in milliseconds, for example `phaseThreshold: 250` will delay the next step from animating in for 250 milliseconds.

### Animating in Reverse

When navigating in reverse (hitting your keyboard's left arrow and going from step 3, to step 2, and so on), the current experience doesn't quite feel natural because our transitions are still playing forwards. Sequence.js can automatically reverse transitions via the [`reverseWhenNavigatingBackwards` option](http://sequencejs.com/documentation/#reversewhennavigatingbackwards).

In the options file `scripts/demo.js`, enable `reverseWhenNavigatingBackwards` like so:

```
var options = {
 keyNavigation: true,
 animateCanvas: false,
 phaseThreshold: false,
 reverseWhenNavigatingBackwards: true
}
```

With this option enabled, you can see our transitions now work in reverse automatically. The titles slide in from left-to-right and the images fade in, then zoom out.

## Adding Next/Previous and Pagination Buttons

During development, we've been using the keyboard's left/right arrow keys to navigate between steps. The user might not know to do this though so instead, let's add some user interface buttons. This is super easy with Sequence.js.

In `index.html`, after the `<ul class="seq-canvas"></ul>` element but still inside the container, add the following:

```
<fieldset class="nav" aria-label="Slider buttons" aria-controls="sequence">

 <button type="button" class="seq-prev" aria-label="Previous"><img src="images/prev.svg" alt="Previous" /></button>

 <!-- Pagination goes here -->

 <button type="button" class="seq-next" aria-label="Next"><img src="images/next.svg" alt="Next" /></button>
</fieldset>
```

Now add the following to `css/demo.css` to style the buttons:

```
.nav {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #1 */
 position: absolute;
 z-index: 100;
 left: 0;
 right: 0;
 bottom: 2.5em;
 max-width: 640px;
 width: 100%;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #2 */
 border: none;
 margin: 0 auto;
 padding: 0;
}

.seq-next,
.seq-prev {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #1 */
 padding: 0;
 background: transparent;
 border: none;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #2 */
 padding: .75em;
 cursor: pointer;
 color: white;
 font-size: .75em;
 text-transform: uppercase;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #3 */
 opacity: .7;
 transition-duration: .25s;
 transition-property: opacity;
}

.seq-next:hover,
.seq-prev:hover {
 opacity: 1;
}
```

The `.nav` ruleset:

1. Positions the element holding the buttons 40px from the bottom
2. Resets styles on the `<fieldset>` element

The `.seq-next, .seq-prev` ruleset:

1. Resets the styles
2. Adds some general styles such as font-size and color
3. Sets up the `opacity` so that it will transition from 70% transparency to opaque when hovered over

Save your HTML and preview in the browser. The buttons now allow you to navigate to the next/previous step. By giving an element inside a Sequence.js container the class `seq-prev` or `seq-next`, you're letting Sequence.js know they should act as previous and/or next buttons. The functionality required for them to work is taken care of, so you needn't worry about the JavaScript.

Using the class `seq-pagination`, you can also add pagination links to an application.

Back in `index.html`, find the line `<!-- Pagination goes here -->` and replace it with the following example HTML for pagination:

```
<ul role="navigation" aria-label="Pagination" class="seq-pagination">
 <li class="seq-current">
 <a href="#step1" rel="step1" title="Go to bananas">
 <img src="images/[email protected]" alt="Bananas" width="50" height="40" />
 </a>
 </li>
 <li>
 <a href="#step2" rel="step2" title="Go to coconut">
 <img src="images/[email protected]" alt="Coconut" width="50" height="40" />
 </a>
 </li>
 <li>
 <a href="#step3" rel="step3" title="Go to orange">
 <img src="images/[email protected]" alt="Orange" width="50" height="40" />
 </a>
 </li>
</ul>
```

The pagination must consist of a container and links; links being the immediate descendant of the container. The first immediate descendant will link to the first step, and so on. As with the Sequence.js container, canvas, and steps, the markup you use here is entirely up to you. Providing you add the class of `seq-pagination` to an element that has immediate descendants, you've got pagination!

Save `index.html` then to `css/demo.css` add the following:

```
.seq-next,
.seq-prev,
.seq-pagination {
 position: relative;
 display: inline-block;
 vertical-align: middle;
 margin-top: 0;
}

.seq-pagination {
 margin: 0 1em;
 padding: 0;
}

.seq-pagination li {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #1 */
 position: relative;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #2 */
 list-style: none;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #3 */
 display: inline-block;
 vertical-align: middle;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #4 */
 width: 50px;
 height: 40px;
 margin: 0 .25em;
}

.seq-pagination li:before {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #1 */
 content: "";
 display: block;
 position: absolute;
 top: 100%;
 left: 50%;
 width: 6px;
 height: 6px;
 margin-left: -3px;
 margin-top: .5em;
 border-radius: 50%;
 background: white;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #2 */
 opacity: 0;
 transition-duration: .25s;
 transition-property: opacity;
}

.seq-pagination .seq-current:before {
 opacity: .7;
}

.seq-pagination img,
.seq-pagination a {
 display: block;
 border: none;
}
```

The `.seq-next, .seq-prev, .seq-pagination` ruleset makes the next/previous buttons and pagination sit side-by-side.

The pagination is given a little space either side in the `.seq-pagination` ruleset.

The `.seq-pagination li` ruleset achieves the following:

1. Position the links so a dot that indicates the active step can be positioned relative to it
2. Removes the disc from the `<li>` element
3. Makes each link sit side-by-side
4. Gives the links dimensions and space

We then add the current link indicator via a pseudo-element in the `.seq-pagination li:before` ruleset, which does the following:

1. Positions the dot underneath the image acting as a pagination link
2. Makes the dot transparent but sets up a transition so that when a link becomes active, the dot fades in

After that we use the `.seq-current` class. This is added by Sequence.js to any pagination link that is associated with the current step so it can be styled. In our case, we used the ruleset `.seq-pagination .seq-current:before` to make our dot appear 70% opaque.

In the ruleset `.seq-pagination img, .seq-pagination a`, we reset styles on the pagination image and link.

Now finally, because our content is currently vertically aligned to the canvas, in relation to the new pagination it seems a little off. Let's instead vertically center the content in the space between the top of the page and the top of the pagination, by updating `.seq-canvas > *`:

```
.seq-canvas > articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #1 */
 position: absolute;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #2 */
 padding: 32px;
 box-sizing: border-box;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old #3 */
 height: auto;
 top: 0;
 bottom: 80px;

}
```

The final block of code (3) removes the height of `100%` that we previously set on the steps, and replaces it with `top: 0` and `bottom: 80px`. Now each step's height will be from the very top of the canvas but 80px from the bottom, leaving enough space for the pagination. We get the value of `80px` because the pagination is positioned 40px from the bottom and is also 40px tall.

![Adding pagination to control the slider](https://davidwalsh.name/demo/sequence-js-images/adding-pagination.gif)

Pretty easy to add these important user interface elements right? Now a user can easily navigate and skip between steps, knows at all times which slide they're on, and how many make up the application.

## Browser Support

We're almost done with our application but as is always important, we need to make sure it works cross-browser.

Firstly, you may notice that the pagination wraps onto a new line if you reduce the browser width below 300px or the pagination overlaps the title when the browser height is less than 350px. We won't do it in this tutorial for the sake of brevity but as you'll see in the official [Mono theme](http:///www.sequencejs.com/themes/mono/), I used media queries to solve these issues.

I will however show you how to make the application usable when JavaScript is disabled, as well as in browsers that don't support CSS transitions, such as Internet Explorer 8 and 9.

### Internet Explorer 8 & 9

Internet Explorer 8 and 9, being older browsers, don't support CSS transitions. To work around this, Sequence.js goes into [Fallback Mode](http://www.sequencejs.com/documentation/#fallback-mode).

In Fallback Mode, all steps are given the class `seq-in` to force the content into its active position. Then, rather than the content animating, the canvas is animated side-to-side much like a traditional slider.

![Sequence.js in Fallback Mode](https://davidwalsh.name/demo/sequence-js-images/fallback-mode.gif)

When it is necessary to give these older browsers specific styles, aside from using [conditional comments](http://www.quirksmode.org/css/condcom.html), you can also use the Sequence.js class `seq-fallback` which is applied to the container element when in Fallback Mode.

### No JavaScript Fallback

When JavaScript is disabled the slide content doesn't appear, only the pagination. This is because Sequence.js can't add the `seq-in` class to any of the steps.

A part-solution is to manually add `seq-in` class onto the step you'd like to appear when JavaScript is disabled, like so:

```
<li class="step1 valign seq-in">
```

The first step's content now appears but the pagination is still visible yet not working.

To work around this, we can make use of the `seq-active` class. This is progressively applied to the container element when JavaScript is enabled so when it's not, we can write non-JavaScript styles.

For example, the next/previous buttons are useless without the JavaScript functionality to make them work. We could use the following to differentiate between states where JavaScript is disabled and enabled; hiding/showing those buttons accordingly:

```
.seq-next,
.seq-prev {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Hide buttons when JavaScript is disabled */
 display: none;
}

.seq-active .seq-next,
.seq-active .seq-prev {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Show buttons when JavaScript is enabled */
 display: inline-block;
}
```

We won't go any further than that in this tutorial but for the application we're creating, when JavaScript is disabled we could make the steps and content relative so they wrap one below the other and the user can navigate between each step like they would a traditional web page.

## Conclusion

This tutorial aims to give you a basic understanding of the main features and concepts in Sequence.js. With help of the official [documentation](http://www.sequencejs.com/documentation), you should now be able to start making your very own unique animated applications.

As you've seen, Sequence.js is a progressive solution. It only aims to simplify the design of animated applications by providing all the functionality you may need in a workflow you're already familiar with. How you tackle the issues of meaningful animation, responsive design, accessibility, and user experience is left yo you – which is a good thing!

I'd love to see what you create with Sequence.js so please feel free to share in the comments, via [Twitter](https://www.twitter.com/IanLunn/), [CodePen](http://codepen.io/), and so on.

The complete version of the Mono Sequence.js theme can be [downloaded here](http://www.sequencejs.com/themes/mono/).

Fruit Illustrations made available under a Public Domain license by [OpenClipartVectors](https://pixabay.com/en/users/OpenClipartVectors-30363/).

[Arrow](https://thenounproject.com/search/?q=next&i=38228) icon by [Yordi Uytersprot](https://thenounproject.com/yordi.uytersprot/) from [the Noun Project](https://thenounproject.com/).
