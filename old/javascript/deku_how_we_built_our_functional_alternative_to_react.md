# Deku: How we built our functional alternative to React

[Original URL](https://segment.com/blog/deku-our-functional-alternative-to-react/)

> 11 May 2015 dev Over the past few months at Segment we've been rebuilding large parts of our app UI. A lot of it had become impossible to maintain because we were relying on models binding to...

11 May 2015 [dev](https://segment.com/blog/categories/dev/)

Over the past few months at Segment we've been rebuilding large parts of our app UI. A lot of it had become impossible to maintain because we were relying on models binding to the DOM via events.

Views that are data-bound to the DOM sound great but they are difficult to follow once they become complex and bi-directional. You'd often forget to bind some events and a portion of your UI would be out of sync, or you'd add a new feature and break 3 others.

So we decided to take on the challenge to build our own functional alternative to [React](https://facebook.github.io/react/ "React").

## Building a prototype

We managed to get a prototype working in about a month. It could render DOM elements and the diffing wasn't too bad. However, the only way to know if it was any good was to throw it into a real project. So that's what we did. We built the [Tracking Plan](https://segment.com/blog/introducing-the-tracking-plan/) using the library. At this point it didn't even have a real name.

It started simple, we found bugs and things we'd overlooked, then we started seeing patterns arising and ways to make the development experience better.

We were able to quickly try some ideas and trash them if they didn't work. At first we started building it like a game engine. It had a rendering loop that would check to see if components were dirty and re-render on every frame, and a `scene` that managed all the components and inputs like a game world. This turned out to be annoying for debugging and made it overly complex.

### Build, test, iterate

Thanks to this process of iteration we were able to cut scope. We never needed `context` or `refs` like React, so we didn't add it. We started with a syntax that used prototypes and constructors but it was unnecessarily verbose. We haven't had to worry about maintaining text selection because we haven't run across it in real-world use. We also haven't had any issues with element focus because we're only supporting newer browsers.

We spent many late nights discussing the API on a white board and it's something we care about a lot. We wanted it to be so simple that it would be almost invisible to the user. An API is just UI for developers so we treated it like any other design problem at Segment -- build, test, iterate.

Performance is the most important feature of any UI library. We couldn't be sure if the library was on the right path until we'd seen it running in a real app with real data and constraints. We managed to get decent performance on the first try and we've been fine-tuning performance as we add and remove new features.

We first ran into performance issues when we had to re-build the debugger. Some customers were sending hundreds of events per second and the animation wouldn't work correctly if we were just trashing DOM elements every frame. We implemented a more optimized key diffing algorithm and now it renders hundreds of events per second at a smooth 60 fps with ease. Animations included.

### Stablizing the API

Eventually everything started to settle down. We took the risk and implemented our own library and it now powers the a large portion of our app. We've stripped thousands of lines of code and now it's incredibly easy to add new features and maintain thanks to this new library.

Finally, we think it's ready to share with everyone else.

## Introducing Deku

[Deku](https://github.com/segmentio/deku "Deku") is our library for building user interfaces. It supports many of the features you're familiar with in React but aims to be small and functional. You define your UI as a tree of components and whenever a state change occurs it re-renders the entire tree to patch the DOM using a [highly optimized diffing algorithm](https://github.com/segmentio/deku/blob/master/lib/render.js#L492 "Diffing Algorithm").

The whole library weighs in at less than 10kb and is easy to follow. It's also using npm so some of those modules are probably being used elsewhere in your code anyway.

It uses the same concept of components as React. However, we don't support older browsers, so the codebase is small and component API is almost non-existent. It even supports JSX thanks to [Babel](https://babeljs.io "Babel").

Here's what a [component](https://github.com/segmentio/deku/blob/master/docs/guides/components.md) looks like in Deku:

```
import {element} from 'deku'

function render (component) {
 let {props,state} = component
 return <button type={props.type}>{props.children}</button>
}

function afterMount(component, el, setState) {
 setState({ mounted: true })
}

export default { render, afterMount }
```

Then you can import that component and render your app:

```
import {tree,render,element} from 'deku'
import Button from './button'

var app = tree(<Button>Hello World</Button>)
render(app, document.body)
```

### Designed for ES6

You'll notice there is no concept of classes or use of `this`. We're using plain objects and functions. The ES6 module syntax is used to define components and every lifecycle hook is passed the `component` object which holds the `props` and `state` you'll use to render your template.

```
export let Button = {
 render({props, state}) {
 return <button>{props.text}</button>
 }
}
```

We never really needed classes. What's the point when you never initialize them anyway? The beauty of using plain functions is that the user can use the ES6 module system to define them however they want! Best of all, there's is no new syntax to learn.

### Lifecycle hooks

Deku has many of the same [lifecycle hooks](https://github.com/segmentio/deku/blob/master/docs/guides/components.md) but with two new ones - `beforeRender` and `afterRender`. These are called every single render, including the first, unlike the update hooks. We've found these let us stop thinking about the lifecycle state so much.

```
function render (component) {
 return <div>Hello World</div> 
}

function afterRender (component, el) {
 // Make changes to the element to match the current state.
 // This could be used for animations.
}

export default {render,afterRender}
```

Some of the [lifecycle hooks](https://github.com/segmentio/deku/blob/master/docs/guides/components.md) are passed the `setState` function so you can trigger side-effects to update state and re-render the app. DOM events are delegated to the root element and we don't need to use any sort of synthesized event system because we're not supporting IE9 and below. This means you never need to worry about handling or optimizing event binding.

### Client and server rendering

To render the component to the DOM we need to create a `tree`. The `tree` will manage loading data, communicating between components and allows us to use plugins on the entire application. For us it has eliminated the need for anything like [Flux](https://github.com/facebook/flux "Flux") and there are no singletons in sight.

You can render the component tree anyway you'd like -- you just need a renderer for it. So far we have a HTML renderer for the server and a DOM renderer for the client since those are the two we've needed. It would be possible to build a canvas or WebGL renderer.

The [dbmonster performance mini-app](http://anthonyshort.github.io/dbmonster "Deku dbmonster") written in Deku is also very fast and renders at roughly 15-16 fps compared to most other libraries which render at 11-12 fps. We're always looking for more ways to optimize the diffing algorithm even further but it's already we think it's fast enough.

You can [read more about Deku](https://github.com/segmentio/deku "Deku") and [view some examples](https://github.com/segmentio/deku/tree/master/examples) on it's Github page.

## Why not React?

The first thing we usually get asked when we tell people about Deku is "Why didn't you just use React?". It could seem like a classic case of [NIH syndrome](http://en.wikipedia.org/wiki/NIH_syndrome "Not Invented Here Syndrome").

We originally looked into this project because we use [Duo](https://github.com/duojs/duo "Duo") as a front-end build tool. Duo is like npm, but just uses Github. It believes in small modules doing one thing well. React was a 'big thing' doing many things within a black box. We like knowing in detail how code works, so we feel comfortable with it and can debug it when something goes wrong. It's very hard to do that with React or any big framework.

So we looked for smaller alternatives, like [virtual-dom](https://github.com/Matt-Esch/virtual-dom "virtual-dom") and [mercury](https://github.com/Raynos/mercury "mercury"). The documentation for virtual-dom was slim and we didn't think the API for mercury was very user friendly.

We ended up using [React](https://facebook.github.io/react/ "React") for a short time but the API forced us to use a class-like syntax that would lock us into the framework. We also found that we kept fighting with function context all the time which is waste of brain energy. React has some functional aspects to it but it still feels very object-oriented. You're always concerning yourself with implicit environment state thanks to `this` and the class system. If you don't use classes you never need to worry about `this`, you never need decorators and you force people to think about their logic in a functional way.

What started as a hack project to see if we could better understand the concept behind React has developed into a library that is replacing thousands of lines of code and has become the backbone of our entire UI. It's also a lot of fun!

## What's next

We've come a long way in the past few months. Next we're going to look at a few ways we could add animation states to components to solve a problem that plagues every component system using virtual DOM.

In our next post on [Deku](https://github.com/segmentio/deku "Deku"), we'll explain how we structure our components and how we deal with CSS. We'll also show off our UIKit -- the set of components we've constructed to rapidly built out our UI.

![Gif](http://media.giphy.com/media/WixLOKoD6ii7m/giphy.gif)
