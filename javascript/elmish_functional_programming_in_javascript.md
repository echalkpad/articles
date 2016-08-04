# Elmish: Functional Programming in Javascript

[Original URL](https://medium.com/@chetcorcos/elmish-functional-programming-in-javascript-50995f1d4b9e#.lgnk6tq4u)

> For the past few months, I've been on adventure learning everything I could about functional programming. It seems like every week there's a new frontend framework or library claiming some...

For the past few months, I've been on adventure learning everything I could about functional programming. It seems like every week there's a new frontend framework or library claiming some inspiration from functional programming, so I decided to check it out. One of my explorations was into [Elm](http://elm-lang.org): an amazing Haskell-inspired language for building web applications. It gave me a taste of what functional programming really means. What really drew me toward Elm was the [time-travelling debugger](http://debug.elm-lang.org) and the amazing abstraction power [Elm Architecture Tutorial](https://github.com/evancz/elm-architecture-tutorial).

It took me a while to understand the Elm language (if you're interested in learning more , I highly recommend[this free online class](https://www.edx.org/course/introduction-functional-programming-delftx-fp101x-0) that teaches Haskell). But as I wasn't entirely fluent in Elm, I found it hard to really get going with my ideas. So thought **I'd try to test my understanding of the Elm Architecture by implementing the same concepts in Javascript**. If you've used [React](https://facebook.github.io/react/) before, you're probably familiar with [Redux](https://github.com/reactjs/react-redux), which is actually directly [inspired by Elm](https://github.com/reactjs/redux#influences), so hopefully you'll notice some similarities.

> The following code is all available in my [elmish github repo](https://github.com/ccorcos/elmish/tree/master/tutorial).

In Elm, everything is a pure function. A component is modeled as a finite state machine using 3 functions:

```
init :: () => state
update :: (state, action) => state
view :: (dispatch, state) => html
```

The update function is a _reducer._ It gets the current state and an action, and produces the next state which gets rendered using a virtual DOM library such as React. To send actions to update the state, you simply have to call dispatch with the action. And thats basically it! Let me show you a simple counter example:

![](https://cdn-images-1.medium.com/max/800/1*WFb799uyNMvM0cR8OxQufQ.png)

Now all thats left to do is connect the pieces. We need to get the initial state of the component and render the component using the view function. Now the dispatch function is tricky. The dispatch function needs to update the state and then re-render. Its a nasty bit of code, but I think it worth looking at and understanding:

![](https://cdn-images-1.medium.com/max/800/1*jlKrsIGB1gfkWm6IhAqHUg.png)

This section is arguably more stylistic than anything, but it introduces some great functional programming concepts that make this code a lot more readable. However, may take some time to wrap your head around it if this is the first time you've heard of [function currying](https://hughfdjackson.com/javascript/why-curry-helps/) or[observable streams](https://www.youtube.com/watch?v=XE692Clb5LU).

To clean things up a little, we get to use my two absolute favorite Javascript libraries, [Ramda](http://ramdajs%20github) and [Flyd](https://github.com/paldepind/flyd). Ramda is [like underscore, but with an eye for function composition](https://www.youtube.com/watch?v=m3svKOdZijA), and Flyd is a simple and intuitive observable streams library. Lets see what these tools can do for us:

![](https://cdn-images-1.medium.com/max/800/1*fqmEZwjrRrVMXJIvi6e9mA.png)

Streams are like lazy arrays. You can map, filter, and reduce (called scan) over them, and you can emit values to them my simply calling them as functions. Its also a convention to keep track of which variables are streams by suffixing with a $.

In the code above, we're creating an action stream, and scanning the action stream over the update function with an initial state. Perhaps the easiest way to understand what scan does is by looking at the [ReactiveX graphics](http://reactivex.io/documentation/operators/scan.html):

![](https://cdn-images-1.medium.com/max/800/1*iSQBGG5Kipw57925DWFdLg.png)

Then with a stream of states, we map over the view function to get a stream of virtual DOM trees. But we did something nifty there. We're partially applying the _view_ function with the action stream. [This talk](https://www.youtube.com/watch?v=m3svKOdZijA&app=desktop) will help you understand currying better, but the point is that when you curry a function and call it with arguments, it will keep returning a function until it has all the arguments the function needs. This is essentially a more generalized concept of partial application. The key thing to realize here is that the dispatch function is really just a way of inputing values back into the action stream.

Enough with this, lets get back to Elmish.

One benefit of building components using the init-update-view pattern is composition. You may want to look up some of the Ramda functions if you're unfamiliar with them, but the beauty of Ramda is that once you understand it, this code should read very declaratively:

![](https://cdn-images-1.medium.com/max/800/1*xknatibl_SUsJBrhX0dN5A.png)

The _listOf_ component is a higher-order component that creates lists of other components. To insert an item, we simply assign an id to the item and init the state of the subcomponent. The other crucial thing is that we wrap the child actions in a special _type: "child"_ by passing _childDispatch_ function to the child _view_ function that forwards the actions to _dispatch._ That way, in the update function, we can lookup which child the action belongs to by id, and update that child's state using the child's update function.

Now this may seem a little bit verbose and I agree that it is, but its the price we pay from amazing abstraction power. If we want a list of counters now, all we need to do is:

```
start(listOf(counter))
```

What really shows the abstraction capabilities is the fact that you can trivially create a list of a list of counters.

```
start(listOf(listOf(counter)))
```

Notice that this kind of abstraction is something you cannot currently do with Redux. That's because we aren't passing the dispatch function all the way down through the components so all actions are global. Anyways, lets check out another higher-order component.

My favorite example that shows the abstraction power of the Elm Architecture is the undoable component. It wraps a component and keeps track of a history of states so you can undo and redo the entire history. And again, this code reads very much like a specification to me. Undo is just decrementing time. Redo is incrementing time. And a child action means taking the current state, updating it to get the next state, and concatenating it onto the past while incrementing the time:

![](https://cdn-images-1.medium.com/max/800/1*uyvxcgM7QkfLGGwWRUTf9A.png)

What really starts to amaze me is how we can start to mix and match these components:

```
start(undoable(listOf(counter)))
```

Voila! It just works, and flawlessly.

So that's it for now. The Elm Architecture is a really cool pattern and gives you magical abstraction power via functional composition. Its not very hard to imagine creating a time-travelling debugger by taking the undoable component and putting a slider at the bottom of the screen.

Two other features that really excite me are reproducable error reporting and automated testing. If you run into a runtime exception, why not send the initial state and all actions the user took to the server so you can exactly reproduce the exception? And since all these functions are pure, you can record all actions and states as you're using the app to generate test cases just by clicking around and using the app!

There are sadly a couple drawbacks to this approach that I've noticed so far:

- There's an annoying amount of boilerplate needed to wrap child actions. I think [Clojurescript](https://github.com/clojure/clojurescript) is an interesting option to alleviate this pain because of its incredibly powerful macros, but I don't anticipate doing that anytime soon. Perhaps there's some Javascript abstraction I haven't thought of yet.

- The entire DOM tree is re-computed after every action. The Elm community would tell you "premature optimization is the root of all evil" and while I don't disagree, I do think that this will eventually be a problem if you're running lots of animations through the action-update cycle. It is possible to wrap these Elmish components in React components to introduce lazy evaluation. But in the _listOf_ component, for example, you'll still be passing a new bound function to the child component on every update, forcing a re-render of every item in the list. It would be **amazing** if there was some kind of standard for deducing equality of _bound pure functions_. That is:

<!--  -->

```
const add = (a,b) => a + b
const f1 = bind(add, 1)
const f2 = bind(add, 1)
// ideally
f1 === f2
// pragmatically
f1.eq(f2)
```

The last thing we didn't talk about yet is _side-effects._ Side-effects are typically the culprit of making your entire app a mess. Maybe if I have some time next week and people are interested, I'll write another article about that.

In the meantime, you can see all these examples, and more in my [Elmish repo](https://github.com/ccorcos/elmish/tree/master/tutorial). Please let me now what you think! And don't hesitate to create an issue ticket if you have any questions, ideas, or just want to discuss.
