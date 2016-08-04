# An introduction to visual programming using NoFlo

[Original URL](http://rawkes.com/articles/an-introduction-to-noflo-and-flow-based-programming)

> I recently stumbled upon NoFlo during my quest for a better way to manage the realtime processing and transformation of data within ViziCities. I was dubious about this bizarre JavaScript framework...

I recently stumbled upon [NoFlo](http://noflojs.org) during my quest for a better way to manage the realtime processing and transformation of data within [ViziCities](http://vizicities.com). I was dubious about this bizarre JavaScript framework at first, initially casting it aside as a fun distraction. In fact, it was only after spending a full day experimenting with NoFlo that I began to comprehend its full potential. And boy does it have potential!

![](http://rawkes.com/media/noflo-introduction/noflo.jpg)<br>
So what actually is NoFlo? And why would you want to use it? No one wants yet another framework to learn, nor another pitch for the Next Big Thing™ in programming (a.k.a. "I didn't like anything else so I half-baked something new that no one will use"). I'm a grumpy cynic at the best of times, usually casting aside all the new crap as a fad (it usually is), but NoFlo is different. It has inspired me and filled me with a kind of excitement and energy that I've not felt for a long time. More specifically, the concept that NoFlo is created on has the potential to change the way you approach programming, forever. I'm deadly serious.

In the following series of posts I will dig into NoFlo, take a look at what can be done with it and show you how to use it. But first, let's unwrap this magical concept that NoFlo is based upon.

## Flow-based programming

Back in the 1970s, a man by the name of Paul Morrison decided it would be a good idea to invent a new way to construct computer programs. It's called flow-based programming, or FBP for short.

![](http://rawkes.com/media/noflo-introduction/ibm-system360.jpg)<br>
Computers were so large in the 1970s that you required an army to use them!

Based on [dataflow programming](http://en.wikipedia.org/wiki/Dataflow_programming), the theory of FBP is simple -- break a computer program into a series of repeatable individual components and network them together.

In [Paul's own words](http://www.jpaulmorrison.com/fbp/):

> FBP defines applications as networks of "black box" processes, which exchange data across predefined connections by message passing, where the connections are specified externally to the processes. These black box processes can be reconnected endlessly to form different applications without having to be changed internally. FBP is thus naturally component-oriented.

Easy peasy! Now I don't know about you, but I find it easier to understand something by doing it. Technical descriptions end up confusing me, then as soon as I think it's started to make sense I become even more confused.

Fortunately, you need only understand the gist of FBP for this series (that it's about breaking things into components and writing little if no code) as the benefits will become obvious as you start to use it. If you do want to dig deep into FBP than I suggest taking a look at [Paul's fantastic website on it](http://www.jpaulmorrison.com/fbp/), including a [wiki overview of the core concepts](https://github.com/flowbased/flowbased.org/wiki).

## Why FBP over, you know, normal programming?

Good question! There's my own personal opinion and then the opinion of the FBP hard-liners. They will say that you should always be using FBP, that once you open your eyes to its majesty then you'll never want to program "normally" ever again. I actually agree with that, partly.

On the flip-side, the grumpy cynical side of me says that you should continue doing things how you do them now, but that you should try out FBP and see what sticks. You might end up using it in its entirety, or even a mix of both, or perhaps you'll never want to look at it again. Any of those options are fine.

Specifically, FBP does introduce new ways of thinking about things and it does make things a lot better in certain areas. None of them are unique to FBP, though together they combine to create a rather powerful offering.

For example, FBP fosters the concept of compartmentalised logic and processing. In fact, it forces you to break things down into processes that ideally take a single expected input and use that to produce a single expected output. These components can be reused amongst other areas of your application, or you can even take components written by other people (think NPM), requiring absolutely zero new code to be written. A perfect FBP application could be created with existing components without writing a single line of code (I'll show you how in a later post). That's pretty amazing.

This compartmentalism (pretty sure I just made that up) means that your application is inherently more robust, more reliable, more testable, more everything-able! FBP literally allows you to create applications faster, and that speed only increases the more you use it and the more components you create (or become available elsewhere). That's an unavoidable fact, not a promise.

## So why not FBP?

FBP isn't all shiny (like every single thing ever, apart from perhaps those mirrors they put in space telescopes). I mean, it really is a fantastic concept and its benefits far outweigh the downsides, but those downsides will rule it out for some situations and that's ok.

One example is that you can find yourself duplicating similar processes in FBP. If you need to run multiple transformations on an array of data then you may find yourself looping over that data multiple times across different components. This introduces extra processing time to your application.

Then there's the overhead of sending data between components. This is an unavoidable part of FBP and it means that every single time you send a message you require a tiny amount of processing time to do that. This can add up when you start sending 1000s of pieces of data around the system.

Fortunately, you can learn to notice and avoid both of these situations. Once you've got used to FBP you'll begin to notice common approaches and optimisations. For example, you can avoid the previous situations by carefully thinking about the right component for the job. There are many ways to achieve the same result in FBP, which is both a blessing and a curse.

## FBP's magic bullet -- visual programming

The crown jewel of FBP is the ability to construct applications using visual tools. This completely changes the way you look at a computer program, allowing you to get a much better idea about the entire structure and flow of your application.

![](http://rawkes.com/media/noflo-introduction/noflo.jpg)<br>
Instead of needing to comprehend thousands of lines of code across countless files, with visual programming using FBP you instead need only connect the dots, quite literally.

This approach means that the application itself visually shows you how it works, rather than requiring prior knowledge of the entire system before understanding how it all fits together. This not only makes more sense to the creator of the application but it also means someone new can look at it and comprehend it nearly immediately. Even if they aren't a programmer!

![](http://rawkes.com/media/noflo-introduction/quartz-composer.png)<br>
This kind of visual programming isn't new. Apple's Quartz Composer [has done this for years](http://www.bbc.co.uk/blogs/legacy/radiolabs/2009/10/fun_with_quartz_composer_in_sn.shtml), as have [Max MSP](https://cycling74.com/products/max/), [vvvv](http://vvvv.org/), [NodeRed](http://nodered.org/) and many others.

Creating applications in this way is incredibly powerful as it allows you to focus on getting to the final result rather than writing the intermediate code and logic. It's a strangely liberating feeling to create an application in a graphical tool without writing a single line of code.

Further on in the series we'll take a look at the new browser-based tooling that exists to create applications in this way.

## Enter NoFlo

It's at this point that [NoFlo](http://noflojs.org/) enters the fray, the hot new thing in the world of FBP and visual programming. Let's save that story for another day.

In the next post we'll dig deeper into NoFlo to learn what it is and how to get started with it. In further posts we'll then use NoFlo to begin creating your own applications using FBP and visual programming.

We'll be covering the following and more:

- NoFlo and FBP concepts
- Getting started with NoFlo
- Using NoFlo in the browser
- Performing common tasks using NoFlo (looping, splitting and merging, etc)
- Optimising for NoFlo
- Integrating NoFlo in an existing application
- Creating a custom build of NoFlo
- Creating your own components

[Make sure to subscribe](http://feeds.feedburner.com/rawkes) so you don't miss out!
