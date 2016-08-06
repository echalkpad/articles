# Real-Time Search in JavaScript

[Original URL](http://osvaldas.info/real-time-search-in-javascript)

> 11 Aug What I meant was scanning the DOM of a page for text equivalents and showing the actual parts of the page, as well as hiding the irrelevant ones. I came up with the technique when I was...

11 Aug What I meant was scanning the DOM of a page for text equivalents and showing the actual parts of the page, as well as hiding the irrelevant ones. I came up with the technique when I was designing [Readerrr](http://readerrr.com)'s FAQ page. Take a look at the example:

I have also implemented the solution here on my [blog](http://osvaldas.info/blog).

## How it works

All simple. Let's take the FAQ page as an example. Here's a typical markup:

```html
<h1>FAQ</h1>
<div class="faq">
    <input type="search" value="" placeholder="Type some keywords (e.g. giza, babylon, colossus)" />
    <ul>
        <li id="faq-1">
            <h2><a href="#faq-1">Great Pyramid of Giza</a></h2>
            <div>
                <p>The Great Pyramid of Giza <!-- ... --></p>
                <!-- ... -->
            </div>
        </li>
        <li id="faq-2">
            <h2><a href="#faq-2">Hanging Gardens of Babylon</a></h2>
            <div>
                <p>The Hanging Gardens of Babylon <!-- ... --></p>
                <!-- ... -->
            </div>
        </li>
        <!-- ... -->
    </ul>
    <div class="faq__notfound"><p>No matches were found.</p></div>
</div>
```

I wrote a tiny piece of JavaScript code to handle the interaction and this is how it works:

1. When the page loads, the script indexes the content of all `li`'s into browser's memory.
2. When a user types text into the search field, the script searches for equivalents among the indexed data and hides the corresponding `li`'s where no equivalents were found. If nothing found, a message is shown.
3. The script highlights the text equivalents by replacing phases, for example, `babylon` becomes `<span class="highlight">babylon</span>`.

Now, try it yourself:

[Demo](http://osvaldas.info/examples/real-time-search-in-javascript)

## Taking it further

Since I chose FAQ page as an example, there are some issues to deal with.

### Toggling the answers

It is a good practice to hide the answers by default and show them only when user needs them, that is to say when they _press_ the question:

```js
.faq > ul > li:not( .is-active ) > div
{
    display: none;
}

$( document ).on( 'click', '.faq h2 a', function( e )
{
    e.preventDefault();
    $( this ).parents( 'li' ).toggleClass( 'is-active' );
});
```

In the CSS part I use _child combinator selector_ `>` because I don't want to select and, therefore, to hide the elements of an answer, which may contain lists and _div's_.

### What if JavaScript is disabled

The user won't be able to see the answers. Unless you show them by default or develop a JavaScript-less solution. To do this, take a closer look at these fragments of the markup:

- `<li id="faq-1">`
- `<a href="#faq-1">`

The usage of _fragment identifiers_ enables us to take the advantage of CSS's pseudo selector `:target`:

```js
.faq > ul > li:not( :target ) > div
{
    display: none;
}
```

Furthermore, the real-time search is not possible as well. But you can either provide a sever-side search possibility or hide the search field and so as not to confuse the user:

```html
<html class="no-js">
    <head>
        <!-- remove this if you use Modernizr -->
        <script>(function(e,t,n){var r=e.querySelectorAll("html")[0];r.className=r.className.replace(/(^|\s)no-js(\s|$)/,"$1$2")})(document,window,0);</script>
    </head>
</html>
```

I added a class name `no-js` to `<html>` element. The `<script>` part removes that class name. If JavaScript support is disabled in a browser, the class name won't be removed; therefore:

```js
.no-js .faq input
{
    display: none;
}
```

The _no-js_ is a very handy technique, you can use it site-wide.

### Improving UX

If there is only one list item that matches user's query, it is a good practice to automatically show the content of that item, without requiring to press the title. To see what I mean, head over the [GIF](http://osvaldas.info/real-time-search-in-javascript#image-1) at the beginning of the post.

### Hidden keywords

Here on my blog I have a _filterable_ list of blog post titles only. Each post has some related keywords assigned. So, during the search, how do I make an item discoverable even if the title does not consist of a particular keyword? For example, how can I make the entry _"Real-Time Search in JavaScript"_ visible if a user entered _"jquery"_? Yes, exactly, that is adding keywords and hiding them with CSS:

```html
<li>
    <h2><a href="/real-time-search-in-javascript">Real-Time Search in JavaScript</a></h2>
    <p class="hidden-keywords" aria-hidden="true">jquery filter input html css</p>
</li>

.hidden-keywords
{
    display: none;
}
```

A simple trick but not always that obvious.

You will find two versions of the code in the source of the demo: without dependencies and jQuery-dependent. These versions are also divided into three groups of code so you can adapt only what your project needs.

[Demo](http://osvaldas.info/examples/real-time-search-in-javascript)
