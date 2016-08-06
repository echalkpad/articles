# A Comprehensive Look at jQuery Selectors

[Original URL](https://www.sitepoint.com/comprehensive-jquery-selectors/)

> This article was peer reviewed by Matt Smith and Tim Severien. Thanks to all of SitePoint's peer reviewers for making SitePoint content the best it can be! Whether you want to manipulate the...

_This article was peer reviewed by [Matt Smith](https://github.com/AllThingsSmitty) and [Tim Severien](https://www.sitepoint.com/author/tseverien/). Thanks to all of SitePoint's peer reviewers for making SitePoint content the best it can be!_

Whether you want to manipulate the content of an element on a web page, attach an event to it, or do something else, you will need to select it first. This is where jQuery selectors come into play: they form a crucial part of the library.

In this tutorial, I will cover all of these selectors and point out important things that you need to keep in mind while using them.

## jQuery Selectors

The main purpose of these selectors is to select elements on a web page that meet certain criteria. The criteria can be anything like their id, classname, attributes or a combination of any or all of these. Most of the selectors in jQuery are based on existing CSS selectors but the library also has its own custom selectors.

### Basic selectors

You can select elements on a webpage using their ID `$("#id")`, their class `$(".class")` or their tag name `$("li")` . You can also use a combination of these selectors like `$(".class tag")` or select combined result of multiple selectors like `$("selectorA, selectorB, selectorC")`.

jQuery also offers few other basic selectors that I have listed below:

- [:header](http://api.jquery.com/header-selector/) Selector -- Let's say you have to select all the headings like `<h1>`, `<h2>`, `<h3>` in a `<section>`. In this case, you could either use the verbose `$("section h1, section h2, section h3")` selector or the much shorter `$("section :header")` selector. Both will do the same job and the latter one is comparatively easier to read. The header selector has set the background of all headlines to yellow in [this demo](http://codepen.io/SitePoint/pen/xOJxJv).

- [:target](http://api.jquery.com/target-selector/) Selector -- This selector returns the element whose `id` matches the fragment identifier or hash of the document's URI. For instance, if the URI is "[https://sitepoint.com/\#hash"](https://sitepoint.com/\#hash”). Then, the selector `$("h2:target")` will select the element `<h2 id="hash">`.

- [:animated](http://api.jquery.com/animated-selector/) Selector -- This selector returns all elements that have an animation in progress when the selector is run. This implies that any element whose animation starts after the selector has been executed, won't be returned. Also, keep in mind that if you are using a custom jQuery build without the effects module, this selector will throw an error. In [this demo](http://codepen.io/SitePoint/pen/zBLYJX) only the animated box turns orange because of the selector.

### Index-based selectors

Besides the basic selectors we discussed above, you can also select elements on the basis of their index. jQuery provides its own set of index-based selectors which use zero-based indexing. This means that to select the third element you will have to use the index 2.

Here is a list of all index based selectors:

- [:eq(n)](http://api.jquery.com/eq-selector/) Selector -- This selector will return the element at index `n`. From version 1.8 onward, it accepts both positive and negative index value. When a negative value is supplied counting occurs backward from the last element.
- [:lt(n)](http://api.jquery.com/lt-selector/) Selector -- This selector will return all elements with index less than `n`. It also accepts both positive and negative values from version 1.8 onward. Just like the `:eq(n)` selector, when a negative value is provided counting occurs backward from the last element.
- [:gt(n)](http://api.jquery.com/gt-selector/) Selector -- This selector is just like `:lt(n)`. The only difference is that it returns all elements with an index greater than or equal to `n`.
- [:first](http://api.jquery.com/first-selector/) Selector -- This will return the first matched DOM element on a webpage. It is equivalent to `:eq(0)` and `:lt(1)`. One difference between `:first` and `:first-child` selector is that `:first-child` can select multiple elements each of which is first child of their parent.
- [:last](http://api.jquery.com/last-selector/) Selector -- This one is similar to the `:first` selector but returns the last child instead.
- [:even](http://api.jquery.com/even-selector/) Selector -- This will return all elements with an even index. Since indexing in jQuery is zero-based, the selectors selects first child, third child and so on. This seems counter-intuitive but that's how it works.
- [:odd](http://api.jquery.com/odd-selector/) Selector -- This one works like the `:even` selector but returns elements with odd indexes.

In the following example, you can click on the three buttons `:lt`, `:gt` and `:eq` and they will randomly generate an index and apply the resulting selector to a list:

See the Pen [Index-based Selectors](http://codepen.io/SitePoint/pen/VjBwEZ/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

As you can see, using `:first` and `:last` only selects the respective first and last elements on the webpage and not within every parent.

### Child selectors

jQuery enables you to select the children of an element based on their index or type. The CSS child selectors are different from jQuery ones in the sense that they don't use zero-based indexing.

Here is a list of all child selectors:

- [:first-child](http://api.jquery.com/first-child-selector/) -- This selector returns all elements which are the first child of their parent.

- [:first-of-type](http://api.jquery.com/first-of-type-selector/) -- This one selects all elements which are the first sibling of their own kind among many others.

- [:last-child](http://api.jquery.com/last-child-selector/) -- This will select the last child of a parent. Just like `:first-child`, it can select multiple elements in case of many parents.

- [:last-of-type](http://api.jquery.com/last-of-type-selector/) -- It selects all children that are the last of their type in a parent. In case of multiple parents, it can select multiple elements.

- [:nth-child()](http://api.jquery.com/nth-child-selector/) -- This one is a bit complex. It can accept a variety of values as parameter like a number greater than or equal to 1, the strings `even` and `odd` or an equation like `4n+1`.

- [:nth-last-child()](http://api.jquery.com/nth-last-child-selector/) -- This selector is similar to the previous one and accepts the same parameters. The only difference is that it begins its counting from the last child.

- [:nth-of-type()](http://api.jquery.com/nth-of-type-selector/) -- This selector returns all elements that are the nth child of their parent with respect to their siblings with same name.

- [:nth-last-of-type()](http://api.jquery.com/nth-last-of-type-selector/) -- This selector functions just like the `:nth-of-type()` selector but the counting begins from the end.

- [:only-child](http://api.jquery.com/only-child-selector/) -- As suggested by its name, this selector returns all elements that are the only child of their parent. If a parent has more than one child, nothing will be selected.

- [:only-of-type](http://api.jquery.com/only-of-type-selector/) -- This selector returns all elements that have no siblings of the same type.

This interactive demo will show you how all these selectors work. Just like the previous demo, all you need to do is click on the buttons:

See the Pen [Child Selectors](http://codepen.io/SitePoint/pen/PzBoym/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

You should note that this time the borders are added to `first` and `last` child in each `div` separately.

### Attribute selectors

You can also select elements based on the value of their attributes. When working with these selectors, you should keep one very important thing in mind: these selectors consider multiple space separated values as a single string. For example, `$("a[rel='nofollow']")` will not match `$("a[rel='nofollow other']")`.

Here is a list of all the attribute-based selectors:

- [$("[attribute|='value']")](http://api.jquery.com/attribute-contains-prefix-selector/) -- The "attribute contains prefix selector" returns all elements with attributes whose value is equal to or starts with the given string followed by a hyphen.

- [$("[attribute*='value']")](http://api.jquery.com/attribute-contains-selector/) -- This "attribute contains selector" returns all elements with attributes whose value contains the given substring. The location of the value doesn't matter. As long as it matches with the given value the selector will select the element.

- [$("[attribute~='value']")](http://api.jquery.com/attribute-contains-word-selector/) -- This selector returns all elements with attributes whose value contains a given word delimited by spaces.

- [\$("[attribute$='value']")](http://api.jquery.com/attribute-ends-with-selector/) -- This selector returns all elements with attributes whose value ends with the given string.

This demo shows how each of these selectors behave with different attribute values: <http://codepen.io/brathi/pen/aZovZN>

- [$("[attribute='value']")](http://api.jquery.com/attribute-equals-selector/) -- This selector returns all elements with attributes whose value is exactly equal to a given string.

- [("[attribute^='value']")](http://api.jquery.com/attribute-starts-with-selector/) -- This selector will return all elements with attributes whose value begins exactly with a given string.

- [( "[attribute!='value']" )](http://api.jquery.com/attribute-not-equal-selector/) -- This selector returns all elements that either don't have a given attribute or the value of the attribute is not equal to the one specified in the selector.

[This demo](http://codepen.io/SitePoint/pen/ZOjEqV) shows the last three selectors in action.

If you just want to check whether an element has a specified attribute and don't care about its value, you can use the `$("[attribute]")` selector.

### Content selectors

These selectors are based on the content inside the elements. There are four selectors in this category.

- [:contains(text)](http://api.jquery.com/contains-selector/) -- This selector returns all elements which have the specified text. The text can appear either directly inside the element or inside one of its descendants. While selecting elements using this selector, keep in mind that the text is case sensitive.

- [:has(selector)](http://api.jquery.com/has-selector/) -- This one returns all elements which have at least one element inside them that matches the specified selector. For instance, `$("section:has(h2)")` will select all sections that have an `h2` element. The `h2` does not need to be a direct child. It can exist anywhere among the descendants.

[This demo](http://codepen.io/SitePoint/pen/qNyBQB) illustrates how `:contains(text)` and `:has(selector)` work in different situations:

The demo clearly shows how the `:contains(text)` selector only cares for the case of text you provide. As long as the text and case match perfectly, the selector does not care what comes before or after the text.

- [:empty](http://api.jquery.com/empty-selector/) -- It returns all elements that don't have any children including text nodes.

- [:parent](http://api.jquery.com/parent-selector/) -- This selector returns all elements that have at least one child node. The child node can be either an element or a text node. In this sense, it is the inverse of `:empty` selector.

### Hierarchy selectors

These selectors use the DOM hierarchy as a criteria for selecting elements, which explains the name of category. Here is a list of all such selectors:

- [$("ancestor descendant")](http://api.jquery.com/descendant-selector/) -- This selector returns all the descendant elements of given parent. The descendant could be a child, grandchild and so on.

- [$("parent > child")](http://api.jquery.com/child-selector/) -- This selector is more specific than the previous one and only returns the direct children of a parent.

- [$("prev + next")](http://api.jquery.com/next-adjacent-selector/) -- This selector returns all elements matching the `next` selector, have the same parent as `prev` and are also immediately preceded by the sibling `prev`.

- [$("prev ~ siblings")](http://api.jquery.com/next-siblings-selector/) -- This one returns all siblings that come after the `prev` element, have the same parent as `prev` and match the `siblings` selector.

[This demo](http://codepen.io/SitePoint/pen/GqBRwA) shows all the hierarchy selectors in action.

As is evident from the demo, the next adjacent sibling and next sibling selectors don't have any effect on the siblings before them. That's the reason why second paragraph is just blue even though it is adjacent to an `<h2>` element.

jQuery provides a shorter version of selectors for most of the input elements in a form. These are categorised as [form selectors](http://api.jquery.com/category/selectors/form-selectors/).

For instance, to select a button element or elements with type button, you can use the shorter `$(":button")` selector instead of the longer `$("button, input[type='button']")` version. Similarly, to select all elements with type radio, you can use the `$(":radio")` selector instead of `$("[type=radio]")`.

[This demo](http://codepen.io/SitePoint/pen/NABWEX) illustrates how you can use all these selectors to count elements in a form.

You can also select all elements that are enabled or disabled using the `$(":enabled")` and `$(":disabled")` selectors respectively. You should only use these selectors on elements that support the disabled attribute, such as, `<button>`, `<input>` and `<textarea>` etc.

There is also a `$(":checked")` selector which will selects all elements that are either checked or selected. It applies to checkboxes, radio buttons and options of `select` elements. If you just want to retrieve the options of `select` elements, you should use `$(":selected")` instead.

The yellow border around various elements in the [this demo](http://codepen.io/SitePoint/pen/KrBKAZ) suggests that they were selected by our specific selectors.

### Visibility selectors

There are two more important selectors that you should know about: [:visible](http://api.jquery.com/visible-selector/), and [:hidden](http://api.jquery.com/hidden-selector/). As the names suggest, they return all elements that are visible or hidden, respectively.

In jQuery, an element is considered hidden in four cases:

- its `display` property is set to `none`,
- its `width` and `height` are set to zero,
- any of its ancestors is hidden, or
- it is a form element with `type="hidden"`.

The rule about height and width has changed in jQuery 3 and it will be discussed in next section.

Since elements with their opacity set to 0 or visibility set to hidden still take up space, they are considered visible. This means that `$("elem").css( "visibility", "hidden" ).is( ":hidden" )` will return `false`.

Any element with opacity zero will still be considered visible as evident from the demo below where the transparent `div` gets its orange color from the `:visible` selector :

See the Pen [Visibility Selectors](http://codepen.io/SitePoint/pen/AXgBmw/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

## Changes in jQuery 3

With the release of jQuery 3, a few things have changed in relation to selectors. Custom selectors like `:visible` and `:hidden` are now up to [17 times faster](https://github.com/jquery/sizzle/issues/315#issuecomment-74336936) in certain cases.

Additionally, from now on an element will be considered visible if it has a layout box returned from the DOM `getClientRects` method, regardless of whether the height or width of the element is zero.

The library will now also throw errors if a selector string only contains a hash symbol. In previous versions, `$("#")` returned an empty collection and `find("#")` threw an error, but from now on they will both be considered invalid.

## Caching Returned Elements

jQuery makes it really easy to select any element using complex selectors. For instance, to select all links with the class `externalLink`, you can just use `$("a .externalLink")`. However, selecting elements like this is a performance intensive task because jQuery needs to rescan the DOM for each such invocation.

When you have to use the same selector many times, it is much better to cache the returned element(s) in a variable. This will avoid multiple scans and improve performance.

As an example:

```
$("a .externalLink").click( function () {
 $("a .externalLink").removeClass("marked");
 $("a .externalLink").addClass("visited");
});
```

can be written as:

```
var $linkExternal = $("a .externalLink");
$linkExternal.click( function () {
 $linkExternal.removeClass("marked");
 $linkExternal.addClass("visited");
});
```

This way the browser only has to scan the document once rather than three times. Even though the performance gain in this case might be minimal, when you are using a large number of selectors the improvement will be significant.

By the way, the performance of `:visible` selector was also improved by [employing caching](https://github.com/jquery/sizzle/issues/315#issuecomment-74336936).

This tutorial covered almost all the jQuery selectors that you might use in a project. I have also tried to point out subtle differences that you should keep in mind when using these selectors. If you have any questions related to this article, please let me know in the comments!
