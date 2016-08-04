# Building a CMS: Structure and Styling

[Original URL](http://code.tutsplus.com/tutorials/building-a-cms-structure-and-styling--cms-24547)

> What You'll Be CreatingThere has been a trend of "flat CMS design" webservers. This simply refers to a Content Management System (CMS) that does not store its information in a database....

![Final product image](https://cms-assets.tutsplus.com/uploads/users/71/posts/24547/final_image/BuildingCMS-Structure-01.jpg) What You'll Be Creating There has been a trend of "flat CMS design" webservers. This simply refers to a Content Management System (CMS) that does not store its information in a database. Instead, all information is stored in plain text files. Since many virtual private servers (VPS) these days are using RAM-based hard drives, this approach is faster and a bit more secure.

In this series of tutorials, I will show you how to create your own flat file system CMS using some standard web technology. These systems are not as full featured as others, but have the basics with a good response time.

With all the information on the hard drive, file organization becomes a big issue. With that in mind, the site information will be in a different directory than the styling and layout information. Also, each type of page has its own directory under the site folder with a parts directory for small parts of the CMS. This keeps everything in its own place and makes a flexible system.

## Main Directory Structure

In the directory in which you are going to build the project, create the following directories: **src**, **site**, and **themes**. The **src** directory will contain the server code, the **site** directory is for all the site information, and the **themes** directory for the layout and theming information.

In the **themes** directory, you need to make a **layouts** and a **styling** directory. The **layouts** directory will contain the different web page layouts. By separating the layout information from the styling information, the themes become much more flexible. For now, there will be one layout called **SingleCol**.

For all the styling and layout creation, I am using **[Sass](http://sass-lang.com/)**, **[Compass](http://compass-style.org/)**, and **[Susy](http://susy.oddbird.net/)**. **Sass** is a Custom Style Sheet processing language. It gives a more robust way to create the CSS style sheets for your website. **Compass** is an extension to Sass. **Sassy-buttons** is a Sass extension for making nice-looking buttons on the website. **Susy** is also an extension for creating a grid layout system for your site.

Since Ruby is pre-installed on all Macs, you will not need to install it. To get Ruby on a Windows system, you will need to download [Ruby's Windows Installer](http://rubyinstaller.org/). On Linux, you need to use your system's package manager to install Ruby.

Once Ruby is on your system, you can install Sass, Compass, Sassy-buttons, and Susy with these command lines:

```
gem install sass
gem install sassy-buttons
gem install compass
gem install susy
```

For this tutorial, I am using Sass 3.4.16, Sassy-buttons 0.2.6, Compass 1.0.3, and Susy 2.2.5\. Depending on your system's configuration, you might have to run these commands with `sudo` before them.

## Layouts

To get started creating layouts, run these commands in the `SingleCol` directory:

```
compass init
```

This will create the **sass** and **stylesheets** directories, and a file named **config.rb**. Since I like using **css** for the stylesheet directory, rename the **stylesheets** directory to **css**. Also, create a **js** directory for any needed JavaScript files for the layout. Open the **config.rb** file and make it look like this one:

```
require 'susy'
http_path = "/"
css_dir = "css"
sass_dir = "sass"
images_dir = "images"
javascripts_dir = "js"
```

Now, to create the basic layout of the website, create a file in the **layout** directory called **template.html**. In this file, add this code:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" lang="en" xml:lang="en">
<head>
    <meta name="generator" content="goPress Servery by Custom Computer Tools.">
    <meta charset="utf-8">
    <title>
        {{{title}}}
    </title>
    <link rel="stylesheet" type="text/css" href="{{{WebAddress}}}/stylesheets.css">
    {{{head}}}
</head>

<body>
    <div id='wrap'>
        <div id='headerwrap'>
            <div id='header'>
                {{{header}}}
            </div>
            <div id='navigation'>
                {{{navbar}}}
            </div>
        </div>
        <div id='sidebar'>
            {{{sidebar}}}
        </div>
        <div id='content'>
            {{{content}}}
        </div>
        <div id='footer'>
            {{{footer}}}
        </div>
    </div>
    <script type="text/javascript" src="{{{WebAddress}}}/scripts.js"></script>
</body>

</html>
```

This template creates a standard web page for the site. Each page has a header with a navigation bar, a single sidebar, a content area, and a footer area. The last thing loaded is the javascripts needed for the site.

Each section is represented with a **Handlebar** macro. The server expands the macros before giving it to the user.

In the **sass** directory, create a file called **base.scss** and place this code:

```
@import 'compass/reset';
@import 'susy';

$susy: (
 flow: ltr,
 math: fluid,
 output: float,
 gutter-position: after,
 container: auto,
 container-position: center,
 columns: 16,
 gutters: .25,
 column-width: false,
 global-box-sizing: content-box,
 last-flow: to,
 debug: (
 image: hide,
 color: rgba(#66f, .25),
 output: background,
 toggle: top right,
 ),
 use-custom: (
 background-image: true,
 background-options: false,
 box-sizing: true,
 clearfix: false,
 rem: true,
 )
);

body {
}

#wrap {
 @include container(16);
 width: 1024px;
 display: block;
}

#headerwrap {
 @include span(16 of 16);
 margin-bottom: 20px;
}

#header {
 margin-top: 20px;
 margin-left: auto;
 margin-right: auto;
 width: 95%;
}

#content {
 @include span(11 of 16);
 }

.col1 {
 @include span(5 of 10);
}

.col2 {
 @include span(last 5 of 10);
}

#footer .col2 {
 width: auto;
}

.box {
 @include span(4 of 10);
}

#sidebar {
 @include span(last 4 of 16);
}

#footer {
 @include span(16 of 16);
}

/** CSS dropdown menu **/

#navigation {
 margin-top: 20px;
 margin-left: auto;
 margin-right: auto;
 width: 95%;
}

#menuh-container {
 top: 1em;
 left: 1em;
 display: inline;
 width: 100%;
}

#menuh {
 margin-top: 1em;
 display: inline;
 width: 100%;
}

#menuh ul li {
 display: inline-block;
 width: fit-content;
}

#menuh a
 {
 text-align: center;
 display:block;
 white-space:nowrap;
 margin:0;
 padding: 5px;
 text-decoration: none;
 }

#menuh ul
 {
 list-style:none;
 margin: 0px 20px 0px 20px;
 padding: 0px;
 }

#menuh li
 {
 position:relative;
 min-height: 1px;
 vertical-align: bottom; 
 width: fit-content;
 }

#menuh ul ul
 {
 position: absolute;
 z-index: 500;
 top: 50px;
 left: 20px;
 display: none;
 padding: 0.5em;
 margin: -1em 0 0 -1em;
 }

 #menuh ul ul li {
 width: 100%;
 }

#menuh ul ul li a {
 text-align: left;
}

#menuh ul ul ul
 {
 left: 90px;
 }

div#menuh li:hover
 {
 cursor:pointer;
 z-index:100;
 }

div#menuh li:hover ul ul,
div#menuh li li:hover ul ul,
div#menuh li li li:hover ul ul,
div#menuh li li li li:hover ul ul
{display:none;}

div#menuh li:hover ul,
div#menuh li li:hover ul,
div#menuh li li li:hover ul,
div#menuh li li li li:hover ul
{display:block;}

/* End CSS Drop Down Menu */
```

This **sass** code loads in the compass reset styles to neutralize the browser defaults. Then it loads and sets up **susy** for creating the proper grid layout for all the elements of the web page.

The **css** navigation system is after the page defines. The hidden drop-downs for the menus become visible with mouse-over definitions. This gives a **css** only menu system.

All of these styles define the basic structure of the website. Nothing here creates a look to the page, just its positioning. All styling gets handled by the **styling** content.

## Styles

For the **styling** directory, create a directory called **Basic**. Initialize the **sass** information as done for the **layouts/SingleCol** directory. In this directory, run the following command line:

```
compass init
```

This will create the **sass** and **stylesheets** directories, and a file named **config.rb**. Since I like using **css** for the stylesheet directory, rename the **stylesheets** directory to **css**. Also, create a **js** directory for any JavaScript for creating the theme. Open the **config.rb** file and make it look like this one:

```
require 'sassy-buttons'
http_path = "/"
css_dir = "css"
sass_dir = "sass"
images_dir = "images"
javascripts_dir = "js"
```

For styling buttons on the website, I like to use **sassy-buttons**. Therefore, it is required first, and then the directory structure.

In the **Basic/sass** directory, create the **Basic.scss** file with this information:

```
// Welcome to Compass.
// In this file you should write your main styles. (or centralize your imports)
// Import this file using the following HTML or equivalent:
// <link href='/stylesheets/screen.css' media='screen, projection' rel='stylesheet' type='text/css' />
//
@import 'compass/css3';
@import 'sassy-buttons';

$style-color1: rgb(247, 237, 222);
$style-color2: #ffedd1;
$style-color3: rgb(245, 213, 166);
$style-color4: #f0d5ad;

//
// Clear Fix
//
.clearfix:after {
 content: '.';
 display: block;
 clear: both;
 visibility: hidden;
 line-height: 0;
 height: 0;
}

.clearfix {
 display: inline-block;
}

html[xmlns] .clearfix {
 display: block;
}

* html .clearfix {
 height: 1%;
}

//
// Main Styling for Wood.
//
@mixin standardsize {
 padding-top: 10px;
 padding-left: 15px;
 font-size: 19px;
 line-height: 1.25em;
}

body {
 background-color: $style-color2;

 input[type='button'] {
 @include sassy-button('shiny', 10px, 16px, $style-color1, darken($style-color1, 20%), #000, bold);
 }

 .button {
 @include sassy-button('shiny', 10px, 16px, $style-color1, darken($style-color1, 20%), #000, bold);
 display: inherit;
 margin-left: auto;
 margin-right: auto;
 margin-top: 10px;
 margin-bottom: 10px;
 text-align: center;
 }

 #wrap {
 background-color: $style-color1;
 @include border-radius(.6em, .6em);
 margin-top: 10px;
 margin-bottom: 10px;
 border: 4px solid $style-color4;

 #header {
 background-color: $style-color2;
 @include border-radius(15px);
 background-repeat: no-repeat;
 border: 2px solid $style-color3;
 height: 130px;
 text-align: left;
 margin-top: 20px;
 font-size: 3em;

 h1 {
 a:link, a:visited {
 color: #000;
 text-decoration: none;
 }
 padding-top: 40px;
 padding-left: 20px;
 }
 h6 {
 font-size: .4em;
 font-style: italic;
 padding-left: 20px;
 }
 }
 #sidebar {
 background-color: $style-color2;
 @include border-radius(.6em, .6em);
 padding: .6em;
 vertical-align: text-top;
 overflow: hidden;
 margin-right: .5em;
 border: 2px solid $style-color3;
 hr {
 color: $style-color2;
 background-color: $style-color2;
 }
 p, ul, li {
 @include standardsize;
 }
 ul li {
 list-style-type: disc;
 margin-left: 25px;
 padding: 0;
 border: 0;
 outline: 0;
 font-size: 100%;
 vertical-align: baseline;
 background: transparent;
 }
 li {
 margin-bottom: 10px;
 }
 h1, h2, h3, h4, h5, h6 {
 @include standardsize;
 font-weight: bold;
 margin-top: .25em;
 margin-bottom: .25em;
 }
 h1 {
 font-size: 2.5em;
 }
 h2 {
 font-size: 2em;
 }
 h3 {
 font-size: 1.5em;
 }
 h4 {
 font-size: 1em;
 }
 h5 {
 font-size: .8em;
 }
 h6 {
 font-size: .6em;
 }
 input[type='button'] {
 margin-left: 120px;
 clear: both;
 }
 }
 #content {
 input[type='button'] {
 margin-left: 200px;
 clear: both;
 }
 h1, h2, h3, h4, h5, h6 {
 @include standardsize;
 font-weight: bold;
 margin-top: .25em;
 margin-bottom: .25em;
 }
 h1 {
 font-size: 2.5em;
 }
 h2 {
 font-size: 2em;
 }
 h3 {
 font-size: 1.5em;
 }
 h4 {
 font-size: 1em;
 }
 h5 {
 font-size: .8em;
 }
 h6 {
 font-size: .6em;
 }
 hr {
 margin-top: 30px;
 margin-bottom: 30px;
 }
 p, ul, li, details, summary, pre {
 @include standardsize;
 }
 details {
 p, pre {
 margin-left: 25px;
 }
 }
 ul li {
 list-style-type: disc;
 margin-left: 25px;
 padding: 0;
 border: 0;
 outline: 0;
 font-size: 100%;
 vertical-align: baseline;
 background: transparent;
 }
 li {
 margin-bottom: 10px;
 }
 .box {
 clear: both;
 background-color: $style-color4;
 float: none;
 margin-left: auto;
 margin-right: auto;
 margin-top: 1.0em;
 margin-bottom: 1em;
 @include border-radius(.6em, .6em);
 display: block;
 padding: .5em;
 }
 img {
 @include border-radius(10px);
 margin: 20px auto 20px auto;
 }
 }
 #footer {
 border-top: 5px;
 border-style: solid;
 border-color: $style-color3;
 @include border-radius(.6em, .6em);
 margin-top: 30px;
 p {
 margin-bottom: .6em;
 @include standardsize;
 margin-right: 15px;
 }
 }
 }
}

// CSS dropdown menu
#navigation {
 text-align: left;
 border: 2px solid $style-color3;
 background-color: $style-color2;
 @include border-radius(15px);
}

#menuh {
 font-size: 1.3em;
 font-family: arial, helvetica, sans-serif;
 background-color: $style-color1;
}

#menuh ul {
 background-color: $style-color2;
}

#menuh ul ul {
 background-color: $style-color2;
 @include border-radius(15px);
 border: 2px solid $style-color3;
}

#menuh a {
 background-color: $style-color2;
 color: #000;
}

#menuh a:link, #menuh a:visited, #menuh a:active {
 color: #000;
 background-color: $style-color2;
}

#menuh a:hover {
 color: #000;
 background-color: $style-color4;
 @include border-radius(5px);
}

// End CSS Drop Down Menu
// Misc classes
.picture {
 border: 3px solid $style-color3;
 @include border-radius(8px);
 float: left;
 margin: 5px 15px 15px 15px;
}

a:link, a:visited {
 color: darken($style-color1, 50);
 text-decoration: none;
}

strong {
 font-weight: bold;
}

table {
 margin: 20px;
 border: 3px solid;
 @include border-radius(10px);
 border-color: lighten($style-color2, 6);
 th {
 text-align: center;
 font-weight: bold;
 padding: 10px 0 10px 0;
 }
 tbody {
 td {
 padding: 10px;
 }
 tr:nth-child(2n+1) {
 background-color: darken($style-color2, 5);
 }
 tr:nth-child(even) {
 background-color: lighten($style-color2, 2.5);
 }
 }
}

#commentSpacer {
 width: 100%;
 margin-top: 20px;
 margin-left: 15px;
}

.tutorial_excerpt {
 margin-left: 20px;
}

.tutorial_thumbnail {
 float: left;
 margin-right: 20px;
 margin-left: 20px;
 margin-bottom: 20px;
}

.tutorial_wrap {
 margin-bottom: 50px;
 float: left;
}

img.wp-post-image {
 -moz-border-radius: 15px;
 border-radius: 15px;
 box-shadow: 10px 10px 5px gray;
}

.showcode {
 margin: 20px auto 20px 30px;
 -moz-border-radius: 15px;
 border-radius: 15px;
 border: $style-color4 3px;
 border-style: solid;
 background: white;
}

#socialmedia {
 width: 700px;
 margin-top: 20px;
 margin-left: 15px;
}

#socialbuttons {
 margin: auto;
}

#socialbuttons a {
 opacity: 0.8;
 filter: alpha(opacity = 80);
 -webkit-transition: all ease-in-out 0.2s;
 -moz-transition: all ease-in-out 0.2s;
 -ms-transition: all ease-in-out 0.2s;
 -o-transition: all ease-in-out 0.2s;
 transition: all ease-in-out 0.2s;
}

#socialbuttons a:hover {
 opacity: 1;
 filter: alpha(opacity = 100);
}

#socialbuttons a {
 display: inline-block;
 height: 28px;
 width: 30px;
 background-image: url(/images/ico-subscribe-social.png);
 background-repeat: no-repeat;
}

#socialbuttons a.twitter {
 background-position: -30px 0;
}

#socialbuttons a.facebook {
 background-position: -60px 0;
}

#socialbuttons a.googleplus {
 background-position: -210px 0;
}

dl {
 margin-left: 20px;
 margin-top: 20px;
 margin-bottom: 20px;
 font-size: 19px;
 line-height: 1.25em;
}

dt {
 margin-left: 20px;
 margin-bottom: 20px;
 font-weight: bold;
}

dd {
 margin-left: 40px;
 margin-bottom: 20px;
}
```

This code defines how the website will look. It defines the background, coloring, fonts, etc. that make up the styling for a typical website.

The **styling/Basic** directory contains the files needed for the default look of the 404 page, header, footer, and sidebar parts of the website. Therefore, create a file called **404.html** and place this code:

```
<h2>404 Page</h2>

<p>Sorry, we could not find the page.</p>
```

This is a basic **404** error page. You have to be careful of the information given here, as you do not want to reveal too much information about how your site works. Since this will be placed in the _contents_ area of the page, it will be easy for the user to navigate away from it.

Next, create the **header.html** file with this content:

```
<h1><a href='/'>Test Site</a></h1>
<h6>A New Approach</h6>
<br>
```

This is just a bare-bones header that is non-descriptive, but it gives the CMS something to show.

Next, create the **footer.html** file with this content:

```
<footer>
[Column1]
    <p>Copyrighted 2012 by <a href="{{{WebAddress}}}/">Your Company</a>
[/Column1]
[Column2]
    <p>Running on a flat CMS server!</p>
[/Column2]
</footer>
```

Next, create the **sidebar.html** file with this content:

```
<h3>A Sidebar</h3>
<p>This is a basic sidebar that can have other times.</p>
```

Again, these are place-holders. All of these files get copied to the **site/parts** directory and changed according to the site's needs. These are here so that the server has something with their names to use in the server in case the user forgets to create them.

## Site Structure

With the theming and layouts defined, it is time to create the basic content and structure for the actual site. In the **site** directory, create the **parts**, **pages**, **posts**, **images**, **css**, **js** directories.

### Parts

The **parts** directory will contain small snippets of **html/css/javascript** that will make a single unit placed in multiple locations of the website. These parts are loading into the server and combined with the page contents as needed. For our demo site, create the following parts:

- **footer.html** with this code:

<!--  -->

```
<footer>
    <p style="float: left;">Copyrighted 2012 by <a href="{{{WebAddress}}}/">Your Company</a>
    <p style="float: right;">Running on a flat CMS server!</p>
</footer>
<script type="text/javascript">
window.PageName = "{{{PageName}}}";
</script>
```

- **head.html** with this code:

<!--  -->

```
<link rel="shortcut icon" href="{{{WebAddress}}}/images/favicon.ico" />
```

- **header.html** with this code:

<!--  -->

```
<h1><a href='{{{ServerAddress}}}/'>Test Site</a></h1>
<h6>Flat File System CMS</h6>
```

- **navbar.html** with this code:

<!--  -->

```
 <div id="menuh-container">
 <div id="menuh">
 <ul>
 <li>
 <a class="top_parent">Pages</a>
 <ul>
 <li>
 <a href="{{{WebAddress}}}/flatcms">Flat CMS</a>
 </li>
 </ul>
 </li>
 <li>
 <a href="{{{WebAddress}}}/posts/blogs/flatcms">Blog</a>
 </li>
 <li>
 <a href="{{{WebAddress}}}/posts/news/flatcms" class="top_parent">News</a>
 </li>
 <li>
 <a href="{{{WebAddress}}}/about" class="top_parent">About</a>
 </li>
 </ul>
</div>
</div>
```

- **sidebar.html** with this code:

<!--  -->

```
<h2>Side Bar</h2>
<hr>
```

- **socialmedia.html** with this code:

<!--  -->

```
<div id="socialmedia">
 <div id="socialbuttons">
 <a href="" class="twitter" title="Twitter"></a>

 <a href="" class="googleplus" title="Google+"></a>
 </div>
 <div id="commentSpacer" class="clearfix"></div>
 <!-- Put other Social Media items here (ie: Disqus, etc.); -->
</div>
```

The server loads these parts using the name without the extension in the **Handlebar** template for expansion. For example, anywhere the **socialmedia.html** part needs to be placed, you place the macro `{{{socialmedia}}}` there. Also, these parts can be HTML, Markdown, or any other format that the server can handle. In the servers I will be making, the supported types of files are **HTML**, **Jade/Amber**, and **Markdown**. I will get into these details in the actual server creation tutorials.

### Pages

All of the pages for the site will be placed in the **pages** directory. For now, create the following files with their content:

- **main.html** with this code:

<!--  -->

```
<h3>Flat CMS Test Site</h3>
<p>This is the Main page of the test site.</p>
```

- **flatcms.md** with this code:

<!--  -->

```
### Flat CMS Server

This is a page about the flat CMS server. 

### About

This would be a standard about page.
```

These are the demo pages for the site. The only required file is **main.html** as the server will load this page content into memory and serve from memory. That helps to speed up the loading of the front page of the website. The other two pages have the **md** extension because they are in **Markdown** format. The server will translate the content to HTML before embedding in the page layout.

In general, pages contain information that doesn't change much over time. They give the viewer information about the site, its purpose and goals.

Pages can be hierarchical in nature by creating directories and sub-directories with pages. These directory names create the addresses to these pages' directory.

### Posts

Unlike pages, posts are items that get added regularly, for example, daily/weekly/monthly blog posts and news items. This area is expected to change often. In the **posts** directory, create two new directories: **blogs** and **news**. In each of these directories, create a directory **flatcms**. These will contain the blog posts and news items for the **Flat CMS** website.

In the **posts/blogs/flatcms** directory, create the following files:

- **index.amber** with these contents:

<!--  -->

```
h3 Flat CMS Blog

p This is a rambling blog about a flat CMS.

mixin article($title, $link, $excerpt, $date)
    div.entry
        div.tutorial_wrap
            div.tutorial_div
                a[href=$link]
                    h3 #{$title}
                h5 #{$date}
                p.tutorial_excerpt #{$excerpt}
    div.clear

+article("Flat CMS Running", "/posts/blogs/flatcms/flatcms", "Flat CMS Test Server is Running!", "August 12, 2015")
```

- **flatcms.md** with these contents:

<!--  -->

```
The New Server is Running
----

After much work and programming, the flat CMS server is running. Stay tuned for more news!

{{{socialmedia}}}
```

The **index.amber** is a **[Jade](http://jade-lang.com/)** template to define the different posts in the blog. The **Jade** HTML template system makes it easy to add new content with an exact format. The parameters to the macros make up the different items. You can find more information about [Jade on Tuts+](https://webdesign.tutsplus.com/categories/jade).

Here, the extension used is **amber** because the **Go language** equivalent of **Jade** is **[Amber](https://github.com/eknkc/amber)**. Since that was where I started, that is how I made it. If you want to change the extension, then remember to change it in the appropriate server code as well.

In the **posts/news/flatcms** directory, create the following files:

- **index.amber** with these contents:

<!--  -->

```
h3 Flat CMS News

p Here is where all the news about what I am doing is found. Please come back often to check it out!

mixin article($title, $link, $excerpt, $date)
    div.entry
        div.tutorial_wrap
            div.tutorial_div
                a[href=$link]
                    h3 #{$title}
                h5 #{$date}
                p.tutorial_excerpt #{$excerpt}
    div.clear

+article("Flat CMS is Running", "/posts/news/flatcms/flatcms", "The Flat CMS Test Server is Running!", "August 12, 2015")
```

- **flatcms.md** with these contents:

<!--  -->

```
The New Server is Running
----

After much work and programming, I finally have everything moved over to a goPress server that I wrote myself. I will be making this available for you too. Stay tuned!

{{{socialmedia}}}
```

This creates the news items for the demo site. In my site, I use the same structure for tutorials and code demo pages.

### Images

All websites have pictures. In this setup, all pictures are in the **site/images** directory. For this demo site, only the social media icons will be placed here. You can get the image file from the download file.

### Site CSS

Even with the **layout css** and **styling css**, there will be times you will want special **css** rules. The **site/css** directory contains all **css** files that put those finishing touches to the website. All the **layout**, **styling**, and **site/css** files will be compiled together in to one **css** file. Therefore, create the **site/css/final** directory to place this compiled file. Now, create the **site/css/site.css** file with these contents:

```
.clear {
 clear: both;
 height: 0px;
}
```

Not much in here--just a definition for a `clear` class. You can add what you want, but it should be something that does not fall into the layout or styling category.

### Site Scripts

The **site/js** directory contains all the special JavaScript files for the site. These will be compiled together and minimized for faster site loading. For now, create the **site/js/final** directory for the compiled script and a **site/js/01-site.js** file with these contents:

```
;
//
// File: Site.js
//
// Description: The JavaScript file for the site.
//
jQuery(document).ready(function () {
    //
    // Code here will be ran once a page is loaded.
    //
});
```

There is not much here right now. But, as your site needs custom JavaScript, it can be placed here.

Since the scripts will be compiled together, I number them in the order needed for loading. The routine for compiling the scripts together will load them in numerical order.

### Automating With Gulp

The easiest way to speed up a web page load is to have as few HTTP requests as possible. Therefore, it is best to combine all CSS and JavaScript files into one file each. The best way to compile these files is with a build script. Performing it every time the server gets a request is a waste of processing time.

My choice of automation script runners is **[Gulp](http://gulpjs.com/)**. **Gulp** runs on **[Node.js](https://nodejs.org/)**. Therefore, please go to the [Node.js website](https://nodejs.org/) and download the program for your system. Once you have Node.js installed, you can install **Gulp** with the following:

```
npm install --global gulp
npm install --global gulp
npm install --global gulp-compass
npm install --global gulp-autoprefixer
npm install --global gulp-compressor
npm install --global gulp-concat
```

This will install all of the **Gulp** and **Gulp** modules that I use in the build script. Now, at the top of your directory, create the **gulpfile.js** file with these contents:

```
// Requirements
var gulp = require('gulp'),
     compass = require('gulp-compass'),
     prefix = require('gulp-autoprefixer'),
     compressor = require('gulp-compressor'),
     concat = require('gulp-concat');

// Defines path to sass
var Theme = "Basic";
var Layout = "SingleCol";
var themesassRoot = 'themes/styling/' + Theme;
var layoutsassRoot = 'themes/layouts/' + Layout;

// Gulp task
gulp.task('theme-compass-to-css', function(){
    return gulp.src(themesassRoot+'/sass/Basic.scss')
             .pipe(compass({
 config_file: themesassRoot + '/config.rb',
 css: themesassRoot + '/css',
 sass: themesassRoot + '/sass',
 require: 'sassy-buttons'
 }))
             .pipe(prefix("last 3 versions"))
             .pipe(gulp.dest(themesassRoot + '/css'))
});

gulp.task('layout-compass-to-css', function(){
    return gulp.src(layoutsassRoot+'/sass/base.scss')
             .pipe(compass({
 config_file: layoutsassRoot + '/config.rb',
 css: layoutsassRoot + '/css',
 sass: layoutsassRoot + '/sass',
 require: 'susy'
 }))
             .pipe(prefix("last 3 versions"))
             .pipe(gulp.dest(layoutsassRoot + '/css'))
});

gulp.task('watch-compass', function(){
    // What to watch
    gulp.watch(themesassRoot + '/sass/Basic.scss', function(){
        // What to run
        gulp.run('theme-compass-to-css');
    });
    gulp.watch(layoutsassRoot + '/sass/Basic.scss', function(){
        // What to run
        gulp.run('layout-compass-to-css');
    });
});

gulp.task('all-compass', ['theme-compass-to-css', 'layout-compass-to-css']);

// js compressor
gulp.task('js', function () {
 gulp.src([ layoutsassRoot +'/js/*.js', themesassRoot + '/js/*.js', 'site/js/*.js'])
 .pipe(compressor())
 .pipe(concat("final.js"))
 .pipe(gulp.dest('site/js/final'));
});

// css compressor
gulp.task('css', ['all-compass'], function () {
 gulp.src([layoutsassRoot +'/css/*.css', themesassRoot + '/css/*.css', 'site/css/*.css'])
 .pipe(compressor())
 .pipe(concat("final.css"))
 .pipe(gulp.dest('site/css/final'));
});

gulp.task('default', ['all-compass', 'js', 'css']);
```

This will compile all of the CSS and JavaScript into a single file: in the **site/css/final/final.css** file for the CSS, and in the **site/js/final/final.js** file for the JavaScript. It will also add the browser prefix rules for the CSS. Just run in the top directory the command:

```
gulp
```

**Gulp** will compile all of our files and leave them in the proper location. Each task defined in the **gulpfile.js** can be run individually by giving the task name after the `gulp` command. You can learn more about [Gulp on Tuts+](https://webdesign.tutsplus.com/categories/gulpjs).

## Server Configuration File

All servers need configuration information. Therefore, in the root of the directory, create the file **server.json** with this information:

```
{
 "CurrentLayout": "SingleCol",
 "CurrentStyling": "Basic",
 "ServerAddress": "localhost:8080",
 "SiteTitle": "Flat CMS Test Site",
 "Sitebase": "./site/",
 "TemplatBase": "./themes/",
 "Cache": false,
 "MainBase": ""
}
```

This **json** file describes information about the layout to use, the styling to use, the address and port the server should listen to, the main name for the site with a sub-title, a caching flag to turn on and off caching for debugging purposes, and the location of the different directories used for the site. By supplying this information to the server in a **json** file, the server can be flexible, with less hard-coded information.

## Uploading the Site to a Server

Most people use an FTP client to copy the files to their server. This is easy to do, but varies greatly regarding the system and programs used. But there are some alternatives.

You can setup a **[Dropbox](http://dropbox.com/)** daemon to load your site from your Dropbox account. The [Dropbox Wiki](http://www.dropboxwiki.com/tips-and-tricks/install-dropbox-in-an-entirely-text-based-linux-environment) site has detailed instructions for setting this up. The only problem is the files are automatically uploaded to the server when you make any change. Sometimes, you will want to make changes without them going to the server. If this is the case, have a non-Dropbox location of all the site's files and a Dropbox location as well. When done, create a task in your **Gulp** file to copy the new site files over to the **Dropbox** location.

![Dropzone 3 Setup for CMS Uploading](https://cms-assets.tutsplus.com/uploads%2Fusers%2F71%2Fposts%2F24547%2Fimage-1438683734928.jpg) Dropzone 3 Setup for CMS Uploading My preferred approach is to use **[Dropzone 3](https://aptonic.com/dropzone3/)**. You can program **Dropzone 3** with **Ruby** or **Python** to perform any type of action on a file that is a dropped on it. It also has many built-in functions. There is a file action built in for uploading to an FTP/SFTP site. I create one **Dropzone 3** target for each of the main directories on my site. Then, when I want to change or add a file, I simply drag it to the appropriate directory dropzone target. You can learn more about **Dropzone 3** with my tutorial [Writing Dropzone 3 Actions](https://computers.tutsplus.com/tutorials/writing-dropzone-3-actions--cms-21590).

## Conclusion

These basic files will create a website that looks like this:

![Demo Site in Browser](https://cms-assets.tutsplus.com/uploads%2Fusers%2F71%2Fposts%2F24547%2Fimage-1438683452983.jpg) Demo Site in Browser This is only the beginning! Now, with the site structure laid out, it is time to start building web servers. Please join me in the next tutorial where I will build a **[Go language](https://golang.org/)** based web server for this demo site.
