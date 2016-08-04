# Theory

[Original URL](http://jnape.com/kolmogorov-complexity/)

> Over the years I've used many various ways to analyze the complexity of software. Some of these have involved analyzing the isolated complexity of discrete constructs (method cohesion,...

Over the years I've used many various ways to analyze the complexity of software. Some of these have involved analyzing the isolated complexity of discrete constructs (method [cohesion](http://www.wikiwand.com/en/Cohesion_(computer_science)), internal/external class [coupling](http://www.wikiwand.com/en/Coupling_(computer_programming)), module [connascence](http://www.wikiwand.com/en/Connascence_(computer_programming))); to uniform complexity and cognitive overhead (branchable logic/[cyclomatic complexity](http://www.wikiwand.com/en/Cyclomatic_complexity), [Halstead complexity](http://www.wikiwand.com/en/Halstead_complexity_measures)); and finally, the highly subjective analysis of the impedence mismatch between the business problem domain and the implemented solution domain (e.g. the business fundamentally only understands the values `1` and `2`, but the implementation handles all 32-bit signed integers and has code handling special cases arising from `13`, `46`, and `-25738459`).

One complexity measurement that's fascinated me over the years is [Kolmogorov complexity](http://www.wikiwand.com/en/Kolmogorov_complexity), or the measurement of the entropy of an algorithm. The measurement is based on the character count of the implementation - the idea being that between two extrinsically identical

<sup>
  <a href="http://jnape.com/kolmogorov-complexity/#fn1" id="ref1">[1]</a>
</sup>

 algorithms, the one that can be expressed - or implemented, as it were - with less characters is fundemantally simpler in the context of the problem domain.

To better understand this, consider a simple program which prints "My name is George and my age is 31" to `stdout`. In Ruby (and many other languages that don't require a lot of ceremony in order to support execution), this is a one-liner:

```
puts 'My name is George and my age is 31' 
```

The character count of this program's source code is 41, and while you could theoretically remove the space between `puts` and the start of the string, it's about as short as you can get in ruby.

Let's look at the same program in Python:

```
print 'My name is George and my age is 31' 
```

The source code of this program consists of 42 characters - so is the Ruby program simpler than the Python program?

<sup>
  <a href="http://jnape.com/kolmogorov-complexity/#fn2" id="ref2">[2]</a>
</sup>

 Clearly the answer is no - which is where the formal description of "complexity" comes in (taken from the wikipedia page on Kolmogorov complexity):

> More formally, the complexity [...] is the length of the shortest possible description [...] in some fixed universal description language

Ah, so when we compare disparate programming languages, we need to use a universal description language to reconcile differences in semantics. Without getting too bogged down here in the discrete implications of a unifying language, let's assume at the very least that this "universal description language" will consider the printing function to be a single additive constant and that it subsumes both `print` and `puts`. In this context, the programs are equally complex. Indeed, even in the face of a language that implicitly prints a supplied string to `stdout`, the lack of explicit function execution still doesn't realistically decrease the complexity of the program, since the printing operation is still mechanically present, albeit implied (in fact, in some ways it could be argued that this would even make the implicit `print` _more complex_, since it's another thing to remember on top of what the source code does - but let's not focus too much on this).

Now let's imagine we wanted to print the following to standard out, newlines included:

```
My name is George and my age is 31\. 
My name is Bethany and my age is 27\. 
My name is Gertrude and my age is 44\. 
```

A naive engineer might implement this the same way as before (examples from here on out in Ruby):

```
puts 'My name is George and my age is 31.' 
puts 'My name is Bethany and my age is 27.' 
puts 'My name is Gertrude and my age is 44.' 
```

Assuming a triple invocation of some universal additive constant value representing `puts` - let's just call it 4 to simplify further source code calculations (so 12 in total) - combined with the respective quote-inclusive lengths of the strings (37, 38, and 39), the total complexity of this implementation might have a score of 126.

However, any diligent engineer will likely have immediately identified a blatant opportunity to shorten this implementation. It's clear that while names and ages are unique, the rest of the text is duplicated. We could therefore refactor this into:

```
[['George', 31], ['Bethany', 27], ['Gertrude', 44]].each { 
 |name, age| puts "My name is #{name} and my age is #{age}."
}
```

Including whitespace, this program's source has a score of 120, and would therefore be considered less complex. What do you think? Does this program seem less complex? While one might argue that understanding this program's source code requires a deeper (albeit modestly so) understanding of the language used for implementation than the first example, a developer familiar with Ruby would likely prefer the second implementation.

Why? Because the second implementation expresses the _intent_ of the program, rather than simply the _output_ - where the intent is, for three people, to print the phrase "My name is [person's name] and my age is [person's age].", followed by a newline.

This is why Komogrov complexity is so interesting to me: for complexity to decrease in Kolmogorov terms, abstractions must emerge to replace constants in previous implementations.

We might have written the second implementation in the following, slightly different way:

```
['George', 31, 'Bethany', 27, 'Gertrude', 44].each_slice(2) {
 |name, age| puts "My name is #{name} and my age is #{age}."
}
```

This amended source code has a Kolmogorov score of 128, and yet added another abstraction (`each_slice`). So what gives?

Two things: first and foremost, the character count rule isn't perfect, which is why the "universal description language" caveat in the formal definition of Kolmogorov complexity is important (in lieu of which, we're stuck with just counting the characters of the valid syntax source); but secondly, and more importantly, the _nature_ of the abstraction we've introduced is a _language_ abstraction, not a _conceptual_ abstraction.

In fact, the only reason to use `each_slice` is to transform the heterogenous array into some homogenous data structure that can support a loopable operation. In this way, it can be argued that introducing `each_slice` is actually harming - or increasing - the implementation complexity of the problem.

Let's test this theory. Even in lieu of some "universal description language" (UDL from here on out), what conceptual characteristics would we expect a UDL to describe about the nature of the program we're trying to write? I can think of three:

1. Given 3 people's names and corresponding ages
2. For each person...
3. ...Print the string "My name is [person's name] and my age is [person's age]" to standard out, followed by a newline.

While some may argue that perhaps separating 1\. and 3\. is unnecessary (or maybe 2\. and 3.), I would be surprised if anyone defended the notion of `each_slice` existing in the problem statement. The rest, however, is illustrated (along with corresponding source code):

1. Given 3 people's names and corresponding ages: `[['George', 31], ['Bethany', 27], ['Gertrude', 44]]`

2. For each person...: `.each { ... }`

3. ...Print the string "My name is [person's name] and my age is [person's age]" to standard out, followed by a newline: `|name, age| puts "My name is #{name} and my age is #{age}."`

The third implementation introduces an abstraction that only acts as an indirection; the first implementation ignores the notions of names, ages, and enumerability entirely. This makes it easier to see how the second implementation is tailored to only represent the concepts outlined by the description of the problem, and as such, is fittingly awarded the lowest Kolmogorov score.

**The danger here, of course, is that an overzealous aim to reduce the Kolmogorov complexity score of a program can lead to an incorrect identification of red-herring abstractions.**

Consider, for example, a regular-expression matcher that validates some unique id of the form `\d\d\d-\d\d\d-\d\d\d\d`. In an attempt to introduce abstractions and therefore reduce the Kolmogorov complexity score of a program, a developer might choose to reuse the same component that validates these ids to also validate phone numbers

<sup>
  <a href="http://jnape.com/kolmogorov-complexity/#fn3" id="ref3">[3]</a>
</sup>

. The unification of these two disparate concepts is erroneous and can lead to trouble down the road: for example, if the structure of unique id's changes, continuing to use the same regex will lead to incorrect formats of phone numbers. Or, at the very least, this could result in confusion and misunderstanding of the rules around unique ids.

It's important to remember that Kolmogorov complexity is just another indicator of intrinsic software quality and not the end-all be-all of software complexity analysis. However, I've personally found it to be particularly motivating and useful when looking for opportunities to simplify implementations - and, in my experience, the scoring comparisons between implementations tends to be eerily in favor of what I consider the simpler implementation to be.

<sup><a href="http://jnape.com/kolmogorov-complexity/#ref1" id="fn1">[1]</a>\ Meaning,\ algorithms\ producing\ the\ same\ result,\ not\ necessarily\ taking\ into\ account\ things\ like\ algorithmic\ efficiency</sup>

<br>

<sup><a href="http://jnape.com/kolmogorov-complexity/#ref2" id="fn2">[2]</a>\ Kolmogorov\ complexity\ measurements\ in\ many\ cases\ are\ closely\ corrolative\ of\ the\ outputs\ from\ <a href="http://www.wikiwand.com/en/Code_golf">code\ golf</a>\ exercises</sup>

<br>

<sup><a href="http://jnape.com/kolmogorov-complexity/#ref3" id="fn3">[3]</a>\ I\ refer\ to\ this\ type\ of\ “duplication”\ as\ incidental\ duplication,\ as\ oppoosed\ to\ essential\ duplication\ or\ conceptual\ duplication.\ The\ <a href="http://www.wikiwand.com/en/Rule_of_three_(computer_programming">Rule\ of\ Three</a>)\ can\ help\ to\ avoid\ mischaracterizing\ incidental\ duplication\ as\ conceptual\ duplication.\ <em>Recursive\ Easter\ Egg:\ this\ is\ not\ to\ be\ confused\ with\ <a href="http://www.wikiwand.com/en/Rule_of_three_(writing">the\ other\ Rule\ of\ Three</a>),\ which\ is\ disconcertingly\ referenced\ in\ this\ 3rd\ of\ three\ footnotes.</em></sup>

<span id="subtitle">The siren song of software complexity</span>
