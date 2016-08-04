# GIMP - A Tutorial for GIMP-Perl Users

[Original URL](https://www.gimp.org/tutorials/Basic_Perl/)

> GIMP Tutorial - A Tutorial for GIMP-Perl Users (text & images) by Ed J is licensed under a Creative Commons Attribution-ShareAlike 3.0 Unported License. Updated for Gimp 2.8 / Gimp-Perl 2.3 Table...

[![Creative Commons By Share Alike](http://i.creativecommons.org/l/by-sa/3.0/80x15.png)](http://creativecommons.org/licenses/by-sa/3.0/deed.en_US) 

<span><span class="caps">GIMP</span> Tutorial - A Tutorial for <span class="caps">GIMP</span>-Perl Users (text <span class="amp">&amp;</span> images)</span>

 by [Ed J](https://metacpan.org/author/ETJ) is licensed under a [Creative Commons Attribution-ShareAlike 3.0 Unported License](http://creativecommons.org/licenses/by-sa/3.0/deed.en_US).

Updated for Gimp 2.8 / Gimp-Perl 2.3

**Table of contents**

## 1\. Version notes[¶](https://www.gimp.org/tutorials/Basic_Perl/#1-version-notes "Permanent link")

This work is a slightly modified version of Dov Grobgeld's excellent tutorial. It has been updated to Gimp-Perl 2.3 and 

<span class="caps">GIMP</span>

 2.8.

## 2\. Background[¶](https://www.gimp.org/tutorials/Basic_Perl/#2-background "Permanent link")

One of the wonderful features of 

<span class="caps">GIMP</span>

 is that it all its functionality may be accessed through scripting. Available scripting environments include Scheme through Script-Fu and Python.

Perl has 

<span class="caps">CPAN</span>

, giving access to vast numbers of modules to get things done. Now, 

<span class="caps">GIMP</span>

 may also be scripted in Perl. This tutorial will describe how to write such plug-ins and scripts for Gimp.

As there are several excellent tutorial texts describing the perl language, this tutorial will assume a working knowledge of Perl, and will instead concentrate on the use of 

<span class="caps">GIMP</span>

 together with the perl modules Gimp and Gimp::Fu.

## 3\. What you need[¶](https://www.gimp.org/tutorials/Basic_Perl/#3-what-you-need "Permanent link")

The tutorial scripts have been tested on Linux (a Windows version is in the works) with the following versions:

Perl and all its associated modules are available in source form from the Comprehensive Perl Archive network, 

<span class="caps">CPAN</span>

.

## 4\. The Gimp module[¶](https://www.gimp.org/tutorials/Basic_Perl/#4-the-gimp-module "Permanent link")

Most scripts make use of the simplified interface Gimp::Fu provided with the Gimp module. Gimp::Fu provides a framework for entering parameters to the script in a dialog-box interface, just like Script-Fu, but also allows running of the script in batch mode from the command line. This tutorial will go into detailed descriptions of the construction of a Gimp::Fu script, but before we do this, here is the general framework of such a script.

<div class="linenodiv">
  <pre>

  <code> 1
 2
 3
 4
 5
 6
 7
 8
 9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35</code>
</pre>
</div> | <div class="codehilite">
  <pre>

  <code>#!/usr/bin/perl -w

use Gimp; 
use Gimp::Fu;

podregister {
 # code...
};

exit main; 
<strong>END</strong>

=head1 NAME

test_exception - exercise gimp-perl filter testing exceptions

=head1 SYNOPSIS

&lt;Image&gt;/Filters/Languages/_Perl/_Test/Exception

=head1 DESCRIPTION

Exercise Gimp-Perl exceptions.

=head1 AUTHOR

The Author.

=head1 DATE

20140310

=head1 LICENSE

The licensing terms.</code>
</pre>
</div>
---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The key points to note in the script are:

- the use of the two modules `Gimp` and `Gimp::Fu`,
- the podregister function, which will be described in detail below,
- the way the control is handed over to `Gimp` module on line 8, and
- the 

  <span class="caps">POD</span>

   documentation below the `__END__` line.

## 5\. The 

<span class="caps">GIMP</span>

 

<span class="caps">PDB</span>

[¶](https://www.gimp.org/tutorials/Basic_Perl/#5-the-gimp-pdb "Permanent link")

Before going into the details of the Gimp::Fu script, we will describe how to access the functionality of 

<span class="caps">GIMP</span>

. All functions known to 

<span class="caps">GIMP</span>

 are available through the procedural database (

<span class="caps">PDB</span>

). All the 

<span class="caps">PDB</span>

 functions may be called from perl, as will be seen below. These 

<span class="caps">PDB</span>

 functions are either internal to 

<span class="caps">GIMP</span>

, or have been made available through a plug-in or a script extension, but as far as the caller is concerned there is no difference. As we will see below, when a perl function is registered through the `podregister` function, it will appear in the 

<span class="caps">PDB</span>

 as well.

Gimp comes with a 

<span class="caps">PDB</span>

 browser available in **Help/Procedure Browser**. This browser provides a way of seeing all the functions in the 

<span class="caps">PDB</span>

, as well as their input and output parameters. E.g. the 

<span class="caps">DB</span>

 Browser entry for `plug-in-gauss-iir`, which will be used in the example below looks like this:

Name: plug-in-gauss-iir Blurb: Apply a gaussian blur In: 

<span class="caps">INT32</span>

 run-mode The run mode { 

<span class="caps">RUN</span>

-

<span class="caps">INTERACTIVE</span>

 (0), 

<span class="caps">RUN</span>

-

<span class="caps">NONINTERACTIVE</span>

 (1) } 

<span class="caps">IMAGE</span>

 image Input image (unused) 

<span class="caps">DRAWABLE</span>

 drawable Input drawable 

<span class="caps">FLOAT</span>

 radius Radius of gaussian blur (in pixels, > 0.0) 

<span class="caps">INT32</span>

 horizontal Blur in horizontal direction 

<span class="caps">INT32</span>

 vertical Blur in vertical direction Help: Applies a gaussian blur to the drawable, with specified radius of affect. The standard deviation of the normal distribution used to modify pixel values is calculated based on the supplied radius. Horizontal and vertical blurring can be independently invoked by specifying only one to run. The 

<span class="caps">IIR</span>

 gaussian blurring works best for large radius values and for images which are not computer-generated. All the the constants mentioned in the 

<span class="caps">PDB</span>

 Explorer have been defined within Gimp::Fu and may be used within perl. E.g. a call to blur an image looks as follows:

```
Gimp::Plugin->gauss_iir($image, $drawable, 50, 6, 6);
```

Note that Perl is using underline characters where the 

<span class="caps">DB</span>

 browser uses hyphens! The 

<span class="caps">PDB</span>

 entry above shows that `plug-in-gauss-iir` is called with various parameters, of various types. These will be explained below.

You will also see that the first parameter, `run-mode`, was omitted. This is a feature of Gimp-Perl, which will automatically supply that parameter if you leave it off. This will make your code a lot tidier-looking.

Script-Fu scripts are called just like any other script according to the 

<span class="caps">PDB</span>

 signature in the 

<span class="caps">PDB</span>

 browser. E.g. to run the script-fu-basic1-logo just do:

```
script_fu_basic1_logo("Hello", 72,
 "Utopia 72", 
 [0,0,0],[255,255,255]);
```

### 5.1\. Gimp::Fu and the podregister function[¶](https://www.gimp.org/tutorials/Basic_Perl/#51-gimpfu-and-the-podregister-function "Permanent link")

Gimp::Fu is Perl's answer to Script-Fu. It provides a simplified method for accepting parameters for a script through a Gtk2 interface, just like script-fu, but as we shall see below, it has some additional bells and whistles.

The main function for a Gimp-Fu script is the `podregister` function. This function declares the interface of the script to 

<span class="caps">GIMP</span>

. The `podregister` function provides 

<span class="caps">GIMP</span>

 with the following information, from various sections of its 

[<span class="caps">POD</span>

 (plain old documentation)](http://perldoc.perl.org/perlpod.html):

- The name of the function. It comes from the first part of the `NAME` section, before the dash. Tells 

  <span class="caps">GIMP</span>

   the name by which the function will be known in the 

  <span class="caps">PDB</span>

  .

- A small description, from the `NAME` section, after the dash.

- A help text, from the `DESCRIPTION` section.

- The author's name, from the `AUTHOR` section.

- The copyright of the script, from the `LICENSE` section.

- Creation date - any text, from the `DATE` section.

- Menu path - a string, from the `SYNOPSIS` section. The path can take these forms, though there are other options:

  - <span class="dquo">“</span>

    <Image>/Filters/Menu/Script Name"
  - <span class="dquo">“</span>

    <Toolbox>/Xtns/Perl/Script Name"If you specify "<Image>", then the script is expecting to operate on, or create, an image. In this case Gimp::Fu will add as the first two parameters to the script the image and the drawable active when the script was invoked. If, however, the "image types" is left as blank or not specified, the plugin is expected to create and therefore return an image (and it will normally be placed under the `File/Create` menu).

  If you specify "<Toolbox>", then the script is a standalone script that appears in the menu hierarchy under Filters (this is a historical thing) and takes all its inputs through the Gimp::Fu interface dialog.

- The acceptable image types, from the `IMAGE TYPES` section - a string. This list contains a list of image types acceptable. This field is only used for scripts that are in the "<Image>" hieararchy. Some possible values are listed in the table below:

  value                          | meaning
  ------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------
  *                              | Any images are accepted
  <span class="caps">RGB</span>  | <span class="caps">RGB</span>

   images
  <span class="caps">RGBA</span> | <span class="caps">RGB</span>

   images with alpha channels
  <span class="caps">GREY</span> | Grey level images
                                 | (left empty or no such section) Does not take image as input, **does** return an image. `Gimp::Fu` will automatically add that return value.

- Parameters, from the `PARAMETERS` section. This will be Perl code, which Gimp::Fu evaluates as a list of parameters. Each parameter in turn is a reference to an array containing the following four or five values (A reference to an array in Perl is simply an array written within square brackets):

  - The type of the parameter. The types recognized by Gimp::Fu and their Perl counterparts are given in the following table:

    Type                                  | Possible forms                                                                                                                                                       | Comment
    ------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------
    PF_INT32<br>
    PF_INT16<br>
    PF_INT8<br> | 42                                                                                                                                                                   | An integer.
    PF_FLOAT<br>                          | 3.141                                                                                                                                                                | A floating point number.
    PF_TOGGLE                             | 0<br>
    1                                                                                                                                                              | A boolean value.
    PF_SLIDER<br>
    PF_SPINNER              | An integer value through a slider and a spinner interface. The range parameter should be specified and is interpreted as minimum, maximum, and step, e.g. [0,100,1]. | ![tpix](https://www.gimp.org/tutorials/Basic_Perl/tpix.gif)
    PF_FONT                               | "Arial"                                                                                                                                                              | A font name.
    PF_STRING                             | "A string"                                                                                                                                                           | A string
    PF_COLOR<br>
    PF_COLOUR                | [255,127,0]<br>
    `#ff7f00`                                                                                                                                            | A color may either be expressed as a reference to an array of three components, or as a hexadecimal triple, proceeded by the hash sign.
    PF_TOGGLE                             | 0<br>
    1                                                                                                                                                              | A boolean toggle
    PF_IMAGE                              | -                                                                                                                                                                    | An image
    PF_DRAWABLE                           | -                                                                                                                                                                    | A drawable.
    PF_BRUSH                              | ![tpix](https://www.gimp.org/tutorials/Basic_Perl/tpix.gif)                                                                                                          | A brush
    PF_GRADIENT                           | ![tpix](https://www.gimp.org/tutorials/Basic_Perl/tpix.gif)                                                                                                          | A gradient
    PF_PATTERN                            | ![tpix](https://www.gimp.org/tutorials/Basic_Perl/tpix.gif)                                                                                                          | A pattern

  - The name of the parameter - a string.

  - A help text for the parameter.

  - Default value for the parameter. This should be given in the form listed in the table above.
  - An array defining allowed range for the value. This is only used for PF_SLIDER and PF_SPINNER.

- Optionally, the return types of the function can be provided, from a `RETURN VALUES` section. This is specified in the same way as the Parameters above. There is no default or extra argument required.

- The perl code implementing the function - most commonly with a sub-reference, surrounded by "{" and "};", as below - thanks to Perl's prototyping, you don't need to specify "sub". This will be called when the associated menu entry declared through the _Menu path_ described above. When the sub is called it is passed a list of parameters as declared in field 9\. In the case of a "<Image>..." script, the active image and drawable (layer or channel) will be passed as first and second parameters. Thanks to the magic of Perl source filtering, you do not need to declare your variables but may simply use them:

  ```
  podregister {
   $drawable->gauss_iir($radius, $horizontal, $vertical);
  };
  # ...
  =head1 PARAMETERS

   [ PF_FLOAT, 'radius', "Radius", 50.0 ],
   [ PF_INT32, 'horizontal', 'Horizontal blur', 6 ],
   [ PF_INT32, 'vertical', 'Vertical blur', 6 ],
  ```

  You will also note that "plug_in_" has been omitted from the method call above, and that not all its parameters appear to be getting passed! This is thanks to the object-oriented implementation in Gimp-Perl, which when given a method name, searches various prefixes for the underlying function including the plug-in related ones (including `script_fu_`).

  The code will normally display a new image if it creates one, and also return the new image, in accordance with the return types declared in parameter 10 of the `podregister` call described above. This enables Gimp::Fu scripts to be used noninteractively by other scripts. More about that behaviour below.

### 5.2\. podregister parameters[¶](https://www.gimp.org/tutorials/Basic_Perl/#52-podregister-parameters "Permanent link")

You will have noticed above that the parameters to the function given to `podregister` used parameters either named in the 

<span class="caps">POD</span>

, or supplied by `Gimp::Fu`, such as `$drawable`. This is because `podregister` automatically makes available to your function all the variables declared either in the 

<span class="caps">POD</span>

 documentation (the "

<span class="caps">PARAMETERS</span>

" section) or added depending on your "image types".

The following Gimp::Fu script example shows the steps described in the previous section. It registers a script that takes two values, the size of the image and a color, and then produces an image of the requested size with the requested color. Quite useless, but it shows the important steps of how to register a script, how to create a new image, and how to access some 

<span class="caps">PDB</span>

 functions.

<div class="linenodiv">
  <pre>

  <code> 1
 2
 3
 4
 5
 6
 7
 8
 9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49</code>
</pre>
</div> | <div class="codehilite">
  <pre>

  <code>#!/usr/bin/perl -w

use strict; 
use Gimp; 
use Gimp::Fu;

podregister { 
 # no need to have my ($size, $color) = @<em>; 
 my $img = Gimp::Image-&gt;new($size, $size, RGB); # Create a new image 
 # Create a new layer 
 my $layer = $img-&gt;layer<em>new($size, $size, RGB, "Layer 1", 100, NORMAL_MODE); 
 $img-&gt;insert_layer($layer, -1, 0); # add the layer to the image 
 Gimp::Context-&gt;set_background($color); # Set background to required color 
 $layer-&gt;edit_fill(BACKGROUND_FILL); # Paint the layer 
 Gimp::Display-&gt;new($img); 
 return $img; # Return image - return implied by no IMAGE TYPES 
};

exit main; 
__END</em></em>

=head1 NAME

img_uni - Create a uniform image

=head1 SYNOPSIS

&lt;Image&gt;/File/Create/Tutorial/Img Uni

=head1 DESCRIPTION

A tutorial script.

=head1 PARAMETERS

[PF_INT32, "size", "Img size", 100], 
[PF_COLOR, "color", "Img color", [255,127,0]]

=head1 AUTHOR

Dov Grobgeld

=head1 DATE

2004-03-27

=head1 LICENSE

Dov Grobgeld (c)</code>
</pre>
</div>
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Most of these commands are directly copied out the 

<span class="caps">PDB</span>

.

This script shows the essential steps of producing a stand-alone script:

line(s) | Description
------- | ------------------------------------------
7       | Registration of the extension
9       | Creating a new image.
11      | Creating one or more layers.
12      | Attaching the layer to the image.
13-14   | Do some painting operations in the layers.
15      | Display the new image
16      | Return the image to the caller

To test the script, save it in the directory `$HOME/.gimp-2.8/plug-ins`. (A more official way to add scripts is to use the `gimptool-2.0 --install-bin` command). It must then be made executable through the command:

```
chmod +x $HOME/.gimp-2.8/plug-ins/uni
```

Then start 

<span class="caps">GIMP</span>

. It is generally a good idea to test the syntax of the script with `perl -c` before starting 

<span class="caps">GIMP</span>

.

**Note:** Due to the way 

<span class="caps">GIMP</span>

 works, it is not possible to add scripts once 

<span class="caps">GIMP</span>

 is running. On the other hand, it is possible to change a script which has already been registered, as long as the parameters don't change.

The script is now accessible through the menu system through the **File/Create/Tutorial** top menu:

![uni-menu.png](https://www.gimp.org/tutorials/Basic_Perl/uni-menu.png) When choosing this menu entry the following screen is popped up:

![uni-entry.png](https://www.gimp.org/tutorials/Basic_Perl/uni-entry.png) Choosing the default values results in the image:

![uni-result.png](https://www.gimp.org/tutorials/Basic_Perl/uni-result.png)

## 6\. Object oriented syntax[¶](https://www.gimp.org/tutorials/Basic_Perl/#6-object-oriented-syntax "Permanent link")

Gimp-Perl provides an alternative object-oriented syntax for the image and the drawable commands. Here is a table showing the procedural vs the object oriented syntax for a few commands:

procedural syntax                  | object oriented syntax
---------------------------------- | -----------------------------
gimp_image_insert_layer($drw,0,0); | $img->insert_layer($drw,0,0);
gimp_drawable_width($drw);         | $drw->width;

The substitution rule for converting a 

<span class="caps">PDB</span>

 into a method is as simple as erasing `gimp_image_` (or sometimes `gimp_`) from the beginning of the function call and calling this method through the image object. Similarly for the `gimp_drawable_...` functions. See the [Gimp-Perl docs](http://search.cpan.org/dist/Gimp/Gimp.pm#OBJECT-ORIENTED_SYNTAX) for more detail.

Note that the object-oriented syntax appears to be only syntactic sugar that makes the calling syntax cleaner. The error messages are still given in the procedural format. What is going on is that 

<span class="caps">GIMP</span>

, and the library on which it is implemented (Glib), use C in an object-oriented fashion.

## 7\. Painting areas with selections[¶](https://www.gimp.org/tutorials/Basic_Perl/#7-painting-areas-with-selections "Permanent link")

In the `uni` script the function `gimp_edit_fill` was called to fill the whole image. Looking at the info for `gimp_edit_fill` in the 

<span class="caps">DB</span>

 browser we find the following:

Name: gimp_edit_fill Blurb: Fill selected area of drawable In: 

<span class="caps">DRAWABLE</span>

 drawable The drawable to fill from 

<span class="caps">INT32</span>

 fill_type The type of fill { 

<span class="caps">FOREGROUND</span>

-

<span class="caps">FILL</span>

 (0), 

<span class="caps">BACKGROUND</span>

-

<span class="caps">FILL</span>

 (1), 

<span class="caps">WHITE</span>

-

<span class="caps">FILL</span>

 (2), 

<span class="caps">TRANSPARENT</span>

-

<span class="caps">FILL</span>

 (3), 

<span class="caps">PATTERN</span>

-

<span class="caps">FILL</span>

 (4), 

<span class="caps">NO</span>

-

<span class="caps">FILL</span>

 (5) } Help: This procedure fills the specified drawable with the fill mode. If the fill mode is foreground, the current foreground color is used. If the fill mode is background, the current background color is used. Other fill modes should not be used. This procedure only affects regions within a selection if there is a selection active. If you want to fill the whole drawable, regardless of the selection, use 'gimp-drawable-fill'. Thus, if a selection is active when `gimp_edit_fill` is called, only the selected region of the drawable is painted. Note also that you must substitute "_" for "-" in the names.

There are lots of ways of choosing a selection as can be seen when searching for a "select" in the 

<span class="caps">PDB</span>

. The example below uses `gimp_image_select_rectangle`, whose entry in the 

<span class="caps">PDB</span>

 looks as follows:

Name: gimp-image-select-rectangle Blurb: Create a rectangular selection over the specified image In: 

<span class="caps">IMAGE</span>

 image The image 

<span class="caps">INT32</span>

 operation The selection operation { 

<span class="caps">CHANNEL</span>

-

<span class="caps">OP</span>

-

<span class="caps">ADD</span>

 (0), 

<span class="caps">CHANNEL</span>

-

<span class="caps">OP</span>

-

<span class="caps">SUBTRACT</span>

 (1), 

<span class="caps">CHANNEL</span>

-

<span class="caps">OP</span>

-

<span class="caps">REPLACE</span>

 (2), 

<span class="caps">CHANNEL</span>

-

<span class="caps">OP</span>

-

<span class="caps">INTERSECT</span>

 (3) } 

<span class="caps">FLOAT</span>

 x x coordinate of upper-left corner of rectangle 

<span class="caps">FLOAT</span>

 y y coordinate of upper-left corner of rectangle 

<span class="caps">FLOAT</span>

 width the width of the rectangle: width >= 0 

<span class="caps">FLOAT</span>

 height the height of the rectangle: width >= 0 Help: This tool creates a rectangular selection over the specified image. The rectangular region can be either added to, subtracted from, or replace the contents of the previous selection mask. This procedure is affected by the following context setters: 'gimp-context-set-feather', 'gimp-context-set-feather-radius'. A simple use of this function which selects a rectangle in the middle of an image and paints that rectangle with a user defined color. This example also introduces a couple of new features we haven't seen before:

- The script is associated with an image since its menu path starts with "<Image>/...". Note that as a result of this the callback sub in line 6 receives two additional parameters, the active image and the selected drawable.
- The use of the 

  <span class="caps">PDB</span>

   functions `gimp_image_undo_group_start` and `gimp_image_undo_group_end`. These functions declare an undo group. When an undo is done on the image, instead of having the individual operators undo, all the actions between the undo start and the undo end calls will be undone at once.

| [paint-select](https://www.gimp.org/tutorials/Basic_Perl/paint-select)
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
| <div class="linenodiv">
  <pre>

  <code> 1
 2
 3
 4
 5
 6
 7
 8
 9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55</code>
</pre>
</div> | <div class="codehilite">
  <pre>

  <code>#!/usr/bin/perl -w

use Gimp; 
use Gimp::Fu;

podregister { 
 my ($width, $height) = ($image-&gt;width, $image-&gt;height); 
 # Select a rectangle inside the image and paint it with color 
 $image-&gt;undo<em>group<em>start; 
 $image-&gt;select_rectangle( 
 CHANNEL_OP_REPLACE, $width/4, $height/4, $width/2, $height/2, 
 ); 
 Gimp::Context-&gt;set_background($color); 
 $drawable-&gt;edit_fill(BACKGROUND_FILL); 
 $image-&gt;selection_none; 
 $image-&gt;undo_group_end; 
 (); 
};

exit main; 
__END</em></em>

=head1 NAME

img_paint_select - Paints the selection

=head1 SYNOPSIS

&lt;Image&gt;/Filters/Tutorial/Paint Select

=head1 DESCRIPTION

Paints the selection

=head1 PARAMETERS

[PF_COLOR, "color", "Rectangle color", [0,0,255]],

=head1 RETURN VALUES

=head1 IMAGE TYPES

*

=head1 AUTHOR

Dov Grobgeld

=head1 DATE

1999-05-14

=head1 LICENSE

Dov Grobgeld</code>
</pre>
</div>
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The result when run on our previous image:

![paint-select.png](https://www.gimp.org/tutorials/Basic_Perl/paint-select.png)

### 7.1\. Complex selections[¶](https://www.gimp.org/tutorials/Basic_Perl/#71-complex-selections "Permanent link")

Besides rectangular selections elliptical selections may also be created through the 

<span class="caps">PDB</span>

 functions `gimp_image_select_ellipse` and `gimp_image_select_polygon` which allows the selection of ellipses and polygons.

More complex selections may be created through the channel mechanism. The 

<span class="caps">PDB</span>

 `gimp_channel_new()` (`Gimp::Channel->new`) creates a new channel. The channel is a drawable that may be painted into, just like any other drawable, but with the difference that it is always a grey level image. Once the channel is finished, the channel may be loaded into the selection through the 

<span class="caps">PDB</span>

 function `gimp_image_select_item`.

Search for "select" in the 

<span class="caps">DB</span>

 Browser to see a list of all the selection related functions.

### 7.2\. Loops[¶](https://www.gimp.org/tutorials/Basic_Perl/#72-loops "Permanent link")

In perl it is trivial to write loops that together with the various selection tools gives powerful creative possibilities. Here is an example that mixes colors in circles. There is nothing really new here, but it shows the power of what we have described above.

[circles](https://www.gimp.org/tutorials/Basic_Perl/circles)

<div class="linenodiv">
  <pre>

  <code> 1
 2
 3
 4
 5
 6
 7
 8
 9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60</code>
</pre>
</div> | <div class="codehilite">
  <pre>

  <code>#!/usr/bin/perl

use Gimp; 
use Gimp::Fu;

podregister { 
 # Create the background 
 my $img = Gimp::Image-&gt;new($size, $size, RGB); 
 $layer = $img-&gt;layer<em>new($size, $size, RGB, "Layer 1", 100, NORMAL<em>MODE); 
 $img-&gt;insert_layer($layer, -1, 0); 
 Gimp::Context-&gt;set_background($bgcolor); 
 $layer-&gt;edit_fill(BACKGROUND_FILL); 
 my $ncircles = int($size/$radius/2); 
 for ($i=0; $i&lt;$ncircles; $i++) { 
 for ($j=0; $j&lt;$ncircles; $j++) { 
 # Select a circle 
 $img-&gt;select_ellipse( 
 CHANNEL_OP_REPLACE, $i<em>$radius</em>2, $j<em>$radius</em>2, $radius<em>2, $radius</em>2 
 ); 
 my $color = [$i<em>30, ($ncircles-$j)</em>25, ($i+$j)*15]; # mix colors 
 Gimp::Context-&gt;set_background($color); 
 $layer-&gt;edit_fill(BACKGROUND_FILL); 
 } 
 } 
 Gimp::Display-&gt;new($img); 
 return $img; 
};

exit main; 
__END</em></em>

=head1 NAME

circles - a loop

=head1 SYNOPSIS

&lt;Image&gt;/File/Create/Tutorial/Circles

=head1 DESCRIPTION

a loop

=head1 PARAMETERS

[PF_INT32, "size", "Image size", 100], 
[PF_COLOR, "bgcolor", "Background color", [40,180,60]], 
[PF_INT32, "radius", "Circle radius", 10]

=head1 AUTHOR

Dov

=head1 DATE

1999-05-14

=head1 LICENSE

Dov</code>
</pre>
</div>
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The result:

![circles.png](https://www.gimp.org/tutorials/Basic_Perl/circles.png)

## 8\. Creating text[¶](https://www.gimp.org/tutorials/Basic_Perl/#8-creating-text "Permanent link")

### 8.1\. Hello World - writing text in an image[¶](https://www.gimp.org/tutorials/Basic_Perl/#81-hello-world-writing-text-in-an-image "Permanent link")

To create text the 

<span class="caps">PDB</span>

 function `gimp_text_fontname()` may be used.

Here is an example of a script that creates an image containing "Hello world".

| [hello-world1](https://www.gimp.org/tutorials/Basic_Perl/hello-world1)
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
| <div class="linenodiv">
  <pre>

  <code> 1
 2
 3
 4
 5
 6
 7
 8
 9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52</code>
</pre>
</div> | <div class="codehilite">
  <pre>

  <code>#!/usr/bin/perl

use Gimp; 
use Gimp::Fu;

podregister { 
 my $img = Gimp::Image-&gt;new(350, 100, RGB); 
 my $drw = $img-&gt;layer<em>new($img-&gt;width, $img-&gt;height, 
 RGB, "BG", 100, NORMAL<em>MODE); 
 $img-&gt;insert_layer($drw, -1, 0); 
 Gimp::Context-&gt;set_background("black"); 
 $drw-&gt;edit_fill(BACKGROUND_FILL); 
 Gimp::Context-&gt;set_foreground([255,255,0]); # Choose color of text 
 # Create the text 
 my $textlayer = $drw-&gt;text_fontname(0, 0, $text, 10, 1, $size, POINTS, $font); 
 $textlayer-&gt;floating_sel_anchor; 
 Gimp::Display-&gt;new($img); 
 return $img; 
};

exit main; 
__END</em></em>

=head1 NAME

hello_world1 - basic text

=head1 SYNOPSIS

&lt;Image&gt;/File/Create/Tutorial/Basic text 1

=head1 DESCRIPTION

basic text

=head1 PARAMETERS

[PF_FONT, "font", "Font", "Sans"], 
[PF_INT32, "size", "Font size", 70], 
[PF_STRING, "text", "Text", "Hello world!"]

=head1 AUTHOR

Dov

=head1 DATE

2004-03-27

=head1 LICENSE

Dov</code>
</pre>
</div>
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The result:

![hello-world1.png](https://www.gimp.org/tutorials/Basic_Perl/hello-world1.png) One thing to note in this script is that the text that is created on line 15 is a _floating layer_, that needs to be anchored to its parent layer. This is done in line 16 through the call to `gimp_floating_sel_anchor()`.

This script suffers from the problem that the image size is unrelated to the text size. This is taken care of in the following more complex example which shows the basic steps for a logo generating script:

- Creation of an image of arbitrary size
- Creation of a background drawable of an arbitrary size
- Creation of text layer which exactly fits the text with the command `gimp_text_fontname`.
- Resizing the image and the background to the size of the text layer.

The result is an image composed of two layers; a transparent text layer on top of a uniform background.

[basic-logo](https://www.gimp.org/tutorials/Basic_Perl/basic-logo)

<div class="linenodiv">
  <pre>

  <code> 1
 2
 3
 4
 5
 6
 7
 8
 9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63</code>
</pre>
</div> | <div class="codehilite">
  <pre>

  <code>#!/usr/bin/perl

use Gimp; 
use Gimp::Fu;

podregister { 
 my $img = Gimp::Image-&gt;new(100, 100, RGB); # any old size 
 my $background = $img-&gt;layer<em>new( 
 100, 100, RGB, "Background", 100, NORMAL<em>MODE 
 ); 
 $img-&gt;insert_layer($background, 0, 0); 
 Gimp::Context-&gt;set_foreground($fgcolor); # Choose color of text 
 # Create the text layer. Using -1 as the drawable creates a new layer. 
 my $text_layer = $img-&gt;text_fontname( 
 -1, 0, 0, $text, $border, 1, $size, POINTS, $font 
 ); 
 # Get size of the text drawable and resize the image and the 
 # background layer to this size. 
 my ($width, $height) = ($text_layer-&gt;width, $text_layer-&gt;height); 
 $img-&gt;resize($width, $height, 0, 0); 
 $background-&gt;resize($width, $height, 0, 0); 
 # Fill the background layer now when it has the right size. 
 Gimp::Context-&gt;set_background($bgcolor); 
 $background-&gt;edit_fill(BACKGROUND_FILL); 
 Gimp::Display-&gt;new($img); 
 return $img; 
};

exit main; 
__END</em></em>

=head1 NAME

basic_logo - Basic logo

=head1 SYNOPSIS

&lt;Image&gt;/File/Create/Tutorial/Basic Logo

=head1 DESCRIPTION

Make a basic logo.

=head1 PARAMETERS

[PF_FONT, "font", "Font", "Utopia Bold"], 
[PF_INT32, "size", "Font size", 10], 
[PF_INT32, "border", "Border", 10], 
[PF_STRING, "text", "Text", "Hello world!"], 
[PF_COLOR, "bgcolor", "Background color", [40,180,160]], 
[PF_COLOR, "fgcolor", "Foreground color", [255,255,0]],

=head1 AUTHOR

Dov Grobgeld

=head1 DATE

2004-03-27

=head1 LICENSE

Dov Grobgeld</code>
</pre>
</div>
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Note the special syntax of `gimp_image_text_fontname` in line 14 in `basic-logo` with the drawable = -1\. The special case drawable=-1 means that instead of creating a floating layer, a new image layer will be created.

The dialog and the resulting image:

![basic-logo-dialog.png](https://www.gimp.org/tutorials/Basic_Perl/basic-logo-dialog.png)

## 9\. Floating selections[¶](https://www.gimp.org/tutorials/Basic_Perl/#9-floating-selections "Permanent link")

When a region has been selected through one of the selection routines, the area outlined by the selection may be copied to the cut-buffer through the `gimp_edit_copy` command. The cut-buffer may subsequently be pasted into a different layer through the `gimp_edit_paste` command. When a layer is pasted it becomes a floating selection. This floating selection may be moved to its required position by the command `gimp_layer_set_offsets`, and finally it is pasted by the `gimp_floating_sel_anchor` command. Another way of determining the position of a pasted layer is to create a selection in the target image before the cut-buffer is pasted.

This is illustrated in the following program, which works on one image and takes as a parameter another image, which it concatenates to the right of the first image. The lines 28-38 shows how the second image is copied and glued into the first image.

| [horiz-cat](https://www.gimp.org/tutorials/Basic_Perl/horiz-cat)
| -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
| <div class="codehilite">
  <pre>

  <code>#!/usr/bin/perl

use Gimp; 
use Gimp::Fu;

podregister { 
 die "Must select layer.\n" unless $drawable-&gt;is<em>layer; 
 $drawable-&gt;become('Gimp::Layer'); 
 my $image2 = $drawable2-&gt;get<em>image; 
 my ($w1, $h1) = ($drawable-&gt;width, $drawable-&gt;height); 
 my ($w2, $h2) = ($drawable2-&gt;width, $drawable2-&gt;height); 
 my $hmax = $h1 &gt; $h2 ? $h1 : $h2; # new height is maximum height of the images 
 $image-&gt;undo_group_start; # Create an undo group 
 # Resize the drawable layer to make room for the image 
 $image-&gt;resize($w1+$w2, $hmax, 0, ($hmax-$h1)/2); 
 $drawable-&gt;resize($w1+$w2, $hmax, 0, ($hmax-$h1)/2); 
 # Copy $drawable2 and paste it into the new space of $drawable1 
 $image2-&gt;selection_all; # select all of image2 
 $drawable2-&gt;edit_copy; 
 $image2-&gt;selection_none; 
 # make a selection in image 1 where $drawable2 is to be pasted 
 $image-&gt;select_rectangle(CHANNEL_OP_ADD, $w1, ($hmax-$h2)/2, $w2, $h2); 
 $drawable-&gt;edit_paste(0)-&gt;floating_sel_anchor; # paste and then anchor it 
 $image-&gt;undo_group_end; # Close the undo group 
 return; 
};

exit main; 
__END</em></em>

=head1 NAME

horiz_cat - Horizontal concat

=head1 SYNOPSIS

&lt;Image&gt;/Filters/Tutorial/Horizontal Concat

=head1 DESCRIPTION

Horizontal concatenation of images.

=head1 PARAMETERS

[PF_DRAWABLE, "drawable2", "Drawable to concatenate", undef],

=head1 IMAGE TYPES

*

=head1 AUTHOR

Dov Grobgeld

=head1 DATE

2004-03-27

=head1 LICENSE

Dov Grobgeld</code>
</pre>
</div>

You might notice something very important on lines 7 and 8: we "know" that we'll always pass a layer as the first drawable, but we're checking that anyway. Then we're telling Gimp-Perl the drawable definitely _is_ a layer. The reason for that is that (obviously) `Gimp::Layer` and `Gimp::Drawable` have different methods available to them, and what matters here is that `Gimp::Drawable` does not have a resize method. (i.e. The 

<span class="caps">PDB</span>

 does not have a `gimp_drawable_resize` function)

## 10\. The Perl Server and stand-alone scripts[¶](https://www.gimp.org/tutorials/Basic_Perl/#10-the-perl-server-and-stand-alone-scripts "Permanent link")

So far the scripts have all been started from the menu structure within 

<span class="caps">GIMP</span>

. But using `Gimp::Fu` there is another possibility, and that is to run the scripts from the command line, as a normal Perl program. When run this way the script tries to connect to the Perl-Server, and if it fails it will launch a 

<span class="caps">GIMP</span>

 of its own. If you plan to run several scripts this way, it is obviously much faster to run the Perl-Server since launching 

<span class="caps">GIMP</span>

 takes quite a bit of time. The Perl-Server may be started from the `Filters/Perl` menu.

When a `Gimp::Fu` script is run from the command line, the result is the same as when it is run through the menus, except that it may be run with the `--output` parameter. This will save the result to a file instead of displaying it within 

<span class="caps">GIMP</span>

. This is great for batch creation of logos, etc.

The filename for the `--output` has some special magic that allows to set some special image saving parameters, like interlace, quality factor, etc. See the [`Gimp::Fu` docs](http://search.cpan.org/dist/Gimp/Gimp/Fu.pm#MISCELLANEOUS_FUNCTIONS) for more detail

Here are two invocations of the scripts declared above, but with output written to a jpg file and a png file.

| [perl-gimp-from-shell](https://www.gimp.org/tutorials/Basic_Perl/perl-gimp-from-shell)
| ------------------------------------------------------------------------------------------------------------------------------------------
| ```
 1: uni -o /tmp/uni.png --size 100 --color purple 
 2: basic-logo --font 'utopia 100' -o /tmp/bl.ppm --size 20 --text "Perl rules" ```

Another important use of this interface is that it enables running the Perl debugger on the perl scripts.

### 10.1\. A shell for Gimp-Perl[¶](https://www.gimp.org/tutorials/Basic_Perl/#101-a-shell-for-gimp-perl "Permanent link")

When using the Perl-Server it is not necessary to use `Gimp::Fu` and the `podregister` function. Instead you may call `Gimp::on_net`, which takes as a parameter a reference to a sub routine that is called when your script has connected to a 

<span class="caps">GIMP</span>

 (or started one up).

For a simple but powerful example of the use of the Gimp without Fu, here is a an interactive Gimp-Perl shell that may be run from the command line:

| [pg-shell](https://www.gimp.org/tutorials/Basic_Perl/pg-shell)
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
| <div class="linenodiv">
  <pre>

  <code> 1
 2
 3
 4
 5
 6
 7
 8
 9
10
11
12
13
14
15
16
17</code>
</pre>
</div> | <div class="codehilite">
  <pre>

  <code>#!/usr/bin/perl

# An interactive command line shell to GIMP. 
use Gimp; 
use Term::ReadLine;

Gimp::on<em>net { 
 $term = new Term::ReadLine("Gimp"); 
 while( defined ($</em> = $term-&gt;readline("Gimp&gt; "))) { 
 $res = eval($_) . "\n"; 
 print("Error: $@"), next if $@; 
 print "\n"; 
 Gimp-&gt;displays_flush(); 
 } 
 };

 exit Gimp::main;</code>
</pre>
</div>
---------------------------------------------------------------------------------------------------------------- | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Here is an example of an interactive session with this shell:

| [interact](https://www.gimp.org/tutorials/Basic_Perl/interact)
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
| ```
 1: Gimp> $img = Gimp::Image->new(100,100,RGB) 
 2: Gimp> $drw = $img->layer_new(100,100,RGB_IMAGE, "bg", 100, NORMAL_MODE) 
 3: Gimp> $img->insert_layer($drw,-1, 0) 
 4: Gimp> Gimp::Display->new($img) 
 5: Gimp> $drw->edit_clear 
 6: Gimp> print Gimp::Image->list 
 7: Gimp::Image->existing(1) ```

## 11\. End notes[¶](https://www.gimp.org/tutorials/Basic_Perl/#11-end-notes "Permanent link")

This tutorial has covered only a small part of the possibilities available to a script writer. In particular the following issues available to Gimp::Perl scripts have not been covered:

- The possibility of writing customized Gtk interfaces (see `examples/example-no-fu` in the Gimp-Perl distribution).
- Writing fully-fledged plug-ins that manipulate the tile data through the Perl Data Language (

  <span class="caps">PDL</span>

  ) module (see `examples/map_to_gradient` in the Gimp-Perl distribution).
- Using Gimp-Perl in a 

  <span class="caps">CGI</span>

   environment.
- How to fill with gradients in a plugin (see `examples/randomblends` in the Gimp-Perl distribution).
- How to do polygon selections (see `examples/triangle` in the Gimp-Perl distribution).

The Gimp-Perl distribution also has over 50 more example scripts supplied. Take a look through those for further inspiration!

Please send your comments to [dov.grobgeld@gmail.com](mailto:dov.grobgeld@gmail.com).

[![Creative Commons By Share Alike](http://i.creativecommons.org/l/by-sa/3.0/80x15.png)](http://creativecommons.org/licenses/by-sa/3.0/deed.en_US) 

<span><span class="caps">GIMP</span> Tutorial - A Tutorial for <span class="caps">GIMP</span>-Perl Users (text <span class="amp">&amp;</span> images)</span>

 by [Ed J](https://metacpan.org/author/ETJ) is licensed under a [Creative Commons Attribution-ShareAlike 3.0 Unported License](http://creativecommons.org/licenses/by-sa/3.0/deed.en_US).
