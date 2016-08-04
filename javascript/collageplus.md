# CollagePlus

[Original URL](http://ed-lea.github.io/jquery-collagePlus/)

> This plugin for jQuery will arrange your images to fit exactly within a container. You can define the padding between images, give the images css borders and define a target row height. Example: Play...

[](http://ed-lea.github.io/jquery-collagePlus/#jquery-collageplus) This plugin for jQuery will arrange your images to fit exactly within a container. You can define the padding between images, give the images css borders and define a target row height.

## [](http://ed-lea.github.io/jquery-collagePlus/#jsfiddle-example)Example:

![](https://raw.github.com/ed-lea/jquery-collagePlus/master/support/images/0.2.0-preview.png) Play with a live example on jsfiddle: <http://jsfiddle.net/edlea/uZv3n/> or take a look at the [demo](http://ed-lea.github.io/jquery-collagePlus/example.html)

## [](http://ed-lea.github.io/jquery-collagePlus/#basic-usage)Basic Usage

```
 // example HTML image gallery
 <div class="Collage">
 <img src="example1.jpg" />
 <img src="example2.jpg" />
 <img src="example3.jpg" />
 </div>

 // collagePlus-ify it!
 $('.Collage').collagePlus();
```

## [](http://ed-lea.github.io/jquery-collagePlus/#getting-started)Getting Started

```
/* In your CSS */
.Collage{

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old define how much padding you want in between your images */
 padding:10px;

 }

.Collage img{

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old ensures padding at the bottom of the image is correct */
 vertical-align:bottom;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old hide the images until the plugin has run. the plugin will reveal the images*/
 opactiy:0;

 }
```

Ensure you have no whitespace between your image tags for a clean grid.

```
<!-- In your HTML -->
<div class="Collage">
<img src="http://placehold.it/350x150"><img src="http://placehold.it/400x300"><img src="http://placehold.it/290x800">
</div>
```

Alternatively, use the jquery.removeWhitespace.js plugin in the extras directory to do this for you e.g.

```
$('.Collage').removeWhitespace().collagePlus();
```

You may want to run the plugin with an image loader like <https://github.com/desandro/imagesloaded>, alternatively you can try it with

```
$(window).load(function () {
 $('.Collage').collagePlus();
});
```

## [](http://ed-lea.github.io/jquery-collagePlus/#browser-resize)Browser Resize

By default this behaviour is not included in the plugin but you can use a few lines of js to reload the images if the user resizes the browser window

````
function collage() {
 $('.Collage').collagePlus(
 {
 'fadeSpeed' : 2000
 }
 );
};

var resizeTimer = null;
$(window).bind('resize', function() {
 // hide all the images until we resize them
 // set the element you are scaling i.e. the first child nodes of ```.Collage``` to opacity 0
 $('.Collage .Image_Wrapper').css("opacity", 0);
 // set a timer to re-apply the plugin
 if (resizeTimer) clearTimeout(resizeTimer);
 resizeTimer = setTimeout(collage, 200);
});
````

## [](http://ed-lea.github.io/jquery-collagePlus/#notes)Notes

CollagePlus relies on all images being loaded before it can calculate the layout. It does not run off image sizes specified in the DOM. If you have image sizes available in the DOM then you're probably better off calculating the layout server-side (assuming that's where you got the image sizes from) and writing the result directly to the HTML template you're generating.

## [](http://ed-lea.github.io/jquery-collagePlus/#licence)License

Dual licensed under the MIT or GPL Version 2 licenses.<br>
<http://www.opensource.org/licenses/mit-license.php><br>
<http://www.opensource.org/licenses/GPL-2.0>

## Author

<span class="avatar">
  <img src="http://www.gravatar.com/avatar/6e7c89e56a46d1a665ad80ca507a82e4.png" alt="">
</span>

 

<span class="author"> I’m <a href="http://edlea.com">Ed Lea</a>, visual and interaction designer.<br>Twitter: <a href="http://twitter.com/ed_lea">@ed_lea</a></span>
