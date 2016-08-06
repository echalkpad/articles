# Functions are first class objects in javascript

[Original URL](http://helephant.com/2008/08/19/functions-are-first-class-objects-in-javascript/)

> Functions in javascript are first class objects. This means that javascript functions are just a special type of object that can do all the things that regular objects can do. Really, just like any...

Functions in javascript are [first class objects](http://en.wikipedia.org/wiki/First-class_function). This means that javascript functions are just a special type of object that can do all the things that regular objects can do.

## Really, just like any other variable

Here are a few of the important objects things that you can do with a function in javascript.

A function is an instance of the Object type:

1. <span class="kw2">function</span>

   feedCat

  <span class="br0">(</span>

  <span class="br0">)</span>

  <span class="br0">{</span>

2. <span class="kw3">alert</span>

  <span class="br0">(</span>

  <span class="st0">“Kibble, tinned food and water”</span>

  <span class="br0">)</span>

  ;

3. 1. <span class="kw3">alert</span>

    <span class="br0">(</span>

    feedCat 

    <span class="kw1">instanceof</span>

     Object

    <span class="br0">)</span>

    ;

A function can have properties and has a link back to its constructor method:

1. feedCat.

  <span class="me1">food</span>

   = 

  <span class="st0">“kibble”</span>

  ;

2. <span class="kw3">alert</span>

  <span class="br0">(</span>

  feedCat.

  <span class="me1">food</span>

  <span class="br0">)</span>

  ;

3. <span class="kw3">alert</span>

  <span class="br0">(</span>

  feedCat.

  <span class="me1">constructor</span>

  <span class="br0">)</span>

  ;

You can store the function in a variable:

1. <span class="kw2">function</span>

   feedCat

  <span class="br0">(</span>

  <span class="br0">)</span>

  <span class="br0">{</span>

2. <span class="kw3">alert</span>

  <span class="br0">(</span>

  <span class="st0">“Kibble, tinned food and water”</span>

  <span class="br0">)</span>

  ;

3. 1. <span class="kw2">var</span>

     eveningChore = feedCat;eveningChore

    <span class="br0">(</span>

    <span class="br0">)</span>

    ;

You can pass the function as a parameter to another function:

1. <span class="kw2">function</span>

   doEveningChores

  <span class="br0">(</span>

  chores

  <span class="br0">)</span>

  <span class="br0">{</span>

2. <span class="kw1">for</span>

  <span class="br0">(</span>

  <span class="kw2">var</span>

   x=

  <span class="nu0">0</span>

  ; x

  <span class="sy0">&lt;</span>

  chores .

  <span class="me1">length</span>

  ; x++

  <span class="br0">)</span>

3. chores

  <span class="br0">[</span>

  x

  <span class="br0">]</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

  ;

4. 1. doEveningChores

    <span class="br0">(</span>

    <span class="br0">[</span>

    feedCat

    <span class="br0">]</span>

    <span class="br0">)</span>

    ;

You can return the function from a function:

1. <span class="kw2">function</span>

   tonightChores

  <span class="br0">(</span>

  <span class="br0">)</span>

  <span class="br0">{</span>

2. <span class="kw1">return</span>

   feedCat;

3. 1. <span class="kw2">var</span>

     tonight = tonightChores

    <span class="br0">(</span>

    <span class="br0">)</span>

    ;

4. tonight

  <span class="br0">(</span>

  <span class="br0">)</span>

  ;

[Complete example](http://helephant.com/wp-content/uploads/2008/11/function.html).

## Can reduce repetitive code

Being able to pass logic around an application in the form of a function means it's possible to move a lot of repetitive code into a library function. It makes it easier to separate the unique pieces of logic from the generally useful logic.

For example, imagine you have a list of chocolate bars and you want to find all the ones that are made by Mars because you love M&Ms and you want to find out what other chocolatey goodness you could be enjoying (I have been on a diet for seven months.. I content myself with imagining chocolate..).

You could write a loop to iterate through the complete list and apply your item selection logic like this:

1. <span class="kw2">var</span>

   chocolateBars = 

  <span class="br0">[</span>

2. <span class="br0">{</span>

  <span class="kw3">name</span>

  : 

  <span class="st0">“Galaxy”</span>

  , manufacturer: 

  <span class="st0">“Mars”</span>

  <span class="br0">}</span>

  ,

3. ...

  <span class="br0">]</span>

  ;

4. <span class="kw2">var</span>

   marsChocolate = 

  <span class="br0">[</span>

  <span class="br0">]</span>

  ;

5. <span class="kw1">for</span>

  <span class="br0">(</span>

  <span class="kw2">var</span>

   x=

  <span class="nu0">0</span>

  ; x

  <span class="sy0">&lt;</span>

  chocolatebars .

  <span class="me1">length</span>

  ; x++

  <span class="br0">)</span>

  <span class="br0">{</span>

6. <span class="kw1">if</span>

  <span class="br0">(</span>

  chocolateBars

  <span class="br0">[</span>

  x

  <span class="br0">]</span>

  .

  <span class="me1">manufacturer</span>

   == 

  <span class="st0">“Mars”</span>

  <span class="br0">)</span>

7. marsChocolate.

  <span class="me1">push</span>

  <span class="br0">(</span>

  chocolateBars

  <span class="br0">[</span>

  x

  <span class="br0">]</span>

  <span class="br0">)</span>

  ;

8.

That's great! Problem solved. Only now it's Christmas time in your application and you have to sort the naughty list from the nice list. You're always so busy at this time of year! Plus you learnt in programmer school that [you shouldn't have to write the same thing twice](http://c2.com/cgi/wiki?DontRepeatYourself).

This sounds like the job for a library function! Let's split the thing that will be the same each time (walking the existing list, building the new list) from the thing that will be different each time (applying the filter). The walking the list and building the new list logic can go into a library function. The filtering logic can be passed in as a parameter:

1. <span class="kw2">var</span>

   array_helper = 

  <span class="br0">{</span>

2. filter: 

  <span class="kw2">function</span>

  <span class="br0">(</span>

  list, filter

  <span class="br0">)</span>

3. 1. <span class="kw2">var</span>

     matches = 

    <span class="br0">[</span>

    <span class="br0">]</span>

    ;

4. <span class="kw1">for</span>

  <span class="br0">(</span>

  <span class="kw2">var</span>

   x=

  <span class="nu0">0</span>

  ; x

  <span class="sy0">&lt;</span>

  list .

  <span class="me1">length</span>

  ; x++

  <span class="br0">)</span>

5. 1. <span class="kw1">if</span>

    <span class="br0">(</span>

    filter

    <span class="br0">(</span>

    list

    <span class="br0">[</span>

    x

    <span class="br0">]</span>

    <span class="br0">)</span>

    <span class="br0">)</span>

6. matches.

  <span class="me1">push</span>

  <span class="br0">(</span>

  list

  <span class="br0">[</span>

  x

  <span class="br0">]</span>

  <span class="br0">)</span>

  ;

7. 1. <span class="kw1">return</span>

     matches;

8. 1. 1. <span class="kw2">var</span>

       marsChocolate = array_helper.

      <span class="me1">filter</span>

      <span class="br0">(</span>

      chocolateBars,

9. <span class="kw2">function</span>

  <span class="br0">(</span>

  <span class="kw1">item</span>

  <span class="br0">)</span>

   

  <span class="br0">{</span>

   

  <span class="kw1">return</span>

   

  <span class="kw1">item</span>

  .

  <span class="me1">manufacturer</span>

   == 

  <span class="st0">“Mars”</span>

10. <span class="br0">}</span>

  <span class="br0">)</span>

  ;

11. <span class="kw2">var</span>

   naughtyList = array_helper.

  <span class="me1">filter</span>

  <span class="br0">(</span>

  childrenOfTheWorld,

12. <span class="kw2">function</span>

  <span class="br0">(</span>

  <span class="kw1">item</span>

  <span class="br0">)</span>

   

  <span class="br0">{</span>

   

  <span class="kw1">return</span>

   

  <span class="kw1">item</span>

  .

  <span class="me1">naughtiness</span>

   

  <span class="sy0">&amp;</span>

  gt; 

  <span class="nu0">50</span>

  ;

13. <span class="br0">}</span>

  <span class="br0">)</span>

  ;

14. <span class="kw2">var</span>

   niceList = array_helper.

  <span class="me1">filter</span>

  <span class="br0">(</span>

  childrenOfTheWorld,

15. <span class="kw2">function</span>

  <span class="br0">(</span>

  <span class="kw1">item</span>

  <span class="br0">)</span>

   

  <span class="br0">{</span>

   

  <span class="kw1">return</span>

   

  <span class="kw1">item</span>

  .

  <span class="me1">naughtiness</span>

   

  <span class="sy0">&amp;</span>

  lt;= 

  <span class="nu0">50</span>

  ;

16. <span class="br0">}</span>

  <span class="br0">)</span>

  ;

[Complete example](http://helephant.com/wp-content/uploads/2008/11/function2.html) (except for the naughty list – I wouldn't presume to take Santa's job).

Now the code that actually needs to be written for each list that needs filtering is really simple. If there's a problem in this logic it will be dead easy to spot. Any problems with the logic in the library function can be fixed in one place in the code. Plus you can get a list of delicious confectionery any time you want and the good children of the world will all get their presents at Christmas time.

The filter function is such a useful idea that it's a part of popular javascript libraries like [JQuery](http://docs.jquery.com/Traversing/filter#fn) and [Dojo](http://api.dojotoolkit.org/jsdoc/dojo/HEAD/dojo.filter). It will also be included in the browser as a standard part of [Javascript 1.6](http://developer.mozilla.org/en/docs/New_in_JavaScript_1.6#Array_extras). However the same idea can be applied in a lot of different places to reduce the amount of repeated code that's not really pulling its weight.

## Methods are properties that contain functions

Object methods are nothing special in javascript. They just are properties that happen to contain a function rather than something like a string or number value:

1. <span class="kw2">var</span>

   sabby = 

  <span class="br0">{</span>

2. <span class="kw3">name</span>

   : 

  <span class="st0">“Sabby”</span>

  ,

3. species: 

  <span class="st0">“cat”</span>

  ,

4. hello : 

  <span class="kw2">function</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

   

  <span class="br0">{</span>

   

  <span class="kw3">alert</span>

  <span class="br0">(</span>

  <span class="st0">“hissss”</span>

  <span class="br0">)</span>

  ; 

  <span class="br0">}</span>

5.

This was pretty clever of the javascript language designers because it meant that they didn't need to do anything special to support object methods. This is part of the reason why [javascript can have objects without having classes](http://helephant.com/2008/08/building-simple-objects/).

## Makes javascript flexible and dynamic

A lot of javascript's flexibility comes from being able to treat functions as first class objects. Pretty much every example in the [javascript objects articles](http://helephant.com/2008/08/how-javascript-objects-work/) takes advantage of being able to treat a function just like a regular object.

All of the basic techniques for using objects in javascript rely on this one thing. Simple objects, constructor functions and prototypes all involve assigning methods to the properties of an object.

It also helps javascript to be flexible and lightweight. Useful ideas from other languages like namespacing, custom events, static methods and extension methods can all be simulated because it's possible to create a function and put it wherever you need.

## Further reading

The best reference I could find about this was an article about [functional javascript](http://www.hunlock.com/blogs/Functional_Javascript). It explains a heap of the [functional language](http://en.wikipedia.org/wiki/Functional_programming) features that javascript has.

Raganwald has an [interesting article](http://weblog.raganwald.com/2007/01/closures-and-higher-order-functions.html) with some more general information about why techniques like this are so useful. It compares ruby and java but the logic still applies to javascript.

## What's next?

[Anonymous functions](http://helephant.com/2008/08/javascript-anonymous-functions/) are functions that are dynamically created at runtime using the function operator. Anonymous functions go hand in hand with functions being first class objects because these are two of the big things that javascript such a flexible and dynamic language.

This article is part of a set of related posts about [How javascript objects work](http://helephant.com/2008/08/how-javascript-objects-work/).
