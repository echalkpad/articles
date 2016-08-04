# Let's Make a Bubble Map

[Original URL](https://bost.ocks.org/mike/bubble-map/)

> My previous Let's Make a Map tutorial describes how to make a basic map with D3 and TopoJSON; now it's time to cover thematic mapping in the form of a graduated symbol map. The simplest...

My previous [Let's Make a Map](https://bost.ocks.org/mike/map/) tutorial describes how to make a basic map with [D3](http://d3js.org) and [TopoJSON](https://github.com/mbostock/topojson); now it's time to cover thematic mapping in the form of a [graduated symbol map](http://indiemapper.com/app/learnmore.php?l=proportional_symbols). The simplest graduated symbol is a circle, or _bubble_, whose size is proportional to the associated data. In this tutorial, we'll make a bubble map of [population](http://xkcd.com/1138/) by U.S. county.

Source: American Community Survey, 2012 5-Year Estimate

This tutorial necessarily covers a lot of ground. The main tasks are for any visualization are:

- **acquiring** data from primary sources,
- **transforming** data into a display-appropriate representation, and
- **displaying** data in a suitable visual encoding.

There are many different ways to perform these tasks, but this tutorial will focus on my [preferred workflow](https://bost.ocks.org/mike/bubble-map/#initializing). After acquiring [cartographic boundaries](https://bost.ocks.org/mike/bubble-map/#finding-boundaries) and [population estimates](https://bost.ocks.org/mike/bubble-map/#finding-data) from the U.S. Census Bureau, we'll [transform](https://bost.ocks.org/mike/bubble-map/#merging-data) this data to TopoJSON and [display](https://bost.ocks.org/mike/bubble-map/#displaying-data) it using D3\. Lastly, I'll briefly comment on effective design for [visual communication](https://bost.ocks.org/mike/bubble-map/#communicating).

## [#](https://bost.ocks.org/mike/bubble-map/#initializing)Initializing

At a minimum, you'll need [Node](http://nodejs.org) and a [basic web server](https://github.com/nodeapps/http-server) for making maps. I covered this [previously](https://bost.ocks.org/mike/block/#setup), so I won't repeat myself here.

Although not essential, I also recommend [Git](http://git-scm.com/) to keep a history of your changes, allowing you to revert mistakes (such as accidentally deleting hours of work). Create a new folder for this project, go to that folder in the terminal, and run the following command:

```
git init
```

I use [NPM](https://www.npmjs.org) to define local dependencies. The benefit of this approach is that you can have multiple versions of software packages installed simultaneously, and you don't have to worry about things breaking when you upgrade because each project is isolated. A minimal package definition is:

```
{
 "name": "anonymous",
 "private": true,
 "version": "0.0.1",
 "dependencies": {
 "topojson": "1"
 }
}
```

Save this to a file called `package.json`, and run:

```
npm install
```

You should now see a `node_modules` folder, containing the installed `topojson` package.

If you're using Git, you should also create a [local `.gitignore` file](https://help.github.com/articles/ignoring-files) so that you don't accidentally check-in generated files to the repository. It should look something like this:

```
.DS_Store
build
node_modules
```

The `build` directory is where we'll store our generated files. Because those files are generated, they don't need to be saved in the Git repository -- they can be rebuilt at any time.

## [#](https://bost.ocks.org/mike/bubble-map/#finding-boundaries)Finding Boundaries

The U.S. Census Bureau publishes [simplified cartographic boundaries](https://www.census.gov/geo/maps-data/data/cbf/cbf_counties.html) as shapefiles for thematic mapping. The Census Bureau also publishes [TIGER/Line](https://www.census.gov/geo/maps-data/data/tiger-line.html) shapefiles that are higher resolution and more up-to-date; however, for the small scale map we are making, that extra resolution is not needed. County boundaries also don't change very frequently, so it's usually acceptable to use the decennial census rather than the most recent release.

We'll be using the lowest-resolution shapefile, at "20m" or 1:20,000,000 scale. Rather than download the file and check it in to our git repo, we'll use [Make](http://www.gnu.org/software/make/manual/) to document where this file is located and download it. Create a `Makefile` with the following contents:

Here `$@` is the path to the target file, `$(dir $@)` is the directory containing the target file, and `$(notdir $@)` is the target file name. These abbreviations are faster to read and help avoid typos, but use long names if you find them too cryptic. build/gz_2010_us_050_00_20m.zip: mkdir -p $(dir $@) curl -o $@ <http://www2.census.gov/geo/tiger/GENZ2010/$(notdir> $@)

Next, run:

```
make build/gz_2010_us_050_00_20m.zip
```

This will download the zipfile from the Census Bureau and save it in the build directory.

## [#](https://bost.ocks.org/mike/bubble-map/#converting-boundaries)Converting Boundaries

The zipfile by itself isn't very useful. We need to unzip its contents and convert the contained shapefile into TopoJSON for web delivery. We could do this by hand, but we'll again use Make so that our process is [documented and repeatable](https://bost.ocks.org/mike/make/). Add the following to the Makefile:

```
build/gz_2010_us_050_00_20m.shp: build/gz_2010_us_050_00_20m.zip
    unzip -od $(dir $@) $
```

This rule unzips the previously-downloaded file, giving us shapefiles. But don't run it yet -- we can combine it with another rule to convert the shapefiles to TopoJSON:

For details on the meaning of these arguments, the see [TopoJSON command-line reference](https://github.com/mbostock/topojson/wiki/Command-Line-Reference). The abbreviation `$ is shorthand for the first prerequisite of this rule: the shapefile. We disable filtering so that all small counties are retained after simplifying.`

```
build/counties.json: build/gz_2010_us_050_00_20m.shp
    node_modules/.bin/topojson \
        -o $@ \
        --projection='width = 960, height = 600, d3.geo.albersUsa() \
            .scale(1280) \
            .translate([width / 2, height / 2])' \
        --simplify=.5 \
        -- counties=$
```

Now run this new command:

```
make build/counties.json
```

In fact, this is not just converting the shapefile to TopoJSON, but also quantizing, projecting to the [Albers USA projection](https://github.com/mbostock/d3/wiki/Geo-Projections#albersUsa) and simplifying. Together, these changes save quite a bit of space! The resulting file is 496KB, while the original shapefile was 1.7MB.

## [#](https://bost.ocks.org/mike/bubble-map/#displaying-boundaries)Displaying Boundaries

Enough terminal. Time to get something on the screen. Create an `index.html`:

```
<!DOCTYPE html>
<meta charset="utf-8">
<style>

path {
 fill: none;
 stroke: #000;
 stroke-linejoin: round;
 stroke-linecap: round;
}

</style>
<body>
<script src="//d3js.org/d3.v3.min.js" charset="utf-8"></script>
<script src="//d3js.org/topojson.v1.min.js"></script>
<script>

var width = 960,
 height = 600;

var path = d3.geo.path()
 .projection(null);

var svg = d3.select("body").append("svg")
 .attr("width", width)
 .attr("height", height);

d3.json("build/counties.json", function(error, us) {
 if (error) return console.error(error);

 svg.append("path")
 .datum(topojson.mesh(us))
 .attr("d", path);
});

</script>
```

Launch your local web server, and then visit your page. It should look something like this:

Two things to note at this stage. First, the [d3.geo.path](https://github.com/mbostock/d3/wiki/Geo-Paths#path) instance has a null projection; that's because our TopoJSON is [already projected](https://bl.ocks.org/mbostock/5557726), so we can display it as-is. This greatly improves rendering performance. Second, we're just displaying the county boundaries so far (using [topojson.mesh](https://github.com/mbostock/topojson/wiki/API-Reference#mesh)). We still have a bit of work to do before we can draw population bubbles.

## [#](https://bost.ocks.org/mike/bubble-map/#finding-data)Finding Data

The next task is to fetch the data we want to visualize: population estimates by county. Sometimes you may find that data conveniently baked into your shapefile, but here we'll need to return to the U.S. Census Bureau and gather the requisite table from the American Community Survey (ACS) using the [American FactFinder](http://factfinder2.census.gov/).

The ACS publishes 1-year, 3-year and 5-year estimates. The 5-year estimates are the most accurate, and are available in smaller geographic subdivisions such as census tracts. If you care more about recency than accuracy, use the 3- or 1-year estimates instead. Here are the approximately twenty steps required to download a CSV:

1. Go to [factfinder2.census.gov](http://factfinder2.census.gov/).
2. Find where it says "American Community Survey" and click "get data »".
3. Click the blue "Geographies" button on the left.
4. In the pop-up, select "..... County - 050" in the "geographic type" menu.
5. Select "All Counties within United States" in the "geographic areas" box.
6. Click the "ADD TO YOUR SELECTIONS" button.
7. Click "CLOSE" to dismiss the pop-up.
8. Click the blue "Topics" button on the left.
9. In the pop-up, expand the "People" submenu.
10. Expand the "Basic Count/Estimate" submenu.
11. Click "Population Total".
12. Click "CLOSE" to dismiss the pop-up.
13. In the table, click on the most recent ACS 5-year estimate named "TOTAL POPULATION".
14. On the next page, click the "Download" link under "Actions".
15. In the pop-up, click "OK".
16. Wait for it to "build" your file.
17. When it's ready, click "DOWNLOAD".
18. Finally, expand the downloaded zip file (ACS_12_5YR_B01003.zip).

Did you know? The American FactFinder2 cost [$33.3 million](http://www.theatlantic.com/technology/archive/2012/07/us-census-bureaus-hated-american-factfinder-tool-cost-333-million-to-build/259698/) and was built by IBM U.S. Federal. If you would prefer this as a two-minute instructional video:

I recently learned that the Census Bureau has [an API](http://www.census.gov/developers/). However, it requires an API key, so I haven't tried it yet. The API supports stateless GET requests, so it should be easy to incorporate into the Makefile. If you've tried it, [let me know](https://twitter.com/mbostock) how it worked out. An eminently more usable alternative to FactFinder is [censusreporter.org](http://censusreporter.org/), a Knight News Challenge-funder project with a convenient autocomplete interface and a robust API. Here is a direct link to download the latest ACS [total population estimate by county](http://api.censusreporter.org/1.0/data/download/latest?table_ids=B01003&geo_ids=050%7C01000US&format=csv). Note, however, that the column headers for this CSV are slightly different than the ones from FactFinder: _you must either edit the file or the Makefile rules accordingly_.

If you want to experience the FactFinder vicariously, you may also instead download [my copy](https://gist.githubusercontent.com/mbostock/9943478/raw/ACS_12_5YR_B01003_with_ann.csv). However, I recommend that you prefer data from primary sources whenever possible, as this ensures the data's accuracy.

## [#](https://bost.ocks.org/mike/bubble-map/#merging-data)Merging Data

The downloaded `ACS_12_5YR_B01003_with_ann.csv` is slightly unusual in that it contains two header lines. Normally, a [CSV](http://tools.ietf.org/html/rfc4180) file only contains at most one header line defining the names of the columns; this is the format that [d3.csv](https://github.com/mbostock/d3/wiki/CSV) (and TopoJSON) expects. Open the downloaded CSV in your text editor and delete the first of the two header lines. The first few lines should look like this:

```
Id,Id2,Geography,Estimate; Total,Margin of Error; Total
0500000US01001,01001,"Autauga County, Alabama",54590,*****
0500000US01003,01003,"Baldwin County, Alabama",183226,*****
0500000US01005,01005,"Barbour County, Alabama",27469,*****
```

Now we can use TopoJSON's `--external-properties` feature to join the shapefile of counties with the CSV of population estimates, making additional properties available in the output TopoJSON. This flag works similar to a join in a relational database. Using the ID property as a primary key, we assign each row in the CSV file to the corresponding feature in the shapefile.

One frequent complication is that the external properties do not use the same ID property name as the shapefile. Here the CSV file uses the name `Id2`, while the shapefile uses `STATE` and `COUNTY`. (We could use the longer `Id` and `GEO_ID` properties, but we'd prefer to use the shorter identifier here, without the redundant leading `0500000US`.)

To address these inconsistencies, the `--id-property` argument accepts a comma-separated list of JavaScript expressions to specify how the ID property should be computed. For the shapefile, we'll use the expression `STATE+COUNTY` to concatenate those two properties, while for the CSV, we'll use `Id2`.

We can also use JavaScript expressions to define the properties we want to include in the generated TopoJSON. Here we'll map the `Geography` column from the CSV to the `name` property, and the `Estimate; Total` column to the `population` property. The latter requires special syntax because the column name isn't a valid JavaScript identifier. Also, we want it to be a number.

Modifying our Makefile slightly:

```
build/counties.json: build/gz_2010_us_050_00_20m.shp ACS_12_5YR_B01003_with_ann.csv
    node_modules/.bin/topojson \
        -o $@ \
        --id-property='STATE+COUNTY,Id2' \
        --external-properties=ACS_12_5YR_B01003_with_ann.csv \
        --properties='name=Geography' \
        --properties='population=+d.properties["Estimate; Total"]' \
        --projection='width = 960, height = 600, d3.geo.albersUsa() \
            .scale(1280) \
            .translate([width / 2, height / 2])' \
        --simplify=.5 \
        -- counties=$
```

## [#](https://bost.ocks.org/mike/bubble-map/#merging-boundaries)Merging Boundaries

One subtle detail you may not have noticed in the final bubble map is that it displays state boundaries rather than county boundaries. This reduces visual noise; each county has a corresponding bubble, while the state boundary lines provide additional geographic context.

We can compute the state boundaries without downloading another shapefile because TopoJSON is a [topological format](http://en.wikipedia.org/wiki/Geospatial_topology). The following rule merges (or "dissolves") counties within the same state, producing a new `states` layer in the output TopoJSON file:

```
build/states.json: build/counties.json
    node_modules/.bin/topojson-merge \
        -o $@ \
        --in-object=counties \
        --out-object=states \
        --key='d.id.substring(0, 2)' \
        -- $
```

The resulting state mesh:

A similar rule can compute the national boundary by merging states:

```
us.json: build/states.json
    node_modules/.bin/topojson-merge \
        -o $@ \
        --in-object=states \
        --out-object=nation \
        -- $
```

To run these new rules:

```
make us.json
```

The [topojson.merge](https://github.com/mbostock/topojson/wiki/API-Reference#client-api) function is part of the client API, so we could do this step in the client rather than baking it into the TopoJSON file. However, it's slightly faster to precompute the merged areas, and sometimes it's nice to have fewer moving parts.

Don't forget to load the new file in `index.html`, replacing the old counties-only file:

```
d3.json("us.json", function(error, us) {
 if (error) return console.error(error);

 // Append to svg here.
});
```

## [#](https://bost.ocks.org/mike/bubble-map/#displaying-data)Displaying Data

First, let's finish the base map that will appear underneath the bubbles.

View [base.html](https://bost.ocks.org/mike/bubble-map/base.html) for the full source of the base map. The relevant code for the base map is:

```
svg.append("path")
 .datum(topojson.feature(us, us.objects.nation))
 .attr("class", "land")
 .attr("d", path);

svg.append("path")
 .datum(topojson.mesh(us, us.objects.states, function(a, b) { return a !== b; }))
 .attr("class", "border border--state")
 .attr("d", path);
```

The land is drawn as a single feature, with the state borders drawn as white lines on top. The filter function passed to topojson.mesh specifies that only internal state borders should be drawn; the coastlines are not stroked so as to retain detail around small islands and inlets.

We'll need these new styles, as well, replacing the old ones:

```
.land {
 fill: #ddd;
}

.border {
 fill: none;
 stroke: #fff;
 stroke-linejoin: round;
 stroke-linecap: round;
}
```

Now to place bubbles at each county centroid:

```
svg.append("g")
 .attr("class", "bubble")
 .selectAll("circle")
 .data(topojson.feature(us, us.objects.counties).features)
 .enter().append("circle")
 .attr("transform", function(d) { return "translate(" + path.centroid(d) + ")"; })
 .attr("r", 1.5);
```

Arguably it would be better to use the [center of population](https://www.census.gov/geo/reference/centersofpop.html) for each county rather than the area centroid. To size the bubbles, create a [d3.scale.sqrt](https://github.com/mbostock/d3/wiki/Quantitative-Scales#sqrt) so that the area of the circle is proportional to the associated population; the radius of the circle is proportional to the square root of the population. (Alternatively, you could use [d3.svg.symbol](https://github.com/mbostock/d3/wiki/SVG-Shapes#symbol) for other proportional symbols.) We could compute the domain of the scale from the data, but since we know the approximate distribution of the data beforehand, we can simply hard-code reasonable values:

To avoid distortion, make sure that the minimum domain and range values are both zero! var radius = d3.scale.sqrt() .domain([0, 1e6]) .range([0, 15]);

View [basic.html](https://bost.ocks.org/mike/bubble-map/basic.html) for the full source of this map. This version of the map suffers greatly from _occlusion_: larger circles, such as Cook County in Illinois and Los Angeles County in California, obscure smaller bubbles underneath. Occlusion can be mitigated by making the bubbles smaller, but this makes it harder to see less-populated counties and emphasizes dense urban areas.

Another way to reduce occlusion is to sort bubbles by descending size, so that smaller bubbles are drawn on top of larger bubbles. The bubbles still overlap, but the smaller bubbles are now visible.

```
svg.append("g")
 .attr("class", "bubble")
 .selectAll("circle")
 .data(topojson.feature(us, us.objects.counties).features
 .sort(function(a, b) { return b.properties.population - a.properties.population; }))
 .enter().append("circle")
 .attr("transform", function(d) { return "translate(" + path.centroid(d) + ")"; })
 .attr("r", function(d) { return radius(d.properties.population); });
```

A bit of transparency and thin white stroke also helps.

```
.bubble {
 fill-opacity: .5;
 stroke: #fff;
 stroke-width: .5px;
}
```

View [sorted.html](https://bost.ocks.org/mike/bubble-map/sorted.html) for the full source of this map. Boom! A bubble map. But now that our map is legible, it's a good time to consider its validity: _often our source data is not as clean and regular as we expect_, and data-cleanliness issues may not be apparent in the visualization. It's critical to spot-check data and verify that it's correct. You should run sanity checks on the data, such as whether any counties are duplicated or missing data.

The [d3.nest](https://github.com/mbostock/d3/wiki/Arrays#d3_nest) operator is useful for checking uniqueness. For example, an earlier version of this tutorial used county boundaries from a [different source](http://www.nationalatlas.gov/mld/1cntyp.html), and the shapefile specified separate features for each of a county's discontiguous areas. (Honolulu County in Hawaii consists not only of Oahu, but the tiny Ford and Sand islands as well.) To avoid duplicate bubbles and misleading readers, you would need to [group](https://github.com/mbostock/topojson/blob/master/bin/topojson-group) features by county! The shapefile from the U.S. Census Bureau is already grouped, so we could skip this step.

## [#](https://bost.ocks.org/mike/bubble-map/#communicating)Communicating

To make this map _communicate_ rather than simply look pretty, we need a few administrative touches. Adding a basic tooltip using SVG's [title element](http://www.w3.org/TR/SVG11/struct.html#DescriptionAndTitleElements) is a reasonable improvement, but we really need a legend to make the meaning of the area encoding is apparent. Here is a basic legend that displays three circles and their associated population sizes:

```
var legend = svg.append("g")
 .attr("class", "legend")
 .attr("transform", "translate(" + (width - 50) + "," + (height - 20) + ")")
 .selectAll("g")
 .data([1e6, 3e6, 6e6])
 .enter().append("g");

legend.append("circle")
 .attr("cy", function(d) { return -radius(d); })
 .attr("r", radius);

legend.append("text")
 .attr("y", function(d) { return -2 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii radius(d); })
 .attr("dy", "1.3em")
 .text(d3.format(".1s"));
```

View [bl.ocks.org/9943478](https://bl.ocks.org/mbostock/9943478) for the full source of the final map, including the Makefile. And the corresponding styles:

```
.legend circle {
 fill: none;
 stroke: #ccc;
}

.legend text {
 fill: #777;
 font: 10px sans-serif;
 text-anchor: middle;
}
```

An alternative to the explicit legend is to annotate a few circles with their exact value -- say, Los Angeles, Miami-Dade, and Cook. These values can then serve as comparison points for the other value, rather than needing additional visual elements.

Lastly, a wide variety of interactive improvements could be made, such as custom tooltip that displays additional information and the county outline, or [panning and zooming](https://bl.ocks.org/mbostock/9656675) to allow the viewer to dive in for more detail. You might also consider a [Voronoi overlay](https://bl.ocks.org/mbostock/8033015) to make the counties with small populations easier to hover. This tutorial merely provides a basic starting point for an interactive graduated symbol map.

May 6, 2014 [Mike Bostock](https://bost.ocks.org/mike/)
