# Adding an SVG Element

[Original URL](https://www.dashingd3js.com/adding-an-svg-element)

> Basic Example In the last example you added a p HTML element to the DOM. In this example, you will use D3.js to add an SVG element to a basic webpage. Per our previous SVG examples , you will add an...

## Basic Example

In the last example you added a **p** HTML element to the DOM. In this example, you will use D3.js to add an SVG element to a basic webpage. Per our previous [SVG examples](https://www.dashingd3js.com/basic-building-blocks) , you will add an SVG circle to the webpage.

Start with a basic HTML webpage:

```
 1<!DOCTYPE html>
 2<html>
 3 <head>
 4 <script type="text/javascript" src="d3.v2.min.js"></script>
 5 </head>
 6 <body>
 7 <p>Hello!</p>
 8 </body>
 9</html>
```

Recall that an SVG circle can be created as such:

```
1<svg width="50" height="50">
2 <circle cx="25" cy="25" r="25" fill="purple" />
3</svg>
```

Next open the Developer Tools (Webkit Inspector). Then type the following long line into the JavaScript Console:

```
1d3.select("body").append("svg").attr("width", 50).attr("height", 50).append("circle").attr("cx", 25).attr("cy", 25).attr("r", 25).style("fill", "purple");
```

This will give you the following:

![Added SVG element with D3.js](https://d1gg5jm9r4jrt6.cloudfront.net/added_svg_element_with_d3.js_673x521.png)

Congratulations - you've added an SVG element to the DOM using D3.js!

## D3.js Legibility

As you progress further into D3.js, the code that you write will go from a few lines to potentially a couple hundred lines. Even in our example:

```
1d3.select("body").append("svg").attr("width", 50).attr("height", 50).append("circle").attr("cx", 25).attr("cy", 25).attr("r", 25).style("fill", "purple");
```

It is hard to follow what is happening all the way through. Luckily, there is hope.

JavaScript, much like HTML, does not care about white spaces or new line breaks. Which means we can rewrite our code as follows in the JavaScript Console:

```
 1d3.select("body")
 2 .append("svg")
 3 .attr("width", 50)
 4 .attr("height", 50)
 5 .append("circle")
 6 .attr("cx", 25)
 7 .attr("cy", 25)
 8 .attr("r", 25)
 9 .style("fill", "purple");
```

Note: to get a new line in the JavaScript Console without having the statement evaluated, hold down the **shift** key while pressing **return**.

This will give you the following:

![Added SVG element with legible D3.js](https://d1gg5jm9r4jrt6.cloudfront.net/added_svg_element_with_legible_d3.js_673x525.png)

More legible code for the same functionality.

## D3.js Style Operator

The very last line of the JavaScript code is **.style("fill", "purple")**.

The Style Operator, if a _name_ and _value_ is specified, sets the CSS style property for the given selection with the given specified value.

The _selection_ given in our example was **everything that came before the statement**.

The _CSS style property_ given in our example was **"fill"**.

The _specified value_ given in our example was **"purple"**.

This powerful because we can apply any CSS style property to any type of selection.

## D3.js Chain Syntax

If we look at our code again, you will notice a **period** in front of every method and operator:

```
1d3.select("body").append("svg").attr("width", 50).attr("height", 50).append("circle").attr("cx", 25).attr("cy", 25).attr("r", 25).style("fill", "purple");
```

This syntax is called _chain syntax_.

If you have used the [jQuery JavaScript Library](http://jquery.com/)before , the _chain syntax_ should be familiar to you. The D3.js chain syntax works much the same way.

If you have not used jQuery before, let us see what is happening:

We start at the left and apply each _.method_ or _.operator_ to it.

So _.select_ is applied to _d3_

_.append_ is applied to _d3.select("body")_

etc...

The longer the chain the more things are being applied to that which is to the left of it.

The reason this works is because the operator method returns a selection.

Which means that not only does _d3.select("body")_ return a selection, so does _d3.select("body").append("svg")_!

Because we can apply an operator method to selections, we can continue to apply operators to operators that have returned selections.

Note - not all D3.js methods return a selection. However, most do so we can use chaining syntax to keep the code clean.

Looking at our code again:

```
1d3.select("body").append("svg").attr("width", 50).attr("height", 50).append("circle").attr("cx", 25).attr("cy", 25).attr("r", 25).style("fill", "purple");
```

You can see that we move from left to right. A selection is returned. An operator is applied, which returns a selection. Then an operator is applied to that selection... so on and so forth.

This is important to realize because it means **order matters**.

## Selections as JavaScript Variables

One of the great things about D3.js returning selections is that we can assign those selections to a JavaScript Variable. Thus we can go from this:

```
 1d3.select("body")
 2 .append("svg")
 3 .attr("width", 50)
 4 .attr("height", 50)
 5 .append("circle")
 6 .attr("cx", 25)
 7 .attr("cy", 25)
 8 .attr("r", 25)
 9 .style("fill", "purple");
```

To this:

```
 1var bodySelection = d3.select("body");
 2
 3var svgSelection = bodySelection.append("svg")
 4 .attr("width", 50)
 5 .attr("height", 50);
 6
 7var circleSelection = svgSelection.append("circle")
 8 .attr("cx", 25)
 9 .attr("cy", 25)
10 .attr("r", 25)
11 .style("fill", "purple");
```

Which when typed into the JavaScript Console gives us this screenshot:

![Added SVG element with legible D3.js using Variables](https://d1gg5jm9r4jrt6.cloudfront.net/added_svg_element_with_legible_d3.js_using_variables_650x568.png)

Which is the same as before. How you decide to write the D3.js code is up to you. Organizing the code by variables makes it easier to read and understand what the code does.
