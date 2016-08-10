# How to Use Map, Filter, & Reduce in JavaScript

[Original URL](http://code.tutsplus.com/tutorials/how-to-use-map-filter-reduce-in-javascript--cms-26209)

> Functional programming has been making quite a splash in the development world these days. And for good reason: Functional techniques can help you write more declarative code that is easier...

Functional programming has been making quite a splash in the development world these days. And for good reason: Functional techniques can help you write more declarative code that is easier to understand at a glance, refactor, and test.

One of the cornerstones of functional programming is its special use of lists and list operations. And those things are exactly what the sound like they are: arrays of things, and the stuff you do to them. But the functional mindset treats them a bit differently than you might expect.

This article will take a close look at what I like to call the "big three" list operations: `map`, `filter`, and `reduce`. Wrapping your head around these three functions is an important step towards being able to write clean functional code, and opens the doors to the vastly powerful techniques of functional and reactive programming.

It also means you'll never have to write a `for` loop again.

Curious? Let's dive in.

## A Map From List to List

Often, we find ourselves needing to take an array and modify every element in it in exactly the same way. Typical examples of this are squaring every element in an array of numbers, retrieving the name from a list of users, or running a regex against an array of strings.

`map` is a method built to do exactly that. It's defined on `Array.prototype`, so you can call it on any array, and it accepts a callback as its first argument.

When you call `map` on an array, it executes that callback on every element within it, returning a _new_ array with all of the values that the callback returned.

Under the hood, `map` passes three arguments to your callback:

1. The _current item_ in the array
2. The _array index_ of the current item
3. The _entire array_ you called map on

Let's look at some code.

### `map` in Practice

Suppose we have an app that maintains an array of your tasks for the day. Each `task` is an object, each with a `name` and `duration` property:

```
// Durations are in minutes

var tasks = [

 {

 'name' : 'Write for Envato Tuts+',

 'duration' : 120

 },

 {

 'name' : 'Work out',

 'duration' : 60

 },

 {

 'name' : 'Procrastinate on Duolingo',

 'duration' : 240

 }

];
```

Let's say we want to create a new array with just the name of each task, so we can take a look at everything we've gotten done today. Using a `for` loop, we'd write something like this:

```
var task_names = [];

for (var i = 0, max = tasks.length; i < max; i += 1) {

 task_names.push(tasks[i].name);

}
```

JavaScript also offers a `forEach` loop. It functions like a `for` loop, but manages all the messiness of checking our loop index against the array length for us:

```
var task_names = [];

tasks.forEach(function (task) {

 task_names.push(task.name);

});
```

Using `map`, we can write:

```
var task_names = tasks.map(function (task, index, array) {

 return task.name; 

});
```

I include the `index` and `array` parameters to remind you that they're there if you need them. Since I didn't use them here, though, you could leave them out, and the code would run just fine.

There are a few important differences between the two approaches:

1. Using `map`, you don't have to manage the state of the `for` loop yourself.
2. You can operate on the element directly, rather than having to index into the array.
3. You don't have to create a new array and `push` into it. `map` returns the finished product all in one go, so we can simply assign the return value to a new variable.
4. You _do_ have to remember to include a `return` statement in your callback. If you don't, you'll get a new array filled with `undefined`.

Turns out, _all_ of the functions we'll look at today share these characteristics.

The fact that we don't have to manually manage the state of the loop makes our code simpler and more maintainable. The fact that we can operate directly on the element instead of having to index into the array makes things more readable.

Using a `forEach` loop solves both of these problems for us. But `map` still has at least two distinct advantages:

1. `forEach` returns `undefined`, so it doesn't chain with other array methods. `map` returns an array, so you _can_ chain it with other array methods.
2. `map` returns__an array with the finished product, rather than requiring us to mutate an array inside the loop.

Keeping the number of places where you modify state to an absolute minimum is an important tenet of functional programming. It makes for safer and more intelligible code.

Now is also a good time to point out that if you're in Node, testing these examples in the Firefox browser console, or using [Babel](https://babeljs.io/) or [Traceur](https://github.com/google/traceur-compiler), you can write this more concisely with ES6 arrow functions:

```
var task_names = tasks.map((task) => task.name );
```

Arrow functions let us leave out the `return` keyword in one-liners.

It doesn't get much more readable than that.

### Gotchas

The callback you pass to `map` must have an explicit `return` statement, or `map` will spit out an array full of `undefined`. It's not hard to remember to include a `return` value, but it's not hard to forget.

If you _do_ forget, `map` won't complain. Instead, it'll quietly hand back an array full of nothing. Silent errors like that can be surprisingly hard to debug.

Fortunately, this is the _only_ gotcha with **`map`. But it's a common enough pitfall that I'm obliged to emphasize: Always make sure your callback contains a `return` statement!

### Implementation

Reading implementations is an important part of understanding. So, let's write our own lightweight `map` to better understand what's going on under the hood. If you want to see a production-quality implementation, check out [Mozilla's polyfill at MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map).

```
var map = function (array, callback) {

 var new_array = [];

 array.forEach(function (element, index, array) {
 new_array.push(callback(element)); 
 });

 return new_array;

};

var task_names = map(tasks, function (task) {

 return task.name;

});
```

This code accepts an array and a callback function as arguments. It then creates a new array; executes the callback on each element on the array we passed in; pushes the results into the new array; and returns the new array. If you run this in your console, you'll get the same result as before. Just make sure you initialize `tasks` before you test it out!

While we're using a for loop under the hood, wrapping it up into a function hides the details and lets us work with the abstraction instead.

That makes our code more declarative--it says _what_ to do, not _how_ to do it. You'll appreciate how much more readable, maintainable, and, erm, _debuggable_ this can make your code.

## Filter Out the Noise

The next of our array operations is `filter`. It does exactly what it sounds like: It takes an array, and filters out unwanted elements.

Like `map`, `filter` is defined on `Array.prototype`. It's available on any array, and you pass it a callback as its first argument. `filter` executes that callback on each element of the array, and spits out a _new_ array containing _only_ the elements for which the callback returned `true`.

Also like `map`, `filter` passes your callback three arguments:

1. The _current item_
2. The _current index_
3. The _array_ you called `filter` on

### `filter` in Practice

Let's revisit our task example. Instead of pulling out the names of each task, let's say I want to get a list of just the tasks that took me two hours or more to get done.

Using `forEach`, we'd write:

```
var difficult_tasks = [];

tasks.forEach(function (task) {
 if (task.duration >= 120) {
 difficult_tasks.push(task);
 }
});
```

With `filter`:

```
var difficult_tasks = tasks.filter(function (task) {
 return task.duration >= 120;
});

// Using ES6
var difficult_tasks = tasks.filter((task) => task.duration >= 120 );
```

Here, I've gone ahead and left out the `index` and `array` arguments to our callback, since we don't use them.

Just like `map`, `filter` lets us:

- avoid mutating an array inside a `forEach` or `for` loop
- assign its result directly to a new variable, rather than push into an array we defined elsewhere

### Gotchas

The callback you pass to `map` has to include a return statement if you want it to function properly. With `filter`, you also have to include a return statement, and you _must_ make sure it returns a boolean value.

If you forget your return statement, your callback will return `undefined`, which `filter` will unhelpfully coerce to `false`. Instead of throwing an error, it will silently return an empty array!

If you go the other route, and return something that's **isn't explicitly `true` or `false`, then `filter` will try to figure out what you meant by applying [JavaScript's coercion rules](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20&%20grammar/ch4.md). More often than not, this is a bug. And, just like forgetting your return statement, it'll be a silent one.

_Always_ make sure your callbacks include an explicit return statement. And _always_ make sure your callbacks in **`filter` return `true` or `false`. Your sanity will thank you.

### Implementation

Once again, the best way to understand a piece of code is... well, to write it. Let's roll our own lightweight `filter`. The good folks at Mozilla have an [industrial-strength polyfill](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) for you to read, too.

```
var filter = function (array, callback) {

 var filtered_array = [];

 array.forEach(function (element, index, array) {
 if (callback(element, index, array)) {
 filtered_array.push(element); 
 }
 });

 return filtered_array;

};
```

## Reducing Arrays

`map` creates a new array by transforming every element in an array, individually. `filter` creates a new array by removing elements that don't belong. `reduce`, on the other hand, takes all of the elements in an array, and _reduces_ them into a single value.

Just like `map` and `filter`, `reduce` is defined on `Array.prototype` and so available on any array, and you pass a callback as its first argument. But it also takes an optional second argument: the value to start combining all your array elements into.

`reduce` passes your callback four arguments:

1. The _current value_
2. The _previous value_ 
3. The _current index_
4. The _array_ you called `reduce` on

Notice that the callback gets a _previous value_ on each iteration. On the first iteration, there _is_ no previous value. This is why you have the option to pass `reduce` an initial value: It acts as the "previous value" for the first iteration, when there otherwise wouldn't be one.

Finally, bear in mind that `reduce` returns a _*single value,_ not* an array containing a single item. This is more important than it might seem, and I'll come back to it in the examples.

### `reduce` in Practice

Since `reduce` is the function that people find most alien at first, we'll start by walking step by step through something simple.

Let's say we want to find the sum of a list of numbers. Using a loop, that looks like this:

```
var numbers = [1, 2, 3, 4, 5],
 total = 0;

numbers.forEach(function (number) {
 total += number;
});
```

While this isn't a bad use case for `forEach`, `reduce` still has the advantage of allowing us to avoid mutation. With `reduce`, we would write:

```
var total = [1, 2, 3, 4, 5].reduce(function (previous, current) {
 return previous + current;
}, 0);
```

First, we call `reduce` on our list of `numbers.` We pass it a callback, which accepts the previous value and current value as arguments, and returns the result of adding them together. Since we passed `0` as a second argument to `reduce`, it'll use that as the value of `previous` on the first iteration.

If we take it step by step, it looks like this:

Iteration | Previous | Current | Total
--------- | -------- | ------- | -----
1         | 0        | 1       | 1
2         | 1        | 2       | 3
3         | 3        | 3       | 6
4         | 6        | 4       | 10
5         | 10       | 5       | 15

If you're not a fan of tables, run this snippet in the console:

```
var total = [1, 2, 3, 4, 5].reduce(function (previous, current, index) {
 var val = previous + current;
 console.log("The previous value is " + previous + 
 "; the current value is " + current +
 ", and the current iteration is " + (index + 1));
 return val;
}, 0);

console.log("The loop is done, and the final value is " + total + ".");
```

To recap: `reduce` iterates over all the elements of an array, combining them however you specify in your callback. On every iteration, your callback has access to the _previous_ _value_, which is the _total-so-far_, or _accumulated value_; the _current value_; the _current index;_ and the entire _array_, if you need them.

Let's turn back to our tasks example. We've gotten a list of task names from `map`, and a filtered list of tasks that took a long time with... well, `filter`.

What if we wanted to know the total amount of time we spent working today?

Using a `forEach` loop, you'd write:

```
var total_time = 0;

tasks.forEach(function (task) {
 // The plus sign just coerces 
 // task.duration from a String to a Number
 total_time += (+task.duration);
});
```

With `reduce`, that becomes:

```
var total_time = tasks.reduce(function (previous, current) {
 return previous + current;
}, 0);

// Using arrow functions
var total_time = tasks.reduce((previous, current) previous + current );
```

Easy.

That's almost all there is to it. Almost, because JavaScript provides us with one more little-known method, called `reduceRight`. In the examples above, `reduce` started at the _first_ item in the array, iterating from left to right:

```
var array_of_arrays = [[1, 2], [3, 4], [5, 6]];
var concatenated = array_of_arrays.reduce( function (previous, current) {
 return previous.concat(current);
});

console.log(concatenated); // [1, 2, 3, 4, 5, 6];
```

`reduceRight` does the same thing, but in the opposite direction:

```
var array_of_arrays = [[1, 2], [3, 4], [5, 6]];
var concatenated = array_of_arrays.reduceRight( function (previous, current) {
 return previous.concat(current);
});

console.log(concatenated); // [5, 6, 3, 4, 1, 2];
```

I use `reduce` every day, but I've never needed `reduceRight`. I reckon you probably won't, either. But in the event you ever do, now you know it's there.

### Gotchas

The three big gotchas with `reduce` are:

1. Forgetting to `return`
2. Forgetting an initial value
3. Expecting an array when `reduce` returns a single value

Fortunately, the first two are easy to avoid. Deciding what your initial value should be depends on what you're doing, but you'll get the hang of it quickly.

The last one might seem a bit strange. If `reduce` only ever returns a single value, why would you expect an array?

There are a few good reasons for that. First, `reduce` always returns a single _value_, not always a single _number_. If you reduce an array of arrays, for instance, it will return a single array. If you're in the habit or reducing arrays, it would be fair to expect that an array containing a single item wouldn't be a special case.

Second, if `reduce` _did_ return an array with a single value, it would naturally play nice with `map` and `filter`, and other functions on arrays that you're likely to be using with it.

### Implementation

Time for our last look under the hood. As usual, Mozilla has a [bulletproof polyfill for reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) if you want to check it out.

```
var reduce = function (array, callback, initial) {
 var accumulator = initial || 0;

 array.forEach(function (element) {
 accumulator = callback(accumulator, array[i]);
 });

 return accumulator;
};
```

Two things to note, here:

1. This time, I used the name `accumulator` instead of `previous`. This is what you'll usually see in the wild.
2. I assign `accumulator` an initial value, if a user provides one, and default to `0`, if not. This is how the real `reduce` behaves, as well.

## Putting It Together: Map, Filter, Reduce, and Chainability

At this point, you might not be _that_ impressed.

Fair enough: `map`, `filter`, and `reduce`, on their own, aren't awfully interesting.

After all, their true power lies in their chainability.

Let's say I want to do the following:

1. Collect two days' worth of tasks.
2. Convert the task durations to hours, instead of minutes.
3. Filter out everything that took two hours or more.
4. Sum it all up.
5. Multiply the result by a per-hour rate for billing.
6. Output a formatted dollar amount.

First, let's define our tasks for Monday and Tuesday:

```
var monday = [
 {
 'name' : 'Write a tutorial',
 'duration' : 180
 },
 {
 'name' : 'Some web development',
 'duration' : 120
 }
 ];

var tuesday = [
 {
 'name' : 'Keep writing that tutorial',
 'duration' : 240
 },
 {
 'name' : 'Some more web development',
 'duration' : 180
 },
 {
 'name' : 'A whole lot of nothing',
 'duration' : 240
 }
 ];

var tasks = [monday, tuesday];
```

And now, our lovely-looking transformation:

```
 var result = tasks.reduce(function (accumulator, current) {
 return accumulator.concat(current);
 }).map(function (task) {
 return (task.duration / 60);
 }).filter(function (duration) {
 return duration >= 2;
 }).map(function (duration) {
 return duration data.json data_ml developing_a_street_basketball_game_part_i_getting_workflow_ready_—_whitestormjs_framework.epub developing_a_street_basketball_game_part_i_getting_workflow_ready_—_whitestormjs_framework.md devops docker embedded from_zero_to_frontend_hero_part_1_—_free_code_camp.epub from_zero_to_frontend_hero_part_1_—_free_code_camp.md gaming gdb-example-ncurses.epub github go how_i_learned_to_stop_worrying_and_love_ephemeral_storage__silicon_valley_data_science.epub how_i_learned_to_stop_worrying_and_love_ephemeral_storage__silicon_valley_data_science.md how_to_check_diskspace_with_the_ncdu_utility.epub how_to_check_diskspace_with_the_ncdu_utility.md html immutable_collections_for_javascript.epub immutable_collections_for_javascript.md javascript linux mobile node-hero-tutorials.epub png programming research_paper_categorization_using_machine_learning_and_nlp.epub research_paper_categorization_using_machine_learning_and_nlp.md science social task_automation_with_npm_run.md tidy understanding_javascript_promises_pt_i_background__basics.epub understanding_javascript_promises_pt_i_background__basics.md understanding_object_streams.epub understanding_object_streams.md url_to_filename.csv web_dev writing_a_javascript_framework__execution_timing_beyond_settimeout.epub writing_a_javascript_framework__execution_timing_beyond_settimeout.md writing_a_javascript_framework__project_structuring.epub writing_a_javascript_framework__project_structuring.md 25;
 }).reduce(function (accumulator, current) {
 return [(+accumulator) + (+current)];
 }).map(function (dollar_amount) {
 return '$' + dollar_amount.toFixed(2);
 }).reduce(function (formatted_dollar_amount) {
 return formatted_dollar_amount;
 });
```

Or, more concisely:

```
 // Concatenate our 2D array into a single list
var result = tasks.reduce((acc, current) => acc.concat(current))
 // Extract the task duration, and convert minutes to hours
 .map((task) => task.duration / 60)
 // Filter out any task that took less than two hours
 .filter((duration) => duration >= 2)
 // Multiply each tasks' duration by our hourly rate
 .map((duration) => duration data.json data_ml developing_a_street_basketball_game_part_i_getting_workflow_ready_—_whitestormjs_framework.epub developing_a_street_basketball_game_part_i_getting_workflow_ready_—_whitestormjs_framework.md devops docker embedded from_zero_to_frontend_hero_part_1_—_free_code_camp.epub from_zero_to_frontend_hero_part_1_—_free_code_camp.md gaming gdb-example-ncurses.epub github go how_i_learned_to_stop_worrying_and_love_ephemeral_storage__silicon_valley_data_science.epub how_i_learned_to_stop_worrying_and_love_ephemeral_storage__silicon_valley_data_science.md how_to_check_diskspace_with_the_ncdu_utility.epub how_to_check_diskspace_with_the_ncdu_utility.md html immutable_collections_for_javascript.epub immutable_collections_for_javascript.md javascript linux mobile node-hero-tutorials.epub png programming research_paper_categorization_using_machine_learning_and_nlp.epub research_paper_categorization_using_machine_learning_and_nlp.md science social task_automation_with_npm_run.md tidy understanding_javascript_promises_pt_i_background__basics.epub understanding_javascript_promises_pt_i_background__basics.md understanding_object_streams.epub understanding_object_streams.md url_to_filename.csv web_dev writing_a_javascript_framework__execution_timing_beyond_settimeout.epub writing_a_javascript_framework__execution_timing_beyond_settimeout.md writing_a_javascript_framework__project_structuring.epub writing_a_javascript_framework__project_structuring.md 25)
 // Combine the sums into a single dollar amount
 .reduce((acc, current) => [(+acc) + (+current)])
 // Convert to a "pretty-printed" dollar amount
 .map((amount) => '$' + amount.toFixed(2))
 // Pull out the only element of the array we got from map
 .reduce((formatted_amount) =>formatted_amount); 
```

If you've made it this far, this should be pretty straightforward. There are two bits of weirdness to explain, though.

First, on line 10, I have to write:

```
// Remainder omitted
reduce(function (accumulator, current) {
 return [(+accumulator) + (+current_];
})
```

Two things to explain here:

1. The plus signs in front of `accumulator` and `current` coerce their values to numbers. If you don't do this, the return value will be the rather useless string, `"12510075100"`.
2. If don't wrap that sum in brackets, `reduce` will spit out a single value, _not_ an array. That would end up throwing a `TypeError`, because you can only use `map` on an array!

The second bit that might make you a bit uncomfortable is the last `reduce`, namely:

```
// Remainder omitted
map(function (dollar_amount) {
 return '$' + dollar_amount.toFixed(2);
}).reduce(function (formatted_dollar_amount) {
 return formatted_dollar_amount;
});
```

That call to `map` returns an array containing a single value. Here, we call `reduce` to pull out that value.

The other way to do this would be to remove the call to reduce, and index into the array that `map` spits out:

```
var result = tasks.reduce(function (accumulator, current) {
 return accumulator.concat(current);
 }).map(function (task) {
 return (task.duration / 60);
 }).filter(function (duration) {
 return duration >= 2;
 }).map(function (duration) {
 return duration data.json data_ml developing_a_street_basketball_game_part_i_getting_workflow_ready_—_whitestormjs_framework.epub developing_a_street_basketball_game_part_i_getting_workflow_ready_—_whitestormjs_framework.md devops docker embedded from_zero_to_frontend_hero_part_1_—_free_code_camp.epub from_zero_to_frontend_hero_part_1_—_free_code_camp.md gaming gdb-example-ncurses.epub github go how_i_learned_to_stop_worrying_and_love_ephemeral_storage__silicon_valley_data_science.epub how_i_learned_to_stop_worrying_and_love_ephemeral_storage__silicon_valley_data_science.md how_to_check_diskspace_with_the_ncdu_utility.epub how_to_check_diskspace_with_the_ncdu_utility.md html immutable_collections_for_javascript.epub immutable_collections_for_javascript.md javascript linux mobile node-hero-tutorials.epub png programming research_paper_categorization_using_machine_learning_and_nlp.epub research_paper_categorization_using_machine_learning_and_nlp.md science social task_automation_with_npm_run.md tidy understanding_javascript_promises_pt_i_background__basics.epub understanding_javascript_promises_pt_i_background__basics.md understanding_object_streams.epub understanding_object_streams.md url_to_filename.csv web_dev writing_a_javascript_framework__execution_timing_beyond_settimeout.epub writing_a_javascript_framework__execution_timing_beyond_settimeout.md writing_a_javascript_framework__project_structuring.epub writing_a_javascript_framework__project_structuring.md 25;
 }).reduce(function (accumulator, current) {
 return [(+accumulator) + (+current)];
 }).map(function (dollar_amount) {
 return '$' + dollar_amount.toFixed(2);
 })[0];
```

That's perfectly correct. If you're more comfortable using an array index, go right ahead.

But I encourage you not to. One of the most powerful ways to use these functions is in the realm of reactive programming, where you won't be free to use array indices. Kicking that habit now will make learning reactive techniques much easier down the line.

Finally, let's see how our friend the `forEach` loop would get it done:

```
var concatenated = monday.concat(tuesday),
 fees = [],
 formatted_sum,
 hourly_rate = 25,
 total_fee = 0;

concatenated.forEach(function (task) {
 var duration = task.duration / 60;

 if (duration >= 2) {
 fees.push(duration data.json data_ml developing_a_street_basketball_game_part_i_getting_workflow_ready_—_whitestormjs_framework.epub developing_a_street_basketball_game_part_i_getting_workflow_ready_—_whitestormjs_framework.md devops docker embedded from_zero_to_frontend_hero_part_1_—_free_code_camp.epub from_zero_to_frontend_hero_part_1_—_free_code_camp.md gaming gdb-example-ncurses.epub github go how_i_learned_to_stop_worrying_and_love_ephemeral_storage__silicon_valley_data_science.epub how_i_learned_to_stop_worrying_and_love_ephemeral_storage__silicon_valley_data_science.md how_to_check_diskspace_with_the_ncdu_utility.epub how_to_check_diskspace_with_the_ncdu_utility.md html immutable_collections_for_javascript.epub immutable_collections_for_javascript.md javascript linux mobile node-hero-tutorials.epub png programming research_paper_categorization_using_machine_learning_and_nlp.epub research_paper_categorization_using_machine_learning_and_nlp.md science social task_automation_with_npm_run.md tidy understanding_javascript_promises_pt_i_background__basics.epub understanding_javascript_promises_pt_i_background__basics.md understanding_object_streams.epub understanding_object_streams.md url_to_filename.csv web_dev writing_a_javascript_framework__execution_timing_beyond_settimeout.epub writing_a_javascript_framework__execution_timing_beyond_settimeout.md writing_a_javascript_framework__project_structuring.epub writing_a_javascript_framework__project_structuring.md hourly_rate);
 }
});

fees.forEach(function (fee) {
 total_fee += fee
});


var formatted_sum = '$' + total_fee.toFixed(2);
```

Tolerable, but noisy.

## Conclusion & Next Steps

In this tutorial, you've learned how `map`, `filter`, and `reduce` work; how to use them; and roughly how they're implemented. You've seen that they all allow you to avoid mutating state, which using `for` and `forEach` loops requires, and you should now have a good idea of how to chain them all together.

By now, I'm sure you're eager for practice and further reading. Here are my top three suggestions for where to head next:

JavaScript has become one of the de-facto languages of working on the web. It's not without its learning curves, and there are plenty of frameworks and libraries to keep you busy, as well. If you're looking for additional resources to study or to use in your work, check out what we have available in the [Envato marketplace](http://codecanyon.net/category/javascript).

If you want more on this sort of thing, [check my profile](https://tutsplus.com/authors/peleke-sengstacke) from time to time; catch me on Twitter ([@PelekeS](http://twitter.com/PelekeS)); or hit my blog at [http://peleke.me.](http://peleke.me)

Questions, comments, or confusions? Leave them below, and I'll do my best to get back to each one individually.
