# GIMP - GIMP and GEGL in 2015

[Original URL](http://www.gimp.org/news/2015/12/31/2015-report/)

> We hope you are having great holidays. Here is our annual report about project activities in 2015\. It's been an interesting year for GIMP. For the most part, we focused on completing the...

We hope you are having great holidays. Here is our annual report about project activities in 2015.

It's been an interesting year for 

<span class="caps">GIMP</span>

. For the most part, we focused on completing the 

[<span class="caps">GEGL</span>](http://gegl.org/)

 port which involved rewriting all the source code related to color management. The vast majority of this work was done by Michael Natterer between April and November. The new implementation is much cleaner and is built right into the core of 

<span class="caps">GIMP</span>

. Here are some of the new features:

- pasting an image into another image that has a different color space just works now;
- <span class="caps">GIMP</span>

   detects color space defined in Exif 2.21/

  <span class="caps">DCF</span>

   2.0 option files;
- greyscale images are finally color-managed;
- layer and image previews are color managed too now.

The few missing features are color management for the color picker tool and color selection dialogs. This will be completed before the final v2.10 release.

Michael Natterer also resurrected Intelligent Scissors selection tool--the last one that wasn't ported to 

<span class="caps">GEGL</span>

--and added undo support for selections in progress.

Elle Stone added decomposition and composition of images to/from 

[<span class="caps">CIE</span>

 

<span class="caps">LCH</span>](https://en.wikipedia.org/wiki/Lab_color_space#Cylindrical_representation:_CIELCh_or_CIEHLC)

, a perceptually uniform color space that includes all perceivable colors and thus has a gamut larger than color spaces based on 

<span class="caps">RGB</span>

 and 

<span class="caps">CMYK</span>

 color models.

Rupert Weber, Jörn Meier, Massimo Valentini, Elle Stone, Thomas Manni, and Michael Natterer introduced a few improvements to the blending modes:

- The _Overlay_ mode is not identical to _Soft Light_ mode anymore and relies on a widely used formula from both 

  <span class="caps">PDF</span>

   

  <span class="caps">AND</span>

   

  <span class="caps">W3C</span>

  's "Compositing and Blending Level 1" specification now.
- There are 

  _<span class="caps">CIE</span>

   

  <span class="caps">LCH</span>_

   based version of _Hue_, _Chroma_, _Color_, and _Lightness_ blending modes available now in addition to their 

  _<span class="caps">HSV</span>_

   based versions. See an [explanation by Elle Stone](http://ninedegreesbelow.com/photography/gimp-lch-blend-modes.html) for details.

Another development focus was on painting features. As requested by people using 

<span class="caps">GIMP</span>

 for digital painting, Jehan Pagès and Michael Natterer added the automatic saving of used colors into a new _Color History_ palette. The palette is also part of the color selection dialog and the 

<span class="caps">FG</span>

/

<span class="caps">BG</span>

 dockable dialog.

In early 2015, Michael Natterer added a quick first version of _MyPaint Brush_ tool that used [MyPaint's](http://mypaint.org/) brush engine available separately. Daniel Sabo, Alexia, and Jehan Pagès revisited this mini-project in December and vastly improved it by making its performance comparable to 

<span class="caps">GIMP</span>

's own brush engine and adding support for smoothing brush strokes. Michael also made it possible to tag and filter MyPaint brushes. The tool is now very stable enough and will be enabled by default in future releases of 

<span class="caps">GIMP</span>

.

![MyPaint Brush tool in action](http://www.gimp.org/news/images/2015-report/2015-12-31-mypaint-brush.jpg) MyPaint Brush tool in action. Painting courtesy by Alexia Additionally, Michael Natterer added canvas flipping that nicely complements canvas rotation and is intended to help painters evaluate their work mirrored horizontally or vertically without having to undo the transformation. Thanks to Simon Budig you can map shortcuts to various commands related to both canvas rotation and flipping to speed up your workflow.

![Rotating and flipping the canvas](http://www.gimp.org/news/images/2015-report/2015-12-31-rotate-flip.jpg) Rotating and flipping the canvas. Painting courtesy by Evelyne Schulz We also improved support for various file formats:

- Tobias Ellinghaus from the [darktable](http://www.darktable.org/) team made the 

  <span class="caps">EXR</span>

   loading color-managed by generating an 

  <span class="caps">ICC</span>

   profile from the embedded white point and chromaticities on the fly, assuming linear gamma images.
- ShadowKyogre, Massimo Valentini, and Michael Natterer fixed several existing bugs in the 

  <span class="caps">PSD</span>

   plugin and improved support for 

  <span class="caps">PSD</span>

   files with greater than 8-bit depth precision.
- Mukund Sivaraman and Tobias Ellinghaus added the reading and writing of 

  [<span class="caps">PFM</span>](http://www.pauldebevec.com/Research/HDR/PFM/)

   (portable floatmap) files commonly used in 

  <span class="caps">HDR</span>

   imaging.
- Joao 

  <span class="caps">S. O.</span>

   Bueno implemented exporting and loading group layers in OpenRaster files--a new feature in upcoming MyPaint 1.2.0.

![Adjusting exposure of a 32-bit float PFM file](http://www.gimp.org/news/images/2015-report/2015-12-31-pfm-processing.jpg) Adjusting exposure of a 32-bit float 

<span class="caps">PFM</span>

 file The next development focus was on 

<span class="caps">GIMP</span>

's visual identity.

Benoit Touchette, Michael Natterer, and Jehan Pagès added the ability to switch between icon themes. 

<span class="caps">GIMP</span>

 now ships with an optional symbolic icon theme originally created by Barbara Muraus with contributions from Jakub Steiner, and Klaus Staedtler.

Benoit Touchette also added an experimental dark theme that should work better for people who work on hi-color images. 

<span class="caps">GIMP</span>

 also ships a negative version of the symbolic icon theme to go with the dark theme.

![Editing a photo in GIMP with a dark theme enabled](http://www.gimp.org/news/images/2015-report/2015-12-31-dark-theme.jpg) Editing a photo in 

<span class="caps">GIMP</span>

 with a dark theme enabled. Photo courtesy by Pat David Michael Natterer revisited the screenshot plug-in's source code. It is now easier to add support for new screenshooting backends (Windows, Wayland etc.).

Michael Henning and Massimo Valentini fixed several bugs in the image ruler code that affected performance during painting. This vastly improved painting experience on Linux, but we hear that Windows users still experience issues, so we'd appreciate a wider testing and code contributions. For now, you can temporarily disable the ruler for your painting sessions.

Johannes Matschke contributed an experimental _Handle Transform_ tool that allows scaling, rotation, shearing, and adjusting perspective with handles placed by a user on the canvas. Michael Natterer further improved the tool's usability.

Michael also merged, improved, and marked as experimental another new tool, _N-Point Deformation_, contributed by Marek Dvoroznak a few years ago as the main objective of his Google Summer of Code project. The tool makes it possible to apply rubber-like transformation to objects.

[<span class="caps">GEGL</span>](http://gegl.org)

 0.3.0, released in June, featured over 70 new filters/operations, as well as experimental multithreading and mipmaps support. We do not extensively use mipmaps in 

<span class="caps">GIMP</span>

 yet, but this will change in the coming releases. Further 

<span class="caps">GEGL</span>

 updates released this year reintroduced the loading and saving of video frames via [FFmpeg](http://ffmpeg.org), various optimizations, and more new operations, such as [libraw](http://www.libraw.org) based operation for loading 

<span class="caps">RAW</span>

 images (not yet exposed in 

<span class="caps">GIMP</span>

).

Most of the work we did this year is available in v2.9.2, the first development release in 2.9.x series made in late November. Improved MyPaint Brush tool and visual changes (icon themes, dark theme) will be available in upcoming v2.9.4 release.

We expect to continue finalizing the 

<span class="caps">GEGL</span>

 port and the existing feature set in 2016\. There are two branches with new features to review, but other than that we do not expect any major changes in the 2.9.x series of releases.

Last but not least, we owe a great debt to [Pat David](https://pixls.us/) for redesigning and launching our new website for the 20th anniversary of the project that we celebrated in November.
