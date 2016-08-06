# Turn-key Electron apps with Meteor

[Original URL](https://mixmax.com/blog/turnkey-electron-apps-with-meteor)

> Monday, Jan 11th, 2016 Mixmax is a communications platform that brings email into the 21st century. We believe everything you do today on the web should be possible in any email. Last month, we...

**Monday, Jan 11th, 2016**

_Mixmax is a communications platform that brings email into the 21st century. We believe everything you do today on the web should be possible in any email._

Last month, we released our first desktop application to [rave reviews](https://www.producthunt.com/tech/mixmax-live-for-mac). After several improvements and the release of the Windows version, we're pulling back the curtain to reveal how we built the app.

When we decided to make a desktop application, [Electron](http://electron.atom.io/) was an easy choice. Using a cross-platform framework would allow us to reach the most users, critical for a business at our early stage. Using a framework based on web technologies would allow us to reuse parts of our web application--and best leverage the talent behind it, in particular [our designer](https://twitter.com/chanpory)'s marvelous 

<span class="caps">HTML</span>

 and 

<span class="caps">CSS</span>

 skills. And using a framework based on Chromium would give us access to the same bleeding-edge web APIs we use in our web application in our desktop app.

But with that choice made, there were still a number of questions remaining: how would we...

- load our web application in Electron?
- package the app?
- communicate between the web and native parts of the app?
- distribute the app?

To answer these questions, we reviewed a ton of Electron documentation, GitHub issues, and prior art. The result is a Meteor package, [meteor-electron](https://github.com/rissem/meteor-electron), that makes it as easy as `meteor add quark:electron` to get your Meteor application running in a native shell. If you'd like to try it out, head on over to the GitHub repo; but if you're interested in its development, read on!

## Preface

We developed our Electron infrastructure as a Meteor package because the web application we were wrapping was a Meteor application, but the techniques and lessons described below would in large part apply to packaging any web application (using Rails, Django, Express, etc.) as an Electron app.

With that said, Meteor facilitates a particularly smooth workflow in that its packages can hook into the build process, server, _and_ client. As you will see, Electron development requires coordinating changes across all three of those areas.

## Meteor in the cloud, Electron on the desktop

We first came across a prototype of meteor-electron when deciding how to load our Meteor app in Electron: remotely or locally? There were [several](https://github.com/sircharleswatson/Electrometeor) [packages](https://github.com/arboleya/electrify) that promised to do the latter--build and run Meteor _inside_ the Electron app. But that decision came with [limited](https://github.com/sircharleswatson/Electrometeor#windows-1) [support](https://github.com/arboleya/electrify#notes) for building the app for multiple platforms. The primary advantage of the local approach seemed to be offline support--but we didn't need that for v1\. More important to us was getting a cross-platform app out as soon as possible and being able to quickly iterate upon it. That made the case for loading Meteor remotely even more strongly, since we could use Meteor's [hot code push](http://info.meteor.com/blog/hot-code-pushes) to update the bulk of the app.

## Automatic packaging

We were also attracted to meteor-electron by its focus on ease of use. Where other projects foregrounded customization and build processes, meteor-electron got us started right away. When we added its package to our app, it automatically downloaded the Electron binary for our system, packaged our application, and opened it in a native shell.

The packaging step bears some discussion: the officially-supported way to package an Electron app is to [manually edit the contents of the Electron binary](http://electron.atom.io/docs/v0.36.0/tutorial/application-distribution/)! That's crazy. Under the hood, meteor-electron uses the excellent community project [electron-packager](https://github.com/maxogden/electron-packager) to bundle your code and resources. What's more, meteor-electron uses reasonable defaults for packaging, [easily customizable](https://github.com/rissem/meteor-electron#configuration) through your [Meteor.settings](http://docs.meteor.com/#/full/meteor_settings) file.

This turn-key packaging was a great user experience, and one that we've enhanced working with meteor-electron's creator, [Mike Risse](https://twitter.com/rissem). For instance, we've since improved the project to automatically rebuild and relaunch the app when the native code or the application settings change.

## Safe native bridge

Once we had our application loading in meteor-electron, we started to consider how we could customize it for the native context. Some simple [feature detection APIs](https://github.com/rissem/meteor-electron/blob/4561e0156152267d00a68e147e54875e7b8f8823/client/index.js#L8) were trivial to add thanks to meteor-electron's ability, as a Meteor package, to load code on the server (like the packaging logic) _and_ the client.

Providing the client with access to native capabilities was trickier. Or should I say, it was _too_ easy: by default, all Node APIs and modules are accessible from web content.

Electron assumes that you will be loading your 

<span class="caps">HTML</span>

 etc. from disk, and thus views it as a trusted extension of the native code. But this assumption fails disastrously for hybrid native-web applications like Meteor desktop apps.

Thankfully, it is possible to disable this Node integration when opening browser windows. (Bonus: doing so can fix web dependencies failing to load by incorrectly assuming they're [running in a CommonJS environment](https://github.com/atom/electron/issues/254).)

We do still wish to expose _certain_ native APIs to the web content. Luckily, there exists a mechanism for doing so called a "preload script". This script is loaded before other scripts run in the page; always has access to Node APIs, no matter whether Node integration is turned on or off; and can add symbols to the page's global scope. With Node integration turned off:

                                 | Native scripts | Preload script | Browser scripts
-------------------------------- | -------------- | -------------- | ---------------
Access to Node global context    | <              | >              |
Access to browser global context |                | <              | >

This means that the preload script can provide the webpage with indirect access to select Node APIs: meteor-electron's use of a preload script thus makes it possible for Meteor apps to safely make use of native functionality.

## Distribution: to be continued...

You've seen thus far how meteor-electron offers Meteor developers a simple and safe path to creating desktop applications. In part 2 of this blog post, we'll talk about how meteor-electron adds support for distribution, even automatic updates, without developers having to add any server-side code themselves.

_Tips for converting webapps to desktop apps? Tweet us [@MixmaxHQ](https://twitter.com/intent/tweet?screen_name=MixmaxHQ) or drop us a line at [hello@mixmax.com](mailto:hello@mixmax.com). You can also come to [Meteor Night 1/21](http://www.meetup.com/Meteor-SFBay/events/227198908/) to see Mike and Jeff demo meteor-electron live._

_Interested in solving hard problems like this? Come [join us](https://mixmax.com/careers) at Mixmax._
