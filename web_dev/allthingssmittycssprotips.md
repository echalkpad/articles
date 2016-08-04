# AllThingsSmitty/css-protips

[Original URL](https://github.com/AllThingsSmitty/css-protips)

> A collection of tips to help take your CSS skills pro. Use :not() to Apply/Unapply Borders on Navigation Instead of putting on the border... /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old add bor

[![light bulb icon](https://camo.githubusercontent.com/9b290de6835cf807aaa81bb6a7cfdf3835636f8c/68747470733a2f2f7261776769742e636f6d2f416c6c5468696e6773536d697474792f6373732d70726f746970732f6d61737465722f6d656469612f6c6f676f2e737667)](https://camo.githubusercontent.com/9b290de6835cf807aaa81bb6a7cfdf3835636f8c/68747470733a2f2f7261776769742e636f6d2f416c6c5468696e6773536d697474792f6373732d70726f746970732f6d61737465722f6d656469612f6c6f676f2e737667)

A collection of tips to help take your CSS skills pro.

## [](https://github.com/AllThingsSmitty/css-protips#use-not-to-applyunapply-borders-on-navigation)Use `:not()` to Apply/Unapply Borders on Navigation

Instead of putting on the border...

```
/* add border */
.nav li {
 border-right: 1px solid #666;
}
```

...and then taking it off the last element...

```
/* remove border */
.nav li:last-child {
 border-right: none;
}
```

...use the `:not()` pseudo-class to only apply to the elements you want:

```
.nav li:not(:last-child) {
 border-right: 1px solid #666;
}
```

Sure, you can use `.nav li + li` or even `.nav li:first-child ~ li`, but with `:not()` the intent is very clear and the CSS selector defines the border the way a human would describe it.

## [](https://github.com/AllThingsSmitty/css-protips#add-line-height-to-body)Add Line-Height to `body`

You don't need to add `line-height` to each `<p>`, `<h*>`, _et al_. separately. Instead, add it to `body`:

This way textual elements can inherit from `body` easily.

## [](https://github.com/AllThingsSmitty/css-protips#vertically-center-anything)Vertically-Center Anything

No, it's not black magic, you really can center elements vertically:

```
html, body {
 height: 100%;
 margin: 0;
}

body {
 -webkit-align-items: center; 
 -ms-flex-align: center; 
 align-items: center;
 display: -webkit-flex;
 display: flex;
}
```

Want to center something else? Vertically, horizontally...anything, anytime, anywhere? CSS-Tricks has [a nice write-up](https://css-tricks.com/centering-css-complete-guide/) on doing all of that.

**Note:** Watch for some [buggy behavior](https://github.com/philipwalton/flexbugs#3-min-height-on-a-flex-container-wont-apply-to-its-flex-items) with flexbox in IE11.

## [](https://github.com/AllThingsSmitty/css-protips#comma-separated-lists)Comma-Separated Lists

Make list items look like a real, comma-separated list:

```
ul > li:not(:last-child)::after {
 content: ",";
}
```

Use the `:not()` pseudo-class so no comma is added to the last item.

## [](https://github.com/AllThingsSmitty/css-protips#select-items-using-negative-nth-child)Select Items Using Negative `nth-child`

Use negative `nth-child` in CSS to select items 1 through n.

```
li {
 display: none;
}

/* select items 1 through 3 and display them */
li:nth-child(-n+3) {
 display: block;
}
```

Or, since you've already learned a little about [using `:not()`](https://github.com/AllThingsSmitty/css-protips#use-not-to-applyunapply-borders-on-navigation), try:

```
/* select items 1 through 3 and display them */
li:not(:nth-child(-n+3)) {
 display: none;
}
```

Well that was pretty easy.

## [](https://github.com/AllThingsSmitty/css-protips#use-svg-for-icons)Use SVG for Icons

There's no reason not to use SVG for icons:

```
.logo {
 background: url("logo.svg");
}
```

SVG scales well for all resolution types and is supported in all browsers back to IE9\. So ditch your .png, .jpg, or .gif-jif-whatev files.

**Note:** If you have SVG icon-only buttons for sighted users and the SVG fails to load, this will help maintain accessibility:

```
.no-svg .icon-only:after {
 content: attr(aria-label);
}
```

## [](https://github.com/AllThingsSmitty/css-protips#use-the-lobotomized-owl-selector)Use the "Lobotomized Owl" Selector

It may have a strange name but using the universal selector (`*`) with the adjacent sibling selector (`+`) can provide a powerful CSS capability:

```
* + * {
 margin-top: 1.5em;
}
```

In this example, all elements in the flow of the document that precede other elements will receive `margin-top: 1.5em`.

For more on the "lobotomized owl" selector, read [Heydon Pickering's post](http://alistapart.com/article/axiomatic-css-and-lobotomized-owls) on _A List Apart_.

## [](https://github.com/AllThingsSmitty/css-protips#use-max-height-for-pure-css-sliders)Use `max-height` for Pure CSS Sliders

Implement CSS-only sliders using `max-height` with overflow hidden:

```
.slider ul {
 max-height: 0;
 overflow: hidden;
}

.slider:hover ul {
 max-height: 1000px;
 transition: .3s ease; /* animate to max-height */
}
```

## [](https://github.com/AllThingsSmitty/css-protips#inherit-box-sizing)Inherit `box-sizing`

Let `box-sizing` be inherited from `html`:

```
html {
 box-sizing: border-box;
}

*, *:before, *:after {
 box-sizing: inherit;
}
```

This makes it easier to change `box-sizing` in plugins or other components that leverage other behavior.

## [](https://github.com/AllThingsSmitty/css-protips#equal-width-table-cells)Equal Width Table Cells

Tables can be a pain to work with so try using `table-layout: fixed` to keep cells at equal width:

```
.calendar {
 table-layout: fixed;
}
```

Pain-free table layouts.

## [](https://github.com/AllThingsSmitty/css-protips#get-rid-of-margin-hacks-with-flexbox)Get Rid of Margin Hacks With Flexbox

When working with column gutters you can get rid of `nth-`, `first-`, and `last-child` hacks by using flexbox's `space-between` property:

```
.list {
 display: flex;
 justify-content: space-between;
}

.list .person {
 flex-basis: 23%;
}
```

Now column gutters always appear evenly-spaced.

## [](https://github.com/AllThingsSmitty/css-protips#use-attribute-selectors-with-empty-links)Use Attribute Selectors with Empty Links

Display links when the `<a>` element has no text value but the `href` attribute has a link:

```
a[href^="http"]:empty::before {
 content: attr(href);
}
```

That's pretty convenient.

## [](https://github.com/AllThingsSmitty/css-protips#style-default-links)Style "Default" Links

Add a style for "default" links:

```
a[href]:not([class]) {
 color: #008000;
 text-decoration: underline;
}
```

Now links that are inserted via a CMS, which don't usually have a `class` attribute, will have a distinction without generically affecting the cascade.

## [](https://github.com/AllThingsSmitty/css-protips#support)Support

These protips work in current versions of Chrome, Firefox, Safari, and Edge, and in IE11.
