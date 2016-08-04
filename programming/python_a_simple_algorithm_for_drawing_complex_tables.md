# [Python] A simple algorithm for drawing complex tables

[Original URL](https://snai.pe/python/algorithm/rendering-tables/)

> I was surprised to see, during the development of rst2ansi, that there were no simple python modules to pretty-print complex tables with ascii characters. Most of them only supported simple tables...

I was surprised to see, during the development of [rst2ansi](https://github.com/Snaipe/python-rst2ansi), that there were no simple python modules to pretty-print complex tables with ascii characters.

Most of them only supported simple tables with uni-line cells, and God forbid you want to print cells that span multiple columns or rows.

I went on and made a little algorithm to help me draw these tables.

## The algorithm

We want to draw an arbitrary table, for instance:

```
+-----+-----+-----+-----+
| A | B | C | D |
+-----+-----+-----+-----+
| E | F |
+-----+-----+-----------+
| G | | |
+-----+ I | J |
| H | | |
+=====+=====+===========+
```

The first thing we need to define is what exactly constitute a row in this table, and a traversal order for each cell:

Once done, we can straight-forwardly draw the table by iterating over the cells using the above order:

1. Draw the top and left table borders
2. For each cell draw its bottom and right borders.

Here is a step-by-step visualisation of the process for the above table:

Step 10 A B C D E F G I J

Step 11 A B C D E F G I J H

Step 12 A B C D E F G I J H

(Note that this algorithm could be used to draw tables on any medium, not just for ascii pretty-printing.)

It is immediately apparent that for this algorithm to work, it needs precise information on the table:

- It needs, of course, the number of rows and columns and the contents of the cells,
- It needs the layout of each cell, i.e. which cells spans multiple rows and/or columns.
- It needs the height of each row and the width of each column.

This means that we have to define a proper data structure to represent these informations.

### The implementation

## Setting up the stage

The first thing we have to define is the way to represent our data. Given a table like this:

```
+------------------------+------------+----------+----------+
| Header row, column 1 | Header 2 | Header 3 | Header 4 |
| (header rows optional) | | | |
+========================+============+==========+==========+
| body row 1, column 1 | column 2 | column 3 | column 4 |
+------------------------+------------+----------+----------+
| body row 2 | Cells may span columns. |
+------------------------+------------+---------------------+
| body row 3 | Cells may | Cells may span both |
+------------------------+ span rows. | rows and columns. |
| body row 4 | | |
+========================+============+=====================+
```

What would be the best way to represent it?

One could think at first to use multi-dimensional list, but we get hit in the face when trying to deal with cells spaning multiple columns and rows.

It turns out the best format to represent a table with, albeit a little (and by that I mean "lot") verbose, is to use a tree:

```
 1 TABLE = {
 2 'node': 'table',
 3 'children': [
 4 {
 5 'node': 'head',
 6 'children': [
 7 {
 8 'node': 'row',
 9 'children': [
10 {'node': 'cell', 'data': 'Header row, column 1\n(header rows optional)'},
11 {'node': 'cell', 'data': 'Header 2'},
12 {'node': 'cell', 'data': 'Header 3'},
13 {'node': 'cell', 'data': 'Header 4'},
14 ]
15 }
16 ]
17 },
18 {
19 'node': 'body',
20 'children': [
21 {
22 'node': 'row',
23 'children': [
24 {'node': 'cell', 'data': 'body row 1, column 1'},
25 {'node': 'cell', 'data': 'column 2'},
26 {'node': 'cell', 'data': 'column 3'},
27 {'node': 'cell', 'data': 'column 4'},
28 ]
29 },
30 {
31 'node': 'row',
32 'children': [
33 {'node': 'cell', 'data': 'body row 2'},
34 {'node': 'cell', 'data': 'Cells may span columns.', 'morecols': 2},
35 ],
36 },
37 {
38 'node': 'row',
39 'children': [
40 {'node': 'cell', 'data': 'body row 3'},
41 {'node': 'cell', 'data': 'Cells may span rows.', 'morerows': 1},
42 {'node': 'cell', 'data': 'Cells may span both rows and columns.', 'morerows': 1, 'morecols': 1},
43 ],
44 },
45 {
46 'node': 'row',
47 'children': [
48 {'node': 'cell', 'data': 'body row 4'},
49 ],
50 }
51 ]
52 }
53 ]
54 }
```

This doesn't really look easy to make, but it matters not: we will only use this format as an intermediate structure, meant to be generated from another source.

Looks familiar? It just so happens that this structure looks a little bit like the one HTML uses:

```
 1 <table>
 2 <thead>
 3 <tr>
 4 <td>Header row, column 1<br>(header rows optional)</td>
 5 <td>Header 2</td>
 6 <td>Header 3</td>
 7 <td>Header 4</td>
 8 </tr>
 9 </thead>
10 <tbody>
11 <tr>
12 <td>body row 1, column 1</td>
13 <td>column 2</td>
14 <td>column 3/td>
15 <td>column 4/td>
16 </tr>
17 <tr>
18 <td>body row 2</td>
19 <td colspan="3">Cells may span columns.</td>
20 </tr>
21 <tr>
22 <td>body row 3</td>
23 <td rowspan="2">Cells may span rows.</td>
24 <td rowspan="2" colspan="2">Cells may span both rows and columns.</td>
25 </tr>
26 <tr>
27 <td>body row 4</td>
28 </tr>
29 </tbody>
30 </table>
```

We could make a function to transform these HTML tables into our intermediate format, for instance.

### Sizing the table up

The other data we need for this algorithm to work is the dimensions of each row and columns.

The implementation of this function depends on the use case and the strategy you want. Some possible ideas:

- Find the width of a column:

  - Use a fixed width
  - For each column, use the mean of widths of all cell contents in this specific column
  - Use the length of the column label

- Find the height of a row:

  - Use the maximum number of lines the contents of each cell in a row can fill.

### Implementing the algorithm

The major component of the algorithm is the iteration over the cells. Since we defined a tree-like data structure, a good iteration technique is walking over each node with a visitor.

We start by defining a `Visitor` class that implements `visit`. `visit` is a method that calls `'visit_' + node_name` on the node, visits all of its children, then calls `'depart_' + node_name`.

We also define a `SkipChildren` exception, which, if raised by a `visit_` function, will tell the visitor not to visit the children of the current node.

```
 1 class SkipChildren(Exception):
 2 pass
 3 
 4 class Visitor(object):
 5 
 6 def visit(self, node):
 7 """
 8  Visit a generic node. Calls 'visit_' + node_name on the node,
 9  then visit its children, then calls 'depart_' + node_name.
10  """
11 def noop(node):
12 pass
13 
14 try:
15 visit_fn = getattr(self, 'visit_' + node['node'])
16 except AttributeError:
17 visit_fn = noop
18 
19 try:
20 depart_fn = getattr(self, 'depart_' + node['node'])
21 except AttributeError:
22 depart_fn = noop
23 
24 try:
25 visit_fn(node)
26 for n in node.get('children', []):
27 self.visit(n)
28 except SkipChildren:
29 return
30 finally:
31 depart_fn(node)
```

With our visitor class, we can finally define our table printer:

```
 1 # Note: for the sake of this article's brevity, the code below
 2 # is actually python pseudocode. A working implementation can
 3 # be found in the git repository linked at the end of this article.
 4 
 5 class TablePrinter(Visitor):
 6 
 7 def __init__(self):
 8 self.row = 0
 9 self.col = 0
10 
11 def visit_table(self, node):
12 _draw_initial_table(node['colspec'], node['rowspec'])
13 
14 def visit_row(self, node):
15 self.col = 0
16 
17 def depart_row(self, node):
18 self.row += 1
19 
20 def visit_cell(self, node):
21 # Get position & dimensions
22 
23 pos = self.row, self.col
24 dim = self._get_cell_dimensions(node, pos)
25 
26 spanned_cols, spanned_rows, width, height = dim
27 
28 # Draw the cell
29 
30 self._draw_bottom_border(dim, pos, node)
31 self._draw_right_border(dim, pos, node)
32 
33 self._draw_cell_contents(dim, pos, node)
34 
35 # Update the position & stop recursing
36 
37 self.col += spanned_cols
38 raise SkipChildren
39 
40 def _get_cell_dimensions(self, node, pos):
41 """
42  Returns the number of columns and rows this cell spans,
43  and its width in character columns and height in lines.
44 
45  Args:
46  node: the node of the cell.
47  pos: the row and column indices of this cell.
48  """
49 raise NotImplementedError
50 
51 def _draw_initial_table(self, widths, heights):
52 """
53  Draw the leftmost and upmost borders of the table,
54  and fills the defined rectangle with spaces.
55  Args:
56  widths: the widths of each columns
57  heights: the heights of each rows
58  """
59 total_width = sum(widths) + (len(widths) + 1)
60 total_height = sum(heights) + (len(heights) + 1)
61 
62 raise NotImplementedError
63 
64 def _draw_bottom_border(self, dim, pos, cell):
65 """
66  Draw a bottom border for this cell
67  Args:
68  dim: the dimensions of the cell
69  pos: the position of the cell in the table
70  cell: the cell properties
71  """
72 raise NotImplementedError
73 
74 def _draw_right_border(self, dim, pos, cell):
75 """
76  Draw a bottom border for this cell
77  Args:
78  dim: the dimensions of the cell
79  pos: the position of the cell in the table
80  cell: the cell properties
81  """
82 raise NotImplementedError
83 
84 def _draw_right_border(self, dim, pos, cell):
85 """
86  Draw the contents of this cell
87  Args:
88  dim: the dimensions of the cell
89  pos: the position of the cell in the table
90  cell: the cell properties
91  """
92 raise NotImplementedError
```

## Wrapping up

I published on github an ascii table renderer using this algorithm as a proof of concept [here](https://github.com/Snaipe/table2ascii). The code isn't perfect, and needs to be cleaned up a bit, but should be overall understandable.

This algorithm is simple enough for purposes where there are no border styles. For broader ones, one could either change this algorithm to make it overwrite previous borders, or use the guidelines as described in the [official w3c document on CSS2 properties of HTML tables](http://www.w3.org/TR/CSS2/tables.html).
