# The future of bundling JavaScript modules

[Original URL](http://www.2ality.com/2015/12/bundling-modules-future.html)

> This blog post examines how the bundling of modules is affected by two future developments: HTTP/2 and native modules. Bundling modules means combining several files with modules into a single file....

This blog post examines how the bundling of modules is affected by two future developments: HTTP/2 and native modules.

[]() Bundling modules means combining several files with modules into a single file. That is done for three reasons:

1. Fewer files need to be retrieved in order to load all modules.
2. Compressing the bundled file is slightly more efficient than compressing separate files.
3. During bundling, unused exports can be removed, potentially resulting in significant space savings.

## JavaScript modules

With ECMAScript 6, JavaScript finally got built-in modules (I'm calling them _JavaScript modules_ for the remainder of this blog post). However, that feature is currently in a strange position:

On one hand, ES6 fully standardized their syntax and much of their semantics. They have become a popular format for writing modules and their static structure enables the automatic omission of unused exports (also known as "tree-shaking" in the JavaScript world).

On the other hand, standardizing how to load JavaScript modules is ongoing and no JavaScript engine supports them natively, yet. That means that, at the moment, the only way of using JavaScript modules is by compiling them to a non-native format. Popular solutions are: browserify, webpack, jspm and Rollup.

## Future developments and bundling

Let's look at two future developments and how they affect the bundling of JavaScript modules.

### Future development: HTTP/2

HTTP/2 is slowly being rolled out. It mainly affects reason #1 for bundling: With HTTP/2, the cost per request has decreased considerably compared to HTTP/1, which means that there are practically no performance gains if you download a single file instead of multiple ones. That enables smaller, more incremental updates: With bundling, you always need to download the complete bundle. Without bundling, you only need to download the parts that have changed (while the other parts are often still in the browser cache).

However, reasons #2 and #3 for bundling are not negated by HTTP/2\. Therefore, mixed approaches may be adopted in the future, to optimize for both incremental updates and minimal total download size.

### Future development: native JavaScript modules

Once engines support native JavaScript modules, will that affect bundling? Even AMD modules – which run natively in browsers – have [a custom bundle format](http://requirejs.org/docs/optimization.html) (along with a minimal loader). Will native JS modules be different? It looks like they will. Rollup lets you bundle multiple JS modules into a single JS module.

Take, for example, these two JS modules:

```
 // lib.js
 export function foo() {}
 export function bar() {}

 // main.js
 import {foo} from './lib.js';
 console.log(foo());
```

Rollup can bundle these two JS modules into the following single JS module (note the eliminated unused export `bar`):

```
 function foo() {}

 console.log(foo());
```

Initially, it wasn't a given that JavaScript modules would work as a bundle format – [quoting Rollup's creator Rich Harris](https://github.com/rollup/rollup/issues/219#issuecomment-150842108):

> When I started writing Rollup, it was an experiment that I wasn't certain would succeed.

The way imports are handled by JS modules helps with bundling: [they are not copies of exports, they are read-only views on them](http://exploringjs.com/es6/ch_modules.html#_imports-are-read-only-views-on-exports).

[Rollup's site](http://rollupjs.org/) has a nice interactive playground where you can try it out.

## Further reading

- "[Building for HTTP/2](http://rmurphey.com/blog/2015/11/25/building-for-http2)" by Rebecca Murphey (explains how best practices change – often radically – with this new version of HTTP)
- Chap. "[Modules](http://exploringjs.com/es6/ch_modules.html)" in "Exploring ES6" (explains how ES6 modules work)
- "[Babel and CommonJS modules](http://www.2ality.com/2015/12/babel-commonjs.html)" (explains how Babel ensures that transpiled ES6 modules interoperate properly with CommonJS modules)
