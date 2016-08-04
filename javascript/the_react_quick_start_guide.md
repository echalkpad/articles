# The React Quick Start Guide

[Original URL](https://www.codementor.io/reactjs/tutorial/the-reactjs-quick-start-guide)

> This article will give you a quick overview of how to build user interfaces in React JS. There's just enough to get yourself started and nothing more. Code along with this starter kit...

![](https://s3.amazonaws.com/codementor_content/2015-Jan-Week3/react_quickstart.png) _This article will give you a quick overview of how to build user interfaces in React JS. There's just enough to get yourself started and nothing more. Code along with this [starter kit](https://github.com/jarsbe/react-starter-kit) (instructions in the repo) or just read along. This article was authored by Jack Callister and originally posted on [his blog](http://www.jackcallister.com/2015/01/05/the-react-quick-start-guide.html)._

## Concepts

React has quite a small API. This makes it fun to use, easy to learn, and simple to understand. However, being simple does not mean it's familiar. There are a few concepts to cover before getting started. Let's look at each in turn.

**React elements** are JavaScript objects which represent HTML elements. They do not exist in the browser. They represent browser elements such as an `h1`, `div` or `section`.

**Components** are developer created React elements. They're usually larger parts of the user interface which contain both the structure and functionality. Think of concepts such as a `NavBar`, `LikeButton` or `ImageUploader`.

**JSX** is a technique for creating React elements and components. For example `<h1>Hello</h1>` is a React element written in JSX. The same React element can be written as JavaScript with `React.DOM.h1(null, 'Hello');`. JSX is less effort to read and write and is transformed into JavaScript before running in the browser.

**The Virtual DOM** is a JavaScript tree of React elements and components. React renders the virtual DOM to the browser to make the user interface visible. React observes the virtual DOM for changes and automatically mutates browser DOM to match the virtual DOM.

With a small understanding of these concepts we can move on to using React. We'll build a series of user interfaces, each adding a layer of functionality on the previous. We'll build a photo stream similar to instagram – example applications don't get much better than this!

## Rendering

The first order of business is rendering a virtual element (a React element or component). Remember, since a virtual element exists only in JavaScript memory, we must explicitly tell React to render it to the browser DOM.

```
React.render(<img src='http://tinyurl.com/lkevsb9' />, document.body);
```

The `render` function accepts two arguments; a virtual element and a real DOM node. React takes the virtual element and inserts it into the given DOM node. The image is now visible in the browser.

## Components

Components are the heart and soul of React. They are custom React elements. They are usually extended with unique functionality and structure.

```
var Photo = React.createClass({

 render: function() {
 return <img src='http://tinyurl.com/lkevsb9' />
 }
});

React.render(<Photo />, document.body);
```

The `createClass` function accepts an object which implements a `render` function.

The `Photo` component is constructed, `<Photo />`, and rendered to the document body.

This component does nothing more than the previous React image element but it's ready to be extended with custom functionality and structure.

## Props

Props can be thought of as a component's options. They're given as arguments to a component and look exactly like HTML attributes.

```
var Photo = React.createClass({

 render: function() {
 return (
 <div className='photo'>
 <img src={this.props.imageURL} />
 <span>{this.props.caption}</span>
 </div>
 )
 }
});

React.render(<Photo imageURL='http://tinyurl.com/lkevsb9' caption='New York!' />, document.body);
```

Inside the React `render` function, two props are passed to the `Photo` component; `imageURL` and `caption`.

Inside the component's `render` function the `imageURL` prop is used as the `src` for the React image element. The `caption` prop is also used as plain text within the React span element.

It's worth noting that a component should never change its props, they're immutable. If a component has data that's mutable, use the state object.

## State

The state object is internal to a component. It holds data which can change over time.

```
var Photo = React.createClass({

 toggleLiked: function() {
 this.setState({
 liked: !this.state.liked
 });
 },

 getInitialState: function() {
 return {
 liked: false
 }
 },

 render: function() {
 var buttonClass = this.state.liked ? 'active' : '';

 return (
 <div className='photo'>
 <img src={this.props.src} />

 <div className='bar'>
 <button onClick={this.toggleLiked} className={buttonClass}>
 ♥
 </button>
 <span>{this.props.caption}</span>
 </div>
 </div>
 )
 }
});

React.render(<Photo src='http://tinyurl.com/lkevsb9' caption='New York!'/>, document.body);
```

Having state in a component introduces a bit more complexity.

The component has a new function `getInitialState`. React calls this function when the component is initialised. The returned object is set as the component's initial state (as the function name implies).

The component has another new function `toggleLiked`. This function calls `setState` on the component which toggles the `liked` value.

Within the component's render function a variable `buttonClass` is assigned either 'active' or nothing – depending on the `liked` state.

`buttonClass` is used as a class name on the React button element. The button also has an `onClick` event handler set to the `toggleLiked` function.

Here's what happens when the component is rendered to the browser DOM:

- When the component's button is clicked, `toggleLiked` is called
- The `liked` state is changed
- React re-renders the component to the virtual DOM
- The new virtual DOM is compared with the previous virtual DOM
- React isolates what has changed and updates the browser DOM

In this case, React will change the class name on the button.

## Composition

Composition means combining smaller components to form a larger whole. For example the `Photo` component could be used inside a `PhotoGallery` component, like so:

```
var Photo = React.createClass({

 toggleLiked: function() {
 this.setState({
 liked: !this.state.liked
 });
 },

 getInitialState: function() {
 return {
 liked: false
 }
 },

 render: function() {
 var buttonClass = this.state.liked ? 'active' : '';

 return (
 <div className='photo'>
 <img src={this.props.src} />

 <div className='bar'>
 <button onClick={this.toggleLiked} className={buttonClass}>
 ♥
 </button>
 <span>{this.props.caption}</span>
 </div>
 </div>
 )
 }
});

var PhotoGallery = React.createClass({

 getDataFromServer: function() {
 return [{
 url: 'http://tinyurl.com/lkevsb9',
 caption: 'New York!'
 },
 {
 url: 'http://tinyurl.com/mxkwh56',
 caption: 'Cows'
 },
 {
 url: 'http://tinyurl.com/nc7jv28',
 caption: 'Scooters'
 }];
 },

 render: function() {
 var data = this.getDataFromServer();

 var photos = data.map(function(photo) {
 return <Photo src={photo.url} caption={photo.caption} />
 });

 return (
 <div className='photo-gallery'>
 {photos}
 </div>
 )
 }
});

React.render(<PhotoGallery />, document.body);
```

The `Photo` component is exactly the same as before.

There's a new `PhotoGallery` component which generates `Photo` components. In this case there's some fake server data which returns an array of 3 objects, each with a `url` and `caption`.

The data is looped over and will generates 3 `Photo` components which are inserted into the return value of the component's `render` function.

## Conclusion

This should be enough to get started building user interfaces with React. The [React docs](http://facebook.github.io/react/docs/getting-started.html) cover everything in detail. I highly recommend reading it.

Also this guide doesn't go into detail about your local environment setup. The documentation should, or alternatively, look at my [boilerplate](https://github.com/jarsbe/react-webpack-boilerplate) for a simple solution.

If I've made a mistake or something's not working for you, ping me on [twitter](http://twitter.com/jarsbe).

![](https://s3.amazonaws.com/codementor_content/2015-Jan-Week3/jack_c.png)Jack Callister is the Co-organiser of [Hack Pack](http://www.meetup.com/hackpack/) and a full-stack developer who enjoys building software with Ruby on Rails and React JS. You can visit [his blog](http://www.jackcallister.com/journal/) for more tips and tricks.
