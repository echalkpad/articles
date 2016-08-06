# Javascript anonymous functions

[Original URL](http://helephant.com/2008/08/23/javascript-anonymous-functions/)

> Anonymous functions are functions that are dynamically declared at runtime. They're called anonymous functions because they aren't given a name in the same way as normal functions....

Anonymous functions are functions that are dynamically declared at runtime. They're called anonymous functions because they aren't given a name in the same way as normal functions.

Anonymous functions are declared using the [function operator instead of the function declaration](http://helephant.com/2012/07/14/javascript-function-declaration-vs-expression/). You can use the function operator to create a new function wherever it's valid to put an expression. For example you could declare a new function as a parameter to a function call or to assign a property of another object.

Here's a typical example of a named function:

1. <span class="kw2">function</span>

   flyToTheMoon

  <span class="br0">(</span>

  <span class="br0">)</span>

2. 1. <span class="kw3">alert</span>

    <span class="br0">(</span>

    <span class="st0">“Zoom! Zoom! Zoom!”</span>

    <span class="br0">)</span>

    ;

3. 1. flyToTheMoon

    <span class="br0">(</span>

    <span class="br0">)</span>

    ;

Here's the same example created as an anonymous function:

1. <span class="kw2">var</span>

   flyToTheMoon = 

  <span class="kw2">function</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

2. 1. <span class="kw3">alert</span>

    <span class="br0">(</span>

    <span class="st0">“Zoom! Zoom! Zoom!”</span>

    <span class="br0">)</span>

    ;

3. 1. flyToTheMoon

    <span class="br0">(</span>

    <span class="br0">)</span>

    ;

## Anonymous functions are created using the function operator

The two most common ways to create a function in javascript are by using the [function declaration or function operator](http://helephant.com/2012/07/14/javascript-function-declaration-vs-expression/). Anonymous functions are created using the function operator.

If the function keyword appears first in the statement and is followed by a function name, the function is being created by a function declaration:<br>
![](http://helephant.com/wp-content/uploads/2008/08/function-declaration.png)

If the function keyword appears anywhere else, it is probably being used as a function operator:<br>
![](http://helephant.com/wp-content/uploads/2008/08/function-operator.png)

When the function operator is called, it creates a new function object and returns it. Here's an example that creates a function and assigns it to a variable called flyToTheMoon:

1. <span class="kw2">var</span>

   flyToTheMoon = 

  <span class="kw2">function</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

   

  <span class="br0">{</span>

2. <span class="kw3">alert</span>

  <span class="br0">(</span>

  <span class="st0">“Zoom! Zoom! Zoom!”</span>

  <span class="br0">)</span>

  ;

3.

The assignment works in exactly the same way you'd assign the return value of any function to a variable, the only special thing is that the value is a function object rather than something simple like a number or a date.

This is possible because functions in javascript are [just a special type of object](http://helephant.com/2008/08/19/functions-are-first-class-objects-in-javascript/). This means they can be used in the same way as any other object. They can be stored in variables, passed to other functions as parameters or returned from a function using the return statement. Functions are always objects, no matter how they are created.

Once the function has been saved to the variable, the variable can be used to invoke it:

## Anonymous functions are created at runtime

The function operator can be used anywhere that it's valid to use an expression. For example you can use the function operator when a variable is being assigned, when a parameter is being passed to a function or in a return statement. This is possible because the function operator is always invoked at runtime.

Function declarations are different. They are run before any of the other code is executed so the functions do not have to be declared before the code that calls them.

Function declarations can't be used to create anonymous functions because they require the function to have a name. The function declaration uses the function name to add it as a variable in the current scope.

## Anonymous functions don't have a name

This seems a little strange because how would you invoke a function that has no name? It works because the function name is something different to the variable that holds the function object.

Functions created with a function declaration always have a function name and a function variable that are exactly the same because the function declaration automatically creates the variable for you:

1. <span class="kw2">function</span>

   flyToTheMoon

  <span class="br0">(</span>

  <span class="br0">)</span>

   

  <span class="br0">{</span>

2. <span class="kw3">alert</span>

  <span class="br0">(</span>

  <span class="st0">“Zoom! Zoom! Zoom!”</span>

  <span class="br0">)</span>

  ;

3. 1. flyToTheMoon

    <span class="br0">(</span>

    <span class="br0">)</span>

    ;

For functions created with the function operator, the name is optional. In most cases, the name isn't important to us so we create an anonymous function that has no name like this one:

1. <span class="kw2">var</span>

   flyToTheMoon = 

  <span class="kw2">function</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

   

  <span class="br0">{</span>

2. <span class="kw3">alert</span>

  <span class="br0">(</span>

  <span class="st0">“Zoom! Zoom! Zoom”</span>

  <span class="br0">)</span>

  ;

3. 1. flyToTheMoon

    <span class="br0">(</span>

    <span class="br0">)</span>

    ;

However, the function operator does support setting a name if you want. Here is the same function again, only this time with a function name:

1. <span class="kw2">var</span>

   flyToTheMoon = 

  <span class="kw2">function</span>

   flyToTheMoon

  <span class="br0">(</span>

  <span class="br0">)</span>

   

  <span class="br0">{</span>

2. <span class="kw3">alert</span>

  <span class="br0">(</span>

  <span class="st0">“Zoom! Zoom! Zoom”</span>

  <span class="br0">)</span>

  ;

3. 1. flyToTheMoon

    <span class="br0">(</span>

    <span class="br0">)</span>

    ;

Giving your function a name does not automatically add a variable into scope with the function name. You still need to assign the return value of the function operator a variable.

In the previous example, the variable that stores the function object and the function's name are the same, but they don't have to be:

1. <span class="kw2">var</span>

   thingsToDoToday = 

  <span class="kw2">function</span>

   flyToTheMoon

  <span class="br0">(</span>

  <span class="br0">)</span>

   

  <span class="br0">{</span>

2. <span class="kw3">alert</span>

  <span class="br0">(</span>

  <span class="st0">“Zoom! Zoom! Zoom”</span>

  <span class="br0">)</span>

  ;

3. 1. thingsToDoToday

    <span class="br0">(</span>

    <span class="br0">)</span>

    ;

## Why have a name?

The function's name can be used to call the function from inside the function itself. That can be useful for recursive functions.

1. <span class="kw2">var</span>

   thingsToDoToday = 

  <span class="kw2">function</span>

   flyToTheMoon

  <span class="br0">(</span>

  <span class="br0">)</span>

   

  <span class="br0">{</span>

2. <span class="kw1">if</span>

  <span class="br0">(</span>

  <span class="sy0">!</span>

  onTheMoon

  <span class="br0">)</span>

3. flyToTheMoon

  <span class="br0">(</span>

  <span class="br0">)</span>

  ;

4. <span class="kw1">else</span>

5. <span class="kw3">alert</span>

  <span class="br0">(</span>

  <span class="st0">“One small step for a man..”</span>

  <span class="br0">)</span>

  ;

6. 1. thingsToDoToday

    <span class="br0">(</span>

    <span class="br0">)</span>

    ;

It can also useful for debugging because you can see the function's name in a call stack. Anonymous functions generally all look the same in the call stack. If you have a nasty debugging situation, sometimes giving names to the functions you are interested in can make things clearer.

## Why are anonymous functions useful?

Not having to set a name for an anonymous function is just a convenience thing since in most cases the name of the function doesn't really matter. Most of the time anonymous functions and named functions will both do any job perfectly well.

Functions created with the function operator can be very useful. [See some examples](http://helephant.com/2012/07/14/javascript-function-declaration-vs-expression/#function-operator-is-an-expression) of where it can be used.

## What's next?

Sometimes you want to create a lot of objects with the same properties and methods. [Constructor functions](http://helephant.com/2008/09/constructor-functions/) are you very own object factories that can stamp out as many objects with the same type as you need.

This article is part of a set of related posts about [How javascript objects work](http://helephant.com/2008/08/how-javascript-objects-work/).
