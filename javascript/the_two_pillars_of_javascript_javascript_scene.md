# The Two Pillars of JavaScript -- JavaScript Scene

[Original URL](https://medium.com/javascript-scene/the-two-pillars-of-javascript-ee6f3281e7f3#.3hi6gq6d8)

> Before we get into this, allow me to introduce myself -- you're probably going to wonder who I think I am before this is over.I'm Eric Elliott, author of...

Before we get into this, allow me to introduce myself -- you're probably going to wonder who I think I am before this is over.

I'm **Eric Elliott**, author of **"Programming JavaScript Applications"** **(O'Reilly)**, host of the _feature-length documentary film-in-production,_ **"Programming Literacy"**, and creator of the [**"Learn JavaScript with Eric Elliott"**](http://ericelliottjs.com/) _**_ series of online JavaScript courses.

I have contributed to software experiences for **Adobe Systems, Zumba Fitness, The Wall Street Journal, ESPN, BBC,** and top recording artists including **Usher, Frank Ocean, Metallica,** and many more.

I was trapped in the darkness. I was blind -- shuffling about, bumping into things, breaking things, and generally making an unholy mess of everything I touched.

In the 90's, I was programming in C++, Delphi, and Java and writing 3D plugins for the software suite that eventually became Maya (used by lots of major motion picture studios to make summer blockbuster movies).

Then it happened: The internet took off. Everybody started building websites, and after writing and editing a couple online magazines, a friend convinced me that the future of the web would be SaaS products (before the term was coined). I didn't know it then, but that subtle course change transformed the way I think about programming on a fundamental level, because _if you want to make a good SaaS product, you have to learn JavaScript._

Once I learned it, I never looked back. Suddenly, everything was easier. The software I made was more malleable. Code survived longer without being rewritten. Initially, I thought JavaScript was mostly UI scripting glue, but when I learned cookies and AJAX blew up, that transformed, too.

I got addicted, and I couldn't go back. JavaScript offers something other languages lack:

> Freedom!

JavaScript is one of the most important programming languages of all time, not simply because of its popularity, but because it popularized two paradigms which are extremely important for the evolution of programming:

- **Prototypal Inheritance** (objects without classes, and prototype delegation, aka OLOO -- Objects Linking to Other Objects), and

- **Functional Programming** (enabled by lambdas with closure)

Collectively, I like to call these paradigms **the two pillars of JavaScript,** ** and I'm not ashamed to admit that they've spoiled me. I don't want to program in a language without them.

_JavaScript will be remembered as one of the most influential languages ever created._ Lots of other languages have already copied one or the other, or both of the pillars, and the pillars have transformed the way we write applications, _even in other languages._

Brendan Eich didn't invent either of the pillars, but JavaScript exposed the programming masses to them. Both pillars are equally important, but I'm concerned that a large number of JavaScript programmers are completely missing one or both innovations, because JavaScript is pretty good at letting you code poorly if you don't bother to learn it properly.

This is actually a feature, because it makes it really easy to pick up JavaScript and start doing useful things with it, but that phase of your development as a JavaScript programmer _should last no more than a year._

If you haven't yet, **it's time to level up.**

If you're creating constructor functions and inheriting from them, you haven't learned JavaScript. It doesn't matter if you've been doing it since 1995\. You're failing to take advantage of JavaScript's most powerful capabilities.

**_You're working in the phony version of JavaScript that only exists to dress the language up like Java._**

You're coding in this amazing, game-changing, seminal programming language and _completely missing what makes it so cool and interesting._

> "Those who are not aware they are walking in darkness will never seek the light." ~ Bruce Lee

**Constructors violate the open/closed principle** because they couple all callers to the details of how your object gets instantiated. Making an HTML5 game? Want to change from new object instances to use object pools so you can recycle objects and [stop the garbage collector from trashing your frame rate?](https://www.youtube.com/watch?v=RWmzxyMf2cE) Too bad. You'll either break all the callers, or you'll end up with a hobbled factory function.

If you return an arbitrary object from a constructor function, it will break your prototype links, and the _`this`_ keyword will no longer be bound to the new object instance in the constructor. It's also less flexible than a real factory function because you can't use _`this`_ at all in the factory; it just gets thrown away.

Constructors that aren't running in strict mode can be downright dangerous, too. If a caller forgets _`new`_ and you're not using _strict mode_ or _ES6 classes_ _[sigh]_, anything you assign to _`this`_ will pollute the global namespace. That's ugly.

Prior to strict mode, this language glitch caused hard-to-find bugs at two different startups I worked for, during critical growth periods when we didn't have a lot of extra time to chase down hard-to-find bugs.

In JavaScript, **factory functions** are simply constructor functions **_minus_** the **_`new`_ requirement,** **global pollution danger** and **awkward limitations** (including that annoying initial capitalized letter convention).

**JavaScript doesn't need constructor functions** because _any function can return a new object._ With dynamic object extension, object literals and `_Object.create()`,_ we have everything we need -- with none of the mess. And _`this`_ behaves just like it does in any other function. Hurray!

> "Quite frequently I am not so miserable as it would be wise to be." ~ T.H. White

Everyone has heard the boiling frog analogy: If you put a frog in boiling water, it will jump out. If you put the frog in cool water and gradually increase the heat, the frog will boil to death because it doesn't sense the danger. In this story, we are the frogs.

If _constructor_ behavior is the _frying pan_, **_classical inheritance_** isn't _the fire;_ **_it's the fire from Dante's seventh circle of hell._**

> "The problem with object-oriented languages is they've got all this implicit environment that they carry around with them. You wanted a banana but what you got was a gorilla holding the banana and the entire jungle." ~ Joe Armstrong

Classical Inheritance generally lets you inherit only from a single ancestor, forcing you into awkward taxonomies. I say awkward because without fail, _every OO design taxonomy I have ever seen in a large application was eventually wrong._

Say you start with two classes: _Tool_ and _Weapon_. You've already screwed up -- _You can't make the game "Clue."_

_The coupling between a child class and its parent is the tightest form of coupling in OO design._ That's the opposite of reusable, modular code.

Making _small changes_ to a class creates _rippling side-effects_ that break things that should be completely unrelated.

The obvious solution to taxonomy troubles is to go back in time, build up new classes with subtle differences by changing up what inherits from what -- but it's too tightly coupled to properly extract and refactor. You end up duplicating code instead of reusing it. You violate the **DRY** principle (Don't Repeat Yourself).

As a consequence, you keep growing your subtly different jungle of classes, and as you add inheritance levels, your classes get more and more arthritic and brittle. When you find a bug, you don't fix it in one place. _You fix it everywhere._

> "Oops. Missed one." -- Every Classical OO programmer, ever.

This is known as **the duplication by necessity problem** in OO design circles.

[**ES6 classes don't fix any of these problems.**](https://medium.com/p/how-to-fix-the-es6-class-keyword-2d42bb3f4caf) **_* ES6 makes them worse, because these_** bad ideas_**_ will be officially blessed by the spec,* and written about in a thousand books and blog posts.

**The _`class`_ keyword will probably be the most harmful feature in JavaScript.** I have enormous respect for the brilliant and hard-working people who have been involved in the standardization effort, but even brilliant people occasionally do the wrong thing. Try adding .1 + .2 in your browser console, for instance. I still think Brendan Eich has contributed greatly to the web, to programming languages, and to computer science in general.

P.S. Don't use _`super`_ unless you enjoy stepping through the debugger into multiple layers of inheritance abstraction.

These problems have a multiplying effect as your application grows, and eventually, the only solution is to rewrite the application from scratch or scrap it entirely -- sometimes the business just needs to cut its losses.

I have seen this process play out **_again*_**, and _**_again**_*,_ ***job**__after__**job**_*,_ ***project**__after__**project_**_.__Will we ever learn?*

At one company I worked for, _it caused a software release date to slip by an entire year for a rewrite._ I believe in **updates, not rewrites.** At another company I consulted for, _it almost caused the entire company to crash and burn._

> **These problems are not just a matter of taste or style. This choice can make or break your product.**

Large companies can usually chug along like nothing is wrong, but startups can't afford to spin their wheels on problems like these while they're struggling to find their product/market fit on a limited runway.

**_I've never seen any of the problems above in a modern code base that avoids classical inheritance altogether._**

> "Perfection is reached not when there is nothing more to add, but when there is nothing more to subtract." ~ Antoine de Saint-Exupéry

A while back, I was working on a library to demonstrate how to use prototypal inheritance for my book, ["Programming JavaScript Applications"](http://www.amazon.com/gp/product/1491950293?ie=UTF8&camp=213733&creative=393185&creativeASIN=1491950293&linkCode=shr&tag=eejs-20&linkId=ZURITP6JDCUKP6QF) (O'Reilly), when I settled on an interesting idea: a factory function that helps you produce factory functions that you can inherit from and compose together. I called the composable factories "stamps," and the library, "Stampit." The library is very small and simple. I gave a talk about Stampit at the O'Reilly Fluent Conference in 2013 (included here at the end of the article), and wrote a blog post about stamps (see below).

There is a small, but steadily growing community of developers whose coding styles have been transformed by stamps. Stampit is in production use in multiple apps with millions of monthly active users.

![](https://cdn-images-1.medium.com/max/400/1*6432XLMVYjpOCisNaKEBgw.jpeg)

> "I have been using Stampit a lot and really enjoy the power afforded by the simplicity in the separation of concerns between the types of prototypes as well as composable nature of stamps. Classical deep inheritance trees always bothered me, especially in the context of web development where business needs are often a moving target. The above mentioned talk and Stampit have inspired different thinking and allow me to do things that feel like interfaces without the overhead, or inherit private data with privileged methods from multiple sources. My prototypal inheritance is now strong, my objects have become shapeless, formless, like water. Keep up the great work, class sugar has nothing on this." ~ Justin Schroeder, Senior Developer, Wrecking Ball Media Group

Stampit isn't the only alternative, of course. Douglas Crockford doesn't use _`new`_ or _`this`_ at all, instead opting for an entirely functional approach to code reuse.

All his objects are just stateless bags of functions, or data-only objects like associative arrays with no methods. This works well unless you're creating a hundreds of thousands of objects and you need your app to perform smoothly at or near realtime (think game engines, realtime signal processors, etc...). In those situations, delegating calls to methods can save you from a lot of manual memory management.

Other good alternatives include making better use of JavaScript modules as an alternative to inheritance (I recommend npm and ES6 modules with Browserify or WebPack), or simply cloning objects by copying properties from a source object to a new object (e.g. _`Object.assign()`_, _`$.extend()`, `_.extend()`,_ etc...).

The copy mechanism is another form of prototypal inheritance. Sources of clone properties are a specific kind of prototype called **exemplar prototypes,** and cloning an exemplar prototype is known as **concatenative inheritance.**

Even if you follow Douglas Crockford's advice and stop using _`this`,_ you can still do things the prototypal way. Concatenative inheritance is possible because of a feature in JavaScript known as **dynamic object extension:** the ability to add to an object after it has been instantiated.

**You never need classes in JavaScript,** and I have never seen a situation where class is a better approach than the alternatives. If you can think of any, leave a comment, but I've been making that challenge for years now, and _nobody has come up with a good use-case_ -- just flimsy arguments about micro-optimizations or style preferences.

When I tell people that constructors and classical inheritance are bad, they get defensive. _I'm not attacking you. I'm trying to help you._

People get attached to their programming style as if their coding style is how they express themselves. _Nonsense._

> What you make with your code is how you express yourself.

How it's implemented doesn't matter **at all** _unless it's implemented poorly._

> The only thing that matters in software development is that your users love the software.

I can warn you that there's a cliff ahead, but some people don't believe there is danger until they experience it first hand. Don't make that mistake; _the cost can be enormous._ **This is your chance** to learn from the mistakes that countless others have made _again_ and _again_ over the span of _decades._ Entire books have been written about these problems.

The seminal ["Design Patterns" book by the Gang of Four](http://www.amazon.com/gp/product/0201633612?ie=UTF8&camp=213733&creative=393185&creativeASIN=0201633612&linkCode=shr&tag=eejs-20&linkId=5S2XB3C32NLP7IVQ) is built around two foundational principles:

_"Program to an interface, not an implementation,"_ and _"favor object composition over class inheritance."_

Because child classes code to the implementation of the parent class, the second principle follows from the first, but it's useful to spell it out.

> The seminal work on classical OO design is anti-class inheritance.

It contains a whole section of object creational patterns that exist solely to work around the limitations of constructors and class inheritance.

Google _"new considered harmful," "inheritance considered harmful,"_ and _"super is a code smell."_ You'll dig up dozens of articles from blog posts and respected publications like Dr. Dobb's Journal dating back to before JavaScript was invented, all saying much the same thing: _`new`_, brittle classical inheritance taxonomies, and parent-child coupling (e.g. _`super`_) are recipes for disaster.

_Even James Gosling, the creator of Java, admits that Java didn't get objects right._

Want to stick with JavaScript references? [Douglas Crockford](https://www.youtube.com/watch?v=bo36MrBfTk4) got _Object.create()_ added to the language so he wouldn't have to use _`new`_.

Kyle Simpson (author, ["You Don't Know JS"](http://www.amazon.com/s/?_encoding=UTF8&camp=213733&creative=393193&linkCode=shr&tag=eejs-20&linkId=YSQEZLRZIRKYVBNS&rl=search-alias%3Daps&field-keywords=you%20don%27t%20know%20js&sprefix=you+don%27t+know+js%2Caps)) wrote a fascinating three part blog series on the topic called ["JS Objects: Inherited a Mess."](http://davidwalsh.name/javascript-objects)

Kyle argues that prototypal inheritance is anti-class, that's simpler and better than class. He even coined the term **OLOO** (Objects Linked to Other Objects) to clarify the distinction between prototype delegation and class inheritance.

> "Simplicity is about subtracting the obvious and adding the meaningful." ~ John Maeda

- **Gets simpler** (Easier to read and to write. No more wrong design taxonomies.)

- **Gets more flexible** (Switch from new instances to recycling object pools or proxies? No problem.)

- **Gets more powerful & expressive** (Inherit from multiple ancestors? Inherit private state? No problem.)

> "If a feature is sometimes dangerous, and there is a better option, then always use the better option." ~ Douglas Crockford

I'm not trying to take a useful tool away from you. I'm warning you that _what you think is a tool is actually a foot-gun._ In the case of constructors and classes, there are _several better options._

Another common argument that programmers use is that it should be up to them how they express themselves, as if code style rises to the level of art or fashion. This argument is a purely emotional and irrational:

_Your code isn't the product of your self expression any more than a painter's paintbrush is the product of their self expression._ **_Code is the tool. The program is the product._**

Yes, _some code is art in and of itself,_ but if it doesn't stand alone published on paper, **_your code doesn't fall into that category._** _* Otherwise, as far as your users are concerned,_ the code is a black box, and what they enjoy is the program.*

Good programming style requires that when you're presented with a choice that's elegant, simple, and flexible, or another choice that's complex, awkward, and restricting, you choose the former. I know it's popular to be open minded about language features, but _there is a right way and a wrong way._

**Choose the right way.**

_~ Eric Elliott_

P.S. Don't miss [The Two Pillars of JavaScript Part 2: Functional Programming (How to Stop Micromanaging Everything)](http://bit.ly/jspillars2)
