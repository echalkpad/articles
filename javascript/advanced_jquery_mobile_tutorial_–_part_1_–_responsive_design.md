# Advanced jQuery Mobile tutorial – Part 1 – Responsive design

[Original URL](http://www.gajotres.net/advanced-jquery-mobile-tutorial-part-1-responsive-design/)

> I intended to write this article one year ago just as I started writing this blog. It is meant for jQuery Mobile developers, doesn't matter how advanced your knowledge is. I will try to cover...

I intended to write this article one year ago just as I started writing this blog. It is meant for jQuery Mobile developers, doesn't matter how advanced your knowledge is. I will try to cover broader advanced topics ranging from responsive design among various devices up to the client/server communication. If there's something unclear or you want me to explain something in more detail, please leave a comment below, you can even do it just to say hi ... I won't bite ... pinky swear. **Note:** If this tutorial was helpful, need further clarification, something is not working or do you have a request for another Ionic post? Furthermore, if you don't like something about this blog, if something is bugging you, don't like how I'm doing stuff here, again leave me a comment below. I'm here to **help** you, I expect the same from **you**. Feel free to comment below, subscribe to my blog, mail me to [dragan.gaic@gmail.com](mailto:dragan.gaic@gmail.com), or follow and mention me on twitter ([@gajotres](https://twitter.com/gajotres)). Thanks and have a nice day! **PS.** If you want my help, if possible (even if it takes you some time to do that), create a working example I can play with. Use [Plunker](https://plnkr.co/) for AngularJS based questions or [jsFiddle](https://jsfiddle.net/) for jQuery/jQuery Mobile based questions.

## Table of Contents

## Intro

**Note:** This tutorial is made for jQuery Mobile version 1.4 (possibly 1.5 if jQuery Mobile developers fulfill their promise and postpone depreciation of some methods) and below. I want to cover as many jQuery Mobile users as I can, so I made a decision to use now deprecated (as of jQuery Mobile 1.4) way of page handling. **Pagecontainer** object introduced in jQuery Mobile 1.4 is still in development, plus it's missing several key features we will use in some future articles. Another reason is that I fear some users won't understand changes made to page handling. If these article series become successful, I will also write examples intended for new page handling.

Unlike some other frameworks, jQuery Mobile is intended exclusively for mobile devices. While it can be used for desktop web design, final results will vary, usually badly. Don't get me wrong, I don't want to scare you, this article will show you how to use jQuery Mobile successfully over a wide range of devices and resolutions. The First part of this article will cover CSS media queries and jQuery Mobile while another part will cover jQuery Mobile usage on larger resolutions.

## We will cover

- Creating a basic image gallery supporting responsive web design
- Passing various data between pages
- Advanced jQuery Mobile UI usage on different screen sizes

## Getting responsive

Before we can start, we need to look at something else then jQuery Mobile. I already told you we will use CSS media queries to achieve support for full range of screen resolutions. For those of you who don't know, media queries, added in CSS3, let the presentation of content be tailored to a specific range of output devices without having to change the content itself. I have not intention of going into too much detail details, for more information about CSS media queries take a look **[here](http://www.w3schools.com/css/css_mediatypes.asp)**. If you're looking for the single fastest way to create a photo gallery you can always use frameworks intended for that specific purpose. I will not use them, in this case, it would defy the purpose of this article. In case you are still interested into jQuery Mobile photo galleries take a look at **[this](http://www.gajotres.net/10-life-saving-jquery-mobile-plugins/)** article I wrote last year, you will even find working examples.

## First example

### HTML:

```
<!DOCTYPE html>
<html>
 <head>
 <title>jQM Complex Demo</title>
 <meta name="viewport" content="initial-scale=1, maximum-scale=1"/>
 <link rel="stylesheet" href="http://code.jquery.com/mobile/1.4.2/jquery.mobile-1.4.2.min.css" /> 
 <script src="http://code.jquery.com/mobile/1.4.2/jquery.mobile-1.4.2.min.js"></script> 
 </head>
 <body>
 <div data-role="page" id="index" data-theme="a">
 <div data-theme="b" data-role="header">
 <h3>
 First Example
 </h3>
 </div>

 <div data-role="content" id="content">
 <ul class="puppys">          
 <li><a><img src="http://example.gajotres.net/images/001.jpg" alt="001"/></a></li>            
 <li><a><img src="http://example.gajotres.net/images/002.jpg" alt="002"/></a></li>            
 <li><a><img src="http://example.gajotres.net/images/003.jpg" alt="003"/></a></li>            
 <li><a><img src="http://example.gajotres.net/images/004.jpg" alt="004"/></a></li>    
 <li><a><img src="http://example.gajotres.net/images/005.jpg" alt="005"/></a></li>    
 <li><a><img src="http://example.gajotres.net/images/006.jpg" alt="006"/></a></li>    
 <li><a><img src="http://example.gajotres.net/images/007.jpg" alt="007"/></a></li>    
 <li><a><img src="http://example.gajotres.net/images/008.jpg" alt="008"/></a></li>    
 <li><a><img src="http://example.gajotres.net/images/009.jpg" alt="009"/></a></li>    
 <li><a><img src="http://example.gajotres.net/images/010.jpg" alt="010"/></a></li>     
 </ul> 
 </div>
 </div>
 <div data-role="page" id="image" data-theme="a">
 <div data-theme="b" data-role="header">
 <a href="#index" data-icon="carat-l" data-transition="slide" data-direction="reverse">Back</a>
 <h3>
 First Example
 </h3>
 </div>

 <div data-role="content" id="content">
 <img src="" alt="show" class="show"/>
 </div> 
 </div> 
 </body>
</html> 
```

### Javascript:

```
$(document).on('pageinit', '#index',function(e, data){ 
 $(document).on('click', '.puppys li a', function() {
 handler.image = $(this).find('img').attr('src');
 $.mobile.changePage( "#image", { transition: "slide", changeHash: false });
 });
});

$(document).on('pagebeforeshow', '#image',function(e, data){ 
 $.mobile.activePage.find('.show').attr('src',handler.image)
});

var handler = {
 image: ''
}
```

### CSS:

```
.ui-content {
 padding: 5px !important; 
 margin: 0 !important;
}

.puppys { 
 padding: 0; 
 margin: 0; 
 list-style: none; 
}

.puppys li { 
 float: left; 
 width: 50%; 
}

.puppys li a { 
 display: block; 
 margin: 5px; 
}

.puppys li a img, .show { 
 display: block; 
 width: 100%; 
 height: auto; 
}

/* 0 - 480px _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ thesrc/ ucii/ 
@media all and (min-width: 480px){  
.puppys li { width: 33.33333333%; }     
}       

/* 481px - 768px _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ thesrc/ ucii/ 
@media only screen and (min-width: 768px) { 
.puppys li { width: 20%; } 
} 

/* 769px - 1024px _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ thesrc/ ucii/ 
@media only screen and (min-width: 1024px) { 
.puppys li { width: 16.66666666%; }     
} 

/* 1025px - 1280px _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ thesrc/ ucii/ 
@media only screen and (min-width: 1280px) { 
.puppys li { width: 14.285714%; } 
} 

/* 1281px - 1440 _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ thesrc/ ucii/ 
@media only screen and (min-width: 1440px) { 
.puppys li { width: 12.5%; } 
} 

/* 1441px - 1600px _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ thesrc/ ucii/ 
@media only screen and (min-width: 1600px) { 
.puppys li { width: 11.111111%; } 
} 

/* 1601px - 1920px _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ thesrc/ ucii/ 
@media only screen and (min-width: 1920px) { 
.puppys li { width: 10%; } 
} 
```

Working jsFiddle example can be found here: <http://jsfiddle.net/Gajotres/Y8uat/> If you want to test this example in its full glory open mentioned jsFiddle link, resize it and watch what's happening. As you can see from this example responsive design is made purely combining HTML with CSS, JavaScript was used only to show a possibility of using jQuery Mobile as a photo gallery. Using media queries we have covered resolutions ranging from 1px to infinity. If you take a careful look, original **<li>** state is 2 images per column (50% width) and that number is getting smaller and smaller as screen width expands. This example shows us how it is of utmost importance to think about page size/layout before we even start doing any coding. Now take a look at this code snippet taken from JavaScript example: var handler = { image: '' }

In this example, I have created a JavaScript object which is used to store data we need persisted during page transitions/switching. There are several other ways of data storing/persisting when working with jQuery Mobile (find more about them **[here](http://www.gajotres.net/passing-data-between-jquery-mobile-pages/)**) and this one is probably best suited for this kind of example. Think about it, we only need it once and we should not care if it is lost afterwards. The rest of a code is pretty much self-explanatory. If there's something unclear leave me a comment below.

## The second example

Hopefully, before reading this article you had some experience with jQuery Mobile. If this is the case then you know how bad jQuery Mobile looks like on desktop browsers or any other device with large enough screen, even worse if you are surfing on 50 inch screen (I so that and I'm still having nightmares). This is because jQuery Mobile was never intended for such large screens, and I am deliberately using word screen instead of resolution, if you thing about it, as of last year, 1920×1080 resolution become standard on handheld devices. This doesn't mean jQuery Mobiles should not be used, as it is, on larger screens, you only need to be especially careful when planning your mobile application UI. You can make something like **[this](http://songmeanings.com/m)**, and mark my word, that site is a pearl in a sea of mediocrity, or you can do something like this:

### HTML:

```
<!DOCTYPE html>
<html>
 <head>
 <title>jQM Complex Demo</title>
 <meta name="viewport" content="initial-scale=1, maximum-scale=1"/>
 <link rel="stylesheet" href="http://code.jquery.com/mobile/1.4.2/jquery.mobile-1.4.2.min.css" /> 
 <script src="http://code.jquery.com/mobile/1.4.2/jquery.mobile-1.4.2.min.js"></script> 
 </head>
 <body>
 <div data-role="page" id="index" data-theme="a">
 <div data-theme="b" data-role="header">
 <h3>
 Second Example
 </h3>
 </div>

 <div data-role="content" id="content">
 <div class="inner-content">
 <div class="image-container">
 <img src="http://example.gajotres.net/images/lamborghini-test.jpg"/>
 </div>
 <div class="controls-container">
 <label for="slider-1">Change image width:</label>
 <input type="range" name="slider-width" id="slider-width" value="50" min="0" max="100"/>
 <label for="slider-1">Change image height:</label>
 <input type="range" name="slider-height" id="slider-height" value="50" min="0" max="100"/>
 </div> 
 </div>
 </div>
 </div> 
 </body>
</html> 
```

### Javascript:

```
$(document).on('pageinit', '#index', function(){ 
 $(document).on( "slidestop", "#slider-width" ,function( event, ui ) {
 $('.image-container img').css({'width':$(this).val() + '%'});
 }); 
 $(document).on( "slidestop", "#slider-height" ,function( event, ui ) {
 $('.image-container img').css({'height':$(this).val() + '%'});
 }); 
});
```

### CSS:

```
.ui-content {
 margin: 0 !important;
 padding: 10px !important;
 position : absolute !important; 
 top : 40px !important; 
 right : 0; 
 bottom : 0 !important; 
 left : 0 !important;
}

.inner-content {
 width: 100%;
 height: 100%;
}

.image-container, .controls-container {
 width: 50%;
 height: 100%;
 float: left;
}

.image-container img {
 width: 50%;
 height: 50%;
}

/* 0 - 480px _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ thesrc/ ucii/ 
@media only screen and (max-width: 768px) { 

 .image-container, .controls-container {
 width: 100%;
 height: 50%;
 }
}               
```

Working jsFiddle example can be found here: <http://jsfiddle.net/Gajotres/aD972/> If you want to properly test this example go to mentioned jsFiddle page and resize result box. My intention was to show you that you are not bound with classic jQuery Mobile responsive design. You can create any layout you want (as long as it is inside a **data-role="page"** container) and fill it with as many widgets. Sky is the limit. Notice that CSS media queries are used again, sliders will be shown below the image if a screen width is too low or right of it if a screen width is wide enough. Of course, this is a simple example. There wasn't any point in creating a more complex application, this one is simple enough for you to understand and build upon. If you still need something more complex then take a look a this **[link](http://example.gajotres.net/iscrollview/)**. This example uses two side by side containers (similar to previous example) where each pane contains a listview. What makes this example great is that each listview is bigger than the available space and scrollable separate from other one. This is done using plugin called **iScrollView**. I will not post mentioned example as you can always get a source code directly.

## Who Am I?

Between working as a **senior Java developer** in one of the largest insurance companies in the world and traveling, in my free time, I work as a **professional mobile development adviser**. I'm also a major jQuery Mobile **supporter** back at **[StackOverflow](http://stackoverflow.com/users/1848600/gajotres)** and a **forum moderator** at the official Ionic Framework forum.

## Blogs worth reading

If you're here looking for information related to the Ionic Framework, you will also like these blogs: [![](http://www.gajotres.net/wp-content/uploads/2015/07/xresources.jpg.pagespeed.ic.woZ4KsQQwt.jpg)]()

[![](http://www.gajotres.net/wp-content/uploads/2015/08/xnikola-breznjak-blog.jpg.pagespeed.ic.2pzaqL3Wz2.jpg)]()
