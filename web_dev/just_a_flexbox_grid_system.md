# Just a Flexbox Grid System

[Original URL](http://gridlex.devlint.fr/)

> Simple. Robust. Responsive. Flexible. How it works? The concept is simple: you need to wrap your .col in a .grid. That's all. You prefer a visual demo? What can we expect? Basically each column is...

## Simple. Robust. Responsive. Flexible.

### How it works?

The concept is simple:<br>
you need to wrap your `.col` in a `.grid`.

That's all.

[You prefer a visual demo?](http://gridlex.devlint.fr/cards.html)

### What can we expect?

- Basically each column is the same width as every other cell in the grid.
- But you can add sizing classes to individual columns.
- For responsive designs, you can add classes based on media-queries.
- Top, bottom, or middle. For the grid. And for the columns.
- Grids can be nested. Always. Directly in a column.

## 1\. The basic. Just add a class `.grid-*` (from -1 to -12)

**This feature is so simple, but we find it very very rarely in a robust and flexible grid!**<br>
Why should we be required to put sizing classes on every single cell when they all have the same width?

In this ways, you will always have the specified number of columns in a row:

_But what happens if we have more than 3 columns in a `.grid-3` ?_<br>
This:

```
<div class="grid-3_xs-1">
 <div class="col">...</div>
 <div class="col">...</div>
 <div class="col">...</div>
 <div class="col">...</div>
 <div class="col">...</div>
 <div class="col">...</div>
 <div class="col">...</div>
 <div class="col">...</div>
 <div class="col">...</div>
</div>
```

## 2\. The precise. Compose cell by cell (with class like `.col-*`)

### See [documentation](http://gridlex.devlint.fr/#documentation) to have the list of availlable classes

```
<div class="grid">
 <div class="col-12">...</div> <!-- first row: one column -->
 <div class="col-6">...</div> <!-- second row: three columns (6+3+3) -->
 <div class="col-3">...</div>
 <div class="col-3">...</div>
</div>
```

## 3\. The automatic. Just add number of cells you want in the grid (`.grid > .col`):

Auto width columns, no size on cells !<br>
That's a pretty cool feature of flex: the number determines the width (size = grid/cells children).

_Tip: Keep in mind that in this way, your grid will only be a row!_

So here we have a grid with one column...

... another one with four columns...

... and last a grid with ... nine columns?

```
<div class="grid"> <!-- Four <div class="col"> for 4 columns in the grid -->
 <div class="col">...</div>
 <div class="col">...</div>
 <div class="col">...</div>
 <div class="col">...</div>
</div>
```

## Did I mention that you can mix the 3 ways?

```
<div class="grid-6_sm-2">
 <!-- first row -->
 <div class="col-8_sm-12">...</div>
 <div class="col">...</div>
 <div class="col">...</div>
 <!-- second row -->
 <div class="col">...</div>
 <div class="col-5_sm-6">...</div>
 <div class="col-2">...</div>
 <div class="col-1_sm-4">...</div>
 <div class="col">...</div>
 <!-- third row -->
 <div class="col">...</div>
 <div class="col">...</div>
 <div class="col">...</div>
 <div class="col">...</div>
 <div class="col">...</div>
 <div class="col">...</div>
</div>

<div class="grid">
 <div class="col">...</div>
 <div class="col-2_sm-12">...</div>
 <div class="col-6_sm-12">...</div>
</div>
```

## We can have a grid without gutter too:

```
<div class="grid-noGutter">
 <div class="col-12">...</div>
 <div class="col-6">...</div>
 <div class="col-6">...</div>
 <div class="col-4">...</div>
 <div class="col-8">...</div>
 <div class="col-3">...</div>
 <div class="col-9">...</div>
 <div class="col-1">...</div>
 <div class="col-11">...</div>
</div>
```

## ... and nesting in the grid:

Each column can become a grid too (`.col-6.grid`)

```
<div class="grid">
 <div class="col-6">
 col-6
 </div>
 <div class="col-6 grid">
 <div class="col-4">
 col-4
 </div>
 <div class="col-8 grid">
 <div class="col-6">
 col-6
 </div>
 <div class="col-6">
 col-6
 </div>
 <div class="col-4
 col-4
 </div>
 <div class="col-4">
 col-4
 </div>
 <div class="col-4">
 col-4
 </div>
 </div>
 </div>
</div>
```

## Horizontal alignment

```
<div class="grid-3">
 <div class="col">...</div>
</div>
<div class="grid-3-center">
 <div class="col">...</div>
</div>
<div class="grid-3-right">
 <div class="col">...</div>
</div>
```

## Vertical alignment on the grid: top

Lorem ipsum dolor sit amet, consectetur adipisicing elit. Dicta, dolorum eius esse id labore odit placeat tempora! Alias aliquam aut eaque nulla repudiandae, temporibus voluptatum.

```
<div class="grid">
 <div class="col">...</div>
 <div class="col">...</div>
 <div class="col">...</div>
</div>
```

## Vertical alignment on the grid: middle

Lorem ipsum dolor sit amet, consectetur adipisicing elit. Dicta, dolorum eius esse id labore odit placeat tempora! Alias aliquam aut eaque nulla repudiandae, temporibus voluptatum.

```
<div class="grid-middle">
 <div class="col">...</div>
 <div class="col">...</div>
 <div class="col">...</div>
</div>
```

## Vertical alignment on the grid: bottom

```
<div class="grid-bottom">
 <div class="col">...</div>
 <div class="col">...</div>
 <div class="col">...</div>
</div>
```

## Vertical alignment on the column: grid-middle & mixed columns

Lorem ipsum dolor sit amet, consectetur adipisicing elit. Aliquam aperiam architecto aut, commodi deserunt dicta eligendi error incidunt inventore maiores mollitia officiis placeat quibusdam tempora unde veritatis vero voluptas. Ab consectetur delectus nam. Ab animi aspernatur, deserunt eos et, inventore iure laboriosam laborum minus nam nihil quae tempore tenetur ullam.

```
<div class="grid-4-middle">
 <div class="col">...</div>
 <div class="col">...</div>
 <div class="col-bottom">...</div>
 <div class="col-top">...</div>
</div>
```

## Reverse, order and other things

### Normal flow

```
<div class="grid">
 <div class="col">A</div>
 <div class="col">B</div>
 <div class="col">C</div>
 <div class="col">D</div>
 <div class="col">E</div>
</div>
```

### Reverse flow

```
<div class="grid-reverse">
 <div class="col">A</div>
 <div class="col">B</div>
 <div class="col">C</div>
 <div class="col">D</div>
 <div class="col">E</div>
</div>
```

### Normal flow with ordering (first / last)

```
<div class="grid">
 <div class="col-last">A</div>
 <div class="col">B</div>
 <div class="col">C</div>
 <div class="col">D</div>
 <div class="col-first">E</div>
</div>
```

### No gutter? Equal-height?

Lorem ipsum dolor sit amet, consectetur adipisicing elit. Quas laboriosam inventore minima facere possimus quisquam.

```
<div class="grid-noGutter-equalHeight">
 <div class="col">...</div>
 <div class="col">...</div>
 <div class="col">...</div>
 <div class="col">...</div>
</div>
```

## Distribution

### Horizontal distribution (grid-spaceBetween)

If you add `spaceBetween` class, the distribution will have no margin around first and last elements :

```
<div class="grid-spaceBetween">
 <div class="col-2">...</div>
 <div class="col-2">...</div>
 <div class="col-2">...</div>
</div>
```

### Horizontal distribution (spaceAround)

If you add `grid-spaceAround` class, the distribution will have margins around first and last elements :

```
<div class="grid-spaceAround">
 <div class="col-2">...</div>
 <div class="col-2">...</div>
 <div class="col-2">...</div>
</div>
```

### Horizontal distribution (width grid-center)

```
<div class="grid-center">
 <div class="col-2">...</div>
 <div class="col-2">...</div>
 <div class="col-2">...</div>
</div>
```

### Columns

```
<div class="grid-column">
 <div class="col-4">A</div>
 <div class="col-2">B</div>
 <div class="col-6">C</div>
</div>
```

### Columns-reverse

```
<div class="grid-column-reverse">
 <div class="col-4">A</div>
 <div class="col-2">B</div>
 <div class="col-6">C</div>
</div>
```

## Offset: push a col from left or from right

```
<div class="grid">
 <div class="col-6 off-6-left">col-6 off-6-left</div>
</div>

<div class="grid">
 <div class="col-4">col-6 off-6-left</div>
 <div class="col-4 off-4-left">col-4 off-4-left</div>
</div>

<div class="grid">
 <div class="col-2 off-2-left">col-2 off-2-left</div>
 <div class="col-2 off-2-left">col-2 off-2-left</div>
 <div class="col-2 off-2-left">col-2 off-2-left</div>

 <div class="col-2 off-2-right">col-2 off-2-right</div>
 <div class="col-2 off-2-right">col-2 off-2-right</div>
 <div class="col-2 off-2-right">col-2 off-2-right</div>

 <div class="col-2</div>
 <div class="col-6 off-1-left off-1-right">col-6 off-1-left off-1-right</div>
 <div class="col-2</div>
</div>
```

## Grid & associated classes

```
.grid
 -* (1 to 12)
 _xs-* (1 to 12)
 _sm-* (1 to 12)
 _md-* (1 to 12)
 _lg-* (1 to 12)

 -center
 -right
 -middle
 -bottom
 -spaceBetween
 -spaceAround

 -reverse
 -column
 -column-reverse

 -noGutter
 -equalHeight
```

## Columns & associated classes:

```
.col
 -* (1 to 12)
 _xs-* (1 to 12)
 _sm-* (1 to 12)
 _md-* (1 to 12)
 _lg-* (1 to 12)
 i.e. <div class="col-12_lg-8_md-6_sm-3_xs-1">...</div>

 .col-first
 .col-last
```

## Offsets: with left="" and right="" attribute

```
 .off-* (0 to 11)
 _xs-* (0 to 11)
 _sm-* (0 to 11)
 _md-* (0 to 11)
 _lg-* (0 to 11)

 -0: reboot the offset (can be usefull in responsive mode with key-suffixes)
```

## Gridlex and media-queries

### Because of responsive, you need sometimes to change the size of columns: with this keys as classes you can control your layout by media-queries.

CSS Media Query                         | Applies    | Usage
--------------------------------------- | ---------- | --------
`@media screen and (max-width: 35.5em)` | Max 568px  | `_xs-__`
`@media screen and (max-width: 48em)`   | Max 768px  | `_sm-`
`@media screen and (max-width: 64em)`   | Max 1024px | `_md-__`
`@media screen and (max-width: 80em)`   | Max 1280px | `_lg-`

## When "Can I use" Gridlex?
