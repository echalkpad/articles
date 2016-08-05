# Best Practices When Working With JavaScript Templates - Tuts+ Code Tutorial

[Original URL](http://code.tutsplus.com/tutorials/best-practices-when-working-with-javascript-templates--net-28364)

> Maybe you don't need them for simple web apps, but it doesn't take too much complexity before embracing JavaScript templates becomes a good decision. Like any other tool or technique, there are a few...

Maybe you don't need them for simple web apps, but it doesn't take too much complexity before embracing JavaScript templates becomes a good decision. Like any other tool or technique, there are a few best practices that you should keep in mind, when using templates. We'll take a look at a handful of these practices in this tutorial.

## <span> 1\. </span>

 [Underscore](http://underscorejs.org/) for Simple, [Handlebars](http://handlebarsjs.com) for Complex

> If you need something with a bit more zest, might I recommend [Handlebars](http://handlebarsjs.com/)?

First things first: unless you're [John Resig](http://ejohn.org/blog/javascript-micro-templating/), you most likely want to choose a well-tested library to offer your template functionality. While the options are nearly endless, there are two really good options, depending on the complexity of your project.

If the project is relatively simple, you can use [Underscore.js](http://underscorejs.org/). For the most part, this library offers functional programming utilities, but it does have a `_.template` method that couldn't make things easier. By default, it uses the ERB-esque `<%= %>` delimiters, but that can easily be modified. The beauty of this solution is that any project that requires templates will likely have Underscore loaded already, simply due to its sheer general usefulness. To learn more about Underscore, check out Siddharth's [comprehensive tutorial](http://net.tutsplus.com/tutorials/javascript-ajax/getting-cozy-with-underscore-js/) right here on Nettuts+.

If you need something with a bit more zest, might I recommend [Handlebars](http://handlebarsjs.com/)? With many useful block expressions (such as `#each` for looping and `#if` for conditionals) and the ability to register your own helper functions, Handlebars will give you everything you need to create even the most complex of templates.

> If you're not familiar with Handlebars, Gabriel Manricks has got you covered in this [tutorial](http://net.tutsplus.com/tutorials/javascript-ajax/introduction-to-handlebars/).

Of course, there are other template libraries out there; feel free to check them out! I only recommend these two because I enjoy and use them myself. They also tend to be the most popular offerings in the community.

[![Handlebars](https://cdn.tutsplus.com/net/uploads/legacy/2167_Templates/handlebars-templating.jpg)](http://handlebarsjs.com/)<br>
Handlebars is an excellent JavaScript templating engine.

## <span> 2\. </span>

 Create Template Helper Functions

> Most template libraries will make the data object that you pass into the template function the context.

There will be times, when the data that you are inserting into a template won't quite be formatted in the way you prefer. In these situations, you'll need to create custom functions to format it. If you're using something like Handlebars, you can easily register a helper function; but other solutions, like Underscore, don't offer that functionality. You'll have to roll your own.

The reason why this isn't as simple as it might sound is because most template libraries will make the data object that you pass into the template function the context, or the value of `this`. Therefore, the function needs to be part of this object. There are a couple of ways to do this. The primary way is to to add the function to the data object before passing it to the template function. Here's an example:

```
// assume data object and template function

data.formatPrice = function (priceInCents) {
 return "$" + (priceInCents / 100).toFixed(2);
}

var html = template(data);
```

This is great for one-off situations, but it's possible that you'll have multiple sets of templates, each which needs its own set of helper functions. What I like to do in these cases is wrap the template function in another function that will apply the helpers to the `data` object.

```
var productTemplate = function (data) {
 var template = _.template("the template string");
 data.helperFunction1 = function () { return "whatever" };
 return template(data);
};
```

There are several ways to improve this (you might start with caching the "raw" template function outside this function, probably via a closure), but that's the basic idea. Now, you can simply pass your data to that `productTemplate` and have access to your helper functions.

## <span> 3\. </span>

 Store Templates in Their Own File

> There are several alternate solutions that might be better, especially in more complex scenarios.

All JavaScript templates obviously start out as text. The most popular (and natural) place to store them is within your HTML document - usually in a `script` tag with an alternate `type` attribute, so that the browser doesn't attempt to execute them. It's fairly easy to grab the `innerHTML` attribute of the `script` tag and pass it to the template-creating function, when you're ready.

However, this isn't the only way to do it; in fact, it might not be the optimal way. There are several alternate solutions that might be better, especially in more complex scenarios.

The first alternative is to store them all within a JavaScript file. Of course, this means that your templates will be stored as strings instead of the more readable indented HTML, but stick with me for a second! First, for templates that are longer than a single line (most templates), you don't have to use an unwieldy, wrapping string. Instead, try something like this:

```
Templates = {};

Templates.contactEntry = [
 "<h1> {{fullName}} </h1>",
 "<ul>",
 "<li> Email: {{email}} </li>",
 "<li> Phone: {{tel}} </li>",
 "</ul>"
].join("\n");
```

Storing a template in an array like this makes it much easier to handle. Using syntax like this, you can easily store all your templates in their own JavaScript file, and have that file loaded onto the page before you need the templates. And of course, you don't have to keep them all within a single `Template` object, but it keeps things organized. That `Templates` object could even be a property on your global application object (as in, `MyApp.Templates`).

But wait, there's more (to coin a phrase). You can convert all of your templates to their respective template functions in a single loop:

```
for (var tmpl in Templates) {
 if (Templates.hasOwnProperty(tmpl) {
 Templates[t] = _.template(Templates[t]); // Underscore example
 }
}
```

If you're using AMD in your application, this method will still work; just put that in a templates module that returns that `Templates` object. However, many AMD solutions have a text plugin that lets you load plain text files; instead of the normal module object, you'll get a string in return. If you're using the RequireJS library, you'll have to include the `text.js` plugin in the same directory as the `require.js` file. Then, you can do something along the lines of:

```
require(["text!templates/document.html"], function (documentTemplate) {

});
```

That `documentTemplate` parameter will be a string containing whatever contents is in that `templates/document.html` file. When doing it this way, you won't be able to put multiple templates into one file, unless you want to manipulate that string.

## <span> 4\. </span>

 Precompile Your Templates

> If using the Asset Pipeline in a Rails app, take advantage of [Sprockets](https://github.com/rails/sprockets-rails) to pre-compile template functions.

If you think about it for a second, there's some extra work done by the browser every time you create a template. Usually, that template starts out as a string that you pass to a template-creating function. That function returns another function, which you can pass the data to and receive HTML from. The extra work is the "creating-the-template-function" part; there's no reason why this can't be done before the JavaScript is sent to the client. Preferably, you could put add this work to your build process, along with minifying your CSS and concatenating your JS.

Unfortunately, pre-compiling JavaScript templates isn't quite as simple as minifying or concatenating... at least, not just yet, probably due to the many ways to create templates. If you're using Grunt or Yeoman, you can look up plugins ([like this one](https://github.com/gruntjs/grunt-contrib-handlebars)) on the [Grunt website](http://gruntjs.com/). If you're using the Asset Pipeline in a Rails app, you can take advantage of [Sprockets](https://github.com/rails/sprockets-rails) to pre-compile your template functions.

> Oh, and if you're adventurous (and a Tuts+ Premium subscriber), you can join me as I [precompile templates from scratch](https://tutsplus.com/lesson/precompiled-templates/) in my [Advanced Backbone Patterns and Techniques course](https://tutsplus.com/course/advanced-backbone-patterns-and-techniques/).

## <span> 5\. </span>

 No Evaluation in Templates

> No evaluation in templates.

Not too long ago, while researching for [another project](https://tutsplus.com/course/advanced-backbone-patterns-and-techniques/), I came across an interesting idea regarding JavaScript templates in the excellent book [Recipes with Backbone](http://recipeswithbackbone.com/). It's since become a best practice in the community: no evaluation in templates. Of course, interpolating variables is, strictly speaking, evaluation, but what I'm more referring to here is logic code. You can put whatever JavaScript you'd like inside of the delimiting tags, but it can easily get out of hand. We all know that it's considered a best practice to keep your HTML, CSS, and JavaScript separate; this makes it easier to keep track of the code and spot errors, when necessary. The same is true for templates: they should be a place for interpolating values only. Any logic or data transformation should be performed outside the template.

Of course, how far you go with this idea is up to you. You might decide that looping inside your templates is okay; you might have a template like this:

```
<h1> My List </h1>
<ul id="myList">
 <% list.forEach(function (item) { %>
 <li> <%= item.name %> </li>
 <% }); %>
</ul>
```

Or, you might instead choose to loop outside your templates by creating a wrapper template, and then looping over items, rendering a sub-templates, and inserting them into the wrapper template. You might end up with two templates like this:

The wrapper template:

```
<h1> My List </h1>
<ul id="myList">
</ul>
```

The sub-template:

```
<li> <%= name %> </li>
```

Of course, this separated method makes for a bit more code, but you'll find it worth the effort in the long run.

Along these lines, it's a good practice follow the lead of the framework or library you're using. For example, I've found that, when using Backbone with Underscore templates, it is easier to use outside loops and sub-templates: Underscore's minimal template functionality doesn't offer any looping syntax, and Backbone's `render` method is a great place to do that looping and inserting the sub-templates. However, when using Meteor, which builds in Handlebars templates, it's much easier to loop inside the templates with an `#each` block; (and use sub-templates, too, if you want).

## <span> 6\. </span>

 Bind Data to the Templates

> Learn more about Backbone.stickit at [Tuts+ Premium.](https://tutsplus.com/lesson/backbone-stickit/)

This won't always be applicable, but, in some cases, it can be really helpful to make your templates update automatically, when the data they are displaying changes. I like this idea a lot, because it allows even the UI of your application to be completely data-driven: when a model attribute updates, the UI updates with it. This is the basic principle behind tools, like Knockout.

While you could probably roll this functionality on your own without too much trouble, all the popular frameworks have it built in. For example, in Backbone, a template's `initialize` function might include some custom event listeners, like so:

```
this.model.on('change', this.render, this);
```

This way, whenever a model attribute changes, the template's `render` function will be called and the template re-rendered. Alternatively, you can use a plugin, like [backbone.stickit](https://github.com/NYTimes/backbone.stickit), which will manage the bindings for you. If you're working with Meteor and using one of its reactive data sources, you'll get this binding for free - no extra work necessary. I'm not familiar enough with any other framework to know exactly how they do it, but any framework worth using should have a similar feature.

## <span> 7\. </span>

 Simplify Your Templates

> Very quickly, your templates can get out of hand and become unwieldy.

If you aren't careful, very quickly, your templates can get out of hand and become unwieldy. This is why it's always a good idea to limit your templates to a reasonable size. If you make them too large, they'll be more difficult to update, and won't allow for a good separation of the code. On the other hand, if they're too small, they'll cost too much for what they bring, and will subsequently slow down your app.

This is why it's important to find a happy medium. Approach your templates in the same way that you write your JavaScript or CSS code: make it modular. Yes, each UI "chunk" or widget should have it's own template, but don't forget about sub-templates. They're useful, when smaller units of a widget have complex layouts or states, as well as when they have multiple events, but remember that they can be a double-edged sword. Don't use them unless you have a good reason to.

## <span> 8\. </span>

 Don't Use 'em if You Don't Need 'em

Finally, remember that JavaScript templates is just one more tool in your box; and, sometimes, it simply isn't the right one for the job. Don't use templates where you don't need them. Use your head: there may be other situations, when a template is not the best tool.

## Conclusion

We'll, those are my best tips for using JavaScript templates, but perhaps you can think of a few more! If so, please share them in the comments below, so that we can continue the discussion.
