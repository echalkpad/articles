# Monads Explained Quickly

[Original URL](http://www.breck-mckye.com/blog/2016/04/monads-explained-quickly/)

> Most monad tutorials are long, confusing, and ineffective. I cannot promise this one will be more clear, more interesting, or even more effective, but I can at least promise to make it brisk. You...

Most monad tutorials are long, confusing, and ineffective. I cannot promise this one will be more clear, more interesting, or even more effective, but I can at least promise to make it brisk. You have nothing to lose by reading it.

Take a look at this object, `Foo`. It's a monad, but what that _means_ isn't relevant right now. Let's just focus on how Foo specifically works:

```
1
2
3
4
5
6
7``` | ```
function Foo(value) {
 this.get = ()=> value;
 this.map = fn => {
 let result = fn(value);
 return new Foo(result);
 };
}```
-------------------- | --------------------------------------------------------------------------------------------------------------------------------

Foo holds a single `value` that never changes. It provides a method for `getting` it, and a curious method named `map`. Map gets a function, passes it the `value`, and then returns the result in a new Foo.

Because map returns a new Foo, it's chainable:

```
1
2
3``` | ```
let one = new Foo(1);
let two = one.map(x => x + 7).map(x => x / 2).map(x => x - 2);
two.get() === 2;```
------------ | ------------------------------------------------------------------------------------------------------------

This chaining is kinda neat – it lets me write the operations I want to perform on _x_ in order, rather than with nested function calls, like below:

```
1``` | ```
let two = minusTwo(divideByTwo(addSeven(1))); // I have to read this right-to-left, which is awkward```
-------- | -----------------------------------------------------------------------------------------------------------

But the real value to an object like Foo is that if I put logic inside `map`, I can abstract away an action that I want to perform between each step of an operation.

Consider another monad, named `Bar`:

```
1
2
3
4
5
6
7
8``` | ```
function Bar(value) {
 this.get = ()=> value;
 this.map = fn => {
 let result = fn(value);
 console.log(result);
 return new Bar(result);
 };
}```
---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------

If I have an operation that makes multiple changes to a value, and I want to log how that value mutates at each turn, `Bar` lets me swap code like this...

```
1
2
3
4
5
6``` | ```
let stepOne = something(1);
console.log(stepOne);
let stepTwo = somethingElse(stepOne);
console.log(stepTwo);
let stepThree = somethingDifferent(stepTwo);
console.log(stepThree);```
------------------ | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

...for code like this:

```
1
2
3
4``` | ```
new Bar(1)
 .map(something) // console >> logs new value
 .map(somethingElse) // console >> logs new value
 .map(somethingDifferent); // console >> logs new value```
-------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**You now understand monads**. I did promise this would be quick. Monads can be boiled down - roughly - to the following rules:

1. A monad contains a value
2. A monad has a method named `map` that takes a function
3. Map has some logic around calling that function and processing the result
4. Map then returns the result in a new monad, so that calls to map can be chained

That's it. If you understand how the above code works, you (basically) understand monads. Sorry if you were hoping for something more magical and mind-bending.

We can do anything we like in `map`. Anything that we want to do between the steps of an operation – be that deciding how we pass the value into the next step, or doing something with the result that comes back out – we can put inside `map` and abstract away. Null checking is a great example:

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
function Maybe(value) {
 this.get = ()=> value;
 this.map = fn => {
 if (value === null) {
 return new Baz(null);
 } else {
 return new Baz(fn(value));
 }
 };
}```
--------------------------- | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------

In `Maybe`, map only calls the supplied function if it can hand it a valid, non-null value. Otherwise it quietly returns a clone of itself. This lets us turn verbose, repetitive code like this...

```
1
2
3
4``` | ```
let connection = getConnection();
let user = connection ? connection.getUser() : null;
let address = user ? user.getAddress() : null;
let zipCode = address ? address.getZip() : null;```
-------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

...into a much more elegant alternative:

```
1
2
3
4
5
6
7``` | ```
let zipCode =
 new Maybe(getConnection())
 .map(c => c.getUser())
 .map(u => u.getAddress())
 .map(a => a.getZip);
zipCode.get(); // returns either a zip code (if every step worked) or a null (if any step returned one)```
-------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Hopefully these two examples are enough to show you why monads and their map methods are so powerful. No doubt you can imagine uses of your own.

There are scores of other monads, all with quite diverse uses and functionality. But they all follow those same four rules we saw in `Foo` and `Bar`. If you follow them likewise you have access to a powerful tool indeed.
