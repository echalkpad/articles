# Interactive JavaScript Charts Using Data from Google Sheets

[Original URL](http://www.sitepoint.com/interactive-javascript-charts-using-data-from-google-sheets/)

> This article was peer reviewed by Jeff Smith. Thanks to all of SitePoint's peer reviewers for making SitePoint content the best it can be! Meet my friend Jenny. She recently started coding...

_This article was peer reviewed by [Jeff Smith](http://www.sitepoint.com/author/jeffsmith). Thanks to all of SitePoint's peer reviewers for making SitePoint content the best it can be!_

Meet my friend Jenny. She recently started coding websites. She loved her job and was very happy until she met Steve, who has a reputation of being, let's say, not a very easygoing client.

Steve had a lot of data in a spreadsheet and he wanted to display that on his website. Our friend (now Jenny is your friend too!) suggested that Steve make the charts in Excel and upload them as images to the website.

But Steve being Steve, he wanted the charts to be interactive. Not only that, he also wanted the charts to get updated whenever he made a change to the data in his spreadsheet .

Jenny didn't know how to tackle this issue, so she came to me. And, being the good friend I am, I gave her this advice:

First ask your client to move his data to Google Sheets (because that's what all the cool people are doing nowadays). Then we can easily implement the features that he needs -- interactive JavaScript charts and dynamic updating.

Her client agreed (luckily!), and Jenny and I coded rest of the solution. But how? Well, that's what this tutorial will teach you.

I have divided this tutorial into five easy-to-follow steps:

1. Exporting data from Google Sheets
2. Fetching JSON from Google Sheets via Ajax
3. Restructuring the data
4. Creating a chart with FusionCharts.
5. Customizing the chart

So without further ado, let's dive in!

## Exporting Google Sheets Data as JSON

Before delving into how to export data, let's first create a sheet. Assuming you've got a Google account, you can do this by going to the [Google Sheets page](https://docs.google.com/spreadsheets/) and hitting the _Start a new spreadsheet_ button. In the spreadsheet that opens, create two columns: _Actor_ and _Income_. Then fill your newly created sheet with some data. I've taken mine from here: [The World's Highest-Paid Actors 2015](http://www.forbes.com/pictures/emjl45mmei/the-worlds-highest-paid-/).

As you can see, the left column contains labels for our chart, the right one values corresponding to those labels. For those of you following along at home, you can [grab a copy of this sheet here](https://docs.google.com/spreadsheets/d/1Aoz_GcPYoEIMMNd1N_meYNOp8TJ0fCXpp1AoUhCpwZo/edit#gid=0) (go to _File_ > _Make a copy_).

The data available in Google Sheets can be exported to multiple formats like JSON, XML etc. And once exported to any of those formats, it can be easily accessed via the web.

To open your document to the web, you need to make the following changes to the document settings:

- Set the sharing of the document to either _Public on the web_, or _Anyone with the link_. To achieve this, click the _Share_ button in the top right-hand corner, then click the _Advanced_ option that appears in the bottom right of the pop up.
- Publish the document to the web. This option is available under _File_ > _Publish to the web_

With these two changes made, the data in the document can be accessed in JSON format via: <https://spreadsheets.google.com/feeds/list/SPREADSHEET/od6/public/basic?alt=json>

You will need to replace `SPREADSHEET` with the ID of your Google Sheet, which in our case is `1Aoz_GcPYoEIMMNd1N_meYNOp8TJ0fCXpp1AoUhCpwZo`. You can see the [results here](https://spreadsheets.google.com/feeds/list/1Aoz_GcPYoEIMMNd1N_meYNOp8TJ0fCXpp1AoUhCpwZo/od6/public/basic?alt=json).

## Using jQuery to Fetch JSON from Google Sheets

We will use [jQuery's get()](https://api.jquery.com/jquery.get/) method to fetch the data from Google Sheets. You can include jQuery from a CDN as shown:

```
<script type="text/javascript" src="https://code.jquery.com/jquery-1.12.0.min.js"></script>
```

With the `<script>` tag added, call `$.get` on the the spreadsheet URL we have created.

```
var spreadsheetId = "1Aoz_GcPYoEIMMNd1N_meYNOp8TJ0fCXpp1AoUhCpwZo",
 url = "https://spreadsheets.google.com/feeds/list/" +
 spreadsheetId +
 "/od6/public/basic?alt=json";

$.get({
 url: url,
 success: function(response) {
 console.log(response);
 }
});
```

The above jQuery code makes an Ajax request to the Google Sheets URL, and upon successfully fetching the data, it calls the `success` function. This function logs the data returned from the server to the console. I would encourage you to run this script and take a moment to examine exactly what is returned.

## Restructuring the Data

The JSON data from Google Sheets needs to be restructured a little to match the data format accepted by our visualization library. For this tutorial we'll be using [FusionCharts](http://www.fusioncharts.com).

FusionCharts accepts JSON data as an array of objects containing `label` and `value` keys. This is what it looks like:

```
[{
 "label": "Robert Downey Jr.",
 "value": "80"
}
...]
```

To achieve this, we need to run following piece of code in the `success` callback of the jQuery `get` function we defined earlier.

```
var data = response.feed.entry,
 len = data.length,
 i = 0,
 parsedData = [];

for (i = 0; i < len; i++) {
 parsedData.push({
 label: data[i].title.$t,
 value: data[i].content.$t.replace('Income: ', '')
 });
}
```

What we are doing here is iterating over each Google Sheet data object present in `response.feed.entry` and extracting the `label` and `value` out of it. Then we are storing them in a new array `parsedData`, which will be used by the chart.

The `label` is available in the `title.$t` key and the value is available in `content.$t` key of the data object. The value though contains the sheet column name, which is not necessary, hence we strip it out. The rest of the data in the Google Sheet is not needed for the visualization.

The `parsedData` array now contains data in a format which will be understood by FusionCharts and we can continue on with creating the visualization.

## Step-3: Creating the Chart

Now with our data ready and accessible, we can move on to creating charts. Creating the chart involves the following steps:

- Creating a `<div>` inside which the chart will be rendered.
- Creating a FusionCharts instance using the parsed data from the Google Sheet.
- Calling the `render` method on the FusionCharts instance to render it in the `div`.

In the markup we include the FusionCharts library via a CDN:

```
<script src="http://static.fusioncharts.com/code/latest/fusioncharts.js"></script>
```

As well as a containing element for the chart. This can contain some place holder text which will display before the chart renders.

```
<div id="chart-container">
 <p>The chart will render here!</p>
</div>
```

To actually create and render a basic horizontal bar chart into `#chart-container`, we'll add the following code to the `success` callback:

```
new FusionCharts({
 type: 'bar2d',
 renderAt: 'chart-container',
 width: '100%',
 height: '300',
 dataFormat: 'json',
 dataSource: {
 "chart": {
 "caption": "Highest Paid Actors",
 "yAxisName": "Annual Income (in milion USD)",
 "numberPrefix": "$"
 },
 "data": parsedData
 }
}).render();
```

Let's look at some of the configuration options we have defined for the chart:

- `type`: defines the chart type we are going to plot – `bar2d` in this example.
- `height`: height of the chart in pixels.
- `width`: width of the chart.
- `chart`: here we can set things like title, labels for both axes, a value prefix, etc.
- `data`: the data the chart will be constructed from.

And here's a demo of what we've got so far:

See the Pen [JavaScript Charts + Google Sheets](http://codepen.io/SitePoint/pen/rxorZd/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

## Customizing the Chart

Whilst the above demo works, it looks perhaps a little generic. Luckily you can customize almost every aspect of a chart's appearance. FusionCharts has some excellent [documentation](http://www.fusioncharts.com/dev/chart-attributes.html?chart=bar2d), which I suggest you check out.

Here's the same chart customized to look a little more, ahem, appealing.

See the Pen [JavaScript Charts + Google Sheets (2)](http://codepen.io/SitePoint/pen/adXzae/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

## Conclusion

As you have seen, it is not so difficult to create a live chart from Google Sheets. Live JavaScript charts can even also be generated for complex chart types, such multi-series charts. The only thing that needs to be worked out for complex charts is how to restructure the data from Google Sheets to the format accepted by the chart. Everything else remains the same.

PS: I will be hanging around in the comments section below So feel free to shoot any questions you have about this tutorial. I'more than happy to help!
