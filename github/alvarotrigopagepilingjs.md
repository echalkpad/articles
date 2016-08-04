# alvarotrigo/pagePiling.js

[Original URL](https://github.com/alvarotrigo/pagePiling.js)

[![preview](https://camo.githubusercontent.com/59a2a5c011cdad9f45883bb69db13b53350a950c/68747470733a2f2f7261772e6769746875622e636f6d2f616c7661726f747269676f2f7061676550696c696e672e6a732f6d61737465722f6578616d706c65732f696d67732f7061676550696c696e672d706c7567696e2e706e67)](https://camo.githubusercontent.com/59a2a5c011cdad9f45883bb69db13b53350a950c/68747470733a2f2f7261772e6769746875622e636f6d2f616c7661726f747269676f2f7061676550696c696e672e6a732f6d61737465722f6578616d706c65732f696d67732f7061676550696c696e672d706c7567696e2e706e67)[![compatibility](https://camo.githubusercontent.com/67c5ed7b37db9b3c14057291969b082794d1dcc8/68747470733a2f2f7261772e6769746875622e636f6d2f616c7661726f747269676f2f7061676550696c696e672e6a732f6d61737465722f6578616d706c65732f696d67732f636f6d70617469626c652e676966)](https://camo.githubusercontent.com/67c5ed7b37db9b3c14057291969b082794d1dcc8/68747470733a2f2f7261772e6769746875622e636f6d2f616c7661726f747269676f2f7061676550696c696e672e6a732f6d61737465722f6578616d706c65732f696d67732f636f6d70617469626c652e676966) Pile your sections one over another and access them scrolling or by URL!

Invite me to a coffee [![Donate](https://camo.githubusercontent.com/24ae43518013d5c9306ddadbbc2fdea234d3522c/68747470733a2f2f7777772e70617970616c6f626a656374732e636f6d2f656e5f55532f47422f692f62746e2f62746e5f646f6e61746543435f4c472e676966)](https://www.paypal.com/cgi-bin/webscr?cmd=_donations&business=atrigol%40gmail%2ecom&lc=ES&item_name=pagePiling%2ejs&item_number=pagePiling&currency_code=USD&bn=PP%2dDonationsBF%3abtn_donateCC_LG%2egif%3aNonHosted)

Customizations of the plugin available upon request for some reasonable price. [Contact me](http://alvarotrigo.com/#contact-page).

Would you like to have a website using pilePage.js functionality but you don't know how to use it? I can do it for you for a reasonable price. [Contact me](http://alvarotrigo.com/#contact-page).

## [](https://github.com/alvarotrigo/pagePiling.js#introduction)Introduction

Suggestion are more than welcome, not only for feature requests but also for coding style improvements. Let's make this a great plugin to make people's lives easier!

## [](https://github.com/alvarotrigo/pagePiling.js#compatibility)Compatibility

pagePiling.js is fully functional on all modern browsers, as well as some old ones such as Internet Explorer 8, 9, Opera 12, etc. It works with browsers with CSS3 support and with the ones which don't have it, making it ideal for old browsers compatibility.

It is also designed to work on toudh devices such as mobile phones or tablets.

## [](https://github.com/alvarotrigo/pagePiling.js#usage)Usage

As you can see in the example files, you will need to include the JavaScript file `jquery.pagepiling.js` (or the minified version `jquery.pagepiling.min.js`) and the css file `jquery.pagepiling.css` of the plugin, as well as [jQuery](http://jquery.com/). Optionally, you can add the [jQuery UI library](http://jqueryui.com/) in case you want to use other easing effects apart from the ones included in the jQuery library which are the `linear` or `swing` effects.

### [](https://github.com/alvarotrigo/pagePiling.js#including-files)Including files:

```
<link rel="stylesheet" type="text/css" href="jquery.pagepiling.css" />

<script src="http://ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>
<script type="text/javascript" src="jquery.pagepiling.js"></script>
```

### [](https://github.com/alvarotrigo/pagePiling.js#required-html-structure)Required HTML structure

Each section will be defined with a `div` containing the `section` class. The active section by default will be the first section, which is taken as the home page.

```
<div id="pagepiling">
 <div class="section">Some section</div>
 <div class="section">Some section</div>
 <div class="section">Some section</div>
 <div class="section">Some section</div>
</div>
```

### [](https://github.com/alvarotrigo/pagePiling.js#initialization)Initialization

All you need to do is call the plugin inside a `$(document).ready` function:

```
$(document).ready(function() {
 $('#pagepiling').pagepiling();
});
```

A more complex initialization with all options set could look like this:

```
$(document).ready(function() {
 $('#pagepiling').pagePiling({
 menu: null,
 verticalCentered: true,
 sectionsColor: [],
 anchors: [],
 scrollingSpeed: 700,
 easing: 'swing',
 loopBottom: false,
 loopTop: false,
 css3: true,
 navigation: {
 'textColor': '#000',
 'bulletsColor': '#000',
 'position': 'right',
 'tooltips': ['section1', 'section2', 'section3', 'section4']
 },
 normalScrollElements: null,
 normalScrollElementTouchThreshold: 5,
 touchSensitivity: 5,
 keyboardScrolling: true,
 sectionSelector: '.section',
 animateAnchor: false,

 //events
 onLeave: function(index, nextIndex, direction){},
 afterLoad: function(anchorLink, index){},
 afterRender: function(){},
 });
});
```

### [](https://github.com/alvarotrigo/pagePiling.js#accesing-sections)Accesing sections

In order to create links to a certain section, you can use a normal URL link if you are using pagePiling.js with anchor links (using the `anchors` option), then you will be able to use anchor links also to navigate directly to a certain section. For example: <http://alvarotrigo.com/pagePiling/#page2>

**Be careful!** `data-anchor` tags can not have the same value as any ID element on the site (or NAME element for IE).

### [](https://github.com/alvarotrigo/pagePiling.js#using-a-menu)Using a menu

To link the menu with the active section you will have to use the `menu` option and make use of anchor links (#) as explained below in the options section.

## [](https://github.com/alvarotrigo/pagePiling.js#options)Options

- `verticalCentered`: (default `true`) Vertically centering of the content within sections.

- `scrollingSpeed`: (default `700`) Speed in milliseconds for the scrolling transitions.

- `sectionsColor`:(default `none`) Define the CSS `background-color` property for each section: Example:

  $('#pagepiling').pagePiling({ sectionsColor: ['#f2f2f2', '#4BBFC3', '#7BAABE', 'whitesmoke', '#000'], });

- `anchors`: (default `[]`) Defines the anchor links (#example) to be shown on the URL for each section. Using anchors forward and backward navigation will also be possible through the browser. This option also allows users to bookmark a specific section. **Be careful!** if you use anchors, they can not have the same value as any ID element on the site (or NAME element for IE).

**Important** It is helpful to understand that the values in the `anchors` option array correlate directly to the element with the class of `.section` by it's position in the markup.

- `easing`: (default `swing`) Defines the transition effect to use for the vertical and horizontal scrolling. It requires the file `vendors/jquery.easings.min.js` or [jQuery UI](http://jqueryui.com/) for using some of its transitions. Other libraries could be used instead.

- `loopTop`: (default `false`) Defines whether scrolling up in the first section should scroll to the last one or not.

- `loopBottom`: (default `false`) Defines whether scrolling down in the last section should scroll to the first one or not.

- `css3`: (default `true`). Defines wheter to use JavaScript or CSS3 transforms to scroll within sections. Useful to speed up the movement in tablet and mobile devices with browsers supporting CSS3\. If this option is set to `true` and the browser doesn't support CSS3, a jQuery fallback will be used instead.

- `normalScrollElements`: (default `null`) If you want to avoid the auto scroll when scrolling over some elements, this is the option you need to use. (useful for maps, scrolling divs etc.) It requires a string with the jQuery selectors for those elements. (For example: `normalScrollElements: '#element1, .element2'`)

- `normalScrollElementTouchThreshold` : (default `5`) Defines the threshold for the number of hops up the html node tree pagePiling will test to see if `normalScrollElements` is a match to allow scrolling functionality on divs on a touch device. (For example: `normalScrollElementTouchThreshold: 3`)

- `keyboardScrolling`: (default `true`) Defines if the content can be navigated using the keyboard

- `touchSensitivity`: (default `5`) Defines a percentage of the browsers window width/height, and how far a swipe must measure for navigating to the next section.

- `animateAnchor`: (default `true`) Defines whether the load of the site when given an anchor (#) will scroll with animation to its destination or will directly load on the given section.

- `menu`: (default `false`) A selector can be used to specify the menu to link with the sections. This way the scrolling of the sections will activate the corresponding element in the menu using the class `active`. This won't generate a menu but will just add the `active` class to the element in the given menu with the corresponding anchor links. In order to link the elements of the menu with the sections, an HTML 5 data-tag (`data-menuanchor`) will be needed to use with the same anchor links as used within the sections. Example:

  - [First section](#firstPage)
  - [Second section](#secondPage)
  - [Third section](#thirdPage)
  - [Fourth section](#fourthPage)

  $('#pagepiling').pagePiling({ anchors: ['firstPage', 'secondPage', 'thirdPage', 'fourthPage', 'lastPage'], menu: '#myMenu' });

**Note:** the menu element should be placed outside the pagePiling wrapper in order to avoid problem when ussing `css3:true`. Otherwise it will be appeneded to the `body` by the plugin itself.

- `navigation`: (default `false`) If set to `true`, it will show a navigation bar made up of small circles.

- `sectionSelector`: (default `.section`) Defines the jQuery selector used for the plugin sections. It might need to be changed sometimes to avoid problem with other plugins using the same selectors as pagePiling.js..

## [](https://github.com/alvarotrigo/pagePiling.js#methods)Methods

### [](https://github.com/alvarotrigo/pagePiling.js#movesectionup)moveSectionUp()

Scrolls one section up:

```
$.fn.pagePiling.moveSectionUp();
```

### [](https://github.com/alvarotrigo/pagePiling.js#movesectiondown)moveSectionDown()

Scrolls one section down:

```
$.fn.pagePiling.moveSectionDown();
```

### [](https://github.com/alvarotrigo/pagePiling.js#movetosection)moveTo(section)

Scrolls the page to the given section.

```
/*Scrolling to the section with the anchor link `firstSection` */
$.fn.pagePiling.moveTo('firstSection');

//Scrolling to the 3rd section in the site
$.fn.pagePiling.moveTo(3, 0);

//Which is the same as
$.fn.pagePiling.moveTo(3);
```

### [](https://github.com/alvarotrigo/pagePiling.js#setallowscrollingboolean)setAllowScrolling(boolean)

Adds or remove the possiblity of scrolling through sections by using the mouse wheel/trackpad or touch gestures (which is active by default).

```
$.fn.pagePiling.setAllowScrolling(false);
```

### [](https://github.com/alvarotrigo/pagePiling.js#setkeyboardscrollingboolean)setKeyboardScrolling(boolean)

Adds or remove the possiblity of scrolling through sections by using the keyboard arrow keys (which is active by default).

```
$.fn.pagePiling.setKeyboardScrolling(false);
```

### [](https://github.com/alvarotrigo/pagePiling.js#setscrollingspeedmilliseconds)setScrollingSpeed(milliseconds)

Defines the scrolling speed in milliseconds.

```
$.fn.pagePiling.setScrollingSpeed(700);
```

## [](https://github.com/alvarotrigo/pagePiling.js#callbacks)Callbacks

### [](https://github.com/alvarotrigo/pagePiling.js#afterload-anchorlink-index)afterLoad (`anchorLink`, `index`)

Callback fired once the sections have been loaded, after the scrolling has ended. Parameters:

- `anchorLink`: anchorLink corresponding to the section.
- `index`: index of the section. Starting from 1.

In case of not having anchorLinks defined in the plugin the `index` parameter would be the only one to use.

Example:

```
 $('#pagepiling').pagePiling({
 anchors: ['firstPage', 'secondPage', 'thirdPage', 'fourthPage', 'lastPage'],

 afterLoad: function(anchorLink, index){
 //using index
 if(index == '3'){
 alert("Section 3 ended loading");
 }

 //using anchorLink
 if(anchorLink == 'secondPage'){
 alert("Section 2 ended loading");
 }
 }
 });
```

### [](https://github.com/alvarotrigo/pagePiling.js#onleave-index-nextindex-direction)onLeave (`index`, `nextIndex`, `direction`)

This callback is fired once the user leaves a section, in the transition to the new section.

Parameters:

- `index`: index of the leaving section. Starting from 1.
- `nextIndex`: index of the destination section. Starting from 1.
- `direction`: it will take the values `up` or `down` depending on the scrolling direction.

Example:

```
 $('#pagepiling').pagePiling({
 onLeave: function(index, nextIndex, direction){
 //after leaving section 2
 if(index == '2' && direction =='down'){
 alert("Going to section 3!");
 }

 else if(index == '2' && direction == 'up'){
 alert("Going to section 1!");
 }
 }
 });
```

### [](https://github.com/alvarotrigo/pagePiling.js#afterrender)afterRender()

This callback is fired just after the structure of the page is generated. This is the callback you want to use to initialize other plugins or fire any code which requires the document to be ready (as this plugin modifies the DOM to create the resulting structure).

Example:

```
 $('#pagepiling').pagePiling({
 afterRender: function(){
 alert("The resulting DOM stucture is ready");
 }
 });
```

## [](https://github.com/alvarotrigo/pagePiling.js#who-is-using-pagepilingjs)Who is using pagePiling.js

If you want your page to be listed here. Please [contact me](mailto:alvaro@alvarotrigo.com) with the URL.

## [](https://github.com/alvarotrigo/pagePiling.js#donations)Donations

Donations would be more than welcome :)

[![Donate](https://camo.githubusercontent.com/24ae43518013d5c9306ddadbbc2fdea234d3522c/68747470733a2f2f7777772e70617970616c6f626a656374732e636f6d2f656e5f55532f47422f692f62746e2f62746e5f646f6e61746543435f4c472e676966)](https://www.paypal.com/cgi-bin/webscr?cmd=_donations&business=atrigol%40gmail%2ecom&lc=ES&item_name=pagePiling%2ejs&item_number=pagePiling&currency_code=USD&bn=PP%2dDonationsBF%3abtn_donateCC_LG%2egif%3aNonHosted)

## [](https://github.com/alvarotrigo/pagePiling.js#license)License

(The MIT License)

Copyright (c) 2014 Alvaro Trigo <[alvaro@alvarotrigo.com](mailto:alvaro@alvarotrigo.com)>

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the 'Software'), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
