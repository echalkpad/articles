# Create An Auto-Centered Responsive Google Map

[Original URL](http://thenewcode.com/359/Create-An-Auto-Centered-Responsive-Google-Map)

> In principle, it's easy to make an embedded Google Map responsive: just remove the height and width attributes that Google supplies in the embed code and substitute appropriate CSS and...

In principle, it's easy to make an embedded [Google Map](http://thenewcode.com/132/Embedding-A-Google-Map-On-A-Web-Page) responsive: just remove the `height` and `width` attributes that Google supplies in the embed code and substitute appropriate CSS and wrapping markup.

However, there's one significant problem: any horizontal window resize cuts off the edge of the map, meaning that it must be moved or rescaled to track back to the original coordinates. However, if you change the approach a little, it's entirely possible to solve the problem with a little [JavaScript](http://thenewcode.com/javascript).

First, rather than using an [`<iframe>`](http://thenewcode.com/628/The-Curious-History-and-Quirky-Nature-of-iFrames) as suggested by the Google Maps embed options, we must construct our own container for the map:

```
<div id="map_container">
 <div id="map"></div>
</div>
```

We need to size and position these elements to take the map we are going to project into it. To do so, we'll use a fairly standard [CSS](http://thenewcode.com/css) pattern:

```
#map_container {
    position: relative;
    padding-top: 50%;
}
#map {
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
}
```

To make the map taller, increase the value of `padding-top`. The `map_container` can be styled in any way you wish (provided with a border, given a percentage width, etc).

To load the map into the [`<div>` elements](http://thenewcode.com/57/Introduction-to-divs) we have made, we use the [Google Maps API](https://developers.google.com/maps/). At the bottom of the page, add:

```
<script src="https://maps.googleapis.com/maps/api/js?v=3.exp&sensor=false"></script>
```

Below this, add a script:

```
function initialize() {
 var myLatlng = new google.maps.LatLng(53.3333,-3.08333),
 mapOptions = {
 zoom: 11,
 center: myLatlng,
 mapTypeId: google.maps.MapTypeId.ROADMAP
 }
var map = new google.maps.Map(document.getElementById('map'), mapOptions),
contentString = 'Some address here..',
infowindow = new google.maps.InfoWindow({
 content: contentString,
 maxWidth: 500
)};

var marker = new google.maps.Marker({
 position: myLatlng,
 map: map
});

google.maps.event.addListener(marker, 'click', function() {
 infowindow.open(map,marker);
});

google.maps.event.addDomListener(window, "resize", function() {
 var center = map.getCenter();
 google.maps.event.trigger(map, "resize");
 map.setCenter(center);
 });
}

google.maps.event.addDomListener(window, 'load', initialize);
```

The result should be what you see at the top of this article. A few important points:

1. The values associated with `google.maps.LatLng` are the `latitude` and `longtitude` of your target. They will be similar to (but may not be exactly the same as) the values you see in the URL for the same location on the Google Maps website. For example:

  ```
  https://www.google.com/maps/place/Times+Square/@40.758895,-73.9873197,17z
  ```

  The first number in this URL is the latitude of the location; the second, longitude. You'll see similar numbers used when you try to embed the location as a Google Map:

  ```
  <iframe src="https://www.google.com/maps/embed?pb=!1m14!1m12!1m3!1
  d3022.1422937950165!2d-73.98731968484512!3d40.75889497932676 width="600" 
  height="450" frameborder="0" style="border:0" allowfullscreen></iframe>
  ```

  In the embed code, the longitude comes first, immediately after the `2d` in the URL, followed by the latitude.

2. `zoom` is the level of zoom for the default appearance of the map. You'll also see that in both URLs, denoted by `z`.

3. `contentString` is the label for the target, which will appear when it is clicked.

There's a great deal more that can be achieved with the Google Maps API, but I'll leave that for future articles.

I must acknowledge the help of [James New](http://codepen.io/jamesnew/) in guiding me to this solution.
