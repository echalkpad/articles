# CSS Tools & Techniques

[Original URL](http://www.teaching-materials.org/csstools/)

> Cross-Browser CSS Browsers Browsers interpret and present information on the web. Each browser varies in how it displays webpages. Layout engine: a program browsers run to render markup (HTML) and...

## Cross-Browser CSS

## Browsers

- Browsers interpret and present information on the web.
- Each browser varies in how it displays webpages.
- Layout engine: a program browsers run to render markup (HTML) and formatting (CSS).
- Most popular are WebKit (Apple), Blink (Chrome), Gecko (Mozilla)

## Browsers

Each browser varies in how it displays webpages.

Example: Web browser vs mobile browsers.

![](http://www.teaching-materials.org/csstools/img/cnn-web.png) ![](http://www.teaching-materials.org/csstools/img/cnn-iphone.png)

## Browsers

Each browser varies in how it displays webpages.

Example: Default form fields.

![](http://www.teaching-materials.org/csstools/img/selectbox.png)

## Browser Compatibility

- The language of the web and standards evolve.
- Older browsers do not know about newer CSS selectors or properties (or newer HTML attributes for that matter!)
- Usually they ignore unknown selectors, sometimes unpredictable.

![](http://www.teaching-materials.org/csstools/img/border-radius-support.png)

Use these sites to check compatibility:

- [caniuse.com](http://caniuse.com/)
- [Quirks Mode: CSS Compatibility](http://www.quirksmode.org/css/contents.html)
- [html5please.com](http://html5please.com/)

## Browser Compatibility

## Browser Bugs

## Browser Testing

Since not all browsers work on all operating systems (IE on Mac), you may need help from these tools for cross-browser testing:

## Cross-Device CSS

## CSS Media Queries

A media query consists of a media type and an optional combination of media features. CSS rules inside the query will be applied only if the query is true.

```
@media <media types> and <media features> {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old media-specific rules */
}
```

They can also be specified for a linked stylesheet:

```
<link rel="stylesheet" media="<media types>
 and <media features>" href="alternative.css" />
```

<https://developer.mozilla.org/en/CSS/@media\#Media\_groups> <https://developer.mozilla.org/en/CSS/Media\_queries>

## Print Styles

All modern browsers support the "print" media type for specifying print styles.

```
@media print {
 body {
 background: white;
 }
 #header, #footer {
 display: none;
 }
 .section {
 page-break-inside: avoid;
 page-break-after: always;
 }
}
```

<http://reference.sitepoint.com/css/pagedmedia>

## Resolution Queries

Modern mobile browsers support media queries which depend on the `device-width` and/or `orientation`:

Targeting iPads:

```
@media only screen
 and (min-device-width : 768px)
 and (max-device-width : 1024px) {
 img {
 width: 100%;
 }
}
```

Read more: [Media Queries for Standard Devices](http://css-tricks.com/snippets/css/media-queries-for-standard-devices/)

## Resolution Queries

A more general approach is just to query on `min-width` and `max-width`, which are calculated based on window size, not device size.

```
@media screen and (max-width: 640px) {
 #sidebar {
 display: none;
 }
}

@media (min-width: 768px) and (max-width: 979px) {
 #sidebar {
 width: 200px;
 }
}
```

<http://css-tricks.com/css-media-queries/> <http://css-tricks.com/resolution-specific-stylesheets/>

## Pixel Density Queries

The experimental `device-pixel-ratio` feature can be used to detect pixel density:

```
@media only screen and (-webkit-min-device-pixel-ratio: 1.5),
 only screen and (-o-min-device-pixel-ratio: 3/2),
 only screen and (min--moz-device-pixel-ratio: 1.5),
 only screen and (min-device-pixel-ratio: 1.5) {
 #logo {
 background-image:url(images/icon@2x.png);
 }
}
```

<https://developer.mozilla.org/en/CSS/Media\_queries\#-moz-device-pixel-ratio>

## Resolution Testing

Besides testing on the actual devices or resizing the browser, you can use these tools:

- [Resolutionizer](http://pamelafox.github.com/resolutionizer/)
- [ScreenQueries](http://screenqueri.es/)
- [Adobe Shadow](http://labs.adobe.com/technologies/shadow/)
- [Firefox Responsive Design View](https://developer.mozilla.org/en-US/docs/Tools/Responsive_Design_View)

## Good Defaults

## CSS Resets

- Not the same blank slate - browsers define different default styles.
- CSS reset stylesheets are used to normalize the default CSS across browsers.

[Reset.css](http://meyerweb.com/eric/tools/css/reset/) removes every default style.

[Normalize.css](http://necolas.github.com/normalize.css/) normalizes the default styles across browsers (and is the preferred approach).

## CSS Grids

A grid system lets you layout pages according to fractions of a grid (usually 12, 16, or 24 columns).

Example: Foundation Grid System

![](http://www.teaching-materials.org/csstools/img/foundation-grid.png)

## CSS Grids

A grid system lets you layout pages according to fractions of a grid (usually 12, 16, or 24 columns).

Example: Bootstrap Grid System

![](http://www.teaching-materials.org/csstools/img/bootstrap-grid-system.jpg)

## CSS Grids

Example: Foundation grid with mixed classes

Using [Foundation CSS Grids](http://foundation.zurb.com/sites/docs/v/5.5.3/components/grid.html):

```
 <link rel="stylesheet" type="text/css" href="https://cdnjs.cloudflare.com/ajax/libs/foundation-essential/6.0.6/css/foundation.css">
 <div class="row">
 <div class="small-2 large-4 columns"> ... </div>
 <div class="small-4 large-4 columns"> ... </div>
 <div class="small-6 large-4 columns"> ... </div>
 </div>
```

![](http://www.teaching-materials.org/csstools/img/foundation-example.png)

Also see: [960 Grids](http://960.gs/), [1KB Grid](http://1kbgrid.com/)

## CSS Grids

![](http://www.teaching-materials.org/csstools/img/devtools.png)

## CSS Frameworks

- Why reinvent the wheel?
- Responsiveness and cross browser consistency for free.
- Modular and reusable components.

## CSS Frameworks

Usually include a reset, a grids system, styles for typography and forms. Some include support for mobile/print.

With [Twitter Bootstrap](http://twitter.github.com/bootstrap/):

```
<link href="https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/2.3.2/css/bootstrap.css" rel="stylesheet">
<div class="container-fluid">
 <div class="row">
 <div class="span4 well">
 </div>
 <div class="span8">
 <div class="alert">Hello!</div>
 </div>
 </div>
</div>
```

Also see: [ZURB Foundation](http://foundation.zurb.com/), [Pure CSS](http://purecss.io), [Topcoat](http://topcoat.io)

## Extras

## CSS workflow in large apps

## Improving rendering speed with CSS
