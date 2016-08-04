# async/await: It's Good and Bad

[Original URL](https://medium.com/@benlesh/async-await-it-s-good-and-bad-15cf121ade40#.qmrf59w0c)

> This might be controversial, as most people I know are huge fans of async/await. If you're not familiar, it's this nifty little syntax addition that allows developers to deal with APIs that return...

This might be controversial, as most people I know are _huge_ fans of async/await. If you're not familiar, it's this nifty little syntax addition that allows developers to deal with APIs that return Promises in a more "native" way:

```
async function doSomethingCool() {
 let someValue = await getSomePromise();
 console.log(someValue + '!!!');
}
```

As opposed to:

```
function doSomethingCool() {
 getSomePromise.then(someValue => someValue + '!!!');
}
```

In the examples above, assume `getSomePromise` is a function that returns a Promise (It's a huge leap I know).

I remember several years ago when async await hit C# and people were very excited about it. It promised to make asynchrony "easier to understand" (at least by some of my coworkers' estimations). And people liked it because it was "cleaner". Now we have async await landing in JavaScript and I hear the same sort of chants, but I find its usefulness in making things "easier to understand" dubious.

1. **Things that would normally take many functions to compose can now be composed in one function!** There isn't a lot more to say here. Visual complexity is reduced. Instead of 2N curly brackets, there are just 2 curly brackets around one glorious function. It looks and feels _simpler_ somehow.

2. **No longer getting stuck with endless `then` chains.** For some people this is a huge turn-off about promises and in some cases functional programming in general: Chains of methods accepting functions. It can honestly be hard for some to wrap their heads around. I don't mind it at all, I work on a whole library built around that concept, but I get it.

3. **try/catch blocks work smoothly over async code.** This is probably my favorite feature of async await. A way to force Promise errors to act a little more like "regular JavaScript" errors. Consider the following:

<!--  -->

```
let bad = () => Promise.reject('bad');

async function coolWay() {
 try {
 let result = await bad();
 doSomething(result);
 } catch (err) {
 console.error('An error!');
 }
}

let bad = () => Promise.reject('bad');

function lameOldWay() {
 bad()
 .then(doSomething)
 .then(null, err => console.error('An error!'));
}
```

In my opinion, with the first example it's much more obvious that the error handling in the catch block handles any error that occurs in either the call to `bad()` or `doSomething(result)`. Whereas with the second example, unless you're really familiar with Promise chaining, it's not readily, visually apparent where error handling is or what it's covering. It's not really a big deal, though, and it's in the eye of the beholder.

1. **Things that would normally take many functions to compose can now be composed in one function!** (You'll notice this is the same as the "pro" above) One problem I have with this is when the code is all in one function, it's easier to allow mutable state to bleed across what are really separate events. Another thing is that when it is broken up into many functions its a better visual queue that "Hey, each part of this is going to happen in it's own event". When they're all munged together into a single function, then any number of individual events are actually handled within that one function. So it's not really "one function" any more, it's several functions joined together into one. Many pieces that run at different points in time are glued together without any easy to discern boundaries.

2. **Often the transpiled version of the code is quite verbose and ugly.** At this particular point in time, if you really want to use async/await, you need to transpile it with something like [Babel](http://babeljs.io) (which is awesome, BTW). The problem is that often something you could have written in 2–3 lines with the regular promise API can be dozens of lines once transpiled. Take a look at the screen capture below of roughly equivalent async/await and straight promise versions of some logic. Consider the following equivalent uses of async/await and Promise chaining:

![](https://cdn-images-1.medium.com/max/1200/1*9BHTR96pWXQUnwwCRJZGcw.png)

Transpiled output of async await in BabelJS ![](https://cdn-images-1.medium.com/max/1200/1*zcMq_wFztO99Vo9sk_1pFg.png)

Transpiled output of comparable promise chain in async await in BabelJS

And the generated code isn't even all of it: You'll notice a reference to a `regeneratorRuntime` in there. This may change in newer versions of Babel, of course, but that's not going to change the fact that there is a lot more code to execute to get this abstraction. All abstraction comes at a cost. In this case, I'm not entirely convinced the cost is always worth it.

**EDIT:** [Stefan Penner](https://medium.com/u/86e3df6a6b21) [brought up another valid point](https://twitter.com/stefanpenner/status/702654894126149632), which is that `this` will be carried throughout the life of your async function. I'd lump that in as a "con", because it's going to stay in memory and a long-lived await might just result in retaining or even leaking resources you didn't want to keep around.

**EDIT EDIT:** Again, [Stefan Penner](https://medium.com/u/86e3df6a6b21)'s valid point: leaking `this` can happen in any async paradigm, including callbacks. It's just that here it could end up much "broader". Also carrying `this` through the function is sort of a "pro" as it's a feature.

You can use both promise chains and async await, and there's nothing wrong with that. Since `then` returns a Promise, you can await the promise it returns. I'm not sure what this approach will buy you, but if it suits a particular use-case and makes the code more readable, go for it.

It's best to know what's going on with async/await. I'd wager that in most cases it probably doesn't matter much whether or not you choose to use a promise chain or async/await. It's really a matter of knowing the trade-offs and choosing whichever approach makes your code the most readable for your maintainers. If either approach is readable to maintainers, then I'd personally lean toward the promise chaining myself, as it's more direct, probably more performant, and definitely outputs smaller code to push to the browser. The best feature of async await is making handling unhandled errors from promises much more natural to the language by enabling you to use a simple try/catch block.
