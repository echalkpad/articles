# A Gentle Introduction to Functional JavaScript: Part 3

[Original URL](http://jrsinclair.com/articles/2016/gentle-introduction-to-functional-javascript-functions/)

> This is part three of a four-part series introducing 'functional' programming in JavaScript. In the last article we saw how we can use functional programming with arrays and lists. In...

This is part three of a four-part series introducing 'functional' programming in JavaScript. In the last article we saw how we can use functional programming with arrays and lists. In this article we examine higher-order functions--functions for making functions.

At the end of the last article, I said that going further down the functional path is not for everyone. That is because once you get past list-processing functions, things start to get a little weird. What I mean is that we started with abstracting collections of instructions into functions. Then, we abstracted for-loops into `map` and `reduce`. The next level of abstraction is to start refactoring patterns of _creating_ functions. We start using functions to make other functions. This can be powerful and elegant, but it starts to look a lot less like the JavaScript you might be used to writing.

## More building blocks

Functions for making other functions are sometimes called _higher order functions_. To understand them though, we need to revisit some of the built-in language features of JavaScript that make higher order functions possible.

### Closures and Scope

One of the more difficult things to wrap one's head around in JavaScript which variables a function can 'see'. In JavaScript, if you define a variable inside a function, it can't be seen outside the function. For example:

```
var thing = 'bat';

var sing = function() {
 // This function can 'see' thing
 var line = 'Twinkle, twinkle, little ' + thing;
 log(line);
};

sing();
// Twinkle, twinkle, little bat

// Outside the function we can't see message though
log(line);
// undefined
```

However, if we define a function inside a function, the inner function can see variables in the outer function:

```
var outer = function() {
 var outerVar = 'Hatter';
 var inner = function() {
 // We can 'see' outerVar here
 console.log(outerVar);
 // Hatter

 var innerVar = 'Dormouse';
 // innerVar is only visible here inside inner()
 }

 // innerVar is not visible here.
}
```

This takes a bit of getting used to. The rules are fairly straight-forward, but once we start passing variables around as arguments, it becomes harder to keep track of which functions can see which variables. If it's confusing at first, be patient: Look at the point where you defined the function, and work out which variables are 'visible' at that point. They may not be what you expect if you are just looking at the point where you're calling the function.

### The special arguments variable

When you create a function in JavaScript, it creates a special variable called `arguments`, that is _sort of_ like an array. It contains the arguments that are passed to the function. for example:

```
var showArgs = function(a, b) {
 console.log(arguments);
}
showArgs('Tweedledee', 'Tweedledum');
//=> { '0': 'Tweedledee', '1': 'Tweedledum' }
```

Notice that the output is more like an object with keys that happen to be integers, rather than an actual array. It also has

The interesting thing about `arguments` is that it contains _all_ the arguments passed in a function call, regardless of how many are defined. So, if you call a function and pass it extra arguments, they will be available in the `arguments` variable.

```
showArgs('a', 'l', 'i', 'c', 'e');
//=> { '0': 'a', '1': 'l', '2': 'i', '3': 'c', '4': 'e' }
```

The `arguments` variable also has a 'length' property, like an array.

```
var argsLen = function() {
 console.log(arguments.length);
}
argsLen('a', 'l', 'i', 'c', 'e');
//=> 5
```

It is often useful to have the `arguments` variable as an actual array. In those cases we can convert the `arguments` variable into a real array by using a built-in array method called `slice`. Because `arguments` isn't is a real array, we have to do it by a roundabout route:

```
var showArgsAsArray = function() {
 var args = Array.prototype.slice.call(arguments, 0);
 console.log(args);
}
showArgsAsArray('Tweedledee', 'Tweedledum');
//=> [ 'Tweedledee', 'Tweedledum' ]
```

The `arguments` variable is most commonly used to create functions that can take a variable number of arguments. This will come in handy later, as we will see.

### Call and Apply

We saw before that arrays in JavaScript have some built-in methods like `.map` and `.reduce`. Well, functions have some built in methods too.

The normal way to call a function is by writing parentheses, and any parameters after the function name. For example:

```
function twinkleTwinkle(thing) {
 console.log('Twinkle, twinkle, little ' + thing);
}
twinkleTwinkle('bat');
//=> Twinkle, twinkle, little bat
```

One of the built-in methods for functions is `call` and it allows you to call a function another way:

```
twinkleTwinkle.call(null, 'star');
//=> Twinkle, twinkle, little star
```

The first argument to the `.call` method defines what the special variable `this` refers to inside the function. But we can ignore it for now. Any arguments after this one are passed directly to the function.

The `.apply` method is much like `.call`, except that instead of passing individual arguments one-by-one, `.apply` allows you to pass an array of arguments as the second parameter. For example:

```
twinkleTwinkle.apply(null, ['bat']);
//=> Twinkle, twinkle, little bat
```

Both of these methods will be useful when we are building functions that create other functions.

### Anonymous Functions

JavaScript allows us to create functions on the fly. Wherever we might create a variable, then do something with that variable, JavaScript will let us stick a function definition right in there. This is often used with `map` and `reduce`, for example:

```
var numbers = [1, 2, 3];
var doubledArray = map(function(x) { return x articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 2}, numbers);
console.log(doubledArray);
//=> [ 2, 4, 6 ]
```

Functions created on the fly like this are called 'anonymous' functions, since they don't have a name. They are also sometimes called 'lambda' functions.

## Partial Application

Sometimes it can be handy to pre-fill a function's arguments. For example, imagine we've made a handy `addClass()` function that takes a class name and a DOM element as parameters:

```
var addClass = function(className, element) {
 element.className += ' ' + className;
 return element;
}
```

We'd like to use this with `map` to add a class to a bunch of elements, but we have a problem: the `map` passes array items one by one as the first parameter to the callback function. So how do we tell `addClass` which class name to add?

The solution is to create a new function that calls `addClass` with the class name we want:

```
var addTweedleClass = function(el) {
 return addClass('tweedle', el);
}
```

Now we have a function that just takes one parameter. It's now suitable to pass to our `map` function:

```
var ids = ['DEE', 'DUM'];
var elements = map(document.getElementById, ids);
elements = map(addTweedleClass, elements);
```

But if we want to add another class, we have to create another function:

```
var addBoyClass = function(el) {
 return addClass('boy', el);
}
```

We are beginning to repeat ourselves... so, let's see if we can find an abstraction for this pattern. What if we had a function that created another function with the first parameter pre-filled?

```
var partialFirstOfTwo(fn, param1) {
 return function(param2) {
 return fn(param1, param2);
 }
}
```

Note that first `return` statement. We've created a function that returns another function.

```
var addTweedleClass = partialFirstOfTwo(addClass, 'tweedle');
var addBoyClass = partialFirstOfTwo(addClass, 'boy');

var ids = ['DEE', 'DUM'];
var elements = map(document.getElementById, ids);
elements = map(addTweedleClass, elements);
elements = map(addBoyClass, elements);
```

This works great when we know our function takes exactly two parameters. But what if we want to partially apply with a function that takes three parameters? Or four? And what if we wanted to partially apply more than one variable? For these cases we need a more generalised partial application function. We'll make use of the `slice` and `apply` methods described above:

```
function partial() {
 // Convert the arguments variable to an array 
 var args = toArray(arguments);

 // Grab the function (the first argument). args now contains the remaining args.
 var fn = args.shift();

 // Return a function that calls fn
 return function() {
 var remainingArgs = toArray(arguments);
 return fn.apply(this, args.concat(remainingArgs));
 }
}
```

Now, the details of _how_ this function works are not as important as _what_ it does. This function allows us to partially apply any number of variables to functions that take any number of parameters.

```
var twinkle = function(noun, wonderAbout) {
 return 'Twinkle, twinkle, little ' +
 noun + '\nHow I wonder where you ' +
 wonderAbout;
}

var twinkleBat = partial(twinkle, 'bat', 'are at');
var twinkleStar = partial(twinkle, 'star', 'are');
```

JavaScript has a built-in method that sort-of works like `partial` called `bind`. It's available as a method on all functions. The trouble is that it expects its first parameter to be an object you want to bind the special `this` variable to. This means, for example, that if you want to partially apply something to `document.getElementById`, you have to pass `document` as the first parameter, like so:

```
var getWhiteRabbit = document.getElementById.bind(document, 'white-rabbit');
var rabbit = getWhiteRabbit();
```

A whole bunch of the time though, we don't need the special `this` variable (especially if we're using a functional style of programming), so we can just pass `null` as the first parameter. For example:

```
 var twinkleBat = twinkle.bind(null, 'bat', 'are at');
var twinkleStar = twinkle.bind(null, 'star', 'are');
```

You can read more about [`.bind` in the MDN JavaScript reference.](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_objects/Function/bind)

### Composition

We said in the last article that functional programming is about taking small, simple functions and piecing them together to do more complex things. Partial application, as we saw above, is a tool that makes that process easier. With partial application we can convert our `addClass` function and convert it into a function we can use with `map`. Composition is another tool for combining simple functions together.

The simplest form of composition is with two functions, _a_ and _b_, both of which expect just one parameter. _Compose_ creates a third function, _c_. Calling _c_ with a parameter _x_ returns the result of calling _a_ with the result of calling _b_ with _x_... What a confusing mess. It's much easier to understand by looking at an example:

```
var composeTwo = function(funcA, funcB) {
 return function(x) {
 return funcA(funcB(x));
 }
}

var nohow = function(sentence) {
 return sentence + ', nohow!';
}
var contrariwise = function(sentence) {
 return sentence + ' Contrariwise…';
}

var statement = 'Not nothin&rsquo;';
var nohowContrariwise = composeTwo(contrariwise, nohow);
console.log(nohowContrariwise(statement));
//=> Not nothin&rsquo;, nohow! Contrariwise…
```

Now, this is pretty good. We can get a long way with just `composeTwo`. But, if you start to write 'pure' functions (we'll discuss this later), then you may find yourself wanting to join more than two functions together. For that we'll need a more generalised `compose` function:[[1]](http://jrsinclair.com/articles/2016/gentle-introduction-to-functional-javascript-functions/#fn:1 "see footnote")

```
var compose = function() {
 var args = arguments;
 var start = args.length - 1;
 return function() {
 var i = start;
 var result = args[start].apply(this, arguments);
 while (i >= 0) {
 result = args[i].call(this, result);
 i = i - 1;
 }
 return result;
 };
};
```

Again, _how_ this works isn't quite as important as _what_ you can do with it. And at first glance, `compose` might not seem that amazing. We can write the function above this way with compose:

```
var nohowContrariwise = compose(contrariwise, nohow);
```

But this doesn't seem that much more concise than writing it this way:

```
var nohowContrariwise = function(x) {
 return nohow(contrariwise(x));
}
```

The real power of compose becomes clearer once we combine it with the `curry` function. But even without currying we can begin to see that if we have a collection small utility functions handy, we can use `compose` to make our code clearer and more concise. For example, imagine we have a plain-text poem:

```
var poem = 'Twas brillig, and the slithy toves\n' + 
 'Did gyre and gimble in the wabe;\n' +
 'All mimsy were the borogoves,\n' +
 'And the mome raths outgrabe.';
```

Now, that poem won't display so well in a browser, so let's add in some line breaks. And, while we're at it, let's translate _brillig_ into something easier to understand. And then we'll wrap the whole thing in a paragraph tag and a block quote. We'll start by creating two very simple functions, and build everything else from there:

```
var replace = function(find, replacement, str) {
 return str.replace(find, replacement);
}

var wrapWith = function(tag, str) {
 return '<' + tag + '>' + str + '</' + tag + '>'; 
}

var addBreaks = partial(replace, '\n', '<br/>\n');
var replaceBrillig = partial(replace, 'brillig', 'four o’clock in the afternoon');
var wrapP = partial(wrapWith, 'p');
var wrapBlockquote = partial(wrapWith, 'blockquote');

var modifyPoem = compose(wrapBlockquote, wrapP, addBreaks, replaceBrillig);

console.log(modifyPoem(poem));
//=> <blockquote><p>Twas four o’clock in the afternoon, and the slithy toves<br/>
// Did gyre and gimble in the wabe;<br/>
// All mimsy were the borogoves,<br/>
// And the mome raths outgrabe.</p></blockquote>
```

Notice that if you read the arguments to `compose` from left to right, they are in reverse to the order they are applied. This is because `compose` reflects the order they'd be in if you wrote them out as nested function calls. Some people find this a little confusing, so most helper libraries provide a reversed form called `pipe` or `flow`.

Using a `pipe` function, we could write our `modifyPoem` function as follows:

```
var modifyPoem = pipe(replaceBrillig, addBreaks, wrapP, wrapBlockquote);
```

### Currying

A limitation of `compose` is that it expects all the functions passed in to take just one parameter. Now, this isn't such a big deal now that we have a `partial` function--we can convert our multi-parameter functions to single-parameter functions with relative ease. But it's still a little bit tedious. _Currying_ is kind of like partial application on steroids.

The details of the `curry` function are a little complicated, so first, let's look at an example. We have a function `formatName` that put's a person's nickname in quotes. It takes three parameters. When we call the curried version of `formatName` with less than three parameters, it returns a new function with the given parameters partially applied:

```
var formatName = function(first, surname, nickname) {
 return first + ' “' + nickname + '” ' + surname;
}
var formatNameCurried = curry(formatName);

var james = formatNameCurried('James');

console.log(james('Sinclair', 'Mad Hatter'));
//=> James “Mad Hatter” Sinclair

var jamesS = james('Sinclair')

console.log(jamesS('Dormouse'));
//=> James “Dormouse” Sinclair

console.log(jamesS('Bandersnatch'));
//=> James “Bandersnatch” Sinclair
```

There's some other things to notice about curried functions:

```
formatNameCurried('a')('b')('c') === formatNameCurried('a', 'b', 'c'); // true
formatNameCurried('a', 'b')('c') === formatNameCurried('a')('b', 'c'); // true
```

This is all very convenient, but it doesn't give us all that much over `partial`. But what if, just suppose, we happened to curry every function we make, just by default. Then, we could create almost any function by combining other functions with composition (and currying).

Remember our poem example from earlier? What if we wanted to wrap emphasis tags around 'four o'clock in the afternoon' string we replaced?

```
var replace = curry(function(find, replacement, str) {
 var regex = new RegExp(find, 'g');
 return str.replace(regex, replacement);
});

var wrapWith = curry(function(tag, str) {
 return '<' + tag + '>' + str + '</' + tag + '>'; 
});

var modifyPoem = pipe(
 replace('brillig', wrapWith('em', 'four o’clock in the afternoon')),
 replace('\n', '<br/>\n'),
 wrapWith('p'),
 wrapWith('blockquote')
);

console.log(modifyPoem(poem));
//=> <blockquote><p>Twas <em>four o’clock in the afternoon</em>, and the slithy toves<br/>
// Did gyre and gimble in the wabe;<br/>
// All mimsy were the borogoves,<br/>
// And the mome raths outgrabe.</p></blockquote>
```

Notice that we've replaced `compose` with `pipe`. And there's no more intermediate functions, we've put the curried functions directly in the pipeline. And, they're stll readable.

Below is an implementation of curry adapted from _JavaScript Allongé._[[2]](http://jrsinclair.com/articles/2016/gentle-introduction-to-functional-javascript-functions/#fn:2 "see footnote") Again, how it works is not as important as what it does.

```
function curry (fn) {
 var arity = fn.length;

 function given (argsSoFar) {
 return function helper () {
 var args = Array.prototype.slice.call(arguments, 0);
 var updatedArgsSoFar = argsSoFar.concat(args);

 if (updatedArgsSoFar.length >= arity) {
 return fn.apply(this, updatedArgsSoFar);
 }
 else {
 return given(updatedArgsSoFar);
 }
 }
 }

 return given([]);
}
```

### But why?

So far, we've looked as `partial`, `compose`, `pipe`, and `curry` as useful tools to piece together small, simple functions to create more complex ones. But, are they really that useful? What do they make possible that wasn't possible before? Well, what it does is open up a whole new style of programming. It lets us think about problems in different ways, which makes certain classes of problem become much easier to solve. It can also help us write more robust, verifiable code. This will be the topic of the next article, so if you're curious, [read on...](http://jrsinclair.com/articles/2016/gentle-introduction-to-functional-javascript-style/)
