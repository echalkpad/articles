# How To Infer Topology

[Original URL](https://bost.ocks.org/mike/topology/)

> bl.ocks.org/6406992 As its name implies, TopoJSON is a topological geospatial data format. In contrast to a geometry, where each shape is encoded with separate (and often redundant) arrays of...

[bl.ocks.org/6406992](https://bl.ocks.org/mbostock/6406992) As its name implies, [TopoJSON](https://github.com/mbostock/topojson) is a _topological_ geospatial data format. In contrast to a geometry, where each shape is encoded with separate (and often redundant) arrays of coordinates, a topology encodes sequences of coordinates in line fragments called _arcs_ that can be shared. For example, the border between California and Nevada is an arc that is shared by both polygons. The various arcs of the U.S. state borders are shown above.

The main benefit of a topology is that it improves [shape simplification](http://www.jasondavies.com/maps/simplify/) by avoiding artifacts that would be caused by simplifying shapes independently. It also enables applications like [map coloring](https://bl.ocks.org/mbostock/4180634) and [selective meshing](https://bl.ocks.org/mbostock/5707610), and makes the format more compact by removing redundant coordinates.

Yet because most geospatial data formats (other than [E00](http://indiemaps.com/blog/2009/02/e00parser-an-actionscript-3-parser-for-the-arcinfo-export-topological-gis-format/)) are [non-topological](http://www.esri.com/news/arcuser/0401/topo.html), you must typically create TopoJSON by **inferring the topology from geometry**. This article documents my implementation, partly for general interest and partly to help you debug conversion problems when using the [`topojson` command-line tool](https://github.com/mbostock/topojson/wiki/Command-Line-Reference). This can also serve as a guide should you be interested in implementing TopoJSON encoding in another language.

The algorithm has four steps:

1. [extract](https://bost.ocks.org/mike/topology/#extract) - decompose shapes into lines and rings.
2. [join](https://bost.ocks.org/mike/topology/#join) - identify junctions (intersection points).
3. [cut](https://bost.ocks.org/mike/topology/#cut) - split or rotate arcs to terminate at junctions.
4. [dedup](https://bost.ocks.org/mike/topology/#dedup) - consolidate duplicate arcs.

Each step is implemented in a [source file](https://github.com/mbostock/topojson/tree/master/lib/topojson/topology) of the same name, should you wish to follow along.

## [#](https://bost.ocks.org/mike/topology/#extract)1\. Extract

The minimum information needed to infer the topology is the set of all lines and rings extracted from the input geometry. This extraction simplifies the downstream logic; for example, the topology doesn't care whether two polygons are represented as two Polygon geometries or one MultiPolygon geometry, as both are simply two rings.

For definitions of these geometry types, see the [GeoJSON specification](http://geojson.org/geojson-spec.html). Extracting lines and rings is a straightforward decomposition of the input geometries: a LineString is converted to one line, a MultiLineString is converted to zero or more lines, a Polygon is converted to one or more rings, and so on. The algorithm must also record which lines and rings are associated with which input geometries, so that the geometry can be recomposed from the final topology when the conversion is complete.

This animation demonstrates extraction on U.S. state borders:

[bl.ocks.org/6408735](https://bl.ocks.org/mbostock/6408735) The order of rings is arbitrary, though note that rings for each state are extracted in sequence, since a state with multiple polygons in this example is represented as a MultiPolygon.

Why differentiate between lines and rings? For topologies, rings are a special form of closed lines that can be rotated: the start of the ring can be changed to any point along the ring without affecting the geometry. This special property can be used [later](https://bost.ocks.org/mike/topology/#cut) to reduce the number of arcs that need to be cut, optimizing the topology.

## [#](https://bost.ocks.org/mike/topology/#join)2\. Join

The next step is to identify junctions: shared points where two or more lines (or rings) intersect. For example, consider the line _ABC_, where _A_, _B_ and _C_ are distinct points. If there is another line _DBE_, then _B_ is a junction. Similarly, if there are two lines _ABC_ and _ABD_, then because the lines diverge at point _B_, _B_ is a junction. A few simple examples:

[bl.ocks.org/6409154](https://bl.ocks.org/mbostock/6409154) (Note: if two line segments intersect, but don't share a point, this intersection is ignored. The topology only cares how points are connected, and whether they are distinct, not their positions.)

Junctions are irrespective to order. So, given two lines _ABC_ and _CBA_, then _B_ is not a junction -- unless there is some other line that intersects _B_. For the purposes of topology construction, we also consider the start and end point of every line to be a junction. This simplifies the code slightly, and has no detrimental effect because it is not necessary to cut a line at a given junction if the line already terminates at that junction.

The junctions for the U.S. state borders can be seen here as black dots:

[bl.ocks.org/6408918](https://bl.ocks.org/mbostock/6408918) As you expect, the border intersections in the contiguous United States are correctly identified. You can see a few additional junctions on the coasts, such as on the Gulf of Mexico and Puget Sound. These junctions are caused by coastal features that are too small to see in the current map, such as adjacent islands that share a single point. Independent rings, such as the Hawaiian islands, have no junctions; as before, this is because rings can be freely rotated.

A [hashtable](https://github.com/mbostock/topojson/tree/master/lib/topojson/topology/hashtable.js) is a reasonable way to record neighbors. However, note that the hashtable can be as large as the input geometry! [Linear probing](http://en.wikipedia.org/wiki/Linear_probing) provides a noticeable performance improvement over array buckets. The process of identifying junctions requires iterating over each line and ring while recording the neighbors of each visited point. If a point is subsequently revisited and has a _different_ set of neighbors than before, then the point is a junction. For example, given the two lines _ABC_ and _ABD_, the first time we visit _B_, the neighbors are {_A_, _C_}. The second time we visit _B_, the new neighbors are {_A_, _D_}, which is different than before, and thus _B_ is a junction.

## [#](https://bost.ocks.org/mike/topology/#cut)3\. Cut

With all junctions identified, the next step is to cut the lines and rings so that they terminate at the junctions. For example, if _B_ is a junction, then the line _ABC_ is split into two lines _AB_ and _BC_, while the line _ABD_ is split into _AB_ and _BD_. Splitting lines at junctions guarantees that all shared point sequences (like _AB_ and _AB_) will be correctly [deduped](https://bost.ocks.org/mike/topology/#dedup), and can be performed by simply iterating over the lines and rings while querying the set of previously-computed junctions.

This animation demonstrates cutting on U.S. state borders:

[bl.ocks.org/6409668](https://bl.ocks.org/mbostock/6409668) Red indicates the section of the ring (or line) before the cut, while the blue indicates the section after the cut. The blue part gets successively smaller as the ring is cut at each junction.

For the first junction encountered on a ring, we can rotate the ring so that it starts on the junction, eliminating a cut and reducing the number of arcs in the generated topology. For example, given the ring _ABCA_, if _B_ is a junction, then we can rotate the ring to the equivalent sequence _BCAB_. This produces a smaller topology than cutting the ring into _AB_ and _BCA_. This treatment could be applied as well to closed lines, but since the start and end point of a closed line may be meaningful -- say, [Magellan's circumnavigation of the globe](http://en.wikipedia.org/wiki/Timeline_of_Magellan's_circumnavigation) -- it's safer to restrict this optimization to rings. Ring rotation is not shown explicitly in the above animation, but notice that the first red cut on the ring always occurs on a junction, not at the ring's original start point.

## [#](https://bost.ocks.org/mike/topology/#dedup)4\. Dedup

With the lines and rings cut into arcs, the last step is to deduplicate arcs so that each shared sequence of points is encoded only once in the final topology. Again, this requires iterating over the arcs that comprise each line or ring, and comparing the current arc to the arcs that were visited previously. If the current arc is different from all previously-visited arcs, it is assigned a new unique identifier (0, 1, 2... _etc._). However if it is the same, it is given the identifier from the identical previous arc, and thus deduplicated.

This animation demonstrates deduplicating cut arcs on U.S. state borders:

[bl.ocks.org/6409844](https://bl.ocks.org/mbostock/6409844) The first time an arc is seen, it flashes red. If it is revisited (and thus duplicated), it flashes blue.

A [hashtable](https://github.com/mbostock/topojson/tree/master/lib/topojson/topology/hashtable.js) is a reasonable way to index arcs, so as to reduce the number of pairwise arc-equality comparisons. You could either compute a hash code for each arc, or index arcs by endpoint. Detecting whether two arcs are the same is mostly a matter of iterating over the points in the arc and seeing whether they are equal, similar to string comparison. However, since arcs can be reversed, it's necessary to perform a backwards traversal if the forwards traversal fails.

Comparing rings is further complicated in that two rings may be rotated but still equivalent. This is resolved by picking a consistent rotation-invariant starting point on both rings before traversing, such as the point with the minimum _x_-coordinate, or the minimum _y_-coordinate if there are multiple points at the minimum _x_-coordinate.

## [#](https://bost.ocks.org/mike/topology/#further-reading)Further Reading

The implementation described in this article is new for [TopoJSON 1.4.0](https://github.com/mbostock/topojson/releases/v1.4.0). (It differs substantially from the old implementation; after this rewrite, I'm amazed the old implementation worked at all, though I'm glad there's a respectable test suite!)

An issue of particular importance to topology inference is messy inputs. For example, if you have two points [-122.416712304917, 37.783305712306] and [-122.416712304916, 37.783305712306], then in regards to the topology they are as distinct as San Francisco and New York. The TopoJSON reference implementation eliminates many of these errors by quantizing inputs, rounding them to lower precision. Quantization gives excellent results when constructing topologies from multiple layers, such as county, state and nation borders. However, overly-quantized inputs can introduce excessive arc self-intersection, resulting in a big topology. So user control over quantization, and perhaps more heuristics, are still needed for dealing with "real-world" inputs. Or we could all start using topological formats...

If you have questions or comments, please find [me on Twitter](http://twitter.com/mbostock).

September 2, 2013 [Mike Bostock](https://bost.ocks.org/mike/)
