# Creating a Photo Discovery App with NW.js (part 1)

[Original URL](https://scotch.io/tutorials/creating-a-photo-discovery-app-with-nw-js-part-1)

> NW.js (formerly known as Node Webkit) is a framework for creating cross-platform desktop applications using HTML, CSS, and JavaScript. In this tutorial, we're going to a desktop application...

**NW.js** (formerly known as Node Webkit) is a framework for creating cross-platform desktop applications using HTML, CSS, and JavaScript. In this tutorial, we're going to a desktop application using [NW.js](http://nwjs.io), and through the tutorial give you a good understanding of how NW.js works under the hood.

This tutorial is aimed at anyone who knows HTML, CSS, and JavaScript. **Experience with Node.js is not required.**. We're going to split this tutorial into 2 parts! Here's part 1.

## What we're going to build

![Step 15 Window Toolbar](https://scotch.io/wp-content/uploads/2015/09/step-15-window-toolbar.png)

We're going to make a photo browsing app called **Lens**. It will allow you to browse photos that exist inside a folder on your computer, take a closer look at one photo at a time, apply colour filters, and save the altered photo to your computer.

## Prerequisites

If you want to follow the tutorial and build the desktop app, you'll need the following:

- Any computer running either Mac OS X, Windows, or Linux
- Node.js (see installing Node.js)

## Installing Node.js

### On Mac OS X or Linux

I would recommend installing Node.js via a tool called [NVM](https://github.com/creationix/nvm) (Node.js version manager). It allows you to install different versions of [Node.js](https://nodejs.org), and switch between them easily. You can find the installation instructions at <https://github.com/creationix/nvm>.

### On Windows

The setup.exe file from the Node.js website is fine. There is a version of NVM for Windows, but I haven't been successful in getting that to work off the bat.

## Getting started

Assuming that you have Node.js installed, we can start by installing the NW.js module. Type the following command into your command line terminal:

```
npm install -g nw
```

For those new to Node.js, npm is Node's package manager, and allows you to install modules to use in your projects. The `-g` flag tells NPM to install nw.js as a global module, meaning that it will be available to all of the Node.js projects on your computer, and allow you to use the "nw" command on the terminal to execute loading a project as a desktop app.

You'll see logging information output to the terminal showing the process of installing the module. If it was successful, you should see something like this

![Pre Install NW.js](https://scotch.io/wp-content/uploads/2015/09/pre-install-nw.png)

If however there is an error, please let me know in the comments, or alternatively search for the error message on Google to see related Stack Overflow posts or Github issues that might offer a way to resolve the issue.

Once we have NW.js installed on our computer, the next thing we want to do is to create the initial skeleton for our application. Using the terminal command prompt, let's create a folder for our application's files called "lens", and change the current working directory to that folder:

```
mkdir lens
cd lens
```

Inside of the folder, we need just 2 files to create a bare skeleton NW.js app; an `index.html` file, and a `package.json` file. The `package.json` file is a manifest file that contains configuration information about the app, such as its name, version, and what file it loads when starting the application. It looks something like this:

```
{
 "name": "Lens",
 "version": "1.0.0",
 "main": "index.html"
}
```

Save this file inside of the lens folder, under the filename `package.json`. Next, we will create the `index.html` file that will be displayed then the app runs. Create a file called `index.html` in the lens folder, and type in the following HTML:

```
<html>
 <head>
 <title>Lens</title>
 </head>
 <body>
 <h1>Lens, the photo app</h1>
 </body>
</html>
```

Once you have saved this file to the computer, we can now run this code as a desktop application.

Going back to the command line terminal, make sure that you are in the lens folder, and run the following command:

```
nw
```

When you do this, you should expect to see an application boot, and the screen should look something like this:

![Step 1 Screenshot](https://scotch.io/wp-content/uploads/2015/09/step-1-screenshot.png)

Some of you may be looking at this and thinking "_this looks like a web page in a browser, embedded in a desktop app_", and to some extent that is correct – NW.js is a combination of Google Chrome's Blink web browser rendering engine and IO.js*, along with some Operating System bindings. The combination of these technologies is very powerful, which we will explore later on in the tutorial.

## Fleshing out the user journey

Now is a good time to look at the user journey of the desktop application:

![NW.js wireframes](https://scotch.io/wp-content/uploads/2015/09/IMG_04101.jpg)

We start by asking the user to select a folder containing their photos. This is because even though we could detect their OS and infer what the path to their photos folder is, we can't guarantee that it is where they keep their photos. When the user clicks on the button and selects a folder, the app will display the photos inside of that folder in a grid fashion.

Let's start by fleshing out the initial app screen's user interface first. We will want to add some HTML for the button and label, some CSS to adjust the layout and style of the screen, and some JavaScript to handle events like clicking on a button and loading a folder's photos.

We'll start by adding some HTML for the "select Folder" button in the middle of the 1st app screen. Inside of the body tag, remove the h1 tag, and replace it with the following HTML:

```
<div id="select_folder">
 <span>Select a folder containing photos</span>
</div>
```

With this in place, we now need to modify the layout and styling the HTML with CSS. To do this, add the following link tag inside of the head section of the `index.html` file, after the title tag:

```
<link rel="stylesheet" href="app.css" />
```

We can now add some CSS to the `app.css`, and review the results. In the app.css file, add the following code:

```
body {
 background-color: #222;
 font-family: Arial, "Helvetica Neue"
}

/* 1st screen */

#select_folder {
 text-shadow: 1px 1px 1px rgba(0,0,0,0.3);
 text-align: center;
 margin: 0 auto;
 position: relative;
 width: 16em;
 top: 40%;
 padding: 1em;
 background: #FF582E;
 border-radius: 2em;
 border: solid 1px #FF582E;
 color: white;
}

#select_folder:hover {
 box-shadow: 0px 0px 20px #FF582E;
 cursor: pointer;
}
```

The app is now beginning to look a bit nicer:

![Lens Screen 1 with Styling](https://scotch.io/wp-content/uploads/2015/09/lens-screen-1-with-styling.png)

The next step is to make clicking the button bring up a dialog box to select a folder. For this, we're going to add a bit of HTML, and some JavaScript to achieve the right behaviour. Below the body tag, add the following snippet of HTML:

```
<input type="file" nwdirectory id="folderSelector"/>
```

The input tag is one that you use for selecting files, with the exception that the 'nwdirectory' attribute tells the input file dialog to accept folders instead of files. We insert this into the HTML so that we can programmatically trigger it when the button is clicked, and bring up the folder select dialog. Next, we'll want to add some CSS to hide the input field out of sight:

```
#folderSelector {
 display: none;
}
```

Now, we'll want to add some JavaScript to the app. We'll want to add a script tag to the HTML. Under the link tag in the head section of the `index.html` file, add the following HTML:

```
<script src="app.js"></script>
```

Next, let's create an empty app.js file inside of the lens folder. In here, we will add some JavaScript that does the following:

1. Intercepts any clicks on the div element with the id of "select_folder".
2. When a click occurs, it will programmatically trigger the click event on the input tag, which will load the open folder dialog.
3. If the user cancels selecting a folder, we take no further action.
4. If the user selects a folder, we pass the folder path onto a function that will later on load all of the photos in that folder, and display them in the grid view.

Let's start by writing some code to ask the user for the folder containing their photos. In the lens folder, create an empty file called app.js, and insert the following script tag in the index.html file, in the head section of the html:

```
<script src="app.js"></script>
```

This will load the app.js file when the application loads. Next, we want to add some code in the app.js file that handles asking the user for the folder containing their photos. In the app.js file, add the following code:

```
'use strict';

function bindSelectFolderClick () {
 var button = document.querySelector('#select_folder');
 button.addEventListener('click', function () {
 alert('clicked on the button');
 });
}

// Runs when the browser has loaded the page
window.onload = function () {
 bindSelectFolderClick();
};
```

This initial JavaScript takes care of listening for clicks on the "select folder" button, and for now emits an alert with a message when the button is clicked. Save the file, give it a quick try to check that it works, and now we can bind on opening the folder dialog.

Add this JavaScript above the `bindSelectFolderClick` function:

```
function openFolderDialog () {
 var inputField = document.querySelector('#folderSelector');
 inputField.addEventListener('change', function () {
 var folderPath = this.value;
 alert('Folder path is ' + folderPath);
 }, false);
 inputField.click();
}
```

And in the `bindSelectFolderClick` function, replace this line:

```
alert('clicked on the button');
```

With this:

```
openFolderDialog();
```

Save the file, reload the app, and you'll see that when you click on the button, the select folder dialog will appear. Once you've selected a folder, it will display the folder path in an alert dialog. Getting to this stage means that we are in a position to start finding out what photos exist in that folder, and start to display the photos.

## Finding the photos

Now that we have a folder path, we can begin the process of finding all of the photos inside of that folder, and displaying them in the application. Taking a quick look bak at the user journey, we see that the select folder disappears when we select a folder, and a grid view of the photos in the folder appears in the app. We'll need to do the following:

1. Hide the select folder button
2. Get a list of all the files that are in the folder.
3. Filter out non-images files and directories from the list
4. Show the photo grid view, with each grid item displaying a photo

Before we focus on hiding the select folder button, we'll adjust the existing functions first. Now that we have a folder path, we're going to want to pass that variable onto another function that will handle finding all of the files that exist in that folder. Ideally we want to pass this value to a function that isn't necessarily defined inside of that function, but can be passed to the function when it is called. Let's modify the `openFolderDialog` function's code to look like this:

```
function openFolderDialog (cb) {
 var inputField = document.querySelector('#folderSelector');
 inputField.addEventListener('change', function () {
 var folderPath = this.value;
 cb(folderPath);
 });
 inputField.click();
}
```

The function now receives a variable called `cb`, which is a common abbreviation for `callback`. The "cb" variable is a function that will receive the folderPath variable. This is a good way to be able to have functions pass data onto other functions without having to hard-code function references inside a function, helping to keep function coupling low.

We now need to find places in our code where the `openFolderDialog` function is called, and change them to receive a callback function. Inside of the `bindSelectFolderClick` function there is the only call to that function, so we'll need to adjust that code to look like this:

```
function bindSelectFolderClick (cb) {
 var button = document.querySelector('#select_folder');
 button.addEventListener('click', function () {
 openFolderDialog(cb);
 });
}
```

The `bindSelectFolderClick` function knows explicitly about the `openFolderDialog` function, and will pass on the callback function to it that it receives when it is called. This means that we also need to look at the code inside of the `window.onload` function, and get that to provide a callback function to the `bindSelectFolderClick` function. We adjust the `window.onload` function to look like this:

```
// Runs when the browser has loaded the page
window.onload = function () {
 bindSelectFolderClick(function (folderPath) {
 console.log(folderPath);
 });
};
```

For now we simply log the folder path in the console, but we will later pass a new function that will find all of the files and identify just the ones that are images. Now, we can add a function that will hide the "select folder" button once we've selected a folder. Add the following function before the `window.onload` function:

```
function hideSelectFolderButton () {
 var button = document.querySelector('#select_folder');
 button.style.display = 'none';
}
```

This function will find the div element for the "select folder" button, and hide it via modifying the element's display property. Let's modify the `window.onload` function so that it calls this function when a folder is selected:

```
// Runs when the browser has loaded the page
window.onload = function () {
 bindSelectFolderClick(function (folderPath) {
 hideSelectFolderButton();
 console.log(folderPath);
 });
};
```

If we save the file, and test it out, we can see that the "select folder" button is hidden when a folder is selected, and that we will see the folder path logged in the developer tools of the app. Here, we can now focus on getting the files for that folder path, and start to see the power of NW.js – its ability to load Node.js modules directly in the same place as our JavaScript.

### Getting a list of files

We want to create a function that will receive a folder path, and return a list of files. We can do this using Node.js using the File System module that is part of the Node.js' core API. We need to load the module first, and then call a public function on it called ["readdir"](https://nodejs.org/api/fs.html#fs_fs_readdir_path_callback). At the top of the app.js file, add the following code after the "use strict" linting declaration:

```
// Dependencies
var fs = require('fs');
```

This snippet of code tells the app to load Node.js' File System module. We can then use this module in a function that will find all of the image files in a given folder. In the same file but just before the `window.onload` function, add the following code:

```
function findAllImageFiles (folderPath, cb) {
 fs.readdir(folderPath, function (err, files) {
 if (err) { return cb(err, null); }
 cb(null, files);
 });
}
```

The `findAllImageFiles` function receives a folder path, and currently finds all of the files in that folder and passes them onto the callback function, along with an error in case an error occurs finding files in that folder. If we then modify the `window.onload` function to pass the folder path onto the `findAllImageFiles` function, we can begin to see what kind of data we are dealing with. Alter the `window.onload` function to look like this:

```
// Runs when the browser has loaded the page
window.onload = function () {
 bindSelectFolderClick(function (folderPath) {
 hideSelectFolderButton();
 findAllImageFiles(folderPath, function (err, files) {
 console.log(err);
 console.log(files);
 });
 });
};
```

With the changes saved, we can take a look at what the files list looks like. Run the app using `nw` on the command line, select a folder containing pictures, and if you open the console in the app's developer tools, you should expect to see something like this:

![Step 6 List of Files](https://scotch.io/wp-content/uploads/2015/09/step-6-list-of-files.png)

You can see here that the files in the pictures folder contains only the names of the files, and not the full path to them, nor what kind of file/folder they are. We therefore need a way to do 3 things:

1. Identify their full path
2. Determine if they are an image file or not
3. If the file is an image, add it to a list of files that we'll want to display

We'll need to make the `findAllImageFiles` function a bit smarter. Thankfully there are some tools that we can bring in to assist us with this. For the 1st requirement, we're going to use another one of Node.js' core API modules to handle generating the full file path, as well as identifying image files.

First off, because we're going to rename the `findAllImageFiles` function to just `findAllFiles`, as we're going to split out the filtering of the image files into a separate function, so that we can maintain code readability. Simply do a quick find and replace, and then we can move onto implementing the image filtering function. Once that's done, we want to install a node module via NPM called `mime`. It is a 3rd-party library that will help us to determine the mime type of the files. Run this command on your terminal:

```
npm install mime --save
```

This will install the mime module, and save it as a dependency in the package.json manifest file. Once that is done, we want to require it along with Node.js' path module in the dependencies section of the app.js file. Alter the top section so that it looks like this:

```
// Dependencies
var fs = require('fs');
var mime = require('mime'); 
var path = require('path');
```

With those modules now available in our code, we can turn to implementing the `findImageFiles` function. This function will take a list of files, construct the full file path to them, use the mime library to find out their mime type, and if they're an image, we can add them to a new list, which will then be passed to a callback function. Add a few blank lines after the `findAllFiles` function, and insert the following Array:

```
var imageMimeTypes = [
 'image/bmp',
 'image/gif',
 'image/jpeg',
 'image/png',
 'image/pjpeg',
 'image/tiff',
 'image/webp',
 'image/x-tiff',
 'image/x-windows-bmp'
];
```

This variable contains a list of image mime types that we can display in the app, and will be used to filter files by their mime type. After this, let's insert the following function:

```
function findImageFiles (files, folderPath, cb) {
 var imageFiles = [];
 files.forEach(function (file) {
 var fullFilePath = path.resolve(folderPath,file);
 var extension = mime.lookup(fullFilePath);
 if (imageMimeTypes.indexOf(extension) !== -1) {
 imageFiles.push({name: file, path: fullFilePath});
 }
 if (files.indexOf(file) === files.length-1) {
 cb(imageFiles);
 }
 });
}
```

The `findImageFiles` function handles looking through all of the files, constructing their full path, checking if they are an image, and then collecting the ones that are. We then alter the `window.onload` function to use it like so:

```
// Runs when the browser has loaded the page
window.onload = function () {
 bindSelectFolderClick(function (folderPath) {
 hideSelectFolderButton();
 findAllFiles(folderPath, function (err, files) {
 if (!err) {
 findImageFiles(files, folderPath, function (imageFiles) {
 console.log(imageFiles);
 });
 }
 });
 });
};
```

With these changes saved, we can reload the app from the command line, and see that in the console of the developer tools, we are now returning only the image files, as opposed to all of the files in the folder:

![Step 7 Full Size Render](https://scotch.io/wp-content/uploads/2015/09/step-7-full-size-render.png)

We're now in a position to have these files passed to a function that will display them in a grid.

## Displaying the photos

Displaying the photos in a grid involves adding some HTML to the page for the photo template and display area, a bit of JavaScript to insert the photos into the app screen, and some CSS to apply the layout and styling of the photo items. We'll start with the HTML.

Luckily for us, we're able to use a lot of modern HTML5 and CSS3 APIs to build the app, and so we can make use of the HTML5 template tag to contain the snippet of html that we want to use for each photo. In the index.html, add the following bit of HTML just after the opening body tag:

```
<template id="photo-template">
 <div class="photo">
 <img />
 </div>
</template>
```

The div element with a class of photo contains an image tag, and we'll set the src attribute of that image tag to the photo's file path. The div element surrounding the image tag is there to support rendering the photos in a grid view with set dimensions, and have the photos be displayed in a fashion that respects the aspect ratio of the image.

We'll also need to have a place to store the list of photos in the app screen. Before the closing body tag, add the following bit of HTML:

```
<div id="photos"></div>
```

With the HTML in place, we can now add some JavaScript to append the photos to the page with the template. In the app.js file, we're going to add a function that handles adding an image to the photos area. Before the "window.onload" function, add the following function:

```
function addImageToPhotosArea (file) {
 var photosArea = document.getElementById('photos');
 var template = document.querySelector('#photo-template');
 template.content.querySelector('img').src = file.path;
 template.content.querySelector('img').setAttribute('data-name',file.name);
 var clone = window.document.importNode(template.content, true);
 photosArea.appendChild(clone);
}
```

The `addImageToPhotosArea` function uses JavaScript to select the photos area and the photo template, sets the src attribute of the image tag in the template to photo's file path and the name of the file as a data attribute, and appends the template item to the photos area.

Next, we alter the `window.onload` function and replace the `console.log` function with a call to loop through the imageFiles array, and pass each file to the `addImageToPhotosArea` function, like so:

```
// Runs when the browser has loaded the page
window.onload = function () {
 bindSelectFolderClick(function (folderPath) {
 hideSelectFolderButton();
 findAllFiles(folderPath, function (err, files) {
 if (!err) {
 findImageFiles(files, folderPath, function (imageFiles) {
 imageFiles.forEach(addImageToPhotosArea);
 });
 }
 });
 });
};
```

Finally, we can apply some layout and styling to the photos area and the photo item. In the app.css file, add the following CSS to the end of the file:

```
 #photos {
 padding: 0;
 margin: 0;
 width: 100%;
 height: 100%;
 }

 .photo {
 text-align: center;
 float: left;
 margin: 2%;
 padding: 1% 0.5%;
 background-color: white;
 width: 20%;
 height: 18%;
 margin-bottom: 1%;
 }

 .photo img {
 max-width: 100%;
 max-height: 100%;
 }
```

Also, we want to nullify any of the default margin and padding applied to the body tag by the Chromium browser, so we'll add these 2 rules to the body element:

```
 margin: 0;
 padding: 0;
```

With this layout and styling in place, if we save the changes, reload the app and select a folder to display photos for, we should see something that resembles this (albeit with different photos):

![Step 8 Photo Grid](https://scotch.io/wp-content/uploads/2015/09/step-8-photo-grid.png)

Now the app is beginning to look a bit more than just a barebones skeleton, and we've been able to use Node.js and leverage the NPM ecosystem to install modules that assist with building the application.

From here, we can start to look at being able to click on a photo, and enter full view mode on it.

## Conclusion

This concludes part 1\. Hopefully by now you'll have a strong understanding of getting started with NW.js. In the next tutorial, we'll improve this app and cover the best things to come:

- Displaying a photo in full-view
- **Applying visual filters** to photos
- Saving altered photos
- Packaging the application into a native application that can run on Windows, Mac, and Linux

If you really like NW.js and this tutorial so far, feel free to check-out my book where I cover many of these topics in depth. **Scotch.io readers can get 40% off of the book with discount code: scotch**

[![NW.JS In Action](https://scotch.io/wp-content/uploads/2015/09/nwjs-in-action.png)](http://manning.com/jensen/)
