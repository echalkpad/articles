# JavaScript Design Patterns: Chain of Responsibility

[Original URL](http://www.joezimjs.com/javascript/javascript-design-patterns-chain-of-responsibility/)

> We've made it to the final installment in the JavaScript Design Patterns series. That's right, after this you'll no longer have any clue what post will be coming out every...

We've made it to the final installment in the JavaScript Design Patterns series. That's right, after this you'll no longer have any clue what post will be coming out every Monday! Well today, we'll be talking about the Chain of Responsibility Pattern. This pattern decouples the sender and receiver of a request. This is done with a chain of objects, each of which can handle the request itself or pass it on to the next object. Confused? Read on.

The rest of the JavaScript Design Patterns series:

## Chain of Responsibility Structure

There are three parts to the Chain of Responsibility pattern: sender, receiver, and request. The sender makes the request. The receiver is a chain of 1 or more objects that choose whether to handle the request or pass it on. The request itself can be an object that encapsulates all the appropriate data.

A sender sends the request to the first receiver object in the chain. The sender only knows about this first object and nothing about the other receivers. The first receiver either handles the request and/or passes it on to the next one in the chain. Each receiver only knows about the next receiver in the line. The request will keep going down the line until the request was handled or there are no more receivers to pass it on to, at which point either nothing happens or an error is thrown, depending on how you want it to work.

## Chains in the World Around Us

Event handling in the DOM uses one implementation of the Chain of Responsibility (amazing how many patterns are used together in the DOM, isn't it). Once an event is fired, it _bubbles_ up the DOM hierarchy, calling every event handler it runs into until it either gets to the end of the chain or a handler tells it to stop propagating.

## Chain of Responsibility Example

For our example today, we'll be creating an ATM. The Chain is going to consist of different sized bills. When you ask for some cash, the machine starts at the larger bills and pulls out as many as it needs, then moves on to the smaller bills. This example is very simple, which helps show the concept more clearly without diluting the code with implementations that are specific to the example.

![Chain of Responsibility:ATM](http://www.joezimjs.com/wp-content/uploads/chain_of_responsibility_atm.png "Chain of Responsibility:ATM")

We'll start by creating the receiver class: `MoneyStacks`. Normally this would just be an abstract class or interface that would be subclassed/implemented to create numerous different receivers, but this example is simple enough that the only variance between each of the receivers will be the size of the bills in the stack, so we can just set that number via a parameter in the constructor.

It's all pretty simple math. `withdraw` is the function that uses the chaining ability by ejecting the required bills and passing the request on when appropriate.

Now, we'll build the ATM. Its constructor creates all of the money stacks and puts them into their hierarchical order. When someone calls its `withdraw` method, it just passes on the responsibility to the chain of money stacks.

## Ending My Responsibilities

That's all there is to this pattern. It's pretty simple. Like the [Command](http://www.joezimjs.com/javascript/javascript-design-patterns-command/ "JavaScript Design Patterns: Command") and [Observer](http://www.joezimjs.com/javascript/javascript-design-patterns-observer/ "JavaScript Design Patterns: Observer") patterns, its purpose is to decouple senders and receivers but for different reasons and with different trade-offs. Due to its hierarchy structure, it's also similar to the [Composite](http://www.joezimjs.com/javascript/javascript-design-patterns-composite/ "JavaScript Design Patterns: Composite") pattern, and can also be injected within the Composite pattern to make some of the methods more efficient.

Well, it's been fun taking you through all of these JavaScript design patterns. I hope you've learned something along the way. If you haven't read through them all, I highly recommend that you do. The list is included below. Remember, though, that just because you know a pattern, doesn't mean it is required for the task at hand (you know the old "when all you have is a hammer, everything looks like a nail" saying).

I also ask that if you found any of these design pattern posts helpful that you go ahead and let me know through a comment or spread the post around the internet to help others like us grow in JavaScript wisdom. You can do that using the links below, if you wish. As always, Happy Coding!

JavaScript Design Patterns series:

Related posts:
