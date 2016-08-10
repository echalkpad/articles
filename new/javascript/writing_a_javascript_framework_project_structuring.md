# Writing a JavaScript Framework - Project Structuring

[Original URL](https://blog.risingstack.com/writing-a-javascript-framework-project-structuring/)

> In the last couple of months Bertalan Miklos, JavaScript engineer at RisingStack wrote a next generation client-side framework, called NX. In the Writing a JavaScript Framework series, Bertalan...

In the last couple of months Bertalan Miklos, JavaScript engineer at RisingStack wrote a next generation client-side framework, called [NX](http://nx-nxframework.rhcloud.com). In the **Writing a JavaScript Framework** series, Bertalan shares what he learned during the process:

**In this chapter, I am going to explain how NX is structured, and how I solved its use case specific difficulties regarding extendibility, dependency injection and private variables.**

The series includes the following chapters.

1. Project structuring (current chapter)
2. Execution timing
3. Sandboxed code evaluation
4. Data binding (part 1)
5. Data binding (part 2)
6. Custom elements
7. Client side routing

## Project Structuring

There is no structure that fits all projects, although there are some general guidelines. Those who are interested can check out our [Node.js project structure tutorial](https://blog.risingstack.com/node-hero-node-js-project-structure-tutorial/) from the Node Hero series.

### An overview of the NX JavaScript Framework

NX aims to be an open-source community driven project, which is easy to extend and scales well.

- It has all the features expected from a modern client-side framework.
- It has no external dependencies, other than polyfills.
- It consists around 3000 lines altogether.
- No module is longer than 300 lines.
- No feature module has more than 3 dependencies.

Its final dependency graph looks like this:

![JavaScript Framework in 2016: The NX project structure](https://risingstack-blog.s3.amazonaws.com/2016/Jul/javascript_framework_in_2016_the_nx_project_structure-1467708098586.svg)

This structure provides a solution for some typical framework related difficulties.

- Extendibility
- Dependency injection
- Private variables

### Achieving Extendibility

Easy extendibility is a must for community driven projects. To achieve it, the project should have a small core and a predefined dependency handling system. The former ensures that it is understandable, while the latter ensures that it will stay that way.

In this section, I focus on having a small core.

The main feature expected from modern frameworks is the ability to create custom components and use them in the DOM. NX has the single `component` function as its core, and that does exactly this. It allows the user to configure and register a new component type.

```
component(config) 
 .register('comp-name')
```

The registered `comp-name` is a blank component type which can be instantiated inside the DOM as expected.

```
<comp-name></comp-name> 
```

The next step is to ensure that the components can be extended with new features. To keep both simplicity and extendibility, these new features should not pollute the core. This is where dependency injection comes handy.

## Dependency Injection (DI) with Middlewares

If you are unfamiliar with dependency injection, I suggest you to read our article on the topic : [Dependency Injection in Node.js](https://blog.risingstack.com/dependency-injection-in-node-js).

> Dependency injection is a design pattern in which one or more dependencies (or services) are injected, or passed by reference, into a dependent object.

DI removes hard burnt dependencies but introduces a new problem. The user has to know how to configure and inject all the dependencies. Most client-side frameworks have DI containers doing this instead of the user.

> A Dependency Injection Container is an object that knows how to instantiate and configure objects.

Another approach is the middleware DI pattern, which is widely used on the server side (Express, Koa). The trick here is that all injectable dependencies (middlewares) have the same interface and can be injected the same way. In this case, no DI container is needed.

I went with this solution to keep simplicity. If you ever used Express the below code will be very familiar.

```
component() 
 .use(paint) // inject paint middleware
 .use(resize) // inject resize middleware
 .register('comp-name')

function paint (elem, state, next) { 
 // elem is the component instance, set it up or extend it here
 elem.style.color = 'red'
 // then call next to run the next middleware (resize)
 next()
}

function resize (elem, state, next) { 
 elem.style.width = '100 px'
 next()
}
```

Middlewares execute when a new component instance is attached to the DOM and typically extend the component instance with new features. Extending the same object by different libraries leads to name collisions. Exposing private variables deepens this problem and may cause accidental usage by others.

Having a small public API and hiding the rest is a good practice to avoid these.

### Handling privacy

Privacy is handled by function scope in JavaScript. When cross-scope private variables are required, people tend to prefix them with `_` to signal their private nature and expose them publicly. This prevents accidental usage but doesn't avoid name collisions. A better alternative is the ES6 `Symbol` primitive.

> A symbol is a unique and immutable data type, that may be used as an identifier for object properties.

The below code demonstrates a symbol in action.

```
const color = Symbol()

// a middleware
function colorize (elem, state, next) { 
 elem[color] = 'red'
 next()
}
```

Now `'red'` is only reachable by owning a reference to the `color` symbol (and the element). The privacy of `'red'` can be controlled by exposing the `color` symbol to different extents. With a reasonable number of private variables, having a central symbol storage is an elegant solution.

```
// symbols module
exports.private = { 
 color: Symbol('color from colorize')
}
exports.public = {} 
```

And an `index.js` like below.

```
// main module
const symbols = require('./symbols') 
exports.symbols = symbols.public 
```

The storage is accessible inside the project for all modules, but the private part is not exposed to the outside. The public part can be used to expose low-level features to external developers. This prevents accidental usage since the developer has to explicitly require the needed symbol to use it. Moreover, symbol references can not collide like string names, so name collision is impossible.

The points below summarize the pattern for different scenarios.

**1\. Public variables**

Use them normally.

```
function (elem, state, next) { 
 elem.publicText = 'Hello World!'
 next()
}
```

**2\. Private variables**

Cross-scope variables, that are private to the project should have a symbol key added to the private symbol registry.

```
// symbols module
exports.private = { 
 text: Symbol('private text')
}
exports.public = {} 
```

And required from it when needed somewhere.

```
const private = require('symbols').private

function (elem, state, next) { 
 elem[private.text] = 'Hello World!'
 next()
}
```

**3\. Semi-private variables**

Variables of the low level API should have a symbol key added to the public symbol registry.

```
// symbols module
exports.private = { 
 text: Symbol('private text')
}
exports.public = { 
 text: Symbol('exposed text')
}
```

And required from it when needed somewhere.

```
const exposed = require('symbols').public

function (elem, state, next) { 
 elem[exposed.text] = 'Hello World!'
 next()
}
```

### Conclusion

If you are interested in the NX framework, please [visit the home page](http://nx-nxframework.rhcloud.com). Adventurous readers can find the NX source code in [this Github repository](https://github.com/RisingStack/nx-framework).

I hope you found this a good read, **see you next time when I'll discuss execution timing!**

If you have any thoughts on the topic, share it in the comments.
