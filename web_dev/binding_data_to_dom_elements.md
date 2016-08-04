# Binding Data to DOM Elements

[Original URL](https://www.dashingd3js.com/binding-data-to-dom-elements)

> Basic Example In this example, you will use D3.js to bind data to DOM elements of a basic webpage. Start with a basic HTML webpage: 1<!DOCTYPE html> 2<html> 3 <head> 4 <script...

## Basic Example

In this example, you will use D3.js to bind data to DOM elements of a basic webpage.

Start with a basic HTML webpage:

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

Open the Webkit Inspector to get the JavaScript Console going along side the Elements Inspector.

Type the following into the JavaScript Console:

```
1var theData = [ 1, 2, 3 ]
2
3var p = d3.select("body").selectAll("p")
4 .data(theData)
5 .enter()
6 .append("p")
7 .text("hello ");
```

This will give you the following:

![Binding Data to DOM Elements with D3.js](https://www.dashingd3js.com/assets/binding_data_to_dom_elements_with_d3js_625x616-f0e1a8eef9b0469901018b17958a9448.png)

Congratulations - you have bound some data to some DOM elements using D3.js!

## D3.js SelectAll Method

The first part of the JavaScript code that we wrote which hasn't been covered before is **.selectAll("p")**

The D3.js SelectAll method uses CSS3 selectors to grab DOM elements. Unlike the Select method (where the first element is selected), the SelectAll method selects all the elements that match the specific selector string.

_But wait!_ The basic HTML page doesn't contain any **<p>** yet. What is it actually doing?

What it is doing is selecting all of the <p> available on the page. Which in this case is none. So it returns an empty selection.

Later use of .data(theData) and .enter( ) will allow us to bind the data to the empty selection.

## D3.js Data Operator

The next part of the JavaScript code that we wrote which hasn't been covered before is **.data(theData)**

The data operator joins an array of data (which can be numbers, objects or other arrays) with the current selection.

In this example, there is no key provided, so each element of **theData** array of data is assigned to each element of the current selection. The first element **1** is assigned to the first **<p>** element, the second to the second, so on and so forth.

_But wait!_ The basic page doesn't contain any **<p>** yet. What is it actually doing?

## D3.js Virtual Selections (Thinking with Joins)

The D3.js Data Operator returns _three_ virtual selections rather than just the regular one like other methods.

The three virtual selections are _enter_, _update_ and _exit_.

The **_enter_** selection contains placeholders for any missing elements.

The **_update_** selection contains existing elements, bound to data.

Any remaining elements end up in the **_exit_** selection for removal.

Since our selection from

```
1 d3.select("body").selectAll("p")
```

was empty.

The virtual **_enter_** selection now contains placeholders for our <p> elements.

We will come back to the power of the virtual selections enter, update, and exit in later sections. For now we will concentrate on the enter virtual selection.

To learn more, please visit the classic article by Mike Bostock ["Thinking with Joins"](http://bost.ocks.org/mike/join/).

## D3.js Enter Method

The D3.js Enter Method returns the **virtual enter selection** from the Data Operator. This method only works on the Data Operator because the Data Operator is the only one that returns three virtual selections.

In this case

```
1var p = d3.select("body").selectAll("p")
2 .data(theData)
3 .enter()
```

This will return a reference to the placeholder elements (nodes) for each data element that did not have a corresponding existing DOM Element.

Once we have this reference we can then operate on this selection.

However, it is important to note that this reference only allows chaining of **append**, **insert** and **select** operators to be used on it.

After these operators have been chained the the .enter() selection, we can treat the selection just like any other selection to modify the content.

## D3.js Append Operator Revisited

Looking at the code again:

```
1var p = d3.select("body").selectAll("p")
2 .data(theData)
3 .enter()
4 .append("p")
```

We **.append("p")** to the .enter() selection.

For each placeholder element created in the previous step, a **p** element is inserted.

Because we had three data points in our data array and no <p> elements on the webpage, the .append("p") creates and adds three HTML paragraph elements.

In the example, after the append operator has operated on the selection, it will return a selection of three HTML paragraph elements.

## D3.js Text Operator

If we wrote the code so that it was missing the last text operator as such:

```
1var theData = [ 1, 2, 3 ]
2
3var p = d3.select("body").selectAll("p")
4 .data(theData)
5 .enter()
6 .append("p");
```

This is what we would see:

![Binding Data to DOM Elements with D3.js without Text](https://www.dashingd3js.com/assets/binding_data_to_dom_elements_with_d3js_without_text_625x519-0f5c0255ad1da33ec765c3d5edb9792f.png)

Notice that none of the paragraphs contain any text versus the previous picture:

![Binding Data to DOM Elements with D3.js](https://www.dashingd3js.com/assets/binding_data_to_dom_elements_with_d3js_625x616-f0e1a8eef9b0469901018b17958a9448.png)

The Text Operator sets the [textContent](http://www.w3.org/TR/DOM-Level-3-Core/core.html#Node3-textContent) of the node to the specified value on all selected elements.

In this example, **.text("hello ")**, the value is _"hello "_. Since the selection was three <p> elements, each element gets a "hello " inserted into it.

## Where did the Data go?

The example starts with a JavaScript data array:

```
1var theData = [ 1, 2, 3 ]
```

and some how we end up with three paragraphs that say **Hello**.

What happened to our numbers 1, 2 and 3?

## D3.js Data Operator Revisited

Lets take a look at our Data Operator again with a simple example in the JavaScript Console:

```
1console.log(d3.select("body"));
```

When you hit return and click through the down arrows to see the properties of "body", you see something like this:

![JavaScript Console showing D3.js console log of Body Selection](https://www.dashingd3js.com/assets/d3.js_console_log_of_body_selection_650x147-8b501c52ad6721a12d4ba149f4d23746.png)

Beneath the line that reads **0: <body>**, you can see the properties of this HTML body element.

Now, run the following example in the JavaScript Console with the .data() operator added in:

```
1console.log(d3.select("body").data([1]));
```

Our data appears as a property named **__data__**:

![JavaScript Console showing D3.js console log of Body Selection With Data Property Added](https://www.dashingd3js.com/assets/d3.js_console_log_of_body_selection_with_data_650x133-822eda741daa407e54b13200b62bca71.png)

When data is assigned to an element, it is stored in the property __data__.

This makes the data "sticky" as the __data__ property is available on re-selection.

This is what we mean when we talk about **Binding Data to Dom Elements**.

## Basic Example Revisited

Going back to our basic example at the top of the page, we can now see where the data was bounded to by using the console.log( ):

```
 1var theData = [ 1, 2, 3 ]
 2
 3var p = d3.select("body").selectAll("p")
 4 .data(theData)
 5 .enter()
 6 .append("p")
 7 .text("hello ");
 8
 9console.log(p);
```

Gives us this:

![Binding Data to DOM Elements with D3.js Full Snapshot](https://www.dashingd3js.com/assets/binding_data_to_dom_elements_with_d3js_full_snapshot_625x697-f7bd431dce78572c16425935cb876e76.png)

From this you can see the three paragraph elements that were appended.

You can also see that the last (third) paragraph element has the __data__ property with a value of **3**. Which came from our data set!
