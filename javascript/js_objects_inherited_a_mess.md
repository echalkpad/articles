# JS Objects: Inherited a Mess

[Original URL](https://davidwalsh.name/javascript-objects)

> JS Objects: TL;DR JavaScript has been plagued since the beginning with misunderstanding and awkwardness around its "prototypal inheritance" system, mostly due to the fact that "inheritance" isn't how...

## JS Objects: TL;DR

JavaScript has been plagued since the beginning with misunderstanding and awkwardness around its "prototypal inheritance" system, mostly due to the fact that "inheritance" isn't how JS works at all, and trying to do that only leads to gotchas and confusions that we have to pave over with user-land helper libs. Instead, embracing that JS has "behavior delegation" (merely delegation links between objects) fits naturally with how JS syntax works, which creates more sensible code without the need of helpers.

When you set aside distractions like mixins, polymorphism, composition, classes, constructors, and instances, and only focus on the objects that link to each other, you gain a powerful tool in behavior delegation that is easier to write, reason about, explain, and code-maintain. Simpler is better. JS is "objects-only" (OO). Leave the classes to those other languages!

## Due Thanks

I'd like to thank the following amazing devs for their generous time in feedback/tech review of this article series: David Bruant, Hugh Wood, Mark Trostler, and Mark McDonnell. I am also honored that David Walsh wanted to publish these articles on his fantastic blog.

## Complete Series

> year 2013: Haskell people are still writing monad tutorials, JavaScript people are still trying to explain inheritance.

> -- Vyacheslav Egorov (@mraleph) [April 12, 2013](https://twitter.com/mraleph/status/322843655381590016)

As sad a criticism on JS as that quote is, it's quite true. (I have no perspective on Haskell or Monads, so I'm only talking about the JS and inheritance part). Of all the confusing and, depending on your biases, "bad", parts of this JS language, the behaviors of `this` and the `[[Prototype]]` chain have remained some of the most elusive to explain and use accurately.

As a bit of background, I've been developing JS full time since 2007\. The first major epiphany I had back then was the understanding of how closures work, and how they enable the classic module pattern. The first open-source project I wrote (early 2008) was [flXHR](https://github.com/flensed/flXHR), a cross-domain Ajax [prollyfill](https://twitter.com/SlexAxton/status/257543702124306432) using the standard Ajax (XHR) interface (via a hidden flash element) that relied heavily upon the module pattern.

It's quite possibly my "ah-ha!" moment around the module pattern that satisfied me enough that I never really felt a strong need to also apply the "inheritance" pattern to my JS design.

Nevertheless, like most JS developers, I've read lots of blogs and books over the years that have tried (and mostly failed) to explain the appeal and mystery that is "JavaScript inheritance" (aka, "prototypal inheritance").

But if it's so hard to understand, and even harder to actually do correctly, the _point_ yet eludes me. And apparently I'm not alone in that frustration.

## OO in JavaScript

In traditional [Object-oriented languages](http://en.wikipedia.org/wiki/Object-oriented_programming), the [syntax of classes](http://www.cs.bu.edu/teaching/cpp/inheritance/intro/) matches the semantics. You can express the object-oriented concepts of classes, inheritance, and polymorphism directly and explicitly using the language's syntax. There's no need to use some helper library to fake your way into OO-like behavior through work-arounds of other language facilities.

JavaScript on the other hand has [a set of syntax that looks somewhat OO](https://developer.mozilla.org/en-US/docs/JavaScript/Introduction_to_Object-Oriented_JavaScript), but which behaves in frustratingly different ways (which we will cover throughout this article series). As a result, the common way that you implement OO patterns in JS is through any of a variety of user-land helper libraries which let you express the desired semantic relationships between your "objects". The reason most JS developers use them is because the underlying JS syntax makes those semantic expressions awkward. It's nice to just let a library handle paving over the confusing syntax hiccups.

Libraries like jQuery are useful because they hide the ugly details of dealing with **cross-browser differences in JS engines**. But these OO-helper libraries are different: they're going to great lengths to **hide the true nature of JavaScript's OO mechanisms**, instead masking them in a set of patterns that are more familiar to other languages.

At this point of understanding, we should really ask ourselves: is the difficulty of [expressing classes and inheritance in pure JavaScript](http://javascript.crockford.com/inheritance.html) a failure of the language (one which can [temporarily](http://prototypejs.org/learn/class-inheritance) be [solved](http://mootools.net/docs/core/Class/Class) with [user libraries](http://ejohn.org/blog/simple-javascript-inheritance/)and ultimately solved by [additions to the language like `class { .. }`](http://wiki.ecmascript.org/doku.php?id=strawman:maximally_minimal_classes) syntax), [as many](https://c9.io/site/blog/2011/11/the-time-has-come-to-add-classes-to-javascript/) devs [feel](http://www.nczonline.net/blog/2012/10/16/does-javascript-need-classes/), or is it something deeper? Is it indicative of a more fundamental disparity, that we're trying to [do something in JS](http://www.kirupa.com/html5/objects_classes_javascript.htm) that it's[just not meant to do](http://webreflection.blogspot.com/2010/01/better-javascript-classes.html)?

[Not everyone](http://www.2ality.com/2011/11/javascript-classes.html) drank the JS classes kool-aid, so the rest of this article series will favor a different perspective.

## Blueprint

One of the most common metaphors used in [traditional class/inheritance OO](http://www.tutorialspoint.com/cplusplus/cpp_inheritance.htm) is that the [class represents a "blueprint"](http://msdn.microsoft.com/en-us/library/ee5edha0%28v=vs.80%29.aspx) for a house to be built, but once you instantiate that class, you are basically copying all the characteristics from the blueprint into the actual built house. This metaphor partially matches, to an extent, what actually happens at a language level when the code is compiled, in that it sort-of flattens the definition of a class (sans "virtual" methods) inheritance hierarchy into the instance.

Of course, a main pillar of [inheritance-oriented coding is overriding and polymorphism](http://www.alexonlinux.com/how-inheritance-encapsulation-and-polymorphism-work-in-cpp), which allows an object to_automatically_ access the most descendant definition for a method, but also to use `super`-style relative references to access ancestor (aka "virtual") versions of the _same-named_ method. In those cases, the compiler maintains lookup tables for the virtual methods, but it flattens out the non-virtual parts of the class/inheritance definition. The compiler can determine a lot about what needs to be preserved and not and highly optimize the definition structure it creates in the compiled code.

For our purposes, we can think of traditional class-inheritance as basically a flattening "copy" of behavior down the chain to the instance. Here's a diagram to illustrate the inheritance relationship between a parent/base class`Foo`, and child class `Bar`, and then instances of each, respectively named `foo1`, `foo2`, `bar1`, and`bar2`. Visually, the arrows (aka, "copying") point from left-to-right and top-to-bottom:

[![Inheritance Arrows](https://davidwalsh.name/demo/InheritanceArrows.png)](https://davidwalsh.name/demo/InheritanceArrows.png)

## What's in a name?

Despite the borrowed implications of the common name "prototypal inheritance", JavaScript's mechanism works quite differently, which we'll see in just a moment.

Both [definitionally](http://dictionary.reference.com/browse/inheritance?s=t) ("...characteristics transmitted from parent to offspring") and behaviorally (as described above), "inheritance" is most closely associated with the idea of "copying" from parent to child.

When you then take "inheritance" and apply it to a mechanism which has some very different behavior, you are asking for the confusion which has plagued "JavaScript inheritance" [documentation](https://developer.mozilla.org/en-US/docs/JavaScript/Introduction_to_Object-Oriented_JavaScript), [education](http://blog.rjzaworski.com/2013/03/composition-in-javascript/), and [usage](http://tobyho.com/2011/11/11/js-object-inheritance/) for nearly 2 decades.

To try to wade through this mess, let's set aside the label "inheritance" and its implications for JS, and hopefully we can arrive at something that is both conceptually more accurate and functionally more useful.

## A.B.D's: Always Be Delegating

JavaScript's OO-like property mechanism for [objects](http://www.ecma-international.org/ecma-262/5.1/#sec-8.6) is notated by `[[Prototype]]`, which is the internal characteristic of **any object** called its prototype-chain – a special link to another object. It's kind of like a scope mechanism, in that the `[[Prototype]]` linkage [describes which alternate object should be referred to](http://www.ecma-international.org/ecma-262/5.1/#sec-8.12.2) if you request a property or method on your object which doesn't exist.

In other words, you're indicating an object to **delegate** behavior to if that behavior isn't defined on the object in question.

The above class-oriented `Foo` and `Bar` example, expressed in JS, relates object `Bar.prototype` to`Foo.prototype`, and then the `foo1`, `foo2`, `bar1` and `bar2` objects to their respective `[[Prototype]]`s. The arrows (not copies but live-links) point in a right-to-left, bottom-to-top fashion in JS:

[![Delegation Arrows](https://davidwalsh.name/demo/DelegationArrows.png)](https://dl.dropboxusercontent.com/u/2474669/jsdiagrams/DelegationArrows.png)

"Behavior delegation" is a more accurate term to describe JavaScript's `[[Prototype]]`. This is not just a matter of word semantics, it's a fundamentally different type of functionality.

If you try to illustrate behavior delegation in terms of the "blueprint" metaphor, you quickly see how it totally breaks down. There's no way that my home, lacking a guest bedroom, could simply refer to another house, or to the original blueprints, to provide a bedroom for my mother-in-law when she comes to visit. Though the outcomes you can achieve have **_some_** respective similarities, the concepts of "inheritance" and "behavior delegation" _are quite different_.

Some devs insist that "delegation" is just the dynamic version of "inheritance", like two sides of the same coin, but I see them **as orthagonal systems**.

### How to delegate?

We'll revisit this later in the article series, but [`Object.create(..)` was added to ES5](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Object/create) to assist with creating an object and then optionally linking its `[[Prototype]]` to another object. The link that is created is a delegation link, as opposed to an inheritance-by-copy.

**Note:** Once an object has its `[[Prototype]]` chain set at its creation, it _should_ for the most part be considered set in stone and not changeable. Technically, browsers which support [the `__proto__` property](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Object/proto), a public representation of the internal link, allow you to change at any time where an object is linked to. However, this practice is littered with landmines and generally frowned upon – it's almost certainly something you'd want to**avoid** in your code.

[]()

## Spade a Spade

You've seen how the mechanisms in JavaScript are comparitively different from the mechanisms in other languages. But is it ok to just hand-waive over these differences so we can keep using the term "inheritance" for JS?

The fact is, it's just **not** an accurate usage of the term. By insisting that JavaScript has "inheritance", we're really saying that the meaning of the word "inheritance" doesn't matter, or is rather soft.

JS doesn't statically analyze what parts of an inheritance chain it can safely flatten out **and copy**, it maintains links to the entire delegation chain throughout runtime, **as distinct objects**, which means our code can take advantage of [a variety of powerful "late binding" dynamic patterns](http://michaux.ca/articles/early-mixins-late-mixins).

If we keep trying to mimic inheritance in JavaScript (syntax hurdles be damned), we get _distracted_ and **miss out on _all that power_ that was built into our language from the start**.

I say: let's call it what it is, and stop trying to pile on JavaScript these other concepts that the "inheritance" label implies.

## So What?

So far, I've tried to identify some misconceptions about JS's `[[Prototype]]` mechanism and how "inheritance" is not a helpful label.

You may still be skeptical why it _actually_ matters what we call this OO-like mechanism in JS? In the [next part](https://davidwalsh.name/javascript-objects-distractions) of the article series, I'm going to address many of the trappings of traditional "class-based" programming which I think are distractions that lead us to missing out on the essence of how JS objects interoperate. In fact, we could even say that [classes/inheritance are a premature optimization](http://www.infoq.com/presentations/Classes-Are-Premature-Optimization) for JavaScript.

Clearing those distractions out of the way leads us to [part 3](https://davidwalsh.name/javascript-objects-deconstruction), where we'll see a simpler and more robust pattern for our JS code, and more importantly, **our code will actually match our semantics** _without_ us having to jump through hoops to hide the ugly mismatches.

Look forward to [parts 2](https://davidwalsh.name/javascript-objects-distractions) and 3 later this week!

[](https://dwf.tw/raygun)
