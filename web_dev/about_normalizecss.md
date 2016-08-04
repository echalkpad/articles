# About normalize.css

[Original URL](http://nicolasgallagher.com/about-normalize-css/)

> Normalize.css is a small CSS file that provides better cross-browser consistency in the default styling of HTML elements. It's a modern, HTML5-ready, alternative to the traditional CSS reset....

[Normalize.css](http://necolas.github.com/normalize.css/) is a small CSS file that provides better cross-browser consistency in the default styling of HTML elements. It's a modern, HTML5-ready, alternative to the traditional CSS reset.

- **[Normalize.css project site](http://necolas.github.com/normalize.css/)**
- **[Normalize.css source on GitHub](https://github.com/necolas/normalize.css)**

Normalize.css is currently used in some form by [Twitter Bootstrap](http://getbootstrap.com/), [HTML5 Boilerplate](http://html5boilerplate.com/), [GOV.UK](http://www.gov.uk/), [Rdio](http://www.rdio.com/), [CSS Tricks](http://css-tricks.com/), and many other frameworks, toolkits, and sites.

## Overview

Normalize.css is an alternative to CSS resets. The project is the product of 100's of hours of extensive research by [@necolas](https://twitter.com/necolas) and [@jon_neal](https://twitter.com/jon_neal) on the differences between default browser styles.

The aims of normalize.css are as follows:

- **Preserve useful browser defaults** rather than erasing them.
- **Normalize styles** for a wide range of HTML elements.
- **Correct bugs** and common browser inconsistencies.
- **Improve usability** with subtle improvements.
- **Explain the code** using comments and detailed documentation.

It supports a wide range of browsers (including mobile browsers) and includes CSS that normalizes HTML5 elements, typography, lists, embedded content, forms, and tables.

Despite the project being based on the principle of normalization, it uses pragmatic defaults where they are preferable.

## Normalize vs Reset

It's worth understanding in greater detail how normalize.css differs from traditional CSS resets.

### Normalize.css preserves useful defaults

Resets impose a homogenous visual style by flattening the default styles for almost all elements. In contrast, normalize.css retains many useful default browser styles. This means that you don't have to redeclare styles for all the common typographic elements.

When an element has different default styles in different browsers, normalize.css aims to make those styles consistent and in line with modern standards when possible.

### Normalize.css corrects common bugs

It fixes common desktop and mobile browser bugs that are out of scope for resets. This includes display settings for HTML5 elements, correcting `font-size` for preformatted text, SVG overflow in IE9, and many form-related bugs across browsers and operating systems.

For example, this is how normalize.css makes the new HTML5 `search` input type cross-browser consistent and stylable:

```
input[type="search"] {
 -webkit-appearance: textfield; 
 -moz-box-sizing: content-box;
 -webkit-box-sizing: content-box; 
 box-sizing: content-box;
}



input[type="search"]::-webkit-search-decoration,
input[type="search"]::-webkit-search-cancel-button {
 -webkit-appearance: none;
}
```

Resets often fail to bring browsers to a level starting point with regards to how an element is rendered. This is particularly true of forms – an area where normalize.css can provide some significant assistance.

A common irritation when using resets is the large inheritance chain that is displayed in browser CSS debugging tools.

![](http://nicolasgallagher.com/about-normalize-css/css-reset-debugging.png) A common sight in browser debugging tools when using a CSS reset This is not such an issue with normalize.css because of the targeted styles and the conservative use of multiple selectors in rulesets.

### Normalize.css is modular

The project is broken down into relatively independent sections, making it easy for you to see exactly which elements need specific styles. Furthermore, it gives you the potential to remove sections (e.g., the form normalizations) if you know they will never be needed by your website.

### Normalize.css has extensive documentation

The normalize.css code is based on detailed cross-browser research and methodical testing. The file is heavily documented inline and further expanded upon in the [GitHub Wiki](https://github.com/necolas/normalize.css/wiki). This means that you can find out what each line of code is doing, why it was included, what the differences are between browsers, and more easily run your own tests.

The project aims to help educate people on how browsers render elements by default, and make it easier for them to be involved in submitting improvements.

## How to use normalize.css

First, install or [download normalize.css](http://necolas.github.com/normalize.css/) from GitHub. There are then 2 main ways to make use of it.

Approach 1: use normalize.css as a starting point for your own project's base CSS, customising the values to match the design's requirements.

Approach 2: include normalize.css untouched and build upon it, overriding the defaults later in your CSS if necessary.

Normalize.css is significantly different in scope and execution to CSS resets. It's worth trying it out to see if it fits with your development approach and preferences.

The project is developed in the open on GitHub. Anyone can report issues and submit patches. The full history of the project is available for anyone to see, and the context and reasoning for all changes can be found in the commit messages and the issue threads.

Detailed information on default UA styles: [WHATWG suggestions for rendering HTML documents](http://www.whatwg.org/specs/web-apps/current-work/multipage/rendering.html#the-css-user-agent-style-sheet-and-presentational-hints), [Internet Explorer User Agent Style Sheets](http://iecss.com/),and [CSS2.1 User Agent Style Sheet Defaults](http://css-class.com/test/css/defaults/UA-style-sheet-defaults.htm).

### Translations

- [Про normalize.css](http://css-live.ru/faq/pro-normalize-css/)
- [About normalize.css (Italiano)](http://www.laboratoriocss.it/about-normalize-css.html)
