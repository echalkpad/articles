# A Gentle Introduction to Functional JavaScript: Part 2

[Original URL](http://jrsinclair.com/articles/2016/gentle-introduction-to-functional-javascript-arrays/)

> This is part two of a three-part series introducing 'functional' programming in JavaScript. In the previous article, we saw how functions can be used to make certain code abstractions...

This is part two of a three-part series introducing 'functional' programming in JavaScript. In the previous article, we saw how functions can be used to make certain code abstractions easier. In this article we apply these techniques to lists.

## Working with Arrays and Lists

Recall that in the previous article, we talked about DRY code. We saw that functions are useful for bundling up sets of actions that might be repeated. But what if we're repeating the same function lots of times? For example:

```
function addColour(colour) {
 var rainbowEl = document.getElementById('rainbow');
 var div = document.createElement('div');
 div.style.paddingTop = '10px';
 div.style.backgroundColour = colour;
 rainbowEl.appendChild(div);
}

addColour('red');
addColour('orange');
addColour('yellow');
addColour('green');
addColour('blue');
addColour('purple');
```

That `addColour` function is called rather a lot. We are repeating ourselves--something we wish to avoid. One way to refactor it is to move the list of colours into an array, and call `addColour` in a for-loop:

```
var colours = [
 'red', 'orange', 'yellow',
 'green', 'blue', 'purple'
];

for (var i = 0; i < colours.length; i = i + 1) {
 addColour(colours[i]);
}
```

This code is perfectly fine. It gets the job done, and it is less repetitive than the previous version. But it's not particularly expressive. We have to give the computer very specific instructions about creating an index variable and incrementing it, and checking to see if it's time to stop. What if we could wrap all that for-loop stuff into a function?

### For-Each

Since JavaScript lets us pass a function as a parameter to another function, writing a `forEach` function is relatively straightforward:

```
function forEach(callback, array) {
 for (var i = 0; i < array.length; i = i + 1) {
 callback(array[i], i);
 }
}
```

This function takes another function, `callback`, as a parameter and calls it on every item in the array.

Now, with our example, we want to run the `addColour` function on each item in the array. Using our new `forEach` function we can express that intent in just one line:

```
forEach(addColour, colours);
```

Calling a function on every item in an array is such a useful tool that modern implementations of JavaScript include it as a built in method on arrays. So instead of using our own `forEach` function, we could use the built in one like so:

```
var colours = [
 'red', 'orange', 'yellow',
 'green', 'blue', 'purple'
];
colours.forEach(addColour);
```

You can find out more about the built in [`forEach` method in the MDN JavaScript reference](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach).

### Map

Now, our `forEach` function is handy, but somewhat limited. If the callback function we pass in returns a value, `forEach` just ignores it. With a small adjustment, we can change our `forEach` function so that it gives us back whatever value the callback function returns. We would then have a new array with a corresponding value for each value in our original array.

Let's look at an example. Say we have an array of IDs, and would like to get the corresponding DOM element for each of them. To find the solution in a 'procedural' way, we use a for-loop:

```
var ids = ['unicorn', 'fairy', 'kitten'];
var elements = [];
for (var i = 0; i < ids.length; i = i + 1) {
 elements[i] = document.getElementById(ids[i]);
}
// elements now contains the elements we are after
```

Again, we have to spell out to the computer how to create an index variable and increment it--details we shouldn't really need to think about. Let's factor out the for-loop like we did with `forEach` and put it into a function called `map`:

```
var map = function(callback, array) {
 var newArray = [];
 for (var i = 0; i < array.length; i = i + 1) {
 newArray[i] = callback(array[i], i);
 }
 return newArray;
}
```

Now we have our shiny new `map` function, we can use it like so:

```
var getElement = function(id) {
 return document.getElementById(id);
};

var elements = map(getElement, ids);
```

The `map` function takes small, trivial functions and and turns them into super-hero functions--it multiplies the function's effectiveness by applying it to an entire array with just one call.

Like `forEach`, `map` is so handy that modern implementations have it as a built-in method for array objects. You can call the built-in method like this:

```
var ids = ['unicorn', 'fairy', 'kitten'];
var getElement = function(id) {
 return document.getElementById(id);
};
var elements = ids.map(getElement, ids);
```

You can read more about the built-in [`map` method in the MDN JavaScript reference](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map).

### Reduce

Now, `map` is very handy, but we can make an even more powerful function if we take an entire array and return just one value. That may seem a little counter-intuitive at first--how can a function that returns one value instead of many be _more_ powerful? To find out why, we have to first look at how this function works.

To illustrate, let's consider two similar problems:

1. Given an array of numbers, calculate the sum; and
2. Given an array of words, join them together with a space between each word.[[1]](http://jrsinclair.com/articles/2016/gentle-introduction-to-functional-javascript-arrays/#fn:1 "see footnote")

Now, these might seem like silly, trivial examples--and they are. But, bear with me, once we see how this `reduce` function works, we'll apply it in more interesting ways.

So, the 'procedural' way to solve these problems is, again, with for-loops:

```
// Given an array of numbers, calculate the sum
var numbers = [1, 3, 5, 7, 9];
var total = 0;
for (i = 0; i < numbers.length; i = i + 1) {
 total = total + numbers[i];
}
// total is 25

// Given an array of words, join them together with a space between each word.
var words = ['sparkle', 'fairies', 'are', 'amazing'];
var sentence = '';
for (i = 0; i < words.length; i++) {
 sentence = sentence + ' ' + words[i];
}
// ' sparkle fairies are amazing'
```

These two solutions have a lot in common. They each use a for-loop to iterate over the array; they each have a working variable (`total` and `sentence`); and they both set their working value to an initial value.

Let's refactor the inner part of each loop, and turn it into a function:

```
var add = function(a, b) {
 return a + b;
}

// Given an array of numbers, calculate the sum
var numbers = [1, 3, 5, 7, 9];
var total = 0;
for (i = 0; i < numbers.length; i = i + 1) {
 total = add(total, numbers[i]);
}
// total is 25

function joinWord(sentence, word) {
 return sentence + ' ' + word;
}

// Given an array of words, join them together with a space between each word.
var words = ['sparkle', 'fairies', 'are', 'amazing'];
var sentence = '';
for (i = 0; i < words.length; i++) {
 sentence = joinWord(sentence, words[i]);
}
// 'sparkle fairies are amazing'
```

Now, this is hardly more concise but the pattern becomes clearer. Both inner functions take the working variable as their first parameter, and the current array element as the second. Now that we can see the pattern more clearly, we can move those untidy for-loops into a function:

```
var reduce = function(callback, initialValue, array) {
 var working = initialValue;
 for (var i = 0; i < array.length; i = i + 1) {
 working = callback(working, array[i]);
 }
 return working;
};
```

Now we have a shiny new `reduce` function, let's take it for a spin:

```
var total = reduce(add, 0, numbers);
var sentence = reduce(joinWord, '', words);
```

Like `forEach` and `map`, `reduce` is also built in to the standard JavaScript array object. One would use it like so:

```
var total = numbers.reduce(add, 0);
var sentence = words.reduce(joinWord, '');
```

You can read more about the built-in [`reduce` method in the MDN JavaScript Reference](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)

## Putting it all together

Now, as we mentioned before, these are trivial examples--the `add` and `joinWord` functions ave fairly simple--and that's kind of the point really. Smaller, simpler functions are easier to think about and easier to test. Even when we take two small, simple functions and combine them (like `add` and `reduce`, for example), the result is still easier to reason about than a single giant, complicated function. But, with that said, we can do more interesting things than add numbers together.

Let's try doing something a little more complicated. We'll start off with some inconveniently formatted data, and use our `map` and `reduce` functions to transform it into an HTML list. Here is our data:[[2]](http://jrsinclair.com/articles/2016/gentle-introduction-to-functional-javascript-arrays/#fn:2 "see footnote")

```
var ponies = [
 [
 ['name', 'Fluttershy'],
 ['image', 'http://tinyurl.com/gpbnlf6'],
 ['description', 'Fluttershy is a female Pegasus pony and one of the main characters of My Little Pony Friendship is Magic.']
 ],
 [
 ['name', 'Applejack'],
 ['image', 'http://tinyurl.com/gkur8a6'],
 ['description', 'Applejack is a female Earth pony and one of the main characters of My Little Pony Friendship is Magic.']
 ],
 [
 ['name', 'Twilight Sparkle'],
 ['image', 'http://tinyurl.com/hj877vs'],
 ['description', 'Twilight Sparkle is the primary main character of My Little Pony Friendship is Magic.']
 ]
];
```

The data is not terribly tidy. It would be much cleaner if those inner arrays were nicely formatted objects. Now, previously, we used the `reduce` function to calculate simple values like strings and numbers, but nobody said that the value returned by `reduce` has to be simple. We can use it with objects, arrays, or even DOM elements. Let's create a function that takes one of those inner arrays (like `['name', 'Fluttershy']`) and adds that key/value pair to an object.

```
var addToObject = function(obj, arr) {
 obj[arr[0]] = arr[1];
 return obj;
};
```

With this `addToObject` function, then we can convert each 'pony' array into an object:

```
var ponyArrayToObject = function(ponyArray) {
 return reduce(addToObject, {}, ponyArray);
};
```

If we then use our `map` function we can convert the whole array into something more tidy:

```
var tidyPonies = map(ponyToObject, ponies);
```

We now have an array of pony objects. With a little help from [Thomas Fuchs' tweet-sized templating engine](http://mir.aculo.us/2011/03/09/little-helpers-a-tweet-sized-javascript-templating-engine/), we can use `reduce` again to convert this into an HTML snippet. The template function takes a template string and an object, and anywhere it finds mustache-wrapped words (like, `{name}` or `{image}`), it replaces them with the correponding value from the object. For example:

```
var data = { name: "Fluttershy" };
t("Hello {name}!", data);
// "Hello Fluttershy!"

data = { who: "Fluttershy", time: Date.now() };
t("Hello {name}! It's {time} ms since epoch.", data);
// "Hello Fluttershy! It's 1454135887369 ms since epoch."
```

So, if we want to convert a pony object to a list item, we can do something like this:

```
var ponyToListItem = function(pony) {
 var template = '<li><img src="{image}" alt="{name}"/>' +
 '<div><h3>{name}</h3><p>{description}</p>' +
 '</div></li>';
 return t(template, pony);
};
```

That gives us a way to convert an individual item into HTML, but to convert the whole array, we'll need our `reduce` and `joinWord` functions:

```
var ponyList = map(ponyToListItem, tidyPonies);
var html = '<ul>' + reduce(joinWord, '', ponyList) + '</ul>';
```

You can see the whole thing put together at <http://jsbin.com/wuzini/edit?html,js,output>

Once you understand the patterns that `map` and `reduce` are suited to, you may find yourself never needing to write an old-style for-loop again. In fact, it's a useful challenge to see if you can completely avoid writing for-loops on your next project. Once you've used `map` and `reduce` a few times, you'll start to notice even more patterns that can be abstracted. Some common ones include [filtering](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter), and [plucking](http://ramdajs.com/docs/#pluck) values from an array. Since these patterns come up quite often, people have put together functional programming libraries so that you can re-use code to address common patterns. Some of the more popular libraries include:

- [Ramda](http://ramdajs.com/),
- [Lodash](https://lodash.com/), and
- [Underscore](http://underscorejs.org/).

Now that you've seen how handy passing functions around as variables can be, especially when dealing with lists, you should have a whole suite of new techniques in your metaphorical tool-belt. And if that's where you choose to leave it, that's OK. You can quit reading here and nobody will think any less of you. You can go on to be a productive, successful programmer and never trouble your dreams with the complexities of _partial application_, _currying_ or _composition_. These things are not for everyone.

But, if you're up for a little bit of adventure, you can [keep reading and see how deep the rabbit hole goes...](http://jrsinclair.com/articles/2016/gentle-introduction-to-functional-javascript-functions/)
