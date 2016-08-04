# rvikmanis/fastflux

[Original URL](https://github.com/rvikmanis/fastflux)

> A lightweight message driven flux implementation written in ES6, inspired by FRP, the original Flux and Redux. Introduction Architecture Installation Getting Started

[![Documentation Status](https://camo.githubusercontent.com/81ad64f9659069241cd62ccd13a334e6a6ac16fe/687474703a2f2f7276696b6d616e69732e6769746875622e696f2f66617374666c75782f62616467652e737667)](http://rvikmanis.github.io/fastflux/source.html)

A lightweight message driven flux implementation written in ES6, inspired by [FRP](https://en.wikipedia.org/wiki/Functional_reactive_programming), the [original Flux](https://facebook.github.io/flux/docs/overview.html) and [Redux](https://github.com/rackt/redux).

- [Introduction](https://github.com/rvikmanis/fastflux#introduction)
- [Architecture](https://github.com/rvikmanis/fastflux#architecture)
- [Installation](https://github.com/rvikmanis/fastflux#installation)
- [Getting Started](https://github.com/rvikmanis/fastflux#getting-started)
- [API Reference](http://rvikmanis.github.io/fastflux/identifiers.html)

## [](https://github.com/rvikmanis/fastflux#introduction)Introduction

Fastflux is a simple application state management library; meant to be the _Model_, and to some extent _Controller_, in your **M-V-C** stack, with [React](https://facebook.github.io/react/) as the _View_.

### [](https://github.com/rvikmanis/fastflux#why)Why?

Creating rich user interfaces in React is simple and fun, but React was never meant to do the job alone, it is not a full web framework.

Say, you need to add new semi-global state to your application of 15 levels of nested components. How will you do it? Add the state to nearest common parent component and pass it down through props? Gets tedious fast, right? We call it _prop hell_.

Fastflux keeps the **state decoupled from components**, allowing you to keep your app and your mind free from grandiose prop hierarchies, letting you focus on what matters most - application logic.

### [](https://github.com/rvikmanis/fastflux#how)How?

Fastflux is a flux-like library, meaning it follows the same "data flows one way" pattern as React. Learn more about the Flux architecture from [the official docs](http://facebook.github.io/flux/docs/overview.html#content).

Architecture of Fastflux and its differences and similarities to classic Flux are explained in the next section.

## [](https://github.com/rvikmanis/fastflux#architecture)Architecture

How data flows in a typical Fastflux application:

[![Architecture](http://s14.postimg.org/orljt6rbl/fastflux.png)](https://camo.githubusercontent.com/6bb5daee67ce6ad23e32fb44d928eeee2b4cbfc2/687474703a2f2f7331342e706f7374696d672e6f72672f6f726c6a743672626c2f66617374666c75782e706e67)

- **Stores** are state containers, coupled with one or more _reducers_ – pure functions describing the transformation of state in response to messages.
- **Messages** – plain objects identified by `type`, optionally containing data fields – primarily used to signal stores about some event (data received from server, user pressed key etc.). Known from classic Flux as _actions_.
- **Actions**, unlike in classic Flux, are self-contained asynchronous functions for a concrete task (create post, logout user etc.). Usually invoked by views, other actions or events from the environment. Subscribers (stores, other actions, even views) listen to actions for messages that can be emitted multiple times per invocation. IO and side effects are permitted.
- **Views** – React components. Can listen to stores for state changes. Additionally views may listen to actions for messages when a short feedback cycle is desired (e.g. input error handling), without polluting the stores.

There is no central dispatcher as in classic Flux – stores subscribe directly to the actions they need.

## [](https://github.com/rvikmanis/fastflux#installation)Installation

Install from npm:

```
npm install fastflux
```

## [](https://github.com/rvikmanis/fastflux#getting-started)Getting Started

This introduction assumes familiarity with ES6 and React.

### [](https://github.com/rvikmanis/fastflux#create-your-first-store-and-action)Create your first store and action

```
import {createStore, createAction} from 'fastflux';

// Define store
let items = createStore({
 getInitialState() {return []},

 reducers: {

 add(state, {text}) {
 return state.concat([text])
 }

 }
});

// Define action
let addItem = createAction(emit => text => {
 emit({type: "add", text})
});

// Link action to store
addItem.subscribe(items.send);
```

#### [](https://github.com/rvikmanis/fastflux#setup-logging)Setup logging

```
// Log state over time
items.subscribe(s => console.log("State of `items`:", s))

// While we're at it, let's create some items
["bar", "foobar", "baz"].forEach(addItem);
```

Open the console, you will see something like this:

```
State of `items`: ["bar"]
State of `items`: ["bar", "foobar"]
State of `items`: ["bar", "foobar", "baz"]
```

### [](https://github.com/rvikmanis/fastflux#create-components)Create components

```
import {createSubscriber} from 'fastflux';
import {Component} from 'react';

// Define input
class ItemInput extends Component {

 state = {value: ""};

 onKeyDown = (e) => {
 if (e.key === "Enter") {
 // Invoke action
 addItem(e.target.value);
 this.clearValue();
 }
 };

 onChange = (e) => {
 this.setValue(e.target.value)
 };

 setValue = (value) => this.setState({value});
 clearValue = () => this.setValue("");

 render() {
 return <input
 type="text"
 onKeyDown={this.onKeyDown}
 onChange={this.onChange}
 value={this.state.value}
 />
 }

}

// Define root component
function ControllerView(props) {
 // `props.items` contains current state of the `items` store
 let items = props.items.map(item => <li>{item}</li>);
 return <div>
 <ul>{items}</ul>
 <ItemInput />
 </div>;
}

// Subscriber observes stores passed in props:
// When a store emits new state, subscriber updates wrapped component's prop.
ControllerView = createSubscriber(ControllerView);
let rootComponent = <ControllerView items={items}>;
```

#### [](https://github.com/rvikmanis/fastflux#render)Render

Assuming you have `<div id="mount"></div>` in your document body:

```
import {render} from 'react-dom';
render(rootComponent, document.querySelector("#mount"))
```
