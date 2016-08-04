# Random-access machine

[Original URL](https://en.wikipedia.org/wiki/Random-access_machine)

> This article is about the abstract machine. For other uses, see Ram. Not to be confused with Random-access memory. In computer science, random-access machine (RAM) is an abstract machine in the...

This article is about the abstract machine. For other uses, see [Ram](https://en.wikipedia.org/wiki/Ram_(disambiguation) "Ram (disambiguation)").

Not to be confused with [Random-access memory](https://en.wikipedia.org/wiki/Random-access_memory "Random-access memory").

In [computer science](https://en.wikipedia.org/wiki/Computer_science "Computer science"), **random-access machine** (RAM) is an [abstract machine](https://en.wikipedia.org/wiki/Abstract_machine "Abstract machine") in the general class of [register machines](https://en.wikipedia.org/wiki/Register_machine "Register machine"). The RAM is very similar to the [counter machine](https://en.wikipedia.org/wiki/Counter_machine "Counter machine") but with the added capability of 'indirect addressing' of its registers. Like the counter machine the RAM has its instructions in the finite-state portion of the machine (the so-called [Harvard architecture](https://en.wikipedia.org/wiki/Harvard_architecture "Harvard architecture")).

The RAM's equivalent of the [universal Turing machine](https://en.wikipedia.org/wiki/Universal_Turing_machine "Universal Turing machine") – with its [program](https://en.wikipedia.org/wiki/Computer_program "Computer program") in the registers as well as its data – is called the [random-access stored-program machine](https://en.wikipedia.org/wiki/Random-access_stored-program_machine "Random-access stored-program machine") or RASP. It is an example of the so-called [von Neumann architecture](https://en.wikipedia.org/wiki/Von_Neumann_architecture "Von Neumann architecture") and is closest to the common notion of [computer](https://en.wikipedia.org/wiki/Computer "Computer").

Together with the [Turing machine](https://en.wikipedia.org/wiki/Turing_machine "Turing machine") and [counter-machine models](https://en.wikipedia.org/wiki/Counter-machine_model "Counter-machine model"), the RAM and RASP models are used for [computational complexity analysis](https://en.wikipedia.org/wiki/Computational_complexity_analysis "Computational complexity analysis"). Van Emde Boas (1990) calls these three plus the [pointer machine](https://en.wikipedia.org/wiki/Pointer_machine "Pointer machine") "sequential machine" models, to distinguish them from "[parallel random-access machine](https://en.wikipedia.org/wiki/Parallel_random-access_machine "Parallel random-access machine")" models.

## Contents

- [<span class="tocnumber">1</span>

   

  <span class="toctext">Introduction to the model</span>](https://en.wikipedia.org/wiki/Random-access_machine#Introduction_to_the_model)

  - [<span class="tocnumber">1.1</span>

     

    <span class="toctext">Formal definition</span>](https://en.wikipedia.org/wiki/Random-access_machine#Formal_definition)

  - [<span class="tocnumber">1.2</span>

     

    <span class="toctext">Refresher: The counter-machine model</span>](https://en.wikipedia.org/wiki/Random-access_machine#Refresher:_The_counter-machine_model)

  - [<span class="tocnumber">1.3</span>

     

    <span class="toctext">Creating “convenience instructions” from the base sets</span>](https://en.wikipedia.org/wiki/Random-access_machine#Creating_.22convenience_instructions.22_from_the_base_sets)

- [<span class="tocnumber">2</span>

   

  <span class="toctext">The “indirect” operation</span>](https://en.wikipedia.org/wiki/Random-access_machine#The_.22indirect.22_operation)

  - [<span class="tocnumber">2.1</span>

     

    <span class="toctext">Example of indirect addressing</span>](https://en.wikipedia.org/wiki/Random-access_machine#Example_of_indirect_addressing)

  - [<span class="tocnumber">2.2</span>

     

    <span class="toctext">Why the need for an indirect operation: Two major problems with the counter-machine model</span>](https://en.wikipedia.org/wiki/Random-access_machine#Why_the_need_for_an_indirect_operation:_Two_major_problems_with_the_counter-machine_model)

  - [<span class="tocnumber">2.3</span>

     

    <span class="toctext">Bounded indirection and the primitive recursive functions</span>](https://en.wikipedia.org/wiki/Random-access_machine#Bounded_indirection_and_the_primitive_recursive_functions)

  - [<span class="tocnumber">2.4</span>

     

    <span class="toctext">Unbounded indirection and the partial recursive functions</span>](https://en.wikipedia.org/wiki/Random-access_machine#Unbounded_indirection_and_the_partial_recursive_functions)

  - [<span class="tocnumber">2.5</span>

     

    <span class="toctext">The indirect COPY instruction</span>](https://en.wikipedia.org/wiki/Random-access_machine#The_indirect_COPY_instruction)

- [<span class="tocnumber">3</span>

   

  <span class="toctext">The notion of “accumulator A”</span>](https://en.wikipedia.org/wiki/Random-access_machine#The_notion_of_.22accumulator_A.22)

- [<span class="tocnumber">4</span>

   

  <span class="toctext">The notion of indirect address register “N”</span>](https://en.wikipedia.org/wiki/Random-access_machine#The_notion_of_indirect_address_register_.22N.22)

- [<span class="tocnumber">5</span>

   

  <span class="toctext">Turing equivalence of the RAM with indirection</span>](https://en.wikipedia.org/wiki/Random-access_machine#Turing_equivalence_of_the_RAM_with_indirection)

- [<span class="tocnumber">6</span>

   

  <span class="toctext">Example: Bounded indirection yields a machine that is not Turing equivalent</span>](https://en.wikipedia.org/wiki/Random-access_machine#Example:_Bounded_indirection_yields_a_machine_that_is_not_Turing_equivalent)

- [<span class="tocnumber">7</span>

   

  <span class="toctext">Examples of models</span>](https://en.wikipedia.org/wiki/Random-access_machine#Examples_of_models)

  - [<span class="tocnumber">7.1</span>

     

    <span class="toctext">Register-to-register (“read-modify-write”) model of Cook and Reckhow (1973)</span>](https://en.wikipedia.org/wiki/Random-access_machine#Register-to-register_.28.22read-modify-write.22.29_model_of_Cook_and_Reckhow_.281973.29)

  - [<span class="tocnumber">7.2</span>

     

    <span class="toctext">Schönhage’s RAM0 and RAM1 (1980)</span>](https://en.wikipedia.org/wiki/Random-access_machine#Sch.C3.B6nhage.27s_RAM0_and_RAM1_.281980.29)

- [<span class="tocnumber">8</span>

   

  <span class="toctext">Footnotes</span>](https://en.wikipedia.org/wiki/Random-access_machine#Footnotes)

  - [<span class="tocnumber">8.1</span>

     

    <span class="toctext">Finite vs unbounded</span>](https://en.wikipedia.org/wiki/Random-access_machine#Finite_vs_unbounded)

- [<span class="tocnumber">9</span>

   

  <span class="toctext">See also</span>](https://en.wikipedia.org/wiki/Random-access_machine#See_also)

- [<span class="tocnumber">10</span>

   

  <span class="toctext">External links</span>](https://en.wikipedia.org/wiki/Random-access_machine#External_links)

- [<span class="tocnumber">11</span>

   

  <span class="toctext">References</span>](https://en.wikipedia.org/wiki/Random-access_machine#References)

## <span id="Introduction_to_the_model" class="mw-headline">Introduction to the model</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Random-access_machine&amp;action=edit&amp;section=1" title="Edit section: Introduction to the model">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

The concept of a [random-access](https://en.wikipedia.org/wiki/Random-access "Random-access") machine (RAM) starts with the simplest model of all, the so-called [counter machine](https://en.wikipedia.org/wiki/Counter_machine "Counter machine") model. Two additions move it away from the counter machine, however. The first enhances the machine with the convenience of indirect addressing; the second moves the model toward the more conventional accumulator-based [computer](https://en.wikipedia.org/wiki/Computer "Computer") with the addition of one or more auxiliary (dedicated) registers, the most common of which is called "the accumulator".

### <span id="Formal_definition" class="mw-headline">Formal definition</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Random-access_machine&amp;action=edit&amp;section=2" title="Edit section: Formal definition">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

A _random-access machine_ (RAM) is an abstract computational-machine model identical to a multiple-register [counter machine](https://en.wikipedia.org/wiki/Counter_machine "Counter machine") with the addition of indirect addressing. At the discretion of an instruction from its finite state machine's TABLE, the machine derives a "target" register's address either (i) directly from the instruction itself, or (ii) indirectly from the _contents_ (e.g. number, label) of the "pointer" register specified in the instruction.

By definition: A _register_ is a location with both an _address_ (a unique, distinguishable designation/locator equivalent to a natural number) and a _content_ – a single natural number. For precision we will use the quasi-formal symbolism from Boolos-Burgess-Jeffrey (2002) to specify a register, its contents, and an operation on a register:

- [r] means "the contents of register with address r". The label "r" here is a "variable" that can be filled with a natural number or a letter (e.g. "A") or a name.
- → means "copy/deposit into", or "replaces", but without destruction of the source

Example: [3] +1 → 3; means "The contents of source register with address "3", plus 1, is put into destination register with address "3" (here source and destination are the same place). If [3]=37, that is, the contents of register 3 is the number "37", then 37+1 = 38 will be put into register 3. Example: [3] → 5; means "The contents of source register with address "3" is put into destination register with address "5". If [3]=38, that is, the contents of register 3 is the number 38, then this number will be put into register 5\. The contents of register 3 are not disturbed by this operation, so [3] continues to be 38, now the same as [5]. Definition: A _direct_ instruction is one that specifies _in the instruction itself_ the address of the source or destination register whose contents will be the subject of the instruction. Definition: An _indirect instruction_ is one that specifies a "pointer register", the contents of which is the address of a "target" register. The target register can be either a source or a destination (the various COPY instructions provide examples of this). A register can address itself indirectly.

_For want of a standard/convention this article will specify "direct/indirect", abbreviated as "d/i", as a parameter (or parameters) in the instruction:_ _Example: COPY ( **d**, A, **i**, N ) means directly **d** get the source register's address (register "A") from the instruction itself but indirectly **i** get the destination address from pointer-register N. Suppose [N]=3, then register 3 is the destination and the instruction will do the following: [A] → 3._ Definition: The contents of _source register_ is used by the instruction. The source register's address can be specified either (i) directly by the instruction, or (ii) indirectly by the pointer register specified by the instruction.

Definition: The contents of the _pointer register_ is the _address_ of the "target" register.

Definition: The contents of the _pointer register_ points to the _target register_ – the "target" may be either a source or a destination register.

Definition: The _destination register_ is where the instruction deposits its result. The source register's address can be specified either (i) directly by the instruction, or (ii) indirectly by the pointer register specified by the instruction. The source and destination registers can be one

### <span id="Refresher:_The_counter-machine_model" class="mw-headline">Refresher: The counter-machine model</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Random-access_machine&amp;action=edit&amp;section=3" title="Edit section: Refresher: The counter-machine model">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

_Melzak (1961) provides an easy visualization of a counter machine: its "registers" are holes in the ground, and these holes hold pebbles. Per an instruction, into and out of these holes "the computer" (person or machine) adds (INCrements) or removes (DECrements) a single pebble. As needed, additional pebbles come from, and excess pebbles go back into, an infinite supply; if the hole is too small to accommodate the pebbles the "computer" digs the hole bigger._ _Minsky (1961) and Hopcroft-Ullman 1979 (p. 171) offer the visualization of a multi-tape [Turing machine](https://en.wikipedia.org/wiki/Turing_machine "Turing machine") with as many left-ended tapes as "registers". Each tape's length is unbounded to the right, and every square is blank except for the left end, which is marked. The_ distance _of a tape's "head" from its left end, measured in numbers of tape-squares, represents the natural number in "the register". To DECrement the count of squares the tape head moves left; INCrement it moves right. There is no need to print or erase marks on the tape; the only conditional instructions are to check to see if the head is at the left end, by testing a left-end mark with a "Jump-if-marked instruction"._ _The following instruction "mnemonics" e.g. "CLR (r)" are arbitrary; no standard exists._ The [register machine](https://en.wikipedia.org/wiki/Register_machine "Register machine") has, for a memory external to its finite-state machine – an unbounded (cf: footnote|countable and unbounded) collection of discrete and uniquely labelled locations with _unbounded_ capacity, called "registers". These registers hold only natural numbers (zero and the positive integers). Per a list of sequential instructions in the finite state machine's TABLE, a few (e.g. 2) types of primitive operations operate on the contents of these "registers". Finally, a _conditional-expression_ in the form of an _IF-THEN-ELSE_ is available to test the contents of one or two registers and "branch/jump" the finite state machine out of the default instruction-sequence.

**Base model 1**: The model closest to Minsky's (1961) visualization and to Lambek (1961):

- { INCrement contents of register r, DECrement contents of register r, _IF_ contents of register r is Zero _THEN_ Jump to instruction I

  <sub>z</sub>

   _ELSE_ continue to next instruction }:

Instruction  | Mnemonic    | Action on register(s) "r" | Action on finite state machine's Instruction Register, IR
------------ | ----------- | ------------------------- | ---------------------------------------------------------
INCrement    | INC ( r )   | [r] +1 → r                | [IR] +1 → IR
DECrement    | DEC ( r )   | [r] -1 → r                | [IR] +1 → IR
Jump if Zero | JZ ( r, z ) | none                      | IF [r1] = 0 THEN z → IR ELSE [IR] +1 → IR
Halt         | H           | none                      | [IR] → IR

**Base model 2**: The "successor" model (named after the successor function of the [Peano axioms](https://en.wikipedia.org/wiki/Peano_axioms "Peano axioms")):

- { INCrement the contents of register r, CLeaR the contents of register r, _IF_ contents of register r

  <sub>j</sub>

   Equals the contents of register r

  <sub>k</sub>

   _THEN_ Jump to instruction I

  <sub>z</sub>

   _ELSE_ goto to next instruction }

Instruction   | Mnemonic       | Action on register(s) "r" | Action on finite state machine's Instruction Register, IR
------------- | -------------- | ------------------------- | ---------------------------------------------------------
CLeaR         | CLR ( r )      | 0 → r                     | [IR] +1 → IR
INCrement     | INC ( r )      | [r] +1 → r                | [IR] +1 → IR
Jump if Equal | JE (r1, r2, z) | none                      | IF [r1] = [r2] THEN z → IR ELSE [IR] +1 → IR
Halt          | H              | none                      | [IR] → IR

**Base model 3**: Used by Elgot-Robinson (1964) in their investigation of bounded and unbounded RASPs – the "successor" model with COPY in the place of CLEAR:

- { INCrement the contents of register r, COPY the contents of register r

  <sub>j</sub>

   to register r

  <sub>k</sub>

  , _IF_ contents of register r

  <sub>j</sub>

   Equals the contents of register r

  <sub>k</sub>

   _then_ Jump to instruction I

  <sub>z</sub>

   _ELSE_ goto to next instruction }

Instruction   | Mnemonic       | Action on register(s) "r" | Action on finite state machine's Instruction Register, IR
------------- | -------------- | ------------------------- | ---------------------------------------------------------
COPY          | COPY (r1, r2)  | r1 → r2                   | [IR] +1 → IR
INCrement     | INC ( r )      | [r] +1 → r                | [IR] +1 → IR
Jump if Equal | JE (r1, r2, z) | none                      | IF [r1] = [r2] THEN z → IR ELSE [IR] +1 → IR
Halt          | H              | none                      | [IR] → IR

### <span id="Creating_.22convenience_instructions.22_from_the_base_sets" class="mw-headline">Creating “convenience instructions” from the base sets</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Random-access_machine&amp;action=edit&amp;section=4" title="Edit section: Creating &quot;convenience instructions&quot; from the base sets">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

The three base sets 1, 2, or 3 above are equivalent in the sense that one can create the instructions of one set using the instructions of another set (an interesting exercise: a hint from Minsky (1967) – declare a reserved register e.g. call it "0" (or Z for "zero" or E for "erase") to contain the number 0). The choice of model will depend on which an author finds easiest to use in a demonstration, or a proof, etc.

Moreover, from base sets 1, 2, or 3 we can create _any_ of the [primitive recursive functions](https://en.wikipedia.org/wiki/Primitive_recursive_function "Primitive recursive function") ( cf Minsky (1967), Boolos-Burgess-Jeffrey (2002) ). (How to cast the net wider to capture the _total_ and _partial_ [mu recursive functions](https://en.wikipedia.org/wiki/Mu_recursive_function "Mu recursive function") will be discussed in context of indirect addressing). However, building the primitive recursive functions is difficult because the instruction sets are so ... primitive (tiny). One solution is to expand a particular set with "convenience instructions" from another set:

_These will not be subroutines in the conventional sense but rather_ blocks _of instructions created from the base set and given a mnemonic. In a formal sense, to use these blocks we need to either (i) "expand" them into their base-instruction equivalents – they will require the use of temporary or "auxiliary" registers so the model must take this into account, or (ii) design our machines/models with the instructions 'built in'._ Example: Base set 1\. To create CLR (r) use the block of instructions to count down register r to zero. Observe the use of the hint mentioned above:

- CLR (r) =

  <sub>equiv</sub>

- _loop_: JZ (r, _exit_)

<!--  -->

- DEC (r)
- JZ (0, _loop_)

<!--  -->

- _exit_: etc.

Again, all of this is for convenience only; none of this increases the model's intrinsic power.

For example: the most expanded set would include each unique instruction from the three sets, plus unconditional jump J (z) i.e.:

- { CLR (r), DEC (r), INC (r), CPY ( r

  <sub>s</sub>

  , r

  <sub>d</sub>

   ), JZ (r, z), JE ( r

  <sub>j</sub>

  , r

  <sub>k</sub>

  , z ), J(z) }

Most authors pick one or the other of the conditional jumps, e.g. Shepherdson-Sturgis (1963) use the above set minus JE (to be perfectly accurate they use JNZ – Jump if _Not_ Zero in place of JZ; yet another possible convenience instruction).

## <span id="The_.22indirect.22_operation" class="mw-headline">The “indirect” operation</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Random-access_machine&amp;action=edit&amp;section=5" title="Edit section: The &quot;indirect&quot; operation">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

### <span id="Example_of_indirect_addressing" class="mw-headline">Example of indirect addressing</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Random-access_machine&amp;action=edit&amp;section=6" title="Edit section: Example of indirect addressing">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

In our daily lives the notion of an "indirect operation" is not unusual.

Example: A treasure hunt. At location "Tom_&_Becky's_cave_in_pirate_chest" will be where we can find a map directing us to "the treasure": (1) We go to location "Tom_&_Becky's_cave..." and dig around until we find a wooden box (2) Inside the box is a map to the location of the treasure: "under_Thatcher's_front_porch" (3) We go to location "under_Thatcher's_front_porch", jackhammer away the concrete, and discover "the treasure": a sack of rusty door-knobs. [Indirection](https://en.wikipedia.org/wiki/Indirection "Indirection") specifies a location identified as the pirate chest in "Tom_&_Becky's_cave..." that acts as a _pointer_ to any other location (including itself): its contents (the treasure map) provides the "address" of the _target_ location "under_Thatcher's_front_porch" where the real action is occurring.

### <span id="Why_the_need_for_an_indirect_operation:_Two_major_problems_with_the_counter-machine_model" class="mw-headline">Why the need for an indirect operation: Two major problems with the counter-machine model</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Random-access_machine&amp;action=edit&amp;section=7" title="Edit section: Why the need for an indirect operation: Two major problems with the counter-machine model">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

In the following one must remember that these models are abstract models with two fundamental differences from anything physically real: unbounded numbers of registers each with unbounded capacities. The problem appears most dramatically when one tries to use a counter-machine model to build a RASP that is [Turing equivalent](https://en.wikipedia.org/wiki/Turing_completeness "Turing completeness") and thus compute any partial [mu recursive function](https://en.wikipedia.org/wiki/Mu_recursive_function "Mu recursive function"):

- _Melzak (1961) added indirection to his "hole-and-pebble" model so that his model could modify itself with a "computed goto" and provides two examples of its use ("Decimal representation in the scale of d" and "Sorting by magnitude", whether these are used in his proof that the model is Turing equivalent is unclear since "the program itself is left to the reader as an exercise" (p. 292)). Minsky (1961, 1967) was able to demonstrate that, with suitable (but difficult-to-use) [Gödel number](https://en.wikipedia.org/wiki/G%C3%B6del_number "Gödel number") encoding, the register model did not need indirection to be Turing equivalent; but it did need at least one unbounded register. As noted below, Minsky (1967) hints at the problem for a RASP but doesn't offer a solution. Elgot and Robinson (1964) proved that their RASP model P

  <sub>0</sub>

   – it has no indirection capability – cannot compute all "recursive sequential functions" (ones that have parameters of arbitrary length) if it does not have the capability of modifying its own instructions, but it can via Gödel numbers if it does (p. 395-397; in particular figure 2 and footnote p. 395). On the other hand their RASP model P'

  <sub>0</sub>

   equipped with an "index register" (indirect addressing) can compute all the "partial recursive sequential functions" ( the mu recursive functions ) ( p. 397-398)._

_Cook and Reckhow (1973) say it most succinctly:_ _The indirect instructions are necessary in order for a fixed program to access an unbounded number of registers as the inputs vary." (p. 73)_

- **Unbounded _capacities_ of registers versus bounded capacities of state-machine instructions**: The so-called _finite_ state part of the machine is supposed to be – by the normal definition of algorithm – _very_ finite both in the number of "states" (instructions) and the instructions' sizes (their capacity to hold symbols/signs). So how does a state machine move an arbitrarily large constant directly into a register, e.g. MOVE (k, r) (Move constant k to register r)? If huge constants are necessary they must either start out in the registers themselves or be created by the state machine using a finite number of instructions e.g. multiply and add subroutines using INC and DEC (but not a quasi-infinite number of these!).

_Sometimes the constant k will be created by use of CLR ( r ) followed by INC ( r ) repeated k times – e.g. to put the constant k=3 into register r, i.e. 3 → r, so at the end of the instruction [r]=3: CLR (r), INC (r), INC (r), INC (r). This trick is mentioned by Kleene (1952) p. 223\. The problem arises when the number to be created exhausts the number of instructions available to the_ finite _state machine; there is always a bigger constant than the number of instructions available to the_ finite _state machine._

- **Unbounded _numbers_ of registers versus bounded state-machine instructions**: This is more severe than the first problem. In particular, this problem arises when we attempt to build a so-called RASP, a "universal machine" (see more at [Universal Turing machine](https://en.wikipedia.org/wiki/Universal_Turing_machine "Universal Turing machine")) that uses its finite-state machine to interpret a "program of instructions" located in its registers – i.e. we are building what is nowadays called a [computer](https://en.wikipedia.org/wiki/Computer "Computer") with the [von Neumann architecture](https://en.wikipedia.org/wiki/Von_Neumann_architecture "Von Neumann architecture").

Observe that the counter machine's finite state machine must call out a register _explicitly_ (directly) by its name/number: INC (65,356) calls out register number "65,365" _explicitly_. If the number of registers exceeds the capability of the _finite_ state machine to address them, then registers outside the bounds will be unreachable. For example, if the finite state machine can only reach 65,536 = 2

<sup>16</sup>

 registers then how can it reach the 65,537th? So how _do_ we address a register beyond the bounds of the finite state machine? One approach would be to modify the _program_-instructions (the ones stored in the registers) so that they contain more than one command. But this too can be exhausted unless an instruction is of (potentially) unbounded size. So why not use just one "über-instruction" – one really really big number – that contains _all_ the program instructions encoded into it! This is how Minsky solves the problem, but the [Gödel numbering](https://en.wikipedia.org/wiki/G%C3%B6del_number "Gödel number") he uses represents a great inconvenience to the model, and the result is nothing at all like our intuitive notion of a "stored program computer".

Elgot and Robinson (1964) come to a similar conclusion with respect to a RASP that is "finitely determined". Indeed it can access an unbounded number of registers (e.g. to fetch instructions from them) but only if the RASP allows "self modification" of its _program_ instructions, and has encoded its "data" in a Gödel number (Fig. 2 p. 396).

In the context of a more computer-like model using his RPT (repeat) instruction Minsky (1967) tantalizes us with a solution to the problem (cf p. 214, p. 259) but offers no firm resolution. He asserts:

"In general a RPT operation could not be an instruction in the finite-state part of the machine ... this might exhaust any particular amount of storage allowed in the finite part of the computer [sic, his name for his RAM models]. RPT operations require infinite registers of their own." (p. 214). He offers us a _bounded_ RPT that together with CLR (r) and INC (r) can compute any [primitive recursive function](https://en.wikipedia.org/wiki/Primitive_recursive_function "Primitive recursive function"), and he offers the unbounded RPT quoted above that as playing the role of μ operator; it together with CLR (r) and INC (r) can compute the mu recursive functions. But he does not discuss "indirection" or the RAM model per se.

From the references in Hartmanis (1971) it appears that Cook (in his lecture notes while at UC Berkeley, 1970) has firmed up the notion of indirect addressing. This becomes clearer in the paper of Cook and Reckhow (1973) – Cook is Reckhow's Master's thesis advisor. Hartmanis' model – quite similar to Melzak's (1961) model – uses two and three-register adds and subtracts and two parameter copies; Cook and Reckhow's model reduce the number of parameters (registers called out in the program instructions) to one call-out by use of an accumulator "AC".

**The solution in a nutshell:** Design our machine/model with unbounded **indirection** – provide an _unbounded_ "address" register that can potentially name (call out) any register no matter how many there are. For this to work, in general, the _unbounded_ register requires an ability to be cleared and then incremented (and, possibly, decremented) by a potentially infinite loop. In this sense the solution represents the unbounded [μ operator](https://en.wikipedia.org/wiki/%CE%9C_operator "Μ operator") that can, if necessary, hunt ad infinitim along the unbounded string of registers until it finds what it is looking for. The pointer register is exactly like any other register with one exception: under the circumstances called "indirect addressing" it provides _its_ contents, rather than the address-operand in the state machine's TABLE, to be the address of the target register (including possibly itself!)

### <span id="Bounded_indirection_and_the_primitive_recursive_functions" class="mw-headline">Bounded indirection and the primitive recursive functions</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Random-access_machine&amp;action=edit&amp;section=8" title="Edit section: Bounded indirection and the primitive recursive functions">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

If we eschew the Minsky approach of one monster number in one register, and specify that our machine model will be "like a computer" we have to confront this problem of indirection if we are to compute the recursive functions (also called the [μ-recursive functions](https://en.wikipedia.org/wiki/%CE%9C-recursive_function "Μ-recursive function") ) – both total and partial varieties.

Our simpler counter-machine model can do a "bounded" form of indirection – and thereby compute the sub-class of [primitive recursive functions](https://en.wikipedia.org/wiki/Primitive_recursive_function "Primitive recursive function") – by using a primitive recursive "operator" called "definition by cases" (defined in Kleene (1952) p. 229 and Boolos-Burgess-Jeffrey p. 74). Such a "bounded indirection" is a laborious, tedious affair. "Definition by cases" requires the machine to determine/distinguish the contents of the pointer register by attempting, time after time until success, to match this contents against a number/name that the case operator _explicitly_ declares. Thus the definition by cases starts from e.g. the lower bound address and continues ad nauseam toward the upper bound address attempting to make a match:

_Is the number in register N equal to 0? If not then is it equal to 1? 2? 3? ... 65364? If not then we're at the last number 65365 and this had better be the one, else we have a problem!_ "Bounded" indirection will not allow us to compute the partial recursive functions – for those we need _unbounded_ indirection aka the [μ operator](https://en.wikipedia.org/wiki/%CE%9C_operator "Μ operator").

_Suppose we had been able to continue on to number 65367, and in fact that register had what we were looking for. Then we could have completed our calculation successfully! But suppose 65367 didn't have what we needed. How far should we continue to go?_ To be [Turing equivalent](https://en.wikipedia.org/wiki/Turing_completeness "Turing completeness") the counter machine needs to either use the unfortunate single-register Minsky [Gödel number](https://en.wikipedia.org/wiki/G%C3%B6del_number "Gödel number") method, or be augmented with an ability to explore the ends of its register string, ad infinitum if necessary. (A failure to find something "out there" defines what it means for an algorithm to fail to terminate; cf Kleene (1952) pp. 316ff _Chapter XII Partial Recursive Functions_, in particular p. 323-325.) See more on this in the example below.

### <span id="Unbounded_indirection_and_the_partial_recursive_functions" class="mw-headline">Unbounded indirection and the partial recursive functions</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Random-access_machine&amp;action=edit&amp;section=9" title="Edit section: Unbounded indirection and the partial recursive functions">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

For _unbounded_ indirection we require a "hardware" change in our machine model. Once we make this change the model is no longer a counter machine, but rather a random-access machine.

Now when e.g. INC is specified, the finite state machine's instruction will have to specify _where_ the address of the register of interest will come from. This _where_ can be either (i) the state machine's instruction that provides an _explicit label_, or (ii) the _pointer-register_ whose _contents_ is the address of interest. Whenever an instruction specifies a register address it now will _also_ need to specify an additional parameter "i/d" – "indirect/direct". In a sense this new "i/d" parameter is a "switch" that flips one way to get the direct address as specified in the instruction or the other way to get the indirect address from the pointer register (which pointer register – in some models every register can be a pointer register – is specified by the instruction). This "mutually exclusive but exhaustive choice" is yet another example of "definition by cases", and the arithmetic equivalent shown in the example below is derived from the definition in Kleene (1952) p. 229.

Example: CPY ( indirect

<sub>source</sub>

, r

<sub>source</sub>

, direct

<sub>destination</sub>

, r

<sub>destination</sub>

 ) Assign a code to specify direct addressing as d="0" and indirect addressing as i="1". Then our machine can determine the source address as follows: i*[r

<sub>s</sub>

] + (1-i)*r

<sub>s</sub>

 For example, suppose the contents of register 3 are "5" (i.e. [3]=5 ) and the contents of register 4 are "2" (i.e. [4]=2 ): Example: CPY ( 1, 3, 0, 4 ) = CPY ( indirect, reg 3, direct, reg 4 ) 1*[3] + 0*3 = [3] = source-register address 5 0*[4] + 1*4 = 4 = destination-register address 4 Example: CPY ( 0, 3, 0, 4 ) 0*[3] + 1*3 = 3 = source-register address 3 0*[4] + 1*4 = 4 = destination-register address 4 Example: CPY ( 0, 3, 1, 4 ) 0*[3] + 1*3 = 3 = source-register address 3 1*[4] + 0*4 = [4] = destination-register address 2

### <span id="The_indirect_COPY_instruction" class="mw-headline">The indirect COPY instruction</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Random-access_machine&amp;action=edit&amp;section=10" title="Edit section: The indirect COPY instruction">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

Probably the most useful of the added instructions is COPY. Indeed Elgot-Robinson (1964) provide their models P

<sub>0</sub>

 and P'

<sub>0</sub>

 with the COPY instructions, and Cook-Reckhow (1973) provide their accumulator-based model with only two indirect instructions – COPY to accumulator indirectly, COPY from accumulator indirectly.

**A plethora of instructions**: Because any instruction acting on a single register can be augmented with its indirect "dual" (including conditional and unconditional jumps, cf the Elgot-Robinson model), the inclusion of indirect instructions will double the number of single parameter/register instructions (e.g. INC (d, r), INC (i, r)). Worse, every two parameter/register instruction will have 4 possible varieties, e.g.:

CPY (d, r

<sub>s</sub>

, d, r

<sub>d</sub>

 ) = COPY directly from source-register directly to destination-register CPY (i, r

<sub>sp</sub>

, d, r

<sub>d</sub>

 ) = COPY to destination-register indirectly using the source address to be found in the source-pointer register r

<sub>sp</sub>

. CPY (d, r

<sub>s</sub>

, i, r

<sub>dp</sub>

 ) = COPY contents of source-register indirectly into register using destination address to be found in the destination-pointer register r

<sub>dp</sub>

. CPY (i, r

<sub>sp</sub>

, i, r

<sub>dp</sub>

 ) = COPY indirectly the contents of the source register with address to be found in source-pointer register r

<sub>sp</sub>

, into the destination register with address to be found in the destination-pointer register r

<sub>dp</sub>

) In a similar manner every three-register instruction that involves two source registers r

<sub>s1</sub>

 r

<sub>s2</sub>

 and a destination register r

<sub>d</sub>

 will result in 8 varieties, for example the addition:

[r

<sub>s1</sub>

] + [r

<sub>s2</sub>

] → r

<sub>d</sub>

 will yield:

- ADD ( d, r

  <sub>s1</sub>

  , d, r

  <sub>s2</sub>

  , d, r

  <sub>d</sub>

   )
- ADD ( i, r

  <sub>sp1</sub>

  , d, r

  <sub>s2</sub>

  , d, r

  <sub>d</sub>

   )
- ADD ( d, r

  <sub>s1</sub>

  , i, r

  <sub>sp2</sub>

  , d, r

  <sub>d</sub>

   )
- ADD ( i, r

  <sub>sp1</sub>

  , i, r

  <sub>sp2</sub>

  , d, r

  <sub>d</sub>

   )
- ADD ( d, r

  <sub>s1</sub>

  , d, r

  <sub>s2</sub>

  , i, r

  <sub>dp</sub>

   )
- ADD ( i, r

  <sub>sp1</sub>

  , d, r

  <sub>s2</sub>

  , i, r

  <sub>dp</sub>

   )
- ADD ( d, r

  <sub>s1</sub>

  , i, r

  <sub>sp2</sub>

  , i, r

  <sub>dp</sub>

   )
- ADD ( i, r

  <sub>s1</sub>

  , i, r

  <sub>s2</sub>

  , i, r

  <sub>dp</sub>

   )

If we designate one register to be the "accumulator" (see below) and place strong restrictions on the various instructions allowed then we can greatly reduce the plethora of direct and indirect operations. However, one must be sure that the resulting reduced instruction-set is sufficient, and we must be aware that the reduction will come at the expense of more instructions per "significant" operation.

## <span id="The_notion_of_.22accumulator_A.22" class="mw-headline">The notion of “accumulator A”</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Random-access_machine&amp;action=edit&amp;section=11" title="Edit section: The notion of &quot;accumulator A&quot;">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

Historical convention dedicates a register to the accumulator, an "arithmetic organ" that literally accumulates its number during a sequence of arithmetic operations:

"The first part of our arithmetic organ ... should be a parallel storage organ which can receive a number and add it to the one already in it, which is also able to clear its contents and which can store what it contains. We will call such an organ an _Accumulator_. It is quite conventional in principle in past and present computing machines of the most varied types, e.g. desk multipliers, standard IBM counters, more modern relay machines, the ENIAC" (boldface in original: Goldstine and von Neumann, 1946; p. 98 in Bell and Newell 1971). However, the accumulator comes at the expense of more instructions per arithmetic "operation", in particular with respect to what are called 'read-modify-write' instructions such as "Increment indirectly the contents of the register pointed to by register r2 ". "A" designates the "accumulator" register A:

Label        | Instruction         |  | A     | r2      | r378,426 | Description
------------ | ------------------- |  | ----- | ------- | -------- | --------------------------------------------------------------------
             |                     |  | . . . | 378,426 | 17       |
INCi ( r2 ): | CPY ( i, r2, d, A ) |  | 17    | 378,426 | 17       | Contents of r2 points to r378,426 with contents "17": copy this to A
             | INC ( A )           |  | 18    | 378,426 | 17       | Incement contents of A
             | CPY ( d, A, i, r2 ) |  | 18    | 378,426 | 18       | Contents of r2 points to r378,426: copy contents of A into r378,426

If we stick with a specific name for the accumulator, e.g. "A", we can imply the accumulator in the instructions, for example,

INC ( A ) = INCA However, when we write the CPY instructions without the accumulator called out the instructions are ambiguous or they must have empty parameters:

CPY ( d, r2, d, A ) = CPY (d, r2, , ) CPY ( d, A, d, r2 ) = CPY ( , , d, r2) Historically what has happened is these two CPY instructions have received distinctive names; however, no convention exists. Tradition (e.g. [Knuth](https://en.wikipedia.org/wiki/Donald_Knuth "Donald Knuth")'s (1973) imaginary [MIX](https://en.wikipedia.org/wiki/MIX "MIX") computer) uses two names called LOAD and STORE. Here we are adding the "i/d" parameter:

LDA ( d/i, r

<sub>s</sub>

 ) =

<sub>def</sub>

 CPY ( d/i, r

<sub>s</sub>

, d, A ) STA ( d/i, r

<sub>d</sub>

 ) =

<sub>def</sub>

 CPY ( d, A, d/i, r

<sub>d</sub>

 ) The typical accumulator-based model will have all its two-variable arithmetic and constant operations (e.g. ADD (A, r), SUB (A, r) ) use (i) the accumulator's contents, together with (ii) a specified register's contents. The one-variable operations (e.g. INC (A), DEC (A) and CLR (A) ) require only the accumulator. Both instruction-types deposit the result (e.g. sum, difference, product, quotient or remainder) in the accumulator.

Example: INCA = [A] +1 → A Example: ADDA (r

<sub>s</sub>

) = [A] + [r

<sub>s</sub>

] → A Example: MULA (r

<sub>s</sub>

) = [A] articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii [r

<sub>s</sub>

] → A If we so choose, we can abbreviate the mnemonics because at least one source-register and the destination register is always the accumulator A. Thus we have :

{ LDA (i/d, r

<sub>s</sub>

), STA (i/d, r

<sub>d</sub>

), CLRA, INCA, DECA, ADDA (r

<sub>s</sub>

), SUBA (r

<sub>s</sub>

), MULA (r

<sub>s</sub>

), DIVA (r

<sub>s</sub>

), etc.)

## <span id="The_notion_of_indirect_address_register_.22N.22" class="mw-headline">The notion of indirect address register “N”</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Random-access_machine&amp;action=edit&amp;section=12" title="Edit section: The notion of indirect address register &quot;N&quot;">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

If our model has an _unbounded accumulator_ can we _bound_ all the other registers? Not until we provide for at least one unbounded register from which we derive our indirect addresses.

The minimimalist approach is to use itself (Schönhage does this).

Another approach (Schönhage does this too) is to declare a specific register the "indirect address register" and confine indirection relative to this register (Schonhage's RAM0 model uses both A and N registers for indirect as well as direct instructions). Again our new register has no conventional name – perhaps "N" from "iNdex", or "iNdirect" or "address Number".

For maximum flexibility, as we have done for the accumulator A – we will consider N just another register subject to increment, decrement, clear, test, direct copy, etc. Again we can shrink the instruction to a single-parameter that provides for direction and indirection, for example.

LDAN (i/d) = CPY (i/d, N, d, A); LoaD Accumulator via iNdirection register STAN (i/d) = CPY (d, A, i/d, N). STore Accumlator via iNdirection register Why is this such an interesting approach? At least two reasons:

**(1) An instruction set with no parameters:**

Schönhage does this to produce his RAM0 instruction set. See section below.

**(2) Reduce a RAM to a Post-Turing machine:**

Posing as minimalists, we reduce all the registers excepting the accumulator A and indirection register N e.g. **r** = { r0, r1, r2, ... } to an unbounded string of (very-) bounded-capacity pigeon-holes. These will do nothing but hold (very-) bounded numbers e.g. a lone bit with value { 0, 1 }. Likewise we shrink the accumulator to a single bit. We restrict any arithmetic to the registers { A, N }, use indirect operations to pull the contents of registers into the accumulator and write 0 or 1 from the accumulator to a register:

{ LDA (i, N), STA (i, N), CLR (A/N), INC (A/N), DEC(N), JZ (A/N, I

<sub>z</sub>

), JZ (I

<sub>z</sub>

), H } We push further and eliminate A altogether by the use of two "constant" registers called "ERASE" and "PRINT": [ERASE]=0, [PRINT]=1.

{ CPY (d, ERASE, i, N), CPY (d, PRINT, i, N), CLR (N), INC (N), DEC (N), JZ (i, N, I

<sub>z</sub>

), JZ (I

<sub>z</sub>

), H } Rename the COPY instructions and call INC (N) = RIGHT, DEC (N) = LEFT and we have the same instructions as the Post-Turing machine, plus an extra CLRN :

{ ERASE, PRINT, CLRN, RIGHT, LEFT, JZ (i, N, I

<sub>z</sub>

), JZ (I

<sub>z</sub>

), H }

## <span id="Turing_equivalence_of_the_RAM_with_indirection" class="mw-headline">Turing equivalence of the RAM with indirection</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Random-access_machine&amp;action=edit&amp;section=13" title="Edit section: Turing equivalence of the RAM with indirection">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

In the section above we informally showed that a RAM with an unbounded indirection capability produces a [Post-Turing machine](https://en.wikipedia.org/wiki/Post-Turing_machine "Post-Turing machine"). The Post-Turing machine is Turing equivalent, so we have shown that the RAM with indirection is Turing equivalent.

We give here a slightly more formal demonstration. Begin by designing our model with three reserved registers "E", "P", and "N", plus an unbounded set of registers 1, 2, ..., n to the right. The registers 1, 2, ..., n will be considered "the squares of the tape". Register "N" points to "the scanned square" that "the head" is currently observing. The "head" can be thought of as being in the conditional jump – observe that it uses indirect addressing ( cf Elgot-Robinson p. 398). As we decrement or increment "N" the (apparent) head will "move left" or "right" along the squares. We will move the contents of "E"=0 or "P"=1 to the "scanned square" as pointed to by N, using the indirect CPY.

The fact that our tape is left-ended presents us with a minor problem: Whenever LEFT occurs our instructions will have to test to determine whether or not the contents of "N" is zero; if so we should leave its count at "0" (this is our choice as designers – for example we might have the machine/model "trigger an event" of our choosing).

Instruction set 1 (augmented): { INC (N), DEC (N), CLR (N), CPY (d, r

<sub>s</sub>

,i, N), JZ ( i, r, z ), HALT } The following table both defines the Post-Turing instructions in terms of their RAM equivalent instructions and gives an example of their functioning. The (apparent)location of the head along the tape of registers r0-r5 . . . is shown shaded:

Mnemonic    | label:         |                    |  | E | P | N |  | r0 | r1 | r2 | r3 | r4 | r5 | etc. | Action on registers | Action on finite state machine Instruction Register IR
----------- | -------------- | ------------------ |  | - | - | - |  | -- | -- | -- | -- | -- | -- | ---- | ------------------- | ------------------------------------------------------------------------------------
            |                |                    |  |   |   |   |  |    |    |    |    |    |    |      |                     |
            | start:         |                    |  | 0 | 1 | 3 |  |    |    |    | 1  | 0  |    |      |                     |
            |                |                    |  |   |   |   |  |    |    |    |    |    |    |      |                     |
R           | right:         | INC ( N )          |  | 0 | 1 | 4 |  |    |    |    | 1  | 0  |    |      | [N] +1 → N          | [IR] +1 → IR
            |                |                    |  |   |   |   |  |    |    |    |    |    |    |      |                     |
P           | print:         | CPY ( d, P, i, N ) |  | 0 | 1 | 4 |  |    |    |    | 1  | 1  |    |      | [P]=1 → [N]=r4      | [IR] +1 → IR
            |                |                    |  |   |   |   |  |    |    |    |    |    |    |      |                     |
E           | erase:         | CPY ( d, E, i, N ) |  | 0 | 1 | 4 |  |    |    |    | 1  | 0  |    |      | [E]=0 → [N]=r4      | [IR] +1 → IR
            |                |                    |  |   |   |   |  |    |    |    |    |    |    |      |                     |
L           | left:          | JZ ( i, N, end )   |  | 0 | 1 | 4 |  |    |    |    | 1  | 0  |    |      | none                | IF [N](https://en.wikipedia.org/wiki/N "N") =r4] =0 THEN "end" → IR else [IR]+1 → IR
            |                | DEC ( N )          |  | 0 | 1 | 3 |  |    |    |    | 1  | 0  |    |      | [N] -1 → N          |
            |                |                    |  |   |   |   |  |    |    |    |    |    |    |      |                     |
J0 ( halt ) | jump_if_blank: | JZ ( i, N, end )   |  | 0 | 1 | 3 |  |    |    |    | 1  | 0  |    |      | none                | IF N =r3] =0 THEN "end" → IR else [IR]+1 → IR
            |                |                    |  |   |   |   |  |    |    |    |    |    |    |      |                     |
J1 ( halt ) | jump_if_mark:  | JZ ( i, N, halt )  |  | 0 | 1 | 3 |  |    |    |    | 1  | 0  |    |      | N =r3] → A          | IF N =r3] =0 THEN "end" → IR else [IR]+1 → IR
            | end            | . . . etc.         |  | 0 | 1 | 3 |  |    |    |    | 1  | 0  |    |      |                     |
            |                |                    |  |   |   |   |  |    |    |    |    |    |    |      |                     |
            | halt:          | H                  |  | 0 | 1 | 3 |  |    |    |    | 1  | 0  |    |      | none                | [IR] +1 → IR

## <span id="Example:_Bounded_indirection_yields_a_machine_that_is_not_Turing_equivalent" class="mw-headline">Example: Bounded indirection yields a machine that is not Turing equivalent</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Random-access_machine&amp;action=edit&amp;section=14" title="Edit section: Example: Bounded indirection yields a machine that is not Turing equivalent">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

Throughout this demonstration we have to keep in mind that the instructions in the finite state machine's TABLE is _bounded_, i.e. _finite_:

"Besides a merely being a _finite set of rules_ which gives a sequence of operations for solving a specific type of problem, an algorithm has five important features [Finiteness, Definiteness, Input, Output, Effectiveness]" (italics added, Knuth p. 4-7). _The difficulty arises because the registers have explicit "names" (numbers) and our machine must call each out by name in order to "access" it._ We will build the indirect CPY ( i, q, d, φ ) with the CASE operator. The address of the target register will be specified by the contents of register "q"; once the CASE operator has determined what this number is, CPY will directly deposit the contents of the register with that number into register "φ". We will need an additional register that we will call "y" – it serves as an up-counter.

_So the following is actually a constructive demonstration or proof that we can indeed simulate the indirect CPY ( i, q, d, φ ) without a "hardware" design change to our counter machine/model. However, note that because this indirect CPY is "bounded" by the size/extent of the finite state machine, a RASP using this indirect CPY can only calculate the [primitive recursive functions](https://en.wikipedia.org/wiki/Primitive_recursive_functions "Primitive recursive functions"), not the full suite of [mu recursive functions](https://en.wikipedia.org/wiki/Mu_recursive_function "Mu recursive function")._ The CASE "operator" is described in Kleene (1952) (p. 229) and in Boolos-Burgess-Jeffrey (2002) (p. 74); the latter authors emphasize its utility. The following definition is per Kleene but modified to reflect the familiar "IF-THEN-ELSE" construction.

The CASE operator "returns" a natural number into φ depending on which "case" is satisfied, starting with "case_0" and going successively through "case_last"; if no case is satisfied then the number called "default" (aka "woops") is returned into φ (here **x** designates some selection of parameters, e.g. register q and the string r0, ... rlast )):

_Definition by cases_ φ (**x**, y):

- case_0: IF Q

  <sub>0</sub>

  (**x**, y) is true THEN φ

  <sub>0</sub>

  (**x**, y) ELSE
- case_1: IF Q

  <sub>1</sub>

  (**x**, y) is true THEN φ

  <sub>1</sub>

  (**x**, y) ELSE
- cases_2 through case_next_to_last: etc. . . . . . . . . ELSE
- case_last: IF Q

  <sub>last</sub>

  (**x**, y) is true THEN φ

  <sub>last</sub>

  (**x**, y) ELSE
- default: do φ

  <sub>default</sub>

  (**x**, y)

Kleene require that the "predicates" Q

<sub>n</sub>

 that doing the testing are all mutually exclusive – "predicates" are functions that produce only { true, false } for output; Boolos-Burgess-Jeffrey add the requirement that the cases are "exhaustive".

We begin with a number in register q that represents the address of the target register. But what is this number? The "predicates" will test it to find out, one trial after another: JE (q, y, z) followed by INC (y). Once the number is identified explicitly, the CASE operator directly/explicitly copies the contents of this register to φ:

_Definition by cases_ CPY (i, q, d, φ) =

<sub>def</sub>

 φ (q, r0, ..., rlast, y) =

- case_0: IF CLR (y), [q] - [y]=0 THEN CPY ( r0, φ ), J (exit) ELSE
- case_1: IF INC (y), [q] = [y]=1 THEN CPY ( r1, φ ), J (exit) ELSE
- case_2 through case n: IF . . . THEN . . . ELSE
- case_n: IF INC (y), [q] = [y]=n THEN CPY ( rn, φ ), J (exit) ELSE
- case_n+1 to case_last: IF . . . THEN . . . ELSE
- case_last: IF INC (y), [q] = [y]="last" THEN CPY ( rlast, φ ), J (exit) ELSE
- default: woops

Case_0 ( the base step of the recursion on y) looks like this:

- _case_0_:

<!--  -->

- CLR ( y ) ; set register y = 0
- JE ( q, y, __φ0_ )
- J ( _case_1_ )

<!--  -->

- __φ0:_ CPY ( r0, φ )
- J ( _exit_ )

<!--  -->

- _case_1:_ etc.

Case_n (the induction step) looks like this; remember, each instance of "n", "n+1", ..., "last" must be an explicit natural number:

- _case_n_:

<!--  -->

- INC ( y )
- JE ( q, y, __φn_ )
- J ( _case_n+1_)

<!--  -->

- __φn:_ CPY ( rn, φ )
- J ( _exit_ )

<!--  -->

- _case__n+1:_ etc.

Case_last stops the induction and bounds the CASE operator (and thereby bounds the "indirect copy" operator):

- _case_last_:

<!--  -->

- INC ( y )
- JE ( q, y, __φlast_ )
- J ( _woops_ )

<!--  -->

- __φlast_: CPY ( rlast, φ )
- J ( _exit_ )

<!--  -->

- _woops:_ how do we handle an out-of-bounds attempt?
- _exit:_ etc.

If the CASE could continue ad infinitum it would be the [mu operator](https://en.wikipedia.org/wiki/Mu_operator "Mu operator"). But it can't – its finite state machine's "state register" has reached its maximum count (e.g. 65365 = 11111111,11111111

<sub>2</sub>

 ) or its table has run out of instructions; it is a _finite_ machine, after all.

## <span id="Examples_of_models" class="mw-headline">Examples of models</span>

<span class="mw-editsection"><span class="mw-editsection-bracket">[</span>[edit](<a href="https://en.wikipedia.org/w/inde">https://en.wikipedia.org/w/inde</a></span>
x.php?title=Random-access_machine&action=edit&section=15 "Edit section: Examples of models")

<span class="mw-editsection-bracket">]</span>

# <span id="Register-to-register_.28.22read-modify-write.22.29_model_of_Cook_and_Reckhow_.281973.29" class="mw-headline">Register-to-register (“read-modify-write”) model of Cook and Reckhow (1973)</span>

<span class="mw-editsection"><span class="mw-editsection-bracket">[</span><a href="https://en.wikipedia.org/w/index.php?title=Random-access_machine&amp;action=edit&amp;section=16" title="Edit section: Register-to-register (&quot;read-modify-write">edit</a> model of Cook and Reckhow (1973)")<span class="mw-editsection-bracket">]</span></span>

The commonly encountered Cook and Rechkow model is a bit like the ternary-register Malzek model (written with Knuth mnemonics – the original instructions had no mnemonics excepting TRA, Read, Print).

- `LOAD ( C, rd ) ; C → rd`, C is any integer

Example: `LOAD ( 0, 5 )` will clear register 5.

- `ADD ( rs1, rs2, rd ) ; [rs1] + [rs2] → rd`, the registers can be the same or different;

Example: `ADD ( A, A, A )` will double the contents of register A.

- `SUB ( rs1, rs2, rd ) ; [rs1] - [rs2] → rd`, the registers can be the same or different:

Example: `SUB ( 3, 3, 3 )` will clear register 3.

- `COPY ( i, rp, d, rd ) ; [[rp] ] → rd`, Indirectly copy the contents of the source-register pointed to by pointer-register r

  <sub>p</sub>

   into the destination register.
- `COPY ( d, rs, i, rp ) ; [rs] → [rp]`. Copy the contents of source register r

  <sub>s</sub>

   into the destination-register pointed to by the pointer-register r

  <sub>p</sub>

  .
- `JNZ ( r, Iz ) ;` Conditional jump if [r] is positive; i.e. IF [r] > 0 THEN jump to instruction z else continue in sequence (Cook and Reckhow call this: "TRAnsfer control to line m if Xj > 0")
- `READ ( rd ) ;` copy "the input" into destination register r

  <sub>d</sub>

- `PRINT ( rs ) ;` copy the contents of source register r

  <sub>s</sub>

   to "the output."

# <span id="Sch.C3.B6nhage.27s_RAM0_and_RAM1_.281980.29" class="mw-headline">Schönhage’s RAM0 and RAM1 (1980)</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Random-access_machine&amp;action=edit&amp;section=17" title="Edit section: Schönhage's RAM0 and RAM1 (1980)">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

Schönhage (1980) describes a very primitive, atomized model chosen for his proof of the equivalence of his SMM [pointer machine](https://en.wikipedia.org/wiki/Pointer_machine "Pointer machine") model:

"In order to avoid any explicit addressing the RAM0 has the accumulator with contents _z_ and an additional address register with current contents _n_ (initially 0)" (p. 494) **RAM1 model**: Schönhage demonstrates how his construction can be used to form the more common, usable form of "successor"-like RAM (using this article's mnemonics):

- `LDA k ; k --> A` , k is a constant, an explicit number such as "47"
- `LDA ( d, r ) ; [r] → A ;` directly load A
- `LDA ( i, r ) ; [[r]] → A ;` indirectly load A
- `STA ( d, r ) ; [A] → r ;` directly store A
- `STA ( i, r ) ; [A] → [r] ;` indirectly store A
- `JEA ( r, z ) ; IF [A] = [r] then Iz else continue`
- `INCA ; [A] + 1 --> A`

**RAM0 model**: Schönhage's RAM0 machine has 6 instructions indicated by a single letter (the 6th "C xxx" seems to involve 'skip over next parameter'. Schönhage designated the accumulator with "z", "N" with "n", etc. Rather than Schönhage's mnemonics we will use the mnemonics developed above.

- `(Z), CLRA: 0 → A`
- `(A), INCA: [A] +1 → A`
- `(N), CPYAN: [A] → N`
- `(A), LDAA: [[A]] → A` ; contents of A points to register address; put register's contents into A
- `(S), STAN: [A] → [N]` ; contents of N points to register address; put contents of A into register pointed to by N
- `(C), JAZ ( z ): [A] = 0 then go to Iz` ; ambiguous in his treatment

Indirection comes (i) from CPYAN (copy/transfer contents A to N) working with store_A_via_N STAN, and from (ii) the peculiar indirection instruction `LDAA ( [[A]] → [A] )`.

## <span id="Footnotes" class="mw-headline">Footnotes</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Random-access_machine&amp;action=edit&amp;section=18" title="Edit section: Footnotes">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

### <span id="Finite_vs_unbounded" class="mw-headline">Finite vs unbounded</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Random-access_machine&amp;action=edit&amp;section=19" title="Edit section: Finite vs unbounded">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

The definitional fact that any sort of counter machine without an unbounded register-"address" register must specify a register "r" by name indicates that the model requires "r" to be _finite_, although it is "unbounded" in the sense that the model implies no upper limit to the number of registers necessary to do its job(s). For example we do not require r < 83,617,563,821,029,283,746 nor r < 2^1,000,001, etc.

Thus our model can "expand" the number of registers, if necessary to perform a certain computation. However this _does_ mean that whatever number the model expands to must be _finite_ – it must be indexable with a natural number: _ω is not an option_. We can escape this restriction by providing an unbounded register to provide the address of the register that specifies an indirect address.

## <span id="See_also" class="mw-headline">See also</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Random-access_machine&amp;action=edit&amp;section=20" title="Edit section: See also">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

- [Real RAM](https://en.wikipedia.org/wiki/Real_RAM "Real RAM")
- [Transdichotomous model](https://en.wikipedia.org/wiki/Transdichotomous_model "Transdichotomous model")

## <span id="External_links" class="mw-headline">External links</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Random-access_machine&amp;action=edit&amp;section=21" title="Edit section: External links">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

- [Random Access Machine Emulator](http://savannah.nongnu.org/projects/ramemu/)
- [Random Access Machine Emulator](http://www.szkup.com/?pid=msthesis&lang=en)

## <span id="References" class="mw-headline">References</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Random-access_machine&amp;action=edit&amp;section=22" title="Edit section: References">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

With a few exceptions, these references are the same as those at [Register machine](https://en.wikipedia.org/wiki/Register_machine "Register machine").

- *Goldstine, Herman H., and von Neumann, John, "Planning and Coding of the Problems for an Electronic Computing Instrument", Rep. 1947, [Institute for Advanced Study](https://en.wikipedia.org/wiki/Institute_for_Advanced_Study "Institute for Advanced Study"), Princeton. Reprinted on pp. 92–119 in Bell, C. Gordon and Newell, Allen (1971), _Computer Structures: Readings and Examples_, McGraw-Hill Book Company, New York. [ISBN 0-07-004357-4](https://en.wikipedia.org/wiki/Special:BookSources/0070043574)}.
- [George Boolos](https://en.wikipedia.org/wiki/George_Boolos "George Boolos"), [John P. Burgess](https://en.wikipedia.org/wiki/John_P._Burgess "John P. Burgess"), [Richard Jeffrey](https://en.wikipedia.org/wiki/Richard_Jeffrey "Richard Jeffrey") (2002), _Computability and Logic: Fourth Edition_, Cambridge University Press, Cambridge, England. The original Boolos-Jeffrey text has been extensively revised by Burgess: more advanced than an introductory textbook. "Abacus machine" model is extensively developed in Chapter 5 _Abacus Computability_; it is one of three models extensively treated and compared – the Turing machine (still in Boolos' original 4-tuple form) and recursion the other two.
- [Arthur Burks](https://en.wikipedia.org/wiki/Arthur_Burks "Arthur Burks"), [Herman Goldstine](https://en.wikipedia.org/wiki/Herman_Goldstine "Herman Goldstine"), [John von Neumann](https://en.wikipedia.org/wiki/John_von_Neumann "John von Neumann") (1946), _Preliminary discussion of the logical design of an electronic computing instrument_, reprinted pp. 92ff in [Gordon Bell](https://en.wikipedia.org/wiki/Gordon_Bell "Gordon Bell") and [Allen Newell](https://en.wikipedia.org/wiki/Allen_Newell "Allen Newell") (1971), _Computer Structures: Readings and Examples_, mcGraw-Hill Book Company, New York. [ISBN 0-07-004357-4](https://en.wikipedia.org/wiki/Special:BookSources/0070043574) .
- [Stephen A. Cook](https://en.wikipedia.org/wiki/Stephen_Cook "Stephen Cook") and Robert A. Reckhow (1973), _Time-bounded random access machines_, Journal of Computer Systems Science 7(4):354-375.
- [Martin Davis](https://en.wikipedia.org/wiki/Martin_Davis "Martin Davis") (1958), _Computability & Unsolvability_, McGraw-Hill Book Company, Inc. New York.
- [Calvin Elgot](https://en.wikipedia.org/w/index.php?title=Calvin_Elgot&action=edit&redlink=1 "Calvin Elgot (page does not exist)") and [Abraham Robinson](https://en.wikipedia.org/wiki/Abraham_Robinson "Abraham Robinson") (1964), _Random-Access Stored-Program Machines, an Approach to Programming Languages_, Journal of the Association for Computing Machinery, Vol. 11, No. 4 (October, 1964), pp. 365–399.
- [J. Hartmanis](https://en.wikipedia.org/w/index.php?title=J._Hartmanis&action=edit&redlink=1 "J. Hartmanis (page does not exist)") (1971), "Computational Complexity of Random Access Stored Program Machines," Mathematical Systems Theory 5, 3 (1971) pp. 232–245.
- [John Hopcroft](https://en.wikipedia.org/wiki/John_Hopcroft "John Hopcroft"), [Jeffrey Ullman](https://en.wikipedia.org/wiki/Jeffrey_Ullman "Jeffrey Ullman") (1979). _Introduction to Automata Theory, Languages and Computation_, 1st ed., Reading Mass: Addison-Wesley. [ISBN 0-201-02988-X](https://en.wikipedia.org/wiki/Special:BookSources/020102988X). A difficult book centered around the issues of machine-interpretation of "languages", NP-Completeness, etc.
- [Stephen Kleene](https://en.wikipedia.org/wiki/Stephen_Kleene "Stephen Kleene") (1952), _Introduction to Metamathematics_, North-Holland Publishing Company, Amsterdam, Netherlands. [ISBN 0-7204-2103-9](https://en.wikipedia.org/wiki/Special:BookSources/0720421039).
- [Donald Knuth](https://en.wikipedia.org/wiki/Donald_Knuth "Donald Knuth") (1968), _The Art of Computer Programming_, Second Edition 1973, Addison-Wesley, Reading, Massachusetts. Cf pages 462-463 where he defines "a new kind of abstract machine or 'automaton' which deals with linked structures."
- [Joachim Lambek](https://en.wikipedia.org/wiki/Joachim_Lambek "Joachim Lambek") (1961, received 15 June 1961), _How to Program an Infinite Abacus_, Mathematical Bulletin, vol. 4, no. 3\. September 1961 pages 295-302\. In his Appendix II, Lambek proposes a "formal definition of 'program'. He references Melzak (1961) and Kleene (1952) _Introduction to Metamathematics_.
- [Z. A. Melzak](https://en.wikipedia.org/w/index.php?title=Z._A._Melzak&action=edit&redlink=1 "Z. A. Melzak (page does not exist)") (1961, received 15 May 1961), _An informal Arithmetical Approach to Computability and Computation_, Canadian Mathematical Bulletin, vol. 4, no. 3\. September 1961 pages 279-293\. Melzak offers no references but acknowledges "the benefit of conversations with Drs. R. Hamming, D. McIlroy and V. Vyssots of the Bell telephone Laborators and with Dr. H. Wang of Oxford University."
- [Marvin Minsky](https://en.wikipedia.org/wiki/Marvin_Minsky "Marvin Minsky") (1961, received August 15, 1960). "Recursive Unsolvability of Post's Problem of 'Tag' and Other Topics in Theory of Turing Machines". _Annals of Math_ (The Annals of Mathematics, Vol. 74, No. 3) **74** (3): 437–455\. [doi](https://en.wikipedia.org/wiki/Digital_object_identifier "Digital object identifier"):[10.2307/1970290](https://dx.doi.org/10.2307%2F1970290). [JSTOR](https://en.wikipedia.org/wiki/JSTOR "JSTOR") [1970290](https://www.jstor.org/stable/1970290).

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ARandom-access+machine&amp;rft.atitle=Recursive+Unsolvability+of+Post%27s+Problem+of+%27Tag%27+and+Other+Topics+in+Theory+of+Turing+Machines&amp;rft.au=Marvin+Minsky&amp;rft.chron=1961%2C+received+August+15%2C+1960&amp;rft.genre=article&amp;rft_id=%2F%2Fwww.jstor.org%2Fstable%2F1970290&amp;rft_id=info%3Adoi%2F10.2307%2F1970290&amp;rft.issue=3&amp;rft.jtitle=Annals+of+Math&amp;rft.pages=437-455&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Ajournal&amp;rft.volume=74">
    <span>
  </span>
  </span>

   

  <span class="error citation-comment">Check date values in: <code>|date=</code> (<a href="https://en.wikipedia.org/wiki/Help:CS1_errors#bad_date" title="Help:CS1 errors">help</a>)</span>

- Marvin Minsky (1967). _Computation: Finite and Infinite Machines_ (1st ed.). Englewood Cliffs, N. J.: Prentice-Hall, Inc.

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ARandom-access+machine&amp;rft.au=Marvin+Minsky&amp;rft.btitle=Computation%3A+Finite+and+Infinite+Machines&amp;rft.date=1967&amp;rft.edition=1st&amp;rft.genre=book&amp;rft.place=Englewood+Cliffs%2C+N.+J.&amp;rft.pub=Prentice-Hall%2C+Inc.&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

   In particular see chapter 11: _Models Similar to Digital Computers_ and chapter 14: _Very Simple Bases for Computability_. In the former chapter he defines "Program machines" and in the later chapter he discusses "Universal Program machines with Two Registers" and "...with one register", etc.
- [John C. Shepherdson](https://en.wikipedia.org/w/index.php?title=John_C._Shepherdson&action=edit&redlink=1 "John C. Shepherdson (page does not exist)") and [H. E. Sturgis](https://en.wikipedia.org/w/index.php?title=H._E._Sturgis&action=edit&redlink=1 "H. E. Sturgis (page does not exist)") (1961) received December 1961 _Computability of Recursive Functions_, Journal of the Association of Computing Machinery (JACM) 10:217-255, 1963\. An extremely valuable reference paper. In their Appendix A the authors cite 4 others with reference to "Minimality of Instructions Used in 4.1: Comparison with Similar Systems".

<!--  -->

- Kaphengst, Heinz, _Eine Abstrakte programmgesteuerte Rechenmaschine'_, Zeitschrift fur mathematische Logik und Grundlagen der Mathematik:_5_ (1959), 366-379.
- [Ershov, A. P.](https://en.wikipedia.org/wiki/Andrey_Ershov "Andrey Ershov") _On operator algorithms_, (Russian) Dok. Akad. Nauk 122 (1958), 967-970\. English translation, Automat. Express 1 (1959), 20-23.
- [Péter, Rózsa](https://en.wikipedia.org/wiki/R%C3%B3zsa_P%C3%A9ter "Rózsa Péter") _Graphschemata und rekursive Funktionen_, Dialectica 12 (1958), 373.
- Hermes, Hans _Die Universalität programmgesteuerter Rechenmaschinen. Math.-Phys. Semsterberichte (Göttingen) 4 (1954), 42-53._

<!--  -->

- [Arnold Schönhage](https://en.wikipedia.org/wiki/Arnold_Sch%C3%B6nhage "Arnold Schönhage") (1980), _Storage Modification Machines_, Society for Industrial and Applied Mathematics, SIAM J. Comput. Vol. 9, No. 3, August 1980\. Wherein Schōnhage shows the equivalence of his SMM with the "successor RAM" (Random Access Machine), etc. resp. _Storage Modification Machines_, in _Theoretical Computer Science_ (1979), pp. 36–37
- [Peter van Emde Boas](https://en.wikipedia.org/wiki/Peter_van_Emde_Boas "Peter van Emde Boas"), "Machine Models and Simulations" pp. 3–66, in: [Jan van Leeuwen](https://en.wikipedia.org/wiki/Jan_van_Leeuwen "Jan van Leeuwen"), ed. _Handbook of Theoretical Computer Science. Volume A: Algorithms and Complexity_, The MIT PRESS/Elsevier, 1990\. [ISBN 0-444-88071-2](https://en.wikipedia.org/wiki/Special:BookSources/0444880712) (volume A). QA 76.H279 1990\. van Emde Boas's treatment of SMMs appears on pp. 32–35\. This treatment clarifies Schōnhage 1980 – it closely follows but expands slightly the Schōnhage treatment. Both references may be needed for effective understanding.
- [Hao Wang](https://en.wikipedia.org/wiki/Hao_Wang_(academic) "Hao Wang (academic)") (1957), _A Variant to Turing's Theory of Computing Machines_, JACM (Journal of the Association for Computing Machinery) 4; 63-92\. Presented at the meeting of the Association, June 23–25, 1954.

![](https://en.wikipedia.org/wiki/Special:CentralAutoLogin/start?type=1x1)

Retrieved from "<https://en.wikipedia.org/w/index.php?title=Random-access_machine&oldid=700484659>"

[Categories](https://en.wikipedia.org/wiki/Help:Category "Help:Category"):

- [Register machines](https://en.wikipedia.org/wiki/Category:Register_machines "Category:Register machines")
