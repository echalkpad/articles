# jQuery Select Box Components - Chosen vs Select2

[Original URL](http://www.sitepoint.com/jquery-select-box-components-chosen-vs-select2/)

> This article was peer reviewed by Martín Martínez and Chris Perry. Thanks to all of SitePoint's peer reviewers for making SitePoint content the best it can be! Have you ever worked...

_This article was peer reviewed by [Martín Martínez](http://www.sitepoint.com/author/mmartinez/) and [Chris Perry](http://www.sitepoint.com/author/cperry/). Thanks to all of SitePoint's peer reviewers for making SitePoint content the best it can be!_

Have you ever worked on a project, and it seemed that something was off visually? Maybe the project was nearly finished, but some elements didn't look so good? These may be small details, but they make a difference.

If your project contains unattractive select boxes and you want to add more features to them, you will find [Chosen](https://harvesthq.github.io/chosen/) and [Select2](http://select2.github.io/) very useful. These are two [jQuery plugins](https://plugins.jquery.com/) that help style your select boxes to improve their look, customize their behavior and make them more user-friendly.

In this article, I will put these two plugins head-to-head, and compare their features and their use cases, so that you can make an informed choice as to which is best for you.

## Installation

Both [Chosen](https://github.com/harvesthq/chosen) and [Select2](https://github.com/select2/select2) are available via GitHub. This means you can clone the respective repository and obtain the files you need.

```
git clone https://github.com/harvesthq/chosen.git
git clone https://github.com/select2/select2.git
```

Otherwise, you can install both plugins with [Bower](http://bower.io/) (which serves as an interface to GitHub). If Bower is your preferred route, you might as well grab jQuery while you're at it.

```
bower install jquery
bower install select2
bower install chosen
```

Otherwise, grab the files from your CDN of choice (e.g. [CDNJS](https://cdnjs.com/)) and include them in your page in the usual manner. This is what I have done in the templates below which you can use this to follow along with the examples in the tutorial.

### Chosen

```
<!DOCTYPE html>
<html lang="en">
 <head>
 <meta charset="UTF-8">
 <title>Chosen/Select2 Template</title>
 <link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/chosen/1.4.2/chosen.min.css">
 </head>
 <body>

 <script src="//code.jquery.com/jquery-1.11.3.min.js"></script>
 <script src="//cdnjs.cloudflare.com/ajax/libs/chosen/1.4.2/chosen.jquery.min.js"></script>
 <script>// Additional logic goes here</script>
 </body>
</html>
```

### Select2

```
<!DOCTYPE html>
<html lang="en">
 <head>
 <meta charset="UTF-8">
 <title>Select2 Template</title>
 <link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/select2/4.0.1/css/select2.min.css">
 </head>
 <body>

 <script src="//code.jquery.com/jquery-1.11.3.min.js"></script>
 <script src="//cdnjs.cloudflare.com/ajax/libs/select2/4.0.1/js/select2.min.js"></script>
 <script>// Additional logic goes here</script>
 </body>
</html>
```

## Are the Projects Actively Maintained?

As you can tell by visiting the its GitHub page, development of the Chosen plugin is not as active as it once was and the [most recent version of Chosen](https://plugins.jquery.com/chosen/) dates from Febraury 6th 2014\. This is not to say that the library has been abandoned -- far from it! Rather the developers have got it to the place they want it to be and it remains a reliable and robust plugin.

Contrast that with Select2 on the other hand and the difference is noticeable. Development of this library is going full steam ahead and it recently released its official [fourth version](http://select2.github.io/announcements-4.0.html) (4.0.0). This means that new features are being added, whilst others are being deprecated and/or removed.

Another useful metric might be the amount of questions with these tags on Stack Overflow. You can try this for yourself here: <http://stackoverflow.com/tags>

If you type in "Select2" and then contrast that "Chosen", you'll see that there seems to be much more activity around the Select2 plugin. You'll also see that it has a number of integrations, such as with [AngularJS](https://github.com/angular-ui/ui-select) and [Ruby on Rails](https://github.com/argerim/select2-rails).

## Select Boxes and Placeholders

[Select2](https://select2.github.io/examples.html#single) and [Chosen](https://harvesthq.github.io/chosen/#standard-select) change the way simple and multiple select boxes appear.

In multiple select boxes with pure HTML, users can see some of the options available in the list. This is not visually appealing and a change must be made to this view. [Chosen](https://harvesthq.github.io/chosen/#multiple-select) and [Select2](https://select2.github.io/examples.html#multiple) remove the "list view" and the list of options will appear when you click inside the select box. Only then you can make the selections.

You can search your options with both plugins. Just click inside the box and then type the first characters of your choice, then the results will appear in real time according to your search.

Select2 makes it easier to deselect options that have already been selected. When the dropdown is open, you can click any selected element to deselect them. Chosen hasn't yet implemented this feature.

Both [Chosen](http://harvesthq.github.io/chosen/#default-text-support) and [Select2](https://select2.github.io/examples.html#placeholders) support placeholder text for single and multiple select boxes.

## Limited Number of Selections

Sometimes in a multiple select box, you need to limit the number of options users select. The advantage of using Chosen and [Select2](https://select2.github.io/examples.html#multiple-max) is that you can change the number of selections very easy.

```
max_selected_options: 2 // Chosen Plugin

maximumSelectionLength: 2 // Select2 Plugin
```

You can limit the number of selections that can be picked from a multi-select box with both of plugins, though it's worth noting that [Chosen's version](http://harvesthq.github.io/chosen/#limit-selected-options-in-multiselect) of this feature is more advanced. After you have reached the limit, if you try to select an additional option an event is called.

In this example, the users are asked to select two days which are the most productive in their week. When they try to select a third day, Chosen will call an event. I have set Chosen up to add an `alert` showing that the limit has been reached:

See the Pen [Limited Number of Selections: Chosen vs Select2](http://codepen.io/SitePoint/pen/gPvdOb/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

## Responsive Design

People will see your pages on all sorts of devices so select boxes, like other components of your website, should be responsive. Both [Chosen](http://harvesthq.github.io/chosen/#custom-width-support) and [Select2](https://select2.github.io/examples.html#responsive) support setting `width` with a percentage and the feature is nearly the same on both of them. This helps create responsive select boxes that take account of the screen size or browser window width.

The only difference that Chosen and Select2 have about this feature, is the code. You can declare Select2's `width` inside `select` tag in HTML. See it below to understand how handy is to make select boxes responsive and visually better with Chosen and Select2.

```
<!-- Chosen - HTML -->
<select class="responsiveChosen">
 ...
</select>

// Chosen - JavaScript
$(".responsiveChosen").chosen({width: "50%"});

<!-- Select2 - HTML -->
<select class="responsiveSelect2" style="width:50%">
 ...
</select>

// Select2 - JavaScript
$(".responsiveSelect2").select2();
```

## Other Similar Features

Chosen and Select2 have other common features, but I'd like to take a look at three features that I found interesting: disabled results, hidden search box, and Right-to-Left (RTL) support.

### Disabled Results

The most interesting of these is disabled results. With this option set, [Chosen](http://harvesthq.github.io/chosen/#selected-and-disabled-support) and [Select2](https://select2.github.io/examples.html#disabled-results) highlights enabled options. You can see disabled choices, but you can't select them. Disabled results would normally be set to prevent a user from selecting the option until some other condition has been met. This feature turns your select boxes into a powerful tool and it increases the engagement with the user if you learn how to use it according to your needs.

In order to let the plugins know that you have activated disabled results, add the attribute `disabled="disabled"` to the `option` tag where you will use this feature.

```
<option value="1" disabled="disabled">Product A</option>
```

In this Chosen example the user can only select options available in their geographic location, but they can also see the other options even though they are not available:

See the Pen [Chosen vs Select2: Disabled Results](http://codepen.io/SitePoint/pen/bELxNe/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

### Hiding the Search Box

When you don't have many choices in the options list in single select boxes, it is advisable to hide the search box. In [Chosen](http://harvesthq.github.io/chosen/#hide-search-on-single-select), to hide the search box you should use `disable_search_threshold` and set to it a bigger value than the number of options in your select box.

```
...
<option value="1">Yes</option>
<option value="0">No</option> <!--2 options-->
...

// Chosen - JavaScript
...
disable_search_threshold: 3
...
```

The same logic applies to [Select2](https://select2.github.io/examples.html#hide-search), but the only change is the terminology, instead of `disable_search_threshold` it is called `minimumResultsForSearch`.

```
// Select2 - JavaScript
...
minimumResultsForSearch: 3
...
```

One thing that I should add about Select2 is that you can hide the search box permanently without being concerned about the number of options you have in your select box. You can do this by setting `minimumResultsForSearch` to `Infinity`.

```
minimumResultsForSearch: Infinity
```

### Right-to-Left (RTL) Support

A large number of websites are written in different languages with different alphabets and aside from that, some of these languages are read from another direction. It is advisable that select boxes should adjust with the rules of these languages and shift their direction from LTR (Left-To-Right) to RTL. This improves the user experience and increases the usability of your website for users of that region.

Both Chosen and Select2 support Right-To-Left technique in select boxes. The only difference they have is that [Select2](https://select2.github.io/examples.html#rtl) needs you to declare the RTL support in the JavaScript file, meanwhile in [Chosen](http://harvesthq.github.io/chosen/#right-to-left-support) you just add `chosen-rtl` class alongside with the selected Chosen class in HTML.

## Why Pick Select2 over Chosen

Whilst Select2 was inspired by Chosen, and has most of features of Chosen, its contributors didn't stop there. They built in some of the coolest features for select boxes including [programmatic access](https://select2.github.io/examples.html#programmatic), [templating](https://select2.github.io/examples.html#templating), [disabled mode](https://select2.github.io/examples.html#disabled), [tagging](https://select2.github.io/examples.html#tags), and [tokenization](https://select2.github.io/examples.html#tokenizer). It also has support for [AJAX](https://select2.github.io/examples.html#data-ajax).

### Programmatic Access

[Programmatic access](https://select2.github.io/examples.html#programmatic) takes select boxes to another level. They are very useful combined with multiple select boxes. You can add buttons that act on these select boxes according to your rules using JavaScript.

If some of the options in the multiple select box have some logic linking with each other and according to your observations these "linked" options have a big probability to be selected from a lot of users, then it is useful to select these choices with a single click.

```
<select class="progControlSelect2" multiple="true">
 <option value="js">JavaScript</option>
 <option value="ph">PHP</option>
 <option value="jv">Java</option>
 <option value="rb">Ruby</option>
 <option value="sw">Swift</option>
 <option value="oc">Objective C</option>
 <option value="py">Python</option>
</select>
<form>
 <input type="button" class="iOSSelect2" value="Set iOS Technologies">
 ...
</form>

var $progControl = $(".progControlSelect2").select2({
 placeholder: "What technologies do you use most of the time" //placeholder
});
$(".iOSSelect2").on("click", function () { 
 $progControl.val(["sw", "oc"]).trigger("change"); 
});
```

In this example, you can quickly set the 'Swift' and 'Objective-C' by clicking the "iOS technologies" button:

See the Pen [Select2 – Programmatic Methods](http://codepen.io/SitePoint/pen/ZQrMYv/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

### Tagging

[Tagging](https://select2.github.io/examples.html#tags) is another of Select2's features that makes select boxes more powerful and extends the limit of normal select boxes. It lets users add new choices that are not already in the options list.

Tagging is also very handy in cases when the range of choices is very big, and you can't type all the choices in the options list. When tagging is enabled, and users can't find their choice in the list, it "pushes" them to create a tag of their choice.

Be careful when you use tagging though, as it can be misused by "malicious" users entering invalid tags which don't correspond to the objective of the select box.

To enable tagging, you should set the `tags` option to `true`.

```
$(".taggingSelect2").select2({
 ...
 tags: true,
});
```

See the Pen [Chosen vs Select2: Tagging](http://codepen.io/SitePoint/pen/vLdzEo/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

### Tokenization

Once `tags` are set to `true`, the option to include [token separators](https://select2.github.io/examples.html#tokenizer) becomes available. After entering the name of the tag, just type one of the token separators that are specified. Then the tag is entered as a choice in the options list. Token separators serve as shortcuts for creating tags by typing characters from your keyboard.

With the help of Select2, you can create token separators with any character you want. In the following example four token separators are used: `/`, `,`, `;` and `" "`(space).

```
$(".tokenizationSelect2").select2({
 placeholder: "Your favourite car", //placeholder
 tags: true,
 tokenSeparators: ['/',',',';',' '] 
});
```

Try it by typing a new option, followed by one of the separator characters to select it and add it to the options list:

See the Pen [Select2 – Tokenization](http://codepen.io/SitePoint/pen/VeQGLO/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

### Templating

One of most powerful features of Select2 is [templating](https://select2.github.io/examples.html#templating). Templating allows us to customize the look of components displayed by Select2\. This feature can be used to merge the minimum input which is provided by the `option` tag with images or other components to create a visual display for the user.

This example combinines Select2's templating with [Bootstrap's Glyphicons](http://getbootstrap.com/components/#glyphicons):

See the Pen [Select2 Templating](http://codepen.io/SitePoint/pen/bELxVw/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

## Conclusion

Though Chosen has some nice options within it, Select2 offers rather more in terms of available features for use with select boxes. Chosen is a good option if you combine it within the "flow" of your website, but if you want to access more advanced features, then Select2 is the better choice.

I would strongly recommend using Select2; it is updated more often than Chosen and has fewer bugs. One thing I found very helpful about Select2 is its documentation. While you are reading it, you can use Select2 components in your project on the fly.

If you have had any good or bad experiences using Chosen or Select2, let me know in the comment section below.
