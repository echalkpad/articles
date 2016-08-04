# PostCSS Deep Dive: What You Need to Know

[Original URL](http://webdesign.tutsplus.com/tutorials/postcss-deep-dive-what-you-need-to-know--cms-24535)

> If there's one thing you really need to know about PostCSS, it's that you should learn what it is and how to use it ASAP.In this series, we're going to take a deep dive into...

If there's one thing you really need to know about PostCSS, it's that _you should learn what it is and how to use it_ _ASAP_.

In this series, we're going to take a deep dive into PostCSS and take you through all the major ways you can use it. If you haven't already had your mind blown by what PostCSS is capable of, get ready for a brave new CSS world.

## Intro to PostCSS

PostCSS has been growing in popularity at breakneck speed. More and more people are beginning to understand what it offers, enjoying that "lightbulb moment" when they realize how they can use it uniquely in their own projects.

![](https://cms-assets.tutsplus.com/uploads/users/53/posts/24535/image/postcssdownloads.png) In 2014 there were a little under 1.4 million downloads in total for the year, but from January to June in 2015 there had already been [over 3.8 million downloads](http://npm-stat.com/charts.html?package=postcss).

[Autoprefixer](https://github.com/postcss/autoprefixer), a highly popular PostCSS plugin, is used by Google, Shopify, Twitter, Bootstrap and CodePen. WordPress also uses Autoprefixer, as well as the [RTLCSS](https://github.com/MohammadYounes/rtlcss) plugin. And [Alibaba](https://github.com/yisibl) uses several PostCSS plugins, as well as developing their own and contributing to other plugin projects.

Beyond that, Mark Otto has talked about Bootstrap version 5 being written in PostCSS:

> Oh, btw--Bootstrap 4 will be in SCSS. And if you care, v5 will likely be in PostCSS because holy crap that sounds cool.

> -- Mark Otto (@mdo) [April 23, 2015](https://twitter.com/mdo/status/591364406816079873)

PostCSS has just been [integrated into CodePen.io](https://blog.codepen.io/2015/07/14/postcss-now-supported-on-codepen/) and can be used inline by selecting it within the CSS settings:

![](https://cms-assets.tutsplus.com/uploads/users/53/posts/24535/image/postcssoncodepen.png) PostCSS on CodePen PostCSS is growing, it's growing fast, and for good reason.

## PostCSS in a Nutshell

So what is PostCSS? The best definition comes from the project's own GitHub page:

> "PostCSS is a tool for transforming CSS with JS plugins. These plugins can support variables and mixins, transpile future CSS syntax, inline images, and more.

In a nutshell, PostCSS takes CSS and turns it into a form of data that JavaScript can manipulate. JavaScript-based plugins for PostCSS then perform said code manipulations. PostCSS itself doesn't change your CSS, it just paves the way for plugins to perform whatever transformations they've been designed to make.

There are essentially no limitations on the kind of manipulation PostCSS plugins can apply to CSS. If you can think of it, you can probably write a PostCSS plugin to make it happen.

PostCSS plugins can behave like preprocessors; they can optimize and autoprefix code, they can add future syntax, they can perform linting, they can process variables and logic, they can provide complete grid systems, they can offer coding shortcuts... the list is long and varied.

## What PostCSS is _Not_

The fact you can do just about anything you want with PostCSS plugins, combined with the fact PostCSS is still relatively new, has lead to some misconceptions about what the tool actually is.

Many people, (myself included, initially), have had an incomplete impression of what PostCSS actually is, and have therefore either missed out on what PostCSS has to offer, or arrived late to the table.

So before we go any further, let's clear up a few of the things PostCSS is _not_.

### PostCSS is Not a _Pre-_processor

Numerous developers have said they're abandoning CSS preprocessors in favor of PostCSS. Meanwhile others state that because they prefer their current preprocessor they don't like PostCSS. However PostCSS is _not_ a preprocessor.

Yes, you can absolutely use it as a preprocessor if you wish, but you can also use PostCSS with absolutely no preprocessor-like functionality. You can even keep using your favorite preprocessor in _conjunction_ with PostCSS.

### PostCSS is Not a _Post-_processor

PostCSS has the word "post" in the name, but it's not a really "post-processor" either. Post processing is typically seen as taking a finished stylesheet comprising valid/standard CSS syntax and processing it, to do things like adding vendor prefixes. However PostCSS is not restricted to operating solely in this way. As mentioned above, it can behave like a pre-processor too.

It's perhaps best to just think of PostCSS as a "processor". As Andrey Sitnik, PostCSS's creator, said on Twitter:

> It is time admit my mistakes. "Postprocessor" term was bad. PostCSS team stoped to use it. <https://t.co/vs2AiXGoJy>

> -- PostCSS (@PostCSS) [July 28, 2015](https://twitter.com/PostCSS/status/626046993006239744)

And as prolific PostCSS contributor and plugin developer Maxime Thirouin described on Twitter, rather than the "post" in PostCSS referring to "post"-processing, it can better be thought of as meaning "CSS and beyond".

> [@HugoGiraudel](https://twitter.com/HugoGiraudel) nobody in postcss contributors is using this expression anymore. Now it is Postcss like "css and beyond"

> -- Maxime Thirouin (@MoOx) [July 21, 2015](https://twitter.com/MoOx/status/623466638680178688)

### PostCSS is Not "Future Syntax"

There are some excellent and very well known PostCSS plugins which allow you to write in future syntax, i.e. using CSS that will be available in the future but is not yet widely supported. However PostCSS is not inherently about supporting future syntax.

Some developers have expressed reluctance to use PostCSS, saying it's because they're not sure they're comfortable with writing future syntax. However, writing future syntax is just one of many ways you can utilize PostCSS.

If you so choose, you can use PostCSS to completely revolutionize your development processes without a line of future syntax in sight.

### PostCSS is Not a Clean Up / Optimization Tool

The success of the Autoprefixer plugin has lead to the common perception of PostCSS as something you run on your completed CSS to clean it up and optimize it for speed and cross browser compatibility. This is the perception I had myself, until I learned of the huge array of other things you can achieve with PostCSS.

Yes, there are many fantastic plugins that offer great clean up and optimization processes, but these are just a fraction of what's on offer.

### PostCSS is Not Any _One_ Thing

The most compelling thing about PostCSS is it is not restricted to any one type of functionality; it represents a completely customizable and configurable set of functionality that is potentially unlimited.

Consider WordPress plugins as a parallel. Ecommerce plugins are very popular, but nobody considers WordPress itself as an ecommerce engine, and the merits of WordPress are not assessed based on its ecommerce plugins.

In the case of PostCSS, I like to think of it as being like the bread you use to make a sandwich. By itself it doesn't seem like much, but that's exactly how it's meant to be, and this apparent "emptiness" is why it has so much potential. It's the capacity for an infinite variety of fillings that gives you something amazing.

![A sandwich yesterday](https://cms-assets.tutsplus.com/uploads/users/30/posts/24535/image/sandwich.jpg) A sandwich, yesterday Tried a peanut butter sandwich and didn't like it? That's absolutely no reason to swear off bread and all types of sandwiches forever. Instead, move on to trying the next filling and you might just discover something that becomes an awesome new part of your daily life.

## What Makes PostCSS Special

PostCSS is an entirely different approach to CSS. A frontend contractor from London I spoke to described it as "a Swiss army knife for CSS production" and that's an entirely apt description.

Let's take a look at a few of the things that make PostCSS so special.

### Its Plugin Ecosystem Offers Diverse Functionality

As amazing as PostCSS itself is, it's the long list of diverse plugins that really make it shine. When you read the list of available plugins on the [PostCSS GitHub page](https://github.com/postcss/postcss) you'll find a variety of functionality that has never been contained in one space before.

There are plugins for future syntax, allowing you to use things like color functions, conic gradients, custom properties, custom selectors, custom aliases for media queries and plenty more.

There are fallback plugins to create legacy syntax, such as adding hexadecimal fallbacks for `rgba()` colors, adding opacity filters for IE8, converting psuedo element selectors for IE8, and generating `px` fallbacks for `rem` units.

Over twenty language extension plugins are available, including adding mixins, variables, conditionals, for and each loops, BEM and SUIT style class generation and several more.

A selection of color management plugins is available, allowing for conversion from one color format to another, modifying alpha levels, combining colors, generating colorblind friendly color schemes, to name a few.

There are grid systems, optimization tools, plugins that add shortcuts and shorthand, linters and other analysis & reporting plugins.

It's not possible to fully convey the constantly expanding diversity of the current plugin selection in a few paragraphs, so be sure to [check out the list](https://github.com/postcss/postcss#packs) for yourself.

### It's Modular; Use Only What You Need

The flip side to the incredible list of plugins available for PostCSS, is that you can use as many or as few of them as you choose.

Just want to use PostCSS to make your CSS more efficient and cross browser friendly? Load up a few optimization plugins and you're away.

Want to use PostCSS solely as a preprocessor? Use some language extension plugins instead and you're all set.

The underlying philosophy of PostCSS is fine grain modularity, whereby there are no hulking plugins that handle multiple functions. Instead, a plugin is created per function, and from there they can be assembled into packs of plugins with commonly themed functionality.

For example, you can hand pick a selection of plugins for language extensions and create your own custom preprocessor. Or alternatively, you can just load up the [PreCSS](https://github.com/jonathantneal/precss) pack which will give you automatic access to several language extension plugins at once.

Whichever way you want to use PostCSS, you're able to run just the plugins you need for your specific purposes, meaning you don't have to drag along any unused functionality as dead weight.

### It's Fast: Up to 3 Times Faster

There are two main reasons PostCSS tests very fast in benchmarks. One is the fact that you only have to load the plugins you need, as described above. The other is that it runs on JavaScript.

You can run these benchmarks for yourself using <https://github.com/postcss/benchmark>

The result of one of these benchmarks, testing parsings, nested rules, mixins, variables and math, was:

```
PostCSS: 36 ms
Rework: 77 ms (2.1 times slower)
libsass: 136 ms (3.8 times slower)
Less: 160 ms (4.4 times slower)
Stylus: 167 ms (4.6 times slower)
Stylecow: 208 ms (5.7 times slower)
Ruby Sass: 1084 ms (30.1 times slower)
```

### You Can Create Your Own Plugins for Anything You Want

Plugins for PostCSS are written in JavaScript, and as such anyone who can write JavaScript can create a plugin for any purpose they want. To give you an idea, I don't consider myself a hard core JavaScript developer by any means, but after discovering PostCSS I was able to make my first fully functional plugin within the space of a few hours.

Preprocessor projects like Stylus, Sass and LESS do a wonderful job, but they can't be everything to everyone. They have to decide on which features will best serve their user base as a whole. If there is functionality you'd like to have, you can make a feature request but it may or may not be deemed to the align with the wider needs of the project.

Even if you do make a feature request that is deemed suitable, the projects' maintainers are typically unpaid volunteers who may not have the time to develop it. So if you don't have the skill level to create it said feature yourself, you're out of luck.

With PostCSS on the other hand, you don't have to ask anyone. If you want a new feature, you can go ahead and make it. From my experience, the JavaScript experience level required to create a PostCSS plugin would be manageable for many front end developers.

In a later tutorial in this series we'll step through creation of a basic PostCSS plugin. Even if you don't consider yourself a JavaScript expert, I think you'll find PostCSS plugin creation to be quite achievable.

### You Can Use it With Regular CSS

A very large portion of PostCSS plugins don't require use of custom syntax, as is typical with preprocessors. Rather, they can be applied to regular CSS. This means that you can use PostCSS with any CSS file you find yourself in possession of, such as completed stylesheets from a front end framework, from someone else's project, or a Normalize.css file for example.

It also means you're not locked out of projects that use a particular preprocessor, be it Stylus, Sass or LESS, because you can always apply PostCSS to the compiled CSS. For example, if using Foundation via Sass, you could run optimization and future syntax plugins after you generate your project's CSS files.

### PostCSS Libraries Aren't Tied to One Preprocessor

We're also starting to see entire libraries built with PostCSS, where in the past they might have been written in Stylus, Sass or LESS.

For example, [Cory Simmons](http://tutsplus.com/authors/cory-simmons) was originally maintaining both Stylus and Sass versions of his [Lost grid system](https://github.com/corysimmons/lost) so users of both preprocessors could gain access. He subsequently ported the project to PostCSS, meaning that now everyone can use Lost, including Stylus and Sass users, but also LESS users and people who don't work with a preprocessor at all.

### It Deploys Seamlessly with Popular Tools

Being JavaScript based, PostCSS doesn't require that you have Ruby installed, and it has ready made integrations for several development tools.

- There are plugins for [Grunt](https://github.com/nDmitry/grunt-postcss), [Gulp](https://github.com/postcss/gulp-postcss), [webpack](https://github.com/postcss/postcss-loader), [Broccoli](https://github.com/jeffjewiss/broccoli-postcss), [Brunch](https://github.com/iamvdo/postcss-brunch) and [ENB](https://github.com/theprotein/enb-postcss).
- [CodePen](https://codepen.io) allows you to use PostCSS inline.
- [Prepros](https://prepros.io/) has in built support for the [Autoprefixer](https://github.com/postcss/autoprefixer) and [cssnext](http://cssnext.io/) plugins.
- The [postcss-use](https://github.com/postcss/postcss-use) plugin allows you to easily load other plugins with simple `@` rules in your CSS
- You can use a "package.json" file so [npm](https://www.npmjs.com/) can automatically install whatever PostCSS plugins a project employs via a two word command: `npm install`. This facilitates easy sharing of PostCSS projects, despite the huge number of possible variations in plugin setup.

We'll go through how you can get setup to use PostCSS in the upcoming "Quick Start Guide" section of this series.

## Okay, Let's Recap

The first thing you need to know is the PostCSS is picking up steam fast, and for good reason, so now is the time to get onto building a clear understanding of how it can help your development processes.

### In this intro we covered what PostCSS is NOT:

- It's not a pre-processor, though it can optionally behave like one.
- It's not a post-processor, though it can optionally behave like one.
- It's not about "future syntax", though it can facilitate support for future syntax
- It's not a clean up / optimization tool, though it can provide such functionality.
- It's not any one thing; it's a means to potentially unlimited functionality configured as you choose.

### We also covered what makes PostCSS special:

- The diverse functionality available via its plugin ecosystem
- Its modular, "use what you need" nature
- Its rapid compilation time
- The accessibility of creating your own plugins
- The option to use it with regular CSS
- The ability to create libraries that don't depend on one preprocessor
- Its seamless deployment with many popular build tools

## Coming Up in "PostCSS Deep Dive"

In this series we're going to kick off by stepping you through how to get started with PostCSS via the tutorials:

- Quickstart Guide - Instant Setup Options
- Quickstart Guide - Gulp Setup
- Quickstart Guide - Grunt Setup
- Quickstart Guide - Exploring Plugins

From there we'll take a close look at several of the varied ways you can use PostCSS in the tutorials:

- For Cross Browser Compatibilty
- For Minification and Optimization
- Preprocessing with PreCSS
- Roll Your Own Preprocessor
- In conjunction with Stylus / Sass / LESS
- BEM / SUIT Method CSS
- Shortcuts and shorthand
- Miscellaneous PostCSS goodies

Finally, we'll wrap up by taking you through _creating your own basic PostCSS plugin_.

You may notice a conspicuous absence of a tutorial on using PostCSS to enable future CSS, especially given many people see the two as almost synonymous.

The reason for this temporary absence is the leading plugin for future CSS, [cssnext](http://cssnext.io/), is looking like it's about to undergo a [major transformation](https://github.com/postcss/postcss/issues/477) that might significantly change the steps required to use it. As such, we'll keep an eye on it and bring you a fresh tutorial once the project has made its transition.

So let's begin! In the next tutorial we're going to get straight into our "Quickstart Guide" and show you the fastest ways to get started using PostCSS. I'll see you there!
