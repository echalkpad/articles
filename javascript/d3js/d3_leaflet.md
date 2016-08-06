# D3 + Leaflet

[Original URL](https://bost.ocks.org/mike/leaflet/)

> © OpenStreetMap contributors This is a quick demonstration of using D3 to render GeoJSON shapes in conjunction with Leaflet. The implementation is straightforward, but Leaflet is regretfully...

[© OpenStreetMap contributors](https://www.openstreetmap.org/copyright) This is a quick demonstration of using [D3](http://d3js.org) to render GeoJSON shapes in conjunction with [Leaflet](http://leafletjs.com/). The implementation is straightforward, but Leaflet is ~~regretfully missing [documentation](http://leafletjs.com/reference.html) and [examples](http://leafletjs.com/examples.html) for custom overlays~~, [EDIT: the documentation has since been updated and there is now a helpful section titled "Implementing Custom Layers"] so hopefully this will help you get started.

## [#](https://bost.ocks.org/mike/leaflet/#init)Initializing the Map and SVG Overlay

We'll start with a basic map using OpenStreetMap tiles:

```
var map = new L.Map("map", {center: [37.8, -96.9], zoom: 4})
 .addLayer(new L.TileLayer("http://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"));
```

The map is placed inside a paragraph (`p`) element with the id "map". An associated stylesheet specifies the dimensions:

```
#map {
 width: 960px;
 height: 500px;
}
```

Next, we add an SVG element to Leaflet's _overlay pane_. Leaflet automatically repositions the overlay pane when the map pans. Note that the SVG element is initialized with no width or height; the dimensions must be set dynamically because they change on zoom. More on that in a bit.

```
var svg = d3.select(map.getPanes().overlayPane).append("svg"),
 g = svg.append("g").attr("class", "leaflet-zoom-hide");
```

Inside the SVG, you'll also need a G (group) element. This will be used to translate the SVG elements so that the top-left corner of the SVG, ⟨0,0⟩, corresponds to Leaflet's layer origin. The `leaflet-zoom-hide` class is needed so that the overlay is hidden during Leaflet's zoom animation; alternatively, you could disable the animation using the `zoomAnimation` option when constructing the map.

## [#](https://bost.ocks.org/mike/leaflet/#project)Loading and Projecting GeoJSON

Next, we load the GeoJSON file using [d3.json](https://github.com/mbostock/d3/wiki/Requests#wiki-d3_json):

```
d3.json("us-states.json", function(error, collection) {
 if (error) throw error;

 // code here
});
```

The load is asynchronous, so the remaining code in this example is within the callback function. The passed `collection` is the contents of the `us-states.json` file: a single FeatureCollection with features for each of the 50 United States and Puerto Rico.

D3 and Leaflet use different APIs for rendering shapes and projecting points. Fortunately, it's easy to adapt Leaflet's API to fit D3 by implementing a custom geometric transformation. A _transform_ converts an input geometry (such as polygons in spherical geographic coordinates) to a different output geometry (such as polygons in projected screen coordinates). Using [d3.geo.transform](https://github.com/mbostock/d3/wiki/Geo-Streams#wiki-transform), it can be implemented as a simple function that projects individual points:

This code adapts Leaflet's `latLngToLayerPoint` and `LatLng` methods to project a single point, passing the resulting coordinates to an output [geometry stream](https://github.com/mbostock/d3/wiki/Geo-Streams). function projectPoint(x, y) { var point = map.latLngToLayerPoint(new L.LatLng(y, x)); this.stream.point(point.x, point.y); }

Now that we can project points, we can create a [d3.geo.path](https://github.com/mbostock/d3/wiki/Geo-Paths#wiki-path) to convert GeoJSON to SVG:

```
var transform = d3.geo.transform({point: projectPoint}),
 path = d3.geo.path().projection(transform);
```

We can also create path elements for each of the features using D3's [data join](https://bost.ocks.org/mike/join/):

```
var feature = g.selectAll("path")
 .data(collection.features)
 .enter().append("path");
```

The path elements are initially empty; later, we'll initialize the path data by setting the `d` attribute:

```
feature.attr("d", path);
```

The [selection.attr](https://github.com/mbostock/d3/wiki/Selections#wiki-attr) method computes the path data for each feature. The path elements are bound to the associated GeoJSON feature, so these features are fed to the path generator (a d3.geo.path), which calls our custom projection, which in turn uses Leaflet's projection. Still following?

## [#](https://bost.ocks.org/mike/leaflet/#fit)Fitting SVG to a Layer

One additional wrench: how big should the SVG element be? Unfortunately, it can't simply be 960×500 because the user can zoom and pan; the size depends on the displayed geographic features and the current zoom level!

It can be difficult to compute a bounding box with arbitrary projections, but fortunately D3 provides a convenient mechanism for computing the [projected bounding box](https://github.com/mbostock/d3/wiki/Geo-Paths#wiki-path_bounds) of our features using our custom transform to convert the longitude and latitude to pixels:

```
var bounds = path.bounds(collection),
 topLeft = bounds[0],
 bottomRight = bounds[1];
```

Now we can set the dimensions of the SVG with sufficient padding to display features above or to the left of the origin. The layer origin is arbitrary, so sometimes you need to draw above and to the left of the origin; without this extra padding, the top-left corner would be clipped and some features would be invisible. (Note that since we're setting the left and top styles, we rely on the SVG element being position: relative.)

```
svg .attr("width", bottomRight[0] - topLeft[0])
 .attr("height", bottomRight[1] - topLeft[1])
 .style("left", topLeft[0] + "px")
 .style("top", topLeft[1] + "px");

g .attr("transform", "translate(" + -topLeft[0] + "," + -topLeft[1] + ")");
```

Lastly, the above code is hooked up to Leaflet's `viewreset` event, so that the SVG can be repositioned and rerendered whenever the map zooms. [View-source](view-source:https://bost.ocks.org/mike/leaflet/) to see the full code.

Happy mapping!

June 19, 2012 [Mike Bostock](https://bost.ocks.org/mike/)
