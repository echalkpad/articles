# SVG animation with GreenSock.

[Original URL](https://www.august.com.au/blog/svg-animation-with-greensock/)

> I'm pretty excited about the GreenSock Animation Platform (GSAP), which is why I wanted to write this article. GSAP brings vector images to life using scalable vector graphic (SVG) animations....

I'm pretty excited about the GreenSock Animation Platform (GSAP), which is why I wanted to write this article.

GSAP brings vector images to life using scalable vector graphic (SVG) animations. You can animate a scene, develop HTML5 banner ads, improve project interfaces – the options are endless. My aim is to give you an introduction to GreenSock, the benefits of the platform and how to use it effectively.

To help illustrate how the platform works, I've embedded the development of an animation so you can see how we built it. I'll then show you what the finished element turned out like. I've kept it basic on purpose but have provided links to further resources and information if you're interested.

While you can use GSAP to animate any type of element, my focus is on using it with SVG images as there's potential to create some awesome animations.

But first, a quick word on SVG images...

If you are unfamiliar with how SVG images and their properties, these two resources will help:

- **Jakob Jenkov** has put together a great series of tutorials on his [website](http://tutorials.jenkov.com/svg/index.html). He is great at explaining SVG properties and including demos on how to use them.
- **Sara Soueidan** writes in-depth articles on SVG for her [website](http://sarasoueidan.com/tags/svg/index.html), making it easy to understand how it all works. She dives into the properties and provides great reference images.

## On to Greensock.

Previously, you might've used Flash to animate for the web. But as support for Flash continues to die due to [security flaws](http://www.theverge.com/2015/7/8/8911077/adobe-flash-hacking-team-vulnerability) and lack of browser support, there's a need for a worthy replacement. SVG has a range of [benefits](http://code.tutsplus.com/articles/why-arent-you-using-svg--net-25414) and with growing [browser support](http://caniuse.com/#feat=svg), it makes good sense as a replacement for Flash.

One of the pain points with animating an SVG image is the [inconsistencies](https://css-tricks.com/svg-animation-on-css-transforms/) in how browsers handle these types of images – in particular CSS transforms. Thankfully, this is where a tool such as GreenSock is handy. It solves browser inconsistencies and makes sure everything works as expected.

GSAP is a JavaScript animation library. It has a strong community, thorough documentation, large forums and plenty of demos to help educate you on how the platform works. GreenSock has multiple [libraries](http://greensock.com/products/) that specialise in different areas and it's worth browsing through the list to see what you can use.

Greensock's performance in the browser is great. It gives you smooth transitions with the GSAP team claiming it's "20x faster than jQuery" and "faster than CSS3 animations and transitions in many cases".

If you want to test its performance for yourself, you can run a stress test on the GreenSock [website](https://www.greensock.com/js/speed.html). The test has two variables: the amount of dots animating around the screen; and the JavaScript library used to animate the dots. You can run the animation with different libraries and compare how they perform.

[TweenLite](https://greensock.com/tweenlite) is the core animation library for GSAP. It's small and can be used alongside other GreenSock libraries. It will most likely be all you need for basic SVG animation. [TweenMax](http://greensock.com/tweenmax) is a larger library, containing Tweenlite, plus nine other GreenSock libraries. It provides a lot more functionality.

You can build a large animation without Tweenlite but you will find it hard to manage when you enter the editing phase. This is because when you are animating elements one after another, you will need to set a duration and a delay for each element in your animation. As you tweak an element's duration, your previously correct delays on other elements will now be too long or too short. You'll find yourself spending time re-tweaking delays so it still animates correctly.

GreenSock's Timeline library comes in handy here, because it solves the above timing issues by completely removing the dependency for delays. Each element in your stack waits for the previous elements to finish animating before it starts animating. Since the full animation runs in a cascading order you can reorder elements and change durations as you please.

## Getting started

In the following examples, we will be using the TimelineMax library because it has more features. You could do these examples with [TimelineLite](https://greensock.com/timelinelite) though for a lighter weight version of the plugin.

The first thing to do is create a Timeline instance and store it as a variable. Your animations append to this new timeline. You are not restricted to only one timeline. If your animation was made up of lots of pieces you could create [multiple timelines](http://codepen.io/GreenSock/pen/emZEam) for each piece and nest them under one master timeline.

```
var tl = new TimelineMax();
```

This line of code creates a timeline instance with a bunch of settings that are pre-configured. It's possible to change the default behaviour, such as having the animation pause upon creation, or changing how many times the animation plays by passing in an object. To learn more about what you can do and how to do it at this point, refer to this [documentation](https://greensock.com/docs/#/HTML5/GSAP/TimelineMax/).

Animating elements in a timeline is straightforward. Below is an example of how to use the [.to()](https://greensock.com/docs/#/HTML5/GSAP/TimelineLite/to/) method to change an element's opacity to zero as it moves 100px on the x axis.

The tl variable we created in the previous step comes first and holds the timeline instance. Inside the to() method, the element to animate is passed in, along with the duration of the animation. After that, you can list anything you want to animate in a comma-separated list.

```
tl.to(element, duration, {
 x: 100,
 opacity: 0
});
```

## Labels + timing.

Animations will be queued and played in whatever order you list them. They play one after the other, which is expected right? But what if we want to overlap animations, have a delay/pause between animations, or want to run multiple animations at the same time?

One way to achieve this is to add a string to the end of the method containing a time value.

The string will either need to include a += or -= followed by a duration. The += will add in the specified amount of time before it starts animating, creating a delay/pause effect between animations.

The -= will do the opposite, making the elements animation start early, creating an overlap effect between animations. In the first example below, the element waits 0.5 seconds before it starts animating. In the second example, the element starts animating 0.5 seconds before the first element finishes animating.

```
tl.to(element, duration, {
 x: 100,
 opacity: 0
}, "+=0.5");

tl.to(element, duration, {
 x: 100,
 opacity: 0
}, "-=0.5");
```

Another way of organising animated elements is to use labels. Labels group elements that you want to animate together in a string. You can name the label anything you choose. Elements with the same label will animate at the same time, rather than playing one after another. Labels can also be appended with += time and -= time if you want to add offsets to an element's animation. Below are three examples of how you might implement labels in your timeline.

**Example 1**

```
tl.to(element, duration, {
 x: 100,
 opacity: 0
}, "myLabel");
```

**Example 2**

```
tl.to(element, duration, {
 x: 100,
 opacity: 0
}, "myLabel+=0.5");
```

**Example 3**

```
tl.to(element, duration, {
 x: 100,
 opacity: 0
}, "myLabel-=0.5");
```

## Easing

Easing changes the rate of speed through an element's animation. A linear ease will move an element at a constant speed from start to finish. An ease-out ease will start fast and slow down as the animation comes to an end. Adding the correct easing to your animations makes your animation more realistic and a lot better.

GreenSock offers a lot of easing types and offer a great visualiser tool on their [website](http://greensock.com/ease-visualizer). If you want to learn more about easing and how it can be used, Ryan Brownhill wrote an article on [crafting easing curves for user interfaces](https://medium.com/@ryan_brownhill/crafting-easing-curves-for-user-interfaces-34f39e1b4a43). It's well worth a read and helps to further illustrate this concept.

You can also set the ease for an element's animation. Below is an example of animating an element's opacity and assigning a custom ease via the ease property.

```
tl.to(element, duration, {
 x: 100,
 opacity: 1,
 ease: SlowMo.ease.config(0.5, 0.7, false)
});
```

When you create a timeline instance, the setup with a default ease is Power1.easeOut. It's possible to change the default ease with this line of code.

```
TimelineMax.defaultEase = add easing you want here;
```

## Control

One of my favourite features of Timeline is that your animations are on a timeline and you can add [controls](http://codepen.io/GreenSock/pen/bpezc). These controls pause, play, resume, reverse and restart the animation. You can build on this functionality to create some pretty cool interactive animations.

Chris Gannon, for example, created this [demo](http://codepen.io/chrisgannon/full/zGmdBN/) to show you how you can scrub through an animation with your mouse. I also really like Adrian Parr's [demo](http://codepen.io/adrianparr/pen/mbrqt?editors=001) on how to scrub through an animation using an on scroll event.

## Key methods

If the information above is too dense for you, these are the fundamentals you need to get started. If nothing else, start attaching these methods to elements to see how they work. It's also a great cheat sheet for when you're getting started as these are methods you'll find yourself using regularly. For an in-depth cheat sheet, check out the one Petr Tichy has put together [here](https://ihatetomatoes.net/greensock-cheat-sheet/).

- [Set()](https://greensock.com/docs/#/HTML5/GSAP/TimelineLite/set/) is the equivalent of animating an element with a zero second duration. It alters an element's appearance or position without any transition.
- [To()](https://greensock.com/docs/#/HTML5/GSAP/TweenMax/to/) animates an element to a state.
- [FromTo()](https://greensock.com/docs/#/HTML5/GSAP/TimelineMax/fromTo/) animates an element from one state to another state.
- [StaggerTo()](https://greensock.com/docs/#/HTML5/GSAP/TweenMax/staggerTo/) animates a group of elements at the same time but with a staggered start time that you specify.
- [StagerFromTo()](https://greensock.com/docs/#/HTML5/GSAP/TimelineLite/staggerFromTo/) is a combination of the above fromTo() and staggerTo().

## Helpful methods

In addition to the functionality I've provided in this article, I also wanted to briefly highlight three methods that I found helpful in Greensock.

- [Seek()](https://greensock.com/docs/#/HTML5/GSAP/TimelineMax/seek/) is handy when it comes to tweaking and polishing your animation. It can be time consuming if watching the whole animation over and over. Seek takes either a time or label as its parameter. This means if you just want to view a part of your animation you can pass in a label and it will start from that point.
- [Pause()](https://greensock.com/docs/#/HTML5/GSAP/Core/SimpleTimeline/pause/) is great for inspecting what's happening in your animation. It can be useful to chain this after a Seek call to see how things look.
- [Timescale()](https://greensock.com/docs/#/HTML5/GSAP/TimelineMax/timeScale/) speeds up or slows down the overall timeline of your animation. Sometimes it can be hard to dissect an animation when it's playing at a normal pace. Slowing it down gives you a better insight into how it's fitting together. Timescale also comes in handy once you have built an animation and want to speed it up or slow it down without going through and tweaking the values.

## The finished animation!

Putting all the above examples together, here is the finished animation I built using the tools and methods mentioned here. I encourage you to read the code and comments to see how it fits together.

View the animation on [Codepen](http://codepen.io/allanpope/pen/11a57cd130517d6bf312e94a0cde135b?editors=001) and play around with it in your browser. Don't be scared of breaking it – go ahead and edit the code, tweak values and re-order events. Experimenting with the animation will improve your understanding of how it works. If you have any questions, feel free to add them to the comments below – I'm more than happy to try and help you out if I can.

See the Pen [Article](http://codepen.io/allanpope/pen/11a57cd130517d6bf312e94a0cde135b/) by Allan Pope ([@allanpope](http://codepen.io/allanpope)) on [CodePen](http://codepen.io).

## Further resources

If you can't get enough of GreenSock and want to read more about animating SVG images with it. Check out these links for more information.

## Wrapping up

This article is meant to be an introduction to GSAP. I hope you've found it useful. Take these foundation ideas and create something simple or make it as intricate as you want. I've mentioned in a [previous article](https://www.august.com.au/blog/resources-for-frontend-developers/) that [Codepen](http://codepen.io/) is a great source of inspiration and this is definitely the case for animations.

Here are some of my favourite GSAP animations. I recommend viewing the animations and reading the source code to see how different people create them. Viewing animations at a slower pace will help you get a better look at how it moves and fits together. As I mentioned earlier in the article, we can apply a [Timescale](https://greensock.com/docs/#/HTML5/GSAP/TimelineMax/timeScale/) to the timeline to slow it down.

After viewing the demos and learning how to animate using GreenSock, I hope you're inspired to go off and make something of your own.

Post a link to your work in the comments, I'd love to see it. If you are thinking "I want to make something but I can't think of anything to make", check out the [Codepen Creative Challenge](https://twitter.com/cccpens).

It's a growing community where users create pens around a theme. There are no prizes – it's all for the fun of it. But it's a great way to get inspired and to build something, share knowledge and be part of a community. It's super-cool to see what others come up with.

Happy animating!
