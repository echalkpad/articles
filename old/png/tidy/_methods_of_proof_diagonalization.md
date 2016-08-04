# Methods of Proof -- Diagonalization

[Original URL](http://jeremykun.com/2015/06/08/methods-of-proof-diagonalization/)

> A while back we featured a post about why learning mathematics can be hard for programmers, and I claimed a major issue was not understanding the basic methods of proof (the lingua franca...

A while back we featured a post about [why learning mathematics can be hard for programmers](http://jeremykun.com/2013/02/08/why-there-is-no-hitchhikers-guide-to-mathematics-for-programmers/ "Why there is no Hitchhiker’s Guide to Mathematics for Programmers"), and I claimed a major issue was not understanding the basic methods of proof (the lingua franca between intuition and rigorous mathematics). I boiled these down to the "basic four," [direct implication](http://jeremykun.com/2013/02/16/methods-of-proof-direct-implication/ "Methods of Proof — Direct Implication"), [contrapositive](http://jeremykun.com/2013/02/22/methods-of-proof-contrapositive/ "Methods of Proof — Contrapositive"), [contradiction](http://jeremykun.com/2013/02/28/methods-of-proof-contradiction/ "Methods of Proof — Contradiction"), and [induction](http://jeremykun.com/2013/03/21/methods-of-proof-induction/ "Methods of Proof — Induction"). But in mathematics there is an ever growing supply of proof methods. There are books written about the "[probabilistic method](http://www.amazon.com/gp/product/0470170204/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0470170204&linkCode=as2&tag=mathinterprog-20&linkId=BBL3WJIANJSAYEZ6)," and I recently went to a lecture where the "linear algebra method" was displayed. There has been recent talk of a "quantum method" for proving theorems unrelated to quantum mechanics, and many more.

So in continuing our series of methods of proof, we'll move up to some of the more advanced methods of proof. And in keeping with the spirit of the series, we'll spend most of our time discussing the structural form of the proofs. This time, diagonalization.

## Diagonalization

Perhaps one of the most famous methods of proof after the basic four is proof by diagonalization. Why do they call it diagonalization? Because the idea behind diagonalization is to write out a table that describes how a collection of objects behaves, and then to manipulate the "diagonal" of that table to get a new object that you can prove isn't in the table.

The simplest and most famous example of this is the proof that there is no bijection between the natural numbers and the real numbers. We defined [injections](http://jeremykun.com/2013/02/22/methods-of-proof-contrapositive/), and [surjections and bijections](http://jeremykun.com/2013/02/28/methods-of-proof-contradiction/), in two earlier posts in this series, but for new readers a bijection is just a one-to-one mapping between two collections of things. For example, one can construct a bijection between all positive integers and all _even_ positive integers by mapping 

<embed src="http://s0.wp.com/latex.php?latex=n&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="n" class="latex">

 to 

<embed src="http://s0.wp.com/latex.php?latex=2n&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="2n" class="latex">

. If there is a bijection between two (perhaps infinite) sets, then we say they have the same size or _cardinality_. And so to say there is no bijection between the natural numbers and the real numbers is to say that one of these two sets (the real numbers) is somehow "larger" than the other, despite both being infinite in size. It's deep, it used to be very controversial, and it made the method of diagonalization famous. Let's see how it works.

**Theorem:** There is no bijection from the natural numbers 

<embed src="http://s0.wp.com/latex.php?latex=%5Cmathbb%7BN%7D&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="\mathbb{N}" class="latex">

 to the real numbers 

<embed src="http://s0.wp.com/latex.php?latex=%5Cmathbb%7BR%7D&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="\mathbb{R}" class="latex">

.

_Proof._ Suppose to the contrary (i.e., we're about to do proof by contradiction) that there is a bijection 

<embed src="http://s0.wp.com/latex.php?latex=f%3A+%5Cmathbb%7BN%7D+%5Cto+%5Cmathbb%7BR%7D&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="f: \mathbb{N} \to \mathbb{R}" class="latex">

. That is, you give me a positive integer 

<embed src="http://s0.wp.com/latex.php?latex=k&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="k" class="latex">

 and I will spit out 

<embed src="http://s0.wp.com/latex.php?latex=f%28k%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="f(k)" class="latex">

, with the property that different 

<embed src="http://s0.wp.com/latex.php?latex=k&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="k" class="latex">

 give different 

<embed src="http://s0.wp.com/latex.php?latex=f%28k%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="f(k)" class="latex">

, and every real number is hit by some natural number 

<embed src="http://s0.wp.com/latex.php?latex=k&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="k" class="latex">

 (this is just what it means to be a one-to-one mapping).

First let me just do some setup. I claim that all we need to do is show that there is no bijection between 

<embed src="http://s0.wp.com/latex.php?latex=%5Cmathbb%7BN%7D&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="\mathbb{N}" class="latex">

 and the real numbers _between 0 and 1_. In particular, I claim there is a bijection from 

<embed src="http://s0.wp.com/latex.php?latex=%280%2C1%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="(0,1)" class="latex">

 to all real numbers, so if there is a bijection from 

<embed src="http://s0.wp.com/latex.php?latex=%5Cmathbb%7BN%7D+%5Cto+%280%2C1%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="\mathbb{N} \to (0,1)" class="latex">

 then we could combine the two bijections. To show there is a bijection from 

<embed src="http://s0.wp.com/latex.php?latex=%280%2C1%29+%5Cto+%5Cmathbb%7BR%7D&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="(0,1) \to \mathbb{R}" class="latex">

, I can first make a bijection from the open interval 

<embed src="http://s0.wp.com/latex.php?latex=%280%2C1%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="(0,1)" class="latex">

 to the interval 

<embed src="http://s0.wp.com/latex.php?latex=%28-%5Cinfty%2C+0%29+%5Ccup+%281%2C+%5Cinfty%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="(-\infty, 0) \cup (1, \infty)" class="latex">

 by mapping 

<embed src="http://s0.wp.com/latex.php?latex=x&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="x" class="latex">

 to 

<embed src="http://s0.wp.com/latex.php?latex=1%2Fx&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="1/x" class="latex">

. With a little bit of extra work (read, messy details) you can extend this to all real numbers. Here's a sketch: make a bijection from 

<embed src="http://s0.wp.com/latex.php?latex=%280%2C1%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="(0,1)" class="latex">

 to 

<embed src="http://s0.wp.com/latex.php?latex=%280%2C2%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="(0,2)" class="latex">

 by doubling; then make a bijection from 

<embed src="http://s0.wp.com/latex.php?latex=%280%2C2%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="(0,2)" class="latex">

 to all real numbers by using the 

<embed src="http://s0.wp.com/latex.php?latex=%280%2C1%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="(0,1)" class="latex">

 part to get 

<embed src="http://s0.wp.com/latex.php?latex=%28-%5Cinfty%2C+0%29+%5Ccup+%281%2C+%5Cinfty%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="(-\infty, 0) \cup (1, \infty)" class="latex">

, and use the 

<embed src="http://s0.wp.com/latex.php?latex=%5B1%2C2%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="[1,2)" class="latex">

 part to get 

<embed src="http://s0.wp.com/latex.php?latex=%5B0%2C1%5D&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="[0,1]" class="latex">

 by subtracting 1 (almost! To be super rigorous you also have to argue that the missing number 1 doesn't change the cardinality, or else write down a more complicated bijection; still, the idea should be clear).

Okay, setup is done. We just have to show there is no bijection between 

<embed src="http://s0.wp.com/latex.php?latex=%280%2C1%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="(0,1)" class="latex">

 and the natural numbers.

The reason I did all that setup is so that I can use the fact that every real number in 

<embed src="http://s0.wp.com/latex.php?latex=%280%2C1%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="(0,1)" class="latex">

 has an infinite binary decimal expansion whose only nonzero digits are after the decimal point. And so I'll write down the expansion of 

<embed src="http://s0.wp.com/latex.php?latex=f%281%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="f(1)" class="latex">

 as a row in a table (an infinite row), and below it I'll write down the expansion of 

<embed src="http://s0.wp.com/latex.php?latex=f%282%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="f(2)" class="latex">

, below that 

<embed src="http://s0.wp.com/latex.php?latex=f%283%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="f(3)" class="latex">

, and so on, and the decimal points will line up. The table looks like this.

[![firsttable](https://jeremykun.files.wordpress.com/2014/03/firsttable1.gif?w=1800)](https://jeremykun.files.wordpress.com/2014/03/firsttable1.gif)The 

<embed src="http://s0.wp.com/latex.php?latex=d&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="d" class="latex">

's above are either 0 or 1\. I need to be a bit more detailed in my table, so I'll index the digits of 

<embed src="http://s0.wp.com/latex.php?latex=f%281%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="f(1)" class="latex">

 by 

<embed src="http://s0.wp.com/latex.php?latex=b_%7B1%2C1%7D%2C+b_%7B1%2C2%7D%2C+b_%7B1%2C3%7D%2C+%5Cdots&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="b_{1,1}, b_{1,2}, b_{1,3}, \dots" class="latex">

, the digits of 

<embed src="http://s0.wp.com/latex.php?latex=f%282%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="f(2)" class="latex">

 by 

<embed src="http://s0.wp.com/latex.php?latex=b_%7B2%2C1%7D%2C+b_%7B2%2C2%7D%2C+b_%7B2%2C3%7D%2C+%5Cdots&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="b_{2,1}, b_{2,2}, b_{2,3}, \dots" class="latex">

, and so on. This makes the table look like this

[![secondtable](https://jeremykun.files.wordpress.com/2014/03/secondtable.gif?w=1800)](https://jeremykun.files.wordpress.com/2014/03/secondtable.gif)

It's a bit harder to read, but trust me the notation is helpful.

Now by the assumption that 

<embed src="http://s0.wp.com/latex.php?latex=f&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="f" class="latex">

 is a bijection, I'm assuming that _every_ real number shows up as a number in this table, and no real number shows up twice. So if I could construct a number that I can prove is not in the table, I will arrive at a contradiction: the table couldn't have had all real numbers to begin with! And that will prove there is no bijection between the natural numbers and the real numbers.

Here's how I'll come up with such a number 

<embed src="http://s0.wp.com/latex.php?latex=N&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="N" class="latex">

 (this is the diagonalization part). It starts with 0., and it's first digit after the decimal is 

<embed src="http://s0.wp.com/latex.php?latex=1-b_%7B1%2C1%7D&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="1-b_{1,1}" class="latex">

. That is, we flip the bit 

<embed src="http://s0.wp.com/latex.php?latex=b_%7B1%2C1%7D&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="b_{1,1}" class="latex">

 to get the first digit of 

<embed src="http://s0.wp.com/latex.php?latex=N&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="N" class="latex">

. The second digit is 

<embed src="http://s0.wp.com/latex.php?latex=1-b_%7B2%2C2%7D&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="1-b_{2,2}" class="latex">

, the third is 

<embed src="http://s0.wp.com/latex.php?latex=1-b_%7B3%2C3%7D&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="1-b_{3,3}" class="latex">

, and so on. In general, digit 

<embed src="http://s0.wp.com/latex.php?latex=i&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="i" class="latex">

 is 

<embed src="http://s0.wp.com/latex.php?latex=1-b_%7Bi%2Ci%7D&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="1-b_{i,i}" class="latex">

.

Now we show that 

<embed src="http://s0.wp.com/latex.php?latex=N&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="N" class="latex">

 isn't in the table. If it were, then it would have to be 

<embed src="http://s0.wp.com/latex.php?latex=N+%3D+f%28m%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="N = f(m)" class="latex">

 for some 

<embed src="http://s0.wp.com/latex.php?latex=m&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="m" class="latex">

, i.e. be the 

<embed src="http://s0.wp.com/latex.php?latex=m&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="m" class="latex">

-th row in the table. Moreover, by the way we built the table, the 

<embed src="http://s0.wp.com/latex.php?latex=m&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="m" class="latex">

-th digit of 

<embed src="http://s0.wp.com/latex.php?latex=N&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="N" class="latex">

 would be 

<embed src="http://s0.wp.com/latex.php?latex=b_%7Bm%2Cm%7D&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="b_{m,m}" class="latex">

. But we _defined_

<embed src="http://s0.wp.com/latex.php?latex=N&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="N" class="latex">

 so that it's 

<embed src="http://s0.wp.com/latex.php?latex=m&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="m" class="latex">

-th digit was actually 

<embed src="http://s0.wp.com/latex.php?latex=1-b_%7Bm%2Cm%7D&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="1-b_{m,m}" class="latex">

. This is very embarrassing for 

<embed src="http://s0.wp.com/latex.php?latex=N&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="N" class="latex">

 (it's a contradiction!). So 

<embed src="http://s0.wp.com/latex.php?latex=N&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="N" class="latex">

 isn't in the table.

<embed src="http://s0.wp.com/latex.php?latex=%5Csquare&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="\square" class="latex">

It's the kind of proof that blows your mind the first time you see it, because it says that _there is more than one kind of infinity_. Not something you think about every day, right?

## The Halting Problem

The second example we'll show of a proof by diagonalization is the Halting Theorem, proved originally by Alan Turing, which says that there are some problems that computers can't solve, even if given unbounded space and time to perform their computations. The formal mathematical model is called a Turing machine, but for simplicity you can think of "Turing machines" and "algorithms described in words" as the same thing. Or if you want it can be "programs written in programming language X." So we'll use the three words "Turing machine," "algorithm," and "program" interchangeably.

The proof works by actually defining a problem and proving it can't be solved. The problem is called _the halting problem_, and it is the problem of deciding: given a program 

<embed src="http://s0.wp.com/latex.php?latex=P&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="P" class="latex">

 and an input 

<embed src="http://s0.wp.com/latex.php?latex=x&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="x" class="latex">

 to that program, will 

<embed src="http://s0.wp.com/latex.php?latex=P&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="P" class="latex">

 ever stop running when given 

<embed src="http://s0.wp.com/latex.php?latex=x&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="x" class="latex">

 as input? What I mean by "decide" is that any program that claims to solve the halting problem is itself required to halt for every possible input with the correct answer. A "halting problem solver" can't loop infinitely!

So first we'll give the standard proof that the halting problem can't be solved, and then we'll inspect the form of the proof more closely to see why it's considered a diagonalization argument.

**Theorem:** The halting program cannot be solved by Turing machines.

_Proof._ Suppose to the contrary that 

<embed src="http://s0.wp.com/latex.php?latex=T&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="T" class="latex">

 is a program that solves the halting problem. We'll use 

<embed src="http://s0.wp.com/latex.php?latex=T&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="T" class="latex">

 as a black box to come up with a new program I'll call meta-

<embed src="http://s0.wp.com/latex.php?latex=T&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="T" class="latex">

, defined in pseudo-python as follows.

```
def metaT(P):
 run T on (P,P)
 if T says that P halts:
 loop infinitely
 else:
 halt and output "success!"
```

In words, meta-

<embed src="http://s0.wp.com/latex.php?latex=T&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="T" class="latex">

 accepts as input the source code of a program 

<embed src="http://s0.wp.com/latex.php?latex=P&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="P" class="latex">

, and then uses 

<embed src="http://s0.wp.com/latex.php?latex=T&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="T" class="latex">

 to tell if 

<embed src="http://s0.wp.com/latex.php?latex=P&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="P" class="latex">

 halts (when given its own source code as input). Based on the result, it behaves the _opposite_ of 

<embed src="http://s0.wp.com/latex.php?latex=P&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="P" class="latex">

; if 

<embed src="http://s0.wp.com/latex.php?latex=P&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="P" class="latex">

 halts then meta-

<embed src="http://s0.wp.com/latex.php?latex=T&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="T" class="latex">

 loops infinitely and vice versa. It's a little meta, right?

Now let's do something crazy: let's run meta-

<embed src="http://s0.wp.com/latex.php?latex=T&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="T" class="latex">

 on itself! That is, run

```
metaT(metaT)
```

So meta. The question is what is the output of this call? The meta-

<embed src="http://s0.wp.com/latex.php?latex=T&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="T" class="latex">

 program uses 

<embed src="http://s0.wp.com/latex.php?latex=T&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="T" class="latex">

 to determine whether meta-

<embed src="http://s0.wp.com/latex.php?latex=T&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="T" class="latex">

 halts when given itself as input. So let's say that the answer to this question is "yes, it does halt." Then by the definition of meta-

<embed src="http://s0.wp.com/latex.php?latex=T&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="T" class="latex">

, the program proceeds to loop forever. But this is a problem, because it means that `metaT(metaT)` (which is the original thing we ran) actually does not halt, contradicting 

<embed src="http://s0.wp.com/latex.php?latex=T&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="T" class="latex">

's answer! Likewise, if 

<embed src="http://s0.wp.com/latex.php?latex=T&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="T" class="latex">

 says that `metaT(metaT)` should loop infinitely, that will cause meta-

<embed src="http://s0.wp.com/latex.php?latex=T&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="T" class="latex">

 to halt, a contradiction. So 

<embed src="http://s0.wp.com/latex.php?latex=T&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="T" class="latex">

 cannot be correct, and the halting problem can't be solved.

<embed src="http://s0.wp.com/latex.php?latex=%5Csquare&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="\square" class="latex">

This theorem is deep because it says that you can't possibly write a program to which can always detect bugs in other programs. Infinite loops are just one special kind of bug.

But let's take a closer look and see why this is a proof by diagonalization. The first thing we need to convince ourselves is that the set of all programs is countable (that is, there is a bijection from 

<embed src="http://s0.wp.com/latex.php?latex=%5Cmathbb%7BN%7D&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="\mathbb{N}" class="latex">

 to the set of all programs). This shouldn't be so hard to see: you can list all programs in [lexicographic](http://en.wikipedia.org/wiki/Lexicographical_order) order, since the set of all strings is countable, and then throw out any that are not syntactically valid programs. Likewise, the set of all inputs, really just all strings, is countable.

The second thing we need to convince ourselves of is that a _problem_ corresponds to an infinite binary string. To do this, we'll restrict our attention to problems with yes/no answers, that is where the goal of the program is to output a single bit corresponding to yes or no for a given input. Then if we list all possible inputs in increasing lexicographic order, a problem can be represented by the infinite list of bits that are the correct outputs to each input.

For example, if the problem is to determine whether a given binary input string corresponds to an even number, the representation might look like this:

`010101010101010101...`

Of course this all depends on the details of how one encodes inputs, but the point is that if you wanted to you could nail all this down precisely. More importantly for us we can represent the halting problem as an infinite _table_ of bits. If the columns of the table are all programs (in lex order), and the rows of the table correspond to inputs (in lex order), then the table would have at entry 

<embed src="http://s0.wp.com/latex.php?latex=%28x%2CP%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="(x,P)" class="latex">

 a 1 if 

<embed src="http://s0.wp.com/latex.php?latex=P%28x%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="P(x)" class="latex">

 halts and a 0 otherwise.

[](https://jeremykun.files.wordpress.com/2014/03/secondtable.gif) [![haltingtable](https://jeremykun.files.wordpress.com/2015/06/haltingtable.gif?w=300&h=195)](https://jeremykun.files.wordpress.com/2015/06/haltingtable.gif)

here 

<embed src="http://s0.wp.com/latex.php?latex=b_%7Bi%2Cj%7D&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="b_{i,j}" class="latex">

 is 1 if 

<embed src="http://s0.wp.com/latex.php?latex=P_j%28x_i%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="P_j(x_i)" class="latex">

 halts and 0 otherwise. The table encodes the answers to the halting problem for all possible inputs.

Now we assume for contradiction sake that some program solves the halting problem, i.e. that every entry of the table is computable. Now we'll construct the answers output by meta-

<embed src="http://s0.wp.com/latex.php?latex=T&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="T" class="latex">

 by flipping each bit of the diagonal of the table. The point is that meta-

<embed src="http://s0.wp.com/latex.php?latex=T&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="T" class="latex">

 corresponds to some _row_ of the table, because there is some input string that is interpreted as the source code of meta-

<embed src="http://s0.wp.com/latex.php?latex=T&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="T" class="latex">

. Then we argue that the entry of the table for 

<embed src="http://s0.wp.com/latex.php?latex=%28%5Ctextup%7Bmeta-%7DT%2C+%5Ctextup%7Bmeta-%7DT%29&amp;bg=ffffff&amp;fg=36312d&amp;s=0" title="(\textup{meta-}T, \textup{meta-}T)" class="latex">

 contradicts its definition, and we're done!

So these are two of the most high-profile uses of the method of diagonalization. It's a great tool for your proving repertoire.

Until next time!
