# Using Data Bound to DOM Elements

[Original URL](https://www.dashingd3js.com/using-data-bound-to-dom-elements)

> Basic Example In this example, you will use D3.js to bind data to DOM elements of a basic webpage. Then you will use D3.js to use the data bound to DOM Elements to update the basic webpage. In the...

## Basic Example

In this example, you will use D3.js to bind data to DOM elements of a basic webpage. Then you will use D3.js to use the data bound to DOM Elements to update the basic webpage.

In the last section, we started with a basic webpage:

```
1<!DOCTYPE html>
2<html>
3 <head>
4 <script type="text/javascript" src="d3.v2.min.js"></script>
5 </head>
6 <body>
7 </body>
8</html>
```

And then bound data to paragraph HTML elements through the JavaScript Console:

```
1var theData = [ 1, 2, 3 ]
2
3var p = d3.select("body").selectAll("p")
4 .data(theData)
5 .enter()
6 .append("p")
7 .text("hello ");
```

Which gave us this:

![Binding Data to DOM Elements with D3.js Full Snapshot](https://www.dashingd3js.com/assets/binding_data_to_dom_elements_with_d3js_full_snapshot_625x697-f7bd431dce78572c16425935cb876e76.png)

Which is amazing - we have bound data to the DOM elements using D3.js.

_However,_ what we really want is to get our data back on to the basic webpage.

_Put another way,_ how do we get this value out of the DOM element?

![DOM Element Bound Data](https://www.dashingd3js.com/assets/dom_element_bound_data_288x254-785a750cde36ff4a10016eef8426ca74.png)

Open the Webkit Inspector to get the JavaScript Console going along side the Elements Inspector.

Type the following into the JavaScript Console:

```
1var theData = [ 1, 2, 3 ]
2
3var p = d3.select("body").selectAll("p")
4 .data(theData)
5 .enter()
6 .append("p")
7 .text( function (d) { return d; } );
```

Which gives us the following:

![Using Data bound to DOM Elements](https://www.dashingd3js.com/assets/using_data_bound_to_dom_elements_625x592-9a37d7836a278c4b54fd3c9aa066e4ce.png)

As you can see - instead of three paragraphs that say "Hello", we now have three paragraphs that contain our data! Congratulations - you have used data bound to DOM elements to update the webpage using D3.js!

## D3.js Text Operator Revisited

The only part of the JavaScript code that changed from our previous example to this example is that

became:

```
1.text( function (d) { return d; } );
```

The selection that the **.text()** operator is working on in both cases is the following:

```
1d3.select("body").selectAll("p")
2 .data(theData)
3 .enter()
4 .append("p")
```

This selection represents three HTML **<p>** elements.

As we covered in the previous section, the Text Operator sets the [textContent](http://www.w3.org/TR/DOM-Level-3-Core/core.html#Node3-textContent) of the node to the specified value on all selected elements.

_What is also true of the Text Operator_ is that if the value passed to it (the Text Operator) is a function, then the function is evaluated for each element in order. **And** the functions result is used to set each element's text context.

Which in our case means, that instead of setting "Hello" as the text value, the function goes through each element, gets the __data__ property and returns it to the Text Operator, so that it can set that result as the text content for the element.

## JavaScript Functions in D3.js Operators

We are passing a JavaScript function to the Text operator:

```
1function (d) { return d; }
```

If you are new to JavaScript functions, here is a brief synopsis:

```
1function functionName (variableName) {
2 return variableName;
3}
```

The "function" operator defines a function named "functionName" which takes a variable "variableName" and then returns the same variable.

If the functionName is missing, then it is called an _anonymous function_. Anonymous functions are used to make code more concise when declaring a function that will only be used in one place. In our case, the function is used only inside of the D3.js Text Operator:

```
1.text( function (d) { return d; } );
```

Note - our function doesn't have to be one line or just a simple return, we could so something like this:

```
1function (d) {
2 var addedNumber = 2;
3 var tempNumber;
4 
5 tempNumber = d + addedNumber;
6 return tempNumber;
7}
```

Which when put into the JavaScript Console as part of our overall D3.js functions, gives us the following:

![Using Functions inside D3.js Operators](https://www.dashingd3js.com/assets/using_functions_inside_d3.js_operators_625x655-e2469b143abe7053c6dad17073e68bd2.png)

As you can see, our function has added the number "2" to our data set and updated the text of the DOM elements.

Using these functions, you can write anything you want using JavaScript to manipulate the data. This is powerful in data visualizations when you will want differentiate style and format of SVG objects based on what values the data contains.

## Variables Available inside D3.js Operators

In our example:

```
1.text( function (d) { return d; } );
```

You can see that the variable _d_ is available for use in the anonymous function. This variable is provided to us by D3.js and it refers to the current ___data___ attribute for the specific element being processed.

The are two other variables provided to us by D3.js - _this_ and _i_.

**_this_** refers to the current DOM element being evaluated.

**_i_** refers to the index of the current HTML element being evaluated in the selected elements selection. Remember that all the data is processed at once and in sequential order. **Note** - _i_ starts at 0\. This means in our data set

```
1var theData = [ 1, 2, 3 ]
```

The data is processed in order of "1", "2" and then "3". With "1" having an _i_ of 0, "2" having an _i_ of 1 and "3" having an _i_ of 2.

You can run the following in the JavaScript Console to see this demonstrated:

```
 1var theData = [ 1, 2, 3 ]
 2
 3var p = d3.select("body").selectAll("p")
 4 .data(theData)
 5 .enter()
 6 .append("p")
 7 .text(function (d,i) {
 8 return "i = " + i + " d = "+d;
 9 });
```

Which gives us the following:

![Viewing index and datum variables in D3.js operator](https://www.dashingd3js.com/assets/viewing_index_and_datum_variables_in_d3.js_operator_625x620-14fc4868a3a9ac6aaa8f8633044a4f11.png)

Having the index available allows for things like alternating style and formatting.
