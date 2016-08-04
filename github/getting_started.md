# Getting started

[Original URL](http://getcontenttools.com/getting-started)

> A very short intro ContentTools is one of a collection of libraries designed for building tools that make it easier to edit HTML content. The ContentTools library provides a fully functional...

## A very short intro

ContentTools is one of a [collection of libraries](http://getcontenttools.com/api) designed for building tools that make it easier to edit HTML content. The ContentTools library provides a fully functional page editor which is the subject of the _Getting started_ guide. However if you're interested in customizing the editor, extending the libraries or have something different in mind for the code then check out the [Tutorials](http://getcontenttools.com/tutorials) and [API](http://getcontenttools.com/api) sections.

## Download

First task is to download the JavaScript, CSS and other associated project files. [Download the repo](https://github.com/GetmeUK/ContentTools/archive/master.zip) and open the **/build** folder which contains everything you need including pre-built source files. Copy the contents of the folder into appropriate locations for your project _(e.g content-tools.min.js > /www/scripts/content-tools.min.js)_. However, the **/images** folder and **icons.woff** font need to be copied into the same folder as **content-tools.min.css** (which I'll explain next). Your file structure should look something like:

```
/site
 /assets
 /images
 - ce-drop-above.png
 - ce-drop-below.png
 - ce-drop-left.png
 - ce-drop-right.png
 - video.png
 - content-tools.min.js
 - content-tools.min.css
 - icons.woff
```

The libraries themselves are written using [CoffeeScript](http://coffeescript.org/) and [SASS](http://sass-lang.com/) and ideally you'll want to roll your own for each web project. The build process is managed by [grunt](http://gruntjs.com/) and is very simple, however to get started there's no need.

If you're more the sort to learn by pulling code apart, there is a full demo of the editor complete with example content included in the **/sandbox** folder.

If you are using Firefox then you may experience an issue where the icon font (icon.woff) doesn't load on your local machine. A reason for this along with a solution to the issue can be found on [stackoverflow](http://stackoverflow.com/questions/2856502/css-font-face-not-working-with-firefox-but-working-with-chrome-and-ie).

## Prepare the HTML

Include the ContentTools CSS and JS in your HTML page:

```
<head>
 <title>My page</title>
 <link rel="stylesheet" type="text/css" href="assets/content-tools.min.css">
 ...
</head>
<body>
 ...
 <script src="assets/content-tools.min.js"></script>
 <script src="assets/editor.js"></script>
</body>
```

Notice that we also included an additional JavaScript file named **editor.js.** This will contain the code that initializes our editor; go ahead and create the file but leave it empty for now.

Next we need to mark the regions that will be editable. There's no set way to do this but using a **CSS class** or **data-...** attribute is recommended. Only the outer element that will contain editable content is marked:

```
<div data-editable data-name="main-content">
 <blockquote>
 Always code as if the guy who ends up maintaining your code will be a violent psychopath who knows where you live.
 </blockquote>
 <p>John F. Woods</p>
</div>
```

The **data-name** attribute is used to identify the region when saving (by default the **id** attribute is used but I prefer to keep the editable markup separated from the existing HTML markup).

### Regions must be parents

A common misunderstanding when marking up editable HTML is to mark up a single element as editable, for example:

```
<h1 data-editable data-name="heading">Content</h1>
```

This wont work as a region has to contain editable block level elements. A simple way around this is to wrap the `<h1>` tag like so:

```
<div data-editable data-name=heading>
 <h1>Content</h1>
</div>
```

## Preparing the CSS

ContentTools uses CSS classes to align text, imagery, videos and iframes (typically used by services such as YouTube and Vimeo to embed their players). You'll need to define styles for these alignment classes in your own CSS, for example:

```
/* Alignment styles for images, videos and iframes in editable regions */

/* Center (default) */
[data-editable] iframe,
[data-editable] image,
[data-editable] [data-ce-tag=img],
[data-editable] img,
[data-editable] video {
 clear: both;
 display: block;
 margin-left: auto;
 margin-right: auto;
 max-width: 100%;
}

/* Left align */
[data-editable] .align-left {
 clear: initial;
 float: left;
 margin-right: 0.5em;
}

/* Right align */
[data-editable].align-right {
 clear: initial;
 float: right;
 margin-left: 0.5em;
}

/* Alignment styles for text in editable regions */
[data-editable] .text-center {
 text-align: center;
}

[data-editable] .text-left {
 text-align: left;
}

[data-editable] .text-right {
 text-align: right;
}
​
```

We scope the alignment styles to apply to elements within **[data-editable]** regions.

## Launch the editor

ContentTools provides an editor but we need to configure a few things before we can launch it, namely:

- The CSS styles we want users to be able to apply to elements.
- The regions of the page we want to be editable.
- A mechanism for saving our content.
- _We might also configure an image handler but that's for a later [tutorial](http://getcontenttools.com/tutorials/handling-image-uploads)._

Add the following code to the **editor.js** file we created earlier:

```
window.addEventListener('load', function() {
 var editor;

});
```

### Configure styles

Much like a word processor, it's possible to configure a list of predefined styles for your content. These then appear when the user **selects a tag from the inspector bar at the bottom of the viewport.** Only styles applicable to the tag type are displayed, though styles can be set to be applicable to all tags.

![Styles tab in properties dialog](http://getcontenttools.com/images/pages/getting-started/properties-dialog-styles-tab.png) We're going to add a single style **.author** that can be applied to paragraph **<p>** tags. Underneath the `var editor;` declaration add:

```
ContentTools.StylePalette.add([
 new ContentTools.Style('Author', 'author', ['p'])
]);
```

The **StylePalette.add** method allows us to add a list of styles to the editor. Each style is declared as a **Style** instance initialized with a display name, a CSS class and a list of tags the style can be applied to. We'll need to add the relevant CSS to support this style so in the head of your HTML add:

```
<head>
 ...
 <style>
 .author {
 font-style: italic;
 font-weight: bold;
 }
 </style>
</head>
```

### Select the editable regions

Next we need to initialize the editor and let it know which elements on the page are editable. To do this add the following code to **editor.js**:

```
editor = ContentTools.EditorApp.get();
editor.init('*[data-editable]', 'data-name');
```

We **init**ialize the editor with a CSS selector for the editable regions of the page and an attribute name (**'data-name'**) to tell the editor which of the element's attributes contains its region name. Region names must be unique within the same page.

### Save changes

Finally we want to be notified when the user saves the page so that we can store the updated content of each region in a file or on a database. To do this we listen for the **saved** event to be triggered by the **editor**. Add the following code to **editor.js** after the **editor.init** statement.

```
editor.addEventListener('saved', function (ev) {
 var name, payload, regions, xhr;

 // Check that something changed
 regions = ev.detail().regions;
 if (Object.keys(regions).length == 0) {
 return;
 }

 // Set the editor as busy while we save our changes
 this.busy(true);

 // Collect the contents of each region into a FormData instance
 payload = new FormData();
 for (name in regions) {
 if (regions.hasOwnProperty(name)) {
 payload.append(name, regions[name]);
 }
 }

 // Send the update content to the server to be saved
 function onStateChange(ev) {
 // Check if the request is finished
 if (ev.target.readyState == 4) {
 editor.busy(false);
 if (ev.target.status == '200') {
 // Save was successful, notify the user with a flash
 new ContentTools.FlashUI('ok');
 } else {
 // Save failed, notify the user with a flash
 new ContentTools.FlashUI('no');
 }
 }
 };

 xhr = new XMLHttpRequest();
 xhr.addEventListener('readystatechange', onStateChange);
 xhr.open('POST', '/save-my-page');
 xhr.send(payload);
});
```

We're using AJAX to send the content of each region to the server when the user saves the page. How that content is saved is a decision for you, but the [Saving strategies](http://getcontenttools.com/tutorials/saving-strategies) tutorial presents a couple of approaches that might be a useful starting point.

Providing the user with some feedback is nice and so we set the editor to its **busy** state while we wait for a response from the server. In its busy state a spinning cog will appear to let the user know that something's happening. Once we get a response, a **flash** is used to notify the user of the success/failure of their save request. Flashes appear large and central for a short period; the **ok** flash displays a tick and **no** a cross.

## Fin

That's it; open your page in a browser, look for the blue edit button top left and click it to start editing.

If you do get stuck don't forget there's a full demo included in the **/sandbox** folder of the ContentTools repo you downloaded.

For a bit of user guidance the [demo page](http://getcontenttools.com/demo) is a good place to start.
