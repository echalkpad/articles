# Let's Build a Redux Powered React Application - Stormpath User Identity API

[Original URL](https://stormpath.com/blog/build-a-redux-powered-react-application/)

> In this blogpost, we'll dig into Redux and learn why it's valuable when building a React application. I'll also walk you through building your first Redux application, including...

In this blogpost, we'll dig into Redux and learn why it's valuable when building a React application. I'll also walk you through building your first Redux application, including how to use the [Stormpath React SDK](https://github.com/stormpath/stormpath-sdk-react) for [user authentication](https://stormpath.com/product/authentication/). When you are finished you can take that knowledge and apply that to your existing React applications!

## What Is Redux?

[Redux](https://github.com/reactjs/redux) is a library that helps you with managing state in your application. It has its design origins in [Flux](https://facebook.github.io/react/docs/flux-overview.html), but evolved from the pain of writing Flux applications. If you have written a Flux application, what you'll quickly notice is that Redux helps you with all the boilerplate that you had to manually write before. Also, unlike Flux, you have a single state container. This is a big advantage because it makes sharing state and reusing code as you build out your application a lot easier.

### Stores

A store is simply a state container. This is the place where your state lives and where actions are dispatched and handled. When you start out building a Redux application, you'll have to think about how you want to model your application and how the state should be stored. This is important because in Redux it's recommended to only have one store, and since state is shared it's a good idea to think of that before you get started.

### Actions

Actions are objects that describe how we want to mutate our state. You can think of actions as the API to your state tree. To illustrate, an action for adding a new user could be:

```
1
2
3
4
5
6
7
8``` | ```
{
 type: 'ADD_USER', data: { name: 'Foo', email: 'foo@bar.com', password: 'Foobar123_'
 }
}```
---------------------- | --------------------------------------------------------------------------------------------------

To make things clearer and easier to reuse, convention is to use a builder to build the action object. I.e. in the case above, you would create a function such as `addUser(name, email, password)` and have that build the object. As you can see, actions themselves doesn't call anything. An action is simply just an object that describes how we want to change the state.

### Reducers

Actions are cool, but they don't make much sense by themselves. This is where reducers come in. Reducers are action handlers that act upon dispatched actions in your store and reduce these actions into state changes. If we were to dispatch an action such as `ADD_USER` in our store, we could have a reducer that would pick that action up and add a new user entry to our state.

## Create The Redux Application

Now when you understand the basics, let's go ahead and design and build our first Redux driven application.

To make things simple, we're going to build a to-do application. This way we'll be able to play around with most of the most important concepts of Redux without focusing too much on the application itself.

If we think of a to-do application, what we'll need is a few basic things. First off, a to-do usually consists of a list. Also, this list contains to-do items that we can mutate.

From a state perspective, we can model this application like:

```
1
2
3
4
5
6
7
8
9
10``` | ```
{
 todo: {
 items: [
 {
 message: "Finish Redux blog post...",
 completed: false
 }
 ]
 }
}```
--------------------------- | --------------------------------------------------------------------------------------------------

### Add Actions

What would we like to do with this state? First off, we'd like to add new to-do items to it. Let's create an action that does that:

```
1
2
3
4
5
6
7``` | ```
function addTodo(message) {
 return {
 type: 'ADD_TODO',
 message: message,
 completed: false
 };
}```
-------------------- | ----------------------------------------------------------------------------------------------------------

Notice the `type` field. This should be a unique name that indicates the intention of your action. Convention is to format the type in uppercase and with underscore the as a word separator. Further on you'll use this name/identifier in your reducers in order to handle specific actions and turn them into state mutations.

Once we have added our to-do item, we definitely want to be able to mark it as completed, and we also want to be able to delete it, and maybe even clear all of the to-do items.

So let's also add actions for those things:

```
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19``` | ```
function completeTodo(index) {
 return {
 type: 'COMPLETE_TODO',
 index: index
 };
}
function deleteTodo(index) {
 return {
 type: 'DELETE_TODO',
 index: index
 };
}
function clearTodo() {
 return {
 type: 'CLEAR_TODO'
 };
}```
------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Now when we have our actions in place, let's continue by setting up our store. If you remember from earlier, the store is the central place in our Redux application, where all of the state, dispatched actions and reducers will live.

```
1
2
3
4
5
6
7
8
9
10
11
12``` | ```
import { createStore } from 'redux';
var defaultState = {
 todo:{
 items: []
 }
};
function todoApp(state, action) {
}
var store = redux.createStore(todoApp, defaultState);```
--------------------------------- | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Add Reducers

Now when we have some actions and a store to work with, let's create our first reducer. If you remember from earlier, a reducer is just an action handler which you can use to handle actions and mutate the state.

So let's start by handling the `ADD_TODO` action as shown below:

```
1
2
3
4
5
6
7
8
9
10
11
12
13
14``` | ```
function todoApp(state, action) {
 switch (action.type) {
 case 'ADD_TODO':
 return Object.assign({}, state, {
 items: items.concat([{
 message: action.message,
 completed: false
 }])
 });
 default:
 return state;
 }
}```
--------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Notice that when we say that a reducer "mutates" the state, if a state change needs to occur, what it actually should do is create a new copy of the state and make the changes to that. If there are no changes, then it's okay to return the same state. But in no case should you ever change the state directly since that would imply altering the state history.

Now when we have our first action handled, let's add support for the rest of them:

```
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45``` | ```
function todoApp(state, action) {
 switch (action.type) {
 case 'ADD_TODO':
 return Object.assign({}, state, {
 todo: {
 items: items.concat([{
 message: action.message,
 completed: false
 }])
 }
 });
 case 'COMPLETE_TODO':
 var items = [].concat(state.todo.items);
 items[action.index].completed = true;
 return Object.assign({}, state, {
 todo: {
 items: items
 }
 });
 case 'DELETE_TODO':
 var items = [].concat(state.todo.items);
 items.splice(action.index, 1);
 return Object.assign({}, state, {
 todo: {
 items: items
 }
 });
 case 'CLEAR_TODO':
 return Object.assign({}, state, {
 todo: {
 items: []
 }
 });
 default:
 return state;
 }
}```
------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Combine It All in a React UI

Now that we have the business logic in place, let's write some UI code. Since most of this is common React knowledge and very alike building a Flux application, I won't dig too deep into it.

```
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
90
91
92
93
94
95
96
97
98
99
100
101
102``` | ```
import React from 'react';
import ReactDOM from 'react-dom';
import { createStore } from 'redux';
var defaultState = {
 todo:{
 items: []
 }
};
// Add the actions here that we created in the previous steps...
function todoApp(state, action) {
 // Add the reducer logic that we added in the previous steps...
}
var store = redux.createStore(todoApp, defaultState);
class AddTodoForm extends React.Component {
 state = {
 message: ''
 };
 onFormSubmit(e) {
 e.preventDefault();
 store.dispatch(addTodo(this.state.message));
 this.setState({ message: '' });
 }
 onMessageChanged(e) {
 var message = e.value.trim();
 this.setState({ message: message });
 }
 render() {
 return (
 <form onSubmit={this.onFormSubmit.bind(this)}>
 <input type="text" placeholder="Todo..." onChange={this.onMessageChanged.bind(this)} value={this.state.message} />
 <button type="submit" value="Add" />
 </form>
 );
 }
}
class TodoItem extends React.Component {
 onDeleteClick() {
 store.dispatch(deleteTodo(this.props.index));
 }
 onCompletedClick() {
 store.dispatch(completeTodo(this.props.index));
 }
 render() {
 return (
 <li>
 <a href="#" onClick={this.onDeleteClick.bind(this)}>[x]</a>
 <a href="#" onClick={this.onCompletedClick.bind(this)}>{this.props.message}</a>
 </li>
 );
 }
}
class TodoList extends React.Component {
 state = {
 items: []
 };
 componentWillMount() {
 store.subscribe(() => {
 var state = store.state();
 this.setState({
 items: state.todo.items
 });
 });
 }
 render() {
 var items = [];
 this.state.items.forEach((item, index) => {
 items.push(<TodoItem
 index={index}
 message={item.message}
 completed={item.completed}
 />);
 });
 return (
 <ol>{ items }</ol>
 );
 }
}
ReactDOM.render(
 <div>
 <h1>Todo</h1>
 <AddTodoForm /><hr />
 <TodoList />
 </div>,
 document.getElementById('app')
);```
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

As you can see, building a UI for our Redux application wasn't that hard. The only difference is that you use `store.subscribe(listener)` to listen to state changes, and then `store.getState()` to retrieve the state. But besides that, it's very much like building a Flux application.

## Redux Support in Stormpath React SDK

Because there's been quite a few requests for adding Redux support to the [Stormpath React SDK](https://github.com/stormpath/stormpath-sdk-react), we went ahead and did that. If you want to configure the SDK to use Redux, simply configure the [Stormpath React SDK](https://github.com/stormpath/stormpath-sdk-react) with the `dispatcher` option and set the `type` to `redux` and point `store` to your Redux store as shown below:

```
1
2
3
4
5
6
7
8
9
10``` | ```
function myApp(state, action) {
 return state;
}
ReactStormpath.init({
 dispatcher: {
 type: 'redux',
 store: createStore(myApp)
 }
});```
--------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------

Once this is done you can intercept and handle all actions dispatched from the [Stormpath React SDK](https://github.com/stormpath/stormpath-sdk-react). For example, if you wish to enrich your state with user data, then simply handle the `USER_SET` action and add the user data to your state.

```
1
2
3
4
5
6
7
8
9
10
11``` | ```
function myApp(state, action) {
 switch (action.type) {
 case 'USER_SET':
 return Object.assign({}, state, {
 user: action.data
 });
 default:
 return state;
 }
}```
------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Wrapping Up

As you just saw in this post, building a Redux driven application is pretty easy and straight forward. It's very much like building one with Flux, just that the concepts are slightly different and there's less boilerplate code to write.

Hopefully you enjoyed following this tutorial and you'll find use of it in the future.

If there's anything that didn't work or that you had trouble with, please take a look at the [reference application](http://github.com/typerandom/stormpath-react-flux-todo-example-application).

And if you have any feedback, please let me know in the comment section below.

## Further Reading

Want to learn more about adding user authentication to React applications? Check out these tutorials:
