# A Dive Into Plain JavaScript

[Original URL](http://blog.adtile.me/2014/01/16/a-dive-into-plain-javascript/)

> While I've worked over a decade building various websites, it has only been the past 3 years that I've started learning more on how to work with plain JavaScript, instead of using...

While I've worked over a decade building various websites, it has only been the past 3 years that I've started learning more on how to work with plain JavaScript, instead of using jQuery always as the starting point. The fact that I'm learning a dozen new things every day now, has made working on [Adtile's JavaScript SDK](http://www.adtile.me/) feel more like building an open source project than "actual work," and I have to say I like that a lot.

Today, I'm going to share some of the basic things I've learned during the past years, which will hopefully also help you to dive into the world of plain JavaScript, making it easier to decide whether or not you will need jQuery in your next project.

## Progressive Enhancement

While libraries like [jQuery](http://jquery.com) help to forget most of the browser inconsistencies, you really become familiar with them once you start using plain JavaScript for everything. To avoid writing JavaScript that's full of browser hacks and code which only solves browser compatibility issues, I recommend building a progressively enhanced experience using feature detection to only target the more modern browsers. This doesn't mean that browsers like IE7 don't see anything at all, it just means that they get a more basic experience without JavaScript enhancements.

### Here's How We're Doing It

We have a separate JavaScript partial which has all the feature tests. The actual list of tests is often much longer, but let's get back to this a bit later. To rule out some of the older browsers we use these two tests:

```
var test = {
 addEventListener : !!window.addEventListener,
 querySelectorAll : !!document.querySelectorAll,
};
```

Then, in the main application partial, we detect if these features are supported by using this simple `"if"` statement below. If they aren't supported, the browser won't execute any of this code:

```
if (test.addEventListener && test.querySelectorAll) {
 this.init();
}
```

These two tests make sure that we have a native way of using CSS selectors in our JavaScript `(querySelectorAll)`, a way to easily add and remove events `(addEventListener)` and also that the browser's standards support is better than what IE8 has. Read more about this technique called "Cutting the mustard" from [BBC's blog](http://responsivenews.co.uk/post/18948466399/cutting-the-mustard).

### Browser Support

Here's a rough list of the browsers which support the features we are testing, and will hence keep executing the JavaScript:

- IE9+
- Firefox 3.5+
- Opera 9+
- Safari 4+
- Chrome 1+
- iPhone and iPad iOS1+
- Android phone and tablets 2.1+
- Blackberry OS6+
- Windows 7.5+
- Mobile Firefox
- Opera Mobile

## The Basics, Plain JavaSript Way

Let's start looking how the most basic and often needed functionalities work in plain JavaScript, compared to jQuery. For each example, I'm going to provide both the jQuery and plain JavaScript approach.

### Document Ready

With jQuery, many of you are probably used to using document.ready like so:

```
$(document).ready(function() {
 // Code
});
```

But did you know that you can just put all of your JavaScript at the bottom of your page and that does basically the same thing? JavaScript has also an event listener for the DOM content loaded event which you can use instead of jQuery's document.ready:

```
document.addEventListener("DOMContentLoaded", function() {
 // Code
}, false);
```

### Selectors API

JavaScript's native selectors API is very good. It works with CSS selectors and is very similar to what jQuery provides. If you are used to writing this in jQuery:

```
var element = $("div");
```

You can now replace that with:

```
var element = document.querySelector("div");
```

Or, to select all div's inside some container:

```
var elements = document.querySelectorAll(".container div");
```

You can also query against a specific element to find it's children:

```
var navigation = document.querySelector("nav");
var links = navigation.querySelectorAll("a");
```

Quite straightforward, easy to understand, and doesn't really require much more writing now does it? To go a little further, we could even build a tiny JavaScript library for ourselves for simple DOM querying. Here's something that Andrew Lunny [has came up with](http://remysharp.com/2013/04/19/i-know-jquery-now-what/#backToTheFutureToday-heading):

```
// This gives us simple dollar function and event binding
var $ = document.querySelectorAll.bind(document);
Element.prototype.on = Element.prototype.addEventListener;

// This is how you use it
$(".element")[0].on("touchstart", handleTouch, false);
```

### Traversing the DOM

Traversing the DOM with plain JavaScript is a bit harder than it is with jQuery. But not too hard. Here are some simple examples:

```
// Getting the parent node
var parent = document.querySelector("div").parentNode;

// Getting the next node
var next = document.querySelector("div").nextSibling;

// Getting the previous node
var next = document.querySelector("div").previousSibling;

// Getting the first child element
var child = document.querySelector("div").children[0];

// Getting the last child
var last = document.querySelector("div").lastElementChild;
```

### Adding and Removing Classes

With jQuery, adding, removing and checking if an element has certain classes is really simple. It's a bit more complex with plain JavaScript, but not too much so. Giving element a class called `"foo"` and replacing all the current classes:

```
// Select an element
var element = document.querySelector(".some-class");

// Give class "foo" to the element
element.className = "foo";
```

Adding a class without replacing the current classes:

```
element.className += " foo";
```

Removing `"no-js"` class from html-element and replacing it with `"js"`:

```
<html class="no-js">
<head>
 <script>
 document.documentElement.className = "js";
 </script>
……
```

That was quite straightforward, right? Next step, removing only certain classes, is a bit more complex. I've been using this small helper function in a separate partial called util.js. It takes 2 parameters: element and the class you want to remove:

```
// removeClass, takes two params: element and classname
function removeClass(el, cls) {
 var reg = new RegExp("(\\s|^)" + cls + "(\\s|$)");
 el.className = el.className.replace(reg, " ").replace(/(^\s*)|(\s*$)/g,"");
}
```

Then, in the main application partial, I've been using it like so:

```
removeClass(element, "foo");
```

If you also want to check an element against some class, kind of like jQuery's `hasClass` works, you could add this in your utils:

```
// hasClass, takes two params: element and classname
function hasClass(el, cls) {
 return el.className && new RegExp("(\\s|^)" + cls + "(\\s|$)").test(el.className);
}
```

...and use it like so:

```
// Check if an element has class "foo"
if (hasClass(element, "foo")) {

 // Show an alert message if it does
 alert("Element has the class!");
}
```

### Introducing HTML5 classList API

If you only need to support more modern browsers like IE10+, Chrome, Firefox, Opera and Safari, you could also start using HTML5's classList functionality which makes adding and removing classes even easier.

This is something, that I ended up doing with our latest Developer Docs, as the functionality I was developing, was more like an enhancement to the UI and not really something that would break the experience if it wasn't present.

You can feature detect if the browser supports it by using this simple `"if"` statement:

```
if ("classList" in document.documentElement) {
 // classList is supported, now do something with it
}
```

Adding, removing and toggling classes with classList:

```
// Adding a class
element.classList.add("bar");

// Removing a class
element.classList.remove("foo");

// Checking if has a class
element.classList.contains("foo");

// Toggle a class
element.classList.toggle("active");
```

One other benefit of using classList is that it will perform much better than using the raw className property. If you had an element like this:

```
<div id="test" class="one two three"></div>
```

Which you'd want to manipulate:

```
var element = document.querySelector("#test");
addClass(element, "two");
removeClass(element, "four");
```

For each of these methods the className property would be read from and then written to, triggering a browser repaint. However, this is not the case if we use the relevant classList methods instead:

```
var element = document.querySelector("#test");
element.classList.add("two");
element.classList.remove("four");
```

With classList the underlying className is only altered when necessary. Given that we are adding a class that is already present and removing a class that isn't, the className is never touched, meaning we've just avoided two repaints!

### Event Listeners

Adding and removing event listeners from elements is almost as simple in plain JavaScript as it's in jQuery. Things get a bit more complex when you have to add multiple event listeners, but I'll explain that in a bit. The simplest example, which will just pop out an alert message when an element is clicked, is as follows:

```
element.addEventListener("click", function() {
 alert("You clicked");
}, false);
```

To achieve this same functionality on all of the elements on a given page, we have to loop through each element, and give them all eventListeners:

```
// Select all links
var links = document.querySelectorAll("a");

// For each link element
[].forEach.call(links, function(el) {

 // Add event listener
 el.addEventListener("click", function(event) {
 event.preventDefault();
 alert("You clicked");
 }, false);

});
```

One of JavaScript's greatest features related to event listeners is the fact that `"addEventListener"` can take an object as a second argument that will automatically look for a method called `"handleEvent"` and call it. [Ryan Seddon](https://twitter.com/ryanseddon) has covered this technique thoroughly [in his article](http://www.thecssninja.com/javascript/handleevent), so I'm just gonna give a fast example and you can read more about it from his blog:

```
var object = {
 init: function() {
 button.addEventListener("click", this, false);
 button.addEventListener("touchstart", this, false);
 },
 handleEvent: function(e) {
 switch(e.type) {
 case "click":
 this.action();
 break;
 case "touchstart":
 this.action();
 break;
 }
 },
 action: function() {
 alert("Clicked or touched!");
 }
};

// Init
object.init();
```

### Manipulating the DOM

Manipulating the DOM with plain JavaScript might sound like a horrible idea at first, but it really isn't much more complex that using jQuery. Below, we have an example that selects an element from the DOM, clones it, manipulates the clone's styles with JavaScript and then replaces the original element with the manipulated one.

```
// Select an element
var element = document.querySelector(".class");

// Clone it
var clone = element.cloneNode(true);

// Do some manipulation off the DOM
clone.style.background = "#000";

// Replaces the original element with the new cloned one
element.parentNode.replaceChild(clone, element);
```

If you don't want to replace anything in the DOM, but instead append the newly created div inside the `<body>`, you could do it like this:

```
document.body.appendChild(clone);
```

If you feel like you'd want to read even more about different DOM methods, I'd suggest you to head over to Peter-Paul Koch's [DOM Core tables](http://quirksmode.org/dom/core/).

## Diving Deeper

I'm going to share two bit more advanced techniques here, which I've recently discovered. These are both functionalities we have needed while building [Adtile](http://www.adtile.me/), so you might find these useful too.

### Determining Max-Width of Responsive Images in JS

This is one of my own favorites, and it's very useful if you ever need to manipulate fluid images with JavaScript. As browsers return the current **resized** dimensions of an image by default, we have to come up with some other solution. Luckily, modern browsers now have a way of doing this:

```
var maxWidth = img.naturalWidth;
```

This will give us image's `max-width: 100%` value in pixels and it's supported in IE9, Chrome, Firefox, Safari and Opera. We can also take this further and add support for older browsers by loading the image into an in-memory object:

```
// Get image's max-width:100%; in pixels
function getMaxWidth(img) {
 var maxWidth;

 // Check if naturalWidth is supported
 if (img.naturalWidth !== undefined) {
 maxWidth = img.naturalWidth;

 // Not supported, use in-memory solution as fallback
 } else {
 var image = new Image();
 image.src = img.src;
 maxWidth = image.width;
 }

 // Return the max-width
 return maxWidth;
}
```

You should note that the images must be fully loaded before checking for the width. This is what we've been using to make sure they have dimensions:

```
function hasDimensions(img) {
 return !!((img.complete && typeof img.naturalWidth !== "undefined") || img.width);
}
```

### Determining if an Element is in the Viewport

You can get the position of any element on the page using [getBoundingClientRect](https://developer.mozilla.org/en-US/docs/Web/API/Element.getBoundingClientRect) method. Below is a simple function showing how simple and powerful it can be. This function takes one parameter, which is the element you want to check. It will return true when the element is visible:

```
// Determine if an element is in the visible viewport
function isInViewport(element) {
 var rect = element.getBoundingClientRect();
 var html = document.documentElement;
 return (
 rect.top >= 0 &&
 rect.left >= 0 &&
 rect.bottom <= (window.innerHeight || html.clientHeight) &&
 rect.right <= (window.innerWidth || html.clientWidth)
 );
}
```

The above function could be used by adding a "scroll" event listener to the window and then calling `isInViewport()`.

## Conclusion

Whether or not you should use [jQuery](http://jquery.com/) in your next project depends a lot on what you are building. If it's something that requires large amounts of front-end code, you should probably use it. However, if you're building a JavaScript plugin or a library, you might want to consider sticking with just plain JavaScript. Using plain JavaScript means fewer requests and less data to load. It also means that you aren't forcing developers to add jQuery to their project just because of that dependency.
