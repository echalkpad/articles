# Turing machine

[Original URL](https://en.wikipedia.org/wiki/Turing_machine)

> This article is about the symbol manipulator. For the deciphering machine, see Bombe. For the test of artificial intelligence, see Turing test. For the instrumental rock band, see Turing Machine...

This article is about the symbol manipulator. For the deciphering machine, see [Bombe](https://en.wikipedia.org/wiki/Bombe "Bombe"). For the test of artificial intelligence, see [Turing test](https://en.wikipedia.org/wiki/Turing_test "Turing test"). For the instrumental rock band, see [Turing Machine (band)](https://en.wikipedia.org/wiki/Turing_Machine_(band) "Turing Machine (band)").

![Automata theory.svg](https://upload.wikimedia.org/wikipedia/commons/thumb/a/a2/Automata_theory.svg/300px-Automata_theory.svg.png) [![About this image](https://en.wikipedia.org/w/extensions/ImageMap/desc-20.png)](https://en.wikipedia.org/wiki/File:Automata_theory.svg "About this image")

A **Turing machine** is an [abstract machine](https://en.wikipedia.org/wiki/Abstract_machine "Abstract machine")

<sup>
  <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_note-1"><span>[</span>1<span>]</span></a>
</sup>

 that manipulates symbols on a strip of tape according to a table of rules; to be more exact, it is a mathematical model that defines such a device.

<sup>
  <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_note-2"><span>[</span>2<span>]</span></a>
</sup>

 Despite the model's simplicity, given any [computer algorithm](https://en.wikipedia.org/wiki/Computer_algorithm "Computer algorithm"), a Turing machine can be constructed that is capable of simulating that algorithm's logic.

<sup>
  <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_note-3"><span>[</span>3<span>]</span></a>
</sup>

The machine operates on an infinite

<sup>
  <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_note-4"><span>[</span>4<span>]</span></a>
</sup>

 memory tape divided into _cells_.

<sup>
  <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_note-5"><span>[</span>5<span>]</span></a>
</sup>

 The machine positions its _head_ over a cell and "reads" (scans

<sup>
  <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_note-6"><span>[</span>6<span>]</span></a>
</sup>

) the symbol there. Then per the symbol and its present place in a _finite table_

<sup>
  <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_note-7"><span>[</span>7<span>]</span></a>
</sup>

 of user-specified instructions the machine (i) writes a symbol (e.g. a digit or a letter from a finite alphabet) in the cell (some models allowing symbol erasure

<sup>
  <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_note-8"><span>[</span>8<span>]</span></a>
</sup>

 and/or no writing), then (ii) either moves the tape one cell left or right (some models allow no motion, some models move the head),

<sup>
  <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_note-9"><span>[</span>9<span>]</span></a>
</sup>

 then (iii) (as determined by the observed symbol and the machine's place in the table) either proceeds to a subsequent instruction or halts

<sup>
  <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_note-10"><span>[</span>10<span>]</span></a>
</sup>

 the computation.

The Turing machine was invented in 1936 by [Alan Turing](https://en.wikipedia.org/wiki/Alan_Turing "Alan Turing"),

<sup>
  <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_note-Hodges-2012-11"><span>[</span>11<span>]</span></a>
  <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_note-12"><span>[</span>12<span>]</span></a>
</sup>

 who called it an _a-machine_ (automatic machine).

<sup>
  <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_note-13"><span>[</span>13<span>]</span></a>
</sup>

 With this model Turing was able to answer two questions in the negative: (1) Does a machine exist that can determine whether any arbitrary machine on its tape is "circular" (e.g. freezes, or fails to continue its computational task); similarly, (2) does a machine exist that can determine whether any arbitrary machine on its tape ever prints a given symbol.

<sup>
  <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_note-14"><span>[</span>14<span>]</span></a>
</sup>

 Thus by providing a mathematical description of a very simple device capable of arbitrary computations, he was able to prove properties of computation in general - and in particular, the un[computability](https://en.wikipedia.org/wiki/Computability "Computability") of the [Hilbert Entscheidungsproblem](https://en.wikipedia.org/wiki/Entscheidungsproblem "Entscheidungsproblem") ("decision problem").

<sup>
  <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_note-15"><span>[</span>15<span>]</span></a>
</sup>

Thus, Turing machines prove fundamental limitations on the power of mechanical computation.

<sup>
  <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_note-16"><span>[</span>16<span>]</span></a>
</sup>

 While they can express arbitrary computations, their minimalistic design makes them unsuitable for computation in practice: actual [computers](https://en.wikipedia.org/wiki/Computer "Computer") are based on different designs that, unlike Turing machines, use [random access memory](https://en.wikipedia.org/wiki/Random_access_memory "Random access memory").

[Turing completeness](https://en.wikipedia.org/wiki/Turing_completeness "Turing completeness") is the ability for a system of instructions to simulate a Turing machine. A programming language that is Turing complete is theoretically capable of expressing all tasks accomplishable by computers; nearly all programming languages are Turing complete.

## Contents

- [<span class="tocnumber">1</span>

   

  <span class="toctext">Overview</span>](https://en.wikipedia.org/wiki/Turing_machine#Overview)

  - [<span class="tocnumber">1.1</span>

     

    <span class="toctext">Physical description</span>](https://en.wikipedia.org/wiki/Turing_machine#Physical_description)

- [<span class="tocnumber">2</span>

   

  <span class="toctext">Informal description</span>](https://en.wikipedia.org/wiki/Turing_machine#Informal_description)

- [<span class="tocnumber">3</span>

   

  <span class="toctext">Formal definition</span>](https://en.wikipedia.org/wiki/Turing_machine#Formal_definition)

- [<span class="tocnumber">4</span>

   

  <span class="toctext">Additional details required to visualize or implement Turing machines</span>](https://en.wikipedia.org/wiki/Turing_machine#Additional_details_required_to_visualize_or_implement_Turing_machines)

  - [<span class="tocnumber">4.1</span>

     

    <span class="toctext">Alternative definitions</span>](https://en.wikipedia.org/wiki/Turing_machine#Alternative_definitions)

  - [<span class="tocnumber">4.2</span>

     

    <span class="toctext">The “state”</span>](https://en.wikipedia.org/wiki/Turing_machine#The_.22state.22)

  - [<span class="tocnumber">4.3</span>

     

    <span class="toctext">Turing machine “state” diagrams</span>](https://en.wikipedia.org/wiki/Turing_machine#Turing_machine_.22state.22_diagrams)

- [<span class="tocnumber">5</span>

   

  <span class="toctext">Models equivalent to the Turing machine model</span>](https://en.wikipedia.org/wiki/Turing_machine#Models_equivalent_to_the_Turing_machine_model)

- [<span class="tocnumber">6</span>

   

  <span class="toctext">Choice c-machines, Oracle o-machines</span>](https://en.wikipedia.org/wiki/Turing_machine#Choice_c-machines.2C_Oracle_o-machines)

- [<span class="tocnumber">7</span>

   

  <span class="toctext">Universal Turing machines</span>](https://en.wikipedia.org/wiki/Turing_machine#Universal_Turing_machines)

- [<span class="tocnumber">8</span>

   

  <span class="toctext">Comparison with real machines</span>](https://en.wikipedia.org/wiki/Turing_machine#Comparison_with_real_machines)

  - [<span class="tocnumber">8.1</span>

     

    <span class="toctext">Limitations of Turing machines</span>](https://en.wikipedia.org/wiki/Turing_machine#Limitations_of_Turing_machines)

    - [<span class="tocnumber">8.1.1</span>

       

      <span class="toctext">Computational complexity theory</span>](https://en.wikipedia.org/wiki/Turing_machine#Computational_complexity_theory)

    - [<span class="tocnumber">8.1.2</span>

       

      <span class="toctext">Concurrency</span>](https://en.wikipedia.org/wiki/Turing_machine#Concurrency)

- [<span class="tocnumber">9</span>

   

  <span class="toctext">History</span>](https://en.wikipedia.org/wiki/Turing_machine#History)

  - [<span class="tocnumber">9.1</span>

     

    <span class="toctext">Historical background: computational machinery</span>](https://en.wikipedia.org/wiki/Turing_machine#Historical_background:_computational_machinery)

  - [<span class="tocnumber">9.2</span>

     

    <span class="toctext">The Entscheidungsproblem (the “decision problem”): Hilbert’s tenth question of 1900</span>](https://en.wikipedia.org/wiki/Turing_machine#The_Entscheidungsproblem_.28the_.22decision_problem.22.29:_Hilbert.27s_tenth_question_of_1900)

  - [<span class="tocnumber">9.3</span>

     

    <span class="toctext">Alan Turing’s a- (automatic-)machine</span>](https://en.wikipedia.org/wiki/Turing_machine#Alan_Turing.27s_a-_.28automatic-.29machine)

  - [<span class="tocnumber">9.4</span>

     

    <span class="toctext">1937–1970: The “digital computer”, the birth of “computer science”</span>](https://en.wikipedia.org/wiki/Turing_machine#1937.E2.80.931970:_The_.22digital_computer.22.2C_the_birth_of_.22computer_science.22)

  - [<span class="tocnumber">9.5</span>

     

    <span class="toctext">1970–present: the Turing machine as a model of computation</span>](https://en.wikipedia.org/wiki/Turing_machine#1970.E2.80.93present:_the_Turing_machine_as_a_model_of_computation)

- [<span class="tocnumber">10</span>

   

  <span class="toctext">See also</span>](https://en.wikipedia.org/wiki/Turing_machine#See_also)

- [<span class="tocnumber">11</span>

   

  <span class="toctext">Notes</span>](https://en.wikipedia.org/wiki/Turing_machine#Notes)

- [<span class="tocnumber">12</span>

   

  <span class="toctext">References</span>](https://en.wikipedia.org/wiki/Turing_machine#References)

  - [<span class="tocnumber">12.1</span>

     

    <span class="toctext">Primary literature, reprints, and compilations</span>](https://en.wikipedia.org/wiki/Turing_machine#Primary_literature.2C_reprints.2C_and_compilations)

  - [<span class="tocnumber">12.2</span>

     

    <span class="toctext">Computability theory</span>](https://en.wikipedia.org/wiki/Turing_machine#Computability_theory)

  - [<span class="tocnumber">12.3</span>

     

    <span class="toctext">Church’s thesis</span>](https://en.wikipedia.org/wiki/Turing_machine#Church.27s_thesis)

  - [<span class="tocnumber">12.4</span>

     

    <span class="toctext">Small Turing machines</span>](https://en.wikipedia.org/wiki/Turing_machine#Small_Turing_machines)

  - [<span class="tocnumber">12.5</span>

     

    <span class="toctext">Other</span>](https://en.wikipedia.org/wiki/Turing_machine#Other)

- [<span class="tocnumber">13</span>

   

  <span class="toctext">External links</span>](https://en.wikipedia.org/wiki/Turing_machine#External_links)

## <span id="Overview" class="mw-headline">Overview</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=1" title="Edit section: Overview">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

A Turing machine is a general example of a [CPU](https://en.wikipedia.org/wiki/CPU "CPU") that controls all data manipulation done by a computer, with the canonical machine using sequential memory to store data. More specifically, it is a machine ([automaton](https://en.wikipedia.org/wiki/Automaton "Automaton")) capable of [enumerating](https://en.wikipedia.org/wiki/Enumeration "Enumeration") some arbitrary subset of valid strings of an [alphabet](https://en.wikipedia.org/wiki/Alphabet_(formal_languages) "Alphabet (formal languages)"); these strings are part of a [recursively enumerable set](https://en.wikipedia.org/wiki/Recursively_enumerable_set "Recursively enumerable set").

Assuming a [black box](https://en.wikipedia.org/wiki/Black_box "Black box"), the Turing machine cannot know whether it will eventually enumerate any one specific string of the subset with a given program. This is due to the fact that the [halting problem](https://en.wikipedia.org/wiki/Halting_problem "Halting problem") is unsolvable, which has major implications for the theoretical limits of computing.

The Turing machine is capable of processing an [unrestricted grammar](https://en.wikipedia.org/wiki/Unrestricted_grammar "Unrestricted grammar"), which further implies that it is capable of robustly evaluating first-order logic in an infinite number of ways. This is famously demonstrated through [lambda calculus](https://en.wikipedia.org/wiki/Lambda_calculus "Lambda calculus").

A Turing machine that is able to simulate any other Turing machine is called a [universal Turing machine](https://en.wikipedia.org/wiki/Universal_Turing_machine "Universal Turing machine") (**UTM**, or simply a **universal machine**). A more mathematically oriented definition with a similar "universal" nature was introduced by [Alonzo Church](https://en.wikipedia.org/wiki/Alonzo_Church "Alonzo Church"), whose work on [lambda calculus](https://en.wikipedia.org/wiki/Lambda_calculus "Lambda calculus") intertwined with Turing's in a formal theory of [computation](https://en.wikipedia.org/wiki/Computation "Computation") known as the [Church–Turing thesis](https://en.wikipedia.org/wiki/Church%E2%80%93Turing_thesis "Church–Turing thesis"). The thesis states that Turing machines indeed capture the informal notion of [effective methods](https://en.wikipedia.org/wiki/Effective_method "Effective method") in [logic](https://en.wikipedia.org/wiki/Logic "Logic") and [mathematics](https://en.wikipedia.org/wiki/Mathematics "Mathematics"), and provide a precise definition of an [algorithm](https://en.wikipedia.org/wiki/Algorithm "Algorithm") or "mechanical procedure". Studying their [abstract properties](https://en.wikipedia.org/wiki/Abstract_machine "Abstract machine") yields many insights into [computer science](https://en.wikipedia.org/wiki/Computer_science "Computer science") and [complexity theory](https://en.wikipedia.org/wiki/Computational_complexity_theory "Computational complexity theory").

### <span id="Physical_description" class="mw-headline">Physical description</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=2" title="Edit section: Physical description">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

In his 1948 essay, "Intelligent Machinery", Turing wrote that his machine consisted of:

...an unlimited memory capacity obtained in the form of an infinite tape marked out into squares, on each of which a symbol could be printed. At any moment there is one symbol in the machine; it is called the scanned symbol. The machine can alter the scanned symbol, and its behavior is in part determined by that symbol, but the symbols on the tape elsewhere do not affect the behavior of the machine. However, the tape can be moved back and forth through the machine, this being one of the elementary operations of the machine. Any symbol on the tape may therefore eventually have an innings.

<sup>
  <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_note-17"><span>[</span>17<span>]</span></a>
</sup>

 (Turing 1948, p. 3

<sup>
  <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_note-18"><span>[</span>18<span>]</span></a>
</sup>

)

## <span id="Informal_description" class="mw-headline">Informal description</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=3" title="Edit section: Informal description">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

For visualizations of Turing machines, see [Turing machine gallery](https://en.wikipedia.org/wiki/Turing_machine_gallery "Turing machine gallery").

The Turing machine mathematically models a machine that mechanically operates on a tape. On this tape are symbols, which the machine can read and write, one at a time, using a tape head. Operation is fully determined by a finite set of elementary instructions such as "in state 42, if the symbol seen is 0, write a 1; if the symbol seen is 1, change into state 17; in state 17, if the symbol seen is 0, write a 1 and change to state 6;" etc. In the original article ("On computable numbers, with an application to the [Entscheidungsproblem](https://en.wikipedia.org/wiki/Entscheidungsproblem "Entscheidungsproblem")", see also [references below](https://en.wikipedia.org/wiki/Turing_machine#The_Entscheidungsproblem_.28the_.22decision_problem.22.29:_Hilbert.27s_tenth_question_of_1900)), Turing imagines not a mechanism, but a person whom he calls the "computer", who executes these deterministic mechanical rules slavishly (or as Turing puts it, "in a desultory manner").

[![](https://upload.wikimedia.org/wikipedia/en/thumb/0/09/Turing_machine_2a.svg/300px-Turing_machine_2a.svg.png)](https://en.wikipedia.org/wiki/File:Turing_machine_2a.svg) [](https://en.wikipedia.org/wiki/File:Turing_machine_2a.svg "Enlarge")

The head is always over a particular square of the tape; only a finite stretch of squares is shown. The instruction to be performed (q

<sub>4</sub>

) is shown over the scanned square. (Drawing after Kleene (1952) p.375.)

[![](https://upload.wikimedia.org/wikipedia/commons/thumb/a/a2/Turing_machine_2b.svg/300px-Turing_machine_2b.svg.png)](https://en.wikipedia.org/wiki/File:Turing_machine_2b.svg) [](https://en.wikipedia.org/wiki/File:Turing_machine_2b.svg "Enlarge")

Here, the internal state (q

<sub>1</sub>

) is shown inside the head, and the illustration describes the tape as being infinite and pre-filled with "0", the symbol serving as blank. The system's full state (its _complete configuration_) consists of the internal state, any non-blank symbols on the tape (in this illustration "11B"), and the position of the head relative to those symbols including blanks, i.e. "011B". (Drawing after Minsky (1967) p. 121).

More precisely, a Turing machine consists of:

1. A **tape** divided into cells, one next to the other. Each cell contains a symbol from some finite alphabet. The alphabet contains a special _blank_ symbol (here written as '0') and one or more other symbols. The tape is assumed to be arbitrarily extendable to the left and to the right, i.e., the Turing machine is always supplied with as much tape as it needs for its computation. Cells that have not been written before are assumed to be filled with the blank symbol. In some models the tape has a left end marked with a special symbol; the tape extends or is indefinitely extensible to the right.
2. A **head** that can read and write symbols on the tape and move the tape left and right one (and only one) cell at a time. In some models the head moves and the tape is stationary.
3. A **state register** that stores the state of the Turing machine, one of finitely many. Among these is the special _start state_ with which the state register is initialized. These states, writes Turing, replace the "state of mind" a person performing computations would ordinarily be in.
4. A finite **table**

  <sup>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_note-19"><span>[</span>19<span>]</span></a>
  </sup>

   of instructions

  <sup>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_note-20"><span>[</span>20<span>]</span></a>
  </sup>

   that, given the _state_(q

  <sub>i</sub>

  ) the machine is currently in _and_ the _symbol_(a

  <sub>j</sub>

  ) it is reading on the tape (symbol currently under the head), tells the machine to do the following in sequence (for the 5-tuple models):

  - Either erase or write a symbol (replacing a

    <sub>j</sub>

     with a

    <sub>j1</sub>

    ), _and then_
  - Move the head (which is described by d

    <sub>k</sub>

     and can have values: 'L' for one step left _or_ 'R' for one step right _or_ 'N' for staying in the same place), _and then_
  - Assume the same or a _new state_ as prescribed (go to state q

    <sub>i1</sub>

    ).

  In the 4-tuple models, erasing or writing a symbol (a

  <sub>j1</sub>

  ) and moving the head left or right (d

  <sub>k</sub>

  ) are specified as separate instructions. Specifically, the table tells the machine to (ia) erase or write a symbol _or_ (ib) move the head left or right, _and then_ (ii) assume the same or a new state as prescribed, but not both actions (ia) and (ib) in the same instruction. In some models, if there is no entry in the table for the current combination of symbol and state then the machine will halt; other models require all entries to be filled.

Note that every part of the machine (i.e. its state, symbol-collections, and used tape at any given time) and its actions (such as printing, erasing and tape motion) is _finite_, _discrete_ and _distinguishable_; it is the unlimited amount of tape and runtime that gives it an unbounded amount of [storage space](https://en.wikipedia.org/wiki/Computer_storage "Computer storage").

## <span id="Formal_definition" class="mw-headline">Formal definition</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=4" title="Edit section: Formal definition">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

Following Hopcroft and Ullman (1979, p. 148), a (one-tape) Turing machine can be formally defined as a 7-[tuple](https://en.wikipedia.org/wiki/Tuple "Tuple") ![M = \langle Q, \Gamma, b, \Sigma, \delta, q_0, F \rangle](https://upload.wikimedia.org/math/4/1/6/4162f55252213c9c8549b771fe5f9aa4.png) where

- ![Q](https://upload.wikimedia.org/math/f/0/9/f09564c9ca56850d4cd6b3319e541aee.png) is a finite, non-empty set of _states_
- ![\Gamma](https://upload.wikimedia.org/math/1/6/2/162d4c413f99ae2763b1ced17ed1a14b.png) is a finite, non-empty set of _tape alphabet symbols_
- ![b \in \Gamma](https://upload.wikimedia.org/math/a/e/e/aee1e6755ef0919d2408c5409e9ddd2e.png) is the _blank symbol_ (the only symbol allowed to occur on the tape infinitely often at any step during the computation)
- ![\Sigma\subseteq\Gamma\setminus\{b\}](https://upload.wikimedia.org/math/0/2/d/02d00dcbd6e20d0c296f96f4621372bf.png) is the set of _input symbols_
- ![\delta: (Q \setminus F) \times \Gamma \rightarrow Q \times \Gamma \times \{L,R\}](https://upload.wikimedia.org/math/9/1/3/913e05d8295e146754d6f26abc21c40a.png) is a [partial function](https://en.wikipedia.org/wiki/Partial_function "Partial function") called the _[transition function](https://en.wikipedia.org/wiki/State_transition_system "State transition system")_, where L is left shift, R is right shift. (A relatively uncommon variant allows "no shift", say N, as a third element of the latter set.) If ![\delta](https://upload.wikimedia.org/math/f/1/0/f10f03c9836c36537d2539196058bfa2.png) is not defined on the current state and the current tape symbol, then the machine halts.

  <sup>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_note-21"><span>[</span>21<span>]</span></a>
  </sup>

- ![q_0 \in Q](https://upload.wikimedia.org/math/9/8/5/985a2a128ebf9e30b78e9273a1ada4d5.png) is the _initial state_
- ![F \subseteq Q](https://upload.wikimedia.org/math/2/e/f/2ef3b9c9ae290bb1868e0a3e1ab6dfdd.png) is the set of _final_ or _accepting states_. The initial tape contents is said to be _accepted_ by ![M](https://upload.wikimedia.org/math/6/9/6/69691c7bdcc3ce6d5d8a1361f22d04ac.png) if it eventually halts in a state from ![F](https://upload.wikimedia.org/math/8/0/0/800618943025315f869e4e1f09471012.png).

Anything that operates according to these specifications is a Turing machine.

The 7-tuple for the 3-state [busy beaver](https://en.wikipedia.org/wiki/Busy_beaver "Busy beaver") looks like this (see more about this busy beaver at [Turing machine examples](https://en.wikipedia.org/wiki/Turing_machine_examples "Turing machine examples")):

- ![Q = \{ \mbox{A}, \mbox{B}, \mbox{C}, \mbox{HALT} \}](https://upload.wikimedia.org/math/8/c/9/8c9ccc2b9eec7723ec82a63548888e01.png)
- ![\Gamma = \{ 0, 1 \}](https://upload.wikimedia.org/math/4/c/7/4c768aaeff22acb345e16f9cc2020d73.png)
- ![b = 0](https://upload.wikimedia.org/math/f/6/d/f6d5eef5ee5e51fc839bb54201c62e3b.png) ("blank")
- ![\Sigma = \{ 1 \}](https://upload.wikimedia.org/math/9/8/1/98140327d88ca4ee48e4d3c89c67df67.png)
- ![q_0 = \mbox{A}](https://upload.wikimedia.org/math/3/c/6/3c656f176d9c18800c1703283d72e87b.png) (the initial state)
- ![F = \{ \mbox{HALT} \}](https://upload.wikimedia.org/math/2/e/a/2eaba7cf1c262289ad79af2c4db3461b.png)
- ![\delta = ](https://upload.wikimedia.org/math/c/5/7/c57a72cbff4bba1f7b357d29ecca17e6.png) see state-table below

Initially all tape cells are marked with ![0](https://upload.wikimedia.org/math/c/f/c/cfcd208495d565ef66e7dff9f98764da.png).

State table for 3 state, 2 symbol busy beaver Tape symbol Current state A Current state B Current state C Write symbol Move tape Next state Write symbol Move tape Next state Write symbol Move tape Next state 0 1 R **B** 1 L **A** 1 L **B** 1 1 L **C** 1 R **B** 1 R **HALT**

## <span id="Additional_details_required_to_visualize_or_implement_Turing_machines" class="mw-headline">Additional details required to visualize or implement Turing machines</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=5" title="Edit section: Additional details required to visualize or implement Turing machines">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

In the words of van Emde Boas (1990), p. 6: "The set-theoretical object [his formal seven-tuple description similar to the above] provides only partial information on how the machine will behave and what its computations will look like."

For instance,

- There will need to be many decisions on what the symbols actually look like, and a failproof way of reading and writing symbols indefinitely.
- The shift left and shift right operations may shift the tape head across the tape, but when actually building a Turing machine it is more practical to make the tape slide back and forth under the head instead.
- The tape can be finite, and automatically extended with blanks as needed (which is closest to the mathematical definition), but it is more common to think of it as stretching infinitely at both ends and being pre-filled with blanks except on the explicitly given finite fragment the tape head is on. (This is, of course, not implementable in practice.) The tape _cannot_ be fixed in length, since that would not correspond to the given definition and would seriously limit the range of computations the machine can perform to those of a [linear bounded automaton](https://en.wikipedia.org/wiki/Linear_bounded_automaton "Linear bounded automaton").

### <span id="Alternative_definitions" class="mw-headline">Alternative definitions</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=6" title="Edit section: Alternative definitions">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

Definitions in literature sometimes differ slightly, to make arguments or proofs easier or clearer, but this is always done in such a way that the resulting machine has the same computational power. For example, changing the set ![\{L,R\}](https://upload.wikimedia.org/math/f/2/5/f251c223246e30ce25880246c0d12bb7.png) to ![\{L,R,N\}](https://upload.wikimedia.org/math/c/4/b/c4bcb8ee3f28d0fe745e258e8f0beec5.png), where _N_ ("None" or "No-operation") would allow the machine to stay on the same tape cell instead of moving left or right, does not increase the machine's computational power.

The most common convention represents each "Turing instruction" in a "Turing table" by one of nine 5-tuples, per the convention of Turing/Davis (Turing (1936) in _Undecidable_, p. 126-127 and Davis (2000) p. 152):

(definition 1): **(q

<sub>i</sub>

, S

<sub>j</sub>

, S

<sub>k</sub>

/E/N, L/R/N, q

<sub>m</sub>

)** **(** current state **q

<sub>i</sub>**

 **,** symbol scanned **S

<sub>j</sub>**

 **,** print symbol **S

<sub>k</sub>**

/erase **E**/none **N** **,** move_tape_one_square left **L**/right **R**/none **N** **,** new state **q

<sub>m</sub>**

 **)** Other authors (Minsky (1967) p. 119, Hopcroft and Ullman (1979) p. 158, Stone (1972) p. 9) adopt a different convention, with new state **q

<sub>m</sub>**

 listed immediately after the scanned symbol S

<sub>j</sub>

:

(definition 2): **(q

<sub>i</sub>

, S

<sub>j</sub>

, q

<sub>m</sub>

, S

<sub>k</sub>

/E/N, L/R/N)** **(** current state **q

<sub>i</sub>**

 **,** symbol scanned **S

<sub>j</sub>**

 **,** new state **q

<sub>m</sub>**

 **,** print symbol **S

<sub>k</sub>**

/erase **E**/none **N** **,** move_tape_one_square left **L**/right **R**/none **N** **)** For the remainder of this article "definition 1" (the Turing/Davis convention) will be used.

Current state | Scanned symbol |  | Print symbol | Move tape | Final (i.e. next) state | 5-tuples
------------- | -------------- |  | ------------ | --------- | ----------------------- | -----------------------
**A**         | 0              |  | 1            | R         | **B**                   | (**A**, 0, 1, R, **B**)
**A**         | 1              |  | 1            | L         | **C**                   | (**A**, 1, 1, L, **C**)
**B**         | 0              |  | 1            | L         | **A**                   | (**B**, 0, 1, L, **A**)
**B**         | 1              |  | 1            | R         | **B**                   | (**B**, 1, 1, R, **B**)
**C**         | 0              |  | 1            | L         | **B**                   | (**C**, 0, 1, L, **B**)
**C**         | 1              |  | 1            | N         | **H**                   | (**C**, 1, 1, N, **H**)

In the following table, Turing's original model allowed only the first three lines that he called N1, N2, N3 (cf Turing in _Undecidable_, p. 126). He allowed for erasure of the "scanned square" by naming a 0th symbol S

<sub>0</sub>

 = "erase" or "blank", etc. However, he did not allow for non-printing, so every instruction-line includes "print symbol S

<sub>k</sub>

" or "erase" (cf footnote 12 in Post (1947), _Undecidable_ p. 300). The abbreviations are Turing's (_Undecidable_ p. 119). Subsequent to Turing's original paper in 1936–1937, machine-models have allowed all nine possible types of five-tuples:

   | Current m-configuration (Turing state) | Tape symbol     | Print-operation          | Tape-motion | Final m-configuration (Turing state) | 5-tuple                                                                         | 5-tuple comments                                       | 4-tuple
-- | -------------------------------------- | --------------- | ------------------------ | ----------- | ------------------------------------ | ------------------------------------------------------------------------------- | ------------------------------------------------------ | ----------------------------------------------------------------------------
N1 | q

<sub>i</sub>                        | S

<sub>j</sub> | Print(S

<sub>k</sub>

) | Left L      | q

<sub>m</sub>                      | (q

<sub>i</sub>

, S

<sub>j</sub>

, S

<sub>k</sub>

, L, q

<sub>m</sub>

) | "blank" = S

<sub>0</sub>

, 1=S

<sub>1</sub>

, etc. |
N2 | q

<sub>i</sub>                        | S

<sub>j</sub> | Print(S

<sub>k</sub>

) | Right R     | q

<sub>m</sub>                      | (q

<sub>i</sub>

, S

<sub>j</sub>

, S

<sub>k</sub>

, R, q

<sub>m</sub>

) | "blank" = S

<sub>0</sub>

, 1=S

<sub>1</sub>

, etc. |
N3 | q

<sub>i</sub>                        | S

<sub>j</sub> | Print(S

<sub>k</sub>

) | None N      | q

<sub>m</sub>                      | (q

<sub>i</sub>

, S

<sub>j</sub>

, S

<sub>k</sub>

, N, q

<sub>m</sub>

) | "blank" = S

<sub>0</sub>

, 1=S

<sub>1</sub>

, etc. | (q

<sub>i</sub>

, S

<sub>j</sub>

, S

<sub>k</sub>

, q

<sub>m</sub>

)
4  | q

<sub>i</sub>                        | S

<sub>j</sub> | None N                   | Left L      | q

<sub>m</sub>                      | (q

<sub>i</sub>

, S

<sub>j</sub>

, N, L, q

<sub>m</sub>

)                 |                                                        | (q

<sub>i</sub>

, S

<sub>j</sub>

, L, q

<sub>m</sub>

)
5  | q

<sub>i</sub>                        | S

<sub>j</sub> | None N                   | Right R     | q

<sub>m</sub>                      | (q

<sub>i</sub>

, S

<sub>j</sub>

, N, R, q

<sub>m</sub>

)                 |                                                        | (q

<sub>i</sub>

, S

<sub>j</sub>

, R, q

<sub>m</sub>

)
6  | q

<sub>i</sub>                        | S

<sub>j</sub> | None N                   | None N      | q

<sub>m</sub>                      | (q

<sub>i</sub>

, S

<sub>j</sub>

, N, N, q

<sub>m</sub>

)                 | Direct "jump"                                          | (q

<sub>i</sub>

, S

<sub>j</sub>

, N, q

<sub>m</sub>

)
7  | q

<sub>i</sub>                        | S

<sub>j</sub> | Erase                    | Left L      | q

<sub>m</sub>                      | (q

<sub>i</sub>

, S

<sub>j</sub>

, E, L, q

<sub>m</sub>

)                 |                                                        |
8  | q

<sub>i</sub>                        | S

<sub>j</sub> | Erase                    | Right R     | q

<sub>m</sub>                      | (q

<sub>i</sub>

, S

<sub>j</sub>

, E, R, q

<sub>m</sub>

)                 |                                                        |
9  | q

<sub>i</sub>                        | S

<sub>j</sub> | Erase                    | None N      | q

<sub>m</sub>                      | (q

<sub>i</sub>

, S

<sub>j</sub>

, E, N, q

<sub>m</sub>

)                 |                                                        | (q

<sub>i</sub>

, S

<sub>j</sub>

, E, q

<sub>m</sub>

)

Any Turing table (list of instructions) can be constructed from the above nine 5-tuples. For technical reasons, the three non-printing or "N" instructions (4, 5, 6) can usually be dispensed with. For examples see [Turing machine examples](https://en.wikipedia.org/wiki/Turing_machine_examples "Turing machine examples").

Less frequently the use of 4-tuples are encountered: these represent a further atomization of the Turing instructions (cf Post (1947), Boolos & Jeffrey (1974, 1999), Davis-Sigal-Weyuker (1994)); also see more at [Post–Turing machine](https://en.wikipedia.org/wiki/Post%E2%80%93Turing_machine "Post–Turing machine").

### <span id="The_.22state.22" class="mw-headline">The “state”</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=7" title="Edit section: The &quot;state&quot;">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

The word "state" used in context of Turing machines can be a source of confusion, as it can mean two things. Most commentators after Turing have used "state" to mean the name/designator of the current instruction to be performed--i.e. the contents of the state register. But Turing (1936) made a strong distinction between a record of what he called the machine's "m-configuration", (its internal state) and the machine's (or person's) "state of progress" through the computation - the current state of the total system. What Turing called "the state formula" includes both the current instruction and _all_ the symbols on the tape:

> Thus the state of progress of the computation at any stage is completely determined by the note of instructions and the symbols on the tape. That is, the **state of the system** may be described by a single expression (sequence of symbols) consisting of the symbols on the tape followed by Δ (which we suppose not to appear elsewhere) and then by the note of instructions. This expression is called the 'state formula'.

> -- _Undecidable_, p.139–140, emphasis added

Earlier in his paper Turing carried this even further: he gives an example where he placed a symbol of the current "m-configuration"--the instruction's label--beneath the scanned square, together with all the symbols on the tape (_Undecidable_, p. 121); this he calls "the _complete configuration_" (_Undecidable_, p. 118). To print the "complete configuration" on one line, he places the state-label/m-configuration to the _left_ of the scanned symbol.

A variant of this is seen in Kleene (1952) where [Kleene](https://en.wikipedia.org/wiki/Stephen_Cole_Kleene "Stephen Cole Kleene") shows how to write the [Gödel number](https://en.wikipedia.org/wiki/G%C3%B6del_number "Gödel number") of a machine's "situation": he places the "m-configuration" symbol q

<sub>4</sub>

 over the scanned square in roughly the center of the 6 non-blank squares on the tape (see the Turing-tape figure in this article) and puts it to the _right_ of the scanned square. But Kleene refers to "q

<sub>4</sub>

" itself as "the machine state" (Kleene, p. 374-375). Hopcroft and Ullman call this composite the "instantaneous description" and follow the Turing convention of putting the "current state" (instruction-label, m-configuration) to the _left_ of the scanned symbol (p. 149).

**Example: total state of 3-state 2-symbol busy beaver after 3 "moves"** (taken from example "run" in the figure below):

1**A**1 This means: after three moves the tape has ... 000110000 ... on it, the head is scanning the right-most 1, and the state is **A**. Blanks (in this case represented by "0"s) can be part of the total state as shown here: **B**01; the tape has a single 1 on it, but the head is scanning the 0 ("blank") to its left and the state is **B**.

"State" in the context of Turing machines should be clarified as to which is being described: (_i_) the current instruction, or (_ii_) the list of symbols on the tape together with the current instruction, or (_iii_) the list of symbols on the tape together with the current instruction placed to the left of the scanned symbol or to the right of the scanned symbol.

Turing's biographer Andrew Hodges (1983: 107) has noted and discussed this confusion.

### <span id="Turing_machine_.22state.22_diagrams" class="mw-headline">Turing machine “state” diagrams</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=8" title="Edit section: Turing machine &quot;state&quot; diagrams">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

The table for the 3-state busy beaver ("P" = print/write a "1") Tape symbol Current state A Current state B Current state C Write symbol Move tape Next state Write symbol Move tape Next state Write symbol Move tape Next state **0** P R **B** P L **A** P L **B** **1** P L **C** P R **B** P R **HALT** [![](https://upload.wikimedia.org/wikipedia/commons/thumb/4/4b/State_diagram_3_state_busy_beaver_2B.svg/500px-State_diagram_3_state_busy_beaver_2B.svg.png)](https://en.wikipedia.org/wiki/File:State_diagram_3_state_busy_beaver_2B.svg) [](https://en.wikipedia.org/wiki/File:State_diagram_3_state_busy_beaver_2B.svg "Enlarge")

The "3-state busy beaver" Turing machine in a [finite state representation](https://en.wikipedia.org/wiki/Finite_State_Machine "Finite State Machine"). Each circle represents a "state" of the TABLE--an "m-configuration" or "instruction". "Direction" of a state _transition_ is shown by an arrow. The label (e.g.. **0/P,R**) near the outgoing state (at the "tail" of the arrow) specifies the scanned symbol that causes a particular transition (e.g. **0**) followed by a slash **/**, followed by the subsequent "behaviors" of the machine, e.g. "**P** **P**rint" then move tape "**R** **R**ight". No general accepted format exists. The convention shown is after McClusky (1965), Booth (1967), Hill, and Peterson (1974).

To the right: the above TABLE as expressed as a "state transition" diagram.

Usually large TABLES are better left as tables (Booth, p. 74). They are more readily simulated by computer in tabular form (Booth, p. 74). However, certain concepts--e.g. machines with "reset" states and machines with repeating patterns (cf Hill and Peterson p. 244ff)--can be more readily seen when viewed as a drawing.

Whether a drawing represents an improvement on its TABLE must be decided by the reader for the particular context. See [Finite state machine](https://en.wikipedia.org/wiki/Finite_state_machine "Finite state machine") for more.

[![](https://upload.wikimedia.org/wikipedia/commons/thumb/2/26/State_diagram_3_state_busy_beaver_4.JPG/500px-State_diagram_3_state_busy_beaver_4.JPG)](https://en.wikipedia.org/wiki/File:State_diagram_3_state_busy_beaver_4.JPG) [](https://en.wikipedia.org/wiki/File:State_diagram_3_state_busy_beaver_4.JPG "Enlarge")

The evolution of the busy-beaver's computation starts at the top and proceeds to the bottom.

The reader should again be cautioned that such diagrams represent a snapshot of their TABLE frozen in time, _not_ the course ("trajectory") of a computation _through_ time and/or space. While every time the busy beaver machine "runs" it will always follow the same state-trajectory, this is not true for the "copy" machine that can be provided with variable input "parameters".

The diagram "Progress of the computation" shows the 3-state busy beaver's "state" (instruction) progress through its computation from start to finish. On the far right is the Turing "complete configuration" (Kleene "situation", Hopcroft–Ullman "instantaneous description") at each step. If the machine were to be stopped and cleared to blank both the "state register" and entire tape, these "configurations" could be used to rekindle a computation anywhere in its progress (cf Turing (1936) _Undecidable_ pp. 139–140).

## <span id="Models_equivalent_to_the_Turing_machine_model" class="mw-headline">Models equivalent to the Turing machine model</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=9" title="Edit section: Models equivalent to the Turing machine model">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

See also: [Turing machine equivalents](https://en.wikipedia.org/wiki/Turing_machine_equivalents "Turing machine equivalents"), [Register machine](https://en.wikipedia.org/wiki/Register_machine "Register machine") and [Post–Turing machine](https://en.wikipedia.org/wiki/Post%E2%80%93Turing_machine "Post–Turing machine")

Many machines that might be thought to have more computational capability than a simple universal Turing machine can be shown to have no more power (Hopcroft and Ullman p. 159, cf Minsky (1967)). They might compute faster, perhaps, or use less memory, or their instruction set might be smaller, but they cannot compute more powerfully (i.e. more mathematical functions). (Recall that the [Church–Turing thesis](https://en.wikipedia.org/wiki/Church%E2%80%93Turing_thesis "Church–Turing thesis") _hypothesizes_ this to be true for any kind of machine: that anything that can be "computed" can be computed by some Turing machine.)

A Turing machine is equivalent to a [pushdown automaton](https://en.wikipedia.org/wiki/Pushdown_automaton "Pushdown automaton") that has been made more flexible and concise by relaxing the [last-in-first-out](https://en.wikipedia.org/wiki/LIFO_(computing) "LIFO (computing)") requirement of its stack.

At the other extreme, some very simple models turn out to be [Turing-equivalent](https://en.wikipedia.org/wiki/Turing_completeness "Turing completeness"), i.e. to have the same computational power as the Turing machine model.

Common equivalent models are the [multi-tape Turing machine](https://en.wikipedia.org/wiki/Multi-tape_Turing_machine "Multi-tape Turing machine"), [multi-track Turing machine](https://en.wikipedia.org/wiki/Multi-track_Turing_machine "Multi-track Turing machine"), machines with input and output, and the [_non-deterministic_ Turing machine](https://en.wikipedia.org/wiki/Non-deterministic_Turing_machine "Non-deterministic Turing machine") (NDTM) as opposed to the _deterministic_ Turing machine (DTM) for which the action table has at most one entry for each combination of symbol and state.

[Read-only, right-moving Turing machines](https://en.wikipedia.org/wiki/Read-only_right_moving_Turing_machines "Read-only right moving Turing machines") are equivalent to [NDFAs](https://en.wikipedia.org/wiki/Nondeterministic_finite_automaton "Nondeterministic finite automaton") (as well as [DFAs](https://en.wikipedia.org/wiki/Deterministic_finite_automaton "Deterministic finite automaton") by conversion using the [NDFA to DFA conversion algorithm](https://en.wikipedia.org/wiki/NDFA_to_DFA_conversion_algorithm "NDFA to DFA conversion algorithm")).

For practical and didactical intentions the equivalent [register machine](https://en.wikipedia.org/wiki/Register_machine "Register machine") can be used as a usual [assembly](https://en.wikipedia.org/wiki/Assembly_language "Assembly language") [programming language](https://en.wikipedia.org/wiki/Programming_language "Programming language").

## <span id="Choice_c-machines.2C_Oracle_o-machines" class="mw-headline">Choice c-machines, Oracle o-machines</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=10" title="Edit section: Choice c-machines, Oracle o-machines">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

Early in his paper (1936) Turing makes a distinction between an "automatic machine"--its "motion ... completely determined by the configuration" and a "choice machine":

> ...whose motion is only partially determined by the configuration ... When such a machine reaches one of these ambiguous configurations, it cannot go on until some arbitrary choice has been made by an external operator. This would be the case if we were using machines to deal with axiomatic systems.

> -- _Undecidable_, p. 118

Turing (1936) does not elaborate further except in a footnote in which he describes how to use an a-machine to "find all the provable formulae of the [Hilbert] calculus" rather than use a choice machine. He "suppose[s] that the choices are always between two possibilities 0 and 1\. Each proof will then be determined by a sequence of choices i

<sub>1</sub>

, i

<sub>2</sub>

, ..., i

<sub>n</sub>

 (i

<sub>1</sub>

 = 0 or 1, i

<sub>2</sub>

 = 0 or 1, ..., i

<sub>n</sub>

 = 0 or 1), and hence the number 2

<sup>n</sup>

 + i

<sub>1</sub>

2

<sup>n-1</sup>

 + i

<sub>2</sub>

2

<sup>n-2</sup>

 + ... +i

<sub>n</sub>

 completely determines the proof. The automatic machine carries out successively proof 1, proof 2, proof 3, ..." (Footnote ‡, _Undecidable_, p. 138)

This is indeed the technique by which a deterministic (i.e. a-) Turing machine can be used to mimic the action of a [nondeterministic Turing machine](https://en.wikipedia.org/wiki/Nondeterministic_Turing_machine "Nondeterministic Turing machine"); Turing solved the matter in a footnote and appears to dismiss it from further consideration.

An [oracle machine](https://en.wikipedia.org/wiki/Oracle_machine "Oracle machine") or o-machine is a Turing a-machine that pauses its computation at state "**o**" while, to complete its calculation, it "awaits the decision" of "the oracle"--an unspecified entity "apart from saying that it cannot be a machine" (Turing (1939), Undecidable p. 166–168). The concept is now actively used by mathematicians.

## <span id="Universal_Turing_machines" class="mw-headline">Universal Turing machines</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=11" title="Edit section: Universal Turing machines">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

Main article: [Universal Turing machine](https://en.wikipedia.org/wiki/Universal_Turing_machine "Universal Turing machine")

[![](https://upload.wikimedia.org/wikipedia/commons/thumb/a/ad/Model_of_a_Turing_machine.jpg/220px-Model_of_a_Turing_machine.jpg)](https://en.wikipedia.org/wiki/File:Model_of_a_Turing_machine.jpg) [](https://en.wikipedia.org/wiki/File:Model_of_a_Turing_machine.jpg "Enlarge")

An implementation of a Turing machine

As Turing wrote in _Undecidable_, p. 128 (italics added):

> It is possible to invent a _single machine_ which can be used to compute _any_ computable sequence. If this machine **U** is supplied with the tape on the beginning of which is written the string of quintuples separated by semicolons of some computing machine **M**, then **U** will compute the same sequence as **M**.

This finding is now taken for granted, but at the time (1936) it was considered astonishing. The model of computation that Turing called his "universal machine"--"**U**" for short--is considered by some (cf Davis (2000)) to have been the fundamental theoretical breakthrough that led to the notion of the [stored-program computer](https://en.wikipedia.org/wiki/Stored-program_computer "Stored-program computer").

> Turing's paper ... contains, in essence, the invention of the modern computer and some of the programming techniques that accompanied it.

> -- Minsky (1967), p. 104

In terms of [computational complexity](https://en.wikipedia.org/wiki/Computational_complexity_theory "Computational complexity theory"), a multi-tape universal Turing machine need only be slower by [logarithmic](https://en.wikipedia.org/wiki/Logarithm "Logarithm") factor compared to the machines it simulates. This result was obtained in 1966 by F. C. Hennie and [R. E. Stearns](https://en.wikipedia.org/wiki/R._E._Stearns "R. E. Stearns"). (Arora and Barak, 2009, theorem 1.9)

## <span id="Comparison_with_real_machines" class="mw-headline">Comparison with real machines</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=12" title="Edit section: Comparison with real machines">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

[![](https://upload.wikimedia.org/wikipedia/commons/thumb/7/7b/Lego_Turing_Machine.jpg/220px-Lego_Turing_Machine.jpg)](https://en.wikipedia.org/wiki/File:Lego_Turing_Machine.jpg) [](https://en.wikipedia.org/wiki/File:Lego_Turing_Machine.jpg "Enlarge")

A Turing machine realisation in LEGO

It is often said

<sup>[<em>
  <a href="https://en.wikipedia.org/wiki/Wikipedia:Manual_of_Style/Words_to_watch#Unsupported_attributions" title="Wikipedia:Manual of Style/Words to watch"><span title="The material near this tag possibly uses too-vague attribution or weasel words. (February 2016)">who?</span></a>
</em>]</sup>

 that Turing machines, unlike simpler automata, are as powerful as real machines, and are able to execute any operation that a real program can. What is neglected in this statement is that, because a real machine can only have a finite number of _configurations_, this "real machine" is really nothing but a [linear bounded automaton](https://en.wikipedia.org/wiki/Linear_bounded_automaton "Linear bounded automaton"). On the other hand, Turing machines are equivalent to machines that have an unlimited amount of storage space for their computations. However, Turing machines are not intended to model computers, but rather they are intended to model computation itself. Historically, computers, which compute only on their (fixed) internal storage, were developed only later.

There are a number of ways to explain why Turing machines are useful models of real computers:

1. Anything a real computer can compute, a Turing machine can also compute. For example: "A Turing machine can simulate any type of subroutine found in programming languages, including recursive procedures and any of the known parameter-passing mechanisms" (Hopcroft and Ullman p. 157). A large enough FSA can also model any real computer, disregarding IO. Thus, a statement about the limitations of Turing machines will also apply to real computers.
2. The difference lies only with the ability of a Turing machine to manipulate an unbounded amount of data. However, given a finite amount of time, a Turing machine (like a real machine) can only manipulate a finite amount of data.
3. Like a Turing machine, a real machine can have its storage space enlarged as needed, by acquiring more disks or other storage media. If the supply of these runs short, the Turing machine may become less useful as a model. But the fact is that neither Turing machines nor real machines need astronomical amounts of storage space in order to perform useful computation. The processing time required is usually much more of a problem.
4. Descriptions of real machine programs using simpler abstract models are often much more complex than descriptions using Turing machines. For example, a Turing machine describing an algorithm may have a few hundred states, while the equivalent deterministic finite automaton (DFA) on a given real machine has quadrillions. This makes the DFA representation infeasible to analyze.
5. Turing machines describe algorithms independent of how much memory they use. There is a limit to the memory possessed by any current machine, but this limit can rise arbitrarily in time. Turing machines allow us to make statements about algorithms which will (theoretically) hold forever, regardless of advances in _conventional_ computing machine architecture.
6. Turing machines simplify the statement of algorithms. Algorithms running on Turing-equivalent abstract machines are usually more general than their counterparts running on real machines, because they have arbitrary-precision data types available and never have to deal with unexpected conditions (including, but not limited to, running [out of memory](https://en.wikipedia.org/wiki/Out_of_memory "Out of memory")).

One way in which Turing machines are a poor model for programs is that many real programs, such as [operating systems](https://en.wikipedia.org/wiki/Operating_system "Operating system") and [word processors](https://en.wikipedia.org/wiki/Word_processor "Word processor"), are written to receive unbounded input over time, and therefore do not halt. Turing machines do not model such ongoing computation well (but can still model portions of it, such as individual procedures).

[![](https://upload.wikimedia.org/wikipedia/commons/thumb/8/8f/Turingmachine.jpg/220px-Turingmachine.jpg)](https://en.wikipedia.org/wiki/File:Turingmachine.jpg) [](https://en.wikipedia.org/wiki/File:Turingmachine.jpg "Enlarge")

An experimental prototype to achieve Turing machine

### <span id="Limitations_of_Turing_machines" class="mw-headline">Limitations of Turing machines</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=13" title="Edit section: Limitations of Turing machines">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

#### <span id="Computational_complexity_theory" class="mw-headline">Computational complexity theory</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=14" title="Edit section: Computational complexity theory">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

Further information: [Computational complexity theory](https://en.wikipedia.org/wiki/Computational_complexity_theory "Computational complexity theory")

A limitation of Turing machines is that they do not model the strengths of a particular arrangement well. For instance, modern stored-program computers are actually instances of a more specific form of [abstract machine](https://en.wikipedia.org/wiki/Abstract_machine "Abstract machine") known as the [random access stored program machine](https://en.wikipedia.org/wiki/Random_access_stored_program_machine "Random access stored program machine") or RASP machine model. Like the [Universal Turing machine](https://en.wikipedia.org/wiki/Universal_Turing_machine "Universal Turing machine") the RASP stores its "program" in "memory" external to its finite-state machine's "instructions". Unlike the universal Turing machine, the RASP has an infinite number of distinguishable, numbered but unbounded "registers"--memory "cells" that can contain any integer (cf. Elgot and Robinson (1964), Hartmanis (1971), and in particular Cook-Rechow (1973); references at [random access machine](https://en.wikipedia.org/wiki/Random_access_machine "Random access machine")). The RASP's finite-state machine is equipped with the capability for indirect addressing (e.g. the contents of one register can be used as an address to specify another register); thus the RASP's "program" can address any register in the register-sequence. The upshot of this distinction is that there are computational optimizations that can be performed based on the memory indices, which are not possible in a general Turing machine; thus when Turing machines are used as the basis for bounding running times, a 'false lower bound' can be proven on certain algorithms' running times (due to the false simplifying assumption of a Turing machine). An example of this is [binary search](https://en.wikipedia.org/wiki/Binary_search "Binary search"), an algorithm that can be shown to perform more quickly when using the RASP model of computation rather than the Turing machine model.

#### <span id="Concurrency" class="mw-headline">Concurrency</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=15" title="Edit section: Concurrency">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

Another limitation of Turing machines is that they do not model concurrency well. For example, there is a bound on the size of integer that can be computed by an always-halting nondeterministic Turing machine starting on a blank tape. (See article on [unbounded nondeterminism](https://en.wikipedia.org/wiki/Unbounded_nondeterminism "Unbounded nondeterminism").) By contrast, there are always-halting concurrent systems with no inputs that can compute an integer of unbounded size. (A process can be created with local storage that is initialized with a count of 0 that concurrently sends itself both a stop and a go message. When it receives a go message, it increments its count by 1 and sends itself a go message. When it receives a stop message, it stops with an unbounded number in its local storage.)

## <span id="History" class="mw-headline">History</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=16" title="Edit section: History">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

See also: [Algorithm](https://en.wikipedia.org/wiki/Algorithm "Algorithm") and [Church–Turing thesis](https://en.wikipedia.org/wiki/Church%E2%80%93Turing_thesis "Church–Turing thesis")

They were described in 1936 by [Alan Turing](https://en.wikipedia.org/wiki/Alan_Turing "Alan Turing").

### <span id="Historical_background:_computational_machinery" class="mw-headline">Historical background: computational machinery</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=17" title="Edit section: Historical background: computational machinery">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

[Robin Gandy](https://en.wikipedia.org/wiki/Robin_Gandy "Robin Gandy") (1919–1995)--a student of [Alan Turing](https://en.wikipedia.org/wiki/Alan_Turing "Alan Turing") (1912–1954) and his lifelong friend--traces the lineage of the notion of "calculating machine" back to [Babbage](https://en.wikipedia.org/wiki/Babbage "Babbage") (circa 1834) and actually proposes "Babbage's Thesis":

> _That the whole of development and operations of analysis are now capable of being executed by machinery_.

> -- (italics in Babbage as cited by Gandy, p. 54)

Gandy's analysis of Babbage's [Analytical Engine](https://en.wikipedia.org/wiki/Analytical_Engine "Analytical Engine") describes the following five operations (cf p. 52–53):

1. The arithmetic functions +, −, × where − indicates "proper" subtraction x − y = 0 if y ≥ x
2. Any sequence of operatio
ns is an operation

1. Iteration of an operation (repeating n times an operation P)
2. Conditional iteration (repeating n times an operation P conditional on the "success" of test T)
3. Conditional transfer (i.e. conditional "[goto](https://en.wikipedia.org/wiki/Goto "Goto")").

Gandy states that "the functions which can be calculated by (1), (2), and (4) are precisely those which are [Turing computable](https://en.wikipedia.org/wiki/Turing_computable "Turing computable")." (p. 53). He cites other proposals for "universal calculating machines" including those of [Percy Ludgate](https://en.wikipedia.org/wiki/Percy_Ludgate "Percy Ludgate") (1909), [Leonardo Torres y Quevedo](https://en.wikipedia.org/wiki/Leonardo_Torres_y_Quevedo "Leonardo Torres y Quevedo") (1914), [Maurice d'Ocagne](https://en.wikipedia.org/wiki/Maurice_d%27Ocagne "Maurice d'Ocagne") (1922), [Louis Couffignal](https://en.wikipedia.org/wiki/Louis_Couffignal "Louis Couffignal") (1933), [Vannevar Bush](https://en.wikipedia.org/wiki/Vannevar_Bush "Vannevar Bush") (1936), [Howard Aiken](https://en.wikipedia.org/wiki/Howard_Aiken "Howard Aiken") (1937). However:

> ... the emphasis is on programming a fixed iterable sequence of arithmetical operations. The fundamental importance of conditional iteration and conditional transfer for a general theory of calculating machines is not recognized ...

> -- Gandy p. 55

# <span id="The_Entscheidungsproblem_.28the_.22decision_problem.22.29:_Hilbert.27s_tenth_question_of_1900" class="mw-headline">The Entscheidungsproblem (the “decision problem”): Hilbert’s tenth question of 1900</span>

<span class="mw-editsection"><span class="mw-editsection-bracket">[</span><a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=18" title="Edit section: The Entscheidungsproblem (the &quot;decision problem">edit</a>: Hilbert's tenth question of 1900")<span class="mw-editsection-bracket">]</span></span>

With regards to [Hilbert's problems](https://en.wikipedia.org/wiki/Hilbert%27s_problems "Hilbert's problems") posed by the famous mathematician [David Hilbert](https://en.wikipedia.org/wiki/David_Hilbert "David Hilbert") in 1900, an aspect of problem #10 had been floating about for almost 30 years before it was framed precisely. Hilbert's original expression for #10 is as follows:

> **10\. Determination of the solvability of a Diophantine equation**. Given a [Diophantine equation](https://en.wikipedia.org/wiki/Diophantine_equation "Diophantine equation") with any number of unknown quantities and with rational integral coefficients: To devise a process according to which it can be determined in a finite number of operations whether the equation is solvable in rational integers. The Entscheidungsproblem [decision problem for [first-order logic](https://en.wikipedia.org/wiki/First-order_logic "First-order logic")] is solved when we know a procedure that allows for any given logical expression to decide by finitely many operations its validity or satisfiability ... The Entscheidungsproblem must be considered the main problem of mathematical logic.

> -- quoted, with this translation and the original German, in Dershowitz and Gurevich, 2008

By 1922, this notion of "[Entscheidungsproblem](https://en.wikipedia.org/wiki/Entscheidungsproblem "Entscheidungsproblem")" had developed a bit, and [H. Behmann](https://en.wikipedia.org/wiki/Heinrich_Behmann "Heinrich Behmann") stated that

> ... most general form of the Entscheidungsproblem [is] as follows:

> A quite definite generally applicable prescription is required which will allow one to decide in a finite number of steps the truth or falsity of a given purely logical assertion ...

> -- Gandy p. 57, quoting Behmann

> Behmann remarks that ... the general problem is equivalent to the problem of deciding which mathematical propositions are true.

> -- _ibid._

> If one were able to solve the Entscheidungsproblem then one would have a "procedure for solving many (or even all) mathematical problems".

> -- _ibid._, p. 92

By the 1928 international congress of mathematicians, Hilbert "made his questions quite precise. First, was mathematics _[complete](https://en.wikipedia.org/wiki/Completeness_(logic) "Completeness (logic)")_ ... Second, was mathematics _[consistent](https://en.wikipedia.org/wiki/Consistency_proof "Consistency proof")_ ... And thirdly, was mathematics _[decidable](https://en.wikipedia.org/wiki/Decidability_(logic) "Decidability (logic)")_?" (Hodges p. 91, Hawking p. 1121). The first two questions were answered in 1930 by [Kurt Gödel](https://en.wikipedia.org/wiki/Kurt_G%C3%B6del "Kurt Gödel") at the very same meeting where Hilbert delivered his retirement speech (much to the chagrin of Hilbert); the third--the Entscheidungsproblem--had to wait until the mid-1930s.

The problem was that an answer first required a precise definition of "_definite general applicable prescription_", which Princeton professor [Alonzo Church](https://en.wikipedia.org/wiki/Alonzo_Church "Alonzo Church") would come to call "[effective calculability](https://en.wikipedia.org/wiki/Effective_calculability "Effective calculability")", and in 1928 no such definition existed. But over the next 6–7 years [Emil Post](https://en.wikipedia.org/wiki/Emil_Post "Emil Post") developed his definition of a worker moving from room to room writing and erasing marks per a list of instructions (Post 1936), as did Church and his two students [Stephen Kleene](https://en.wikipedia.org/wiki/Stephen_Kleene "Stephen Kleene") and [J. B. Rosser](https://en.wikipedia.org/wiki/J._B._Rosser "J. B. Rosser") by use of Church's [lambda-calculus](https://en.wikipedia.org/wiki/Lambda-calculus "Lambda-calculus") and Gödel's [recursion theory](https://en.wikipedia.org/wiki/Recursion_theory "Recursion theory") (1934). Church's paper (published 15 April 1936) showed that the Entscheidungsproblem was indeed "undecidable" and beat Turing to the punch by almost a year (Turing's paper submitted 28 May 1936, published January 1937). In the meantime, Emil Post submitted a brief paper in the fall of 1936, so Turing at least had priority over Post. While Church refereed Turing's paper, Turing had time to study Church's paper and add an Appendix where he sketched a proof that Church's lambda-calculus and his machines would compute the same functions.

> But what Church had done was something rather different, and in a certain sense weaker. ... the Turing construction was more direct, and provided an argument from first principles, closing the gap in Church's demonstration.

> -- Hodges p. 112

And Post had only proposed a definition of [calculability](https://en.wikipedia.org/wiki/Church%E2%80%93Turing_thesis "Church–Turing thesis") and criticized Church's "definition", but had proved nothing.

# <span id="Alan_Turing.27s_a-_.28automatic-.29machine" class="mw-headline">Alan Turing’s a- (automatic-)machine</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=19" title="Edit section: Alan Turing's a- (automatic-)machine">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

In the spring of 1935, Turing as a young Master's student at [King's College Cambridge](https://en.wikipedia.org/wiki/King%27s_College_Cambridge "King's College Cambridge"), [UK](https://en.wikipedia.org/wiki/UK "UK"), took on the challenge; he had been stimulated by the lectures of the logician [M. H. A. Newman](https://en.wikipedia.org/wiki/M._H._A._Newman "M. H. A. Newman") "and learned from them of Gödel's work and the Entscheidungsproblem ... Newman used the word 'mechanical' ... In his obituary of Turing 1955 Newman writes:

> To the question 'what is a "mechanical" process?' Turing returned the characteristic answer 'Something that can be done by a machine' and he embarked on the highly congenial task of analysing the general notion of a computing machine.

> -- Gandy, p. 74

Gandy states that:

> I suppose, but do not know, that Turing, right from the start of his work, had as his goal a proof of the undecidability of the Entscheidungsproblem. He told me that the 'main idea' of the paper came to him when he was lying in Grantchester meadows in the summer of 1935\. The 'main idea' might have either been his analysis of computation or his realization that there was a universal machine, and so a [diagonal argument](https://en.wikipedia.org/wiki/Cantor%27s_diagonal_argument "Cantor's diagonal argument") to prove unsolvability.

> -- _ibid._, p. 76

While Gandy believed that Newman's statement above is "misleading", this opinion is not shared by all. Turing had a lifelong interest in machines: "Alan had dreamt of inventing typewriters as a boy; [his mother] Mrs. Turing had a typewriter; and he could well have begun by asking himself what was meant by calling a typewriter 'mechanical'" (Hodges p. 96). While at Princeton pursuing his PhD, Turing built a Boolean-logic multiplier (see below). His PhD thesis, titled "Systems of Logic Based on Ordinals", contains the following definition of "a computable function":

> It was stated above that 'a function is effectively calculable if its values can be found by some purely mechanical process'. We may take this statement literally, understanding by a purely mechanical process one which could be carried out by a machine. It is possible to give a mathematical description, in a certain normal form, of the structures of these machines. The development of these ideas leads to the author's definition of a computable function, and to an identification of computability with effective calculability. It is not difficult, though somewhat laborious, to prove that these three definitions [the 3rd is the λ-calculus] are equivalent.

> -- Turing (1939) in _The Undecidable_, p. 160

When Turing returned to the UK he ultimately became jointly responsible for breaking the German secret codes created by encryption machines called "The Enigma"; he also became involved in the design of the ACE ([Automatic Computing Engine](https://en.wikipedia.org/wiki/Automatic_Computing_Engine "Automatic Computing Engine")), "[Turing's] ACE proposal was effectively self-contained, and its roots lay not in the [EDVAC](https://en.wikipedia.org/wiki/EDVAC "EDVAC") [the USA's initiative], but in his own universal machine" (Hodges p. 318). Arguments still continue concerning the origin and nature of what has been named by Kleene (1952) [Turing's Thesis](https://en.wikipedia.org/wiki/Turing%27s_Thesis "Turing's Thesis"). But what Turing _did prove_ with his computational-machine model appears in his paper _On Computable Numbers, With an Application to the Entscheidungsproblem_ (1937):

> [that] the Hilbert Entscheidungsproblem can have no solution ... I propose, therefore to show that there can be no general process for determining whether a given formula U of the functional calculus K is provable, i.e. that there can be no machine which, supplied with any one U of these formulae, will eventually say whether U is provable.

> -- from Turing's paper as reprinted in _The Undecidable_, p. 145

Turing's example (his second proof): If one is to ask for a general procedure to tell us: "Does this machine ever print 0", the question is "undecidable".

# <span id="1937.E2.80.931970:_The_.22digital_computer.22.2C_the_birth_of_.22computer_science.22" class="mw-headline">1937–1970: The “digital computer”, the birth of “computer science”</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=20" title="Edit section: 1937–1970: The &quot;digital computer&quot;, the birth of &quot;computer science&quot;">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

In 1937, while at Princeton working on his PhD thesis, Turing built a digital (Boolean-logic) multiplier from scratch, making his own electromechanical relays (Hodges p. 138). "Alan's task was to embody the logical design of a Turing machine in a network of relay-operated switches ..." (Hodges p. 138). While Turing might have been just initially curious and experimenting, quite-earnest work in the same direction was going in Germany ([Konrad Zuse](https://en.wikipedia.org/wiki/Konrad_Zuse "Konrad Zuse") (1938)), and in the United States ([Howard Aiken](https://en.wikipedia.org/wiki/Howard_Aiken "Howard Aiken")) and [George Stibitz](https://en.wikipedia.org/wiki/George_Stibitz "George Stibitz") (1937); the fruits of their labors were used by the Axis and Allied military in [World War II](https://en.wikipedia.org/wiki/World_War_II "World War II") (cf Hodges p. 298–299). In the early to mid-1950s [Hao Wang](https://en.wikipedia.org/wiki/Hao_Wang_(academic) "Hao Wang (academic)") and [Marvin Minsky](https://en.wikipedia.org/wiki/Marvin_Minsky "Marvin Minsky") reduced the Turing machine to a simpler form (a precursor to the [Post-Turing machine](https://en.wikipedia.org/wiki/Post-Turing_machine "Post-Turing machine") of [Martin Davis](https://en.wikipedia.org/wiki/Martin_Davis "Martin Davis")); simultaneously European researchers were reducing the new-fangled [electronic computer](https://en.wikipedia.org/wiki/Electronic_computer "Electronic computer") to a computer-like theoretical object equivalent to what was now being called a "Turing machine". In the late 1950s and early 1960s, the coincidentally parallel developments of Melzak and Lambek (1961), Minsky (1961), and Shepherdson and Sturgis (1961) carried the European work further and reduced the Turing machine to a more friendly, computer-like abstract model called the [counter machine](https://en.wikipedia.org/wiki/Counter_machine "Counter machine"); Elgot and Robinson (1964), Hartmanis (1971), Cook and Reckhow (1973) carried this work even further with the [register machine](https://en.wikipedia.org/wiki/Register_machine "Register machine") and [random access machine](https://en.wikipedia.org/wiki/Random_access_machine "Random access machine") models--but basically all are just multi-tape Turing machines with an arithmetic-like instruction set.

# <span id="1970.E2.80.93present:_the_Turing_machine_as_a_model_of_computation" class="mw-headline">1970–present: the Turing machine as a model of computation</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=21" title="Edit section: 1970–present: the Turing machine as a model of computation">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

Today, the counter, register and random-access machines and their sire the Turing machine continue to be the models of choice for theorists investigating questions in the [theory of computation](https://en.wikipedia.org/wiki/Theory_of_computation "Theory of computation"). In particular, [computational complexity theory](https://en.wikipedia.org/wiki/Computational_complexity_theory "Computational complexity theory") makes use of the Turing machine:

> Depending on the objects one likes to manipulate in the computations (numbers like nonnegative integers or alphanumeric strings), two models have obtained a dominant position in machine-based complexity theory:

> _the off-line multitape Turing machine_..., which represents the standard model for string-oriented computation, and

> the _random access machine (RAM)_ as introduced by Cook and Reckhow ..., which models the idealized Von Neumann style computer.

> -- van Emde Boas 1990:4

> Only in the related area of analysis of algorithms this role is taken over by the RAM model.

> -- van Emde Boas 1990:16

Kantorovitz(2005) Sweden was the first to show the most simple obvious representation of Turing Machines published academically which unifies Turing Machines with mathematical analysis and analog computers.

## <span id="See_also" class="mw-headline">See also</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=22" title="Edit section: See also">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

- [Algorithm](https://en.wikipedia.org/wiki/Algorithm "Algorithm"), for a brief history of some of the inventions and the mathematics leading to Turing's definition of what he called his "a-machine"
- [Arithmetical hierarchy](https://en.wikipedia.org/wiki/Arithmetical_hierarchy "Arithmetical hierarchy")
- [Bekenstein bound](https://en.wikipedia.org/wiki/Bekenstein_bound "Bekenstein bound"), showing the impossibility of infinite-tape Turing machines of finite size and bounded energy
- [BlooP and FlooP](https://en.wikipedia.org/wiki/BlooP_and_FlooP "BlooP and FlooP")
- [Busy beaver](https://en.wikipedia.org/wiki/Busy_beaver "Busy beaver")
- [Chaitin constant](https://en.wikipedia.org/wiki/Chaitin_constant "Chaitin constant") or [Omega (computer science)](https://en.wikipedia.org/wiki/Omega_(computer_science) "Omega (computer science)") for information relating to the halting problem
- [Church–Turing thesis](https://en.wikipedia.org/wiki/Church%E2%80%93Turing_thesis "Church–Turing thesis"), which says Turing machines can perform any computation that can be performed
- [Claude Shannon](https://en.wikipedia.org/wiki/Claude_Shannon "Claude Shannon"), another leading thinker in information theory
- [Conway's Game of Life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life "Conway's Game of Life"), a Turing-complete cellular automaton
- [Digital infinity](https://en.wikipedia.org/wiki/Digital_infinity "Digital infinity")
- [Enumerator (in theoretical computer science)](https://en.wikipedia.org/wiki/Enumerator_(in_theoretical_computer_science) "Enumerator (in theoretical computer science)")
- _[Gödel, Escher, Bach: An Eternal Golden Braid](https://en.wikipedia.org/wiki/G%C3%B6del,_Escher,_Bach:_An_Eternal_Golden_Braid "Gödel, Escher, Bach: An Eternal Golden Braid")_, a famous book that discusses, among other topics, the Church–Turing thesis | - [The Emperor's New Mind](https://en.wikipedia.org/wiki/The_Emperor%27s_New_Mind "The Emperor's New Mind")
- [Halting problem](https://en.wikipedia.org/wiki/Halting_problem "Halting problem"), for more references
- [Harvard architecture](https://en.wikipedia.org/wiki/Harvard_architecture "Harvard architecture")
- [Imperative programming](https://en.wikipedia.org/wiki/Imperative_programming "Imperative programming")
- [Langton's ant](https://en.wikipedia.org/wiki/Langton%27s_ant "Langton's ant") and [Turmites](https://en.wikipedia.org/wiki/Turmite "Turmite"), simple two-dimensional analogues of the Turing machine
- [Modified Harvard architecture](https://en.wikipedia.org/wiki/Modified_Harvard_architecture "Modified Harvard architecture")
- [Probabilistic Turing machine](https://en.wikipedia.org/wiki/Probabilistic_Turing_machine "Probabilistic Turing machine")
- [Unorganized machine](https://en.wikipedia.org/wiki/Unorganized_machine "Unorganized machine"), for Turing's very early ideas on neural networks
- [Quantum Turing machine](https://en.wikipedia.org/wiki/Quantum_Turing_machine "Quantum Turing machine")
- [Turing completeness](https://en.wikipedia.org/wiki/Turing_completeness "Turing completeness"), an attribute used in [computability theory](https://en.wikipedia.org/wiki/Computability_theory_(computation) "Computability theory (computation)") to describe computing systems with power equivalent to a universal Turing machine
- [Turing machine examples](https://en.wikipedia.org/wiki/Turing_machine_examples "Turing machine examples")
- [Turing switch](https://en.wikipedia.org/wiki/Turing_switch "Turing switch")
- [Turing tarpit](https://en.wikipedia.org/wiki/Turing_tarpit "Turing tarpit"), any computing system or language that, despite being Turing complete, is generally considered useless for practical computing
- [Von Neumann architecture](https://en.wikipedia.org/wiki/Von_Neumann_architecture "Von Neumann architecture")
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## <span id="Notes" class="mw-headline">Notes</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=23" title="Edit section: Notes">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

1. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_ref-1">^</a>
  </strong>
  </span>

   

  <span class="reference-text">Minsky 1967:107 “In his 1936 paper, A. M. Turing defined the class of abstract machines that now bear his name. A Turing machine is a finite-state machine associated with a special kind of environment – its tape – in which it can store (and later recover) sequences of symbols”, also Stone 1972:8 where the word “machine” is in quotation marks.</span>

2. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_ref-2">^</a>
  </strong>
  </span>

   

  <span class="reference-text">Stone 1972:8 states “This ”machine" is an abstract mathematical model“, also cf Sipser 2006:137ff that describes the ”Turing machine model“. Rogers 1987 (1967):13 refers to ”Turing’s characterization“, Boolos Burgess and Jeffrey 2002:25 refers to a ”specific kind of idealized machine".</span>

3. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_ref-3">^</a>
  </strong>
  </span>

   

  <span class="reference-text">Sipser 2006:137 “A Turing machine can do everything that a real computer can do”.</span>

4. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_ref-4">^</a>
  </strong>
  </span>

   

  <span class="reference-text">cf Sipser 2002:137\. Also Rogers 1987 (1967):13 describes “a paper tape of infinite length in both directions”. Minsky 1967:118 states “The tape is regarded as infinite in both directions”. Boolos Burgess and Jeffrey 2002:25 include the possibility of “there is someone stationed at each end to add extra blank squares as needed”.</span>

5. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_ref-5">^</a>
  </strong>
  </span>

   

  <span class="reference-text">cf Rogers 1987 (1967):13\. Other authors use the word “square” e.g. Boolos Burgess Jeffrey 2002:35, Minsky 1967:117, Penrose 1989:37.</span>

6. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_ref-6">^</a>
  </strong>
  </span>

   

  <span class="reference-text">The word used by e.g. Davis 2000:151</span>

7. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_ref-7">^</a>
  </strong>
  </span>

   

  <span class="reference-text">This table represents an <a href="https://en.wikipedia.org/wiki/Algorithm" title="Algorithm">algorithm</a> or “effective computational procedure” which is necessarily finite; see Penrose 1989:30ff, Stone 1972:3ff.</span>

8. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_ref-8">^</a>
  </strong>
  </span>

   

  <span class="reference-text">Boolos Burgess and Jeffrey 2002:25</span>

9. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_ref-9">^</a>
  </strong>
  </span>

   

  <span class="reference-text">Boolos Burgess Jeffry 2002:25 illustrate the machine as moving along the tape. Penrose 1989:36-37 describes himself as “uncomfortable” with an infinite tape observing that it “might be hard to shift!”; he “prefer[s] to think of the tape as representing some external environment through which our finite device can move” and after observing that the " ‘movement’ is a convenient way of picturing things" and then suggests that "the device receives all its input from this environment.</span>

10. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_ref-10">^</a>
  </strong>
  </span>

   

  <span class="reference-text">“Also by convention one of the states is distinguished as the stopping state and is given the name HALT” (Stone 1972:9). Turing’s original description did not include a HALT instruction but he did allow for a “circular” condition, a “configuration from which there is no possible move” (see Turing 1936 in <em>The Undecidable</em> 1967:119); this notion was added in the 1950s; see more at <a href="https://en.wikipedia.org/wiki/Halting_problem" title="Halting problem">Halting problem</a>.</span>

11. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_ref-Hodges-2012_11-0">^</a>
  </strong>
  </span>

   

  <span class="reference-text"><a href="https://en.wikipedia.org/wiki/Andrew_Hodges" title="Andrew Hodges">Andrew Hodges</a> (2012). <em>Alan Turing: The Enigma (THE CENTENARY EDITION)</em>. Princeton University Press. <a href="https://en.wikipedia.org/wiki/International_Standard_Book_Number" title="International Standard Book Number">ISBN</a> <a href="https://en.wikipedia.org/wiki/Special:BookSources/978-0-691-15564-7" title="Special:BookSources/978-0-691-15564-7">978-0-691-15564-7</a>.<span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.au=Andrew+Hodges&amp;rft.btitle=Alan+Turing%3A+The+Enigma+%28THE+CENTENARY+EDITION%29&amp;rft.date=2012&amp;rft.genre=book&amp;rft.isbn=978-0-691-15564-7&amp;rft.pub=Princeton+University+Press&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span></span>

12. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_ref-12">^</a>
  </strong>
  </span>

   

  <span class="reference-text">The idea came to him in mid-1935 (perhaps, see more in the History section) after a question posed by <a href="https://en.wikipedia.org/wiki/M._H._A._Newman" class="mw-redirect" title="M. H. A. Newman">M. H. A. Newman</a> in his lectures: "Was there a definite method, or as Newman put it, a <em>mechanical process</em> which could be applied to a mathematical statement, and which would come up with the answer as to whether it was provable" (Hodges 1983:93). Turing submitted his paper on 31 May 1936 to the London Mathematical Society for its <em>Proceedings</em> (cf Hodges 1983:112), but it was <em>published</em> in early 1937 and offprints were available in February 1937 (cf Hodges 1983:129).</span>

13. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_ref-13">^</a>
  </strong>
  </span>

   

  <span class="reference-text">See footnote in Davis 2000:151.</span>

14. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_ref-14">^</a>
  </strong>
  </span>

   

  <span class="reference-text">Turing 1936 in <em>The Undecidable</em> 1965:132-134; Turing’s definition of “circular” is found on page 119.</span>

15. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_ref-15">^</a>
  </strong>
  </span>

   

  <span class="reference-text">Turing 1936 in <em>The Undecidable</em> 1965:145</span>

16. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_ref-16">^</a>
  </strong>
  </span>

   

  <span class="reference-text">Sipser 2006:137 observes that “A Turing machine can do everything that a real computer can do. Nevertheless, even a Turing machine cannot solve certain problems. In a very real sense, these problems are beyond the theoretical limits of computation.”</span>

17. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_ref-17">^</a>
  </strong>
  </span>

   

  <span class="reference-text">See the definition of "<a href="https://en.wiktionary.org/wiki/innings" class="extiw" title="wikt:innings">innings</a>" on <a href="https://en.wikipedia.org/wiki/Wiktionary" title="Wiktionary">Wiktionary</a></span>

18. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_ref-18">^</a>
  </strong>
  </span>

   

  <span class="reference-text">A.M. Turing (1948). <a href="http://www.alanturing.net/turing_archive/archive/l/l32/L32-004.html" class="external text">“Intelligent Machinery (manuscript)”</a>. The Turing Archive. p. 3.<span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.au=A.M.+Turing&amp;rft.btitle=Intelligent+Machinery+%28manuscript%29&amp;rft.date=1948&amp;rft.genre=unknown&amp;rft_id=http%3A%2F%2Fwww.alanturing.net%2Fturing_archive%2Farchive%2Fl%2Fl32%2FL32-004.html&amp;rft.pages=3&amp;rft.pub=The+Turing+Archive&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span></span>

19. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_ref-19">^</a>
  </strong>
  </span>

   

  <span class="reference-text">Occasionally called an <strong>action table</strong> or <strong>transition function</strong></span>

20. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_ref-20">^</a>
  </strong>
  </span>

   

  <span class="reference-text">Usually quintuples [5-tuples]: q<sub>i</sub>a<sub>j</sub>→q<sub>i1</sub>a<sub>j1</sub>d<sub>k</sub>, but sometimes quadruples [4-tuples].</span>

21. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Turing_machine#cite_ref-21">^</a>
  </strong>
  </span>

   

  <span class="reference-text">p.149; in particular, Hopcroft and Ullman assume that <img src="https://upload.wikimedia.org/math/f/1/0/f10f03c9836c36537d2539196058bfa2.png" alt="\delta" class="tex entry-content-asset"> is undefined on all states from <img src="https://upload.wikimedia.org/math/8/0/0/800618943025315f869e4e1f09471012.png" alt="F" class="tex entry-content-asset"></span>

## <span id="References" class="mw-headline">References</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=24" title="Edit section: References">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

### <span id="Primary_literature.2C_reprints.2C_and_compilations" class="mw-headline">Primary literature, reprints, and compilations</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=25" title="Edit section: Primary literature, reprints, and compilations">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

- B. [Jack Copeland](https://en.wikipedia.org/wiki/Jack_Copeland "Jack Copeland") ed. (2004), _The Essential Turing: Seminal Writings in Computing, Logic, Philosophy, Artificial Intelligence, and Artificial Life plus The Secrets of Enigma,_ Clarendon Press (Oxford University Press), Oxford UK, [ISBN 0-19-825079-7](https://en.wikipedia.org/wiki/Special:BookSources/0198250797). Contains the Turing papers plus a draft letter to [Emil Post](https://en.wikipedia.org/wiki/Emil_Post "Emil Post") re his criticism of "Turing's convention", and Donald W. Davies' _Corrections to Turing's Universal Computing Machine_
- [Martin Davis](https://en.wikipedia.org/wiki/Martin_Davis "Martin Davis") (ed.) (1965), _The Undecidable_, Raven Press, Hewlett, NY.
- Emil Post (1936), "Finite Combinatory Processes--Formulation 1", _Journal of Symbolic Logic_, 1, 103–105, 1936\. Reprinted in _The Undecidable_ pp. 289ff.
- Emil Post (1947), "Recursive Unsolvability of a Problem of Thue", _Journal of Symbolic Logic_, vol. 12, pp. 1–11\. Reprinted in _The Undecidable_ pp. 293ff. In the Appendix of this paper Post comments on and gives corrections to Turing's paper of 1936–1937\. In particular see the footnotes 11 with corrections to the universal computing machine coding and footnote 14 with comments on [Turing's first and second proofs](https://en.wikipedia.org/wiki/Turing%27s_proof "Turing's proof").
- Turing, A.M. (1936). "On Computable Numbers, with an Application to the Entscheidungs problem". _Proceedings of the London Mathematical Society_. 2 (1937) **42**: 230–265\. [doi](https://en.wikipedia.org/wiki/Digital_object_identifier "Digital object identifier"):[10.1112/plms/s2-42.1.230](https://dx.doi.org/10.1112%2Fplms%2Fs2-42.1.230).

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.atitle=On+Computable+Numbers%2C+with+an+Application+to+the+Entscheidungs+problem&amp;rft.aufirst=A.M.&amp;rft.aulast=Turing&amp;rft.date=1936&amp;rft.genre=article&amp;rft_id=info%3Adoi%2F10.1112%2Fplms%2Fs2-42.1.230&amp;rft.jtitle=Proceedings+of+the+London+Mathematical+Society&amp;rft.pages=230-265&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Ajournal&amp;rft.volume=42">
    <span>
  </span>
  </span>

   (and Turing, A.M. (1938). "On Computable Numbers, with an Application to the Entscheidungsproblem: A correction". _Proceedings of the London Mathematical Society_. 2 (1937) **43** (6): 544–6\. [doi](https://en.wikipedia.org/wiki/Digital_object_identifier "Digital object identifier"):[10.1112/plms/s2-43.6.544](https://dx.doi.org/10.1112%2Fplms%2Fs2-43.6.544).

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.atitle=On+Computable+Numbers%2C+with+an+Application+to+the+Entscheidungsproblem%3A+A+correction&amp;rft.aufirst=A.M.&amp;rft.aulast=Turing&amp;rft.date=1938&amp;rft.genre=article&amp;rft_id=info%3Adoi%2F10.1112%2Fplms%2Fs2-43.6.544&amp;rft.issue=6&amp;rft.jtitle=Proceedings+of+the+London+Mathematical+Society&amp;rft.pages=544-6&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Ajournal&amp;rft.volume=43">
    <span>
  </span>
  </span>

  ). Reprinted in many collections, e.g. in _The Undecidable_ pp. 115–154; available on the web in many places.
- Alan Turing, 1948, "Intelligent Machinery." Reprinted in "Cybernetics: Key Papers." Ed. C.R. Evans and A.D.J. Robertson. Baltimore: University Park Press, 1968\. p. 31\. Reprinted in Turing, A. M. (1996). "Intelligent Machinery, A Heretical Theory". _Philosophia Mathematica_ **4** (3): 256\. [doi](https://en.wikipedia.org/wiki/Digital_object_identifier "Digital object identifier"):[10.1093/philmat/4.3.256](https://dx.doi.org/10.1093%2Fphilmat%2F4.3.256).

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.atitle=Intelligent+Machinery%2C+A+Heretical+Theory&amp;rft.aufirst=A.+M.&amp;rft.aulast=Turing&amp;rft.date=1996&amp;rft.genre=article&amp;rft_id=info%3Adoi%2F10.1093%2Fphilmat%2F4.3.256&amp;rft.issue=3&amp;rft.jtitle=Philosophia+Mathematica&amp;rft.pages=256&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Ajournal&amp;rft.volume=4">
    <span>
  </span>
  </span>

- F. C. Hennie and [R. E. Stearns](https://en.wikipedia.org/wiki/R._E._Stearns "R. E. Stearns"). _Two-tape simulation of multitape Turing machines_. [JACM](https://en.wikipedia.org/wiki/JACM "JACM"), 13(4):533–546, 1966.

### <span id="Computability_theory" class="mw-headline">Computability theory</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=26" title="Edit section: Computability theory">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

- Boolos, George; Richard Jeffrey (1999) [1989]. _Computability and Logic_ (3rd ed.). Cambridge UK: Cambridge University Press. [ISBN](https://en.wikipedia.org/wiki/International_Standard_Book_Number "International Standard Book Number") [0-521-20402-X](https://en.wikipedia.org/wiki/Special:BookSources/0-521-20402-X "Special:BookSources/0-521-20402-X").

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.aufirst=George&amp;rft.aulast=Boolos&amp;rft.au=Richard+Jeffrey&amp;rft.btitle=Computability+and+Logic&amp;rft.date=1999&amp;rft.edition=3rd&amp;rft.genre=book&amp;rft.isbn=0-521-20402-X&amp;rft.place=Cambridge+UK&amp;rft.pub=Cambridge+University+Press&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

- Boolos, George; John Burgess; Richard Jeffrey (2002). _Computability and Logic_ (4th ed.). Cambridge UK: Cambridge University Press. [ISBN](https://en.wikipedia.org/wiki/International_Standard_Book_Number "International Standard Book Number") [0-521-00758-5](https://en.wikipedia.org/wiki/Special:BookSources/0-521-00758-5 "Special:BookSources/0-521-00758-5").

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.aufirst=George&amp;rft.au=John+Burgess&amp;rft.aulast=Boolos&amp;rft.au=Richard+Jeffrey&amp;rft.btitle=Computability+and+Logic&amp;rft.date=2002&amp;rft.edition=4th&amp;rft.genre=book&amp;rft.isbn=0-521-00758-5&amp;rft.place=Cambridge+UK&amp;rft.pub=Cambridge+University+Press&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

   Some parts have been significantly rewritten by Burgess. Presentation of Turing machines in context of Lambek "abacus machines" (cf [Register machine](https://en.wikipedia.org/wiki/Register_machine "Register machine")) and [recursive functions](https://en.wikipedia.org/wiki/Computable_function "Computable function"), showing their equivalence.
- [Taylor L. Booth](https://en.wikipedia.org/wiki/Taylor_L._Booth "Taylor L. Booth") (1967), _Sequential Machines and Automata Theory_, John Wiley and Sons, Inc., New York. Graduate level engineering text; ranges over a wide variety of topics, Chapter IX _Turing Machines_ includes some recursion theory.
- [Martin Davis](https://en.wikipedia.org/wiki/Martin_Davis "Martin Davis") (1958). _Computability and Unsolvability_. McGraw-Hill Book Company, Inc, New York.

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.au=Martin+Davis&amp;rft.btitle=Computability+and+Unsolvability&amp;rft.date=1958&amp;rft.genre=book&amp;rft.pub=McGraw-Hill+Book+Company%2C+Inc%2C+New+York&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

  . On pages 12–20 he gives examples of 5-tuple tables for Addition, The Successor Function, Subtraction (x ≥ y), Proper Subtraction (0 if x < y), The Identity Function and various identity functions, and Multiplication.
- Davis, Martin; Ron Sigal; Elaine J. Weyuker (1994). _Computability, Complexity, and Languages and Logic: Fundamentals of Theoretical Computer Science_ (2nd ed.). San Diego: Academic Press, Harcourt, Brace & Company. [ISBN](https://en.wikipedia.org/wiki/International_Standard_Book_Number "International Standard Book Number") [0-12-206382-1](https://en.wikipedia.org/wiki/Special:BookSources/0-12-206382-1 "Special:BookSources/0-12-206382-1").

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.au=Elaine+J.+Weyuker&amp;rft.aufirst=Martin&amp;rft.aulast=Davis&amp;rft.au=Ron+Sigal&amp;rft.btitle=Computability%2C+Complexity%2C+and+Languages+and+Logic%3A+Fundamentals+of+Theoretical+Computer+Science&amp;rft.date=1994&amp;rft.edition=2nd&amp;rft.genre=book&amp;rft.isbn=0-12-206382-1&amp;rft.place=San+Diego&amp;rft.pub=Academic+Press%2C+Harcourt%2C+Brace+%26+Company&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

- Hennie, Fredrick (1977). _Introduction to Computability_. Addison–Wesley, Reading, Mass. QA248.5H4 1977.

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.aufirst=Fredrick&amp;rft.aulast=Hennie&amp;rft.btitle=Introduction+to+Computability&amp;rft.date=1977&amp;rft.genre=book&amp;rft.pub=Addison%93Wesley%2C+Reading%2C+Mass&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

  . On pages 90–103 Hennie discusses the UTM with examples and flow-charts, but no actual 'code'.
- [John Hopcroft](https://en.wikipedia.org/wiki/John_Hopcroft "John Hopcroft") and [Jeffrey Ullman](https://en.wikipedia.org/wiki/Jeffrey_Ullman "Jeffrey Ullman"), (1979). _[Introduction to Automata Theory, Languages, and Computation](https://en.wikipedia.org/wiki/Introduction_to_Automata_Theory,_Languages,_and_Computation "Introduction to Automata Theory, Languages, and Computation")_ (1st ed.). Addison–Wesley, Reading Mass. [ISBN](https://en.wikipedia.org/wiki/International_Standard_Book_Number "International Standard Book Number") [0-201-02988-X](https://en.wikipedia.org/wiki/Special:BookSources/0-201-02988-X "Special:BookSources/0-201-02988-X").

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.au=John+Hopcroft+and+Jeffrey+Ullman%2C&amp;rft.btitle=Introduction+to+Automata+Theory%2C+Languages%2C+and+Computation&amp;rft.date=1979&amp;rft.edition=1st&amp;rft.genre=book&amp;rft.isbn=0-201-02988-X&amp;rft.pub=Addison%93Wesley%2C+Reading+Mass&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

   A difficult book. Centered around the issues of machine-interpretation of "languages", NP-completeness, etc.
- Hopcroft, John E.; Rajeev Motwani; Jeffrey D. Ullman (2001). _Introduction to Automata Theory, Languages, and Computation_ (2nd ed.). Reading Mass: Addison–Wesley. [ISBN](https://en.wikipedia.org/wiki/International_Standard_Book_Number "International Standard Book Number") [0-201-44124-1](https://en.wikipedia.org/wiki/Special:BookSources/0-201-44124-1 "Special:BookSources/0-201-44124-1").

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.aufirst=John+E.&amp;rft.au=Jeffrey+D.+Ullman&amp;rft.aulast=Hopcroft&amp;rft.au=Rajeev+Motwani&amp;rft.btitle=Introduction+to+Automata+Theory%2C+Languages%2C+and+Computation&amp;rft.date=2001&amp;rft.edition=2nd&amp;rft.genre=book&amp;rft.isbn=0-201-44124-1&amp;rft.place=Reading+Mass&amp;rft.pub=Addison%93Wesley&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

   Distinctly different and less intimidating than the first edition.
- [Stephen Kleene](https://en.wikipedia.org/wiki/Stephen_Kleene "Stephen Kleene") (1952), _Introduction to Metamathematics_, North–Holland Publishing Company, Amsterdam Netherlands, 10th impression (with corrections of 6th reprint 1971). Graduate level text; most of Chapter XIII _Computable functions_ is on Turing machine proofs of computability of recursive functions, etc.
- [Knuth, Donald E.](https://en.wikipedia.org/wiki/Donald_Knuth "Donald Knuth") (1973). _Volume 1/Fundamental Algorithms: The Art of computer Programming_ (2nd ed.). Reading, Mass.: Addison–Wesley Publishing Company.

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.aufirst=Donald+E.&amp;rft.aulast=Knuth&amp;rft.btitle=Volume+1%2FFundamental+Algorithms%3A+The+Art+of+computer+Programming&amp;rft.date=1973&amp;rft.edition=2nd&amp;rft.genre=book&amp;rft.place=Reading%2C+Mass.&amp;rft.pub=Addison%93Wesley+Publishing+Company&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

  . With reference to the role of Turing machines in the development of computation (both hardware and software) see 1.4.5 _History and Bibliography_ pp. 225ff and 2.6 _History and Bibliography_pp. 456ff.
- [Zohar Manna](https://en.wikipedia.org/wiki/Zohar_Manna "Zohar Manna"), 1974, _[Mathematical Theory of Computation](https://en.wikipedia.org/w/index.php?title=Mathematical_Theory_of_Computation&action=edit&redlink=1 "Mathematical Theory of Computation (page does not exist)")_. Reprinted, Dover, 2003\. [ISBN 978-0-486-43238-0](https://en.wikipedia.org/wiki/Special:BookSources/9780486432380)
- [Marvin Minsky](https://en.wikipedia.org/wiki/Marvin_Minsky "Marvin Minsky"), _Computation: Finite and Infinite Machines_, Prentice–Hall, Inc., N.J., 1967\. See Chapter 8, Section 8.2 "Unsolvability of the Halting Problem." Excellent, i.e. relatively readable, sometimes funny.
- [Christos Papadimitriou](https://en.wikipedia.org/wiki/Christos_Papadimitriou "Christos Papadimitriou") (1993). _Computational Complexity_ (1st ed.). Addison Wesley. [ISBN](https://en.wikipedia.org/wiki/International_Standard_Book_Number "International Standard Book Number") [0-201-53082-1](https://en.wikipedia.org/wiki/Special:BookSources/0-201-53082-1 "Special:BookSources/0-201-53082-1").

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.au=Christos+Papadimitriou&amp;rft.btitle=Computational+Complexity&amp;rft.date=1993&amp;rft.edition=1st&amp;rft.genre=book&amp;rft.isbn=0-201-53082-1&amp;rft.pub=Addison+Wesley&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

   Chapter 2: Turing machines, pp. 19–56.
- [Hartley Rogers, Jr.](https://en.wikipedia.org/wiki/Hartley_Rogers,_Jr. "Hartley Rogers, Jr."), _Theory of Recursive Functions and Effective Computability_, The MIT Press, Cambridge MA, paperback edition 1987, original McGraw-Hill edition 1967, [ISBN 0-262-68052-1](https://en.wikipedia.org/wiki/Special:BookSources/0262680521) (pbk.)
- [Michael Sipser](https://en.wikipedia.org/wiki/Michael_Sipser "Michael Sipser") (1997). _Introduction to the Theory of Computation_. PWS Publishing. [ISBN](https://en.wikipedia.org/wiki/International_Standard_Book_Number "International Standard Book Number") [0-534-94728-X](https://en.wikipedia.org/wiki/Special:BookSources/0-534-94728-X "Special:BookSources/0-534-94728-X").

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.au=Michael+Sipser&amp;rft.btitle=Introduction+to+the+Theory+of+Computation&amp;rft.date=1997&amp;rft.genre=book&amp;rft.isbn=0-534-94728-X&amp;rft.pub=PWS+Publishing&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

   Chapter 3: The Church–Turing Thesis, pp. 125–149.
- Stone, Harold S. (1972). _Introduction to Computer Organization and Data Structures_ (1st ed.). New York: McGraw–Hill Book Company. [ISBN](https://en.wikipedia.org/wiki/International_Standard_Book_Number "International Standard Book Number") [0-07-061726-0](https://en.wikipedia.org/wiki/Special:BookSources/0-07-061726-0 "Special:BookSources/0-07-061726-0").

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.aufirst=Harold+S.&amp;rft.aulast=Stone&amp;rft.btitle=Introduction+to+Computer+Organization+and+Data+Structures&amp;rft.date=1972&amp;rft.edition=1st&amp;rft.genre=book&amp;rft.isbn=0-07-061726-0&amp;rft.place=New+York&amp;rft.pub=McGraw%93Hill+Book+Company&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

- [Peter van Emde Boas](https://en.wikipedia.org/wiki/Peter_van_Emde_Boas "Peter van Emde Boas") 1990, _Machine Models and Simulations_, pp. 3–66, in [Jan van Leeuwen](https://en.wikipedia.org/wiki/Jan_van_Leeuwen "Jan van Leeuwen"), ed., _Handbook of Theoretical Computer Science, Volume A: Algorithms and Complexity_, The MIT Press/Elsevier, [place?], [ISBN 0-444-88071-2](https://en.wikipedia.org/wiki/Special:BookSources/0444880712) (Volume A). QA76.H279 1990\. Valuable survey, with 141 references.

### <span id="Church.27s_thesis" class="mw-headline">Church’s thesis</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=27" title="Edit section: Church's thesis">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

- Nachum Dershowitz; Yuri Gurevich (September 2008). ["A natural axiomatization of computability and proof of Church's Thesis"](http://research.microsoft.com/en-us/um/people/gurevich/Opera/188.pdf) 

  <span>(PDF)</span>

  . _Bulletin of Symbolic Logic_ **14** (3)

  <span class="reference-accessdate">. Retrieved <span class="nowrap">2008-10-15</span></span>

  .

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.atitle=A+natural+axiomatization+of+computability+and+proof+of+Church%27s+Thesis&amp;rft.au=Nachum+Dershowitz&amp;rft.au=Yuri+Gurevich&amp;rft.date=2008-09&amp;rft.genre=article&amp;rft_id=http%3A%2F%2Fresearch.microsoft.com%2Fen-us%2Fum%2Fpeople%2Fgurevich%2FOpera%2F188.pdf&amp;rft.issue=3&amp;rft.jtitle=Bulletin+of+Symbolic+Logic&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Ajournal&amp;rft.volume=14">
    <span>
  </span>
  </span>

- [Roger Penrose](https://en.wikipedia.org/wiki/Roger_Penrose "Roger Penrose") (1990) [1989]. _The Emperor's New Mind_ (2nd ed.). Oxford University Press, New York. [ISBN](https://en.wikipedia.org/wiki/International_Standard_Book_Number "International Standard Book Number") [0-19-851973-7](https://en.wikipedia.org/wiki/Special:BookSources/0-19-851973-7 "Special:BookSources/0-19-851973-7").

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.au=Roger+Penrose&amp;rft.btitle=The+Emperor%27s+New+Mind&amp;rft.date=1990&amp;rft.edition=2nd&amp;rft.genre=book&amp;rft.isbn=0-19-851973-7&amp;rft.pub=Oxford+University+Press%2C+New+York&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

### <span id="Small_Turing_machines" class="mw-headline">Small Turing machines</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=28" title="Edit section: Small Turing machines">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

- Rogozhin, Yurii, 1998, "[A Universal Turing Machine with 22 States and 2 Symbols](http://web.archive.org/web/20050308141040/http://www.imt.ro/Romjist/Volum1/Vol1_3/turing.htm)", _Romanian Journal Of Information Science and Technology_, 1(3), 259–265, 1998\. (surveys known results about small universal Turing machines)
- [Stephen Wolfram](https://en.wikipedia.org/wiki/Stephen_Wolfram "Stephen Wolfram"), 2002, [_A New Kind of Science_](http://www.wolframscience.com/nksonline/page-707), Wolfram Media, [ISBN 1-57955-008-8](https://en.wikipedia.org/wiki/Special:BookSources/1579550088)
- Brunfiel, Geoff, [Student snags maths prize](http://www.nature.com/news/2007/071024/full/news.2007.190.html), _Nature_, October 24\. 2007.
- Jim Giles (2007), [Simplest 'universal computer' wins student $25,000](http://technology.newscientist.com/article/dn12826-simplest-universal-computer-wins-student-25000.html), New Scientist, October 24, 2007.
- Alex Smith, [Universality of Wolfram's 2, 3 Turing Machine](http://www.wolframscience.com/prizes/tm23/TM23Proof.pdf), Submission for the Wolfram 2, 3 Turing Machine Research Prize.
- Vaughan Pratt, 2007, "[Simple Turing machines, Universality, Encodings, etc.](http://cs.nyu.edu/pipermail/fom/2007-October/012156.html)", FOM email list. October 29, 2007.
- Martin Davis, 2007, "[Smallest universal machine](http://cs.nyu.edu/pipermail/fom/2007-October/012132.html)", and [Definition of universal Turing machine](http://cs.nyu.edu/pipermail/fom/2007-October/012145.html) FOM email list. October 26–27, 2007.
- Alasdair Urquhart, 2007 "[Smallest universal machine](http://cs.nyu.edu/pipermail/fom/2007-October/012140.html)", FOM email list. October 26, 2007.
- Hector Zenil (Wolfram Research), 2007 "[smallest universal machine](http://cs.nyu.edu/pipermail/fom/2007-October/012163.html)", FOM email list. October 29, 2007.
- Todd Rowland, 2007, "[Confusion on FOM](http://forum.wolframscience.com/showthread.php?s=&threadid=1472)", Wolfram Science message board, October 30, 2007.
- Olivier and Marc RAYNAUD, 2014, [A programmable prototype to achieve Turing machines](http://www.machinedeturing.com/ang_default.asp)" LIMOS Laboratory of Blaise Pascal University (Clermont-Ferrand in France).

### <span id="Other" class="mw-headline">Other</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=29" title="Edit section: Other">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

- [Martin Davis](https://en.wikipedia.org/wiki/Martin_Davis "Martin Davis") (2000). _Engines of Logic: Mathematicians and the origin of the Computer_ (1st ed.). W. W. Norton & Company, New York. [ISBN](https://en.wikipedia.org/wiki/International_Standard_Book_Number "International Standard Book Number") [0-393-32229-7](https://en.wikipedia.org/wiki/Special:BookSources/0-393-32229-7 "Special:BookSources/0-393-32229-7").

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.au=Martin+Davis&amp;rft.btitle=Engines+of+Logic%3A+Mathematicians+and+the+origin+of+the+Computer&amp;rft.date=2000&amp;rft.edition=1st&amp;rft.genre=book&amp;rft.isbn=0-393-32229-7&amp;rft.pub=W.+W.+Norton+%26+Company%2C+New+York&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

- [Robin Gandy](https://en.wikipedia.org/wiki/Robin_Gandy "Robin Gandy"), "The Confluence of Ideas in 1936", pp. 51–102 in [Rolf Herken](https://en.wikipedia.org/w/index.php?title=Rolf_Herken&action=edit&redlink=1 "Rolf Herken (page does not exist)"), see below.
- [Stephen Hawking](https://en.wikipedia.org/wiki/Stephen_Hawking "Stephen Hawking") (editor), 2005, _God Created the Integers: The Mathematical Breakthroughs that Changed History_, Running Press, Philadelphia, [ISBN 978-0-7624-1922-7](https://en.wikipedia.org/wiki/Special:BookSources/9780762419227). Includes Turing's 1936–1937 paper, with brief commentary and biography of Turing as written by Hawking.
- Rolf Herken (1995). _The Universal Turing Machine--A Half-Century Survey_. Springer Verlag. [ISBN](https://en.wikipedia.org/wiki/International_Standard_Book_Number "International Standard Book Number") [3-211-82637-8](https://en.wikipedia.org/wiki/Special:BookSources/3-211-82637-8 "Special:BookSources/3-211-82637-8").

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.au=Rolf+Herken&amp;rft.btitle=The+Universal+Turing+Machine%94A+Half-Century+Survey&amp;rft.date=1995&amp;rft.genre=book&amp;rft.isbn=3-211-82637-8&amp;rft.pub=Springer+Verlag&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

- [Andrew Hodges](https://en.wikipedia.org/wiki/Andrew_Hodges "Andrew Hodges"), _Alan Turing: The Enigma_, Simon and Schuster, New York. Cf Chapter "The Spirit of Truth" for a history leading to, and a discussion of, his proof.
- [Ivars Peterson](https://en.wikipedia.org/wiki/Ivars_Peterson "Ivars Peterson") (1988). _The Mathematical Tourist: Snapshots of Modern Mathematics_ (1st ed.). W. H. Freeman and Company, New York. [ISBN](https://en.wikipedia.org/wiki/International_Standard_Book_Number "International Standard Book Number") [0-7167-2064-7](https://en.wikipedia.org/wiki/Special:BookSources/0-7167-2064-7 "Special:BookSources/0-7167-2064-7").

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.au=Ivars+Peterson&amp;rft.btitle=The+Mathematical+Tourist%3A+Snapshots+of+Modern+Mathematics&amp;rft.date=1988&amp;rft.edition=1st&amp;rft.genre=book&amp;rft.isbn=0-7167-2064-7&amp;rft.pub=W.+H.+Freeman+and+Company%2C+New+York&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

- [Roger Penrose](https://en.wikipedia.org/wiki/Roger_Penrose "Roger Penrose"), _The Emporer's New Mind: Concerning Computers, Minds, and the Laws of Physics_, Oxford University Press, Oxford and New York, 1989 (1990 corrections), [ISBN 0-19-851973-7](https://en.wikipedia.org/wiki/Special:BookSources/0198519737).
- [Paul Strathern](https://en.wikipedia.org/wiki/Paul_Strathern "Paul Strathern") (1997). _Turing and the Computer--The Big Idea_. Anchor Books/Doubleday. [ISBN](https://en.wikipedia.org/wiki/International_Standard_Book_Number "International Standard Book Number") [0-385-49243-X](https://en.wikipedia.org/wiki/Special:BookSources/0-385-49243-X "Special:BookSources/0-385-49243-X").

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.au=Paul+Strathern&amp;rft.btitle=Turing+and+the+Computer%94The+Big+Idea&amp;rft.date=1997&amp;rft.genre=book&amp;rft.isbn=0-385-49243-X&amp;rft.pub=Anchor+Books%2FDoubleday&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

- [Hao Wang](https://en.wikipedia.org/wiki/Hao_Wang_(academic) "Hao Wang (academic)"), "A variant to Turing's theory of computing machines", _Journal of the Association for Computing Machinery_ (JACM) 4, 63–92 (1957).
- [Charles Petzold](https://en.wikipedia.org/wiki/Charles_Petzold "Charles Petzold"), [Petzold, Charles, _The Annotated Turing_](http://www.theannotatedturing.com/), John Wiley & Sons, Inc., [ISBN 0-470-22905-5](https://en.wikipedia.org/wiki/Special:BookSources/0470229055)
- Arora, Sanjeev; Barak, Boaz, ["Complexity Theory: A Modern Approach"](http://www.cs.princeton.edu/theory/complexity/), Cambridge University Press, 2009, [ISBN 978-0-521-42426-4](https://en.wikipedia.org/wiki/Special:BookSources/9780521424264), section 1.4, "Machines as strings and the universal Turing machine" and 1.7, "Proof of theorem 1.9"
- Kantorovitz, Isaiah Pinchas (December 1, 2005). "A note on turing machine computability of rule driven systems". _[SIGACT News](https://en.wikipedia.org/wiki/SIGACT_News "SIGACT News")_ (ACM) **36** (4): 109–110\. [doi](https://en.wikipedia.org/wiki/Digital_object_identifier "Digital object identifier"):[10.1145/1107523.1107525](https://dx.doi.org/10.1145%2F1107523.1107525).

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.atitle=A+note+on+turing+machine+computability+of+rule+driven+systems&amp;rft.aufirst=Isaiah+Pinchas&amp;rft.aulast=Kantorovitz&amp;rft.date=2005-12-01&amp;rft.genre=article&amp;rft_id=info%3Adoi%2F10.1145%2F1107523.1107525&amp;rft.issue=4&amp;rft.jtitle=SIGACT+News&amp;rft.pages=109-110&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Ajournal&amp;rft.volume=36">
    <span>
  </span>
  </span>

   

  <span class="error citation-comment"><code>|access-date=</code> requires <code>|url=</code> (<a href="https://en.wikipedia.org/wiki/Help:CS1_errors#accessdate_missing_url" title="Help:CS1 errors">help</a>)</span>

## <span id="External_links" class="mw-headline">External links</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Turing_machine&amp;action=edit&amp;section=30" title="Edit section: External links">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

![](https://upload.wikimedia.org/wikipedia/en/thumb/4/4a/Commons-logo.svg/30px-Commons-logo.svg.png) | Wikimedia Commons has media related to _**[Turing machines](https://commons.wikimedia.org/wiki/Category:Turing_machines "commons:Category:Turing machines")**_.
---------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------

- Hazewinkel, Michiel, ed. (2001), ["Turing machine"](http://www.encyclopediaofmath.org/index.php?title=p/t094460), _[Encyclopedia of Mathematics](https://en.wikipedia.org/wiki/Encyclopedia_of_Mathematics "Encyclopedia of Mathematics")_, [Springer](https://en.wikipedia.org/wiki/Springer_Science%2BBusiness_Media "Springer Science+Business Media"), [ISBN](https://en.wikipedia.org/wiki/International_Standard_Book_Number "International Standard Book Number") [978-1-55608-010-4](https://en.wikipedia.org/wiki/Special:BookSources/978-1-55608-010-4 "Special:BookSources/978-1-55608-010-4")

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ATuring+machine&amp;rft.atitle=Turing+machine&amp;rft.btitle=Encyclopedia+of+Mathematics&amp;rft.date=2001&amp;rft.genre=bookitem&amp;rft_id=http%3A%2F%2Fwww.encyclopediaofmath.org%2Findex.php%3Ftitle%3Dp%2Ft094460&amp;rft.isbn=978-1-55608-010-4&amp;rft.pub=Springer&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

- [Turing Machine on Stanford Encyclopedia of Philosophy](http://plato.stanford.edu/entries/turing-machine/)
- [Detailed info on the Church–Turing Hypothesis](http://plato.stanford.edu/entries/church-turing/) (Stanford Encyclopedia of Philosophy)
- [Web Turing Machine](http://webturingmachine.appspot.com/) Web application to construct and execute Turing machines (Javascript)
- [Turing Machine-Like Models](http://www.weizmann.ac.il/mathusers/lbn/new_pages/Research_Turing.html) in Molecular Biology, to understand life mechanisms with a DNA-tape processor.
- [The Turing machine](http://www.SaschaSeidel.de/html/programmierung/download_The_Turing_machine.php)--Summary about the Turing machine, its functionality and historical facts
- [The Wolfram 2,3 Turing Machine Research Prize](http://www.wolframscience.com/prizes/tm23/)--Stephen Wolfram's $25,000 prize for the proof or disproof of the universality of the potentially smallest universal Turing Machine. The contest has ended, with the proof affirming the machine's universality.
- "[Turing Machine Causal Networks](http://demonstrations.wolfram.com/TuringMachineCausalNetworks/)" by Enrique Zeleny, [Wolfram Demonstrations Project](https://en.wikipedia.org/wiki/Wolfram_Demonstrations_Project "Wolfram Demonstrations Project").
- [Turing Machines](https://www.dmoz.org/Computers/Computer_Science/Theoretical/Automata_Theory/Turing_Machines) at [DMOZ](https://en.wikipedia.org/wiki/DMOZ "DMOZ")
- [Purely mechanical Turing Machine](http://www.turing2012.fr/?p=530&lang=en)
- [JSTMSimulator](http://www.turingsimulator.net/): an open source Turing Machine simulator, written in JavaScript. ([source code on GitHub](http://www.turingsimulator.net/github))

![](https://en.wikipedia.org/wiki/Special:CentralAutoLogin/start?type=1x1)

Retrieved from "<https://en.wikipedia.org/w/index.php?title=Turing_machine&oldid=707292746>"

[Categories](https://en.wikipedia.org/wiki/Help:Category "Help:Category"):

- [1937 in computer science](<https://en.wikipedia.org/wiki/Category:1937_in_computer_science> "Categor
y:1937 in computer science")

- [Turing machine](https://en.wikipedia.org/wiki/Category:Turing_machine "Category:Turing machine")
- [Educational abstract machines](https://en.wikipedia.org/wiki/Category:Educational_abstract_machines "Category:Educational abstract machines")
- [Theoretical computer science](https://en.wikipedia.org/wiki/Category:Theoretical_computer_science "Category:Theoretical computer science")
- [Alan Turing](https://en.wikipedia.org/wiki/Category:Alan_Turing "Category:Alan Turing")
- [Models of computation](https://en.wikipedia.org/wiki/Category:Models_of_computation "Category:Models of computation")
- [Formal methods](https://en.wikipedia.org/wiki/Category:Formal_methods "Category:Formal methods")
- [Computability theory](https://en.wikipedia.org/wiki/Category:Computability_theory "Category:Computability theory")
- [English inventions](https://en.wikipedia.org/wiki/Category:English_inventions "Category:English inventions")
- [Automata (computation)](https://en.wikipedia.org/wiki/Category:Automata_(computation) "Category:Automata (computation)")
- [Formal languages](https://en.wikipedia.org/wiki/Category:Formal_languages "Category:Formal languages")
