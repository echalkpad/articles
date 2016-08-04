# Higher Order Components: A React Application Design Pattern

[Original URL](https://www.sitepoint.com/react-higher-order-components/)

> This article is by guest author Jack Franklin. SitePoint guest posts aim to bring you engaging content from prominent writers and speakers of the JavaScript community In this article we will discuss...

_This article is by guest author **Jack Franklin**. SitePoint guest posts aim to bring you engaging content from prominent writers and speakers of the JavaScript community_

In this article we will discuss how to use Higher Order Components to keep your React applications tidy, well structured and easy to maintain. We'll discuss how pure functions keep code clean and how these same principles can be applied to React components.

## Pure Functions

A function is considered pure if it adheres to the following properties:

- All the data it deals with are declared as arguments
- It does not mutate data it was given or any other data (these are often referred to as _side effects_).
- Given the same input, it will _always_ return the same output.

For example, the `add` function below is pure:

```js
function add(x, y) {
 return x + y;
}
```

However, the function `badAdd` below is impure:

```js
var y = 2;
function badAdd(x) {
 return x + y;
}
```

This function is not pure because it references data that it hasn't directly been given. As a result, it's possible to call this function with the same input and get different output:

```js
var y = 2;
badAdd(3) // 5
y = 3;
badAdd(3) // 6
```

To read more about pure functions you can read ["An introduction to reasonably pure programming"](https://www.sitepoint.com/an-introduction-to-reasonably-pure-functional-programming/) by Mark Brown.

Whilst pure functions are very useful, and make debugging and testing an application much easier, occasionally you will need to create impure functions that have side effects, or modify the behavior of an existing function that you are unable to access directly (a function from a library, for example). To enable this we need to look at higher order functions.

## Higher Order Functions

A higher order function is a function that when called, returns another function. Often they also take a function as an argument, but this is not required for a function to be considered higher order.

Let's say we have our `add` function from above, and we want to write some code so that when we call it we log the result to the console before returning the result. We're unable to edit the `add` function, so instead we can create a new function:

```js
function addAndLog(x, y) {
  var result = add(x, y);
  console.log('Result', result);
  return result;
}
```

We decide that logging results of functions is useful, and now we want to do the same with a `subtract` function. Rather than duplicate the above, we could write a _higher order function_ that can take a function and return a new function that calls the given function and logs the result before then returning it:

```js
function logAndReturn(func) {
  return function() {
    var args = Array.prototype.slice.call(arguments);
    var result = func.apply(null, args);
    console.log('Result', result);
    return result;
  }
}
```

Now we can take this function and use it to add logging to `add` and `subtract`:

```js
var addAndLog = logAndReturn(add);
addAndLog(4, 4) // 8 is returned, ‘Result 8’ is logged

var subtractAndLog = logAndReturn(subtract);
subtractAndLog(4, 3) // 1 is returned, ‘Result 1’ is logged;
```

`logAndReturn` is a HOF because it takes a function as its argument and returns a new function that we can call. These are really useful for wrapping existing functions that you can't change in behavior. For more information on this, check M. David Green's article ["Higher-Order Functions in JavaScript](https://www.sitepoint.com/higher-order-functions-javascript/) which goes into much more detail on the subject.

Additionally you can check out [this CodePen](https://codepen.io/SitePoint/pen/akKmYp?editors=0012), which shows the above code in action.

## Higher Order Components

Moving into React land, we can use the same logic as above to take existing React components and give them some extra behaviours.

In this section we're going to use [React Router](https://github.com/reactjs/react-router), the de facto routing solution for React. If you'd like to get started with the library I highly recommend the [React Router Tutorial](https://github.com/reactjs/react-router-tutorial) on GitHub.

React Router provides a `<Link>` component that is used to link between pages in a React application. One of the properties that this `<Link>` component takes is `activeClassName`. When a `<Link>` has this property and it is currently active (the user is on a URL that the link points to), the component will be given this class, enabling the developer to style it.

This is a really useful feature, and in our hypothetical application we decide that we always want to use this property. However, after doing so we quickly discover that this is making all our `<Link>` components very verbose:

```html
<Link to="/" activeClassName="active-link">Home</Link>
<Link to="/about" activeClassName="active-link">About</Link>
<Link to="/contact" activeClassName="active-link">Contact</Link>
```

Notice that we are having to repeat the class name property every time. Not only does this make our components verbose, it also means that if we decide to change the class name we've got to do it in a lot of places.

Instead, we can write a component that wraps the `<Link>` component:

```js
var AppLink = React.createClass({
  render: function() {
    return (
      <Link to={this.props.to} activeClassName="active-link">
      {this.props.children}
      </Link>;
    );
  }
});
```

And now we can use this component, which tidies up our links:

```html
<AppLink to="/">Home</AppLink>
<AppLink to="/about">About</AppLink>
<AppLink to="/contact">Contact</AppLink>
```

You can [see this example working on Plunker](https://plnkr.co/edit/ohD1yy?p=preview).

In the React ecosystem these components are known as higher order components because they take an existing component and manipulate it slightly _without changing the existing component_. You can also think of these as wrapper components, but you'll find them commonly referred to as higher order components in React-based content.

## Functional, Stateless Components

React 0.14 introduced support for functional, stateless components. These are components that have the following characteristics:

- They do not have any state.
- They do not use any React lifecycle methods (such as `componentWillMount()`).
- They only define the `render` method and nothing more.

When a component adheres to the above, we can define it as a function, rather than using `React.createClass` (or `class App extends React.Component` if you're using ES2015 classes). For example, the two expressions below both produce the same component:

```js
var App = React.createClass({
  render: function() {
    return <p>My name is { this.props.name }</p>;
  }
});

var App = function(props) {
 return <p>My name is { props.name }</p>;
}
```

In the functional, stateless component instead of referring to `this.props` we're instead passed `props` as an argument. You can read more about this [on the React documentation](https://facebook.github.io/react/docs/reusable-components.html#stateless-functions).

Because higher order components often wrap an existing component you'll often find you can define them as a functional component. For the rest of this article I'll do that whenever possible.

## Better Higher Order Components

The above component works, but we can do much better. The `AppLink` component that we created isn't quite fit for purpose.

### Accepting multiple properties

The `<AppLink>` component expects two properties:

- `this.props.to` which is the URL the link should take the user to
- `this.props.children` which is the text shown to the user

However, the `<Link>` component accepts many more properties, and there might be a time when you want to pass extra properties along with the two above which we nearly always want to pass. We haven't made `<AppLink>` very extensible by hard coding the exact properties we need.

### The JSX spread

JSX, the HTML-like syntax we use to define React elements, supports the spread operator for passing an object to a component as properties. For example, the code samples below achieve the same thing:

```js
var props = { a: 1, b: 2};
<Foo a={props.a} b={props.b} />

<Foo {...props} />
```

Using `{...props}` spreads each key in the object and passes it to `Foo` as an individual property.

We can make use of this trick with `<AppLink>` so we support any arbitrary property that `<Link>` supports. By doing this we also future proof ourselves; if `<Link>` adds any new properties in the future our wrapper component will already support them. While we're at it, I'm also going to change `AppLink` to be a functional component.

```js
var AppLink = function(props) {
  return <Link {...props} activeClassName="active-link" />;
}
```

Now `<AppLink>` will accept any properties and pass them through. Note that we also can use the self closing form instead of explicitly referencing `{props.children}` in-between the `<Link>` tags. React allows `children` to be passed as a regular prop or as child elements of a component between the opening and closing tag.

[You can see this working on Plunker](https://plnkr.co/edit/hgBZ96?p=preview).

### Property ordering in React

Imagine that for one specific link on your page, you have to use a different `activeClassName`. You try passing it into `<AppLink>`, since we pass all properties through:

```js
<AppLink to=“/special-link” activeClassName=“special-active”>Special Secret Link</AppLink>
```

However, this doesn't work. The reason is because of the ordering of properties when we render the `<Link>` component:

```js
return <Link {...props} activeClassName="active-link" />;
```

When you have the same property multiple times in a React component, the _last declaration wins_. This means that our last `activeClassName=“active-link”` declaration will always win, since it's placed _after_ `{...this.props}`. To fix this we can reorder the properties so we spread `this.props` last. This means that we set sensible defaults that we'd like to use, but the user can override them if they really need to:

```js
return <Link activeClassName="active-link" {...props} />;
```

Once again, you can [see this change in action on Plunker](https://plnkr.co/edit/imLPgAQAB430lT1oBdU2?p=preview).

By creating higher order components that wrap existing ones but with additional behavior, we keep our code base clean and defend against future changes by not repeating properties and keeping their values in just one place.

## Higher Order Component Creators

Often you'll have a number of components that you'll need to wrap in the same behavior. This is very similar to earlier in this article when we wrapped `add` and `subtract` to add logging to them.

Let's imagine in your application you have an object that contains information on the current user who is authenticated on the system. You need some of your React components to be able to access this information, but rather than blindly making it accessible for every component you want to be more strict about which components receive the information.

The way to solve this is to create a function that we can call with a React component. The function will then return a new React component that will render the given component but with an extra property which will give it access to the user information.

That sounds pretty complicated, but it's made more straightforward with some code:

```js
function wrapWithUser(Component) {
  // information that we don’t want everything to access
  var secretUserInfo = {
    name: 'Jack Franklin',
    favouriteColour: 'blue'
  };

  // return a newly generated React component
  // using a functional, stateless component
  return function(props) {
  // pass in the user variable as a property, along with
  // all the other props that we might be given
  return <Component user={secretUserInfo} {...props} />
  }
}
```

The function takes a React component (which is easy to spot given React components have to have capital letters at the beginning) and returns a new function that will render the component it was given with an extra property of `user`, which is set to the `secretUserInfo`.

Now let's take a component, `<AppHeader>`, which wants access to this information so it can display the logged in user:

```js
var AppHeader = function(props) {
  if (props.user) {
    return <p>Logged in as {props.user.name}</p>;
  } else {
    return <p>You need to login</p>;
  }
}
```

The final step is to connect this component up so it is given `this.props.user`. We can create a new component by passing this one into our `wrapWithUser` function.

```js
var ConnectedAppHeader = wrapWithUser(AppHeader);
```

We now have a `<ConnectedAppHeader>` component that can be rendered, and will have access to the `user` object.

[View this example on CodePen](http://codepen.io/SitePoint/pen/oLyzdo?editors=0010) if you'd like to see it in action.

I chose to call the component `ConnectedAppHeader` because I think of it as being connected with some extra piece of data that not every component is given access to.

This pattern is very common in React libraries, particularly in [Redux](http://redux.js.org), so being aware of how it works and the reasons it's being used will help you as your application grows and you rely on other third party libraries that use this approach.

## Conclusion

This article has shown how, by applying principles of functional programming such as pure functions and higher order components to React, you can create a codebase that's easier to maintain and work with on a daily basis.

By creating higher order components you're able to keep data defined in only one place, making refactoring easier. Higher order function creators enable you to keep most data private and only expose pieces of data to the components that really need it. By doing this you make it obvious which components are using which bits of data, and as your application grows you'll find this beneficial.

If you have any questions I'd love to hear them; feel free to leave a comment or grab me as [@Jack_Franklin](http://twitter.com/jack_franklin) on Twitter.
