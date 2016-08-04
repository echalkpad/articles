# Systematic Web Design

[Original URL](http://www.systematicwebdesign.com/)

> Systematic Web Design is a methodology for applying CSS to HTML in a structured, systematic way. It makes it easier to prototype, iterate and scale a web design. This methodology works for any...

Systematic Web Design is a methodology for applying CSS to HTML in a structured, systematic way. It makes it easier to prototype, iterate and scale a web design.

This methodology works for any website or web app, but it's particularly helpful in big, complex, long-running projects with lots of collaborators. And it works with both standard CSS and extended CSS languages such as Sass and Less.

Systematic Web Design shares many of the principles of other CSS methodologies including Nicole Sullivan's Object-Oriented CSS ([OOCSS](http://github.com/stubbornella/oocss)), Yandex's Block Element Modifier ([BEM](http://bem.info/)), and Jonathan Snook's Scalable and Modular Architecture for CSS ([SMACSS](http://smacss.com/)).

[![](http://public.systematicwebdesign.com/images/systematic-web-design-20150901.png)](http://www.kieranpotts.com/books/systematic-web-design.pdf)

## Free e-book (PDF)

- Advanced techniques
- Best practices
- More examples

[Download](http://www.kieranpotts.com/books/systematic-web-design.pdf)

There are four stages to creating a web design using the Systematic methodology:

1. A dedicated style sheet prepares the default presentation of individual HTML **[elements](http://www.systematicwebdesign.com/#elements)**.
2. A wireframe **[layout](http://www.systematicwebdesign.com/#layout)** is designed independently of the content that it will accommodate.
3. HTML elements are combined to create custom UI **[widgets](http://www.systematicwebdesign.com/#widgets)**, such as navigation bars and modal popups.
4. **[Modifier](http://www.systematicwebdesign.com/#modifiers)** classes bring variety to the presentation of layout sections, elements and widgets.

## Elements

First, set the default presentation for individual HTML elements that will encapsulate content and render interactive controls. Examples include headings, paragraphs, lists, tables, forms, fieldsets, legends, inputs and buttons.

Use naked type selectors to set the default presentation for each element. At this stage do not introduce variations by qualifying the type selectors with classes (we'll do that later when we make our [widgets](http://www.systematicwebdesign.com/#widgets) and [modifiers](http://www.systematicwebdesign.com/#modifiers).)

```
h2 {
 font-size: 2rem;
 line-height: 1.2;
 margin-top: 2em;
 margin-bottom: 1em;
}

h3 {
 font-size: 1.4rem;
 line-height: 1.2;
 margin-top: 2em;
 margin-bottom: 1em;
}
```

For efficiency, some inheritable typographic styles may be set on the `<body>` element and allowed to cascade from there.

```
body {
 color: hsl(0, 0%, 27%);
 font-family: Arial, Helvetica, sans-serif;
 font-size: 62.5%;
}
```

Pseudo-classes, pseudo-elements and attribute selectors are useful, too.

```
a:hover { opacity: 1; }
q::before { content: ' «'; }
button[disabled] { opacity: 0.5; }
```

But descendant selectors, child selectors and sibling selectors should be used more sparingly. As a general rule, the default presentation of HTML elements should not be dependant upon the context in which they are used within the wider document.

```
li a {}
li > a {}
h2 + h3 {}
h2 ~ p {}
```

There should certainly never be any rulesets that modify the presentation of content based on where it is located within the layout. Keep these two concerns – layout and content – separate. Never do this:

```
.MAIN h2 {}
.SIDEBAR h2 {}
```

## Layout

Next, position a series of boxes to create a layout.

Give each section of the layout a unique class name, written in full upper case with words delimited by underscores. The layout starts out empty – we'll add content later.

```
<body>
 <div class="CONTAINER">
 <header class="BANNER"></header>
 <nav class="NAVIGATION_PRIMARY"></nav>
 <nav class="NAVIGATION_SECONDARY"></nav>
 <main class="MAIN"></main>
 <aside class="ADVERTS"></aside>
 <footer class="FOOTER">
 <nav class="SITEMAP"></nav>
 <div class="LEGAL"></div>
 </footer>
 </div>
</body>
```

Here are the CSS selectors you'd need for this layout.

```
.CONTAINER {}
.BANNER {}
.NAVIGATION_PRIMARY {}
.NAVIGATION_SECONDARY {}
.MAIN {}
.ADVERTS {}
.FOOTER {}
.SIDEBAR {}
.LEGAL {}
```

Do not qualify the class selectors with element types, as that will give you the freedom to swap around sectioning elements in the markup without needing to refactor your style sheets, too. For example, you could easily switch an `<aside>` for a generic `<div>`, so changing the semantics of the document.

Once a default layout is in place, you can start to introduce variations for some pages. To do this, give the root `<html>` element a unique class name, using the same naming convention.

```
<html class="SEARCH">
```

Now you can use this root-level class as a namespace to modify and extend the default layout.

```
html.SEARCH .BANNER {}
html.SEARCH .SIDEBAR {}
html.SEARCH .PAGINATION {}
```

Layout should be kept separate from content. We want to have the freedom to move content from one section of the layout to another without needing to change any CSS. So layout classes must not set any properties that will cascade down and affect nested content and interactive controls. Only non-inheritable properties should be applied to layout sections, such as: `display`, `float`, `position`, `top`, `bottom`, `left`, `right`, `width`, `height`, `background`, `border`, `margin`, `padding` and `z-index`.

HTML's extensive suite of native element types still does not provide us with a large enough vocabulary from which we can design rich, modern web interfaces. We often need to group together multiple HTML elements to compose custom UI components. We might for example combine an `<input>` control with a `<button>` to create a search box, or repurpose the `<ul>` element to make a navigation menu.

We could just place these things directly inside the sections of the layout where we want them to go.

```
<nav class="NAVIGATION">
 <ul>
 <li><a href="./">Home</a></li>
 <li><a href="about.html">About</a></li>
 <li><a href="learn/">Learn</a></li>
 <li><a href="extend/">Extend</a></li>
 <li><a href="share/">Share</a></li>
 </ul>
 <form action="search.html" method="get">
 <label for="input-search">Search</label>
 <input name="q" type="search" id="input-search" />
 <button type="submit">Search</button>
 </form>
</nav>
```

We'd then modify the default presentation of each element by using the class of the parent section as a namespace to encapsulate the changes.

```
.NAVIGATION ul {}
.NAVIGATION li {}
.NAVIGATION a {}
.NAVIGATION form {}
.NAVIGATION label {}
.NAVIGATION input {}
.NAVIGATION button {}
```

The big disadvantage of this approach is that the presentation of content – in this case, a navigation menu and search box – is tightly coupled to its location in a particular part of the layout. This is inflexible. We can't reuse stuff or move things around in the HTML without triggering wider refactoring of our style sheets, too.

A better approach is to identify recurring patterns of content in our designs and to bundle them up into independent modules of markup and styling (and optionally scripting).

We need a name for these custom UI components. Let's call them "widgets".

A widget could be something simple like a search box or a navigation menu. Or it could be something more complex and dynamic like a modal popup, a slideshow or a real-time user comment feed.

Some widgets may be used on every page of a website. Others may be rolled out only occasionally.

Creating widgets is easy. Simply encapsulate the markup for each widget in a unique class. Use the camel case naming convention for widget classes, to distinguish them from layout classes: `"WidgetName"`.

Below, the content for our navigation menu is now encapsulated in a new `<div>` element which is given the class `"NavBar"`. And the markup for our search box is now encapsulated in another `<div>` which is given the class `"SearchBox"`.

```
<div class="NavBar">
 <ul>
 <li><a href="./">Home</a></li>
 <li><a href="about.html">About</a></li>
 <li><a href="learn/">Learn</a></li>
 <li><a href="extend/">Extend</a></li>
 <li><a href="share/">Share</a></li>
 </ul>
</div>

<div class="SearchBox">
 <form action="search.html" method="get">
 <label for="input-search">Search</label>
 <input name="q" type="search" id="input-search" />
 <button type="submit">Search</button>
 </form>
</div>
```

Here's the full and final markup, with our new `"NavBar"` and `"SearchBox"` widgets nested back inside the original `"NAVIGATION"` section of the layout.

```
<nav class="NAVIGATION">

 <div class="NavBar">
 <ul>
 <li><a href="./">Home</a></li>
 <li><a href="about.html">About</a></li>
 <li><a href="learn/">Learn</a></li>
 <li><a href="extend/">Extend</a></li>
 <li><a href="share/">Share</a></li>
 </ul>
 </div>

 <div class="SearchBox">
 <form action="search.html" method="get">
 <label for="input-search">Search</label>
 <input name="q" type="search" id="input-search" />
 <button type="submit">Search</button>
 </form>
 </div>

</nav>
```

We don't need anymore to use the class name of the parent layout section as a means of encapsulating the presentation of the nested widgets. We use each widgets' own unique class name for that.

```
.NavBar {}
.NavBar ul {}
.NavBar li {}
.NavBar a {}
.NavBar a:hover {}

.SearchBox {}
.SearchBox form {}
.SearchBox label {}
.SearchBox input {}
.SearchBox button {}
```

Because the presentation of the NavBar and SearchBox widgets is no longer dependant upon the markup for those widgets being placed in a certain position within the layout, we can now more easily reuse the same widgets in different contexts. We can copy-and-paste their markup into different sections of the layout and their presentation will remain intact.

## Modifiers

Sometimes you will want to vary the presentation of a single instance of an HTML element, a widget, or a section of the layout. To do that, add a modifier class.

Modifiers are written full lower case with words delimited by hyphens: `"modifier-name"`.

Modifiers may be defined as globals, which means they can be applied to just about anything. Be explicit in your intention and include the universal selector (`*`) when defining global modifiers.

```
*.ssh { color: #999; }
```

The problem with global modiiers is that they easily get overridden by higher-specificity selectors. That in turn encourages use of `!important`. For this reason it is better to design modifiers to be used on specific elements, widgets or layout sections.

Modifiers that are designed to vary the default presentation of specific HTML elements should be qualified with type selectors.

```
h1.homepage { font-size: 8rem; }
```

Modifiers that are designed to vary the default presentation of a particular widget should be prefixed with the name of that widget, written lower case. Furthermore, widget modifiers should be additionally qualified with the encapsulating widget class. Like this:

```
.Popup.popup-alert { border-color: red; }
```

The purpose is to reduce as much as possible the chances of the same modifier name being used for two unrelated things, which might result in unwanted style leakages. The prefix also helps us to see in the markup which modifiers belong to which widgets.

```
<div class="Popup popup-alert">
 ...
</div>
```

Modifiers may be needed to target specific elements _within_ a widget structure, too.

```
.Popup .popup-header { padding: 1rem 2rem; }
```

Usage:

```
<div class="Popup popup-alert">
 <header class="popup-header">
 ...
 </header>
 ...
</div>
```

Modifiers may be used to vary the default presentation of individual sections of the layout, too. The same rules apply as for widgets:

```
.NAVIGATION.navigation-is-collapsed { height: 0; }
```

Usage:

```
<nav class="NAVIGATION navigation-is-collapsed">
 ...
</nav>
```

Modifiers may get dynamically injected into a web page by a client-side JavaScript program. Dynamic modifiers should be prefixed with `"is-"` followed by a verb or noun that describes the component's current state.

```
<input type="text" class="is-invalid" />
```

Dynamic modifiers that belong to a particular widget or layout section should be further prefixed with the name of the widget or layout section (written lower case).

```
.SideNav.sidenav-is-opening {}
.SideNav.sidenav-is-open {}
.SideNav.sidenav-is-closing {}
.SideNav.sidenav-is-closed {}
```

Without exception every class must represent the name of a layout section, or the namespace for a widget, or it must exist to modify the default presentation of something. No one class must fuse these separate concerns.

The three types of classes are written differently:

- Layout: `"UPPER_CASE"`
- Widgets: `"CamelCase"`
- Modifiers: `"lower-case"`

What's nice about this naming convention is that the hierarchy of classes is represented by their case. Layout classes, written with full capitalisation, scream out from the markup ("HELLO_WORLD"). The camel case names of widgets, which always will be nested within layout sections, are a little less prominent ("HelloWorld"). And modifier classes, which are arguably the least important classes since they merely modify things and are not critical to achieving a default presentation, are the least conspicuous of all ("hello-world").

All three naming conventions can be seen in the following example, which is a fragment of markup from a Trello-like project management application.

```
<main class="BOARD">
 <div class="List">
 <h2 class="list-header">
 To do
 <svg><title>Options</title>...</svg>
 </h2>
 <div class="Card card-is-new">
 <a href="card/1234">
 <h3 class="plaintext">Homepage changes</h3>
 <ul class="card-members">
 <li title="Kieran Potts">KP</li>
 <li title="Maja Sienkiewicz">MS</li>
 </ul>
 </a>
 </div>
 <div class="Card">
 ...
 </div>
 </div>
 <div class="List">
 ...
 </div>
</main>
```

We can work out from their names and use of capitalisation what each class does. We know that the class `"BOARD"` positions a section of the main layout and that `"List"` and `"Card"` are the names of widgets – recurring patterns of content – that are reused again and again. We can also deduce that the modifier class `"list-header"` belongs to the List widget, while `"card-members"` belongs to the Card widget. The `"card-is-new"` modifier also belongs to the Card widget but it got put there by a JavaScript plugin that is responsible for adding dynamic behaviours to instances of the Card widget wherever they appear in the markup. And we might guess that the `"plaintext"` modifier makes the `<h3>` element look more like a standard paragraph, and that because this class is not prefixed it might work on other element types, too.

For further examples of the Systematic Web Design methodology, [download the free ebook](http://www.kieranpotts.com/books/systematic-web-design.pdf).

[![](http://public.systematicwebdesign.com/images/systematic-web-design-20150901.png)](http://www.kieranpotts.com/books/systematic-web-design.pdf)

## Free e-book (PDF)

- Advanced techniques
- Best practices
- More examples

[Download](http://www.kieranpotts.com/books/systematic-web-design.pdf)
