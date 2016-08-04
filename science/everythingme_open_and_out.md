# EverythingMe open and out

[Original URL](https://medium.com/@joeysim/everythingme-open-and-out-6ed94b436e4c#.vkxbhvncq)

> do not go gentle into that good nightThis post is not about the past but rather about the future, EverythingMe has announced it has shut down its operations a few weeks ago, and since then...

> do not go gentle into that good night

This post is not about the past but rather about the future, EverythingMe has [announced](http://blog.everything.me/index.php/2015/11/22/everythingme-is-closing-down/) it has shut down its operations a few weeks ago, and since then we've done what's necessary to gracefully remove the app from the store, shutdown the service and communicate with our users.

> rage rage against the dying of the light

We've decided to actively open up as much of our code, tools and practices as possible for others to use, it was always part of our _moda operandi_ and it is only fitting to make that one last push so it doesn't fade into oblivion. If some of it helps other teams and stays alive we'll be proud and happy about it.

So without further ado, this is what we've managed to open:

One of the things EverythingMe was known for was its prediction bar, a section of your homescreen that surfaced the apps you'll most likely be using next. We didn't use a simple frecenecy algorithm or popularity ranking to power it, it wouldn't have achieved the end result we were aiming for -- finding that app you only use on Fri mornings in the city when you park your car, we called it "magic moments".

Instead of going the default routes, we joined forces with [Dr. Joseph Keshet](http://u.cs.biu.ac.il/~jkeshet/) and have rigorously iterated to come up with an on-device on-line learning approach that yielded better results than any other alternative.

Today, we're [**releasing a draft**](http://arxiv.org/abs/1512.07851) of the paper we'll soon publish that describes our approach and implementation details.

[**Vertex**](https://github.com/EverythingMe/vertex) - Go-based API management framework

The 3rd generation of our API framework which was crucial and has helped us move fast, for the price of defining your API endpoints and payload you get so much more -- a fast, data-validated, self-documenting, client-generating, tested API server.<br>
1st and 2nd generations were called Gondor and built in PHP and then ported to Python over tornado<br>
Contributors: [Dvir Volk](https://github.com/dvirsky)

[**Meduza**](https://github.com/EverythingMe/meduza)  ****-****  cross-language data store

A cross-language, cross database, loose schema NoSQL data store, with an Object Mapper for easy querying and code integration.<br>
It has a server implemented in Go, and clients currently implemented in both Go and Python.<br>
The idea behind Meduza is to create a simple yet powerful data abstraction API, but in contrast to other ORMs, make it easy to use it across different languages.<br>
Contributors: [Dvir Volk](https://github.com/dvirsky)

[**Teleport**](https://github.com/EverythingMe/teleport)

A python library that allows you to execute code within the networking context of a specific country, it is intended to run inside a docker container as it takes over the entire network "namespace".<br>
Have you ever needed to "look" at specific URLs from different countries? We surely did and that's why we streamlined the process to a point where all there's needed was a simple _with_ in our python code.<br>
Contributors: [Eddie Mishelevich](https://github.com/quatrix), [Omri Bahumi](https://github.com/omribahumi)

[**Lobo**](https://github.com/EverythingMe/lobo) - dev workflow cli

Internally we called this tool _wolf_, this is the tool we used to improve engineers productivity while tying everything together and providing visibility to the entire team.<br>
Lobo covers things like opening feature branches on multiple submodules, updating JIRA tickets status, issuing builds on our CI server, and uploading multiple APKs to the Play Store using Google Publishing API. You can think of it as our team's OS.<br>
Contributors: [Adam Kariv](https://github.com/akariv), [Rotem Mizrachi](https://github.com/rotemmiz), [Ran Byron](https://github.com/ranbena)

**Jitt** - user-driven translations for apps ([jitt.io](http://jitt.io))

A crowd-sourcing oriented localization service for Android applications. It holds all your resources along with their translations, and allows your users to suggest better translations or vote on current ones.<br>
It also has a cli tool called [**jitter**](https://github.com/EverythingMe/jitter) that helps integrating jitt into your builds.<br>
Contributors: [Adam Kariv](https://github.com/akariv)

[**Recat**](https://github.com/EverythingMe/recat) **+** [**PyRetrace**](https://github.com/EverythingMe/pyretrace)

A logcat alternative with on-the-fly log de-obfuscation --<br>
it retraces obfuscated logs using [pyretrace](https://github.com/EverythingMe/pyretrace), a python reimplementation of Proguard's Retrace.<br>
Contributors: [Rotem Mizrachi](https://github.com/rotemmiz)

[**Overscroll Decor**](https://github.com/EverythingMe/overscroll-decor)

The library provides an iOS-like over-scrolling effect for many Android native views. It also allows for very easy expansion to support custom views.<br>
Contributors: [Amit Davidi](https://github.com/amitEverythingMe)

[**Probe**](https://github.com/EverythingMe/probe)  ****-****  android performance instrumentation

Our solution for the vicious cycle of performance tuning sessions we used to go through after adding functionality to our app. We built a tool that ran on all builds on all branches and collected vital signs we could analyze, detect deviations and avoid bringing in performance regressions.<br>
Contributors: [Rotem Mizrachi](https://github.com/rotemmiz)

![](https://cdn-images-1.medium.com/max/600/1*nR5y4PZaWz0Gumzaiq9uYA.png)

Redash **R**[**edash**](http://redash.io/) -- our open source data querying and collaboration tool will live on and its maintainer [Arik Fraimovich](https://github.com/arikfr) is hard at work making it even better, you should keep an eye on this one (and take it for a spin if you haven't already).

![](https://cdn-images-1.medium.com/max/600/1*g-j1I2btV17f8nOaHyMGBw.jpeg)

Magneto [**Magneto**](http://getmagneto.com/) -- our Android UI automation framework has received its own [organization](https://github.com/magnetotesting) on Github and we're calling interested parties who wish to help maintaining it, it needs you!<br>
Maintainer: [Ran Byron](https://github.com/ranbena)

If you're interested in the entire EverythingMe OSS portfolio including projects we've open sourced in the past, you can find it here <http://everythingme.github.io/>

EverythingMe, over and out.
