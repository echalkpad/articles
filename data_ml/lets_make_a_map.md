# Let's Make a Map

[Original URL](https://bost.ocks.org/mike/map/)

> In this tutorial, I'll cover how to make a modest map from scratch using D3 and TopoJSON. I'll show you a few places where you can find free geographic data online, and how to convert...

In this tutorial, I'll cover how to make a modest map from scratch using [D3](http://d3js.org) and [TopoJSON](https://github.com/mbostock/topojson). I'll show you a few places where you can find free geographic data online, and how to convert it into a format that is both efficient and convenient for display. I won't cover [thematic mapping](http://en.wikipedia.org/wiki/Thematic_map), but the map we'll make includes labels for populated places and you can extend this technique to geographic visualizations such as [graduated symbol maps](https://bl.ocks.org/4342045) and [choropleths](https://bl.ocks.org/4060606).

Without further ado, here's the map:

Shown are the four constituent countries of the United Kingdom: [Scotland](http://en.wikipedia.org/wiki/Scotland), [Northern Ireland](http://en.wikipedia.org/wiki/Northern_Ireland), [Wales](http://en.wikipedia.org/wiki/Wales) and [England](http://en.wikipedia.org/wiki/England). As maps go, it's not particularly interesting, but the simplicity should serve adequately to demonstrate the basics of map-making.

## [#](https://bost.ocks.org/mike/map/#finding-data)Finding Data

The first task for any map is finding geometry. The primary source for administrative boundaries and other official data is the respective government. The U.S. [Census Bureau](http://www.census.gov/), for example, publishes geometry for a range of spatial subdivisions such as states, counties and census tracts. As government work, this data is typically in the public domain and thus free to use. The [Ordnance Survey](http://www.ordnancesurvey.co.uk/oswebsite/) provides similar vector data for the UK.

Unfortunately, government data can sometimes be hard to find or use. Just ask anyone who's wrangled with [GRIB files](http://en.wikipedia.org/wiki/GRIB) or aimlessly traversed [mysterious FTP servers](ftp://ftp2.census.gov/geo/tiger/TIGER2010/)! This should improve over time as demand for data grows, but there will always be cruft from past releases and confusing formats spawned by committee. Projects such as [Data.gov](http://www.data.gov/) and the [Sunlight Foundation](http://sunlightfoundation.com/) aim specifically to improve the quality of government data offerings, and sites such as [Social Explorer](http://www.socialexplorer.com/) package up government data in more accessible formats.

For a crowdsourced alternative, [GeoCommons](http://geocommons.com) is a platform for sharing geographic datasets. Integrated search and preview make it easy to explore. But, while there's lots of useful data, you should be wary of unverified sources, at least for journalism; it's better to get data directly from an official agency or other citeable source.

Hands-down, the most convenient source of free geographic data is [Natural Earth](http://www.naturalearthdata.com/). An apparent labor of love by cartographer [Nathaniel Vaughn Kelso](http://kelsocartography.com/) ([and others](http://www.naturalearthdata.com/about/contributors/)), Natural Earth provides a variety of [cultural, physical and raster datasets](http://www.naturalearthdata.com/downloads/). Shapefiles are beautifully simplified by hand for different resolutions, letting you choose the resolution appropriate to your application. We'll use two 1:1e7 Natural Earth datasets for this map:

- [Admin 0 - Details - map subunits](http://www.naturalearthdata.com/http//www.naturalearthdata.com/download/10m/cultural/ne_10m_admin_0_map_subunits.zip)
- [Populated Places](http://www.naturalearthdata.com/http//www.naturalearthdata.com/download/10m/cultural/ne_10m_populated_places.zip)

The first includes country polygons, while the second has names and locations of populated places. Both of these files cover the entire world, so we'll next filter the data to a suitable subset.

## [#](https://bost.ocks.org/mike/map/#installing-tools)Installing Tools

Geographic data files are almost always too large for manual cleanup or conversion. Fortunately, there's a vibrant [geo open-source community](http://www.osgeo.org/), and many excellent free tools to manipulate and convert between standard formats.

Don't ask [what OGR stands for](http://trac.osgeo.org/gdal/wiki/FAQGeneral#WhatdoesOGRstandfor), or [how to pronounce GDAL](https://twitter.com/HULKGEO/status/25680040871), because it's a long story... The big multitool to know is the [Geospatial Data Abstraction Library](http://www.gdal.org/). Commonly referred to as GDAL, it includes the [OGR Simple Features Library](http://www.gdal.org/ogr/) and the `ogr2ogr` binary we'll use to manipulate the Natural Earth shapefiles. There are [official GDAL binaries](http://trac.osgeo.org/gdal/wiki/DownloadingGdalBinaries) for a variety of platforms, but if you're on a Mac you should use [Homebrew](http://mxcl.github.com/homebrew/):

```
brew install gdal
```

Next you'll need the reference implementation for [TopoJSON](https://github.com/mbostock/topojson), which requires Node.js. (You can install Node via Homebrew, but the [official installers](http://nodejs.org/download/) work just as well.) After installing Node, run the following to install TopoJSON:

```
npm install -g topojson
```

And to verify the two installations, try:

```
which ogr2ogr
which topojson
```

If you're having installation problems, check that your `PATH` environment variable is set correctly by editing `/etc/paths`. This should print `/usr/local/bin/ogr2ogr` and `/usr/local/bin/topojson`.

## [#](https://bost.ocks.org/mike/map/#converting-data)Converting Data

Now equipped, we can merge the two shapefiles into a single TopoJSON file. We'll first filter the shapefile so that it includes only the UK features we need. Then we'll convert the shapefiles to intermediate [GeoJSON](http://geojson.org) before finally generating TopoJSON.

Why two JSON formats? In truth, they're siblings: [TopoJSON](https://github.com/mbostock/topojson) is an extension of GeoJSON that encodes topology. Combined with fixed-precision encoding for coordinates, TopoJSON is usually much smaller than GeoJSON. Our map's GeoJSON is 536KB, while the TopoJSON is only 80KB, a reduction of 85%. (This reduction holds even after gzip compression!) In addition, the topology information in TopoJSON enables automatic computation of boundary lines and other [interesting](http://prag.ma/code/d3-cartogram/) [applications](https://bl.ocks.org/4180634), rather than requiring additional storage.

Taking the downloaded `ne_10m_admin_0_map_subunits.shp` as input, use `ogr2ogr` to create the `subunits.json` GeoJSON file:

Note: earlier versions of Natural Earth data used lowercase property names, such as `adm0_a3` instead of `ADM0_A3`. ogr2ogr \ -f GeoJSON \ -where "ADM0_A3 IN ('GBR', 'IRL')" \ subunits.json \ ne_10m_admin_0_map_subunits.shp

The `-where` argument implies a filter: only features whose `ADM0_A3` property equals `"GBR"` or `"IRL"` will be in the output GeoJSON. Here, `ADM0` refers to Admin-0, the highest level administrative boundaries, and `A3` refers to [ISO 3166-1 alpha-3](http://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) country codes. Despite mapping only the United Kingdom, we need all of [Ireland](http://en.wikipedia.org/wiki/Ireland) to portray land accurately; otherwise, we might give the mistaken impression that Northern Ireland is an island unto itself!

Next we'll filter the populated places. The places' properties are (somewhat arbitrarily) different, so the where clause refers to `ISO_A2` instead. The `SCALERANK` filter further whittles the labels down to major cities.

If you're unsure which properties to filter on, try dropping the where clause and viewing the GeoJSON output in a text editor. ogr2ogr \ -f GeoJSON \ -where "ISO_A2 = 'GB' AND SCALERANK

And last we combine `subunits.json` and `places.json` into a single `uk.json`. This step includes a minor transformation to fix inconsistencies in the source data, renaming the `NAME` property to `name`, and promoting the `SU_A3` property to the object id.

```
topojson \
 -o uk.json \
 --id-property SU_A3 \
 --properties name=NAME \
 -- \
 subunits.json \
 places.json
```

Although unneeded for our simple map, `ogr2ogr` has many more powerful features that you may need on occasion. The `-clipdst` argument, for example, clips the shapefile to a rectangular [bounding box](http://boundingbox.klokantech.com/), useful for displaying only a small part of certain features. If your shapefile uses a gridded coordinate system (e.g., [UTM](http://en.wikipedia.org/wiki/Universal_Transverse_Mercator)), use `-t_srs EPSG:4326` to convert back to conventional latitude and longitude. Read the [ogr2ogr manual](http://www.gdal.org/ogr2ogr.html) for more options.

## [#](https://bost.ocks.org/mike/map/#loading-data)Loading Data

With that brief foray into the command line for manipulating geographic data, we can return to web development. Rather than discuss what's been [covered previously](https://github.com/mbostock/d3/wiki/Tutorials), I'll assume you're already familiar with HTML and JavaScript. If you're not, take a moment to read [Scott Murray's introduction to D3](http://alignedleft.com/tutorials/d3/). In the same directory as the `uk.json` file, create an `index.html` file with the following template:

```
<!DOCTYPE html>
<meta charset="utf-8">
<style>

/* CSS goes here. */

</style>
<body>
<script src="//d3js.org/d3.v3.min.js" charset="utf-8"></script>
<script src="//d3js.org/topojson.v1.min.js"></script>
<script>

/* JavaScript goes here. */

</script>
```

Then, fire up a local web server to view your example. I use [http-server](https://github.com/nodeapps/http-server), but any server will do:

A web server is needed to skirt security restrictions loading data out of the local file system. http-server -p 8008 &

If you visit <http://localhost:8008>, you should now see a glorious blank page:

View source: [step-1.html](https://bost.ocks.org/mike/map/step-1.html)

Probably not the excitement you were hoping for! But, we can change that pretty quickly. Inside the main script tag (where it says "JavaScript goes here"), add a call to [d3.json](https://github.com/mbostock/d3/wiki/Requests#wiki-d3_json) to load the TopoJSON file:

```
d3.json("uk.json", function(error, uk) {
 if (error) return console.error(error);
 console.log(uk);
});
```

Now if you peek at your [JavaScript console](https://developers.google.com/chrome-developer-tools/docs/console), you should see a _topology_ object that represents the administrative boundaries and populated places of the United Kingdom.

## [#](https://bost.ocks.org/mike/map/#displaying-polygons)Displaying Polygons

There are a variety of ways to render two-dimensional geometry in a browser, but the two main standards are [SVG](http://www.w3.org/Graphics/SVG/) and [Canvas](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-canvas-element.html). [D3 3.0](https://github.com/mbostock/d3/wiki/3.0) supports both. We'll use SVG for this example because you can style SVG via [CSS](http://www.w3.org/Style/CSS/), and declarative styling is easier. To create the root SVG element:

You could instead declare the SVG element in HTML, but then you'd need to pull the width and height out of the DOM. var width = 960, height = 1160;

```
var svg = d3.select("body").append("svg")
 .attr("width", width)
 .attr("height", height);
```

I recommend doing this at the top of the main script, rather than inside the d3.json callback. That's because d3.json is _asynchronous_: the rest of the page will render while we wait for the TopoJSON file to download. Creating the empty SVG root when the page first loads avoids distracting reflow when the geometry finally arrives.

We need two more things to render geography: a [projection](https://github.com/mbostock/d3/wiki/Geo-Projections) and a [path generator](https://github.com/mbostock/d3/wiki/Geo-Paths). As its name implies, the projection _projects_ spherical coordinate to the Cartesian plane. This is needed to display spherical geometry on a 2D screen; you can skip this step if this is the future and you're using a 3D holographic display. The path generator takes the projected 2D geometry and formats it appropriately for SVG or Canvas.

**So make a map already!** Replace the code inside the d3.json callback like so:

```
d3.json("uk.json", function(error, uk) {
 if (error) return console.error(error);

 svg.append("path")
 .datum(topojson.feature(uk, uk.objects.subunits))
 .attr("d", d3.geo.path().projection(d3.geo.mercator()));
});
```

You should now see a small, black, familiar speck:

View source: [step-2.html](https://bost.ocks.org/mike/map/step-2.html)

Lesser cartographers would now proclaim a job well-done and go home for a beer. But we're better than that, so we'll polish a bit more before we throw in the towel. Also, it might help if I explain those three magical lines of code...

Future versions of D3 may support TopoJSON natively and eliminate this step. Recall from earlier the two closely-related JSON geographic data formats: GeoJSON and TopoJSON. While our data can be stored more efficiently in TopoJSON, we must convert _back_ to GeoJSON for display. Breaking this step out to make it explicit:

```
var subunits = topojson.feature(uk, uk.objects.subunits);
```

Similarly, we can extract the definition of the projection to make the code clearer:

```
var projection = d3.geo.mercator()
 .scale(500)
 .translate([width / 2, height / 2]);
```

And likewise the path generator:

```
var path = d3.geo.path()
 .projection(projection);
```

And the path element, to which we [bind](https://github.com/mbostock/d3/wiki/Selections#wiki-datum) the GeoJSON data, and then use [selection.attr](https://github.com/mbostock/d3/wiki/Selections#wiki-attr) to set the `"d"` attribute to the formatted path data:

```
svg.append("path")
 .datum(subunits)
 .attr("d", path);
```

With the code so structured, we can now change the projection to something more suitable for the United Kingdom. The [Albers equal-area conic projection](http://en.wikipedia.org/wiki/Albers_projection) is a good choice, with standard parallels of 50°N and 60°N. To position the map, we [rotate longitude](https://bl.ocks.org/4282586) by +4.4° and set the center to 0°W 55.4°N, giving an effective origin of [4.4°W 55.4°N](https://maps.google.com/maps?q=4.4°W+55.4°N&z=7), somewhere in a field in Scotland.

```
var projection = d3.geo.albers()
 .center([0, 55.4])
 .rotate([4.4, 0])
 .parallels([50, 60])
 .scale(6000)
 .translate([width / 2, height / 2]);
```

Our map takes shape:

View source: [step-3.html](https://bost.ocks.org/mike/map/step-3.html)

## [#](https://bost.ocks.org/mike/map/#styling-polygons)Styling Polygons

As I mentioned earlier, a benefit of SVG is that we can style with CSS: we can color constituent countries by style rules that assign the [fill property](http://www.w3.org/TR/SVG/painting.html#FillProperty). However, we first need to give each country its own path element, rather than sharing one. Without distinct path elements, we have no way to assign distinct colors.

Within the `uk.json` TopoJSON file, the Admin-0 map subunits are represented as a [feature collection](http://geojson.org/geojson-spec.html#feature-collection-objects). By pulling out the `features` array, we can compute a [data join](https://bost.ocks.org/mike/join/) and create a path element for each feature:

```
svg.selectAll(".subunit")
 .data(topojson.feature(uk, uk.objects.subunits).features)
 .enter().append("path")
 .attr("class", function(d) { return "subunit " + d.id; })
 .attr("d", path);
```

We can also compute the `"class"` attribute as a function of data, so that in addition to the `subunit` class, each country has a three-letter class corresponding to its ISO-3166 alpha-3 country code. These allow us to apply separate fill styles for each country:

```
.subunit.SCT { fill: #ddc; }
.subunit.WLS { fill: #cdd; }
.subunit.NIR { fill: #cdc; }
.subunit.ENG { fill: #dcd; }
.subunit.IRL { display: none; }
```

The styles above also hide Ireland completely, but we'll bring it back in the next step when we display boundary lines. The map so far:

View source: [step-4.html](https://bost.ocks.org/mike/map/step-4.html)

## [#](https://bost.ocks.org/mike/map/#displaying-boundaries)Displaying Boundaries

To apply the finishing touch to the polygons, we need a few lines. These are two boundary lines representing the borders of England with Scotland and Wales, respectively, and the Irish coastline.

We'll use [topojson.mesh](https://github.com/mbostock/topojson/blob/v0.0.6/topojson.js#L101) to compute the boundaries from the topology. This requires two arguments: the _topology_ and a constituent geometry _object_. An optional _filter_ can reduce the set of returned boundaries, taking two arguments _a_ and _b_ representing the two features on either side of the boundary. For exterior boundaries such as coastlines, _a_ and _b_ are the same. Thus by filtering on `a === b` or `a !== b`, we obtain exterior or interior boundaries exclusively.

The England-Scotland and England-Wales borders are interior boundaries. We can exclude Ireland's border with Northern Ireland by also filtering on `id`:

```
svg.append("path")
 .datum(topojson.mesh(uk, uk.objects.subunits, function(a, b) { return a !== b && a.id !== "IRL"; }))
 .attr("d", path)
 .attr("class", "subunit-boundary");
```

That leaves only the Irish coastline, an exterior boundary:

```
svg.append("path")
 .datum(topojson.mesh(uk, uk.objects.subunits, function(a, b) { return a === b && a.id === "IRL"; }))
 .attr("d", path)
 .attr("class", "subunit-boundary IRL");
```

Add a bit of style:

```
.subunit-boundary {
 fill: none;
 stroke: #777;
 stroke-dasharray: 2,2;
 stroke-linejoin: round;
}

.subunit-boundary.IRL {
 stroke: #aaa;
}
```

And voilà:

View source: [step-5.html](https://bost.ocks.org/mike/map/step-5.html)

## [#](https://bost.ocks.org/mike/map/#displaying-places)Displaying Places

As with the country polygons, the populated places are a feature collection, so we can again convert from TopoJSON to GeoJSON and use d3.geo.path to render:

```
svg.append("path")
 .datum(topojson.feature(uk, uk.objects.places))
 .attr("d", path)
 .attr("class", "place");
```

This will draw a small circle for each city. We can adjust the radius by setting [path.pointRadius](https://github.com/mbostock/d3/wiki/Geo-Paths#wiki-pointRadius), and assign styles via CSS. But we _also_ want labels, so we need a data join to create text elements. By computing the `transform` property by projecting the place's coordinates, we translate the labels into the desired position:

```
svg.selectAll(".place-label")
 .data(topojson.feature(uk, uk.objects.places).features)
 .enter().append("text")
 .attr("class", "place-label")
 .attr("transform", function(d) { return "translate(" + projection(d.geometry.coordinates) + ")"; })
 .attr("dy", ".35em")
 .text(function(d) { return d.properties.name; });
```

Labeling a map well can be challenging, especially if you want to position labels automatically. We've mostly ignored the problem for our simple map, though it helps that we earlier filtered labels by `SCALERANK`. A convenient trick is to use right-aligned labels on the left side of the map, and left-aligned labels on the right side of the map, here using 1°W as the threshold:

```
svg.selectAll(".place-label")
 .attr("x", function(d) { return d.geometry.coordinates[0] > -1 ? 6 : -6; })
 .style("text-anchor", function(d) { return d.geometry.coordinates[0] > -1 ? "start" : "end"; });
```

As you can see below, this works reasonably well, though there remain a few overlapping labels (_e.g._, Nottingham, Coventry). If you're so inclined, you can fix these by special-casing alternate alignment, or you could simply remove the offending labels. You could even try [simulated annealing](https://github.com/migurski/Dymo) or [force-directed layout](https://bl.ocks.org/1377729), but I'll leave [automatic label placement](http://en.wikipedia.org/wiki/Automatic_label_placement) for another day!

View source: [step-6.html](https://bost.ocks.org/mike/map/step-6.html)

## [#](https://bost.ocks.org/mike/map/#country-labels)Country Labels

Our map is missing a critical component: we haven't labeled the countries! We could have used Natural Earth's [Admin-0 label points](http://www.naturalearthdata.com/http//www.naturalearthdata.com/download/10m/cultural/ne_10m_admin_0_label_points.zip), but we can just as easily compute country label points using the [projected centroid](https://github.com/mbostock/d3/wiki/Geo-Paths#wiki-centroid):

```
svg.selectAll(".subunit-label")
 .data(topojson.feature(uk, uk.objects.subunits).features)
 .enter().append("text")
 .attr("class", function(d) { return "subunit-label " + d.id; })
 .attr("transform", function(d) { return "translate(" + path.centroid(d) + ")"; })
 .attr("dy", ".35em")
 .text(function(d) { return d.properties.name; });
```

The country labels are styled larger to distinguish them from city labels. By making them partly transparent, country labels are relegated to the background and city labels are more legible:

```
.subunit-label {
 fill: #777;
 fill-opacity: .5;
 font-size: 20px;
 font-weight: 300;
 text-anchor: middle;
}
```

And there we have it:

View source: [step-7.html](https://bost.ocks.org/mike/map/step-7.html)

## Continued Reading

If you enjoyed this tutorial, try perusing my [many other examples](https://bl.ocks.org/mbostock). The D3 [3.0 release notes](https://github.com/mbostock/d3/wiki/3.0) and [API reference](https://github.com/mbostock/d3/wiki/Geo-Projections) are also peppered with examples of geographic visualizations. You can also [hit me on Twitter](http://twitter.com/mbostock) with requests. _Happy New Year!_

December 30, 2012 [Mike Bostock](https://bost.ocks.org/mike/)
