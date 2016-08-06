# A Gentle Introduction to Functional JavaScript: Part 4

[Original URL](http://jrsinclair.com/articles/2016/gentle-introduction-to-functional-javascript-style/)

> This is part four of a four-part introduction to functional programming in JavaScript. In the last article we looked at higher-order functions: functions for making functions. Inthis article, we...

This is part four of a four-part introduction to functional programming in JavaScript. In the last article we looked at higher-order functions: functions for making functions. Inthis article, we discuss how to use these new tools with style.

## Doing it with Style

In the last article we looked at `partial`, `compose`, `curry` and `pipe`, and how we can use them to piece together small, simple functions into larger, more complicated ones. But what does that do for us? Is it worth the bother when we're already writing perfectly valid code?

Part of the answer is that it's always useful to have more tools available for getting the job done--so long as you know how to use them--and functional programming certainly gives us a useful set of tools for writing JavaScript. But I think there's more to it than that. Functional programming opens up a different _style_ of programming. This in turn allows us to conceptualise problems and solutions in different ways.

There are two key features to functional programming:

1. Writing pure functions, which is important if you want to give functional programming a go; and
2. Pointfree programming style, which is not _as_ important but good to understand.

### Purity

If you read about functional programming, you will eventually come across the concept of _pure_ and _impure_ functions. Pure functions are functions that fulfil two criteria:

1. Calling the function with the same inputs always _returns_ the same output.
2. Calling the function produces no side-effects: No network calls; no files read or written; no database queries; no DOM elements modified; no global variables modified; and no console output. Nothing.

Impure functions make functional programmers uncomfortable. So uncomfortable that they avoid them as much as they possibly can. Now, the trouble with this is that the whole point of writing computer programs _is_ the side effects. Making a network call and rendering DOM elements is at the core of what a web application does; it's what JavaScript was invented for.

So what's an aspiring functional programmer to do? Well, the key is that we don't avoid impure functions entirely, we just give them a healthy amount of respect, and put-off dealing with them until we absolutely have to. We work out a clear, tested plan for what we want to do _before_ we try to do it. As Eric Elliot puts it in _[The Dao of Immutability](https://medium.com/javascript-scene/the-dao-of-immutability-9f91a70c88cd):_

> **Separation:** _Logic is thought. Effects are action. Therefore the wise think before acting, and act only when the thinking is done._

> If you try to perform effects and logic at the same time, you may create hidden side effects which cause bugs in the logic. Keep functions small. Do one thing at a time, and do it well.

In other words, with functional programming, we generally try and work out the logic of what we're trying to achieve first, before we do anything that has potential side effects.

Another way to think about it is, it's like the difference between using a machine gun and a sniper rifle. With a machine gun you spray as many bullets as possible, counting on the fact that if you keep on spraying, eventually you'll hit something. But you may also hit things you didn't mean to. A sniper rifle is different though. You pick the best vantage point, line up the shot, take into account the wind speed and distance to the target. You patiently, methodically, carefully set things up and at the right moment, pull the trigger. A lot less bullets, and a much more precise effect.

So how do we make our functions pure? Let's look at an example:

```
var myGlobalMessage = '{{verb}} me';

var impureInstuction = function(verb) {
 return myGlobalMessage.replace('{{verb}}', verb);
}

var eatMe = impureInstruction('Eat');
//=> 'Eat me'
var drinkMe = impureInstruction('Drink');
//=> 'Drink me'
```

This function is impure because it depends on the global variable `myGlobalMessage`. If that variable ever changes, it becomes difficult to tell what `impureInstruction` will do. So, one way to make it pure is to move the variable inside:

```
var pureInstruction = function (verb) {
 var message = '{{verb}} me';
 return message.replace('{{verb}}', verb);
}
```

This function will now always return the same result given the same set of inputs. But sometimes we can't use that technique. For example:

```
var getHTMLImpure = function(id) {
 var el = document.getElementById(id);
 return el.innerHTML;
}
```

This function is impure because it is relying on the `document` object to access the DOM. If the DOM changes it _might_ produce different results. Now, we can't define `document` inside our function because it's an API to the browser, but we _can_ pass it in as a parameter:

```
var getHTML = function(doc, id) {
 var el = doc.getElementById(id);
 return el.innerHTML;
}
```

This may seem kind of trivial and pointless, but it's a handy technique. Imagine you were trying to unit test this function. Usually, we'd have to set up some kind of browser to get a document object so we could test this. But, since we have doc as a parameter, it's easy to pass in a _stub_ object instead:

```
var stubDoc = {
 getElementById: function(id) {
 if (id === 'jabberwocky') {
 return {
 innerHTML: '<p>Twas brillig…'
 };
 }
 }
};

assert.equal(getHTML('jabberwocky'), '<p>Twas brillig…');
//=> test passes
```

Writing that stub might seem like a bit of effort, but we can now test this function without needing a browser. If we wanted to, we could run it from the command line without having to configure a headless browser. And, as an added bonus, the test will run many, many times faster than one with the full document object.

One other way to make a function pure is to have it return another function that will eventually do something impure when we call it. It feels a bit like a dirty hack at first, but it's entirely legitimate. For example:

```
var htmlGetter = function(id) {
 return function() {
 var el = document.getElementById(id);
 return el.innerHTML;
 }
}
```

The `htmlGetter` function is pure because running it does not access the global variable--instead it always returns the same exact function.

Doing things this way isn't as useful for unit testing, and it doesn't remove the impurity altogether--it just postpones it. And that's not necessarily a bad thing. Remember, we want to get all the logic straight with pure functions first, before we pull the trigger on any side effects.

### Pointfree

_Pointfree_ or _tacit_ programming is a particular style of programming that higher-order functions like `curry` and `compose` make possible. To explain it, let's look again at the poem example from the last article:

```
var poem = 'Twas brillig, and the slithy toves\n' + 
 'Did gyre and gimble in the wabe;\n' +
 'All mimsy were the borogoves,\n' +
 'And the mome raths outgrabe.';

var replace = curry(function(find, replacement, str) {
 var regex = new RegExp(find, 'g');
 return str.replace(regex, replacement);
});

var wrapWith = curry(function(tag, str) {
 return '<' + tag + '>' + str + '</' + tag + '>'; 
});

var addBreaks = replace('\n', '<br/>\n');
var replaceBrillig = replace('brillig', wrapWith('em', 'four o’clock in the afternoon'));
var wrapP = wrapWith('p');
var wrapBlockquote = wrapWith('blockquote');

var modifyPoem = compose(wrapBlockquote, wrapP, addBreaks, replaceBrillig);
```

Notice that `compose` expects every function it's passed to take exactly one parameter. So, we use `curry` to change our multi-parameter functions `replace` and `wrapWith` into single-parameter functions. Notice also that we were a little bit deliberate with the order of our functions so that `wrapWith`, for example, takes the tag as its first parameter other than the string to wrap. If we're careful like this in the way we set up our functions, it makes creating functions by composing easy.[[1]](http://jrsinclair.com/articles/2016/gentle-introduction-to-functional-javascript-style/#fn:1 "see footnote")

It becomes so easy in fact, that you can write _all_ your code in this way. But notice a little side effect: When we define the final `modifyPoem` function, we never mention anywhere that it takes a single string argument. And if you look at the curried functions, `addBreaks`, `replaceBrillig`, `wrapP` and `wrapBlockquote`, none of those mention that they take a single string variable either. This is _pointfree_ programming: starting with a base set of utility functions (like Ramda or functional.js) and writing code in such a way that you never mention the input variables.

What does this give us? Well, nothing special in terms of the code itself. The clever thing about pointfree style is that it _forces_ you to use `compose`, `curry` and `pipe`, etc. This in turn _strongly encourages_ you to stick with small, simple functions pieced together in sensible ways. In other words, it's a self-imposed restraint, like a haiku or a sonnet. Not all poetry has to be written that way--and following the rules does not guarantee a beautiful poem--but some poetry written in those styles can be incredibly beautiful.

Now, doing everything in pointfree style way isn't always practical. Sometimes, it adds unnecessary complication to a simple function. But, giving it a go and _trying_ to write all your functions pointfree is a good way to get a better understanding of functional programming.

### Hindley-Milner Type Signatures

Once you're doing everything in pointfree, it does leave the question, of how to communicate to other programmers what type of parameter they should pass to your function. To facilitate this, functional programmers have developed a special notation for specifying what types of parameter a function takes, and what it returns. The notation is called _Hindley-Milner type signatures_. We write them as comments where we define the function. Let's look at some examples:

```
// instruction :: String -> String
var instruction = function(verb) {
 return verb + ' me';
}
```

The type signature says that `instruction` takes a single String as an input and returns another String. So far, so good. What about if we have a function that takes two parameters though?

```
// wrapWith :: String -> (String -> String)
var wrapWith = curry(function(tag, str) {
 return '<' + tag + '>' + str + '</' + tag + '>'; 
});
```

This is slightly more complicated, but not too difficult. This one says that `wrapWith` takes a String and returns a _function_, and this function takes a String and returns a String. Note that this works because we've curried the function. When we're using this style, it's just assumed that you will always curry all your functions.

What about something with three parameters instead of two? One way to write it would be like this:

```
// replace :: String -> (String -> (String -> String))
var replace = curry(function(find, replacement, str) {
 var regex = new RegExp(find, 'g');
 return str.replace(regex, replacement);
});
```

Now we have a function that returns a function that returns a function that returns a string. It still makes sense, but because we're always assuming that everything is curried, we tend to drop the brackets:

```
// replace :: String -> String -> String -> String
```

What about if we have a different type of input parameter:

```
// formatDollars :: Number -> String
var formatDollars = replace('${{number}}', '{{number}}');

formatDollars(100);
//=> $100
```

Here we have a pointfree function, and it becomes clearer why the type signatures are helpful. This one takes a Number and returns a string.

What about if we have an array?

```
// sum :: [Number] -> Number
var sum = reduce(add, 0);
```

This one takes an array of numbers and returns a number (assuming we curried our `reduce` function from the the second article).

A couple of final examples:[[2]](http://jrsinclair.com/articles/2016/gentle-introduction-to-functional-javascript-style/#fn:2 "see footnote")

```
// identity :: a -> a
var identity = function(x) { return x };

// map :: (a -> b) -> [a] -> [b]
var map = curry(function(callback, array) {
 return array.map(callback);
});
```

The `identity` function above takes a parameter of any old type, and returns a variable of the same type. The the `map` function on the other hand, takes a function that takes a variable of type _a_ and returns a variable of type _b_. I then takes an array of values, all type _a_, and returns an array of values, all type _b_.

You'll find that libraries like [Ramda](http://ramdajs.com/), for example, use this notation to document all the functions in the library.

### Going deeper

We've barely scratched the surface of functional programming. But understanding first-class functions, partial application and composition give us the basic building blocks to take it a lot further. If you're interested in reading further, there's a list of helpful resources below:
