# A Guide To Building SVG Maps From Natural Earth Data – Smashing Magazine

[Original URL](http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/)

> Interactive maps are a fantastic way to present geographic data to your visitors. Libraries like Google Maps and Open Street Maps are a popular choice to do this and they excel at visualizing...

**Interactive maps** are a fantastic way to present geographic data to your visitors. Libraries like Google Maps and Open Street Maps are a popular choice to do this and they excel at visualizing street-level data. However, for small-scale maps, **SVG maps** are often a better option. They are lightweight, fully customizable and are not encumbered by any licensing restrictions.

It's possible to find a number of SVG maps released under permissible licenses in the Wikimedia Commons. Unfortunately, it's likely that you will eventually find these options lacking. The map you need may not exist, may be out of date (as borders change), or may not be well-formatted for web use. This article will explain **how to create your own SVG maps using Natural Earth data** and open source tools. You will then be able to create SVG maps of any area of the world, using any projection, at any resolution. As an illustration, we will create an SVG world map.

The process will require us to:

- Download geographic data from Natural Earth data
- View and edit the geographic data using QGIS
- Convert the geographic data into SVG using Kartograph.py

## Getting The Geographic Data [Link](http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#getting-the-geographic-data)

To start, we need **geographic data** for country borders. This data is available from [Natural Earth](http://www.naturalearthdata.com/)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#1">1</a>
</sup>

. Natural Earth is built by volunteers and supported by the North American Cartographic Information Society. It specializes in small-scale maps that are well-suited for the web. This means the maps will look great at the country or province level, but aren't of a high enough resolution to show the neighborhoods of a city. Natural Earth releases its maps into the public domain.

To see all maps available for download, view the [Natural Earth downloads page](http://www.naturalearthdata.com/downloads/)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#2">2</a>
</sup>

.

Many borders in the world are hotly contested. Natural Earth's policy is to draw borders based on who controls the situation on the ground. We will be primarily working off of a small-scale cultural map, which contains all 247 countries in the world, and has boundary lakes removed. You can [download it as a zip file (186KB)](http://www.naturalearthdata.com/http//www.naturalearthdata.com/download/110m/cultural/ne_110m_admin_0_countries_lakes.zip)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#3">3</a>
</sup>

.

The data you download is stored in the [shapefile](http://en.wikipedia.org/wiki/Shapefile)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#4">4</a>
</sup>

 (_.shp_) format. Shapefile is an open standard geospatial vector data format created by Esri. The shapefile is accompanied by complementary files with the following extensions: _.dbf_, _.prj_ and _.shx_. Together, these files contain the vector geometry, attributes (`name`, `id`, etc.) and geospatial information for each country. For simplicity, when people refer to a shapefile, they are really referring to this group of files.

## Viewing The Geographic Data [Link](http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#viewing-the-geographic-data)

To see the data we just downloaded, we need to use [GIS](http://en.wikipedia.org/wiki/Geographic_information_system)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#5">5</a>
</sup>

 (geographic information systems) software. QGIS is open source GIS software. It runs on Linux, Mac OS X and Windows. [Download it from QGIS.](http://www.qgis.org/en/site/index.html)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#6">6</a>
</sup>

 This tutorial will be using QGIS 2.8.2 Wien.

After you have installed QGIS, open the QGIS Desktop application. QGIS is a powerful program that can work with many types of geographic data. Because of this, it can be quite intimidating. We'll only be using a small fraction of its functionality and can ignore much of what QGIS offers. For example, since we aren't working with any raster images (like satellite photography), we can ignore tools related to raster images.

To get started, we'll add our shapefile as a vector layer to our project.

- Select **Layer → Add Layer → Add Vector Layer** (from the menu bar)
- Browse to and open _ne_110m_admin_0_countries_lakes.shp_
- The map should appear in the window (it may be a different color for you)

[![World map](http://media.mediatemple.netdna-cdn.com/wp-content/uploads/2015/07/01-world-opt-small.png)](http://media.mediatemple.netdna-cdn.com/wp-content/uploads/2015/07/01-world-opt.png)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#7">7</a>
</sup>

<br>
The world map vector layer. ([View large version](http://media.mediatemple.netdna-cdn.com/wp-content/uploads/2015/07/01-world-opt.png)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#8">8</a>
</sup>

) Notice that _ne_110m_admin_0_countries_lakes.shp_ has been added to the **Layers** dialog.

![Layers dialog](http://media.mediatemple.netdna-cdn.com/wp-content/uploads/2015/07/02-layer-opt.png)<br>
The new layer. Layers in QGIS are similar to layers you find in photo editing software like Photoshop or GIMP. _You must select a layer before you can work on it_. As you go through this tutorial, if you find a tool isn't working properly, you have likely neglected to select the current layer.

It's also possible to hide layers from view by unchecking the box. This allows you to add multiple shapefiles to your project and view them individually.

After adding a vector layer, it's a good idea to save your work: **Project → Save**

Each country contains a list of attributes. You can view these attributes in a table: **Layer → Open Attribute Table**

[![Attribute table](http://media.mediatemple.netdna-cdn.com/wp-content/uploads/2015/07/03-table-opt-small.png)](http://media.mediatemple.netdna-cdn.com/wp-content/uploads/2015/07/03-table-opt.png)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#9">9</a>
</sup>

<br>
The country attribute table. ([View large version](http://media.mediatemple.netdna-cdn.com/wp-content/uploads/2015/07/03-table-opt.png)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#10">10</a>
</sup>

) The Natural Earth data contains a wealth of information about each country. We'll be using [two-letter ISO codes](http://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#11">11</a>
</sup>

 to uniquely identify countries. These are stored in the `iso_a2` variable. We'll also use the country name (`name`) to label countries in our SVG file. Feel free to explore the table to get a sense of the data. When you're done, close the table.

## Editing The Geographic Data [Link](http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#editing-the-geographic-data)

_(You can skip this section if you are happy with the Natural Earth data defaults.)_

It's possible to **edit the individual geographic shapes** using QGIS. You will probably not need to redraw borders, but you may want to split a country into parts.

Each shape in our shapefile is referred to as a _feature_ in QGIS. To select a feature, choose: **View → Select → Select Feature(s)** and then click on your target. The entire feature should turn yellow. For example, this is what France looks like when you click on it:

[![France selected](http://media.mediatemple.netdna-cdn.com/wp-content/uploads/2015/07/04-france-opt-small.png)](http://media.mediatemple.netdna-cdn.com/wp-content/uploads/2015/07/04-france-opt.png)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#12">12</a>
</sup>

<br>
France is selected and highlighted. ([View large version](http://media.mediatemple.netdna-cdn.com/wp-content/uploads/2015/07/04-france-opt.png)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#13">13</a>
</sup>

) Notice that France includes French Guiana in South America. This is because French Guiana is an overseas département and region of France ([French Guiana on Wikipedia](http://en.wikipedia.org/wiki/French_Guiana)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#14">14</a>
</sup>

). Nonetheless, French Guiana has its own unique ISO code and, for many applications, it makes sense to display it as a separate entity.

Splitting France into two separate entities is a straightforward process, but it requires a number of steps:

1. Display editing tools in the toolbar: **View → Toolbars → Advanced Digitizing**; and **View → Toolbars → Digitizing Tools**
2. Put the map in edit mode: **Layer → Toggle Editing**
3. Select France: **View → Select → Select Feature(s)**
4. Click on France in the map view
5. Choose **Split selected multi-part features to single part** from the toolbar.

  ![05-split-opt](http://media.mediatemple.netdna-cdn.com/wp-content/uploads/2015/07/05-split-opt.png)

  This will split all parts of France into separate entities.

6. Put France back together:

  - Select both the mainland of France and the island of Corsica. To select multiple features, hold down Ctrl (Cmd on Mac).
  - Merge the mainland of France and the island Corsica (**Edit → Merge Selected Features**) – you will be alerted that the feature attributes will also be merged. Click **OK**.

7. Edit French Guiana's attributes:

  - **View → Identify Features**
  - Click on French Guiana. This will bring up the attributes for French Guiana. It currently has the attributes for France. We need to replace these with attributes for French Guiana. Click on the **Edit feature form** icon as shown in the screenshot. Replace the ISO code with "GF" and the name with "French Guiana".

  [![Replacing attributes](http://media.mediatemple.netdna-cdn.com/wp-content/uploads/2015/07/06-table-opt-small.png)](http://media.mediatemple.netdna-cdn.com/wp-content/uploads/2015/07/06-table-opt.png)

  <sup>
    <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#15">15</a>
  </sup>

  <br>
  Replacing French Guiana's attributes. ([View large version](http://media.mediatemple.netdna-cdn.com/wp-content/uploads/2015/07/06-table-opt.png)

  <sup>
    <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#16">16</a>
  </sup>

  )

8. Save your changes: **Layer → Save Layer Edits**

That's all! French Guiana is now its own feature and we've given it unique attributes. To learn more about editing, check out the [QGIS documentation](http://docs.qgis.org/testing/en/docs/user_manual/working_with_vector/editing_geometry_attributes.html)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#17">17</a>
</sup>

.

## Creating SVG Files With Kartograph.py [Link](http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#creating-svg-files-with-kartograph-py)

[Kartograph.py](http://kartograph.org/about/kartograph.py/)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#18">18</a>
</sup>

 is a lightweight Python library that will convert our shapefile into a web-friendly SVG file. It was created by New York Times graphics editor [Gregor Aisch](http://driven-by-data.net/)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#19">19</a>
</sup>

 and is available under an AGPL license.

To install Kartograph.py, [follow these installation instructions](http://kartograph.org/docs/kartograph.py/#installing-kartograph-py)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#20">20</a>
</sup>

. This process will require you to install a number of dependencies. If you're not ready to commit to so many installations, you can install Kartograph.py in a virtual machine. Or, you can just download the SVG output shown as images in this article. This tutorial was created using Kartograph.py installed in Ubuntu 14.04 LTS.

Kartograph is a command line program that **requires a JSON configuration file**. Name this file _config.json_ and place it in the same directory as the shapefile. This file must contain a `layers` property with a dictionary of the layers we want to convert and the location of each layer's shapefile. To convert _ne_110m_admin_0_countries_lakes.shp_ we can use the following _config.json_:

```
{
 "layers": {
 "countries": {
 "src": "ne_110m_admin_0_countries_lakes.shp"
 }
 }
}
```

Then execute the following Kartograph command in the console:

```
kartograph config.json -o world_basic.svg
```

Kartograph will create _world_basic.svg_ in the current directory. If you open this file in a modern browser, you will see a map of the world.

See the Pen [gaOXNv](http://codepen.io/smashingmag/pen/gaOXNv/)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#21">21</a>
</sup>

 by Smashing Magazine ([@smashingmag](http://codepen.io/smashingmag)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#26">26</a>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#22">22</a>
</sup>

) on [CodePen](http://codepen.io)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#27">27</a>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#23">23</a>
</sup>

.

SVG world map.

## Changing The Map Projection [Link](http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#changing-the-map-projection)

You may notice that this map looks somewhat different from the map in QGIS. There are a number of different ways to project our three-dimensional Earth into two-dimensional space. By default, **Kartograph uses the Robinson projection**, an aesthetically pleasing projection commonly used for world maps. However, you may prefer to work with the Mercator projection (used by Google Maps) which projects latitude and longitude as straight lines. To do so, add a `proj` object after the `layers` object in the _config.json_ file:

```
"proj": {
 "id":"mercator"
}
```

## Reducing The File Size [Link](http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#reducing-the-file-size)

Often, the SVG file you create may be too large to be practical for web use. Kartograph includes the [Visvalingam-Whyatt simplification algorithm](http://bost.ocks.org/mike/simplify/)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#24">24</a>
</sup>

. This tool makes it possible to dramatically reduce the size of a file with only subtle visual changes to country borders. Our file is already quite small (231KB), but we can simplify it further by adding a `simplify` property to the end of the `countries` property:

```
"simplify": 1
```

The higher the value of `simplify`, the more country borders will be simplified and the smaller the file will become.

## Filtering Out Features [Link](http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#filtering-out-features)

By default, the map will include all the features present in the shapefile. You may, however, want to exclude certain features from the SVG file. For example, we may wish to remove Antarctica since it is of less relevance to our task. Kartograph includes a filter tool that makes it possible to filter features based on their attributes. We can exclude Antarctica using its ISO code. To do this, add a `filter` property to the `countries` layer:

```
"filter": ["iso_a2", "not in", ["AQ"]],
```

## Saving Data Within The SVG File [Link](http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#saving-data-within-the-svg-file)

Finally, we want our SVG map to include data attributes with each country's ISO code and name. This will allow us to identify SVG elements in the browser. To do this, add an `attributes` property to the `countries` layer:

```
"attributes": {
 "id": "iso_a2", 
 "name": "name"
},
```

## Putting It All Together [Link](http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#putting-it-all-together)

After making these changes, _config.json_ will consist of the following:

```
{
 "layers": {
 "countries": { 
 "src": "ne_110m_admin_0_countries_lakes.shp",
 "filter": ["iso_a2", "not in", ["AQ"]],
 "attributes": {
 "id": "iso_a2",
 "name": "name"
 },
 "simplify": "1"
 }
 },
 "proj": {
 "id": "mercator"
 }
}
```

Execute the same Kartograph command as before (with an updated file name):

```
kartograph config.json -o world.svg
```

Kartograph will create a new map _world.svg_ that is smaller and looks quite different:

See the Pen [LpYOob](http://codepen.io/smashingmag/pen/LpYOob/)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#25">25</a>
</sup>

 by Smashing Magazine ([@smashingmag](http://codepen.io/smashingmag)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#26">26</a>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#22">22</a>
</sup>

) on [CodePen](http://codepen.io)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#27">27</a>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#23">23</a>
</sup>

.

The new SVG world map. If you open this file in a text editor, you will see that each `<path>` now includes identifying data in the following form:

```
data-id="US" data-name="United States" id="US"
```

This introduction only scratches the surface of what is possible with Kartograph. You can use multiple layers, join features together, and frame the map using latitude and longitude. You can learn about these options and more in the [Kartograph.py documentation](http://kartograph.org/docs/kartograph.py/)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#28">28</a>
</sup>

.

## Adding The SVG Map To A Website [Link](http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#adding-the-svg-map-to-a-website)

Now that you've created an SVG map, you'll probably want to customize it and add it to your website. Modern browsers support SVG natively so you can add an SVG image to your webpage just like you would add any other image:

```
<img src="world.svg" alt="World Map">
```

However, embedding the map this way makes it difficult to style the map and make it interactive. A simple way to apply CSS and JavaScript to the map is to first embed the SVG file inline. Simply open _world.svg_ in a text editor, copy the entire `<svg>` element, and paste it directly into your webpage.

The `<svg>` element contains a separate `<path>` for every country. To style the map, simply apply CSS to all `<path>` elements. Use the `fill` and `stroke` properties to style each country's color and border respectively. For example, we can make all countries light gray and add a hover effect:

```
 path {fill: lightgray; stroke: white;}
 path:hover {fill: gray;}
```

Similarly, we can use JavaScript to define event handlers. The following snippet will alert each country's name with a click:

```
<script>
 // <![CDATA[
 var countryElements = document.getElementById('countries').childNodes;
 var countryCount = countryElements.length;
 for (var i = 0; i < countryCount; i++) {
 countryElements[i].onclick = function() {
 alert('You clicked on ' + this.getAttribute('data-name'));
 }
 }
 // ]]>
</script> 
```

This should be placed after the inline `<svg>` element and right before the closing `<body>` tag. Your full HTML file should look like the example [provided in this Gist](https://gist.github.com/youderian/fbff53fe18b13116adf5)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#29">29</a>
</sup>

.

Open this file in a browser, and you should see an interactive map of the world.

[![Interactive map](http://media.mediatemple.netdna-cdn.com/wp-content/uploads/2015/07/09-interactive-world-opt-small.png)](http://media.mediatemple.netdna-cdn.com/wp-content/uploads/2015/07/09-interactive-world-opt.png)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#30">30</a>
</sup>

<br>
Hopefully, this simple example illustrates how you can easily manipulate your SVG map with JavaScript and CSS. For more advanced interactions, such as animating SVG paths, check out an SVG JavaScript library like [Snap.js](http://snapsvg.io/)

<sup>
  <a href="http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#31">31</a>
</sup>

.

## Conclusion [Link](http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#conclusion)

Interactive maps can be intimidating, but they don't have to be a black box. You can create your own custom SVG maps with open data and software. In no time at all, you will have enhanced your website with a beautiful, fully customizable, interactive map.

_(ml, ds, og)_

[↑ Back to top](http://www.smashingmagazine.com/2015/09/making-svg-maps-from-natural-earth-data/#top) [Tweet it](https://twitter.com/intent/tweet?original_referer=http%3A%2F%2Fwww.smashingmagazine.com%2F2015%2F09%2Fmaking-svg-maps-from-natural-earth-data%2F&source=tweetbutton&text=A%20Guide%20To%20Building%20SVG%20Maps%20From%20Natural%20Earth%20Data&url=http%3A%2F%2Fwww.smashingmagazine.com%2F2015%2F09%2Fmaking-svg-maps-from-natural-earth-data%2F&via=smashingmag)[Share on Facebook](http://www.facebook.com/sharer/sharer.php?u=http%3A%2F%2Fwww.smashingmagazine.com%2F2015%2F09%2Fmaking-svg-maps-from-natural-earth-data%2F)
