# CSS Reset

[Original URL](http://yuilibrary.com/yui/docs/cssreset/)

> Note: The files "reset.css" and "reset-context.css" are deprecated, use "cssreset.css" and "cssreset-context.css" instead. The foundational CSS Reset removes the inconsistent styling of HTML elements...

***Note:** The files "reset.css" and "reset-context.css" are deprecated, use "cssreset.css" and "cssreset-context.css" instead.*

The foundational CSS Reset removes the inconsistent styling of HTML elements provided by browsers. This creates a dependably flat foundation to build upon. With CSS Reset loaded, write explicit CSS your project needs.

**Note:** [CSS Base](http://yuilibrary.com/yui/docs/cssbase/) can complement [CSS Reset](http://yuilibrary.com/yui/docs/cssreset/) by applying a style foundation for common HTML elements that is consistent for our [browser baseline](http://developer.yahoo.com/yui/articles/gbs).

## Getting Started

### Include Dependencies

To use CSS Reset, include the following source file in your web page:

```
<link rel="stylesheet" type="text/css" href="http://yui.yahooapis.com/3.10.0/build/cssreset/cssreset-min.css">
```

### Global vs. Contextual

YUI's CSS resources apply rules to HTML elements directly (using [type selectors](http://www.w3.org/TR/CSS21/selector.html#type-selectors)). An alternate version of the resource is available that target elements by context only. This contextual `-context.css` version selects HTML elements only when they descend from the `.yui3-cssreset` classname.

Here is how to include the contextual version of CSS Reset:

```
<link rel="stylesheet" type="text/css" href="http://yui.yahooapis.com/3.10.0/build/cssreset-context/cssreset-context-min.css">
```

## Using CSS Reset

### Using CSS Reset Globally

CSS Reset is easy to use. When CSS Reset is loaded it removes the inconsistent styling of HTML elements provided by browsers. Now, just begin writing the CSS rules your project needs without being burdened by what the browser _thinks_ you need.

Here is an [example page showing HTML elements](http://yuilibrary.com/yui/docs/cssreset/cssreset-basic.html) once CSS Reset has been included in the page globally.

### Using CSS Reset Contextually

If you're using the contextual version, CSS Reset's rules are only applied to nodes that descend from a node with a class value of `.yui3-cssreset`. In this example, CSS Reset applies to the `h1` in the left column, but does not impact the `h1` in the right column:

```
<div>
    <div id="left-column" class="yui3-cssreset"><h1>Lorem Ipsum</h1></div>
    <div id="right-column"><h1>Lorem Ipsum</h1></div>
</div>
```

Here is an [example page where only a portion of the page is impacted](http://yuilibrary.com/yui/docs/cssreset/cssreset-context.html) because it is being used contextually.
