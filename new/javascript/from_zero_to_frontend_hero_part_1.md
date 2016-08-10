# From Zero to Front-end Hero (Part 1) -- Free Code Camp

[Original URL](https://medium.freecodecamp.com/from-zero-to-front-end-hero-part-1-7d4f7f0bff02)

> So far what you've been learning are the basics of HTML and CSS. The next step is to learn best practices. Best practices are a set of informal rules that improve the quality of your code.One...

So far what you've been learning are the basics of HTML and CSS. The next step is to learn best practices. Best practices are a set of informal rules that improve the quality of your code.

One of the best practices for HTML and CSS is to write semantic markup. Good web semantics means using appropriate HTML tags and meaningful CSS class names to convey structural meaning.

For example, the _h1_ tag tells us that the text it wraps is an important heading. Another example would be the _footer_ tag, which tells us that the element belongs at the bottom of the page. For further reading, read [A Look Into Proper HTML5 Semantics](http://www.hongkiat.com/blog/html-5-semantics/) and [What Makes For a Semantic Class Name](https://css-tricks.com/semantic-class-names/) by CSSTricks.

The next important best practice for CSS is proper naming conventions. Good naming conventions, like semantic markup, convey meaning and help make our code predictable, readable, and maintainable. You can read about the different naming conventions in the article [OOCSS, ACSS, BEM, SMACSS: what are they? What should I use?](http://clubmate.fi/oocss-acss-bem-smacss-what-are-they-what-should-i-use/)

In general, I suggest trying out simple naming conventions that make intuitive sense to you. Over time, you'll discover ones that work best for you. To see how companies like Medium utilize naming conventions like BEM, read [Medium's CSS is actually pretty f_*_ing good](https://medium.com/@fat/mediums-css-is-actually-pretty-fucking-good-b8e2a6c78b06#.ef81j61eg). In that article, you'll also learn that coming up with an effective set of CSS conventions is an iterative process.

Browsers have small styling inconsistencies from margins to line-heights. For this reason, always reset your CSS. [MeyerWeb](http://meyerweb.com/eric/tools/css/reset/index.html) is a popular reset. If you want to dig deeper, you can read [Create Your Own Simple Reset.css File](http://code.tutsplus.com/tutorials/weekend-quick-tip-create-your-own-resetcss-file--net-206).

Cross browser support means that your code supports most up-to-date browsers. Some CSS properties like _transition_ need [vendor prefixes](https://developer.mozilla.org/en-US/docs/Glossary/Vendor_Prefix) to work properly in across different browsers. You can read more about vendor prefixes in this article, [CSS Vendor Prefixes](http://webdesign.about.com/od/css/a/css-vendor-prefixes.htm). The main take away is that you should test your website across multiple browsers including Chrome, Firefox, and Safari.

![](https://cdn-images-1.medium.com/max/800/1*pCAitbJZl5eai2oNdzIphA.png)

Since the introduction of CSS in 1990s, CSS has come a long way. Since UI systems have become increasingly complex, people came up with tools known as preprocessors and postprocessors to manage complexity.

CSS preprocessors are CSS language extensions that add bells and whistles like variables, mixins, and inheritance. The two main CSS preprocessors are [Sass](http://sass-lang.com/guide) and [Less](http://lesscss.org/). In 2016, Sass is generally more widely used. Bootstrap, the popular responsive CSS framework, is switching from Less to Sass as well. Also, when most people talk about Sass, they're actually talking about [SCSS](https://www.sitepoint.com/whats-difference-sass-scss/).

![](https://cdn-images-1.medium.com/max/800/1*7Px9Kzaw8-eLCf2D41yauQ.png)

CSS postprocessors apply changes to CSS after it has been either hand written or compiled by a preprocessor. For example, some postprocessors like [PostCSS](https://github.com/postcss/postcss) have plugins that add browser vendor prefixes automatically.

When you first discover CSS preprocessors and postprocessors, it's tempting to use them everywhere. However, start off simple and add extensions like variables and mixins only when necessary. The article I suggested earlier, [Medium's CSS is actually pretty f_*_ing good](https://medium.com/@fat/mediums-css-is-actually-pretty-fucking-good-b8e2a6c78b06#.ef81j61eg), also covers how much is too much when it comes to preprocessors.

Grid systems are CSS structures that let you stack elements horizontally and vertically.

![](https://cdn-images-1.medium.com/max/800/1*SqbRKZTnd78gsQEOPPAt1g.png)

Grid frameworks like [Bootstrap](http://getbootstrap.com/), [Skeleton](http://getskeleton.com/), and [Foundation](http://foundation.zurb.com/) provide stylesheets that manage rows and columns in layouts. While grid frameworks are useful, it's also important to understand how grids work. [Understanding CSS Grid Systems](http://www.sitepoint.com/understanding-css-grid-systems/) and [Don't Overthink Grids](https://css-tricks.com/dont-overthink-it-grids/) are great overviews.

One of the main purposes of grid systems is to add _responsiveness_ to your website. Responsiveness means that your website resizes based on window width. A lot of times responsiveness is achieved by using [CSS media queries](http://www.w3schools.com/css/css_rwd_mediaqueries.asp), CSS rules that only only apply to certain screen widths.

![](https://cdn-images-1.medium.com/max/800/1*EERzyzZhHJ5FWXKi2PNxuA.gif)

You can read more about media queries in [Intro to Media Queries](https://varvy.com/mobile/media-queries.html). Also, because we have entered a [mobile-first](http://zurb.com/word/mobile-first) era, check out [An Introduction to Mobile-First Media Queries](http://www.sitepoint.com/introduction-mobile-first-media-queries/).
