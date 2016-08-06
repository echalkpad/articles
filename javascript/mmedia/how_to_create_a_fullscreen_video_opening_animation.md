# How to Create a Fullscreen Video Opening Animation

[Original URL](http://tympanus.net/codrops/2015/09/17/how-to-create-a-fullscreen-video-opening-animation/)

> A tutorial on how to create a video opening animation inspired by the effect seen on momentsapp.com. View demo Download source In t

A tutorial on how to create a video opening animation inspired by the effect seen on momentsapp.com.

![](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/themes/codropstheme03/images/advertisement.jpg)

[![VideoOpeningAnimation_main](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/uploads/2015/07/VideoOpeningAnimation_main.jpg)](http://tympanus.net/Tutorials/VideoOpeningAnimation/)

[View demo](http://tympanus.net/Tutorials/VideoOpeningAnimation/) [Download source](http://tympanus.net/Tutorials/VideoOpeningAnimation/VideoOpeningAnimation.zip)

In this in-depth tutorial we want to show you how to create a similar video opening effect to the one seen on [momentsapp.com](http://momentsapp.com/). If you click the "Watch the video" button on the Moments App page, you will see a new image appearing on the photo stack which contains a video. The animation is a slight rotation of the frame and a subtle scaling of the video, which already starts to play. The whole wrapper expands to full screen without any play controls; just the closing cross will be shown. Once the video ends, or, if the user clicks on the closing cross, the video will simply disappear.

We'll be using many interesting CSS properties, like [CSS animations](http://tympanus.net/codrops/css_reference/animation/), [viewport-percentage lengths](http://tympanus.net/codrops/css_reference/length/#section_viewportlengths), [media queries](http://www.w3.org/TR/css3-mediaqueries/) and the [Flexbox](http://tympanus.net/codrops/css_reference/flexbox/) layout mode, beside others. We'll also dive a bit into HTML5 video and some of its attributes and definitions.

_Note that most of these properties are supported in modern browsers only._

**The images in the demo are by photographer [Herriest](https://pixabay.com/en/users/Herriest-98056/) and the video is from [UnripeContent](http://unripecontent.com/), a great place to find lots of free footage from various categories.**

Let's get down to business and start by planning the effect, write our markup and the JavaScript. Then we'll style everything and set up the animations that will give live to the whole effect.

## Planning, Markup and JavaScript

We want to animate a wrapper that contains a video that also gets animated, so we'll need a nested structure with two wrappers for the video. The only control that we want to have is a close button which will also go into the same wrapper. The video will need to be in MP4 format for minimum support but we'll add multiple sources for support in various browsers. Read more about this topic in the [Wikipedia entry on HTML5 video](https://en.wikipedia.org/wiki/HTML5_video) and Pete LePage's insightful [HTML5 Video](http://www.html5rocks.com/en/tutorials/video/basics/) article on HTML5 Rocks. We've used the handy [CloudConvert service](https://cloudconvert.com/mp4-to-anything) for converting our MP4 video into the required file formats (_WebM_ and _Ogg_).

The _poster_ attribute value allows us to set an image that will be shown before the video loads. With the _preload_ attribute value set to **auto**, we tell the browser that it's desirable to download the entire video optimistically, i.e. with the outlook on accomplishment. The sources for the different video file formats are listed inside the `video` element. When defining a source, we also set the _codec_; a _codec_ encodes audio and video by turning a set of images into a compressed video stream or a set of audio samples into an audio stream and the other way around. The containers for video/audio stream combinations are the familiar file formats like MP4 (combines H.264 with AAC audio) or WebM (VP8 and Vorbis). These also have an identifying MIME type, like "video/webm". For an in-depth explanation, check out [The 'Codecs' and 'Profiles' Parameters for "Bucket" Media Types](http://tools.ietf.org/html/rfc6381).

![Videocodec](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/uploads/2015/07/VideoOpeningAnimation_codec.jpg) Using a program like the VLC media player, you can get useful information about the streams. Let's write the markup:

```
<div class="video-wrap">
    <div class="video-inner">
        <video class="video-player" src="media/woods.mp4" poster="media/woods.jpg" preload="auto">
            <source src="media/woods.webm" type='video/webm; codecs="vp8.0, vorbis"'>
            <source src="media/woods.ogg" type='video/ogg; codecs="theora, vorbis"'>
            <source src="media/woods.mp4" type='video/mp4; codecs="avc1.4D401E, mp4a.40.2"'>
            <p>Sorry, but your browser does not support this video format.</p>
        </video>
        <button class="action action--close">
            <i class="fa fa-close"></i>
            <span class="action__label action__label--hidden">Close preview</span>
        </button>
    </div><!-- /video-inner-->
</div><!-- /video-wrap-->

<div class="content">
    <div class="loader">
        <i class="fa fa-spinner fa-pulse"></i>
    </div>
    <button class="action action--hidden action--play">
        <i class="fa fa-play"></i><span class="action__label">Watch the video</span>
    </button>
</div>
```

We'll also add a play button that will serve as our trigger for the opening animation and playing the video. As you can see, we are using the [Font Awesome icon font](http://fortawesome.github.io/Font-Awesome/) here (class **fa**).

When we click on that play button, we will add the class **video-wrap–show** to the **video-wrap** division. This will allow us to control the effect on a class-basis by using CSS animations and transitions. Once we click on the closing cross, we will remove that class and add **video-wrap–hide** instead. Like that, we can define an alternative closing behavior with a different animation.

Our aim is to control all the effect using mostly CSS; adding the classes and controlling the playing of the video will be our script's job.

So let's sum up what we need to do in order to achieve what we want:

- The video will take some time to load, so we'll need to make sure that we don't allow opening the video if it's not ready yet. We can do that by adding the control class **video-loaded** to the body once the video is loaded. This will allow us to control the visibility of the loader element and the play button.
- When we click on the play button, we want the animation to start. By adding the class **video-wrap–show** to our video wrapper, we trigger the animation, and we start playing the video a bit later.
- When the video reaches the end or when we click on the closing cross, we'll add the class **video-wrap–hide** to play our alternate closing animation.
- We pause the video if it hasn't ended and we set it to the beginning if we open it (again).

Let's write the JavaScript, implementing all the control behavior we have defined before:

```
;( function() {

    'use strict';

    var bodyEl = document.body,
        videoWrap = document.querySelector('.video-wrap'),
        videoEl = videoWrap.querySelector('video'),
        playCtrl = document.querySelector('.action--play'),
        closeCtrl = document.querySelector('.action--close');

    function init() {
        initEvents();
    }

    function initEvents() {
        playCtrl.addEventListener('click', play);
        closeCtrl.addEventListener('click', hide);
        videoEl.addEventListener('canplaythrough', allowPlay);
        videoEl.addEventListener('ended', hide);
    }

    function allowPlay() {
        classie.add(bodyEl, 'video-loaded');
    }

    function play() {
        videoEl.currentTime = 0;
        classie.remove(videoWrap, 'video-wrap--hide');
        classie.add(videoWrap, 'video-wrap--show');
        setTimeout(function() {videoEl.play();}, 600);
    }

    function hide() {
        classie.remove(videoWrap, 'video-wrap--show');
        classie.add(videoWrap, 'video-wrap--hide');
        videoEl.pause();
    }

    init();

})();
```

Quite straightforward, right? :) The beauty of having the functionality class-controlled is that we can now define the behavior in an interconnected way in our stylesheet. This has many advantages when it comes to managing an effect. But it's a matter of preference and requirements; if you have many complex interactions going on, you might be better off defining most or all the behavior (animations, transitions or visibility) in your JavaScript. But we want to dive into CSS today and explore some exciting properties that will hopefully help you with some other challenges during your development work.

![](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/themes/codropstheme03/images/advertisement.jpg)

The effect from the Moments App website is great for examining and utilizing some exciting techniques, so let's dive into the CSS!

## The CSS

Let's bring everything to life with our style definitions. We'll focus on the styles that are important for the effect only, but you can check out the resting styles for the page in the _style.css_ file that is located in the _css_ folder.

Let's start by setting the [box-sizing](http://tympanus.net/codrops/css_reference/box-sizing/) to **border-box** for all elements. This box model is the preferred choice among developers because it has the advantage of allowing to define sizes _including_ borders and paddings:

```
*,
*:after,
*:before {
    -webkit-box-sizing: border-box;
    box-sizing: border-box;
}
```

Let's define the body and link styles:

```
body {
    font-family: 'Avenir Next', 'Helvetica Neue', 'Lato', 'Segoe UI', Helvetica, Arial, sans-serif;
    color: #fff;
    background: #333 url(../media/woods.jpg) no-repeat center center;
    background-size: cover;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
}

a {
    text-decoration: none;
    color: #d9ecc9;
    outline: none;
}

a:hover,
a:focus {
    color: #fff;
}
```

You don't need to set the font-smoothing properties, but we choose to do so here as it makes the fonts crisp on Mac and prevents some jiggling in Firefox.

Next, we need to set the styles for our main page container that wraps everything. Using [viewport-percentage lengths](http://tympanus.net/codrops/css_reference/length/#section_viewportlengths), we set its dimensions to span over the whole viewport and hide any overflow. _How do we want the elements inside of the container to be laid out?_ Since we are using a fixed 100% height, we want the elements to be spread in one column, which is centered on the screen. What a pain this would be without the fantastic [Flexbox](http://tympanus.net/codrops/css_reference/flexbox/) layout mode :) Let's make extensive use of it and define exactly what we want:

```
.container {
    width: 100vw;
    height: 100vh;
    border: 20px solid #fff;
    position: relative;
    overflow: hidden;
    display: -webkit-flex;
    display: flex;
    -webkit-flex-direction: column;
    flex-direction: column;
    -webkit-justify-content: space-around;
    justify-content: space-around;
    -webkit-align-items: center;
    align-items: center;
}
```

We set the display to **flex** and the flex-direction property to **column**. Justifying the content with **space-around** and aligning the items using **center** will give us the elastic layout we want. Try resizing your browser window to see how the elements will be laid out depending on how much space is available.

Let's take care of that main video wrapper. Now, before we start styling away, let's reflect on some possible scenarios and what we need to consider so that things don't break. We need to cover every possible case (or at least the most important ones, leaving out devices like tiny watches). Considering the case of a smartphone or small device, we want to avoid too much fanciness and we have to consider native behavior (which might just throw our entire effect overboard, like it is the case for iOS).

So we want our effect to only animate entirely for larger screens and we don't want to do anything too elaborate for tiny screens (we just want to show the video wrapper). We make use of media queries of which we will have two: one for defining the whole effect for screens bigger than 25em, and one for defining some styles differently according to the aspect ratio of the screen. Our reference aspect ratio is the video itself: with a size of 1280 × 720, we have an aspect ratio of 1280/720, or 16/9\. Since we want to make sure that the video covers the whole screen, we need to distinguish between the "vertical" and the "horizontal" case. One will use the width of the video to cover the screen and one will take the height. But more on that later.

The "default" styles without the main animation effect will be defined first, and then we'll add everything else we need to our media queries.

The main wrapper will be a fixed element that will take up all the width and height available. We'll use flexbox to center the inner container, so let's define the necessary properties:

```
.video-wrap {
    position: fixed;
    z-index: 1000;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    pointer-events: none;
    display: -webkit-flex;
    display: flex;
    -webkit-align-items: center;
    align-items: center;
}
```

We set the z-index value of the element very high so that it's on top of everything else, but we don't want it to be clickable when the inner parts are hidden. Using **pointer-events: none** will make the wrapper "untouchable" and invisible to interaction but be careful when using this, [support is limited](http://caniuse.com/#feat=pointer-events). You can achieve a similar behavior by using _visibility_, though. If you are interested in how that works, check out this short post of our [Fullscreen Overlay Effects](http://tympanus.net/codrops/2014/02/06/fullscreen-overlay-effects/).

When we show the video, we will set the pointer-events to _auto_:

```
.video-wrap--show {
    pointer-events: auto;
}
```

While the main video wrapper serves as a layout container, the **video-inner** division and the video itself are our crucial main players. Let's set the base styles for the **video-inner** div:

```
.video-inner {
    position: relative;
    overflow: hidden;
    width: 100%;
    height: 100%;
    margin: 0 auto;
    opacity: 0;
    background: black;
}
```

Here we set the width and height to 100% because no expansion or fancy effect will happen for the base case of small devices.

```
.video-wrap--show .video-inner {
    opacity: 1;
}
```

Adding our "show" class will simply trigger the visibility of the inner division.

The video element itself will be positioned absolutely and centered by setting the top to 50% and a transform to "pull" it back up half of its own height:

```
.video-player {
    position: absolute;
    top: 50%;
    width: 100%;
    -webkit-transform: translate3d(0,-50%,0);
    transform: translate3d(0,-50%,0);
}
```

These are the base styles that will allow to show the main video wrapper and to look like this for small screens:

![VideoOpeningAnimation_screen_base](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/uploads/2015/07/VideoOpeningAnimation_screen_base.jpg)

Remember, the styles for the main effect will be defined in two media queries later on.

Let's style the loader and action buttons (play and close). The loader and play button will be placed in a division with the class **content** which needs relative positioning. Why? We want the loader to be positioned absolutely in the same place of the play button so that we can switch both with a transition:

```
.content {
    position: relative;
}

/* Loader */
.loader {
    font-size: 2.5em;
    position: absolute;
    top: 50%;
    left: 50%;
    -webkit-transform: translate3d(-50%,-50%,0);
    transform: translate3d(-50%,-50%,0);
}

.video-loaded .loader {
    opacity: 0;
    pointer-events: none;
    -webkit-transition: opacity 0.3s;
    transition: opacity 0.3s;
}
```

The buttons that are actions will get the class **action** and we will define some common styles:

```
.action {
    font-family: 'Avenir Next', 'Helvetica Neue', 'Lato', 'Segoe UI', Helvetica, Arial, sans-serif;
    font-size: 1.15em;
    font-weight: bold;
    position: relative;
    overflow: hidden;
    margin: 0;
    padding: 1em 2em;
    color: #fff;
    border: 2px solid;
    border-radius: 40px;
    background: none;
    -webkit-flex: none;
    flex: none;
}

.action:focus {
    outline: none;
}
```

Setting the flex property value to _none_ will make sure that our button doesn't get squeezed when things get tight.

We remove the outline on focus. _(Note: Yes, it's an absolute sin to do this but I consider it an absolute no-brainer that **you should provide an appropriate focus style for maintaining good accessibility** but when creating demos for Codrops, I want the UI to be impeccable for the sake of being able to concentrate solely on the effect in question; in my opinion, the :focus style breaks this by changing the style on click in a permanent way.)_

The action label or text will be set to inline and when we don't want to show the label but keep it screen reader accessible, we can hide it by setting its position to absolute and the top value to 200%. Note that we need to set the parent's overflow value to _hidden_ for this to work.

```
.action__label {
    display: inline-block;
    margin: 0 0 0 0.75em;
}

.action__label--hidden {
    position: absolute;
    top: 200%;
}
```

The play button will be invisible initially, and then, when the video is loaded and we add the **video-loaded** class to the body, we show it:

```
.action--play {
    display: block;
    margin: 1em auto;
    opacity: 0;
    pointer-events: none;
    -webkit-transition: opacity 0.3s 0.1s;
    transition: opacity 0.3s 0.1s;
}

.video-loaded .action--play {
    opacity: 1;
    pointer-events: auto;
}
```

![VideoOpeningAnimation_screen1](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/uploads/2015/07/VideoOpeningAnimation_screen12.jpg)

The closing button which is placed in our inner video wrapper, will be shown using a transition on its opacity and scale transform. This transition will have a delay when we open the video wrapper, but none when we close it:

```
.action--close {
    line-height: 1;
    position: absolute;
    z-index: 1000;
    top: 30px;
    right: 30px;
    width: 60px;
    height: 60px;
    padding: 0;
    opacity: 0;
    -webkit-transition: -webkit-transform 0.3s, opacity 0.3s;
    transition: transform 0.3s, opacity 0.3s;
    -webkit-transform: scale3d(0.7,0.7,1);
    transform: scale3d(0.7,0.7,1);
}

.video-wrap--show .action--close {
    opacity: 1;
    -webkit-transition-delay: 1.2s;
    transition-delay: 1.2s;
    -webkit-transform: scale3d(1,1,1);
    transform: scale3d(1,1,1);
}
```

Now we are getting to the heart of our styles.

For screens that are at least 25em in width, we want the expanding animation to take effect. So first we need to set the inner video wrapper to the right size. For the "vertical" case we will use the viewport width as a base measure to size our inner container. We do that so that we have a similar size to the decorative polaroid images which also have a dynamic width and height depending on the aspect ratio.

Once we set the main wrapper's class to **video-wrap–show**, we play the animation **showVideo-1**. When we close it, we will use the animation **hideVideo**. We'll have a more detailed look at the animations later on.

The inner video wrapper will be slightly rotated and scaled down initially. You could also set these properties in the 0% keyframe but you can save yourself the repetition and just define the initial state in the class declaration.

The video element will take up all the viewport height for this default "vertical case" (in the next media query we will switch that) and let the width be defined automatically. It will also be transformed; first, to be centered with the "translate -50%" technique, then scaled and rotated in the opposite direction to compensate the rotation of the parent slightly. We don't want to rotate it too much, as we don't want any edges to show here. Make sure to play around with these transforms to see how you can achieve other interesting effects.

When we show the main wrapper, we will change the video element's transform (we'll still keep it centered, though) and make sure that it's also in that state when we close it. When we hide everything, we simply want to fade out the wrapper without animating it back. When closing things and making them disappear always think of the fastest elegant way to do it; remember that the user has decided to get rid of whatever is being closed, so making it easy and more fast can be desirable in cases like these.

```
@media screen and (min-width: 25em) {
    .video-inner {
        width: 30vw;
        height: 30vw;
        border: 20px solid #fff;
        -webkit-transform: scale3d(0.1,0.1,1) rotate3d(0,0,1,-5deg);
        transform: scale3d(0.1,0.1,1) rotate3d(0,0,1,-5deg);
    }
    .video-wrap--show .video-inner {
        opacity: 0;
        -webkit-animation: showVideo-1 1.25s forwards;
        animation: showVideo-1 1.25s forwards;
    }
    .video-wrap--hide .video-inner {
        -webkit-animation: hideVideo 1.25s forwards;
        animation: hideVideo 1.25s forwards;
    }
    .video-player {
        left: 50%;
        width: auto;
        height: 100vh;
        -webkit-transition: -webkit-transform 1s;
        transition: transform 1s;
        -webkit-transform: translate3d(-50%,-50%,0) scale3d(0.7,0.7,1) rotate3d(0,0,1,5deg);
        transform: translate3d(-50%,-50%,0) scale3d(0.7,0.7,1) rotate3d(0,0,1,5deg);
    }
    .video-wrap--show .video-player,
    .video-wrap--hide .video-player {
        -webkit-transform: translate3d(-50%,-50%,0) scale3d(1,1,1);
        transform: translate3d(-50%,-50%,0) scale3d(1,1,1);
    }
}
```

![VideoOpeningAnimation_screen2](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/uploads/2015/07/VideoOpeningAnimation_screen2.jpg)

The second media query is for the "horizontal" case, i.e. the case that we know will make the video cover the whole screen by setting the width to the full viewport width. We take the values of the video's width and height to define the minimum aspect ratio, which is the same as 16/9.

We size our inner wrapper to _30vh_ initially, and the video itself will take up all the width of the screen while leaving the height to _auto_.

```
@media screen and (min-width: 25em) and (min-aspect-ratio: 1280/720) {
    .video-inner {
        width: 30vh;
        height: 30vh;
    }
    .video-wrap--show .video-inner {
        -webkit-animation: showVideo-2 1.25s forwards;
        animation: showVideo-2 1.25s forwards;
    }
    .video-player {
        width: 100vw;
        height: auto;
    }
}
```

Just a side note on formatting the CSS: you might have noticed that we don't use a new line between rules in media queries; this turns out to be really useful because you _know_ that you are inside of a media query when editing your styles. Not a big amazing thing, but sometimes it can speed things up and ease locating something.

Let's define the animations!

_You might wonder why we are using the WebKit prefix for transitions and animations. Animations still [need the prefix](http://caniuse.com/#search=animation) for Safari (8), iOS Safari (8.4) and Android (40) in order to work and [transitions](http://caniuse.com/#search=transition) for some older Android browsers._

The first animation is for expanding the inner video wrapper for the vertical case where we take the viewport width as base measure. You don't need to distinguish these two cases for the inner wrapper if you have a fixed initial size. But since we want a dynamic size depending on the ratio (and because we want to show you how to abuse the relative viewport units) we define two different opening animations.

![VideoOpeningAnimation_screen3](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/uploads/2015/07/VideoOpeningAnimation_screen31.jpg)

Considering the initial state of the inner wrapper, we now animate its dimensions to half of the viewport width and set the opacity to 1\. We scale it up a bit but keep the rotation value. Then we animate to fullscreen, using 100vw for the width and 100vh for the height. The transforms will be "unset":

```
@-webkit-keyframes showVideo-1 {
    50% {
        width: 50vw;
        height: 50vw;
        opacity: 1;
        -webkit-transform: scale3d(0.5,0.5,1) rotate3d(0,0,1,-5deg);
        transform: scale3d(0.5,0.5,1) rotate3d(0,0,1,-5deg);
    }
    100% {
        width: 100vw;
        height: 100vh;
        opacity: 1;
        -webkit-transform: scale3d(1,1,1) rotate3d(0,0,1,0deg);
        transform: scale3d(1,1,1) rotate3d(0,0,1,0deg);
    }
}

@keyframes showVideo-1 {
    50% {
        width: 50vw;
        height: 50vw;
        opacity: 1;
        -webkit-transform: scale3d(0.5,0.5,1) rotate3d(0,0,1,-5deg);
        transform: scale3d(0.5,0.5,1) rotate3d(0,0,1,-5deg);
    }
    100% {
        width: 100vw;
        height: 100vh;
        opacity: 1;
        -webkit-transform: scale3d(1,1,1) rotate3d(0,0,1,0deg);
        transform: scale3d(1,1,1) rotate3d(0,0,1,0deg);
    }
}
```

The other animation is exactly the same, we just use the viewport height as base measure and so we need to set the 50% keyframe width and height to 50vh.

```
@-webkit-keyframes showVideo-2 {
    50% {
        width: 50vh;
        height: 50vh;
        opacity: 1;
        -webkit-transform: scale3d(0.5,0.5,1) rotate3d(0,0,1,-5deg);
        transform: scale3d(0.5,0.5,1) rotate3d(0,0,1,-5deg);
    }
    100% {
        width: 100vw;
        height: 100vh;
        opacity: 1;
        -webkit-transform: scale3d(1,1,1) rotate3d(0,0,1,0deg);
        transform: scale3d(1,1,1) rotate3d(0,0,1,0deg);
    }
}

@keyframes showVideo-2 {
    50% {
        width: 50vh;
        height: 50vh;
        opacity: 1;
        -webkit-transform: scale3d(0.5,0.5,1) rotate3d(0,0,1,-5deg);
        transform: scale3d(0.5,0.5,1) rotate3d(0,0,1,-5deg);
    }
    100% {
        width: 100vw;
        height: 100vh;
        opacity: 1;
        -webkit-transform: scale3d(1,1,1) rotate3d(0,0,1,0deg);
        transform: scale3d(1,1,1) rotate3d(0,0,1,0deg);
    }
}
```

Notice, how we add the actual reset transform with the scale3d and the rotate3d. You could as well simply write one of the transform types but this is a troublemaker for browsers like FF. It's a good idea to thoroughly test each case if you'd like to simplify it.

![VideoOpeningAnimation_screen4](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/uploads/2015/07/VideoOpeningAnimation_screen4.jpg)

Last, but not least, we define the animation for hiding the whole thing. Remember that we've already taken care of hiding the video element itself in the first media query. For the video wrapper animation we set the first and last keyframe to be the same except for the opacity. Like that we are simply fading out the video wrapper.

```
@-webkit-keyframes hideVideo {
    0% {
        width: 100vw;
        height: 100vh;
        opacity: 1;
        -webkit-transform: scale3d(1,1,1) rotate3d(0,0,1,0deg);
        transform: scale3d(1,1,1) rotate3d(0,0,1,0deg);
    }
    100% {
        width: 100vw;
        height: 100vh;
        opacity: 0;
        -webkit-transform: scale3d(1,1,1) rotate3d(0,0,1,0deg);
        transform: scale3d(1,1,1) rotate3d(0,0,1,0deg);
    }
}

@keyframes hideVideo {
    0% {
        width: 100vw;
        height: 100vh;
        opacity: 1;
        -webkit-transform: scale3d(1,1,1) rotate3d(0,0,1,0deg);
        transform: scale3d(1,1,1) rotate3d(0,0,1,0deg);
    }
    100% {
        width: 100vw;
        height: 100vh;
        opacity: 0;
        -webkit-transform: scale3d(1,1,1) rotate3d(0,0,1,0deg);
        transform: scale3d(1,1,1) rotate3d(0,0,1,0deg);
    }
}
```

This syntax looks like we could simplify it, right? It should be possible to define the 0% and 100% keyframe in one definition and then set the different properties in single keyframes, but this does not seem to work well in IE, unfortunately. Also, placing the animations into the media query definitions will bring you into hell's kitchen in IE10 and IE11: they simply won't work. Read more about this problem in this article by Karen Menezes: [IE bug: keyframe animations inside a media query block](http://blog.karenmenezes.com/2014/dec/26/ie-bug-keyframe-animations/).

These are all the styles for the effect. Make sure to play around with the transforms and the animations. For example, you could set a nice timing function or rotate differently, or even rotate in perspective; there are many possibilities--imagination is all you need :)

**We hope you enjoyed this tutorial and find it useful! Thank you for reading!**

[View demo](http://tympanus.net/Tutorials/VideoOpeningAnimation/) [Download source](http://tympanus.net/Tutorials/VideoOpeningAnimation/VideoOpeningAnimation.zip)
