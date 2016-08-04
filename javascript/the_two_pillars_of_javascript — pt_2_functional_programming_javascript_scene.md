# The Two Pillars of JavaScript -- Pt 2: Functional Programming -- JavaScript Scene

[Original URL](https://medium.com/javascript-scene/the-two-pillars-of-javascript-pt-2-functional-programming-a63aa53a41a4#.kc2im991x)

> Two Pillars of JavaScript Part 1: "How to Escape the 7th Circle of Hell". From the introduction:JavaScript is one of the most important programming languages of all time, not simply...

[Two Pillars of JavaScript Part 1: "How to Escape the 7th Circle of Hell"](https://medium.com/javascript-scene/the-two-pillars-of-javascript-ee6f3281e7f3). From the introduction:

> JavaScript is one of the most important programming languages of all time, not simply because of its popularity, but because it popularized **two features which are extremely important for the evolution of programming:**

> **Prototypal Inheritance** (objects without classes, and prototype delegation, aka OLOO -- Objects Linking to Other Objects), and

> **Functional Programming** (enabled by lambdas with closure)

> Collectively, I like to call these paradigms **the two pillars of JavaScript,** and I'm not ashamed to admit that they've spoiled me. I don't want to program in a language without them.

> "I skate to where the puck is going to be, not where it has been." ~ Wayne Gretzky

The high tech world is built on a culture of continuous innovation. We've built great things together because of a shared need to push technology past the bleeding edge. Each new breakthrough enables us to do things we never dreamed we'd be doing a few years ago. Welcome to the world of tech.

![](https://cdn-images-1.medium.com/max/400/1*G1CB0J4Dmar7yA0XreWu-g.jpeg)

[Silicon Valley 1936](https://www.flickr.com/photos/sanjoselibrary/4026205357/in/photolist-j5MMLL-bWgGcu-78MxBk-78Muqx-78Rob9-78RdiC-78Rdnb-78MmzK-aFdNdM-AkJqZ-cvKN3L-mrpR4Q/) Not only are things changing quickly, the rate at which things are changing is changing quickly. We're several decades and many tech generations into an exponential tech explosion. The way we built typical applications five years ago is dramatically different from the way we build them today. You're probably using tools now that didn't exist 2 years ago. Go back 15 or 20 years, and even our processes were radically different (waterfall vs agile, etc...).

![](https://cdn-images-1.medium.com/max/800/1*WylVGaTz4c8C9kM8AKDYWA.jpeg)

[Silicon Valley 1971](https://www.flickr.com/photos/intelfreepress/6351051085/in/photolist-j5MMLL-bWgGcu-78MxBk-78Muqx-78Rob9-78RdiC-78Rdnb-78MmzK-aFdNdM-AkJqZ-cvKN3L-mrpR4Q/) Did you spend the last two years learning Angular? If so, I feel your pain. A lot of what you learned doesn't apply to Angular 2.0 or React. This rate of change is not sustainable if we continue to do things the way we've always done them.

If we're going to survive as engineers in the long run, the most important thing we must learn is how to adapt, and **how to make our code more adaptable.**

We must learn fast, because the technology we use next year will likely be twice as complex as the technology we're using now. As the internet of things and AI continue to explode, the loads on our applications will continue to grow exponentially. Tomorrow's apps will need to be more scalable, interoperable, concurrent, connected, performant, and intelligent than they are today.

The only way we'll be able to keep up with the exponentially increasing complexity will be to decrease the complexity of understanding programs. To maintain the goliath apps of tomorrow, we must learn to build more expressive code. We must learn to write programs that are easier to reason about, debug, create, analyze, and understand.

> Procedural programming and OOP are not going to get us where we need to go by themselves.

Over the course of the next few years, **the way we code is going to change in radical ways,** pushing us in a fundamentally different direction than the one we've been sprinting for the past 30 years or so. These changes will lead to many important breakthroughs in programming techniques, processes, application scalability, and quality controls.

> **Developers proficient in functional programming are going to be in large demand in the very near future.**

This change is already sweeping through organizations like Netflix, Facebook, and Microsoft, and there's very good reason for this **tectonic shift in programming styles.**

In Buddhist philosophy, Zen kōans are often used to challenge a student's fundamental understanding of reality. Many feature two seemingly conflicting ideas or meanings, and when the student has realized the truth of both perspectives, mastery or enlightenment is attained. Here's a modern spin I included in my book, ["Programming JavaScript Applications" (O'Reilly)](http://learn-javascript.ericelliott.me/programming-javascript-applications/):

> The venerable master Qc Na was walking with his student, Anton. Hoping to prompt the master into a discussion, Anton said "Master, I have heard that objects are a very good thing -- is this true?" Qc Na looked pityingly at his student and replied, "Foolish pupil -- objects are merely a poor man's closures."

> Chastised, Anton took his leave from his master and returned to his cell, intent on studying closures. He carefully read the entire "Lambda: The Ultimate..." series of papers and its cousins, and implemented a small Scheme interpreter with a closure-based object system. He learned much, and looked forward to informing his master of his progress.

> On his next walk with Qc Na, Anton attempted to impress his master by saying "Master, I have diligently studied the matter, and now understand that objects are truly a poor man's closures." Qc Na responded by hitting Anton with his stick, saying "When will you learn? Closures are a poor man's object." At that moment, Anton became enlightened.

> [_~_ Anton van Straaten](http://people.csail.mit.edu/gregs/ll1-discuss-archive-html/msg03277.html)

Like objects, closures are a mechanism for containing state. In JavaScript, a closure is created whenever a function accesses a variable defined outside the immediate function scope. It's easy to create closures: Simply define a function inside another function, and expose the inner function, either by returning it, or passing it into another function. The variables used by the inner function will be available to it, even after the outer function has finished running.

You can use closures to create data privacy in JavaScript using a factory function:

```
var counter = function counter() {
 var count = 0;
 return {
 getCount: function getCount() {
 return count;
 },
 increment: function increment() {
 count += 1;
 }
 };
};
```

In [The Two Pillars of JavaScript Part 1](https://medium.com/javascript-scene/the-two-pillars-of-javascript-ee6f3281e7f3), I described some of the major shortcomings of what most of us have come to know as object oriented programming, but also pointed out a glimmer of hope for a brighter future in object oriented design: **prototypal OO** -- _not simply the concept of prototype delegation and chaining,_ but also the concept of **building new instances not from classes, but from a compilation of smaller object prototypes.**

In **The Two Pillars of JavaScript Part 2**, we're going to discuss a completely different programming paradigm that is going to play a much bigger role going forward as modern programming languages add more and more features that reduce boilerplate, repetition, and syntax noise: **Functional Programming (FP).**

> "...the way forward is sometimes the way back." ~ The Wise Man, "Labyrinth"

In the very beginnings of the computing revolution, decades before the invention of the microprocessor, a man named **Alonzo Church** did groundbreaking work in theoretical computer science. You may have heard of his student and collaborator, **Alan Turing**. Together, they created a theory of computable functions called the **Church-Turing Thesis**, which described the nature of computable functions.

Two foundational models of computation emerged from that work. The famous **turing machine**, and **lambda calculus**.

Today, the turing machine is frequently cited as the baseline of requirements for a modern programming language. A language or processor / VM instruction set is said to be **turing complete** if it can be used to simulate a universal turing machine. The first famous example of a turing-complete system was lambda calculus, described by Alonzo Church in 1936.

Lambda calculus went on to inspire one of the first high-level programming languages, and the second-oldest high-level language in common use today: Lisp.

Lisp was (and remains) extremely influential and popular in academic circles, but it has also been very popular in productivity applications, particularly those that work on **continuous data** such as scalable vector graphics. Lisp was originally specified in 1958, and it's still often embedded in many complex applications. Notably, almost all popular CAD applications support AutoLISP, a dialect of Lisp used in AutoCAD, which (including its many specialized derivatives) is still the most widely used CAD application in the world.

> Continuous data: Data that must be measured rather than counted, and could take any value within a range. Contrast with discrete data, which can be counted and accurately indexed. All values come from a specific set of valid values. For instance, the frequencies that can be produced by a violin must be represented by continuous data, because the string can be stopped at any point along the fingerboard. The frequencies that can be produced by a piano are discrete, because each string is tuned to a specific pitch and can't be stopped at different points depending on the performer's will. There are usually 88 notes on a piano keyboard. It's impossible to count the number of notes that could be produced by a violin player, and violin players frequently take advantage of that by sliding the pitch up or down between different notes.

Lisp and its derivatives birthed an entire family of **functional programming languages** which includes Curry, Haskell, Erlang, Clojure, ML, OCaml, etc...

As popular as many of those languages are, JavaScript has exposed a whole new generation of programmers to functional programming concepts.

Functional programming is a natural fit for JavaScript, because JavaScript offers a few important features: First-class functions, closures, and simple lambda syntax.

JavaScript makes it easy to assign functions to variables, pass them into other functions, return functions from other functions, compose functions, and so on. JavaScript also offers immutable values for primitive types, and features that make it easy to return new objects and arrays rather than manipulate properties of those that are passed in as arguments.

_Why is all that important?_

Functional programming glossaries contain a large number of large words, but at its core, the essence of FP is really very simple; programs are built mostly with a handful of very small, very reusable, very predictable **pure functions.**

Pure functions have a few properties that make them extremely reusable and extremely useful for a variety of applications:

**Idempotence:** Given the same inputs, a pure function will always return the same output, regardless of the number of times the function is called. You may have heard this word used to describe HTTP GET requests. **Idempotence is an important feature for building RESTful web services,** but it also serves the very useful purpose of separating computation from dependency on both time and order of operations -- **extremely valuable for parallel & distributed computation** (think horizontal scaling).

Because of idempotence, pure functions are also a great fit when you need to operate on continuous data sets. For instance, automated fade-ins and fade-outs in video or audio. Typically they're designed as linear or logarithmic curves independent of framerates or sample rates, and only applied at the last possible moment to produce discrete data values. In graphics, this is analogous to scalable vector images like SVG, fonts, and Adobe Illustrator files. Key frames or control points are laid out relative to each other or to a timeline, rather than keyed to fixed pixels, frames, or samples.

Because idempotent functions don't have any dependency on time or sample resolution, it's possible to treat continuous data as unbounded (virtually infinite) data streams, which allows free scaling of data across time, (sampling rates), pixel resolution, audio volume, and so on.

**Free from Side-effects:** Pure functions can be safely applied with no side-effects, meaning that they do not mutate any shared state or mutable arguments, and other than their return value, they don't produce any observable output, including thrown exceptions, triggered events, I/O devices, network, console, display, logs, etc...

Because of the lack of shared state and side-effects, pure functions are much less likely to conflict with each other or cause bugs in unrelated parts of the program.

In other words, for the OOP coding audience, **pure functions produce stronger guarantees of encapsulation than objects without function purity,** and those guarantees provide many of the same benefits as encapsulation in OOP: The ability to change implementation without impacting the rest of the program, a self-documenting public interface (the function signature), independence from outside code, the ability to move code around freely between files, modules, projects, and so on.

Function purity is FP's answer to the gorilla/banana problem from OOP:

> "The problem with object-oriented languages is they've got all this implicit environment that they carry around with them. You wanted a banana but what you got was a gorilla holding the banana and the entire jungle." ~ Joe Armstrong

Obviously, most programs need to produce output, so complex programs usually can't be composed using **only** pure functions, but wherever it's practical, it's a good idea to make your functions pure.

FP provides several kinds of functions that are extremely reusable. Various implementations will have a different set using different names. This list is mostly pulled from Haskell documentation. Haskell is one of the more popular functional languages, but you'll find functions similar to these in several popular JavaScript libraries:

**List utilities:**

- head() -- get first element

- tail() -- get all but first elements

- last() -- get last element

- length() -- count of elements

**Predicates / comparators (test an element, return boolean)**

- equal()

- greaterThan()

- lessThan()

**List transformations:**

- map() ([x]) -> [y] take list x and apply a transformation to each element in that list, return new list y

- reverse() ([1, 2, 3]) -> [3, 2, 1]

**List searches:**

- find() ([x]) -> x take list x and return first element that matches a predicate

- filter() ([x]) -> [y] take list x and return all items matching predicate

**List Reducers / folds:**

- reduce() -- ([x], function[, accumulator]) -> apply function to each element and accumulate the results as a single value

- any() -- true if any values match predicate

- all() -- true if all values match predicate

- sum() -- total of all values

- product() -- product of all values

- maximum() -- highest value

- minimum() -- lowest value

- concat() -- take a list of lists and return a single, concatenated list in list order

- merge() -- take a list of lists and return a single, merged list in element order

**Iterators / Generators / Collectors (infinite lists)**

- sample() -- return current value of continuous input source (temperature, form input, toggle switch state, etc...)

- repeat() -- (1) -> [1, 1, 1, 1, 1, 1,...]

- cycle() / loop() -- When the end of the list is reached, wrap around to the beginning again.

Some of these common utility extras got added to JavaScript with the ECMAScript 5 Array extras. e.g.:

```
// Using ES6 syntax. () => means function () {}
var foo = [1, 2, 3, 4, 5];
var bar = foo.map( (n) => n + 1 ); // [2, 3, 4, 5, 6]
var baz = bar.filter( (n) => n >=3 && n <=5); // [3,4,5]
var bif = baz.reduce( (n, el) => n += el); // 12
```

You might notice that most of the functions above are utilities for managing or reasoning about **lists**. Once you start doing a lot of functional programming, you might start to see virtually everything as a **list**, an **element** of a list, a **predicate** used to test list values, or a **transformation** based on list values. That thinking lays the foundation for extremely reusable code.

**Generics** are functions that are capable of working on a range of different data types. In JavaScript, many functions that operate on collections are generic. For example, you can use the same function on both strings and arrays, because a string can be treated as an array of characters.

The process of changing a function that only works with a single type to one that works with many types is called **lifting**.

The key to all these functions working on every type of data you feed to them is that data is processed in lists which **all share the same interface**. I discussed lifting a bit towards the end of my Fluent 2014 talk, "Static Types are Overrated" (see below).

In OO and imperative programming, we micromanage everything. We micromanage state, iteration counters, the timing of what happens when using things like event emitters and callbacks. What If I told you that all of that work is completely unnecessary -- that you could literally delete entire categories of code from your programs?

**Reactive programming** uses functional utilities like map, filter, and reduce to create and process data flows which propogate changes through the system: hence, **reactive**. When input _x_ changes, output _y_ updates _automatically_ in response.

In OO, you might set up some object (say, a form input), and turn that object into an event emitter, and set up an event listener that jumps through some hoops and maybe produces some output whenever the event is triggered.

When using reactive programming, you instead specify data dependencies in a more declarative fashion, and most of the heavy lifting is offloaded to standard functional utilities so you don't have to reinvent the wheel over and over again.

Imagine that **every list is a stream**: An array is a stream of values in element order. A form input can be a stream of values sampled with each change. A button is a stream of clicks.

> A stream is just a list expressed over time.

In Rx (reactive extensions) nomenclature, you create **observable streams** and then process those streams using a set of functional utilities like the ones described above.

Similar to Node streams, **observables** can feature optional shared **backpressure**. Like **generators**, your program can consume observable values at its own pace, as it becomes ready to handle them.

Imagine you want to watch all your social media feeds for a particular hash tag. You could collect a list of lists, merge them in the order they're received, and then use any combination of the utility functions above to process them.

That might look something like this:

```
var messages = merge(twitter, fb, gplus, github)

messages.map(normalize) // convert to single message format
 .filter(selectHashtag) // cherry pick messages that use the tag
 .pipe(process.stdout) // stream output to stdout
```

You may have heard of promises. A **promise** is an object that provides a standard interface for dealing with values that may or may not be available at the time the promise is used. In other words, a promise wraps a potential value that may be resolved in the future. Typically, a function call will return a promise that may resolve with some future value:

```
fetchFutureStockPrices().then(becomeAMillionaire);
```

Well, it doesn't work exactly like that, because `.then()` will only be called when the stock prices become available, so when `becomeAMillionaire` finally gets run, the "future" stock prices will be the present stock prices.

A promise is basically a stream that only emits a single value (or rejection). Observables can replace promises in your code and provide all of the standard functional utilities you may have grown accustomed to with libraries like Underscore or Lo-Dash.

Now would be a great time to learn more about functional programming, the benefits of function purity, lazy evaluation, and a lot more. I promise you're going to hear a lot more about these topics over the next few years.

Here we are, coming up on 2015, returning to computer science roots planted in the 1930's so that we can more easily manage the software of future.

I really loved this talk by Jafar Husain, Technical Lead at Netflix. It's called, "Asynchronous Programming at Netflix" and it was delivered at [@Scale 2014](http://atscaleconference.com/).

<https://www.youtube.com/watch?v=gawmdhCNy-A> James Coglan gave a good talk that touches on some key topics such as the roles of promises and laziness:
