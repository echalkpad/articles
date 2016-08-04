# Circles Sines and Signals - Introduction

[Original URL](http://jackschaedler.github.io/circles-sines-signals/)

> This text is designed to accompany your study of introductory digital signal processing.1 It's an eccentric piece of not-so-rigorous literature with a preoccupation for explaining things using...

This text is designed to accompany your study of introductory digital signal processing.

<sup>1</sup>

 It's an eccentric piece of not-so-rigorous literature with a preoccupation for explaining things using interactive visualizations, animations and sound.

<sup>2</sup>

 My goal is to explain the Discrete Fourier Transform using a miniature curriculum which leverages your ability to learn concepts and absorb information _visually_ instead of linguistically.

<sup>3</sup>

 My hope is that these glyphs become slightly more comprehensible and slightly less intimidating after reading the subsequent 30 or so pages.

<sup>4</sup>

$$ \mathrm{DFT}[k] = \sum_{n=0}^{N-1} \mathrm{x}[n] \cdot e^{-\varphi\mathrm{i}} \ where \quad \varphi = k \frac{n}{N} 2\pi $$ |
------------------------------------------------------------------------------------------------------------------------------ | -----------------------------------------------------------------
**Equation 1.** The Discrete Fourier Transform                                                                                 | **Figure 1.** Visualizing the Composition<br>
of a Complex Signal

"One reason math texts are so abstruse and technical is because of all the specifications and conditions that have to be put on theorems to keep them out of crevasses. In this sense, they're like legal documents, and often about as much fun to read." **- David Foster Wallace, _Everything and More_

<sup>5</sup>**

If you're really serious about learning the theory and practice of digital signal processing, you should _not_ use this website as your sole resource. Wherever a compromise was necessary, I've erred in favor of simplicity over completeness, fun and engagement over rigor. This text is a piece of pop-science and you should treat it as such.

If you want a solid introduction to digital signal processing, I strongly suggest that you read [Understanding Digital Signal Processing](http://www.amazon.com/Understanding-Digital-Signal-Processing-Edition/dp/0137027419) by Richard J Lyons. Lyons writes with a great deal of empathy for the reader, patiently explicating concepts that other authors might deem to be "obvious". Paolo Prandoni and Martin Vetterli periodically offer [a course](https://youtu.be/Fa-r34Ciwog) on digital signal processing through [Coursera](https://www.coursera.org/course/dsp). Their course is free, and the lectures are really quite lovely. Martin and Paolo have also written [Signal Processing for Communications](http://www.sp4comm.org/), which is freely available online.

<sup>6</sup>

 [Julius O. Smith III](https://ccrma.stanford.edu/~jos/) has an encyclopedic catalog of DSP writings freely available on his [site](https://ccrma.stanford.edu/~jos/). Smith's online text, [The Mathematics of the Discrete Fourier Transform](https://ccrma.stanford.edu/~jos/mdft/) is of particular interest. [Dan Ellis](http://www.ee.columbia.edu/~dpwe/) posts all of his teaching materials online for free and open access. Geoff Martin's delightful [online book](http://www.tonmeister.ca/main/textbook/index.html) is recommended if you're interested in a less formal, but still very thorough resource on mathematics and DSP.

[Contact me](http://www.twitter.com/jackschaedler) on twitter if you have any comments, questions, or complaints about this site. If you encounter an error in the mathematics or a technical issue with the presentation, please file this as an _issue_ on the [github site](https://github.com/jackschaedler/circles-sines-signals).

**1.** Almost always referred to as "DSP", especially if you're into the whole brevity thing. **2.** I've attempted to avoid the gratuitous use of interactivity after reading the section titled [Interactivity Considered Harmful](http://worrydream.com/#!/MagicInk) in Bret Victor's "Magic Ink" essay. If you have strong feelings about interactivity and computer-centric presentations of information, I highly recommend this essay.

**3.** This sentiment and wording might be familiar to you if you've read Bret Victor's [Kill Math](http://worrydream.com/KillMath/). If you haven't already, you might also be interested in reading Evan Miller's [critique of Kill Math](http://www.evanmiller.org/dont-kill-math.html).

**4.** See Seymour Papert's exceptional [Mindstorms](http://books.google.de/books/about/Mindstorms.html?id=AP8WBQAAQBAJ&redir_esc=y). Chapter two is all about "Mathophobia" where he writes, "It is not uncommon for intelligent adults to turn into passive observers of their own incompetence in anything but the most elementary mathematics." If you're a mathophobe (like me), I hope this text feels like a welcome respite.

**5.** I'm mentioning DFW here because he has an interesting take on technical writing. Everything and More wasn't received particularly well by mathematicians (See Michael Harris' review titled, "A Sometimes Funny Book Supposedly About Infinity"), but I think his comments about technical writing are totally relevant.

**6.** Prandoni and Vetterli are engaged in a concerted effort to make learning materials freely available to the community. They should be commended for this effort. If you've ever hit the, ["Academic Paywall"](http://en.wikipedia.org/wiki/Paywall), you should thank people like Prandoni and Vetterli for not only writing, but also actively distributing their high-quality learning material for free. Their writings and lectures also incorporate philosophy, art, and history into the signal processing curriculum. It's education in the best sense.
