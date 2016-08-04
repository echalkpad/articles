# jQuery responsive lightbox

[Original URL](http://chocolat.insipi.de/#demo)

> chocolat.js features settings demo

- [<span> chocolat.js </span>](http://chocolat.insipi.de/#home)

- [<span> features </span>](http://chocolat.insipi.de/#features)

- [<span> settings </span>](http://chocolat.insipi.de/#settings)

- [<span> demo </span>](http://chocolat.insipi.de/#demo)

- [<span> getting started </span>](http://chocolat.insipi.de/#getting-started)

- [<span> documentation </span>](https://github.com/nicolas-t/Chocolat/blob/master/readme.md)

- [<span> github </span>](https://github.com/nicolas-t/Chocolat)

- [<span> download </span>](http://chocolat.insipi.de/#download)

[New version (0.4.14) available !](https://github.com/nicolas-t/Chocolat/releases/tag/0.4.14)

Chocolat.js enables you to display one or several images staying on the same page.

The choice is left to the user to group together a series of pictures as a link, or let them appear as thumbnails.

The viewer may appears full-page or in a block.

```
$ bower install chocolat
```

![mec](http://chocolat.insipi.de/images/mec.png)

## <span class="line-1">Open</span>

<span class="line-2">Source</span>

<br>

<span class="line-3">Easy</span>

<br>

<span class="line-4">Free</span>

[](http://chocolat.insipi.de/#features) 01.

### <span class="animated white"> either for noobs </span>

<span class="animated white"> or veterans</span>

Chocolat comes with an API and multiples CSS classes which make it very handy to use for advanced users, and very easy to use for noobs.

[](http://chocolat.insipi.de/#features) 02.

### <span class="animated white">crossbrowser</span>

Chocolat works well with all major browser. It has been tested on : IE7+, firefox, chrome, opera, safari.

[](http://chocolat.insipi.de/#features) 03.

### <span class="animated white">light</span>

Chocolat.js is only 23 kb and reaches 10 kb once minified.

[](http://chocolat.insipi.de/#features) 04.

### <span class="animated white">responsive</span>

Chocolat works on desktop, tablet & mobile, you can also define your own breakpoints.

![](http://chocolat.insipi.de/images/feature-1.png)

Block,<br>
Fullscreen,<br>
FullWindow

container : DOM element

fullScreen : bool

Chocolat can be opened fullwindow (as many other lightboxes) but it can also be opened in a container (a block element in the page).<br>
With recent browser you can also navigate through the images fullscreen.

![](http://chocolat.insipi.de/images/feature-2.png)

Cover,<br>
Contain,<br>
Native

imageSize :<br>
'default', contain', 'cover' or 'native'

You can choose either to crop the image or to make it perfectly fit its parent (be it any kind of block) but it's still quite unclear.

![](http://chocolat.insipi.de/images/feature-3.png)

Loop

loop : bool

Choose to restart at the beginning once you reached the last image.

![](http://chocolat.insipi.de/images/feature-4.png)

Keyboard<br>
Navigation

You can navigate through the images using your keyboard and assign keys.

lol

lol

lol

CONTAINER LIMITS

#### <span class="blue">3 files</span>

needed

[](http://code.jquery.com/jquery-1.11.3.min.js) jQuery library

jquery.js

[](https://raw.githubusercontent.com/nicolas-t/Chocolat/master/dist/js/jquery.chocolat.js) Plugin File

jquery.chocolat.js

[](https://raw.githubusercontent.com/nicolas-t/Chocolat/master/dist/css/chocolat.css) CSS file

chocolat.css

Between head tags

[view file on github](https://gist.github.com/nicolas-t/78d82b0f438ff380d76e)

#### Building a 

<span class="blue">set </span>

##### Create links to your full-sized images.

You can put images thumbnails within those links.

Image captions are adressed to the 

<span>title</span>

 atribute, while the 

<span>data-chocolat-title</span>

 attribute of the parent, correspond to the set title.<br>
Links must have the css class 

<span>chocolat-image</span>

.

Between body tags (example with thumbnails)

[view file on github](https://gist.github.com/nicolas-t/7b57c8a38221263ae9bb)

#### Calling the 

<span class="blue">script </span>

Finally, we call the script that will enable navigation

The call is as follows:<br>

<span>$(’selector’).Chocolat()</span>

If you want to embrace the full power of chocolat [check the documentation](https://github.com/nicolas-t/Chocolat/blob/master/readme.md)

Between script tags

[view file on github](https://gist.github.com/nicolas-t/b79afaefd41e1c258e4c)

#### Page is 

<span class="blue">ready !</span>

If you do not understand the subtleties of the installation,

try using the sample page provided in the archive.

[](https://github.com/nicolas-t/Chocolat/releases/0.4.14) download<br>
chocolat.js

Current version is **0.4.14**<br>
Licensed under a Creative Commons Attribution-ShareAlike 2.0 France.

[](https://github.com/nicolas-t/Chocolat/) fork it<br>
on github

Fork this site on GitHub, improve it, run 

<span>$ gulp test</span>

 and submit a pull request \o/

<script type="text/javascript">
<!--
h='';a='&#64;';n='&#110;&#x69;&#x63;&#x6f;&#108;&#x61;&#x73;&#40;&#x61;&#116;&#x29;&#x69;&#110;&#x73;&#x69;&#112;&#x69;&#46;&#100;&#x65;';e=n+a+h;
document.write('<a h'+'ref'+'="ma'+'ilto'+':'+e+'" clas'+'s="em' + 'ail">'+'&#x43;&#x4f;&#78;&#84;&#x41;&#x43;&#84;'+'<\/'+'a'+'>');
// -->
</script>

<noscript>&#x43;&#x4f;&#78;&#84;&#x41;&#x43;&#84;&#32;&#40;&#110;&#x69;&#x63;&#x6f;&#108;&#x61;&#x73;&#40;&#x61;&#116;&#x29;&#x69;&#110;&#x73;&#x69;&#112;&#x69;&#46;&#100;&#x65;&#32;&#x61;&#116;&#32;&#x29;</noscript>

<br>
[MAKE A DONATION](http://chocolat.insipi.de/#demo)

Plugin developed by<br>
[NICOLAS T.](http://nicolasturlais.com)

website designed by<br>
[maxime chillemi](http://maximechillemi.com)

[](http://chocolat.insipi.de/)

## [chocolat.js](http://chocolat.insipi.de/#home)

### jquery

lightbox<br>
candy

- [features](http://chocolat.insipi.de/#features)
- [settings](http://chocolat.insipi.de/#settings)
- [demo](http://chocolat.insipi.de/#demo)
- [getting started](http://chocolat.insipi.de/#getting-started)
- [download](http://chocolat.insipi.de/#download)
- [documentation ∝](https://github.com/nicolas-t/Chocolat/blob/master/readme.md)
- [github ∝](https://github.com/nicolas-t/Chocolat)

[download](http://chocolat.insipi.de/#download)<br>

<span class="version">version 0.4.14</span>

## [chocolat.js](http://chocolat.insipi.de/#home)

### jquery

lightbox<br>
candy

- [features](http://chocolat.insipi.de/#features)
- [settings](http://chocolat.insipi.de/#settings)
- [demo](http://chocolat.insipi.de/#demo)
- [getting started](http://chocolat.insipi.de/#getting-started)
- [download](http://chocolat.insipi.de/#download)
- [documentation ∝](https://github.com/nicolas-t/Chocolat/blob/master/readme.md)
- [github ∝](https://github.com/nicolas-t/Chocolat)

[download](http://chocolat.insipi.de/#download)<br>

<span class="version">version 0.4.14</span>

## [chocolat.js](http://chocolat.insipi.de/#home)

### jquery

lightbox<br>
candy

- [features](http://chocolat.insipi.de/#features)
- [settings](http://chocolat.insipi.de/#settings)
- [demo](http://chocolat.insipi.de/#demo)
- [getting started](http://chocolat.insipi.de/#getting-started)
- [download](http://chocolat.insipi.de/#download)
- [documentation ∝](https://github.com/nicolas-t/Chocolat/blob/master/readme.md)
- [github ∝](https://github.com/nicolas-t/Chocolat)

[download](http://chocolat.insipi.de/#download)<br>

<span class="version">version 0.4.14</span>

## [chocolat.js](http://chocolat.insipi.de/#home)

### jquery

lightbox<br>
candy

- [features](http://chocolat.insipi.de/#features)
- [settings](http://chocolat.insipi.de/#settings)
- [demo](http://chocolat.insipi.de/#demo)
- [getting started](http://chocolat.insipi.de/#getting-started)
- [download](http://chocolat.insipi.de/#download)
- [documentation ∝](https://github.com/nicolas-t/Chocolat/blob/master/readme.md)
- [github ∝](https://github.com/nicolas-t/Chocolat)

[download](http://chocolat.insipi.de/#download)<br>

<span class="version">version 0.4.14</span>

## [chocolat.js](http://chocolat.insipi.de/#home)

### jquery

lightbox<br>
candy

- [features](http://chocolat.insipi.de/#features)
- [settings](http://chocolat.insipi.de/#settings)
- [demo](http://chocolat.insipi.de/#demo)
- [getting started](http://chocolat.insipi.de/#getting-started)
- [download](http://chocolat.insipi.de/#download)
- [documentation ∝](https://github.com/nicolas-t/Chocolat/blob/master/readme.md)
- [github ∝](https://github.com/nicolas-t/Chocolat)

[download](http://chocolat.insipi.de/#download)<br>

<span class="version">version 0.4.14</span>

## [chocolat.js](http://chocolat.insipi.de/#home)

### jquery

lightbox<br>
candy

- [features](http://chocolat.insipi.de/#features)
- [settings](http://chocolat.insipi.de/#settings)
- [demo](http://chocolat.insipi.de/#demo)
- [getting started](http://chocolat.insipi.de/#getting-started)
- [download](http://chocolat.insipi.de/#download)
- [documentation ∝](https://github.com/nicolas-t/Chocolat/blob/master/readme.md)
- [github ∝](https://github.com/nicolas-t/Chocolat)

[download](http://chocolat.insipi.de/#download)<br>

<span class="version">version 0.4.14</span>

<span class="animated white"> Full window </span>

<span class="animated white"> contain </span>

<span class="animated white"> cover </span>

<span> Container </span>

<span class="animated white"> contain </span>

<span class="animated white"> cover </span>
