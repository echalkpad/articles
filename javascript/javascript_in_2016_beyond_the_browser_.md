# JavaScript In 2016 - Beyond the Browser -

[Original URL](http://developer.telerik.com/featured/what-to-expect-from-javascript-in-2016-beyond-the-browser/)

> This is the third part of a 3 part series on what to expect from JavaScript in 2016 taken from our whitepaper, "The Future of JavaScript: 2016 and Beyond". Today's post focuses...

_This is the third part of a 3 part series on what to expect from JavaScript in 2016 taken from our whitepaper, "[The Future of JavaScript: 2016 and Beyond](http://www.telerik.com/campaigns/kendo-ui/javascript-future)". Today's post focuses on the future of the JavaScript language itself. Part 1, which covered the future of JavaScript frameworks, [can be found here](http://developer.telerik.com/featured/what-to-expect-from-javascript-in-2016-frameworks/) and part two, which covers the evolution of the language, [can be found here](http://developer.telerik.com/featured/what-to-expect-from-javascript-in-2016-language-enhancements/)._

In the last several years, JavaScript, a scripting language designed for use in web browsers, has been used in an increasingly diverse set of software applications. With JavaScript now running as server-side code, driving iOS and Android apps, and even [controlling robotics](http://johnny-five.io/), it's hard to find a software ecosystem that JavaScript hasn't influenced.

Part of what's driving JavaScript's expansion into these "new frontiers" is performance. Whereas, years ago, running JavaScript on a server was unthinkable, Google's entry into the browser and [JavaScript engine](http://developer.telerik.com/featured/a-guide-to-javascript-engines-for-idiots/) world in 2008 sparked a performance competition that drastically improved the speed of the language. More recent efforts such as [asm.js](http://asmjs.org/) have only furthered this effort.

In this article, we'll look at what's next for the JavaScript frameworks being used to run server-side JavaScript, build mobile apps, and to build desktop applications. We'll get the perspective from many key developers involved in building these solutions directly. Let's start by looking at what is perhaps JavaScript's first new frontier: Node.js.

## Node.js

[Node.js](https://nodejs.org/en/) is an open source runtime environment based on Google's V8 JavaScript engine. Although plenty of companies and frameworks had tried to run JavaScript on the server, Node.js was the first runtime to succeed at doing so at scale.

Node.js was first written in 2009, and has since skyrocketed in popularity. The [list of companies using Node.js](https://github.com/nodejs/node-v0.x-archive/wiki/Projects,-Applications,-and-Companies-Using-Node) is enormous, and the recently formed [Node.js Foundation](https://nodejs.org/en/foundation/members/) includes the likes of IBM, Intel, PayPal, and Microsoft. The Node.js package manager, npm, [became the biggest package manager in the software world in 2014](http://www.modulecounts.com/), and now contains nearly twice as many modules as similar package managers from the Java and Ruby worlds.

<embed src="http://developer.telerik.com/wp-content/uploads/2016/01/modulecount-300x214.jpg%20300w,%20http://developer.telerik.com/wp-content/uploads/2016/01/modulecount.jpg%20587w" class="alignnone size-full wp-image-67371" width="587">

<br>
_Growth of npm as a package manager. Image from_ [_modulecounts.com/_](http://www.modulecounts.com/)_._

However, the success of Node.js hasn't come without growing pains. In late 2014 a group of developers [forked the popular framework](http://www.javaworld.com/article/2855639/open-source-tools/qanda-why-io-js-decided-to-fork-node-js.html), citing the lack of active and new contributors and the lack of releases. This new framework, [io.js](https://iojs.org/en/), quickly gained followers and community support, leaving many to fear a long-term fragmentation in the Node.js world. Thankfully those fears were averted when [Node.js and io.js](http://www.linuxfoundation.org/news-media/announcements/2015/06/nodejs-foundation-advances-community-collaboration-announces-new) merged in June of 2015.

Part of the merger involved the formation of an LTS, or a [Long-Term Support plan for Node.js releases](https://github.com/nodejs/LTS). Under the plan, Node.js will designate one release per year an LTS release, and will actively support that release for a full 18 months.

<embed src="http://developer.telerik.com/wp-content/uploads/2016/01/nodereleases-300x168.jpg%20300w,%20http://developer.telerik.com/wp-content/uploads/2016/01/nodereleases-768x431.jpg%20768w,%20http://developer.telerik.com/wp-content/uploads/2016/01/nodereleases.jpg%20800w" class="alignnone size-full wp-image-67381" width="800">

The development cycle is aimed to appease both developers that want to stay on the cutting edge and large companies that need a stable release they can count on for years to come. The development cycle also has major implications for the future of Node.js. When I asked [Mikeal Rogers](http://mikealrogers.com/) from the Node Foundation what the biggest change coming for Node.js in 2016 he had this to say:

> "The new development cycle is going to be the biggest change. We'll have 2 major releases each year, with only one of those releases receiving Long Term Support. That's significantly more than before and we've never truly had LTS before so this is all a big change for developers and a new opportunity for enterprise to expand adoption as well." Mikeal Rogers, Node Foundation

### Node.js in 2016

In 2016 expect to see further adoption of Node.js and its package manager npm. The continued adoption of Node from large companies -- Microsoft, IBM, Intel, Progress Software, etc. -- as well as enterprise-friendly features such as long-term support plans, may signal a growth in Node.js adoption in the enterprise, replacing typical enterprise solutions like .NET and Java.

Expect the exponential growth in modules on npm to continue as well, as the npm's recent releases have [aimed at providing better support for client-side JavaScript](http://blog.npmjs.org/post/122450408965/npm-weekly-20-npm-3-is-here-ish), thus replacing the need for client-side JavaScript package managers such as Bower. As developers start [registering their client-side scripts and jQuery plugins on npm](http://blog.npmjs.org/post/112064849860/using-jquery-plugins-with-npm), npm's reach will only grow. In fact, according to Mikeal Rogers, the major reason for npm's growth is that is an ecosystem of ecosystems.

> "A few years back I quantified the growth rate of npm and created a predictive graph. At the time people thought it was insane, because it said that in a little over a year we'd have over 100K modules and that the rate of growth wouldn't level out. We hit 100K modules within a few days of what it predicted which even I was shocked by.

> If you dive deep into npm's growth you'll see that what is pushing it forward is that npm is an ecosystem of ecosystems. It's the best place to build sub-platforms for a variety of use cases. Parts of that ecosystem are leveling off but they keep getting replaced by new rapidly growing ecosystems." Mikeal Rogers, Node Foundation

As evidence of this claim, basically every other JavaScript solution in this article -- including Cordova, React Native, and NativeScript -- all use npm as a package manager. A quick npm search for ["jquery"](https://www.npmjs.com/search?q=jquery), ["polymer"](https://www.npmjs.com/search?q=polymer), ["meteor"](https://www.npmjs.com/search?q=meteor), or ["react"](https://www.npmjs.com/search?q=react) can give you an idea of the sheer scale npm operates at now. As JavaScript grows in popularity, npm grows in popularity. And as npm grows in popularity, so does Node.js. The future looks bright for the software world's first mainstream server-side JavaScript framework.

Let's shift our focus to some technologies that don't run JavaScript on the server, but rather, use JavaScript to drive mobile apps.

## PhoneGap and Cordova

Much like Node.js was the first mainstream solution for running JavaScript on the server, PhoneGap was the first mainstream solution for using JavaScript to run native mobile apps. PhoneGap was originally created by Nitobi in 2009, and was [acquired by Adobe in October of 2011](http://www.adobe.com/aboutadobe/pressroom/pressreleases/201110/AdobeAcquiresNitobi.html). As part of the acquisition, the PhoneGap source code was donated to the Apache Software Foundation and the project became known as Cordova. Today, Cordova is the free and open source framework that [many companies contribute to](https://wiki.apache.org/cordova/who) and PhoneGap is an Adobe-owned distribution of Cordova.

Over the years Cordova has defended itself against a perception of bad performance, with the [most notorious complaint](http://techcrunch.com/2012/09/11/mark-zuckerberg-our-biggest-mistake-with-mobile-was-betting-too-much-on-html5/) coming from one of technology's most influential people in 2012.

> "When I'm introspective about the last few years I think the biggest mistake that we made, as a company, is betting too much on HTML5 as opposed to native... because it just wasn't there." Mark Zuckerberg, Facebook

Since 2012, a number of companies have stepped in to attack this performance problem. This includes performance-minded UI frameworks like [Ionic](http://ionicframework.com/), [Onsen](https://en.wikipedia.org/wiki/Onsen), and [Kendo UI Mobile](http://demos.telerik.com/kendo-ui/m/index), [tooling improvements from Telerik and the PhoneGap team](http://developer.telerik.com/featured/the-state-of-hybrid-mobile-development/), new web views such as those provided by [Crosswalk](https://crosswalk-project.org/), and high-quality plugins, such as those found in the [Telerik Verified Plugins Marketplace](http://plugins.telerik.com/cordova).

<embed src="http://developer.telerik.com/wp-content/uploads/2016/01/plugins-300x202.jpg%20300w,%20http://developer.telerik.com/wp-content/uploads/2016/01/plugins-768x517.jpg%20768w,%20http://developer.telerik.com/wp-content/uploads/2016/01/plugins.jpg%20800w" class="alignnone size-full wp-image-67391" width="800">

<br>
_The Telerik Verified Plugins Marketplace_

In addition to company-provided performance aids, new features provided by mobile OS makers, such as [Android's auto-updating web views](http://developer.telerik.com/featured/android-5-0s-auto-updating-webview-means-mobile-apps/) and [iOS's new WKWebView API](http://developer.telerik.com/featured/why-ios-8s-wkwebview-is-a-big-deal-for-hybrid-development/), have greatly improved the Cordova performance situation. With that in mind, I asked [Brian LeRoux](http://brian.io/), formerly of Adobe's PhoneGap team, about what's next for Cordova.

> "Cordova has grown very deliberately stable. We strive to keep things simple, push the features out to the plugins interface, and stay on top of platform upgrades like Android M and iOS 9 as much as possible. It took a few years of thrashing but 'small modules' mindset is beginning to take hold which makes me happy. The end dev audience won't see this unless they extend Cordova with their own distribution." Brian LeRoux

### Cordova in 2016

Much like Node.js, Cordova's stability will appeal to large companies, many of which are just dipping their toes into the waters of mobile development. The Cordova approach to building mobile apps with HTML, CSS, and JavaScript will continue to appeal to web developers, especially when compared to native development options involving Xcode and Android Studio.

Although Cordova continues to grow in popularity, its development approach is being challenged from two different angles. The first is from Google, who is pushing the concept of [progressive apps](https://infrequently.org/2015/06/progressive-apps-escaping-tabs-without-losing-our-soul/), which are true web apps with native-like features such as splash screens, home screen placement, and offline access. Progressive apps are still in their early days, and their features are still only offered on Chrome for Android, however, expect Google to continue pushing the concept of progressive apps in 2016.

The bigger immediate challenge to Cordova development comes from a recent development in the JavaScript world: using JavaScript to build truly native mobile applications.

## Native mobile apps

2015 saw the emergence of a new category of JavaScript-based mobile app development known as ["JavaScript Native"](http://developer.telerik.com/featured/defining-a-new-breed-of-cross-platform-mobile-apps/). Unlike Cordova/PhoneGap-based apps, JavaScript Native apps use a platform's native controls and paradigms to build their user interfaces; there is no browser or web view involved.

JavaScript Native frameworks attempt to offer a best-of-both-worlds way to build iOS and Android apps: use JavaScript to write your application logic (rather than Java, Swift, and so forth), and use a platform's native user interface APIs to build apps that fit in on the native OS, and offer the best possible performance.

<embed src="http://developer.telerik.com/wp-content/uploads/2016/01/jsnativeapps-300x152.jpg%20300w,%20http://developer.telerik.com/wp-content/uploads/2016/01/jsnativeapps-768x390.jpg%20768w,%20http://developer.telerik.com/wp-content/uploads/2016/01/jsnativeapps.jpg%20800w" class="alignnone size-full wp-image-67401" width="800">

<br>
_Example of mobile apps that were built with JavaScript._ [_Check out the source code_](https://github.com/NativeScript/nativescript-marketplace-demo)_._

[React Native](https://github.com/facebook/react-native) and [NativeScript](https://www.nativescript.org/) were the first JavaScript Native frameworks to publicly release in 2015, and they were followed by others such as [Fuse](https://www.fusetools.com/) and [tabris.js](https://tabrisjs.com/). Different frameworks offer different features. For instance, React Native lets you reuse the React JavaScript framework while NativeScript lets you [access iOS and Android APIs directly from JavaScript](http://developer.telerik.com/featured/nativescript-works/). But they share the high-level approach to building truly native apps with JavaScript.

Although the idea of building native apps with JavaScript can sound appealing to web developers, JavaScript Native frameworks can have some drawbacks when compared frameworks like Cordova. Here are a few:

- Because JavaScript Native frameworks don't use a browser, you have to learn framework-specific APIs for building your interfaces, rather than simply using HTML as you would in a Cordova app.
- Because JavaScript Native apps are native apps, memory management can be a concern when building larger apps, just like it is in native iOS and Android apps.
- Finally, JavaScript Native frameworks are new, and as such, there are fewer examples and tutorials. The frameworks themselves are less mature than frameworks that have been under active development for many years.

I asked [Christopher Chedeau (aka Vjeux)](http://blog.vjeux.com/) from the React Native team and [Valio Stoychev](https://twitter.com/valiostoychev), NativeScript's product manager, about what's coming for their frameworks in 2016, and both echoed this focus on stability.

> "For React Native, we exited the phase where it was a crazy idea/prototype and now enter the phase where we need to make it solid. You should see a lot of work being done on performance tooling/optimizations, improvement of all the core APIs, better error messages, fix edge cases... This way engineers at Facebook and outside can build the high quality mobile apps they want to." Christopher Chedeau (Vjeux), Facebook

> "As our user base rapidly grows, we need to make sure our users have a robust framework they can count on for building real-world applications. Therefore we intend to continue working on things like performance and debug tooling to improve the NativeScript developer experience. Our other major focus is [our work with the Angular 2 team](http://angularjs.blogspot.com/2015/12/building-mobile-apps-with-angular-2-and.html), which we anticipate will continue throughout 2016." Valio Stoychev, Telerik

### JavaScript Native in 2016

Expect 2016 to be the year of stability and adoption for JavaScript Native platforms in 2016\. As frameworks like React Native and NativeScript solidify their feature set, expect to see an increase in tooling being created around those frameworks, such as [Telerik's own Telerik Platform](http://www.telerik.com/platform) for building NativeScript apps.

Time will tell whether the hype JavaScript Native apps generated in 2015 will transfer to large-scale usage in 2016, but the number of high-quality applications already being built with these frameworks (see [React Native's showcases](https://facebook.github.io/react-native/showcase.html) and [NativeScript's showcases](https://www.nativescript.org/showcases)) suggest that the JavaScript Native approach to building applications will be around for some time to come.

For companies that need native apps with native UIs, JavaScript Native frameworks offer a compelling option when compared to building an iOS app with Xcode and Objective-C/Swift and an Android app with Android Studio and Java, especially considering many companies have developers with existing JavaScript development skills.

Overall, JavaScript Native apps represent an exciting new frontier for JavaScript developers. No longer do JavaScript developers have to learn native programming languages to write a native mobile app. And native mobile apps aren't the only software world JavaScript is creeping into – the same is true of traditional desktop applications.

## Desktop apps

Traditionally, if you wanted to build a Windows or Mac app you'd use platform-specific tools like WPF and Windows Forms or cross-platform interfaces using something like Java or Adobe Air. But, like every other software ecosystem discussed in this article, JavaScript-based solutions are slowly working their way into this picture.

The first JavaScript-based solution in this space was Node-WebKit, which was created by Intel and was open-sourced in late 2011\. Node-WebKit, now called [NW.js](http://nwjs.io/) because of its switch from WebKit to Chromium internally, works somewhat similarly to Cordova but for desktop apps.

<embed src="http://developer.telerik.com/wp-content/uploads/2016/01/nwjs-300x150.jpg%20300w,%20http://developer.telerik.com/wp-content/uploads/2016/01/nwjs-768x385.jpg%20768w,%20http://developer.telerik.com/wp-content/uploads/2016/01/nwjs.jpg%20800w" class="alignnone size-full wp-image-67411" width="800">

<br>
_NW.js was developed at Intel, and released back in 2011_

NW.js packages up a web application in a native shell, while providing access to native desktop APIs, such as the file picker, window menus, and so forth. The combination lets you write Windows, OS X, and Linux desktop applications using standards-based web technologies.

Fast forward a few years and NW.js is not the only framework using such an architecture. In April of 2015 [GitHub announced Electron](http://blog.atom.io/2015/04/23/electron.html), a similar framework for building cross-platform desktop apps.

<embed src="http://developer.telerik.com/wp-content/uploads/2016/01/electron-300x209.jpg%20300w,%20http://developer.telerik.com/wp-content/uploads/2016/01/electron-768x534.jpg%20768w,%20http://developer.telerik.com/wp-content/uploads/2016/01/electron.jpg%20800w" class="alignnone size-full wp-image-67421" width="800">

<br>
_GitHub's Electron was released in April 2015_

Electron was developed as the shell for [Atom](https://atom.io/), GitHub's web-based text editor, and has since been decoupled to make it easier to use in any project. With GitHub's backing, Electron has surged in popularity, and now has over [20,000 stars on GitHub](https://github.com/atom/electron) (quickly catching up with [NW.js's 25,000+ stars](https://github.com/nwjs/nw.js)).

Electron also made headlines in 2015 as the engine behind Microsoft's new [cross-platform Visual Studio Code IDE](https://code.visualstudio.com/), and a quick look through a [list of community-created Electron resources](https://github.com/sindresorhus/awesome-electron) shows just how popular Electron has become in the development community.

### Desktop apps in 2016

Like many of the technologies discussed in this article, the future seems bright for these cross-platform JavaScript-based tools for building desktop apps. With the likes of GitHub, Microsoft, and even Slack -- which isn't built on NW.js or Electron, but also takes the approach of using web technologies to build a native app -- other companies can feel confident building desktop apps with web technologies. Expect projects like NW.js, Electron, and others to drive many of the new desktop applications you see launching in 2016.

## JavaScript's new frontiers in 2016

Although this article has discussed a seemingly disparate set of topics -- server-side code, mobile apps, and desktop apps -- the narrative has been the same: in the span of a few years, running JavaScript in these contexts has gone from unthinkable to mainstream. In less than a decade, JavaScript has gone from a toy language for handling image rollovers, to perhaps the world's most popular programming language. And there seems to be no end to where JavaScript can go.

In 2007, Jeff Atwood [famously stated](http://blog.codinghorror.com/the-principle-of-least-power/) that "any application that _can_ be written in JavaScript, _will_ eventually be written in JavaScript", and that statement has never seemed more prophetic. JavaScript has reached places that there wasn't space enough to cover in this article, such as running on hardware through projects like [Johnny-Five](http://johnny-five.io/) and being [offered as a first-class citizen for building native apps on Apple's recently announced tvOS](https://developer.apple.com/library/tvos/documentation/General/Conceptual/AppleTV_PG/YourFirstAppleTVApp.html) for Apple TVs.

One of the reasons driving JavaScript's growth is the desire for a single development model to build software for multiple paradigms. Companies, especially small companies, cannot possibly hire developers with the expertise to reach the crazy number of operating systems and devices people use today. This is even a problem at a company at Facebook's scale, as Christopher Chedeau shares:

> "To me, the big tragedy of the developer world today is that communities are divided by language, we even call them ecosystems. JavaScript, Java, Objective-C, Python, C++, . What happens is that there is a massive waste of effort as each ecosystem have the same tools such as package manager, IDE, core libraries, knowledge base...

> To give a concrete example, at Facebook, we need to implement the exact same feature 3 times: for Web, iOS and Android. Even worse, because it's so hard for one engineer to get ramped up in those ecosystems, we usually have 3 people implementing that feature. This is sad.

> In order to solve that, my intuition is that there needs to be a single language/ecosystem. With React Native we opted for JavaScript but in the grand scheme of things it doesn't matter which language it is. What's most important is that there is only one." Christopher Chedeau, Facebook

With JavaScript rapidly becoming a viable option in all of these worlds -- mobile, desktop, server, hardware -- it's uniquely positioned to make this desire to build once a reality. Time will tell whether JavaScript's meteoric growth continues in 2016 and beyond, but the surge in popularity of JavaScript tooling across software ecosystems seems to indicate that there's no end in sight.

With that in mind I'll let Brendan Eich's famous quote stand as the last word in this article: "[Always bet on JS](http://alwaysbetonjs.com/)."
