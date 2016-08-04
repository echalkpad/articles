# Promises - A gentle introduction

[Original URL](http://bytearcher.com/articles/es6-promise-then-beginner-tutorial/)

> In order to get anything done in Node.js, you are destined to use asynchronous functions. They prepare an operation and accept a callback that is to be called when the results are in. For your...

In order to get anything done in Node.js, you are destined to use asynchronous functions. They prepare an operation and accept a callback that is to be called when the results are in. For your program to work correctly, you typically need to the results of the previous call in your further steps. What you end up with is nesting rest of your script inside a callback. Doing this multiple times leads to code being nested multiple levels.

Nesting code excessively in callbacks may seem counterintuitive and messy. Promises allow you to express a chain of dependent steps without having to excessively nest code.

## Callback is called when the results are in

Callbacks are the traditional way of making asynchronous calls. You make a call and pass a function argument that is called at a later time when the operation is complete.

Let us demonstrate callbacks with an example. Let us write a program that will get the current time as a string from `http://www.timeapi.org/utc/now`, write it to disk and ultimately re-read it for printing.

```
var fs = require('fs');
var request = require('request');

request('http://www.timeapi.org/utc/now', { headers: { 'User-Agent': 'request' } }, (error, response, time) => {
 fs.writeFile('time.txt', time, (err) => {
 fs.readFile('time.txt', 'utf8', (err, readData) => {
 // this block is nested three levels
 console.log(readData);
 });
 });
});
```

Notice how this short example goes to three levels of nested code.

## Promise gives you something to work on right now

When performing an asynchronous call, it is impossible, by definition, to get the results at the time of making the call. This means that any further processing steps are not evaluated until the callback is called and the results arrive.

With Promises, this approach is reversed and multiple processing steps are defined in one sitting. In order to do this, something is needed to represent the results while they don't exist yet.

To avoid waiting for the results to arrive before defining next steps, a Promise is used as a stand-in for a result.

> Promises are a way of saying: _"I can't give you the results right now, but here's something to work on."_

This stand-in can be used to schedule a dependent step that is to be executed when the actual results arrive. Multiple steps can be chained to create a sequence of steps.

The example written using Promises looks like this.

```
var fs = require('fs-promise');
var request = require('request-promise');

request('http://www.timeapi.org/utc/now', { headers: { 'User-Agent': 'request' } }).then((time) => {
 return fs.writeFile('time.txt', time);
}).then(() => {
 return fs.readFile('time.txt', 'utf8');
}).then((readData) => {
 // this block is nested one level
 console.log(readData);
});
```

In order to use Promises, the depended upon libraries were replaced with versions returning Promises. Notice how the code is nested at most one level instead of three levels.

## `.then()` schedules a dependent step

```
...
}).then((readData) => {
...
```

A dependent step is scheduled with the `.then()` method. It takes a function argument that accepts one parameter for passing the results from the previous step. At most one result value can be passed on to the next step.

```
...
 return fs.readFile('time.txt', 'utf8');
}).then((readData) => {
...
```

A step is ended by returning a value or returning another Promise. In the latter case the Promise is evaluated and its result value will be passed on to the next step.

## `.catch()` handles problems

If `.then()` is used to define a normal next step, there is similar method `.catch()` that is used for handling errors. Errors can originate from throwing an exception in your step code or they can originate from the internal implementation of an API call.

```
fs.readFile('time.txt', 'utf8').then((data) => {
 throw new Error();
}).then(() => {
 // skipped
}).catch((e) => {
 console.log('aborted', e);
});
```

When an error occurs, execution of current function will stop and execution is continued at nearest following `.catch()` handler. This may cause skipping any amount `.then()` handlers in between.

Using `catch()` handlers ease up error handling. It removes the need to pass on separate `err` objects as seen with callbacks.

## Things to watch out for

Promises are useful and help preventing code marching excessively to the right, but there are a couple of things to watch out.

Only one value can be passed from a step to another. If you need to pass more than one value, you can compose an object literal and return it.

If you need to reference more values than just the result of the immediate preceding step you need to arrange your code to allow this. There are various ways to achieve, for details see for example this [stackoverflow question](http://stackoverflow.com/questions/28250680/how-do-i-access-previous-promise-results-in-a-then-chain).

All of these lead to nesting or otherwise complicating code. The goal of Promises was to create cleaner asynchronous code. In parts, Promises do not fully achieve this goal.

## ES6 specification

Promises are not the only attempt at solving the infinite nesting callbacks problem. Other available options are libraries such as async, co, generators and fibers to name just a few. But, today Promises are part of the ES6 specification. That merit is hard to ignore when evaluating the available options. Are the other attempts any better? We can make no such promises.
