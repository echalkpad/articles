# Jack000/Expose

[Original URL](https://github.com/Jack000/expose)

> A simple static site generator for photoessays Intro If you're into photography, you probably have folders of images and videos like this: Expose is a Bash script that turns those images and videos...

A simple static site generator for photoessays

## [](https://github.com/Jack000/expose#intro)Intro

If you're into photography, you probably have folders of images and videos like this:

[![a bunch of images](https://camo.githubusercontent.com/0c5919bdc927809f9c538a25f6434c381c3f1cc0/687474703a2f2f6a61636b2e776f726b732f6578706f7365696d616765732f666f6c6465722e6a7067)](https://camo.githubusercontent.com/0c5919bdc927809f9c538a25f6434c381c3f1cc0/687474703a2f2f6a61636b2e776f726b732f6578706f7365696d616765732f666f6c6465722e6a7067)

Expose is a Bash script that turns those images _and videos_ into a photoessay similar to [jack.ventures](http://jack.ventures) or [jack.works](http://jack.works) (my personal blogs)

If you're not a fan of that look, a [Medium-style theme](http://jack.ventures/sample/inner-mongolia) is included

tested on Windows/Cygwin, OSX, and should be fine on Linux

## [](https://github.com/Jack000/expose#installation)Installation

The only dependency is Imagemagick. For videos FFmpeg is also required.

download the repo and alias the script

```
alias expose=/script/location/expose.sh
```

for permanent use add this line to your ~/.profiles, ~/.bashrc etc depending on system

## [](https://github.com/Jack000/expose#basic-usage)Basic usage

```
cd ~/folderofimages
expose
```

The script operates on your current working directory, and outputs a _site directory. Configuration and settings can be edited in the expose.sh file itself

## [](https://github.com/Jack000/expose#flags)Flags

```
expose -d
```

The -d flag enables draft mode, where only a single low resolution is encoded. This can be used for a quick preview or for layout purposes.

Generated images and videos are not overwritten, to do a completely clean build delete the existing _site directory first.

## [](https://github.com/Jack000/expose#adding-text)Adding text

The text associated with each image is read from any text file with the same filename as the image, eg:

[![images and text files](https://camo.githubusercontent.com/59c1bd2d9b24b44f2b051756c864e56aec4e777b/687474703a2f2f6a61636b2e776f726b732f6578706f7365696d616765732f696d61676574657874322e6a7067)](https://camo.githubusercontent.com/59c1bd2d9b24b44f2b051756c864e56aec4e777b/687474703a2f2f6a61636b2e776f726b732f6578706f7365696d616765732f696d61676574657874322e6a7067)

## [](https://github.com/Jack000/expose#sorting)Sorting

Images are sorted by alphabetical order. To arbitrarily order images, add a numerical prefix

## [](https://github.com/Jack000/expose#organization)Organization

You can put images in folders to organize them. The folders can be nested any number of times, and are also sorted alphabetically. The folder structure is used to generate a nested html menu.

To arbitrarily order folders, add a numerical prefix to the folder name. Any numerical prefixes are stripped from the url.

Any folders, images or videos with an "_" prefix are ignored and excluded from the build.

[![folders](https://camo.githubusercontent.com/40aea267d55a3a0eb1101c8428922b27ac0fa795/687474703a2f2f6a61636b2e776f726b732f6578706f7365696d616765732f666f6c646572732e6a7067)](https://camo.githubusercontent.com/40aea267d55a3a0eb1101c8428922b27ac0fa795/687474703a2f2f6a61636b2e776f726b732f6578706f7365696d616765732f666f6c646572732e6a7067)

## [](https://github.com/Jack000/expose#text-metadata)Text metadata

YAML in the text file is read and made available to the theme. The variables depend on the theme used.

## [](https://github.com/Jack000/expose#theme-1-specific-options)Theme-1 specific options

```
---
top: 30
left: 5
width: 30
height: 20
textcolor: #ffffff
---
```

[![content dimensions](https://camo.githubusercontent.com/e546f55a30927f88cda5779086fcb3b04ed2a944/687474703a2f2f6a61636b2e776f726b732f6578706f7365696d616765732f64696d656e73696f6e7661726961626c65732e6a7067)](https://camo.githubusercontent.com/e546f55a30927f88cda5779086fcb3b04ed2a944/687474703a2f2f6a61636b2e776f726b732f6578706f7365696d616765732f64696d656e73696f6e7661726961626c65732e6a7067)

The units are in percentages

```
---
top: 12
left: 50
width: 40
height: 50
polygon:[{"x":5, "y":0},{"x":100, "y":0},{"x":100, "y":100},{"x":7, "y":55}, {"x":0, "y":16}]
textcolor: #ff9518
---
```

Use a polygon to wrap text around shapes. The polygon is defined by 3 or more points in a JSON blob. Units are again in percentages.

[![content polygon](https://camo.githubusercontent.com/fc5efd36466185d1a53b9db22bf1623d5714f73e/687474703a2f2f6a61636b2e776f726b732f6578706f7365696d616765732f706f6c79676f6e322e6a7067)](https://camo.githubusercontent.com/fc5efd36466185d1a53b9db22bf1623d5714f73e/687474703a2f2f6a61636b2e776f726b732f6578706f7365696d616765732f706f6c79676f6e322e6a7067)

## [](https://github.com/Jack000/expose#theme-2-specific-options)Theme-2 specific options

```
---
width: 32.5
---
```

In theme-2 the width variable acts on the image rather than the content. You can use this to tile images in a row:

[![image row](https://camo.githubusercontent.com/919fd7aead88c860c1f981c91c69ccbf12e2b54b/687474703a2f2f6a61636b2e776f726b732f6578706f7365696d616765732f77696474686f7074696f6e2e6a7067)](https://camo.githubusercontent.com/919fd7aead88c860c1f981c91c69ccbf12e2b54b/687474703a2f2f6a61636b2e776f726b732f6578706f7365696d616765732f77696474686f7074696f6e2e6a7067)

Clicking on each image shows it in full screen mode.

Note that in this theme the text goes above its associated image, except the first image which is used as a masthead.

## [](https://github.com/Jack000/expose#metadata-file)Metadata file

If you want certain variables to apply to an entire gallery, place a metadata.txt (this is configurable) file in the gallery directory. eg. in metadata.txt:

```
width: 19 
```

[![image grid](https://camo.githubusercontent.com/40035fba51571381b0637cb8237552385b4e165a/687474703a2f2f6a61636b2e776f726b732f6578706f7365696d616765732f677269642e6a7067)](https://camo.githubusercontent.com/40035fba51571381b0637cb8237552385b4e165a/687474703a2f2f6a61636b2e776f726b732f6578706f7365696d616765732f677269642e6a7067)

This sets all image widths to form a grid. Metadata file parameters are overriden by metadata in individual posts.

## [](https://github.com/Jack000/expose#advanced-usage)Advanced usage

## [](https://github.com/Jack000/expose#video-options)Video options

Since we're using FFMpeg for video, we can leverage its filter framework for quick effects. This also saves a re-encode from a video editor workflow. Not all the FFmpeg options are applicable, but here are a few I found useful:

```
---
video-options: -ss 10 -t 5
---
```

This will cut the video 10 seconds from the start, with a duration of 5 seconds.

```
---
video-filters: lut3d=file=fuji3510.cube
---
```

If you're like me and shoot video in log profile, doing post work can be a pain. I like to globally apply a [film print emulation LUT](http://juanmelara.com.au/print-film-emulation-luts-for-download/) for a consistent look. Note that FFmpeg will look for the LUT file in the working directory you started the script in. FFmpeg does not support .look LUTs, so you'll have to convert them to one of .cube .3dl .dat or .m3d

[![3d LUT](https://camo.githubusercontent.com/47db3a017b300824804a24829b8d5b683651bbc7/687474703a2f2f6a61636b2e776f726b732f6578706f7365696d616765732f6c757433642e706e67)](https://camo.githubusercontent.com/47db3a017b300824804a24829b8d5b683651bbc7/687474703a2f2f6a61636b2e776f726b732f6578706f7365696d616765732f6c757433642e706e67)

Here I use a nice low-contrast LUT I [found online](http://caseywilsondp.com/2015/03/25/a7s-slog2-rec709-lut/) with excellent highlight rolloff for a cinematic look.

```
---
video-filters: deshake,unsharp=6:6:3,lutyuv="u=128:v=128"
---
```

Applies stabilization to the video and a slight sharpen filter, then converts to grayscale. Separate filters with commas.

A full list of FFmpeg filters can be [found here](https://ffmpeg.org/ffmpeg-filters.html#Video-Filters)

## [](https://github.com/Jack000/expose#image-options)Image options

Similar to videos, we can leverage the image editing features of Imagemagick.

Things like cropping and color correction are very visual operations that are hard to do in command line. Most people would shoot in RAW and export as jpeg anyways, so a lot of ImageMagick's CLI options won't be very useful. However, it is very handy for non-destructively applying effects across an entire gallery, eg:

```
---
image-options: watermark.png -gravity SouthEast -geometry +50+50 -composite 
---
```

You can use this to apply a watermark on the bottom right corner, with a 50 pixel margin from the edge.

```
---
image-options: -sharpen 0x1.5
---
```

Sharpens the image with a 1.5 pixel radius

```
---
image-options: -hald-clut transform.png
---
```

Imagemagick does not read LUTs natively, but will accept a Hald color lookup image. This image can be created in photoshop or other graphics package by applying your LUT to the [Hald identity CLUT image](http://www.quelsolaar.com/technology/clut.html)

```
---
image-options: -colorspace Gray -sigmoidal-contrast 5,50%
---
```

Convert to a black-and-white image. Typically you would want to enhance contrast as well, which can be done by the sigmoidal contrast modifier. The first number controls contrast intensity.

A full list of Imagemagick options can be [found here](http://www.imagemagick.org/script/command-line-options.php)

## [](https://github.com/Jack000/expose#image-sequences)Image sequences

Timelapse and stop-motion are a great way to add motion to a scene. If your folder contains the key word "imagesequence" (this is configurable), the images in the folder will be converted to a video. Video options and filters may be applied to image sequences.

[![Image sequence](https://camo.githubusercontent.com/93ac68246214694fbcf483c731788635e85c5cda/687474703a2f2f6a61636b2e776f726b732f6578706f7365696d616765732f696d61676573657175656e63652e676966)](https://camo.githubusercontent.com/93ac68246214694fbcf483c731788635e85c5cda/687474703a2f2f6a61636b2e776f726b732f6578706f7365696d616765732f696d61676573657175656e63652e676966)

By default the video is encoded at 24fps.
