# Function Composition: Building Blocks for Maintainable Code

[Original URL](https://www.sitepoint.com/function-composition-building-blocks-for-maintainable-code/)

> This article was peer reviewed by Jeff Mott, Dan Prince and Sebastian Seitz. Thanks to all of SitePoint's peer reviewers for making SitePoint content the best it can be! One of the advantages...

_This article was peer reviewed by [Jeff Mott](https://www.sitepoint.com/author/jmott), [Dan Prince](https://www.sitepoint.com/author/dprince) and [Sebastian Seitz](https://www.sitepoint.com/community/users/m3g4p0p/activity). Thanks to all of SitePoint's peer reviewers for making SitePoint content the best it can be!_

![Image of a conductor in front of glowing screens of code](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/07/1470745998FunctionalComposition900-01-1024x569.jpg)

One of the advantages of thinking about JavaScript in a functional way is the ability to build complex functionality using small, easy to understand individual functions. But sometimes that involves looking at a problem backwards instead of forwards in order to figure out how to create the most elegant solution.

In this article I'm going to employ a step-by-step approach to examine functional composition in JavaScript and demonstrate how it can result in code which is easier to reason about and which has fewer bugs.

## Nesting Functions

Composition is a technique that allows you to take two or more simple functions, and combine them into a single, more complex function that performs each of the sub functions in a logical sequence on whatever data you pass in.

To get this result, you nest one function inside the other, and perform the operation of the outer function on the result of the inner function repeatedly until you produce a result. And the result can be different depending on the order in which the functions are applied.

This can be easily demonstrated using programming techniques we're already familiar with in JavaScript by passing a function call as an argument to another function:

```
function addOne(x) {
 return x + 1;
}
function timesTwo(x) {
 return x data.json data_ml developing_a_street_basketball_game_part_i_getting_workflow_ready_—_whitestormjs_framework.epub developing_a_street_basketball_game_part_i_getting_workflow_ready_—_whitestormjs_framework.md devops docker embedded from_zero_to_frontend_hero_part_1_—_free_code_camp.epub from_zero_to_frontend_hero_part_1_—_free_code_camp.md gaming gdb-example-ncurses.epub github go how_i_learned_to_stop_worrying_and_love_ephemeral_storage__silicon_valley_data_science.epub how_i_learned_to_stop_worrying_and_love_ephemeral_storage__silicon_valley_data_science.md how_to_check_diskspace_with_the_ncdu_utility.epub how_to_check_diskspace_with_the_ncdu_utility.md how_to_use_map_filter__reduce_in javascript.epub how_to_use_map_filter__reduce_in javascript.md html immutable_collections_for_javascript.epub immutable_collections_for_javascript.md javascript linux mobile node-hero-tutorials.epub parallel_processing_in_js.epub parallel_processing_in_js.md png programming research_paper_categorization_using_machine_learning_and_nlp.epub research_paper_categorization_using_machine_learning_and_nlp.md science social task_automation_with_npm_run.md tidy understanding_javascript_promises_pt_i_background__basics.epub understanding_javascript_promises_pt_i_background__basics.md understanding_object_streams.epub understanding_object_streams.md url_to_filename.csv web_dev writing_a_javascript_framework__execution_timing_beyond_settimeout.epub writing_a_javascript_framework__execution_timing_beyond_settimeout.md writing_a_javascript_framework__project_structuring.epub writing_a_javascript_framework__project_structuring.md 2;
}
console.log(addOne(timesTwo(3))); //7
console.log(timesTwo(addOne(3))); //8
```

In this case we defined a function `addOne()` to add one to a value, and a `timesTwo()` function that multiplies a value by two. By passing the result of one function in as the argument for the other function, we can see how nesting one of these inside the other can produce different results, even with the same initial value. The inner function is performed first, and then the result is passed to the outer function.

## Imperative Composition

If you wanted to perform the same sequence of operations repeatedly, it might be convenient to define a new function that automatically applied first one and then the other of the smaller functions. That might look something like this:

```
// ...previous function definitions from above
function addOneTimesTwo(x) {
 var holder = x;
 holder = addOne(holder);
 holder = timesTwo(holder);
 return holder;
}
console.log(addOneTimesTwo(3)); //8
console.log(addOneTimesTwo(4)); //10
```

What we've done in this case is manually compose these two functions together in a particular order. We created a new function that first assigns the value being passed to a holder variable, then updates the value of that variable by executing the first function, and then the second function, and finally returns the value of that holder.

(Note that we're using a variable called `holder` to hold the value we're passing in temporarily. With such a simple function, the extra local variable may seem redundant, but even in imperative JavaScript it's a good practice to treat the value of arguments passed into a function as if they were constants. Modifying them is possible locally, but it introduces confusion about what the value of the argument is when it's called at different stages within a function.)

Similarly, if we wanted to create another new function that applies these two smaller functions in the opposite order, we can do something like this:

```
// ...previous function definitions from above
function timesTwoAddOne(x) {
 var holder = x;
 holder = timesTwo(holder);
 holder = addOne(holder);
 return holder;
}
console.log(timesTwoAddOne(3)); //7
console.log(timesTwoAddOne(4)); //9
```

Of course, this code is starting to look pretty repetitive. Our two new composed functions are almost exactly the same, except for the order in which the two smaller functions they call are executed. We need to DRY that up (as in Don't Repeat Yourself). Also, using temporary variables that change their value like this isn't very functional, even if it is being hidden inside of the composed functions we're creating.

Bottom line: we can do better.

## Creating a Functional Compose

Let's craft a compose function that can take existing functions and compose them together in the order that we want. To do that in a consistent way without having to play with the internals every time, we have to decide on the order that we want to pass the functions in as arguments.

We have two choices. The arguments will each be functions, and they can either be executed left to right, or right to left. That is to say that using our proposed new function, `compose(timesTwo, addOne)` could either mean `timesTwo(addOne())` reading the arguments right to left, or `addOne(timesTwo())` reading the arguments left to right.

The advantage of executing the arguments left to right is that they will read the same way that English reads, much the way that we named our composed function `timesTwoAddOne()` in order to imply that the multiplication should happen before the addition. We all know the importance of logical naming to clean readable code.

The disadvantage of executing the arguments from left to right is that the the values to be operated upon would have to come first. But putting the values first makes it less convenient to compose the resulting function with other functions in the future. For a good explanation of the thinking behind this logic, you can't beat Brian Lonsdorf's classic video [Hey Underscore, You're Doing it Wrong](https://www.youtube.com/watch?v=m3svKOdZijA). (Although it should be noted that there is now [an fp option for Underscore](https://github.com/stoeffel/underscore.string.fp) that helps to address the functional programming issue Brian discusses when using Underscore in concert with a functional programming library such as [lodash-fp](https://github.com/lodash/lodash-fp) or [Ramda](http://ramdajs.com/).)

In any event, what we really want to do is pass in all of the configuration data first, and pass the value(s) to be operated on last. Because of this, it makes the most sense to define our compose function to read in its arguments and apply them from right to left.

So we can create a rudimentary `compose` function that looks something like this:

```
function compose(f1, f2) {
 return function(value) {
 return f1(f2(value));
 };
}
```

Using this very simple `compose` function, we can construct both of our previous complex functions much more simply, and see that the results are the same:

```
function addOne(x) {
 return x + 1;
}
function timesTwo(x) {
 return x data.json data_ml developing_a_street_basketball_game_part_i_getting_workflow_ready_—_whitestormjs_framework.epub developing_a_street_basketball_game_part_i_getting_workflow_ready_—_whitestormjs_framework.md devops docker embedded from_zero_to_frontend_hero_part_1_—_free_code_camp.epub from_zero_to_frontend_hero_part_1_—_free_code_camp.md gaming gdb-example-ncurses.epub github go how_i_learned_to_stop_worrying_and_love_ephemeral_storage__silicon_valley_data_science.epub how_i_learned_to_stop_worrying_and_love_ephemeral_storage__silicon_valley_data_science.md how_to_check_diskspace_with_the_ncdu_utility.epub how_to_check_diskspace_with_the_ncdu_utility.md how_to_use_map_filter__reduce_in javascript.epub how_to_use_map_filter__reduce_in javascript.md html immutable_collections_for_javascript.epub immutable_collections_for_javascript.md javascript linux mobile node-hero-tutorials.epub parallel_processing_in_js.epub parallel_processing_in_js.md png programming research_paper_categorization_using_machine_learning_and_nlp.epub research_paper_categorization_using_machine_learning_and_nlp.md science social task_automation_with_npm_run.md tidy understanding_javascript_promises_pt_i_background__basics.epub understanding_javascript_promises_pt_i_background__basics.md understanding_object_streams.epub understanding_object_streams.md url_to_filename.csv web_dev writing_a_javascript_framework__execution_timing_beyond_settimeout.epub writing_a_javascript_framework__execution_timing_beyond_settimeout.md writing_a_javascript_framework__project_structuring.epub writing_a_javascript_framework__project_structuring.md 2;
}
function compose(f1, f2) {
 return function(value) {
 return f1(f2(value));
 };
}
var addOneTimesTwo = compose(timesTwo, addOne);
console.log(addOneTimesTwo(3)); //8
console.log(addOneTimesTwo(4)); //10
var timesTwoAddOne = compose(addOne, timesTwo);
console.log(timesTwoAddOne(3)); //7
console.log(timesTwoAddOne(4)); //9
```

While this simple `compose` function works, it doesn't take into account a number of issues that limit its flexibility and applicability. For example, we might want to compose more than two functions. Also, we lose track of `this` along the way.

We could fix these problems, but it's not necessary in order to grasp how composition works. Rather than roll our own, it's probably more productive to inherit a more robust `compose` from one of the functional libraries out there, such as [Ramda](http://ramdajs.com/docs/#compose), which does account for right to left ordering of arguments by default.

## Types Are Your Responsibility

It's important to keep in mind that it is the responsibility of the programmer to know the type returned by each of the functions being composed, so it can be handled correctly by the next function. Unlike purely functional programming languages that do strict type checking, JavaScript won't prevent you from trying to compose functions that return values of inappropriate types.

You're not limited to passing numbers, and you're not even limited to maintaining the same type of variable from one function to the next. But you are responsible for making sure that the functions you are composing are prepared to deal with whatever value the previous function returns.

Always remember that someone else may need to use or modify your code in the future. Using composition inside traditional JavaScript code can appear complicated to programmers not familiar with functional paradigms. the goal is cleaner code that's easier to read and maintain.

But with the advent of ES2015 syntax, the creation of a simple composed function as a one-line call can even be done without a special `compose` method using arrow functions:

```
function addOne(x) {
 return x + 1;
}
function timesTwo(x) {
 return x data.json data_ml developing_a_street_basketball_game_part_i_getting_workflow_ready_—_whitestormjs_framework.epub developing_a_street_basketball_game_part_i_getting_workflow_ready_—_whitestormjs_framework.md devops docker embedded from_zero_to_frontend_hero_part_1_—_free_code_camp.epub from_zero_to_frontend_hero_part_1_—_free_code_camp.md gaming gdb-example-ncurses.epub github go how_i_learned_to_stop_worrying_and_love_ephemeral_storage__silicon_valley_data_science.epub how_i_learned_to_stop_worrying_and_love_ephemeral_storage__silicon_valley_data_science.md how_to_check_diskspace_with_the_ncdu_utility.epub how_to_check_diskspace_with_the_ncdu_utility.md how_to_use_map_filter__reduce_in javascript.epub how_to_use_map_filter__reduce_in javascript.md html immutable_collections_for_javascript.epub immutable_collections_for_javascript.md javascript linux mobile node-hero-tutorials.epub parallel_processing_in_js.epub parallel_processing_in_js.md png programming research_paper_categorization_using_machine_learning_and_nlp.epub research_paper_categorization_using_machine_learning_and_nlp.md science social task_automation_with_npm_run.md tidy understanding_javascript_promises_pt_i_background__basics.epub understanding_javascript_promises_pt_i_background__basics.md understanding_object_streams.epub understanding_object_streams.md url_to_filename.csv web_dev writing_a_javascript_framework__execution_timing_beyond_settimeout.epub writing_a_javascript_framework__execution_timing_beyond_settimeout.md writing_a_javascript_framework__project_structuring.epub writing_a_javascript_framework__project_structuring.md 2;
}
var addOneTimesTwo = x => timesTwo(addOne(x));
console.log(addOneTimesTwo(3)); //8
console.log(addOneTimesTwo(4)); //10
```

## Start Composing Today

As with all functional programming techniques, it's important to keep in mind that your composed functions should be pure. In a nutshell this means that every time a specific value is passed into a function, the function should return the same result, and the function should not produce side effects that alter values outside of itself.

Compositional nesting can be very convenient when you have a set of related functionality that you want to apply to your data, and you can break down the components of that functionality into reusable and easily composed functions.

As with all functional programming techniques, I recommend sprinkling composition judiciously into your existing code to get familiar with it. If you're doing it right, the result will be cleaner, dryer, and more readable code. And isn't that what we all want?
