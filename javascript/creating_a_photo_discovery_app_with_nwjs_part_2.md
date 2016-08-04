# Creating a Photo Discovery App with NW.js (part 2)

[Original URL](https://scotch.io/tutorials/creating-a-photo-discovery-app-with-nw-js-part-2)

> NW.js (formerly known as Node Webkit) is a framework for creating cross-platform desktop applications using HTML, CSS, and JavaScript. This is part 2 of our tutorial on building a Photo Discovery App...

**NW.js** (formerly known as Node Webkit) is a framework for creating cross-platform desktop applications using HTML, CSS, and JavaScript. This is part 2 of our tutorial on building a Photo Discovery App with NW.js.

In the [previous tutorial](https://scotch.io/tutorials/creating-a-photo-discovery-app-with-nw-js-part-1), we covered:

- Prerequisites
- Getting your computer setup (like installing Node)
- Getting Started
- Finding photos

In this tutorial, we're cover some more cool stuff and wrap-up with packaging the application into a native one!

## Displaying a photo in full-view mode

From the photos being displayed in grid mode, we want to allow a user to be able to click on a photo, and have that photo be displayed in full-view mode, where the entire app screen shows the photo (as much as it can whilst respecting the photo's aspect ratio).

This feature will require a combination of HTML, CSS, and JavaScript. We will begin with the first action in the user journey – clicking on an image, and then work through to displaying the photo in full view mode.

First, we need to modify the "window.onload" function so that once the photos are in the DOM of the HTML, we can call a function to listen to when a photo div element is clicked on, and trigger another function. Alter the code of the `window.onload` function to look like this:

```
 // Runs when the browser has loaded the page
 window.onload = function () {
 bindSelectFolderClick(function (folderPath) {
 hideSelectFolderButton();
 findAllFiles(folderPath, function (err, files) {
 if (!err) {
 findImageFiles(files, folderPath, function (imageFiles) {
 imageFiles.forEach(function (file, index) {
 addImageToPhotosArea(file);
 if (index === imageFiles.length-1) {
 bindClickingOnAllPhotos();
 }
 });
 });
 }
 });
 });
 };
```

We check for when the last imageFile is added to the DOM, and then call a new function called `bindClickingOnAllPhotos`. Before the `window.onload` function is defined, add the following function:

```
 function bindClickingOnAllPhotos () {
 var photos = document.querySelectorAll('.photo');
 for (var i=0;i<photos.length;i++) {
 var photo = photos[i];
 bindClickingOnAPhoto(photo);
 }
 }
```

This function finds all of the photo div elements, loops through them, and then calls another function that will listen for clicking on that individual photo (as you shouldn't define functions within a loop), a function called `bindClickingOnAPhoto`. Before the `bindClickingOnAllPhotos` function, add the following function:

```
 function bindClickingOnAPhoto (photo) {
 photo.onclick = function () {
 console.log(this); // A placeholder until we load full view mode here
 };
 }
```

The `bindClickingOnAPhoto` function receives the photo div element from the `bindClickingOnAllPhotos` function, and attaches a function to execute on it (in this a simple console.log statement as a placeholder for now).

Next, we want to focus on displaying the clicked photo in full view mode. This feature requires a tiny bit of HTML, some CSS, and some JavaScript. Like the photo display feature before, we'll start with the HTML. In the index.html file, add the following snippet of HTML after the div element with the id attribute "photos":

```
<div id="fullViewPhoto">
 <img />
</div>
```

This snippet of HTML will handle displaying the photo in full view mode. Next, we'll add some CSS. In the app.css file, add the following rule for the ".photo" element:

```
 cursor: pointer;
```

This will make sure that the cursor will turn to a hand when it appears over a photo in the grid view, indicating to the user that they can click on it. At the bottom of the CSS file, we'll add these rules for the `#fullPhotoView` element:

```
 #fullViewPhoto {
 text-align: center;
 position: fixed;
 top: 0px;
 left: 0px;
 width: 100%;
 height: 100%;
 background-color: #222;
 z-index: 2;
 display: none;
 }
```

Finally, we'll reuse the CSS rules for the `.photo img` element, and append the `#fullViewPhoto img` element to it, like so:

```
 .photo img, #fullViewPhoto img {
 max-width: 100%;
 max-height: 100%;
 }
```

This ensures that the photo scales up inside of the full photo view, whilst also respecting the photo's aspect ratio and avoiding distortion. Finally, we add some JavaScript to handle displaying the photo in full view mode. In the app.js file, add the following function above the `bindClickingOnAPhoto` function:

```
 function displayPhotoInFullView (photo) {
 var filePath = photo.querySelector('img').src;
 var fileName = photo.querySelector('img').attributes[1].value;
 document.querySelector('#fullViewPhoto > img').src = filePath;
 document.querySelector('#fullViewPhoto > img').setAttribute('data-name', fileName);
 document.querySelector('#fullViewPhoto').style.display = 'block';
 }
```

This function will receive the photo element that is clicked on, extract the image tag's src and data-name attributes, insert them into the image tag that is inside of the `#fullViewPhoto` div element, and then alter the styling of that element to make it visible.

We then need to modify the `bindClickingOnAPhoto` function so that it passes the photo to the new function, like this:

```
 function bindClickingOnAPhoto (photo) {
 photo.onclick = function () {
 displayPhotoInFullView(photo);
 };
 }
```

With these changes saved and the app reloaded, we can now try to click on a photo displayed in the grid view, and see the photo be displayed in full view mode, just like in the screenshot below:

![Step 10 Full View Photo](https://scotch.io/wp-content/uploads/2015/09/step-10-full-view-photo.png)

Nice, now we need to think about how users are going to get back to the grid view from there. We need to implement a toolbar containing a button to click on that will take the user back. In the index.html file, add the following snippet of HTML inside of the `fullViewPhoto` element, after the image tag:

```
<div id="toolbar">
 <div id="back" onclick="backToGridView();">Back</div>
</div>
```

This bit of HTML contains the toolbar, a button with the text "Back", and when it is clicked it will call a JavaScript function called "backToGridView", that will do what the name suggests. Next, we will add some CSS for the layout, styling, and user interactivity. At the bottom of the app.css file, add the following CSS:

```
 #toolbar {
 width: 96%;
 z-index: 2;
 position: absolute;
 top: 0;
 padding: 2%;
 background: rgba(0,0,0,0.7);
 color: white;
 opacity: 0;
 }

 #toolbar:hover {
 opacity: 1;
 }

 #back {
 text-shadow: 1px 1px 1px rgba(0,0,0,0.3);
 background: rgba(255,255,255,0.2);
 border: solid 2px white;
 border-radius: 2em;
 padding: 5px 15px;
 margin: 5px;
 float: right;
 }

 #back:hover {
 background: rgba(255,255,255,0.3);
 box-shadow: 0px 0px 20px rgba(255,255,255,0.6);
 cursor: pointer;
 }
```

For the last step, we will insert the `backToGridView` function into the app.js file, before the `bindClickingOnAPhoto` function:

```
 function backToGridView () {
 document.querySelector('#fullViewPhoto').style.display = 'none';
 }
```

This bit of JavaScript simply changes the display attribute of the `#fullViewPhoto` div element, giving the visual impression of it being taken out of site, and revealing the grid view that has always been there, just not in direct view.

With these changes saved and the app reloaded, when we click on a photo and hover our cursor towards the top of the screen, we can see the toolbar appear, as shown in the screenshot below:

![Step 11 Toolbar](https://scotch.io/wp-content/uploads/2015/09/step-11-toolbar.png)

We'll also notice that when you click on the back button, you are take back to the grid view, allowing users to navigate around. At this stage we've got the exploration and navigation parts of the app covered, but the next thing that we want to achieve is being able to apply Instagram-like filters to the photos, and saving those altered photos back to the computer.

## Applying visual filters to the photos

Now for the fun bit – making some Instagram-esque filters to apply to the photos. For this we're going to make use of a really good library called Caman. It's a browser-based library that allows you to alter different visual aspects of an image (brightness, hue, saturation, contrast, sepia tint, and others). It can enable all kinds of filter effects to be applied to photos, and helps us to not try and re-invent the wheel on such a requirement.

We should grab a copy of the library first. Although it suggests either installing it via npm or bower, I'm going to suggest an alternative approach, and grab a copy of the minified file for distribution. I'll explain a bit later. First, make a new folder inside of the "lens" folder called "scripts", and then download a copy of the minified javascript file listed below to inside that folder:

```
https://raw.githubusercontent.com/meltingice/CamanJS/master/dist/caman.full.min.js
```

The reason why I suggested this is for 2 reasons. The first is that installing it via npm requires some other system libraries to be installed beforehand, and that's a bit of overkill in this case (as we're going to be rendering the photo filter effects in the browser rather than the server side). The second reason is that if we had installed it via bower, then there's a chance that we could run into an issue getting the app to work on Windows. For some weird reason, Windows does not like file paths that are longer than 255 characters, and given the way that dependencies are nested in npm (as bower is an npm module), then we would possibly run into this issue, along with the fact that any files present in the node_modules folder would be loaded by NW.js, and for some large dependencies that can have a noticeable impact on performance.

Now that we've created the script folder and downloaded the CamanJS file to it, let's start playing with the library so we can get a feel for it. In the index.html file, let's include the file in the head section, above the app.js script tag:

```
<script src="scripts/caman.full.js"></script>
```

Next, we turn our attention to the toolbar, the location for where the filter buttons will be. For now, we're just going to create a text button and implement a simple filter, so that we can verify that it all works as we would like. In the index.html file, alter the HTML inside of the "#fullViewPhoto" div element, so that it looks like this:

```
<div id="fullViewPhoto">
 <img id="image"/>
 <div id="toolbar">
 <div id="applyFilter" class="button" onclick="applyFilter();">Apply filter</div>
 <div id="back" class="button" onclick="backToGridView();">Back</div>
 </div>
</div>
```

We've made a couple of changes. Firstly, we've added an id attribute to the image tag, so that CamanJS will be able to easily attach to it and apply the photo effects there. Secondly, we've modified the `#back` div element that was the back button, so that it now has a CSS class of "button", and finally, we've added a new div element for the "Apply filter" button, using the CSS class of "button" as well.

In the app.css file, we're going to make some alterations there to accommodate for the new button in the toolbar, and also to handle the way that CamanJS manipulates images using the HTML5 Canvas API. The first change to make is to append another CSS matching rule onto an existing rule for the canvas tag inside of the `#fullViewPhoto` div element, like so:

```
.photo img, #fullViewPhoto img, #fullViewPhoto canvas {
 max-width: 100%;
 max-height: 100%;
}
```

When CamanJS manipulates an image, it does it using the HTML5 Canvas API, and converts the `img` element into a `canvas` element, so we need to apply the same CSS layout rules for it.

Next, with the introduction of the .button class we need to copy most of the properties of the "#back" CSS rule over to a new rule for the ".button" class. Add the following CSS:

```
 .button {
 text-shadow: 1px 1px 1px rgba(0,0,0,0.3);
 background: rgba(255,255,255,0.2);
 border: solid 2px white;
 border-radius: 2em;
 padding: 5px 15px;
 margin: 5px;
 cursor: pointer;
 }
```

We can then shrink the CSS for the `#back` rule down to just this:

```
 #back {
 float: right;
 }
```

Above that CSS rule, we'd also like to add another rule for the `#applyFilter` div element:

```
 #applyFilter {
 float: left;
 }
```

Then, we remove the `cursor:pointer` property from the `#back:hover` CSS rule, as it is now handled by the button class, and is therefore redundant. Now for the JavaScript. We will need to add a new function called `applyFilter`, and modify the `backToGridView` function so that it will reset the modified DOM of the `#fullViewPhoto` div element back to how it was before any filters were applied.

Before the `backToGridView` function in the app.js file, add the following function:

```
 function applyFilter () {
 Caman('#image', function () {
 this.brightness(10);
 this.sepia(20);
 this.saturation(30);
 this.render();
 }); 
 }
```

This function (which is called when the apply filter button is clicked) will call CamanJS on the `img` element with the id attribute "image", apply some image aspect changes, and render the final result to the DOM. Next, we need to alter the `backToGridView` function to this:

```
 function backToGridView () {
 var canvas = document.querySelector('canvas');
 var image = document.createElement('img');
 image.setAttribute('id','image');
 canvas.parentNode.removeChild(canvas);
 var fullViewPhoto = document.querySelector('#fullViewPhoto');
 fullViewPhoto.insertBefore(image, fullViewPhoto.firstChild);
 document.querySelector('#fullViewPhoto').style.display = 'none';
 }
```

The `backToGridView` function resets any changes made to the DOM when applying filters to the photo. If you save the changes to the JS file and reload the application, select a folder of photos, click on one, and then click on the "apply filter" button in the top left, then you should see something like this:

![Step 12 Apply Filter](https://scotch.io/wp-content/uploads/2015/09/step-12-apply-filter.png)

This is where the value of the app comes through – being able to browse photos, apply filters to them, and then save those modified photos. Before we get onto saving the photos, we want to be able to apply other filter styles.

### Creating the filters

For the app, we're going to allow the user to apply a small number of filters to their photos. These filters will be displayed in the toolbar as images that represent a preview of what the filter's effect is on the original photo. When a user clicks on an image of a filter effect, it is applied to the photo.

We're going to need to create a folder called "images" inside of the Lens folder, and then inside of the new images folder, we need to save some PNG images inside of it for the filter effect buttons. Download the images listed below:

```
https://raw.githubusercontent.com/paulbjensen/lens-photo-app/master/images/original.png
https://raw.githubusercontent.com/paulbjensen/lens-photo-app/master/images/grayscale.png
https://raw.githubusercontent.com/paulbjensen/lens-photo-app/master/images/sepia.png
https://raw.githubusercontent.com/paulbjensen/lens-photo-app/master/images/sunburst.png
https://raw.githubusercontent.com/paulbjensen/lens-photo-app/master/images/port.png
```

Once those files are saved in the images folder, we can move onto modifying the HTML to incorporate the new buttons. Inside the `#toolbar` div element, we'll remove the `#applyFilter` div element, and replace it with the following HTML:

```
<div id="filterLabel">Filters</div>
<div class="filter" onclick="applyFilter('original');" title="original">
 <img src="images/original.png" alt="original" />
</div>
<div class="filter" onclick="applyFilter('grayscale');" title="grayscale">
 <img src="images/grayscale.png" alt="grayscale"/>
</div>
<div class="filter" onclick="applyFilter('sepia');" title="sepia">
 <img src="images/sepia.png" alt="sepia"/>
</div>
<div class="filter" onclick="applyFilter('sunburst');" title="sunburst">
 <img src="images/sunburst.png" alt="sunburst"/>
</div>
<div class="filter" onclick="applyFilter('port');" title="port">
 <img src="images/port.png" alt="port"/>
</div>
```

The toolbar contains a set of div element with images inside representing the various photo filters, and when each div element is clicked on, it will call the `applyFilter` function, along with the name of the filter effect that we want to apply. Next, we'll make some layout and styling changes in the CSS file. First, remove the now redundant CSS rule for the `#applyFilter` div element, and insert the following CSS in its place:

```
 #filterLabel {
 padding: 0.5em;
 float: left;
 }

 .filter img {
 float: left;
 width: 45px;
 height: 30px;
 cursor: pointer;
 border: solid 1px white;
 margin: 0em 0.25em;
 }
```

The CSS rules above help to position the filter label and the filter effect images in the position that we want. Finally, we change the "#back:hover" CSS rule to this instead:

```
 .button:hover, .filter img:hover {
```

This way, we can give a visual cue of interactivity to the filter effect buttons as well as any other buttons. Now for the JavaScript that handles applying the filter effects. Above the "applyFilter" function, insert the following JavaScript in the app.js file:

```
 var filters = {
 original: function () {},

 grayscale: function (item) {
 item.saturation(-100);
 item.render();
 },
 sepia: function (item) {
 item.saturation(-100);
 item.vibrance(100);
 item.sepia(100);
 item.render();
 }, 
 sunburst: function (item) {
 item.brightness(21);
 item.vibrance(22);
 item.contrast(11);
 item.saturation(-18);
 item.exposure(18);
 item.sepia(17);
 item.render();
 },
 port: function (item) {
 item.vibrance(49);
 item.hue(6);
 item.gamma(0.6);
 item.stackBlur(2);
 item.contrast(11);
 item.saturation(19);
 item.exposure(2);
 item.noise(2);
 item.render();
 }
 };
```

We have a filters Object, containing a list of named functions that execute the filter effects. They are passed the CamanJS instance, and run a number of image manipulation commands on them. We then use these in the `applyFilter` function, which is modified to call them like so:

```
 function applyFilter (filterName) {
 Caman('#image', function () {
 this.reset();
 filters[filterName](this);
 });
 }
```

The CamanJS instance will reset any previously-applied effects for a start, and then name of the filter passed by the div element in the html corresponds with the name of the filter in the filters object.

With those changes saved and and the app reloaded, we should now be able to try out different filter effects on our photos, like in the screenshot below:

![Step 13 Multiple Photo Filters](https://scotch.io/wp-content/uploads/2015/09/step-13-multiple-photo-filters.png)

This is where we get oh so close to the final product, but for one more feature – being able to save the modified photo back to the computer.

## Saving the altered photo

The final feature to add to the app before the final touches is to enable the user to save the altered photo onto their computer. Although CamanJS does have a API method for saving the file to your computer, unfortunately it does not work in combination with NW.js, so we're going to have to do a bit more work to implement this feature.

What we're going to do to implement this feature instead is the following:

1. Use an input tag with the attribute "nwsaveas" to bring up a save file dialog, and populate that dialog with the name of the file.
2. Use the HTML5 Canvas API to extract the photo's data, and prepare for being saved to a file.
3. Use Node.js' FileSystem API to receive the file name and file path chosen by the user, and save the data of the photo to there.

We will start by adding the HTML needed to trigger the file save dialog as well as the HTML for the "Save as" button in the toolbar. In the index.html file, add the following line of HTML before the input tag with the id attribute of "folderSelector":

```
 <input type="file" nwsaveas id="photoSaver"/>
```

NW.js provides some custom HTML input tag attributes for opening files/folders, as well as saving files. As we need to save a file, we'll use the "nwsaveas" attribute. Next, for the "Save as" button, add the following line of HTML after the div element with the id attribute "back":

```
 <div id="save" class="button" onclick="saveToDisk();">Save as</div>
```

The "Save as" button will call a JavaScript function called `saveToDisk()`, which we will implement later on. Next, we need to make sure that these new HTML elements are styled properly. The input tag needs to be hidden (as users won't click on it directly), and the "Save as" button needs to be displayed to the left of the back button. For the `#photoSaver` input tag, add the `#photoSaver` rule to the existing rule for the `#folderSelector` element, like this:

```
 #folderSelector, #photoSaver {
 display: none;
 }
```

This ensures that the `#photoSaver` element is hidden from the user interface. For the `#save` div element, we will do the same thing and append its rule to the existing rule for the "#back" element:

```
 #back, #save {
 float: right;
 }
```

Even though the `#save` element comes after the `#back` element in the HTML, because both are floating to the right, the "Save as" button will be displayed to the left of the "Back" button. Now, we need to implement the `saveToDisk` function in the app.js file.

The way that the JavaScript will work is this:

- We add an event listener on when the input tag with the id attribute "photoSaver" changes its value (when the user choose a file name and location for the photo to save from the save as dialog). This will need to be called once when the app is initialised, and the function inside will handle saving the photo data with the file name to the computer.
- We add an empty variable called "photoData" that will allow us to dynamically set the photo data that needs to be saved to the computer.
- We add a function called "saveToDisk" that will get the photo data that is currently loaded, and set the photoData variable to store that data. It will then trigger the save as dialog box by finding the input dialog, and programmatically triggering a click event on it.

After the dependencies at the top of the app.js file, add the following line of JS:

```
 var photoData = null;
```

This variable will help to store the raw base64 data of the photo for saving to the computer. Next, we want to add a function for binding on the `#photoSaver` input tag being clicked. Add the following JS after the `applyFilter` function:

```
 function bindSavingToDisk () {
 var photoSaver = document.querySelector('#photoSaver');
 photoSaver.addEventListener('change', function () {
 var filePath = this.value;
 fs.writeFile(filePath, photoData, 'base64', function (err) {
 if (err) { alert('There was an error saving the photo:',err.message); }
 photoData = null;
 });
 });
 }
```

This binding function listens on when the "save as" dialog receives a file name and path to save the photo to (filePath). Using Node.js' File System API, it accesses the photo data, and writes the photo's data to the file with the given name and path. It then does a bit of memory cleanup afterwards.

We then need to modify the `window.onload` function so that this function is bound on startup. Add the following line after the `bindClickingOnAllPhotos();` line inside of the `window.onload` function:

```
 bindSavingToDisk();
```

Now, after the `bindSavingToDisk` function back higher up in the code, add the following function:

```
 function saveToDisk () {
 var photoSaver = document.querySelector('#photoSaver');
 var canvas = document.querySelector('canvas');
 photoSaver.setAttribute('nwsaveas','Copy of ' + canvas.attributes['data-name'].value);
 photoData = canvas.toDataURL('image/png').replace(/^data:image\/(png|jpg|jpeg);base64,/, '');
 photoSaver.click();
 }
```

This function does 2 things; first, it sets the name of the `nwsaveas` attribute to a "Copy of" plus the photo's name, so that the user has a pre-filled name suggestion for the name of the file in the save as dialog. It then sets the photoData to store the base64-encoded data for the photo, so that the bound function can access it. Finally, it triggers the "save as" dialog programmatically by calling "click" on the `#photoSaver` input tag.

After all that, give it a spin. You'll see now when you go to a photo, apply a filter, and click "Save as" in the toolbar, you'll be presented with a "save as" dialog and the name of the file to save to your computer, like in the screenshot below:

![Step 14 Saving File to Disk](https://scotch.io/wp-content/uploads/2015/09/step-14-saving-file-to-disk.png)

Now that we've got here, there's a bit of tidying up to do before we package the app for distribution:

## Tidying up

### Fixing a bug

Before we put the bow and ribbon on the app, we need to do a few things to get it polished. Firstly, I noticed in playing around with the app that a little bug had crept in where if I clicked on a photo, made no filter changes, and clicked "back", then it wouldn't work. The issue lies with how we assume that there will always be a filter applied in the `backToGridView` function inside of the app.js file. Alter the function to look like this:

```
 function backToGridView () {
 var canvas = document.querySelector('canvas');
 if (canvas) {
 var image = document.createElement('img');
 image.setAttribute('id','image');
 canvas.parentNode.removeChild(canvas);
 var fullViewPhoto = document.querySelector('#fullViewPhoto');
 fullViewPhoto.insertBefore(image, fullViewPhoto.firstChild); 
 }
 document.querySelector('#fullViewPhoto').style.display = 'none';
 }
```

We wrap the code that would cleanup after applying any CamanJS filter effects inside of an if function. If a filter effect has been applied, then there will be a canvas tag to clean up, otherwise, we can simply continue on.

### Improving the app's visual performance

You might notice that when you load the a folder with many photos, the images can take a long time to load. That is because all of the photos are being loaded at the same time, and if you have a large number of photos to load, then this problem becomes noticeable.

One of the options for tackling this is to use lazy loading for the images. The idea is that those images which are in the visible area of the application will be loaded, and the rest won't be until they appear in the visual area. This means that even if we have a folder containing thousands of photos, it will initially load the 12 photos that are visible in the area, and then start to show the rest as they become visible.

For this, we're going to use a JavaScript library called "Echo" by Todd Motto. This library handles image lazy loading for us, and all we need to do is to integrate it into the right places in our app's code.

First, get a copy of Echo's minified javascript [echo.min.js](https://github.com/toddmotto/echo/raw/master/dist/echo.min.js) from [the github repository](https://github.com/toddmotto/echo), and put the file in the scripts folder. Next, we want to include the script in our index.html file, by placing the following script tag in our head section (before the app.js script include):

```
 <script src="scripts/echo.min.js"></script>
```

With the library included, we can now modify the app.js file in 2 places to make use of this library. Echo works by inspecting image tags that have a 'data-echo' attribute in them, and loads the value of that attribute in place of the image tag's src attribute. In the addImageToPhotosArea of the app.js file, change the function so that it looks like this:

```
 function addImageToPhotosArea (file) {
 var photosArea = document.getElementById('photos');
 var template = document.querySelector('#photo-template');
 template.content.querySelector('img').src = 'images/blank.png';
 template.content.querySelector('img').setAttribute('data-echo', file.path);
 template.content.querySelector('img').setAttribute('data-name',file.name);
 var clone = window.document.importNode(template.content, true);
 photosArea.appendChild(clone);
 }
```

We've changed the function so that instead of setting the image's src attribute to the file path for the photo, we set the 'data-echo' attribute instead, and make the src attribute load a blank png image. Echo will identify the data-echo attributes in the image tags, and load the images when they appear in the visible viewport of the application.

In order to make Echo identify these image tags and lazy-load them, it will need to be initialised. In the app.js file's window.onload function, we add a call to Echo's init function, and it's render function, like so:

```
 window.onload = function () {

 echo.init({
 offset: 0,
 throttle: 0,
 unload: false
 });

 bindSelectFolderClick(function (folderPath) {
 hideSelectFolderButton();
 findAllFiles(folderPath, function (err, files) {
 if (!err) {
 findImageFiles(files, folderPath, function (imageFiles) {
 imageFiles.forEach(function (file, index) {
 addImageToPhotosArea(file);
 if (index === imageFiles.length-1) {
 echo.render();
 bindClickingOnAllPhotos();
 bindSavingToDisk();
 }
 });
 });
 }
 });
 });
 };
```

The Echo library is initialised when the DOM is loaded, and once all of the photo image have been added into the DOM, we call Echo's render function to automatically trigger the lazy-loading of images that are already in the visible area of the application. We also add a 1×1 PNG to the images folder, called blank, which is there to be loaded by the image initially.

With the changes saved, reload the application, and you should now notice that the images load quicker in the visible area, and that the app is that bit snappier for it.

### Viewing other photo folders

When you play with the application enough, you begin to realise that there is one remaining flaw in the user experience – you can't browse another folder without closing the application down and reopening it. Luckily we can resolve this pretty easily, and do it by using one of NW.js UI APIs – the application window menu. What we want to do is implement a single application window menu item that will allow the user to load another folder containing photos. This will repurpose some of the code we created for loading the app when a folder is selected, and will use some more code to take care of the new functionality.

The first thing that we will need to do is to make sure that NW.js' GUI module is loaded into the app. In the dependencies section of the app.js file, add the following line of code:

```
 var gui = require('nw.gui');
```

This loads NW.js' GUI module, so that we can load the Menu API. Next, we want to write some code that will be able to handle the way that NW.js handle Menus for application windows. Menus in NW.js have different API methods for Windows/Linux and Mac OS X – this is because application window menus in Mac OS X are displayed at the top of the screen, and not nested under each application window, unlike Windows and Linux applications.

For this, we're going to write a function called "loadMenu", which will take care of handling which menu type to load, based on the user's Operating System. We will call this function when the user first loads a folder. In the window.onload function at the end of the app.js file, adjust it to this:

```
 window.onload = function () {

 echo.init({
 offset: 0,
 throttle: 0,
 unload: false
 });

 bindSelectFolderClick(function (folderPath) {
 loadMenu();
 hideSelectFolderButton();
 findAllFiles(folderPath, function (err, files) {
 if (!err) {
 findImageFiles(files, folderPath, function (imageFiles) {
 imageFiles.forEach(function (file, index) {
 addImageToPhotosArea(file);
 if (index === imageFiles.length-1) {
 echo.render();
 bindClickingOnAllPhotos();
 bindSavingToDisk();
 }
 });
 });
 }
 });
 });
 };
```

We've added a new function called "loadMenu", and now we need to flesh it out. Above the window.onload function, add a new function called "loadMenu", with the following code:

```
 function loadMenu () {
 var menuBar = new gui.Menu({type:'menubar'});
 var menuItems = new gui.Menu();

 menuItems.append(new gui.MenuItem({ label: 'Load another folder', click: loadAnotherFolder }));

 var fileMenu = new gui.MenuItem({
 label: 'File',
 submenu: menuItems
 });

 if (process.platform === 'darwin') {

 // Load Mac OS X application menu
 menuBar.createMacBuiltin('Lens');
 menuBar.insert(fileMenu, 1);

 } else {

 // Load Windows/Linux application menu
 menuBar.append(fileMenu, 1);

 }

 gui.Window.get().menu = menuBar;

 }
```

This code does a number of things; firstly, it instantiates some objects that are needed for constructing an application window menu, such as the menubar, the menu containing the action items (in this case just one action item for loading another folder), and the File Menu for the menu bar, to contain the 1 action item.

We then use Node.js' process.platform API to determine what Operating System the user is running. In this case, we want to identify if the user is running Mac OS X or not. If they are, then we call NW.js' API for loading Mac OS X's own menu and insert the File menu into it, and if they're running something else, then we call NW.js' API and append the File menu to the menu bar. After this, we attach the menu bar to the application window.

You'll also notice in the code that one of the menu items (Load another folder) will call a function called "loadAnotherFolder" when it is clicked on. We haven't yet fleshed out this function, so we will need to do that next. Above the `loadMenu` function that we added, we will create a function called `loadAnotherFolder`, and put the following code inside of it:

```
 function loadAnotherFolder () {
 openFolderDialog(function (folderPath) { 
 findAllFiles(folderPath, function (err, files) {
 if (!err) {
 clearArea();
 echo.init({
 offset: 0,
 throttle: 0,
 unload: false
 });
 findImageFiles(files, folderPath, function (imageFiles) {
 imageFiles.forEach(function (file, index) {
 addImageToPhotosArea(file);
 if (index === imageFiles.length-1) {
 echo.render();
 bindClickingOnAllPhotos();
 bindSavingToDisk();
 }
 });
 });
 }
 });
 });
 }
```

This function essentially repurposes some of the code that exists in the window.onload function, but with some subtle differences. The function will trigger the selector folder dialog, but when it has a folder and has scanned the files that exist in that folder, what it will do instead is clear the photos that were displayed in the previously-selected folder away, make sure that echo is initialised for when the photos from the newly-selected folder are added in place, and then adds the new photos to the application. Isolate the differences between this function and the window.onload function, and there is a small refactoring opportunity to be had.

In order to make the function clear the photos of the previously-selected folder, we need to implement another function to handle clearing them, called "clearArea". Above the "loadAnotherFolder" function, add the following code:

```
 function clearArea () {
 document.getElementById('photos').innerHTML = '';
 }
```

This function finds the #photos div, and simply sets the innerHTML to an empty string, removing all of the nested div elements inside of it, which are the photos from the previously-selected folder.

Reload the app, and you should now see that when you select a folder, the app should display an application menu, with the option under the File menu to load another folder, as shown in the screenshot below:

![App with Menu](https://scotch.io/wp-content/uploads/2015/09/app-with-menu.png)

Voila, you can now load another folder within the app, helping to close off an annoying UX fail that the app previously had.

### Hiding the developer tools toolbar

Next, we want to hide the developer tools toolbar at the top of the app window. In the package.json file, we can add a bit of JSON to handle the display of that toolbar. After the "dependencies" section, add the following snippet of JSON:

```
 "window": {
 "toolbar": false
 }
```

NW.js is able to configure the behaviour of the window based on the properties in the package.json file. Here, we are telling the window to not load the toolbar. Save this file, reload the app from the command line, and now we should see this:

![Step 15 Window Toolbar](https://scotch.io/wp-content/uploads/2015/09/step-15-window-toolbar.png)

With these changes in place, we can now think about how to get the app packaged as binaries for multiple Operating Systems.

## Packaging the app

In order to package the app, we also need to create an icon for the app as well, and package it so that the apps builds will have it. In this case, I've created one for the app, shown below:

![Lens App](https://scotch.io/wp-content/uploads/2015/09/lens.png)

Take a copy of the file, and save it as "lens.png" inside of the lens folder. Next, we want to reference the icon in the package.json file. Add the following snippet of JSON to the package.json file, after the "toolbar: false" setting:

```
 "icon":"lens.png"
```

With that saved, we can now package the app for multiple operating systems. For this, we will use a tool called "nwbuilder", which simplifies the task. Run the following command on your command line terminal:

```
 npm install -g nw-builder
```

This will install the nw-builder tool as a global dependency on your computer. Next, we want to run the tool to build the app. Next, change to the parent directory that contains the lens folder, and run the following command on your terminal:

```
 nwbuild -p win32,win64,osx32,osx64,linux32,linux64 lens
```

This will go ahead and build binaries of the lens app for Windows, Mac and Linux, with both 32-bit and 64-bit compatible versions for each. Once the process has finished, there will be a build folder, and inside of it there will be another folder with the app name "Lens", and inside of that will be 6 folders, two each for Windows, Mac and Linux (with the 32-bit and 64-bit versions).

If you go ahead and browse for the application that works on our operating system, we can double-click on it, and it will run as a standalone application.

## Application Icons

Unfortunately nw-builder does not set the application icon for the Mac OS X applications, and so in this case we have to do it manually. That said, the good news is that changing an application icon in Mac OS X is very easy to do, but we need to make sure that the application icon is in the right file format.

On Mac OS X, application icons use the .icns file format. There are a number of applications out there for creating .icns files, and the one that I recommend using is [iconvert icons](http://iconverticons.com). You can either use the website, or purchase the desktop app from either the Mac App Store or Windows App Store (if you're using a PC for this tutorial).

We'll need to create a Mac OS icns file as opposed to a Mac OS folder icon. Once we've created this, we can change the application icon for the Mac OS X builds of the Lens app.

If you navigate to one of the Mac OS X apps inside of the build folder, right click on the app to bring up the context menu, and select "Get Info". This will bring up a window displaying some of the details about the application (when it was created, how big the file size is, and other details). In the top left of the window you will see the application's icon (as per the screenshot).

![Step 16 Change App Icon](https://scotch.io/wp-content/uploads/2015/09/step-16-change-app-icon.png)

Drag the lens.icns file that we generated from iConvert Icons into the area displaying the icon in the "Get Info" window, and this will change the application's icon to use the lens.icns file. If we now double-click on the app and run it, we will see that the application uses the new app icon (as opposed to NW.js' icon).

If you run the application on Windows, you should see something like this:

![Lens on Windows v2](https://scotch.io/wp-content/uploads/2015/09/lens-on-windows-v2.png)

We now have an app that we can be given to people to use on their computers.

## Conclusion

The major takeaway from this tutorial is to show you that if you have web design skills and know your way around JavaScript, then it won't take much for you to use those skills to create a desktop application, especially one that can be built for Mac OS X, Windows, and Linux. Not only that, but as a desktop app that can access the user's file system and interact with files, you have a greater range of possible apps that you can build, beyond what can be achieved with just a web application.

[![NW.JS In Action](https://scotch.io/wp-content/uploads/2015/09/nwjs-in-action.png)](http://manning.com/jensen/)

If you are interested in finding out more about NW.js, you can checkout the [web site](http://nwjs.io). Also, I'm writing a book for Manning called ["NW.js in Action"](http://manning.com/jensen), where you get to find out not just how NW.js works and how to build desktop applications with it, but also how to test those applications, debug performance issues, and prepare your app for selling on App Stores. It's available now via the MEAP program, and will be published Spring 2016\. **Scotch.io readers can get 40% off of the book with discount code: scotch**

If you have any queries about the app or the article, my email is [paulbjensen@gmail.com](mailto://paulbjensen@gmail.com). Hope you enjoyed the tutorial, and thanks for reading.

### Appendix

*In the old days it was using Webkit and Node.js, but switched to the forks of those projects, hence the renaming of the project from Node Webkit to NW.js.
