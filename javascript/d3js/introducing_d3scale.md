# Introducing d3-scale

[Original URL](https://medium.com/@mbostock/introducing-d3-scale-61980c51545f#.ksp3lylvr)

> I'd like D3 to become the standard library of data visualization: not just a tool you use directly to visualize data by writing code, but also a suite of tools that underpin more powerful software.To...

I'd like D3 to become the standard library of data visualization: not just a tool you use directly to visualize data by writing code, but also a suite of tools that underpin more powerful software.

To this end, D3 espouses abstractions that are useful for any visualization application and rejects the tyranny of charts.

![](https://cdn-images-1.medium.com/max/800/1*lgRTO__UEihFlgbeKRz5ng.png)

As Leland Wilkinson wrote in [_The Grammar of Graphics_](https://books.google.com/books?id=_kRX4LoFfGQC),

> If we endeavor to develop a charting instead of a graphing program, we will accomplish two things. First, we inevitably will offer fewer charts than people want. Second, our package will have no deep structure. Our computer program will be unnecessarily complex, because we will fail to reuse objects or routines that function similarly in different charts. And we will have no way to add new charts to our system without generating complex new code. Elegant design requires us to think about a theory of graphics, not charts.

If visualization is constructing "visual representations of abstract data to [amplify cognition](https://books.google.com/books?id=wdh2gqWfQmgC)", then perhaps the most important concept in D3 is the _scale_, which maps a dimension of abstract data to a visual variable.

And now scales are available in a standalone library, [d3-scale](https://github.com/d3/d3-scale).

But what is a "dimension" of data? Or a "visual variable"? Consider a table of data, as in a spreadsheet. Each row in the table is a vector, and each column is a dimension. A dimension is just a named attribute whose values have a particular meaning, such as a price in dollars.

![](https://cdn-images-1.medium.com/max/800/1*V6UFFEUmE6Hv7wv5TqU-Bg.png)

We typically think of dimensions as spatial and quantitative, such as a position in space represented by real numbers ⟨_x, y, z_⟩. Yet with abstract data there are also non-quantitative dimensions; for example, diamond cut quality (fair, good, very good, ideal) is ordinal, while diamond cut shape (princess, round, marquise, _etc._) is categorical.

Visual variables are best explained by Jacques Bertin in [_Semiology of Graphics_](http://esripress.esri.com/display/index.cfm?fuseaction=display&websiteID=190)_._ He described how graphical marks (say, dots in a scatterplot) can represent data using planar position ⟨_x, y_⟩ and a luminous dimension _z_:

> Within the plane a mark can be at the top or the bottom, to the right or the left. The eye perceives two independent dimensions along X and Y, which are distinguished orthogonally. A variation in light energy _**_ produces a third dimension in Z, which is independent of X and Y...

> The eye is sensitive, along the Z dimension, to 6 independent visual variables, which can be superimposed on the planar figures: the size of the marks, their value, texture, color, orientation, and shape. They can represent differences (≠), similarities (≡), a quantified order (Q), or a nonquantified order (O), and can express groups, hierarchies, or vertical movements.

![](https://cdn-images-1.medium.com/max/800/1*jsb78Rr2cDy6zrE7j2IKig.png)

From [_Semiology of Graphics_](http://esripress.esri.com/display/index.cfm?fuseaction=display&websiteID=190), colorized by the author. Thus, a scale is a function that takes an _abstract value_ of data, such as the mass of a diamond in carats, and returns a _visual value_ such as the horizontal position of a dot in pixels. With two scales (one each for _x_ and _y_), we have the basis for a scatterplot.

![](https://cdn-images-1.medium.com/max/1200/1*DwPi4qo-Jf01Y0jdRuZrEg.png)

The relationship between diamond mass _and_ price. [View source.](http://bl.ocks.org/mbostock/ebb45892cc6ec5e6c902)

To illustrate how scales work, imagine how you might compute _x_ and _y_ for each dot above. Given some values derived from data (minCarat, maxCarat, minPrice, maxPrice) and some from the chart size (width, height), you might do something like this:

```
function x(carat) {
 return (carat - minCarat)
 / (maxCarat - minCarat)
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii width;
}

function y(price) {
 return height
 - (price - minPrice)
 / (maxPrice - minPrice)
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii height;
}
```

The lightest diamond is placed at the chart's left edge, the heaviest diamond is placed at the chart's right edge, and so on. Note that the range of the _y_-scale is inverted because graphics systems put the origin in the top-left corner whereas scatterplots put it in the bottom-left.

Like the above, D3's quantitative scales are functions configured by two intervals. The input _domain_ is an interval in the abstract dimension of data, often the [extent](https://github.com/d3/d3-array#extent) of the observed values. The output _range_ is an interval in the visual variable, such as the visible area defined by the chart size.

```
var x = d3_scale.linear()
 .domain(d3_array.extent(data, function(d) { return d.carat; }))
 .range([0, width]);

var y = d3_scale.linear()
 .domain(d3_array.extent(data, function(d) { return d.price; }))
 .range([height, 0]);
```

But scales do much more than basic arithmetic!

For one, it is now trivial to apply quantitative transformations: replace a [linear](https://github.com/d3/d3-scale#linear) scale with a [logarithmic](https://github.com/d3/d3-scale#log-scales) or [power](https://github.com/d3/d3-scale#power-scales) scale. A linear scale is a good default choice because it preserves proportionality, but a log or pow scale may aid the differentiation of data that is not uniformly distributed. (Log scales are also good for [showing change](http://bl.ocks.org/mbostock/c69f5960c6b1a95b6f78).)

![](https://cdn-images-1.medium.com/max/1200/1*_IYHXdjCloGggu03_OCpcw.png)

The previous scatterplot modified to use log scales. [View source.](http://bl.ocks.org/mbostock/c3034eef9d73b5fdf274)

For two, scales alleviate the tedium of drawing legible axes by [generating](https://github.com/d3/d3-scale#continuous_ticks) and [formatting](https://github.com/d3/d3-scale#continuous_tickFormat) nice, round values (_ticks_) from the domain. A scale's ticks are type-appropriate: for example, the log ticks above are uniformly-spaced within each power of ten, while a time scale uses calendar intervals.

Most scales are bidirectional_:_ you can [invert](https://github.com/d3/d3-scale#continuous_invert) the mapping from visual representation back to abstract data, facilitating interaction. For example, a brushed interval in pixels can be inverted to abstract data for querying.

![](https://cdn-images-1.medium.com/max/800/1*5V0nv0k69NyNcVtSjAiyqA.gif)

Brushing a scatterplot matrix. [View source.](http://bl.ocks.org/mbostock/4063663) And there are scales for ordinal and categorical data. The [band](https://github.com/d3/d3-scale#band-scales) scale, for instance, simplifies the calculation of bar widths and positions, allowing configurable padding, alignment and rounding.

![](https://cdn-images-1.medium.com/max/800/1*cIYJvJvpPlsC0i1eD69nqw.png)

The frequency of English letters. [View source.](http://bl.ocks.org/mbostock/946ddf8a32b3b660ffd8) Yet scales are not just for positioning; they are for computing _any_ visual variable. Scales can [interpolate](https://github.com/d3/d3-scale#continuous_interpolate) symbol sizes, font sizes, stroke widths, colors in various color spaces, geometric transforms, shapes and even deeply-nested objects. Below, a scale represents quantity using angular orientation, with small numbers leaning left (\) and large numbers leaning right (/). This reveals the behavior of a sorting algorithm on an array of numbers:

![](https://cdn-images-1.medium.com/max/1200/1*IUn5Qb-VneCovMfH8PqAsA.png)

Visualizing quicksort. From [Visualizing Algorithms.](http://bost.ocks.org/mike/algorithms/)

Below, a square-root scale computes the appropriate radius so that the area of each county's bubble is proportional to the number of people living there:

![](https://cdn-images-1.medium.com/max/1200/1*K1xOjX_qc_6TtP7hU_o5hA.png)

Population in 2008\. From [Let's Make a Bubble Map](http://bost.ocks.org/mike/bubble-map/).

Below, a comparison of perceptually-uniform sequential color scales used for a choropleth of unemployment rate:

![](https://cdn-images-1.medium.com/max/400/1*0ptljP-upBOkJG0_8o05zg.png)

![](https://cdn-images-1.medium.com/max/400/1*bG7dyONArCRSUSJFgFFehA.png)

![](https://cdn-images-1.medium.com/max/400/1*MAOWJ7_NI5yVrYDUzcRJug.png)

Unemployment in 2008, using magma, viridis and cubehelix. Darker colors indicate a higher unemployment rate. [View source.](http://bl.ocks.org/mbostock/4060606)

You can even create piecewise scales for diverging colors, or quantize scales for applying discrete breaks to continuous data.

So, [try it out](https://github.com/d3/d3-scale)! And check out the other [new D3 modules](https://github.com/d3), too, such as [d3-time](https://github.com/d3/d3-time), [d3-format](https://github.com/d3/d3-format), and [d3-shape](https://medium.com/@mbostock/introducing-d3-shape-73f8367e6d12).

Happy scaling!
