# How to use CSS and SVG clipping and masking techniques

[Original URL](https://getflywheel.com/layout/css-svg-clipping-and-masking-techniques/?utm_source=codropscollective)

> How To SVGs are great for working on the web, and clipping and masking allows for some interesting ways to show or hide pieces of your web graphics. Using these techniques also allows for more...

![Abbey Fitzgerald](http://getflywheel.com/wp-content/uploads/2014/11/Abbey-Fitzgerald_avatar_1416195379-80x80.jpg) [How To](https://getflywheel.com/layout/category/how-to/) SVGs are great for working on the web, and clipping and masking allows for some interesting ways to show or hide pieces of your web graphics. Using these techniques also allows for more flexibility with your designs because you do not have to manually make changes and create new images – it's all done with code. By using a combination of CSS and masking techniques, you will have lots of options for your website graphics.

To help clarify things, masking and clipping are two different ways you can manipulate images with CSS. Let's start with clipping.

## Clipping basics

If you've ever used Photoshop, you are probably already familiar with Clipping Masks. It's a similar kind of approach. Clipping involves laying a vector shape, like a circle or a triangle, on top of an image or an element. Any part of the image behind the shape will be visible, while everything outside the boundaries of the shape will be hidden.

For example, if a triangle clipping mask is over the top of an image of a forest, you will see the forest image within the triangle shape. The shape's boundary is called the clip path, not to be confused with the depreciated `clip` property. You create the clip path by using the `clip-path` property.

<embed src="https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-clipping-graphic-300x115.jpg%20300w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-clipping-graphic-157x60.jpg%20157w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-clipping-graphic-225x86.jpg%20225w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-clipping-graphic-249x95.jpg%20249w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-clipping-graphic-498x190.jpg%20498w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-clipping-graphic-144x55.jpg%20144w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-clipping-graphic-288x110.jpg%20288w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-clipping-graphic-276x105.jpg%20276w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-clipping-graphic-552x211.jpg%20552w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-clipping-graphic.jpg%20710w" class="aligncenter size-full wp-image-18288" width="710">

**Clipping in action**

Clips are always vector paths. It can be confusing to understand, but anything outside the path will be hidden, while anything inside the path will be visible. To get a better understanding and to test this out yourself, see the sample on [CodePen](http://codepen.io/abbeyjfitzgerald/pen/dXZXNr).

<embed src="https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-triangle-example-300x124.jpg%20300w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-triangle-example-157x65.jpg%20157w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-triangle-example-225x93.jpg%20225w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-triangle-example-230x95.jpg%20230w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-triangle-example-460x190.jpg%20460w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-triangle-example-133x55.jpg%20133w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-triangle-example-267x110.jpg%20267w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-triangle-example-276x114.jpg%20276w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-triangle-example-552x228.jpg%20552w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-triangle-example.jpg%20710w" class="aligncenter size-full wp-image-18295" width="710">

Here is a snippet of the HTML structure from the example:

```
<svg class="clip-svg">
    <defs>
        <clipPath id="polygon-clip-triangle-equilateral" clipPathUnits="objectBoundingBox">
            <polygon points="0 0.87, 0.5 0, 0.5 0, 1 0.87" />
        </clipPath>
    </defs>
</svg>
```

This is the CSS to make the clipping happen:

```
.polygon-clip-triangle-equilateral {
    -webkit-clip-path: polygon(0% 87%, 50% 0%, 50% 0%, 100% 87%);
    clip-path: polygon(0% 87%, 50% 0%, 50% 0%, 100% 87%);
    -webkit-clip-path: url("#polygon-clip-triangle-equilateral");
    clip-path: url("#polygon-clip-triangle-equilateral");
}
```

You can see where the `clipPath` id is being referenced in the HTML and how it uses the `clip-path` URL to do the clipping.

**Clippy tool**

[Clippy](http://bennettfeely.com/clippy/) is a great tool to generate CSS clip paths. There are a wide variety of starter shapes and sizes that can be customized.

<embed src="https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-clippy-300x150.png%20300w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-clippy-157x78.png%20157w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-clippy-225x112.png%20225w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-clippy-191x95.png%20191w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-clippy-381x190.png%20381w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-clippy-110x55.png%20110w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-clippy-221x110.png%20221w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-clippy-276x138.png%20276w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-clippy-552x275.png%20552w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-clippy.png%20710w" class="aligncenter size-full wp-image-18289" width="710">

## **Masking basics**

Masking is done using a PNG image, CSS gradient, or an SVG element to hide part of an image or another element on the page. We will be focusing on SVG graphics, but keep in mind this can be done with other image types or styles.

**The mask property and mask element**

Just a refresher to help visualize, it's important to keep in mind that the masked element is the "original" (before mask is applied) image. You may not want to see the whole image, so hiding parts of it is done with the CSS `mask` property. The `mask` is the CSS shorthand for a group of individual properties, which we'll dive into in a second. The SVG `<mask>` element is used inside an SVG graphic to add masking effects. In this example, the mask is a circle and there is also a gradient applied.

**Using the SVG mask element on an SVG graphic**

To get a feel for the SVG `<mask>` we will be masking with an SVG graphic.

It might be a bit complex at a first glance, but it all works together to mask the underlying image. We have an actual image as a background, so where does the SVG come into play? Unlike the clipping examples, this background image is technically inside of an SVG element. We'll use CSS to apply this mask to the image. Properties will come from the SVG mask element, and we'll give it the id of `masked-element` in our CSS.

To see this in action, check out this [Codepen sample](http://codepen.io/abbeyjfitzgerald/pen/mELOpy). Here's the working code for the masked SVG graphic:

```
<svg class="masked-element" width="300" height="300" viewBox="0 0 300 300">
    <image xlink:href="image link" width="300px" height="300px" />
</svg>
```

<embed src="https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-masked-outline-300x116.png%20300w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-masked-outline-157x61.png%20157w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-masked-outline-225x87.png%20225w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-masked-outline-245x95.png%20245w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-masked-outline-491x190.png%20491w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-masked-outline-142x55.png%20142w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-masked-outline-284x110.png%20284w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-masked-outline-276x107.png%20276w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-masked-outline-552x214.png%20552w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-masked-outline.png%20710w" class="wp-image-18290 size-full" width="710">

 if you go to the Inspector, you can see the boundaries for the masked element. The circular shape is created with a mask.

With this CSS, we are specifying where to find the mask. It will look for the ID of `#mask-this:`

```
/* Here’s the CSS for masking */
.masked-element image {
 mask: url(#mask1);
}
```

<embed src="https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-svg-mask-300x85.png%20300w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-svg-mask-225x64.png%20225w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-svg-mask-334x95.png%20334w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-svg-mask-668x190.png%20668w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-svg-mask-193x55.png%20193w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-svg-mask-387x110.png%20387w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-svg-mask-276x79.png%20276w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-svg-mask-552x157.png%20552w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-svg-mask.png%20710w" class="aligncenter size-full wp-image-18293" width="710">

Notice a gradient in the circle shape? A gradient has been applied, as well as setting the circle shape for the mask.

```
<svg class="svg-mask">
    <defs>
        <mask id="mask1" maskUnits="objectBoundingBox" maskContentUnits="objectBoundingBox">
 <linearGradient id="grad" gradientUnits="objectBoundingBox" x2="0" y2="1">
 <stop stop-color="white" offset="0"/>
 <stop stop-color="green" stop-opacity="0" offset="1"/>
 </linearGradient>
 <circle cx="0.50" cy="0.50" r="0.50" id="circle" fill="url(#grad)"/>
    </mask>
    </defs>
</svg>
```

## SVG text masking

Text masking can do some pretty cool things, such as showing an image through a text block. The good news is that a text element can be used inside of an SVG mask. As browser support increases in the future, this could be a really interesting way to combine images and typography.

<embed src="https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-text-mask-300x94.png%20300w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-text-mask-157x49.png%20157w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-text-mask-225x70.png%20225w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-text-mask-304x95.png%20304w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-text-mask-608x190.png%20608w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-text-mask-176x55.png%20176w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-text-mask-352x110.png%20352w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-text-mask-276x86.png%20276w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-text-mask-552x173.png%20552w,%20https://getflywheel.com/wp-content/uploads/2016/08/css-svg-clipping-masking-text-mask.png%20710w" class="aligncenter size-full wp-image-18294" width="710">

Here's a basic explanation of what is going on. There is an SVG text element inside the SVG mask. We've specified the RGB value for white, which creates the oval area around the masked text. Anything behind the oval area shows through the text, giving a cut-out feel.

```
<div class="text-wrap">
 <svg class="text-demo" viewBox="0 0 600 400" width="600" height="400">
 <defs>
 <mask id="myMask">
 <rect width="100%" height="100%" fill="#fff" />
 <text x="50" y="200" id="myText">My Text</text>
 <text x="125" y="293" id="mySubtext">SVG</text>
 </mask>
 </defs>
 <ellipse class="masked" cx="300" cy="200" rx="300" ry="150" fill="rgba(255, 255, 255, 0.8)" />
 </svg>
 </div>


/* Here’s the CSS for text element */
#myText {
font-size: 125px;
font-style: bold;
fill: #000;
}

/* Here’s the CSS for masking */
.masked {
mask: url("#myMask");
}
```

To fully understand, it's helpful to play around and experiment with the code. Try changing colors, changing text, and adjusting sizes in this [Codepen](http://codepen.io/abbeyjfitzgerald/pen/jAxwOk).

## Browser support

Before committing to this new way of working with graphics, it's important to note that browser support is not consistent with clipping and masking. Clipping is more supported than masking, but Internet Explorer does not fully support clipping. Current browser support for CSS masks is also fairly limited, so it's suggested to be used as an enhancement on a few decorative elements. That way, if it is not supported by the user's browser, it does not affect the content viewing experience.

To test things out and see if your masks and clippings are supported, I'd recommend making a [JSFiddle](https://jsfiddle.net/) or [Codepen](http://codepen.io/) and then trying it in different browsers. Don't let the limitations get you down – it's always good to be ahead of the game and once support is more mainstream, you will know exactly how to revolutionize your graphics. When in doubt, be sure to reference the trusted [Can I Use](http://caniuse.com/#feat=css-masks).

After experimenting with these examples, this should provide a good introduction to masking and clipping. Although browser support is limited at this time, this will likely become a mainstream practice in the future. It's always fun to think about how these techniques can be used to create interesting visuals. The future of web graphics will make us less dependent on image editors and allow for more effective ways to create and modify imagery directly in the browser.

![](https://getflywheel.com/wp-content/themes/flywheel15/images/shortcode-icon.png)
