# Building & Maintaining OUI (Optimizely's UI Library): Part 1/2

[Original URL](https://css-tricks.com/optimizelys-ui-library-oui-1-of-2/)

> The following is a guest post by Tom Genoni. Tom is going to introduce us to the thinking and process behind Optimizely's new UI library / Sass framework. Part 2, by Daniel O'Connor, looks at some of...

_The following is a guest post by [Tom Genoni](http://atomeye.com/). Tom is going to introduce us to the thinking and process behind [Optimizely's](https://www.optimizely.com/) new UI library / Sass framework. [Part 2](https://css-tricks.com/optimizelys-ui-library-oui-2-of-2), by [Daniel O'Connor](https://danoc.me/), looks at some of the technical and integration bits._

When I first started working on web projects, stylesheets were seen as a necessary evil. It was neither a _real_ language to be taken seriously by a computer-science minded engineer nor simple enough for a designer to fully own and understand. With few best practices, organization of the CSS was always ad hoc--"type styles in this section, colors in that section"--and every company did it differently. But as web applications, and the teams building them, grew larger and more complex it became harder to manage ballooning codebases while maintaining consistency across teams and projects.

Among the first popular CSS frameworks that emerged to address this problem is [Bootstrap](http://getbootstrap.com/). Many similar frameworks have followed but the purpose has always been the same: instead of writing CSS from scratch on a project by project basis, start with a styled set of the most common components--grids, buttons, form elements, breadcrumbs--that are cross-browser compatible and easily combined into larger interfaces.

At Optimizely we wrote and actively maintain our own Sass framework called [OUI](https://github.com/optimizely/oui) (pronounced like the French word for "yes") based on the work of innumerable members of the web community including Mark Otto, Jonathan Snooks, Nicole Sullivan, and Harry Roberts and the philosophies of scalable, object-oriented CSS and HTML espoused by [BEM](https://css-tricks.com/bem-101/) and [SMACSS](https://smacss.com/).

![](https://cdn.css-tricks.com/wp-content/uploads/2016/02/open-eyes.svg)<br>
Figure 1: Meet Louis, the official mascot of OUI.

## The ongoing goals for OUI are to provide code that is...

- **Abstracted.** Component names shouldn't be derived from the content they contain. Class names should convey structural meaning.
- **Reusable.** Components should be generic enough to be reused throughout the site. They should make no assumptions what page/view they will be used on. Problems solved in one area should be easily applied elsewhere.
- **Mixable.** Components should be able to join together to create larger blocks.
- **Powered by variables.** All common design elements--colors, fonts, spacings, shadows--should be defined using the pre-existing variables.
- **Scalable.** Reusing patterns means new elements can be created faster and with minimal additional CSS.
- **Consistent.** Developers will be better able to read each other's code and will contribute to more reliable end-user experiences.
- **Small and [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself).** Since we're reusing low-level components to build larger objects we cut down on CSS bloat. Less code means fewer bugs.

In this post I'll discuss the practical steps and the partnerships with engineers and designers it took to build it along with some problems we encountered along the way and, in Part II, my colleague Daniel O'Connor and fellow [UI Engineer](https://medium.com/design-optimizely/why-we-hire-ui-engineers-on-optimizely-s-design-team-b2a789553b79#.mxps7rnrz) will describe the technical details of how we test, version, and integrate it into projects.

## Phase 1: Get Your Designers On Board

Arguably the most important phase of creating a UI library is establishing close collaboration with your design team. Designing with a framework often requires a workflow shift away from pixel-perfect mocks and can lead to what might seem like a loss of design freedom. Fortunately there's a way to address this: audit your site and present your findings.

Devote a day or two to taking screenshots of your site. Scour every nook and cranny for all instances of buttons, forms, tables, font sizes, colors, tags, icons, etc., and group them together. What you'll likely surface will leave your designers aghast: buttons of all shapes and sizes, headings with little regularity, 27 shades of blue, and many other examples of good intentions gone bad. Putting your designers in charge of consolidating these inconsistencies will make them partners in the framework's construction and allies in rallying others around the effort.

## Phase 2: Own the Front end

Decide with your engineering team who "owns" the visual front end. Because your framework's CSS and HTML are tied together we've found it's important to have a smaller group, familiar with the framework's patterns, responsible for delivering code to often thankful engineers who no longer have to wrestle with uncooperative z-indexes.

At Optimizely it's our [UI Engineers](https://medium.com/design-optimizely/why-we-hire-ui-engineers-on-optimizely-s-design-team-b2a789553b79#.h3opdbld1), who are officially [part of the design team](https://medium.com/design-optimizely/the-structure-of-optimizely-s-design-team-97e5eb3ed712#.dwaxqmqrf), that fill this role. With this tighter control we've dramatically cut the new CSS we have to write, our code is cleaner and more consistent, and we experience far fewer bugs. Over time the responsibility for writing HTML and CSS can widen as engineers understand how to use the library effectively and as the framework matures.

## Phase 3: Identify Your Components

Bootstrap contains just about everything you'd need to build most sites. But maybe you don't want breadcrumbs or perhaps you can use newer CSS properties because of your browser support. Either way we recommend referencing existing frameworks and creating a list of only the components required to cover your use cases. By building it yourself you'll more easily identify potential trouble spots and will learn a ton in the process.

OUI was built with only the components and variables we decided were universal. Each Optimizely project that uses OUI adds any custom CSS code on top of it, only in that project's repository. This allows each project to meet its unique design needs while keeping OUI lean and untouched. If different projects introduce similar components we have the option to "graduate" them into OUI, though this has rarely happened. In Part II we'll describe our integration and versioning system in more detail.

## Phase 4: Organize & Build

CSS preprocessors have been a boon for organizing code by supporting partial files containing only what's needed for a given component. But it's still a challenge to decide how to name things and where to put them. With OUI we currently use the following structure:

**_oui-partials.scss:** A rollup of all the partials to be included. **_oui-variables.scss:** Variables for virtually everything. This includes a custom function to retrieve values from nested variable objects. **oui.scss:** A root Sass file that includes all the rollups (used just for testing and not referenced by projects that use OUI). **library:** Third party libraries (we will likely remove this) **partials:** A directory of all the partial files.

- _elements:_ Mixins and functions. Since we're using node-sass for compilation we also include the mixins we need for animations, tints, and prefixes that would ordinarily come from Compass.
- _base:_ Resets and minimal HTML element styling (links, tables, lists)
- _components:_ Low-level bits that help form objects (grid, media, nav)
- _objects:_ The fully formed and styled pieces (buttons, spinner, dropdown)
- _trumps:_ Helper classes for layout (margins, paddings, font styling)

In the Sass we don't use IDs, selector depth is kept to a [minimum](http://thesassway.com/intermediate/avoid-nested-selectors-for-more-modular-css), and the source order helps us keep [specificity low](https://css-tricks.com/strategies-keeping-css-specificity-low/). You can take a closer look at the [OUI repository](https://github.com/optimizely/oui).

## Phase 5: Document & Evangelize

All this work won't help streamline your code if nobody knows about it or how to use it. Here are a few suggestions that have helped OUI gain traction at Optimizely.

1. **Pick a code name.** Like any good engineering project, your framework should have an internal code name. It's probably not something you usually do with CSS projects but, hey, this one is important! If it's short enough you can use it as a prefix for namespacing classes.
2. **Provide good documentation.** As anyone who has ever tried it knows, writing and more importantly maintaining good documentation with code samples, usage guidelines, and a changelog, is not easy. Incomplete or out-of-date documentation will only frustrate your developers and can undermine trust in the project. To combat this, nominate owners and have clear expectations about what to document and how to keep it accessible and current.

  This is an on-going challenge for us, but we're inching closer. Unhappy with existing solutions, Daniel O'Connor has been leading the effort to build a robust living style guide script, called ScribeSass, that parses comments and code in Sass source files and renders code samples and examples. We'll be releasing that as an open source project soon.

3. **Evangelize.** With your pithy code name and documentation in place, start spreading the word. Give tech talks using real examples that demonstrate the speed and consistency benefits of reusing existing styles and patterns. If you have the budget for it, make stickers, t-shirts, or posters. Each month collect statistics on the size, rules, and specificity of your CSS and the number of bugs and time your team spent fixing them and share the results. We saw drops across the board.

## Lessons Learned

Like any web project we made lots of mistakes, fixes and additions along the way. Here are a few of the challenges we faced, adjustments we made, and issues we continue to confront.

- **Namespacing.** OUI worked well for new projects but as we started refactoring existing projects we realized we could have class-name collisions. To avoid this we added the option of a namespaced Sass variable that will add a prefix to most classes in the final CSS output. For example, if "oui-" is the namespace value, the class ".media" becomes ".oui-media". This gave us the flexibility to more freely mix OUI with legacy code.
- **Versioning.** For a while we weren't convinced the overhead of introducing formal versioning was worth the effort. There were only a few projects using OUI and we didn't want to add too much friction to making changes. But pushing fixes--especially ones that might cause breaks--and communicating their impact became problematic. We decided to adopt [Semantic Versioning](http://semver.org/) and with the help of a well-maintained [changelog](https://github.com/optimizely/oui/blob/devel/CHANGELOG.md) and a few Gulp packages we are able to add features and fix bugs without fear of breaking projects using OUI. In Part II, Daniel O'Connor will discuss setting up versioning in more detail.
- **Sass mixins vs extend.** Initially OUI was setup using a fair number of extends. We were aware of their [pitfalls](http://www.sitepoint.com/avoid-sass-extend/) and introduced them carefully, always with placeholders. But using extends can move CSS classes around in unwanted ways and despite our best efforts we did eventually have a few issues with source order and specificity. We decided to convert many of the extends to mixins and although it's slightly more code we're less concerned about future gotchas.
- **Responsiveness.** This one is tricky. We've included media query mixins and optional breakpoints to a handful of our patterns, like grids. But since so much of responsive design is unavoidably complex, often requiring a good amount of custom code, there isn't much more OUI can provide beyond the basic building blocks. Despite this inherent challenge, we will continue to evaluate ways of making the framework responsive friendly.
- **Custom vs reusable patterns.**<br>
  As new designs and layouts are created, falling on a spectrum between reusing an existing pattern to completely custom, it's up to the UI Engineer to decide how best to build it. Ultimately the goal of building a UI library is keeping your CSS codebase as small and manageable as possible, but you also have to weigh the needs and flexibility required by your brand and products. Can the design be altered slightly to fall into an existing pattern? If this isn't the case, should this be a new pattern or is it entirely unique? Negotiating this is more art than science and underscores the importance of collaboration and compromise between Designers and UI Engineers.
- **Stick to it.** Over time it's easy to let standards slide. A few hard-coded values and magic numbers here and there won't bring down your application. But since many CSS bugs arise from the unintended consequences of code written long ago it's important to keep those little time bombs out. That discipline will pay off.

## Results So Far

Though the following comparison isn't quite "apples to apples", these CSS statistics* come from two similarly visually complex products at Optimizely. The first is an older application built prior to OUI and the second used OUI exclusively.

                         | Product without OUI | Product with OUI | Change
------------------------ | ------------------- | ---------------- | ------
Gzip Size                | 101 KB              | 33 KB            | -68%
Stylesheet Size          | 618 KB              | 193 KB           | -69%
Rules                    | 3259                | 1757             | -46%
Selectors                | 5183                | 2407             | -54%
Identifiers              | 21375               | 4009             | -81%
Declarations             | 9356                | 4429             | -53%
Specificity Per Selector | 87                  | 15               | -83%
Top Selector Specificity | 641                 | 50               | -92%
ID Selectors             | 3135                | 0                | -100%

_\_ Most values generated using [Parker](https://github.com/katiefenn/parker), a "stylesheet analysis tool".*

## Summary

Managing a framework at your company, either by forking an existing one or writing your own, can be daunting. But for design and engineering teams concerned about maintaining consistency between projects, the benefits are too numerous to ignore. By relying on predefined patterns you'll get uniformity in visuals and in code, faster HTML builds, fewer bugs, less CSS bloat, and it allows product designers to spend less time on specifications and more time on the bigger challenges of user experience and information architecture.

[Twitter](https://twitter.com/intent/tweet?text=Building%20&%20Maintaining%20OUI%20(Optimizely’s%20UI%20Library):%20Part%201/2&url=https://css-tricks.com/optimizelys-ui-library-oui-1-of-2/&via=real_css_tricks) [Facebook](https://www.facebook.com/sharer/sharer.php?u=https://css-tricks.com/optimizelys-ui-library-oui-1-of-2/) [Google+](https://plus.google.com/share?url=https://css-tricks.com/optimizelys-ui-library-oui-1-of-2/)
