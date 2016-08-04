# PreviousFree PNG And SVG Location Icons Collection

[Original URL](http://pepsized.com/customize-your-google-map-markers/)

> Hello everybody! In his previous post JoeVains shared with you a pack of location icons. Initially they were designed for the PeHaa THEMES recent premium WordPress theme (YaGa) where they...

Hello everybody! In his [previous post](http://pepsized.com/free-png-and-svg-location-icons-collection/) JoeVains shared with you a pack of location icons. Initially they were designed for the [PeHaa THEMES recent premium WordPress theme (YaGa)](http://wptemplates.pehaa.com/buy-yaga) where they are used as custom Google Maps markers. We decided to extend the Yaga Theme pack and share it with you (free for both personal and commercial use).

This post is for those who do not have much experience with customizing Google maps with Google Maps Javascript API.

I'll show you how to generate a map and add markers using our location icons (or your own location icons if you prefer). We'll discuss improving user experience and browsers inconsistencies. Don't hesitate to download the [.zip folder](http://pepsized.com/wp-content/uploads/2016/01/pepsized_maps_markers.zip) to have a complete version of the code.

## Generating a map

Our main reference is [developers.google.com/maps/documentation/javascript/tutorial](https://developers.google.com/maps/documentation/javascript/tutorial)

If you are just experimenting you do not necessarily need to generate an API key. If you put your map online you may need to control if the requests quota is not exceeded and be able to increase it. With an API key you can also set which web domains are allowed to access your maps – this will protect you from any unauthorized usage. To get it visit [this link.](https://developers.google.com/maps/documentation/javascript/get-api-key)

We will start with a slightly modified example code that you can find in the [Google Maps Api tutorial](https://developers.google.com/maps/documentation/javascript/tutorial). We will center our map in Place de Vosges, Paris, France – our coordinates are `{lat: 48.855510, lng: 2.365505}.`

```
<html>
<head>
 <style>
 html, body {height:100%; margin:0; padding:0;}
 .map {height:100%}
 </style>
</head>
<body>
 <div id="map" class="map desk-three-forth"></div>
 <script type="text/javascript">
 var map;
 function initMap() {
 var myLatLng = {lat: 48.855510, lng: 2.365505};
 map = new google.maps.Map(document.getElementById('map'), {
 zoom: 18,
 center: myLatLng,
 styles: [{"stylers": [{ "saturation": -100 }]}]
 });
 }
 </script>
 <script src="https://maps.googleapis.com/maps/api/js?callback=initMap"></script>
</body>
</html>
```

We zoom in 18 times and we also modify the map styles. Our style modification is one of the simplest possible – basic desaturation.

If you want to play with map styles you should visit [snazzymaps.com](https://snazzymaps.com/) – a community built around creating great looking styles for Google Maps. Using the styles from [snazzymaps](https://snazzymaps.com/) in your website is as easy as copying a bit of code (JSON) and passing it to the [map options](https://developers.google.com/maps/documentation/javascript/styling#styling_the_default_map) of your map, ie. replace the `[{"stylers": [{ "saturation": -100 }]}]`.

**Please note that you have to determine the map dimensions, here we set `height:100%` together with the `body` and `html` heights set to 100%. Otherwise the map will not display.**

## Adding Markers

We will add two markers, they coordinates are: `{lat: 48.856259, lng: 2.365043}` and `{lat: 48.85484, lng: 2.366427}.`<br>
We will use two different svg icons from the [JoeVains pack](http://pepsized.com/free-png-and-svg…icons-collection/). We choose svg because of their vector format assuring flexibility in sizes and a crisp look on retina screens. Using .pngs icons would always work, we [will discuss it later.](http://pepsized.com/customize-your-google-map-markers/#using-pngs)

Let's add the location of PEPSized Coffee to the map (unfortunately, it does not exists in the real life...). We will use the the Coffee_3.svg and display it 64px wide and 64px height.

```
var marker = new google.maps.Marker({
    position: {lat: 48.856259, lng: 2.365043},
    map: map,
    title: 'PEPSized Coffee',
    icon: {
        url: "images/markers/svg/Coffee_3.svg",
        scaledSize: new google.maps.Size(64, 64)
    }
});
```

Now you can replicate this code to add more markers, varying icons, titles and sizes.

We'll organise the code a little bit:

```
var locations = [
    {
        title: 'PEPSized Coffee',
        position: {lat: 48.856259, lng: 2.365043},
        icon: {
            url: "images/markers/svg/Coffee_3.svg",
            scaledSize: new google.maps.Size(64, 64)
        }

    },
    {
        title: 'PEPSized Office',
        position: {lat: 48.85484, lng: 2.366427},
        icon: {
            url: "images/markers/svg/Arrow_1.svg",
            scaledSize: new google.maps.Size(96, 96)
        }
    }                   
];
```

and add the markers with the `forEach` loop:

```
locations.forEach( function( element ) {
    var marker = new google.maps.Marker({
            position: element.position,
            map: map,
            title: element.title,
            icon: element.icon,
        });
    }); 
}
```

## Using SVG sprites

The icon object has a few more attributes that we haven't used so far. They are particularly useful if we use a svg sprite. In our example we use two icons, we could use a sprite as in the image below:

<embed src="http://pepsized.com/wp-content/uploads/2016/01/sprites0-300x57.png%20300w,%20http://pepsized.com/wp-content/uploads/2016/01/sprites0-768x146.png%20768w,%20http://pepsized.com/wp-content/uploads/2016/01/sprites0-1024x194.png%201024w,%20http://pepsized.com/wp-content/uploads/2016/01/sprites0-1140x216.png%201140w,%20http://pepsized.com/wp-content/uploads/2016/01/sprites0-480x91.png%20480w,%20http://pepsized.com/wp-content/uploads/2016/01/sprites0-752x143.png%20752w,%20http://pepsized.com/wp-content/uploads/2016/01/sprites0.png%201504w" class="aligncenter size-full wp-image-2064" width="1504">

 The attributes we will need to properly scale and position each icon are: `size, scaledSize` and `origin`. The `size` attribute corresponds to the size of the marker that will be displayed on the map, `scaledSize` the size of the image we use and `origin` – from which point the image should be displayed, (0,0) being the top left corner.

<embed src="http://pepsized.com/wp-content/uploads/2016/01/sprites-1-300x185.jpg%20300w,%20http://pepsized.com/wp-content/uploads/2016/01/sprites-1-768x474.jpg%20768w,%20http://pepsized.com/wp-content/uploads/2016/01/sprites-1-1024x632.jpg%201024w,%20http://pepsized.com/wp-content/uploads/2016/01/sprites-1-1140x703.jpg%201140w,%20http://pepsized.com/wp-content/uploads/2016/01/sprites-1-480x296.jpg%20480w,%20http://pepsized.com/wp-content/uploads/2016/01/sprites-1-752x464.jpg%20752w,%20http://pepsized.com/wp-content/uploads/2016/01/sprites-1.jpg%201504w" class="aligncenter size-full wp-image-2084" width="1504">

 var locations = [ { title: 'PEPSized Coffee', position: {lat: 48.856259, lng: 2.365043}, icon: { url: "images/markers/svg/Arrows.svg", size: new google.maps.Size(64, 64), scaledSize: new google.maps.Size(128, 64) } }, { title: 'PEPSized Office', position: {lat: 48.85484, lng: 2.366427}, icon: { url: "images/markers/svg/Arrows.svg", origin: new google.maps.Point(96, 0), size: new google.maps.Size(96, 96), scaledSize: new google.maps.Size(192, 96) } } ];

An icon object has also an `anchor` attribute that we are not using in our example but that can be very important. The anchor attribute defines 

<span class="s1">where the icon’s hotspot should be located (based on the scaledSize and origin value). You can see what happens when playing with anchor values in the image below. The red default Google Maps marker indicates the referenced coordinates. The default value of anchor (top left image) is the point in the middle of the icon’s bottom. For our 96px 96px icon it is:</span>

```
anchor: new google.maps.Point(48,96)
```

The (0,0) anchor would mean that the referenced coordinates are indicated by the top left corner of the icon etc.

<embed src="http://pepsized.com/wp-content/uploads/2016/01/anchors-300x207.png%20300w,%20http://pepsized.com/wp-content/uploads/2016/01/anchors-768x529.png%20768w,%20http://pepsized.com/wp-content/uploads/2016/01/anchors-1024x705.png%201024w,%20http://pepsized.com/wp-content/uploads/2016/01/anchors-1140x785.png%201140w,%20http://pepsized.com/wp-content/uploads/2016/01/anchors-480x331.png%20480w,%20http://pepsized.com/wp-content/uploads/2016/01/anchors-752x518.png%20752w,%20http://pepsized.com/wp-content/uploads/2016/01/anchors.png%201504w" class="aligncenter size-full wp-image-2087" width="1504">

## Improving User Experience

It may be very annoying when you want to quickly scroll down a page and instead you keep interacting with a map. That's why we decided to disable the scrolling and dragging behaviour on small screens where the map takes full width. We will also set the initial map zoom parameter depending on the screen width. We will use a small custom [modernizr](http://modernizr.com/download/?-hiddenscroll-mq-setclasses) build. We will use the Modernizr.mq option – that allows to check if the current browser window state matches a media query. We will also detect the hidden scrollbars – overlay scrollbars (when scrollbars on overflowed blocks are visible), common on mobile and OS X.

We include it within the page HEAD element:

```
<head>
    <title>PEPSized Places in Paris</title>
    <meta name="viewport" content="initial-scale=1.0">
    <meta charset="utf-8">
    <link rel="stylesheet" href="style.css" type="text/css" media="all">
    <script src="modernizr.js"></script>
</head>
```

And we modify the piece of code where the map is generated

```
var map,
    desktopScreen = Modernizr.mq( "only screen and (min-width:1024px)" ),
    zoom = desktopScreen ? 18 : 17,
    scrollable = draggable = !Modernizr.hiddenscroll || desktopScreen;

function initMap() {
    var myLatLng = {lat: 48.855510, lng: 2.365505};
    map = new google.maps.Map(document.getElementById('map'), {
        zoom: zoom,
        center: myLatLng,
        mapTypeId: google.maps.MapTypeId.ROADMAP,
        scrollwheel: scrollable,
        draggable: draggable,
        styles: [{"stylers": [{ "saturation": -100 }]}],
    });
...
```

## Using .png icons

You could use the .png icons instead the .svg – the difference would appear on retina screens. If you have your custom .png icon (for example my_icon_48.png) that you want to use as a marker, make sure to have it double sized (for example my_icon_96.png):

```
icon: {
    url: window.devicePixelRatio > 1 ? "my_icon96png" : "my_icon48.png",
    scaledSize: new google.maps.Size(48, 48)
}
```

## Troubleshooting

The main problem with the svg approach is that it does not work in IE11 (it does in IE10 and Edge although).

Here is our workaround – we detect the IE11 browser with javascript and use a png icon instead of a svg.

```
var isIE11 = !!(navigator.userAgent.match(/Trident/) && navigator.userAgent.match(/rv[ :]11/));
var locations = [
    {
        title: 'PEPSized Coffee',
        position: {lat: 48.856259, lng: 2.365043},
        icon: {
            url: isIE11 ? "images/markers/png/Coffee_3.png" : "images/markers/svg/Coffee_3.svg",
            scaledSize: new google.maps.Size(64, 64)
        }

    },
    {
        title: 'PEPSized Office',
        position: {lat: 48.85484, lng: 2.366427},
        icon: {
            url: isIE11 ? "images/markers/png/Arrow_1.png" : "images/markers/svg/Arrow_1.svg",
            scaledSize: new google.maps.Size(96, 96)
        }
    }                   
];
```

## The Demo Layout

For those who might be interested, a few words about our demo layout. This is our markup:

```
<div id="map" class="map desk-three-forth"></div>
<div class="map-legend desk-one-forth">
    <div class="location location-1">
        <div class="location--inner">
            <img src="images/markers/svg/Coffee_3.svg" alt="Coffee Shop icon">
            <!-- or or instead of <img> if we use a sprite
            <span class="marker-icon marker-coffee"></span>
            -->
            <h3>PEPSized Coffee</h3>
            <p>13 Place de PEPS</p>
            <h4>Opening Hours:</h4>
            <p>Opened daily:</p>
            <p>9am - 8pm</p>
        </div>
    </div>
    <div class="location location-2">
        <div class="location--inner">
            <img src="images/markers/svg/Arrow_1.svg" alt="PEPSized Office icon">
            <!-- or instead of <img> if we use a sprite
            <span class="marker-icon marker-office"></span>
            -->
            <h3>PEPSized Office</h3>
            <p>1 Place de PEPS</p>
            <h4>Opening Hours:</h4>
            <p>Tuesday - Saturday</p>
            <p>9am - 8pm</p>
        </div>
    </div>
</div>
```

We use background images for the location divs and an :after pseudo-element to add a semi-transparent overlay:

```
.location {
    background-size:cover;
    background-position:center center;
    position:relative;
    color:white;
}
.location-1 {
    background-color:#554738;
    background-image:url( 'images/coffee_bg.jpg');
}
.location-2 {
    background-color:#383838;
    background-image:url( 'images/office_bg.jpg');
}

.location::after {
    content:"";
    background-color:inherit;
    opacity:.65;
    position:absolute;
    left:0;
    right:0;
    top:0;
    bottom:0;
}
.location--inner {
    position:relative;
    z-index:1;
}
```

We set the width of the map to 75% and float it left. We also use the flexbox property to vertically center the .location–inner divs:

```
@media screen and (min-width: 1024px) { 
    .map, .map-legend {
        height:100%;
    }
    .desk-three-forth {
        float: left;
        width:75%;
    }
    .desk-one-forth {
        width:25%;
    }
    .map-legend, .location {
        display:flex;
        flex-direction:column;
    }
    .location {
        min-height:50%;
        justify-content:center;
        align-items:center  
    }
}
```

Displaying the icons within the location–inner divs seemed absolutely straightforward but... we discovered some strange behavior in Safari. If we modify the dimensions of the svg icon (the same .svg file as being used in a marker), the new dimensions interfere with the icon object size attributes. It happens both for and img element or if background-image is used. The workaround it to use transform:scale instead of modifying the dimensions directly, that's why we have:

```
.location img {
    display:inline-block;
    -webkit-transform:scale(2);
    transform:scale(2); 
}
```

or if using sprites:

```
.marker-icon {
    background-image:url("images/markers/svg/Arrows.svg");
    display:inline-block;
    width:48px;
    height:48px;
    -webkit-transform:scale(2);
    transform:scale(2);
}
.marker-office {
    background-position:48px 0;
}
```

That's all. We hope you got inspired! Thank you very much!

### Terms of use :

You may use the effects demonstrated in tutorials in your own work, both commercial or non-commercial without any attribution. You may not reproduce entire nor large parts of our tutorials. The outcome of our tutorials may not be re-saled nor redistributed.
