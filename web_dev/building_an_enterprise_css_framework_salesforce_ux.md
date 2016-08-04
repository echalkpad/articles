# Building an Enterprise CSS Framework -- Salesforce UX

[Original URL](https://medium.com/salesforce-ux/building-an-enterprise-framework-is-hard-1e8d8b33e082#.2c1vc64ra)

> Early in my career as a front-end developer, I was very idealistic. It seemed things were black or white. That there was a right or wrong way to do everything. Of course, idealism can be a fabulous...

Early in my career as a front-end developer, I was very idealistic. It seemed things were black or white. That there was a right or wrong way to do everything. Of course, idealism _can_ be a fabulous thing. Lofty goals are important.

At some point as I matured as a developer, pragmatism and practicality entered my world -- I started to perceive the shades of gray. For any given challenge, there might be several different but equally viable solutions. But pragmatism and idealism don't have to be in opposition to each other. Both are important to our craft when analyzing a situation and determining the most practical solution. They are the key to building a solid and maintainable structure.

After 16 years in the business, I was given the opportunity to join the Salesforce UX team, leading the effort to build an enterprise CSS framework for the new [Salesforce Lightning Design System](https://www.lightningdesignsystem.com/). The first thing I'll tell you is, "Building an enterprise framework is **hard**." I wasn't naive enough to think it would be _easy_, but enterprise has considerations that most frameworks don't need to take into account.

Our CSS framework will be consumed by a variety of disparate Salesforce users -- engineers building on our platform, rapid prototypers building different views for testing, the various Salesforce clouds, and partners building apps on our platform using a variety of methods. The Design System framework has to be platform-agnostic and meet the needs of users on a variety of different technology stacks.

![](https://cdn-images-1.medium.com/max/800/1*vRzbJmu0VC5yWRWbc7d-0w.png)

The previous Salesforce1 style guide With the launch of Salesforce1, our Design Systems team created a really awesome living style guide. It was a great first step in aligning an organization as large as Salesforce. But as the team talked with hundreds of customers at Dreamforce last year, they heard feedback that made them question whether the effort had gone far enough.

> _How do I get_ **_my_** _app to look like this?_

> _Can I use_ **_your_** _CSS in my app?_

Salesforce partners wanted their apps to have a Salesforce look, but many didn't have the resources or developers needed to make it happen, and those that did were inspecting in the browser to reverse engineer our code. A version of Bootstrap with a Salesforce look was created, though not by us, and it quickly fell out of step with our changing designs.

Internally, we also had a few needs not yet being met by our style guide. We want to empower our designers to code and iterate more efficiently in the browser. We want to enable our developers creating prototypes to build more quickly. But our style guide was static and didn't provide an easy, maintainable way to grab or use the code.

On top of all of that, we were finally creating a completely reimagined Lightning Experience to replace the Salesforce classic UI. You can imagine how large a task it is to keep all our designers, along with hundreds of engineers, on the same page.

To meet this variety of needs, we wanted to build a Design System comprised of Salesforce design patterns, CSS and markup for components, icons, a custom font, color swatches, and other resources that both internal developers and partners could take advantage of. We wanted it to grow and evolve as Salesforce matured, bringing developer's code along with it. We wanted our UI components to take the place of redline specs, allowing developers to reference actual code.

At Salesforce UX, we are guided by four design principles. In order of importance, they are -- clarity, efficiency, consistency, and beauty . These principles assisted us in prioritizing competing goals and helped us make tough calls.

I'd like to share some of the decisions we made while architecting the framework. Some of these choices may be unexpected. And there have been times when our ideas have morphed while building, as we discovered yet _another_ platform or situation we needed to solve for.

_Eliminate ambiguity. Enable people to see, understand, and act with confidence._

![](https://cdn-images-1.medium.com/max/400/1*xGWh8ebb-77TEFDlCEt9Fw.png)

I realize, anyone who's worked with CSS may already be struggling with the idea of "clarity in CSS". What does this even mean?

We chose clarity and understandability in class names. Our classes are explicit. We use few abbreviations. And yes, it is verbose. But with our wide range of users, and their varied expertise with CSS, we opted for clarity over brevity. For us it's worth the few extra characters.

We also opted for [BEM naming](https://css-tricks.com/bem-101/). No matter what you believe about BEM (and the variants people have created), it makes it easier for different developers on the same project to understand a class more quickly. A class name like _.house__door -- wood_ immediately tells you that you have a component called house, with a door made of wood. Single classes also keep your code base flatter, virtually eliminating specificity issues, and helps to keep it more maintainable.

Another area where clarity played heavily into our decisions was our documentation. We _love_ documentation. Well, to be perfectly honest, we don't necessarily love writing it. Who does? But we love what it does for our users.

![](https://cdn-images-1.medium.com/max/1200/1*GNpoJYhD9UQiQ2foWpYo3w.png)

Button Component page

Let's look at an example component, [buttons](https://www.lightningdesignsystem.com/components/buttons). At the top of each component is the basic description of the component itself. That is followed by information you might need to be sure that component is accessible. From there, we move to the _base_ style -- which is the core structure of the component. The HTML and Sass for the base style are displayed in tabs. You can continue scrolling down the page to see each variant, or on the right side of each component page, you can click a link to the variant you'd like to see.

Each component variant has information specific to it, a _living_ example you can interact with (you can hover or click the button to see its different states). It also displays the HTML and additional Sass needed for that variant. Variants are marked as either _prototype_ or _dev-ready_. Prototype means that there may be changes coming for that component as we work with it further.

At the bottom of each component section, we have a component overview. It shows all the classes used for that component, what happens when it's applied, whether it's required or not, the DOM element it should be placed on, and sometimes, comments and caveats.

_Streamline and optimize workflows. Intelligently anticipate needs to help people work better, smarter, and faster._

![](https://cdn-images-1.medium.com/max/800/1*85jgSdgueDxPpMY0ahYTyg.png)

We started this process by doing an audit and inventory of all the components in our designer's Lightning Experience Comps. (Wanna guess at how many font sizes we found?) Within our design system, design patterns were established to standardize all these designs. A sizing rhythm was established for fonts and spacing. Border-radius properties were aligned. Colors were minimized and aligned. This exercise let our designers align all their comps and allowed us to build lighter-weight, more efficient CSS.

As we inventoried each component, we were able to identify the objects and patterns which made up the component itself. We began by building the smallest objects, which were then combined to create the larger components.

One of those small objects is the [media object](https://www.lightningdesignsystem.com/components/media-objects). If you're familiar with [OOCSS](http://www.stubbornella.org/content/2010/06/25/the-media-object-saves-hundreds-of-lines-of-code/), you know that the media object is simply an image, icon or other figure sitting next to text. It's a very common pattern -- especially in our applications. Since our browser target for the framework is "all modern browsers including Internet Explorer 11," we had the latitude to utilize [flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox). This means we were able to combine the media object and a similar abstraction, the [flag object](http://csswizardry.com/2013/05/the-flag-object/), into a single object (the ._media -- centered_ variant displays like the flag object).

This basic object is combined with other small abstractions to create larger components. [Tiles](https://www.lightningdesignsystem.com/components/tiles) are a good example of a small component that uses even smaller abstractions. The [Activity Timeline](https://www.lightningdesignsystem.com/components/activity-timeline) is a larger component created from many smaller abstractions, including the media object, form elements, various lists, etc.

Our design audit showed us other interesting things. Enterprise applications demand content and data-rich interfaces. They lack vertical rhythm. In our design audits, we found that headings and paragraphs rarely have margins. So we removed them by default.

Semantically, lists are used a great deal in enterprise applications -- ul, ol and dl. But they rarely need list markers and certainly don't need their default indentation. By default, we removed the default list-style, margin, and padding. Then we created several utility classes to add the behavior back in if needed.

Though this might sound strange, it solves the majority of our use cases. We have a number of padding and margin utilities if users need to opt back in to spacing. We also have a wrapper class to put around a whole section of content if you need the default element styling in a whole area.

In a framework, we can't know how and where users plan to use headings. So another rather unique choice we made was to equalize all headings to a default of 1rem. This removes the temptation to use an h3 because it's the size a developer needs. They should use the proper semantic heading level and add the [proper text utility](https://www.lightningdesignsystem.com/components/utilities/text#heading-large) to create the heading style.

Working in the enterprise means that accessibility is a directive. Accessibility for all people and devices is a high priority at Salesforce. I love this since accessibility has always been near and dear to my development heart. For this reason, we bake accessibility into our framework.

- Where semantics matter, we use the proper elements.

- Our components receive the proper ARIA roles.

- We have accessibility documentation for components so that when you're adding javascript, you can be sure not to miss anything.

- Sizing is defined in REM units, so the whole component scales when text size increases. While I understand that people with younger eyes and good vision may say, "But the browser can zoom! What's the point?" There actually is a point. Many users with low vision change the default text size for their browser. When they surf onto your site with large text, if you've defined your components in pixels, the component will remain the default pixel size. But the text could be very large making it unsightly and possibly breaking out of the component bounds. With REM units, the component scales with the text and though larger, the user sees exactly what you designed.

_Create familiarity and strengthen intuition by applying the same solution to the same problem._

![](https://cdn-images-1.medium.com/max/800/1*x1vMGMKafCbrKhGu0563jg.png)

Design tokens are the basic atoms of the design system. Design tokens are simply variables or constants that represent a value. During the design audit I previously mentioned, we were able to identify common values which we then created as tokens. By using these tokens, or variables, our user's applications will remain in sync if there's a change in the design system value. You can find [tokens inside the resources section](https://www.lightningdesignsystem.com/resources/tokens) of the site.

We provide tokens for a large number of properties:

- Fonts

- Font Sizes

- Font Weights

- Line Heights

- Background Colors

- Text Colors

- Border Colors

- Shadows

- Spacing

- Sizing

- Animation Durations

- Media Queries

- Z-indexes

Using Sass and our token variables will also allow users to give their application custom branding if desired. Tokens are also great for our designers when specing their designs. They are able to use these tokens instead of absolute values.

Whether you're using Sass or not, every developer writing CSS knows that the most frustrating part of CSS is -- the C. Cascading and specificity wars are just awesome! _Not_. Since some of the class names used in our framework are common, and could exist in our user's personal CSS (or another framework), causing conflicts, we chose to namespace all our class names. Rather than using _.button_ we use _.slds-button_. As long as users can use normalize.css and our base element styles in their application, this is all that's needed.

But what if a user is in an environment where they don't control all of the DOM? They might be creating a Visualforce page or a Lightning component that includes their app in another environment. They might be transitioning off the out-of-date Bootstrap version created years ago. In that case, we offer scoping. This simply means they will wrap their design system components in an _.slds_ wrapper. Essentially, this keeps our base element defaults from clashing with those in the other system.

So for example, an _h1_ becomes ._slds h1_ so that, within our components, our element styles trump any other base element styling.

_Demonstrate respect for people's time and attention through thoughtful and elegant craftsmanship._

![](https://cdn-images-1.medium.com/max/400/1*4IS9fhjcMr4-w2GDFb5ttw.png)

Those of you building for a variety of form factors and devices know that making everything look great can be challenging. We chose SVG icons for beauty at every size. They allow you to have a consistent, crisp look no matter the resolution. We created sprites of our SVGs using the symbol element. Though we could have put all the accessibility requirements directly on the symbol element, we chose to only include the viewbox in the symbol. We allow the developer to add the accessibility text since it may vary with a specific usage. **Note**: Since _<symbol>_ is a replaced element, it has an inherent size in the browser. You must give the icons a width and height to override that default.

Most of our components are built so that the width is controlled by the parent container. They can be included in a wide, main page area or in a narrow sidebar. This makes it easier to keep both mobile and desktop applications attractive. Also, heights are rarely used. We've opted instead to use line-height, min/max-height and flexbox to control heights where needed. This is important for localization and accessibility as well.

Though we were guided by the four defining principles, my teammates [Jina Anne](http://twitter.com/jina), [Brandon Ferrua](https://twitter.com/bferrua) and [I](https://twitter.com/stefsull) also had numerous, and wide-ranging, discussions and debates about literally ALL THE THINGS. In my opinion, debate is a seriously healthy sanity check for all of us. Sometimes in making your case, you hear what you're missing. Other times you sway someone else. Two or three brains are always better than one.

As you've probably found when debating and discussing with colleagues, sometimes after you each make your point, you still don't come to an agreement. What to do? For us, we move to [Cap Watkins Sliding Scale](http://blog.capwatkins.com/the-sliding-scale-of-giving-a-fuck). It's an awesome idea that might help your team make decisions as well (though feel free to use whatever verbiage works in your team). In a nutshell, the person who's still the most passionate after the discussion wins!

We've just reached beta. We still have some things to clean up, changes to make and probably a good bit to add. It's a process. We're [open source](https://github.com/salesforce-ux/design-system) now, so we hope to get some great community feedback and assistance from you. While it _was_ hard, with the team we have working on this, hard has been the most fun I've ever had!
