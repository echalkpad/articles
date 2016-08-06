# Understanding native JavaScript array methods – Ben Frain

[Original URL](https://benfrain.com/understanding-native-javascript-array-methods/)

> 2Days 2 days since this post was last revised. All content should be accurate. This is one of those posts there are already plenty of on the net. There's nothing new here. However, part of my...

<span class="post-DaysSince_Info"><strong>2*</strong>Days*</span>

 2 days since this post was last revised. All content should be accurate.

This is one of those posts there are already plenty of on the net. There's nothing new here. However, part of my process in understanding anything new is to re-explain what I (think) I have learnt to someone new. That's you (sorry).

There are many times in JavaScript when you want to do something with an array of 'stuff'.

Perhaps you want to create a subset of the array elements. Perhaps you want to create something else with each of the elements. Historically, I have always used a 'for loop' for this purpose.

Consider a simple array:

```
var main = [
 "one", "two", "three", "four", "five", "six", "seven", "eight", "nine", "ten", "eleven"
]
```

Using a 'for loop' to `console.log` each one out would look like this:

```
for (var i=0; i < main.length; i++) {
 console.log(main[i]);
}
```

Even if, like me, you write very little JavaScript, that loop should be easy enough to grasp conceptually, even if writing the syntax isn't second nature to you. We create a variable and set it to zero and increment it once each time as we count up to the total number of items in the array. We use the `i` as the marker to reach inside the `main` array and `console.log` each item.

I like using `let` in place of `var` in these loops these days but it's not as well supported so we'll stick with var here.

Using a for loop like this is probably the most widely understood and taught way of using the elements in an array. However, I've recently started playing about with the native Array methods of JavaScript, heavily inspired by 'FunFunFunction' on YouTube: <https://www.youtube.com/channel/UCO1cgjhGzsSYb1rsB4bFe4Q>. [Mattias Petter Johansson](http://twitter.com/@mpjme) has a whole series on Functional JavaScript that includes using these methods and I really appreciate the fun and energetic manner in which he delivers it. Highly recommended.

## forEach

The first native Array method I want to explain the use of is `forEach`. We'll start here as its name is very descriptive. It lets you do something `for each` element in the array. This is what it looks like:

```
main.forEach(function(item) {
 console.log(item); // logs each item
});
```

You could also just pass another function to `forEach` if you wanted to abstract the functionality you are applying to each element in the array. If we made a simple logging function we could use it like this:

```
main.forEach(logger);

function logger(item) {
 console.log(item);
}
```

The function that is called each time is auto-magically passed the element reference each time (and the iteration count if needed) so there is no need to pass anything as an argument to the `logger` function from the `forEach` method.

## filter

The filter method lets you create a new, filtered version of an existing array. Let's look at a simple use case.

Suppose we take our example array from before:

```
var main = [
 "one", "two", "three", "four", "five", "six", "seven", "eight", "nine", "ten", "eleven"
]
```

And we want to filter that array down to only include elements that match another array. Our other array looks like this:

```
var firstPattern = [0,2,4,5,6];
```

Like, `forEach` if we pass `filter` a function it auto-magically sends the item and iteration count to that function. So, let's use a function for each element in the original array like this:

```
var matchPattern = main.filter(itemMatchesPattern);
```

We are creating a new array called `matchPattern` that will include elements from the `main` array that match our criteria.

We will define the criteria as a new function that returns a true/false result based on whether the count number of each item in the `main` array (remember we are iterating through each item in this array) exists in our new `firstPattern` array. If it does exist it will be included in the `matchPattern` array. That's harder to explain than it is to write. It looks like this:

```
function itemMatchesPattern(item, count) {
 return firstPattern.includes(count);
}
```

You can see here that `itemMatchesPattern` takes two arguments, the element itself (`item`) and the iteration count (`count`). It then returns true or false for whether that count number exists in the `firstPattern` array. The filter we have applied to the `main` array only includes items that return true so the `matchPattern` array we are making only includes these 'true' elements. Logging out `matchPattern` therefore gives us this:

```
[ 'one', 'three', 'five', 'six', 'seven' ]
```

Nice. However, that `itemMatchesPattern` function isn't very flexible. What if we wanted to match against another pattern?

Something like this:

```
var secondPattern = [1,3,4,7];
```

This had me stumped at first. I couldn't figure out how to pass a different array to the `itemMatchesPattern` function to check against. [Thank goodness for Stack Overflow](http://stackoverflow.com/a/7759286/1147859)!

So, we can amend our function to this:

```
function itemMatchesPattern(arrayName) {
 return function (item, count) {
 return arrayName.includes(count);
 }
}
```

The function returns another function (which in turn does what it did before). However because the nested function has access to the outer scope it can now make use of the `arrayName` that's being passed in when it is invoked. So, this is how it is now invoked:

```
var matchPattern = main.filter(itemMatchesPattern(firstPattern));
```

Or if we wanted to use filter but use our `itemMatchesPattern` with a different pattern:

```
var matchPattern = main.filter(itemMatchesPattern(secondPattern));
```

Our `itemMatchesPattern` function can be said to be 'pure', meaning it takes input and returns output without any 'side effects' (in that it isn't amending anything else around it). I had no idea what that phrase meant before playing about with these array methods so I'm glad I did.

## map

The `map()` method creates a new array which is the result of mapping a function onto each element on the existing array. Let's see that with an example:

```
var mapped = main.map(function(item) {
 return item.toUpperCase();
});
```

If we log mapped (it's also being applied to our original `main` array) we get this:

```
[ 'ONE', 'TWO', 'THREE', 'FOUR', 'FIVE', 'SIX', 'SEVEN', 'EIGHT', 'NINE', 'TEN', 'ELEVEN' ]
```

You may be wondering how this differs from `forEach` that we looked at first. The key thing is that `forEach` is executing on the original array, so may inadvertently effect it. Map on the other hand is creating a new array which is the result of a function being run against each element in the original array.

## reduce

The last array method I looked at was `reduce()`. This is kind of a custom array method because it's pretty flexible in what you can do with it. I'm going to explain a very simple example. So simple in fact that it could be done by chaining the other methods but humour me, OK?

Now, at your age you should know that E's are bad for you. So I want to create a new array that only includes numbers from main that don't contain the substring 'e'.

```
var reduced = main.reduce(function(reducedArray, item) { // 1.
 if (item.toString().indexOf("e") === -1) {
 reducedArray.push(item); // 2.
 }
 return reducedArray; // 3.
}, []); // 4.
```

Right, this takes a bit of wrapping your head around. I've marked the code with numbered references.

- 1 is where you pass in to the function the arguments that reduce accepts.
- 2 is where we are pushing any elements that match the criteria of the if statement
- 3 is where we are returning the result of the final invocation of this method (the sum total of elements we push into the new array in our example)
- 4 is where you define what the starting point of your reduction will be. I know, WHAAAATTT?

The last part of that function, where we write `[]` took some time for me to understand. Here's my best shot at explaining it:

The first argument we pass to the function that reduce accepts is the value of the previous invocation of the function. In our case, we are making a reference to `reducedArray` which we are defining as an empty array at the end of the function (notice the `[]` before the closing bracket). So, when the function is first called `reducedArray` is just an empty array (`[]`). Then each time it is invoked by `reduce()` we are adding to that existing array (hence the array gets (potentially) populated each time). You can stick whatever you want into that new array each pass until it is returned on the final pass.

The second argument reduce() accepts is the item it's currently 'working on'. However, reduce() can also accept the current index (think of this as the `i` of your for loop) as a third argument and the original array as the fourth (in our case `main`). This is obviously handy if you want to do anything to the main array at the same time.

So, the result of logging `reduced` is:

```
[ 'two', 'four', 'six' ]
```

Not an E in site.

This is a brief overview of some of the Array methods popular in functional programming. For a more authoritative explanation, ensure you check out [Mattias Petter Johansson](https://www.youtube.com/channel/UCO1cgjhGzsSYb1rsB4bFe4Q) on YouTube. I also found a good intro to Functional JavaScript from Anjana Vakil, here: [https://www.youtube.com/watch?v=e–5obm1G_FY](https://www.youtube.com/watch?v=e-5obm1G_FY).

As ever, I found MDN to be the 'Daddy' when it came to documentation, although I'll be honest and say that I often struggle conceptually when trying to understand docs of this nature: <https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array>.

### About The Author

![](https://secure.gravatar.com/avatar/faf2d64943cca673a5de3a565966e8bf?s=100&d=retro&r=pg)
