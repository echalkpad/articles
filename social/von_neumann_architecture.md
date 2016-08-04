# Von Neumann architecture

[Original URL](https://en.wikipedia.org/wiki/Von_Neumann_architecture)

> See also: Stored-program computer and Universal Turing machine § Stored-program computer

See also: [Stored-program computer](https://en.wikipedia.org/wiki/Stored-program_computer "Stored-program computer") and [Universal Turing machine § Stored-program computer](https://en.wikipedia.org/wiki/Universal_Turing_machine#Stored-program_computer "Universal Turing machine")

[![](https://upload.wikimedia.org/wikipedia/commons/thumb/e/e5/Von_Neumann_Architecture.svg/330px-Von_Neumann_Architecture.svg.png)](https://en.wikipedia.org/wiki/File:Von_Neumann_Architecture.svg) [![](https://bits.wikimedia.org/static-1.24wmf4/skins/common/images/magnify-clip.png)](https://en.wikipedia.org/wiki/File:Von_Neumann_Architecture.svg "Enlarge")

Von Neumann architecture scheme

[![](https://upload.wikimedia.org/wikipedia/commons/thumb/8/84/Von_Neumann_architecture.svg/330px-Von_Neumann_architecture.svg.png)](https://en.wikipedia.org/wiki/File:Von_Neumann_architecture.svg) [![](https://bits.wikimedia.org/static-1.24wmf4/skins/common/images/magnify-clip.png)](https://en.wikipedia.org/wiki/File:Von_Neumann_architecture.svg "Enlarge")

Von Neumann architecture scheme

The term **Von Neumann architecture**, also known as the **Von Neumann model** or the **Princeton architecture**, derives from a 1945 [computer architecture](https://en.wikipedia.org/wiki/Computer_architecture "Computer architecture") description by the [mathematician](https://en.wikipedia.org/wiki/Mathematician "Mathematician") and [physicist](https://en.wikipedia.org/wiki/Physicist "Physicist") [John von Neumann](https://en.wikipedia.org/wiki/John_von_Neumann "John von Neumann") and others, _[First Draft of a Report on the EDVAC](https://en.wikipedia.org/wiki/First_Draft_of_a_Report_on_the_EDVAC "First Draft of a Report on the EDVAC")_.

<sup>
  <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-FirstDraftReport-1"><span>[</span>1<span>]</span></a>
</sup>

 This describes a design architecture for an electronic [digital computer](https://en.wikipedia.org/wiki/Digital_computer "Digital computer") with subdivisions of a [processing unit](https://en.wikipedia.org/wiki/Central_processing_unit "Central processing unit") consisting of an [arithmetic logic unit](https://en.wikipedia.org/wiki/Arithmetic_logic_unit "Arithmetic logic unit") and [processor registers](https://en.wikipedia.org/wiki/Processor_register "Processor register"), a [control unit](https://en.wikipedia.org/wiki/Control_unit "Control unit") containing an [instruction register](https://en.wikipedia.org/wiki/Instruction_register "Instruction register") and [program counter](https://en.wikipedia.org/wiki/Program_counter "Program counter"), a [memory](https://en.wikipedia.org/wiki/Computer_memory "Computer memory") to store both data and [instructions](https://en.wikipedia.org/wiki/Instruction_(computer_science) "Instruction (computer science)"), external [mass storage](https://en.wikipedia.org/wiki/Mass_storage "Mass storage"), and [input and output](https://en.wikipedia.org/wiki/Input_and_output "Input and output") mechanisms.

<sup>
  <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-FirstDraftReport-1"><span>[</span>1<span>]</span></a>
  <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-GanesanCh4-2"><span>[</span>2<span>]</span></a>
</sup>

 The meaning of the term has evolved to mean a [stored-program computer](https://en.wikipedia.org/wiki/Stored-program_computer "Stored-program computer") in which an instruction fetch and a data operation cannot occur at the same time because they share a common [bus](https://en.wikipedia.org/wiki/Bus_(computing) "Bus (computing)"). This is referred to as the [Von Neumann bottleneck](https://en.wikipedia.org/wiki/Von_Neumann_architecture#Von_Neumann_bottleneck) and often limits the performance of the system.

<sup>
  <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-3"><span>[</span>3<span>]</span></a>
</sup>

The design of a Von Neumann architecture is simpler than the more modern [Harvard architecture](https://en.wikipedia.org/wiki/Harvard_architecture "Harvard architecture") which is also a stored-program system but has one dedicated set of address and data buses for reading data from and writing data to memory, and another set of address and data buses for fetching instructions.

A stored-program digital computer is one that keeps its [programmed](https://en.wikipedia.org/wiki/Computer_program "Computer program") instructions, as well as its data, in [read-write](https://en.wikipedia.org/wiki/Read-write_memory "Read-write memory"), [random-access memory](https://en.wikipedia.org/wiki/Random-access_memory "Random-access memory") (RAM). Stored-program computers were an advancement over the program-controlled computers of the 1940s, such as the [Colossus](https://en.wikipedia.org/wiki/Colossus_computer "Colossus computer") and the [ENIAC](https://en.wikipedia.org/wiki/ENIAC "ENIAC"), which were programmed by setting switches and inserting patch leads to route data and to control signals between various functional units. In the vast majority of modern computers, the same memory is used for both data and program instructions, and the Von Neumann vs. Harvard distinction applies to the [cache](https://en.wikipedia.org/wiki/CPU_cache "CPU cache") architecture, not main memory.

## Contents

- [<span class="tocnumber">1</span>

   

  <span class="toctext">History</span>](https://en.wikipedia.org/wiki/Von_Neumann_architecture#History)

- [<span class="tocnumber">2</span>

   

  <span class="toctext">Development of the stored-program concept</span>](https://en.wikipedia.org/wiki/Von_Neumann_architecture#Development_of_the_stored-program_concept)

- [<span class="tocnumber">3</span>

   

  <span class="toctext">Early von Neumann-architecture computers</span>](https://en.wikipedia.org/wiki/Von_Neumann_architecture#Early_von_Neumann-architecture_computers)

- [<span class="tocnumber">4</span>

   

  <span class="toctext">Early stored-program computers</span>](https://en.wikipedia.org/wiki/Von_Neumann_architecture#Early_stored-program_computers)

- [<span class="tocnumber">5</span>

   

  <span class="toctext">Evolution</span>](https://en.wikipedia.org/wiki/Von_Neumann_architecture#Evolution)

- [<span class="tocnumber">6</span>

   

  <span class="toctext">Von Neumann bottleneck</span>](https://en.wikipedia.org/wiki/Von_Neumann_architecture#Von_Neumann_bottleneck)

- [<span class="tocnumber">7</span>

   

  <span class="toctext">Non-von Neumann processors</span>](https://en.wikipedia.org/wiki/Von_Neumann_architecture#Non-von_Neumann_processors)

- [<span class="tocnumber">8</span>

   

  <span class="toctext">See also</span>](https://en.wikipedia.org/wiki/Von_Neumann_architecture#See_also)

- [<span class="tocnumber">9</span>

   

  <span class="toctext">References</span>](https://en.wikipedia.org/wiki/Von_Neumann_architecture#References)

  - [<span class="tocnumber">9.1</span>

     

    <span class="toctext">Inline</span>](https://en.wikipedia.org/wiki/Von_Neumann_architecture#Inline)

  - [<span class="tocnumber">9.2</span>

     

    <span class="toctext">General</span>](https://en.wikipedia.org/wiki/Von_Neumann_architecture#General)

- [<span class="tocnumber">10</span>

   

  <span class="toctext">External links</span>](https://en.wikipedia.org/wiki/Von_Neumann_architecture#External_links)

## <span id="History" class="mw-headline">History</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Von_Neumann_architecture&amp;action=edit&amp;section=1" title="Edit section: History">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

The earliest computing machines had fixed programs. Some very simple computers still use this design, either for simplicity or training purposes. For example, a desk [calculator](https://en.wikipedia.org/wiki/Calculator "Calculator") (in principle) is a fixed program computer. It can do basic [mathematics](https://en.wikipedia.org/wiki/Mathematics "Mathematics"), but it cannot be used as a [word processor](https://en.wikipedia.org/wiki/Word_processor "Word processor") or a gaming console. Changing the program of a fixed-program machine requires re-wiring, re-structuring, or re-designing the machine. The earliest computers were not so much "programmed" as they were "designed". "Reprogramming", when it was possible at all, was a laborious process, starting with [flowcharts](https://en.wikipedia.org/wiki/Flowchart "Flowchart") and paper notes, followed by detailed engineering designs, and then the often-arduous process of physically re-wiring and re-building the machine. It could take three weeks to set up a program on [ENIAC](https://en.wikipedia.org/wiki/ENIAC "ENIAC") and get it working.

<sup>
  <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-4"><span>[</span>4<span>]</span></a>
</sup>

With the proposal of the stored-program computer this changed. A stored-program computer includes by design an [instruction set](https://en.wikipedia.org/wiki/Instruction_set "Instruction set") and can store in memory a set of instructions (a [program](https://en.wikipedia.org/wiki/Computer_program "Computer program")) that details the [computation](https://en.wikipedia.org/wiki/Computation "Computation").

A stored-program design also allows for [self-modifying code](https://en.wikipedia.org/wiki/Self-modifying_code "Self-modifying code"). One early motivation for such a facility was the need for a program to increment or otherwise modify the address portion of instructions, which had to be done manually in early designs. This became less important when [index registers](https://en.wikipedia.org/wiki/Index_register "Index register") and [indirect addressing](https://en.wikipedia.org/wiki/Addressing_mode "Addressing mode") became usual features of machine architecture. Another use was to embed frequently used data in the instruction stream using [immediate addressing](https://en.wikipedia.org/wiki/Addressing_mode "Addressing mode"). Self-modifying code has largely fallen out of favor, since it is usually hard to understand and [debug](https://en.wikipedia.org/wiki/Debugging "Debugging"), as well as being inefficient under modern processor pipelining and caching schemes.

On a large scale, the ability to treat instructions as data is what makes [assemblers](https://en.wikipedia.org/wiki/Assembly_language#Assembler "Assembly language"), [compilers](https://en.wikipedia.org/wiki/Compiler "Compiler") and other automated programming tools possible. One can "write programs which write programs".

<sup>
  <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-5"><span>[</span>5<span>]</span></a>
</sup>

 On a smaller scale, repetitive I/O-intensive operations such as the [BITBLT](https://en.wikipedia.org/wiki/Bit_blit "Bit blit") image manipulation primitive or [pixel & vertex shaders](https://en.wikipedia.org/wiki/HLSL "HLSL") in modern 3D graphics, were considered inefficient to run without custom hardware. These operations could be accelerated on general purpose processors with "on the fly compilation" ("[just-in-time compilation](https://en.wikipedia.org/wiki/Just-in-time_compilation "Just-in-time compilation")") technology, e.g., code-generating programs--one form of self-modifying code that has remained popular.

There are drawbacks to the Von Neumann design. Aside from the Von Neumann bottleneck described below, program modifications can be quite harmful, either by accident or design. In some simple stored-program computer designs, a malfunctioning program can damage itself, other programs, or the [operating system](https://en.wikipedia.org/wiki/Operating_system "Operating system"), possibly leading to a computer [crash](https://en.wikipedia.org/wiki/Crash_(computing) "Crash (computing)"). [Memory protection](https://en.wikipedia.org/wiki/Memory_protection "Memory protection") and other forms of [access control](https://en.wikipedia.org/wiki/Access_control "Access control") can usually protect against both accidental and malicious program modification.

## <span id="Development_of_the_stored-program_concept" class="mw-headline">Development of the stored-program concept</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Von_Neumann_architecture&amp;action=edit&amp;section=2" title="Edit section: Development of the stored-program concept">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

The mathematician [Alan Turing](https://en.wikipedia.org/wiki/Alan_Turing "Alan Turing"), who had been alerted to a problem of mathematical logic by the lectures of [Max Newman](https://en.wikipedia.org/wiki/Max_Newman "Max Newman") at the [University of Cambridge](https://en.wikipedia.org/wiki/University_of_Cambridge "University of Cambridge"), wrote a paper in 1936 entitled _On Computable Numbers, with an Application to the [Entscheidungsproblem](https://en.wikipedia.org/wiki/Entscheidungsproblem "Entscheidungsproblem")_, which was published in the _Proceedings of the London Mathematical Society_.

<sup>
  <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-Turing1936-6"><span>[</span>6<span>]</span></a>
</sup>

 In it he described a hypothetical machine which he called a "universal computing machine", and which is now known as the "[Universal Turing machine](https://en.wikipedia.org/wiki/Universal_Turing_machine "Universal Turing machine")". The hypothetical machine had an infinite store (memory in today's terminology) that contained both instructions and data. [John von Neumann](https://en.wikipedia.org/wiki/John_von_Neumann "John von Neumann") became acquainted with Turing while he was a visiting professor at Cambridge in 1935, and also during Turing's PhD year at the [Institute for Advanced Study](https://en.wikipedia.org/wiki/Institute_for_Advanced_Study "Institute for Advanced Study") in [Princeton, New Jersey](https://en.wikipedia.org/wiki/Princeton,_New_Jersey "Princeton, New Jersey") during 1936 – 37\. Whether he knew of Turing's paper of 1936 at that time is not clear.

In 1936 [Konrad Zuse](https://en.wikipedia.org/wiki/Konrad_Zuse "Konrad Zuse") also anticipated in two patent applications that machine instructions could be stored in the same storage used for data.

<sup>
  <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-7"><span>[</span>7<span>]</span></a>
</sup>

Independently, [J. Presper Eckert](https://en.wikipedia.org/wiki/J._Presper_Eckert "J. Presper Eckert") and [John Mauchly](https://en.wikipedia.org/wiki/John_Mauchly "John Mauchly"), who were developing the [ENIAC](https://en.wikipedia.org/wiki/ENIAC "ENIAC") at the [Moore School of Electrical Engineering](https://en.wikipedia.org/wiki/Moore_School_of_Electrical_Engineering "Moore School of Electrical Engineering"), at the [University of Pennsylvania](https://en.wikipedia.org/wiki/University_of_Pennsylvania "University of Pennsylvania"), wrote about the stored-program concept in December 1943.

<sup>
  <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-8"><span>[</span>8<span>]</span></a>
  <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-9"><span>[</span>9<span>]</span></a>
</sup>

 In planning a new machine, [EDVAC](https://en.wikipedia.org/wiki/EDVAC "EDVAC"), Eckert wrote in January 1944 that they would store data and programs in a new addressable memory device, a mercury metal [delay line memory](https://en.wikipedia.org/wiki/Delay_line_memory "Delay line memory"). This was the first time the construction of a practical stored-program machine was proposed. At that time, he and Mauchly were not aware of Turing's work.

Von Neumann was involved in the [Manhattan Project](https://en.wikipedia.org/wiki/Manhattan_Project "Manhattan Project") at the [Los Alamos National Laboratory](https://en.wikipedia.org/wiki/Los_Alamos_National_Laboratory "Los Alamos National Laboratory"), which required huge amounts of calculation. This drew him to the ENIAC project, during the summer of 1944\. There he joined into the ongoing discussions on the design of this stored-program computer, the EDVAC. As part of that group, he volunteered to write up a description of it and produced the _First Draft of a Report on the EDVAC_

<sup>
  <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-FirstDraftReport-1"><span>[</span>1<span>]</span></a>
</sup>

 which included ideas from Eckert and Mauchly. It was unfinished when his colleague [Herman Goldstine](https://en.wikipedia.org/wiki/Herman_Goldstine "Herman Goldstine") circulated it with only von Neumann's name on it, to the consternation of Eckert and Mauchly.

<sup>
  <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-10"><span>[</span>10<span>]</span></a>
</sup>

 The paper was read by dozens of von Neumann's colleagues in America and Europe, and influenced the next round of computer designs.

Hence, Von Neumann was not alone in developing the idea of the stored-program architecture, and [Jack Copeland](https://en.wikipedia.org/wiki/Jack_Copeland "Jack Copeland") considers that it is "historically inappropriate, to refer to electronic stored-program digital computers as 'von Neumann machines'".

<sup>
  <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-11"><span>[</span>11<span>]</span></a>
</sup>

 His Los Alamos colleague [Stan Frankel](https://en.wikipedia.org/wiki/Stan_Frankel "Stan Frankel") said of von Neumann's regard for Turing's ideas:

> _I know that in or about 1943 or '44 von Neumann was well aware of the fundamental importance of Turing's paper of 1936 ... Von Neumann introduced me to that paper and at his urging I studied it with care. Many people have acclaimed von Neumann as the "father of the computer" (in a modern sense of the term) but I am sure that he would never have made that mistake himself. He might well be called the midwife, perhaps, but he firmly emphasized to me, and to others I am sure, that the fundamental conception is owing to Turing-- in so far as not anticipated by Babbage ... Both Turing and von Neumann, of course, also made substantial contributions to the "reduction to practice" of these concepts but I would not regard these as comparable in importance with the introduction and explication of the concept of a computer able to store in its memory its program of activities and of modifying that program in the course of these activities._ 

> <sup>
>   <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-12"><span>[</span>12<span>]</span></a>
> </sup>

At the time that the "First Draft" report was circulated, Turing was producing a report entitled _Proposed Electronic Calculator_ which described in engineering and programming detail, his idea of a machine that was called the [Automatic Computing Engine (ACE)](https://en.wikipedia.org/wiki/Automatic_Computing_Engine "Automatic Computing Engine").

<sup>
  <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-13"><span>[</span>13<span>]</span></a>
</sup>

 He presented this to the Executive Committee of the British [National Physical Laboratory](https://en.wikipedia.org/wiki/National_Physical_Laboratory,_UK "National Physical Laboratory, UK") on February 19, 1946\. Although Turing knew from his wartime experience at Bletchley Park that what he proposed was feasible, the secrecy surrounding [Colossus](https://en.wikipedia.org/wiki/Colossus_computer "Colossus computer"), that was subsequently maintained for several decades, prevented him from saying so. Various successful implementations of the ACE design were produced.

Both von Neumann's and Turing's papers described stored-program computers, but von Neumann's earlier paper achieved greater circulation and the computer architecture it outlined became known as the "von Neumann architecture". In the 1953 publication _Faster than Thought: A Symposium on Digital Computing Machines_ (edited by B.V. Bowden), a section in the chapter on _Computers in America_ reads as follows:

<sup>
  <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-14"><span>[</span>14<span>]</span></a>
</sup>

> **_THE MACHINE OF THE INSTITUTE FOR ADVANCED STUDIES, PRINCETON_**

> _In 1945, Professor J. von Neumann, who was then working at the Moore School of Engineering in Philadelphia, where the E.N.I.A.C. had been built, issued on behalf of a group of his co-workers a report on the logical design of digital computers. The report contained a fairly detailed proposal for the design of the machine which has since become known as the E.D.V.A.C. (electronic discrete variable automatic computer). This machine has only recently been completed in America, but the von Neumann report inspired the construction of the E.D.S.A.C. (electronic delay-storage automatic calculator) in Cambridge (see page 130)._

> _In 1947, Burks, Goldstine and von Neumann published another report which outlined the design of another type of machine (a parallel machine this time) which should be exceedingly fast, capable perhaps of 20,000 operations per second. They pointed out that the outstanding problem in constructing such a machine was in the development of a suitable memory, all the contents of which were instantaneously accessible, and at first they suggested the use of a special [vacuum tube](https://en.wikipedia.org/wiki/Vacuum_tube "Vacuum tube") -- called the "[Selectron](https://en.wikipedia.org/wiki/Selectron_tube "Selectron tube")" – which had been invented by the Princeton Laboratories of the R.C.A. These tubes were expensive and difficult to make, so von Neumann subsequently decided to build a machine based on the [Williams memory](https://en.wikipedia.org/wiki/Williams_tube "Williams tube"). This machine, which was completed in June, 1952 in Princeton has become popularly known as the Maniac. The design of this machine has inspired that of half a dozen or more machines which are now being built in America, all of which are known affectionately as "Johniacs."'_

In the same book, the first two paragraphs of a chapter on ACE read as follows:

<sup>
  <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-15"><span>[</span>15<span>]</span></a>
</sup>

> **_AUTOMATIC COMPUTATION AT THE NATIONAL PHYSICAL LABORATORY'_**

> _One of the most modern digital computers which embodies developments and improvements in the technique of automatic electronic computing was recently demonstrated at the National Physical Laboratory, Teddington, where it has been designed and built by a small team of mathematicians and electronics research engineers on the staff of the Laboratory, assisted by a number of production engineers from the English Electric Company, Limited. The equipment so far erected at the Laboratory is only the pilot model of a much larger installation which will be known as the Automatic Computing Engine, but although comparatively small in bulk and containing only about 800 thermionic valves, as can be judged from Plates XII, XIII and XIV, it is an extremely rapid and versatile calculating machine._

> _The basic concepts and abstract principles of computation by a machine were formulated by Dr. A. M. Turing, F.R.S., in a paper

> <sup>1</sup>

> . read before the London Mathematical Society in 1936, but work on such machines in Britain was delayed by the war. In 1945, however, an examination of the problems was made at the National Physical Laboratory by Mr. J. R. Womersley, then superintendent of the Mathematics Division of the Laboratory. He was joined by Dr. Turing and a small staff of specialists, and, by 1947, the preliminary planning was sufficiently advanced to warrant the establishment of the special group already mentioned. In April, 1948, the latter became the Electronics Section of the Laboratory, under the charge of Mr. F. M. Colebrook._

## <span id="Early_von_Neumann-architecture_computers" class="mw-headline">Early von Neumann-architecture computers</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Von_Neumann_architecture&amp;action=edit&amp;section=3" title="Edit section: Early von Neumann-architecture computers">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

The _First Draft_ described a design that was used by many universities and corporations to construct their computers.

<sup>
  <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-16"><span>[</span>16<span>]</span></a>
</sup>

 Among these various computers, only ILLIAC and ORDVAC had compatible instruction sets.

- [Manchester Small-Scale Experimental Machine](https://en.wikipedia.org/wiki/Manchester_Small-Scale_Experimental_Machine "Manchester Small-Scale Experimental Machine") (SSEM), nicknamed "Baby" (University of Manchester, England) made its first successful run of a stored-program on June 21, 1948.
- [EDSAC](https://en.wikipedia.org/wiki/EDSAC "EDSAC") (University of Cambridge, England) was the first practical stored-program electronic computer (May 1949)
- [Manchester Mark 1](https://en.wikipedia.org/wiki/Manchester_Mark_1 "Manchester Mark 1") (University of Manchester, England) Developed from the SSEM (June 1949)
- [CSIRAC](https://en.wikipedia.org/wiki/CSIRAC "CSIRAC") ([Council for Scientific and Industrial Research](https://en.wikipedia.org/wiki/Commonwealth_Scientific_and_Industrial_Research_Organisation "Commonwealth Scientific and Industrial Research Organisation")) Australia (November 1949)
- [EDVAC](https://en.wikipedia.org/wiki/EDVAC "EDVAC") ([Ballistic Research Laboratory](https://en.wikipedia.org/wiki/Ballistic_Research_Laboratory "Ballistic Research Laboratory"), Computing Laboratory at [Aberdeen Proving Ground](https://en.wikipedia.org/wiki/Aberdeen_Proving_Ground "Aberdeen Proving Ground") 1951)
- [ORDVAC](https://en.wikipedia.org/wiki/ORDVAC "ORDVAC") (U-Illinois) at Aberdeen Proving Ground, Maryland (completed November 1951)

  <sup>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-17"><span>[</span>17<span>]</span></a>
  </sup>

- [IAS machine](https://en.wikipedia.org/wiki/IAS_machine "IAS machine") at Princeton University (January 1952)
- [MANIAC I](https://en.wikipedia.org/wiki/MANIAC_I "MANIAC I") at Los Alamos Scientific Laboratory (March 1952)
- [ILLIAC](https://en.wikipedia.org/wiki/ILLIAC "ILLIAC") at the University of Illinois, (September 1952)
- [BESM-1](https://en.wikipedia.org/wiki/BESM "BESM") in Moscow (1952)
- [AVIDAC](https://en.wikipedia.org/wiki/AVIDAC "AVIDAC") at Argonne National Laboratory (1953)
- [ORACLE](https://en.wikipedia.org/wiki/ORACLE_(computer) "ORACLE (computer)") at Oak Ridge National Laboratory (June 1953)
- [BESK](https://en.wikipedia.org/wiki/BESK "BESK") in Stockholm (1953)
- [JOHNNIAC](https://en.wikipedia.org/wiki/JOHNNIAC "JOHNNIAC") at RAND Corporation (January 1954)
- [DASK](https://en.wikipedia.org/wiki/DASK "DASK") in Denmark (1955)
- [WEIZAC](https://en.wikipedia.org/wiki/WEIZAC "WEIZAC") in Rehovoth (1955)
- [PERM](https://en.wikipedia.org/wiki/PERM_(computer) "PERM (computer)") in Munich (1956?)
- [SILLIAC](https://en.wikipedia.org/wiki/SILLIAC "SILLIAC") in Sydney (1956)

## <span id="Early_stored-program_computers" class="mw-headline">Early stored-program computers</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Von_Neumann_architecture&amp;action=edit&amp;section=4" title="Edit section: Early stored-program computers">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

The date information in the following chronology is difficult to put into proper order. Some dates are for first running a test program, some dates are the first time the computer was demonstrated or completed, and some dates are for the first delivery or installation.

- The [IBM SSEC](https://en.wikipedia.org/wiki/IBM_SSEC "IBM SSEC") had the ability to treat instructions as data, and was publicly demonstrated on January 27, 1948\. This ability was claimed in a US patent.

  <sup>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-18"><span>[</span>18<span>]</span></a>
  </sup>

   However it was partially electromechanical, not fully electronic. In practice, instructions were read from paper tape due to its limited memory.

  <sup>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-herb-19"><span>[</span>19<span>]</span></a>
  </sup>

- The [Manchester](https://en.wikipedia.org/wiki/Victoria_University_of_Manchester "Victoria University of Manchester") [SSEM](https://en.wikipedia.org/wiki/Manchester_Small-Scale_Experimental_Machine "Manchester Small-Scale Experimental Machine") (the _Baby_) was the first fully electronic computer to run a stored program. It ran a factoring program for 52 minutes on June 21, 1948, after running a simple division program and a program to show that two numbers were [relatively prime](https://en.wikipedia.org/wiki/Coprime "Coprime").
- The [ENIAC](https://en.wikipedia.org/wiki/ENIAC "ENIAC") was modified to run as a primitive read-only stored-program computer (using the Function Tables for program [ROM](https://en.wikipedia.org/wiki/Read-only_memory "Read-only memory")) and was demonstrated as such on September 16, 1948, running a program by [Adele Goldstine](https://en.wikipedia.org/wiki/Adele_Goldstine "Adele Goldstine") for von Neumann.
- The [BINAC](https://en.wikipedia.org/wiki/BINAC "BINAC") ran some test programs in February, March, and April 1949, although was not completed until September 1949.
- The [Manchester Mark 1](https://en.wikipedia.org/wiki/Manchester_Mark_1 "Manchester Mark 1") developed from the SSEM project. An intermediate version of the Mark 1 was available to run programs in April 1949, but was not completed until October 1949.
- The [EDSAC](https://en.wikipedia.org/wiki/Electronic_Delay_Storage_Automatic_Calculator "Electronic Delay Storage Automatic Calculator") ran its first program on May 6, 1949.
- The [EDVAC](https://en.wikipedia.org/wiki/EDVAC "EDVAC") was delivered in August 1949, but it had problems that kept it from being put into regular operation until 1951.
- The [CSIR Mk I](https://en.wikipedia.org/wiki/CSIRAC "CSIRAC") ran its first program in November 1949.
- The [SEAC](https://en.wikipedia.org/wiki/SEAC_(computer) "SEAC (computer)") was demonstrated in April 1950.
- The [Pilot ACE](https://en.wikipedia.org/wiki/Pilot_ACE "Pilot ACE") ran its first program on May 10, 1950 and was demonstrated in December 1950.
- The [SWAC](https://en.wikipedia.org/wiki/SWAC_(computer) "SWAC (computer)") was completed in July 1950.
- The [Whirlwind](https://en.wikipedia.org/wiki/Whirlwind_(computer) "Whirlwind (computer)") was completed in December 1950 and was in actual use in April 1951.
- The first [ERA Atlas](https://en.wikipedia.org/wiki/UNIVAC_1101 "UNIVAC 1101") (later the commercial ERA 1101/UNIVAC 1101) was installed in December 1950.

## <span id="Evolution" class="mw-headline">Evolution</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Von_Neumann_architecture&amp;action=edit&amp;section=5" title="Edit section: Evolution">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

[![](https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Computer_system_bus.svg/220px-Computer_system_bus.svg.png)](https://en.wikipedia.org/wiki/File:Computer_system_bus.svg) [![](https://bits.wikimedia.org/static-1.24wmf4/skins/common/images/magnify-clip.png)](https://en.wikipedia.org/wiki/File:Computer_system_bus.svg "Enlarge")

Single [system bus](https://en.wikipedia.org/wiki/System_bus "System bus") evolution of the architecture

Through the decades of the 1960s and 1970s computers generally became both smaller and faster, which led to some evolutions in their architecture. For example, [memory-mapped I/O](https://en.wikipedia.org/wiki/Memory-mapped_I/O "Memory-mapped I/O") allows input and output devices to be treated the same as memory.

<sup>
  <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-20"><span>[</span>20<span>]</span></a>
</sup>

 A single [system bus](https://en.wikipedia.org/wiki/System_bus "System bus") could be used to provide a modular system with lower cost. This is sometimes called a "streamlining" of the architecture.

<sup>
  <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-21"><span>[</span>21<span>]</span></a>
</sup>

 In subsequent decades, simple [microcontrollers](https://en.wikipedia.org/wiki/Microcontrollers "Microcontrollers") would sometimes omit features of the model to lower cost and size. Larger computers added features for higher performance.

## <span id="Von_Neumann_bottleneck" class="mw-headline">Von Neumann bottleneck</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Von_Neumann_architecture&amp;action=edit&amp;section=6" title="Edit section: Von Neumann bottleneck">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

The shared bus between the program memory and data memory leads to the _Von Neumann bottleneck_, the limited [throughput](https://en.wikipedia.org/wiki/Throughput "Throughput") (data transfer rate) between the CPU and memory compared to the amount of memory. Because program memory and data memory cannot be accessed at the same time, throughput is much smaller than the rate at which the CPU can work. This seriously limits the effective processing speed when the CPU is required to perform minimal processing on large amounts of data. The CPU is continually [forced to wait](https://en.wikipedia.org/wiki/Wait_state "Wait state") for needed data to be transferred to or from memory. Since CPU speed and memory size have increased much faster than the throughput between them, the bottleneck has become more of a problem, a problem whose severity increases with every newer generation of CPU.

The term "von Neumann bottleneck" was coined by [John Backus](https://en.wikipedia.org/wiki/John_Backus "John Backus") in his 1977 ACM [Turing Award](https://en.wikipedia.org/wiki/Turing_Award "Turing Award") lecture. According to Backus:

> Surely there must be a less primitive way of making big changes in the store than by pushing vast numbers of [words](https://en.wikipedia.org/wiki/Word_(data_type) "Word (data type)") back and forth through the von Neumann bottleneck. Not only is this tube a literal bottleneck for the data traffic of a problem, but, more importantly, it is an intellectual bottleneck that has kept us tied to word-at-a-time thinking instead of encouraging us to think in terms of the larger conceptual units of the task at hand. Thus programming is basically planning and detailing the enormous traffic of words through the von Neumann bottleneck, and much of that traffic concerns not significant data itself, but where to find it.

> <sup>
>   <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-backus-22"><span>[</span>22<span>]</span></a>
>   <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-23"><span>[</span>23<span>]</span></a>
> </sup>

The performance problem can be alleviated (to some extent) by several mechanisms. Providing a [cache](https://en.wikipedia.org/wiki/CPU_cache "CPU cache") between the CPU and the main memory, providing separate caches or separate access paths for data and instructions (the so-called [Modified Harvard architecture](https://en.wikipedia.org/wiki/Modified_Harvard_architecture "Modified Harvard architecture")), using [branch predictor](https://en.wikipedia.org/wiki/Branch_predictor "Branch predictor") algorithms and logic, and providing a limited CPU stack or other on-chip [scratchpad memory](https://en.wikipedia.org/wiki/Scratchpad_memory "Scratchpad memory") to reduce memory access are four of the ways performance is increased. The problem can also be sidestepped somewhat by using [parallel computing](https://en.wikipedia.org/wiki/Parallel_computing "Parallel computing"), using for example the [Non-Uniform Memory Access](https://en.wikipedia.org/wiki/Non-Uniform_Memory_Access "Non-Uniform Memory Access") (NUMA) architecture--this approach is commonly employed by supercomputers. It is less clear whether the _intellectual bottleneck_ that Backus criticized has changed much since 1977\. Backus's proposed solution has not had a major influence.

<sup>[<em>
  <a href="https://en.wikipedia.org/wiki/Wikipedia:Citation_needed" title="Wikipedia:Citation needed"><span title="This claim needs references to reliable sources. (December 2010)">citation\ needed</span></a>
</em>]</sup>

 Modern [functional programming](https://en.wikipedia.org/wiki/Functional_programming "Functional programming") and [object-oriented programming](https://en.wikipedia.org/wiki/Object-oriented_programming "Object-oriented programming") are much less geared towards "pushing vast numbers of words back and forth" than earlier languages like [Fortran](https://en.wikipedia.org/wiki/Fortran "Fortran") were, but internally, that is still what computers spend much of their time doing, even highly parallel supercomputers.

As of 1996, a database benchmark study found that three out of four CPU cycles were spent waiting for memory. Researchers expect that increasing the number of simultaneous instruction streams with [multithreading](https://en.wikipedia.org/wiki/Multithreading_(computer_architecture) "Multithreading (computer architecture)") or single-chip [multiprocessing](https://en.wikipedia.org/wiki/Multiprocessing "Multiprocessing") will make this bottleneck even worse. 

<sup>
  <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-24"><span>[</span>24<span>]</span></a>
</sup>

## <span id="Non-von_Neumann_processors" class="mw-headline">Non-von Neumann processors</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Von_Neumann_architecture&amp;action=edit&amp;section=7" title="Edit section: Non-von Neumann processors">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

The [National Semiconductor](https://en.wikipedia.org/wiki/National_Semiconductor "National Semiconductor") [COP8](https://en.wikipedia.org/wiki/COP8 "COP8") was introduced in 1986; it has a [Modified Harvard architecture](https://en.wikipedia.org/wiki/Modified_Harvard_architecture "Modified Harvard architecture").

<sup>
  <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-25"><span>[</span>25<span>]</span></a>
  <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_note-26"><span>[</span>26<span>]</span></a>
</sup>

Perhaps the most common kind of non-von Neumann structure used in modern computers is [content-addressable memory](https://en.wikipedia.org/wiki/Content-addressable_memory "Content-addressable memory") (CAM).

## <span id="See_also" class="mw-headline">See also</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Von_Neumann_architecture&amp;action=edit&amp;section=8" title="Edit section: See also">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

[![Portal icon](https://upload.wikimedia.org/wikipedia/commons/thumb/d/d2/Internet_map_1024.jpg/28px-Internet_map_1024.jpg)](https://en.wikipedia.org/wiki/File:Internet_map_1024.jpg) | [Computer science portal](https://en.wikipedia.org/wiki/Portal:Computer_science "Portal:Computer science")
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------

- [CARDboard Illustrative Aid to Computation](https://en.wikipedia.org/wiki/CARDboard_Illustrative_Aid_to_Computation "CARDboard Illustrative Aid to Computation")
- [Harvard architecture](https://en.wikipedia.org/wiki/Harvard_architecture "Harvard architecture")
- [Interconnect bottleneck](https://en.wikipedia.org/wiki/Interconnect_bottleneck "Interconnect bottleneck")
- [Little man computer](https://en.wikipedia.org/wiki/Little_man_computer "Little man computer")
- [Modified Harvard architecture](https://en.wikipedia.org/wiki/Modified_Harvard_architecture "Modified Harvard architecture")
- [Random access machine](https://en.wikipedia.org/wiki/Random_access_machine "Random access machine")
- [Turing machine](https://en.wikipedia.org/wiki/Turing_machine "Turing machine")

## <span id="References" class="mw-headline">References</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Von_Neumann_architecture&amp;action=edit&amp;section=9" title="Edit section: References">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

### <span id="Inline" class="mw-headline">Inline</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Von_Neumann_architecture&amp;action=edit&amp;section=10" title="Edit section: Inline">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

1. <span class="mw-cite-backlink">^ <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-FirstDraftReport_1-0">
    <sup>
    <strong>
    <em>a</em>
  </strong>
  </sup>
  </a> <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-FirstDraftReport_1-1">
    <sup>
    <strong>
    <em>b</em>
  </strong>
  </sup>
  </a> <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-FirstDraftReport_1-2">
    <sup>
    <strong>
    <em>c</em>
  </strong>
  </sup>
  </a></span>

   

  <span class="reference-text">
    <span id="CITEREFvon_Neumann1945" class="citation"><a href="https://en.wikipedia.org/wiki/John_von_Neumann" title="John von Neumann">von Neumann, John</a> (1945), <a href="http://qss.stanford.edu/~godfrey/vonNeumann/vnedvac.pdf" class="external text"><em>First Draft of a Report on the EDVAC</em></a><span class="reference-accessdate">, retrieved August 24, 2011</span></span>
    <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.aufirst=John&amp;rft.aulast=von+Neumann&amp;rft.au=von+Neumann%2C+John&amp;rft.btitle=First+Draft+of+a+Report+on+the+EDVAC&amp;rft.date=1945&amp;rft.genre=book&amp;rft_id=http%3A%2F%2Fqss.stanford.edu%2F~godfrey%2FvonNeumann%2Fvnedvac.pdf&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>
  </span>

2. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-GanesanCh4_2-0">^</a>
  </strong>
  </span>

   

  <span class="reference-text">
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#CITEREFGanesan2009">Ganesan 2009</a>
  </span>

3. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-3">^</a>
  </strong>
  </span>

   

  <span class="reference-text">
    <span id="CITEREFMarkgraf2007" class="citation">Markgraf, Joey D. (2007), <a href="http://aws.linnbenton.edu/cs271c/markgrj/" class="external text"><em>The Von Neumann bottleneck</em></a><span class="reference-accessdate">, retrieved August 24, 2011</span></span>
    <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.aufirst=Joey+D.&amp;rft.aulast=Markgraf&amp;rft.au=Markgraf%2C+Joey+D.&amp;rft.btitle=The+Von+Neumann+bottleneck&amp;rft.date=2007&amp;rft.genre=book&amp;rft_id=http%3A%2F%2Faws.linnbenton.edu%2Fcs271c%2Fmarkgrj%2F&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>
  </span>

4. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-4">^</a>
  </strong>
  </span>

   

  <span class="reference-text"><a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#CITEREFCopeland2006">Copeland 2006</a>, p. 104</span>

5. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-5">^</a>
  </strong>
  </span>

   

  <span class="reference-text">
    <span id="CITEREF" class="citation">
    <a href="http://catb.org/~esr/jargon/html/M/MFTL.html" class="external text">MFTL <em>(My Favorite Toy Language) entry Jargon File 4.4.7</em></a>
    <span class="reference-accessdate">, retrieved 2008-07-11</span>
  </span>
    <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.btitle=%27%27MFTL%27%27+%28My+Favorite+Toy+Language%29+entry+Jargon+File+4.4.7&amp;rft.genre=book&amp;rft_id=http%3A%2F%2Fcatb.org%2F~esr%2Fjargon%2Fhtml%2FM%2FMFTL.html&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>
  </span>

6. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-Turing1936_6-0">^</a>
  </strong>
  </span>

   

  <span class="reference-text"><span id="CITEREFTuring1936" class="citation">Turing, A.M. (1936), “On Computable Numbers, with an Application to the Entscheidungsproblem”, <em>Proceedings of the London Mathematical Society</em>, 2 (1937) <strong>42</strong>: 230–65, <a href="https://en.wikipedia.org/wiki/Digital_object_identifier" title="Digital object identifier">doi</a>:<a href="http://dx.doi.org/10.1112%2Fplms%2Fs2-42.1.230" class="external text">10.1112/plms/s2-42.1.230</a></span><span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.atitle=On+Computable+Numbers%2C+with+an+Application+to+the+Entscheidungsproblem&amp;rft.aufirst=A.M.&amp;rft.aulast=Turing&amp;rft.au=Turing%2C+A.M.&amp;rft.date=1936&amp;rft.genre=article&amp;rft_id=info%3Adoi%2F10.1112%2Fplms%2Fs2-42.1.230&amp;rft.jtitle=Proceedings+of+the+London+Mathematical+Society&amp;rft.pages=230-65&amp;rft.series=2&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Ajournal&amp;rft.volume=42">
    <span>
  </span>
  </span> (and <span id="CITEREFTuring1938" class="citation">Turing, A.M. (1938), “On Computable Numbers, with an Application to the Entscheidungsproblem. A correction”, <em>Proceedings of the London Mathematical Society</em>, 2 (1937) <strong>43</strong> (6): 544–6, <a href="https://en.wikipedia.org/wiki/Digital_object_identifier" title="Digital object identifier">doi</a>:<a href="http://dx.doi.org/10.1112%2Fplms%2Fs2-43.6.544" class="external text">10.1112/plms/s2-43.6.544</a></span><span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.atitle=On+Computable+Numbers%2C+with+an+Application+to+the+Entscheidungsproblem.+A+correction&amp;rft.aufirst=A.M.&amp;rft.aulast=Turing&amp;rft.au=Turing%2C+A.M.&amp;rft.date=1938&amp;rft.genre=article&amp;rft_id=info%3Adoi%2F10.1112%2Fplms%2Fs2-43.6.544&amp;rft.issue=6&amp;rft.jtitle=Proceedings+of+the+London+Mathematical+Society&amp;rft.pages=544-6&amp;rft.series=2&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Ajournal&amp;rft.volume=43">
    <span>
  </span>
  </span>)</span>

7. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-7">^</a>
  </strong>
  </span>

   

  <span class="reference-text">
    <span id="CITEREF1948" class="citation"><a href="http://www.computer50.org/kgill/mark1/natletter.html" class="external text">“Electronic Digital Computers”</a>, <em>Nature</em> <strong>162</strong>, September 25, 1948: 487, <a href="https://en.wikipedia.org/wiki/Digital_object_identifier" title="Digital object identifier">doi</a>:<a href="http://dx.doi.org/10.1038%2F162487a0" class="external text">10.1038/162487a0</a><span class="reference-accessdate">, retrieved 2009-04-10</span></span>
    <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.atitle=Electronic+Digital+Computers&amp;rft.date=September+25%2C+1948&amp;rft.genre=article&amp;rft_id=http%3A%2F%2Fwww.computer50.org%2Fkgill%2Fmark1%2Fnatletter.html&amp;rft_id=info%3Adoi%2F10.1038%2F162487a0&amp;rft.jtitle=Nature&amp;rft.pages=487&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Ajournal&amp;rft.volume=162">
    <span>
  </span>
  </span>
  </span>

8. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-8">^</a>
  </strong>
  </span>

   

  <span class="reference-text">
    <span id="CITEREFLukoff1979" class="citation">Lukoff, Herman (1979), <em>From Dits to Bits…: A Personal History of the Electronic Computer</em>, Robotics Press, <a href="https://en.wikipedia.org/wiki/International_Standard_Book_Number" title="International Standard Book Number">ISBN</a> <a href="https://en.wikipedia.org/wiki/Special:BookSources/978-0-89661-002-6" title="Special:BookSources/978-0-89661-002-6">978-0-89661-002-6</a></span>
    <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.aufirst=Herman&amp;rft.aulast=Lukoff&amp;rft.au=Lukoff%2C+Herman&amp;rft.btitle=From+Dits+to+Bits...%3A+A+Personal+History+of+the+Electronic+Computer&amp;rft.date=1979&amp;rft.genre=book&amp;rft.isbn=978-0-89661-002-6&amp;rft.pub=Robotics+Press&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>
  </span>

9. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-9">^</a>
  </strong>
  </span>

   

  <span class="reference-text">ENIAC project administrator Grist Brainerd’s December 1943 progress report for the first period of the ENIAC’s development implicitly proposed the stored program concept (while simultaneously rejecting its implementation in the ENIAC) by stating that “in order to have the simplest project and not to complicate matters” the ENIAC would be constructed without any “automatic regulation”.</span>

10. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-10">^</a>
  </strong>
  </span>

   

  <span class="reference-text"><a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#CITEREFCopeland2006">Copeland 2006</a>, p. 113</span>

11. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-11">^</a>
  </strong>
  </span>

   

  <span class="reference-text">
    <span id="CITEREFCopeland2000" class="citation"><a href="https://en.wikipedia.org/wiki/Jack_Copeland" title="Jack Copeland">Copeland, Jack</a> (2000), <a href="http://www.alanturing.net/turing_archive/pages/Reference%20Articles/BriefHistofComp.html#ACE" class="external text"><em>A Brief History of Computing: ENIAC and EDVAC</em></a><span class="reference-accessdate">, retrieved January 27, 2010</span></span>
    <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.au=Copeland%2C+Jack&amp;rft.aufirst=Jack&amp;rft.aulast=Copeland&amp;rft.btitle=A+Brief+History+of+Computing%3A+ENIAC+and+EDVAC&amp;rft.date=2000&amp;rft.genre=book&amp;rft_id=http%3A%2F%2Fwww.alanturing.net%2Fturing_archive%2Fpages%2FReference%2520Articles%2FBriefHistofComp.html%23ACE&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>
  </span>

12. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-12">^</a>
  </strong>
  </span>

   

  <span class="reference-text"><span id="CITEREFCopeland2000" class="citation"><a href="https://en.wikipedia.org/wiki/Jack_Copeland" title="Jack Copeland">Copeland, Jack</a> (2000), <a href="http://www.alanturing.net/turing_archive/pages/Reference%20Articles/BriefHistofComp.html#ACE" class="external text"><em>A Brief History of Computing: ENIAC and EDVAC</em></a><span class="reference-accessdate">, retrieved 27 January 2010</span></span><span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.au=Copeland%2C+Jack&amp;rft.aufirst=Jack&amp;rft.aulast=Copeland&amp;rft.btitle=A+Brief+History+of+Computing%3A+ENIAC+and+EDVAC&amp;rft.date=2000&amp;rft.genre=book&amp;rft_id=http%3A%2F%2Fwww.alanturing.net%2Fturing_archive%2Fpages%2FReference%2520Articles%2FBriefHistofComp.html%23ACE&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span> which cites <span id="CITEREFRandell1972" class="citation"><a href="https://en.wikipedia.org/wiki/Brian_Randell" title="Brian Randell">Randell, B.</a> (1972), “On Alan Turing and the Origins of Digital Computers”, in Meltzer, B.; Michie, D., <em>Machine Intelligence 7</em> (Edinburgh: Edinburgh University Press): 10, <a href="https://en.wikipedia.org/wiki/International_Standard_Book_Number" title="International Standard Book Number">ISBN</a> <a href="https://en.wikipedia.org/wiki/Special:BookSources/0-902383-26-4" title="Special:BookSources/0-902383-26-4">0-902383-26-4</a></span><span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.atitle=On+Alan+Turing+and+the+Origins+of+Digital+Computers&amp;rft.aufirst=B.&amp;rft.aulast=Randell&amp;rft.au=Randell%2C+B.&amp;rft.date=1972&amp;rft.genre=article&amp;rft.isbn=0-902383-26-4&amp;rft.jtitle=Machine+Intelligence+7&amp;rft.pages=10&amp;rft.place=Edinburgh&amp;rft.pub=Edinburgh+University+Press&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Ajournal">
    <span>
  </span>
  </span></span>

13. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-13">^</a>
  </strong>
  </span>

   

  <span class="reference-text"><a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#CITEREFCopeland2006">Copeland 2006</a>, pp. 108–111</span>

14. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-14">^</a>
  </strong>
  </span>

   

  <span class="reference-text"><a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#CITEREFBowden1953">Bowden 1953</a>, pp. 176,177</span>

15. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-15">^</a>
  </strong>
  </span>

   

  <span class="reference-text"><a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#CITEREFBowden1953">Bowden 1953</a>, p. 135</span>

16. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-16">^</a>
  </strong>
  </span>

   

  <span class="reference-text">
    <span class="citation web"><a href="http://www.ias.edu/people/vonneumann/ecp/" class="external text">“Electronic Computer Project”</a>. <a href="https://en.wikipedia.org/wiki/Institute_for_Advanced_Study" title="Institute for Advanced Study">Institute for Advanced Study</a><span class="reference-accessdate">. Retrieved May 26, 2011</span>.</span>
    <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.btitle=Electronic+Computer+Project&amp;rft.genre=book&amp;rft_id=http%3A%2F%2Fwww.ias.edu%2Fpeople%2Fvonneumann%2Fecp%2F&amp;rft.pub=Institute+for+Advanced+Study&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>
  </span>

17. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-17">^</a>
  </strong>
  </span>

   

  <span class="reference-text">
    <span id="CITEREFJames_E._Robertson1955" class="citation">James E. Robertson (1955), <em>Illiac Design Techniques</em>, report number UIUCDCS-R-1955-146, Digital Computer Laboratory, University of Illinois at Urbana-Champaign</span>
    <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.au=James+E.+Robertson&amp;rft.aulast=James+E.+Robertson&amp;rft.btitle=Illiac+Design+Techniques&amp;rft.date=1955&amp;rft.genre=book&amp;rft.pub=report+number+UIUCDCS-R-1955-146%2C+Digital+Computer+Laboratory%2C+University+of+Illinois+at+Urbana-Champaign&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>
  </span>

18. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-18">^</a>
  </strong>
  </span>

   

  <span class="reference-text"><span class="citation web">F.E. Hamilton, R.R. Seeber, R.A. Rowley, and E.S. Hughes (January 19, 1949). <a href="http://patft1.uspto.gov/netacgi/nph-Parser?Sect1=PTO1&amp;Sect2=HITOFF&amp;d=PALL&amp;p=1&amp;u=%2Fnetahtml%2FPTO%2Fsrchnum.htm&amp;r=1&amp;f=G&amp;l=50&amp;s1=2636672.PN.&amp;OS=PN/2636672&amp;RS=PN/2636672" class="external text">“Selective Sequence Electronic Calculator”</a>. <em>US Patent 2,636,672</em><span class="reference-accessdate">. Retrieved April 28, 2011</span>.</span><span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.atitle=Selective+Sequence+Electronic+Calculator&amp;rft.au=F.E.+Hamilton%2C+R.R.+Seeber%2C+R.A.+Rowley%2C+and+E.S.+Hughes&amp;rft.aulast=F.E.+Hamilton%2C+R.R.+Seeber%2C+R.A.+Rowley%2C+and+E.S.+Hughes&amp;rft.date=January+19%2C+1949&amp;rft.genre=article&amp;rft_id=http%3A%2F%2Fpatft1.uspto.gov%2Fnetacgi%2Fnph-Parser%3FSect1%3DPTO1%26Sect2%3DHITOFF%26d%3DPALL%26p%3D1%26u%3D%252Fnetahtml%252FPTO%252Fsrchnum.htm%26r%3D1%26f%3DG%26l%3D50%26s1%3D2636672.PN.%26OS%3DPN%2F2636672%26RS%3DPN%2F2636672&amp;rft.jtitle=US+Patent+2%2C636%2C672&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Ajournal">
    <span>
  </span>
  </span> Issued April 28, 1953.</span>

19. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-herb_19-0">^</a>
  </strong>
  </span>

   

  <span class="reference-text">
    <span id="CITEREFHerbert_R.J._Grosch1991" class="citation"><a href="https://en.wikipedia.org/wiki/Herb_Grosch" title="Herb Grosch">Herbert R.J. Grosch</a> (1991), <a href="http://www.columbia.edu/acis/history/computer.html" class="external text"><em>Computer: Bit Slices From a Life</em></a>, Third Millennium Books, <a href="https://en.wikipedia.org/wiki/International_Standard_Book_Number" title="International Standard Book Number">ISBN</a> <a href="https://en.wikipedia.org/wiki/Special:BookSources/0-88733-085-1" title="Special:BookSources/0-88733-085-1">0-88733-085-1</a></span>
    <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.au=Herbert+R.J.+Grosch&amp;rft.aulast=Herbert+R.J.+Grosch&amp;rft.btitle=Computer%3A+Bit+Slices+From+a+Life&amp;rft.date=1991&amp;rft.genre=book&amp;rft_id=http%3A%2F%2Fwww.columbia.edu%2Facis%2Fhistory%2Fcomputer.html&amp;rft.isbn=0-88733-085-1&amp;rft.pub=Third+Millennium+Books&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>
  </span>

20. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-20">^</a>
  </strong>
  </span>

   

  <span class="reference-text">
    <span id="CITEREFC._Gordon_BellR._CadyH._McFarlandJ._O.27Laughlin1970" class="citation"><a href="https://en.wikipedia.org/wiki/Gordon_Bell" title="Gordon Bell">C. Gordon Bell</a>; R. Cady; H. McFarland; J. O’Laughlin; R. Noonan; W. Wulf (1970), <a href="http://research.microsoft.com/en-us/um/people/gbell/CGB%20Files/New%20Architecture%20PDP11%20SJCC%201970%20c.pdf" class="external text">“A New Architecture for Mini-Computers—The DEC PDP-11”</a>, <em>Spring Joint Computer Conference</em>: 657–675.</span>
    <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.atitle=A+New+Architecture+for+Mini-Computers%E2%80%94The+DEC+PDP-11&amp;rft.au=C.+Gordon+Bell&amp;rft.au=H.+McFarland&amp;rft.au=J.+O%27Laughlin&amp;rft.aulast=C.+Gordon+Bell&amp;rft.au=R.+Cady&amp;rft.au=R.+Noonan&amp;rft.au=W.+Wulf&amp;rft.date=1970&amp;rft.genre=article&amp;rft_id=http%3A%2F%2Fresearch.microsoft.com%2Fen-us%2Fum%2Fpeople%2Fgbell%2FCGB%2520Files%2FNew%2520Architecture%2520PDP11%2520SJCC%25201970%2520c.pdf&amp;rft.jtitle=Spring+Joint+Computer+Conference&amp;rft.pages=657-675&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Ajournal">
    <span>
  </span>
  </span>
  </span>

21. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-21">^</a>
  </strong>
  </span>

   

  <span class="reference-text">
    <span id="CITEREFLinda_NullJulia_Lobur2010" class="citation">Linda Null; Julia Lobur (2010), <a href="http://books.google.com/books?id=f83XxoBC_8MC&amp;pg=PA36" class="external text"><em>The essentials of computer organization and architecture</em></a> (3rd ed.), Jones &amp; Bartlett Learning, pp. 36,199–203, <a href="https://en.wikipedia.org/wiki/International_Standard_Book_Number" title="International Standard Book Number">ISBN</a> <a href="https://en.wikipedia.org/wiki/Special:BookSources/978-1-4496-0006-8" title="Special:BookSources/978-1-4496-0006-8">978-1-4496-0006-8</a></span>
    <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.au=Julia+Lobur&amp;rft.aulast=Linda+Null&amp;rft.au=Linda+Null&amp;rft.btitle=The+essentials+of+computer+organization+and+architecture&amp;rft.date=2010&amp;rft.edition=3rd&amp;rft.genre=book&amp;rft_id=http%3A%2F%2Fbooks.google.com%2Fbooks%3Fid%3Df83XxoBC_8MC%26pg%3DPA36&amp;rft.isbn=978-1-4496-0006-8&amp;rft.pages=36%2C199-203&amp;rft.pub=Jones+%26+Bartlett+Learning&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>
  </span>

22. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-backus_22-0">^</a>
  </strong>
  </span>

   

  <span class="reference-text"><span class="citation journal"><a href="https://en.wikipedia.org/wiki/John_Backus" title="John Backus">Backus, John W.</a>. <em>Can Programming Be Liberated from the von Neumann Style? A Functional Style and Its Algebra of Programs</em>. <a href="https://en.wikipedia.org/wiki/Digital_object_identifier" title="Digital object identifier">doi</a>:<a href="http://dx.doi.org/10.1145%2F359576.359579" class="external text">10.1145/359576.359579</a>.</span><span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.au=Backus%2C+John+W.&amp;rft.aufirst=John+W.&amp;rft.aulast=Backus&amp;rft.btitle=Can+Programming+Be+Liberated+from+the+von+Neumann+Style%3F+A+Functional+Style+and+Its+Algebra+of+Programs&amp;rft.genre=book&amp;rft_id=info%3Adoi%2F10.1145%2F359576.359579&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span> <span class="error citation-comment"><code>|accessdate=</code> requires <code>|url=</code> (<a href="https://en.wikipedia.org/wiki/Help:CS1_errors#accessdate_missing_url" title="Help:CS1 errors">help</a>)</span></span>

23. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-23">^</a>
  </strong>
  </span>

   

  <span class="reference-text">
    <span class="citation web"><a href="https://en.wikipedia.org/wiki/Edsger_W._Dijkstra" title="Edsger W. Dijkstra">Dijkstra, Edsger W.</a>. <a href="http://www.cs.utexas.edu/~EWD/transcriptions/EWD06xx/EWD692.html" class="external text">“E. W. Dijkstra Archive: A review of the 1977 Turing Award Lecture”</a><span class="reference-accessdate">. Retrieved 2008-07-11</span>.</span>
    <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.au=Dijkstra%2C+Edsger+W.&amp;rft.aufirst=Edsger+W.&amp;rft.aulast=Dijkstra&amp;rft.btitle=E.+W.+Dijkstra+Archive%3A+A+review+of+the+1977+Turing+Award+Lecture&amp;rft.genre=book&amp;rft_id=http%3A%2F%2Fwww.cs.utexas.edu%2F~EWD%2Ftranscriptions%2FEWD06xx%2FEWD692.html&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>
  </span>

24. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-24">^</a>
  </strong>
  </span>

   

  <span class="reference-text">Richard L. Sites, Yale Patt. <a href="http://cva.stanford.edu/classes/cs99s/papers/architects_look_to_future.pdf" class="external text">“Architects Look to Processors of Future”</a>. Microprocessor report. 1996.</span>

25. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-25">^</a>
  </strong>
  </span>

   

  <span class="reference-text">
    <span class="citation web"><a href="http://www.national.com/appinfo/mcu/files/Basic_user1.pdf" class="external text">“COP8 Basic Family User’s Manual”</a>. <a href="https://en.wikipedia.org/wiki/National_Semiconductor" title="National Semiconductor">National Semiconductor</a><span class="reference-accessdate">. Retrieved 2012-01-20</span>.</span>
    <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.btitle=COP8+Basic+Family+User%E2%80%99s+Manual&amp;rft.genre=book&amp;rft_id=http%3A%2F%2Fwww.national.com%2Fappinfo%2Fmcu%2Ffiles%2FBasic_user1.pdf&amp;rft.pub=National+Semiconductor&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>
  </span>

26. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Von_Neumann_architecture#cite_ref-26">^</a>
  </strong>
  </span>

   

  <span class="reference-text"><span class="citation web"><a href="http://www.national.com/appinfo/mcu/files/Feature_user.pdf" class="external text">“COP888 Feature Family User’s Manual”</a>. National Semiconductor<span class="reference-accessdate">. Retrieved 2012-01-20</span>.</span>&lt;span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.btitle=COP888+Feature+Family+User%E2%80%99s+Manual&amp;rft.genre=book&amp;rft_id=http%3A%2F%2Fwww.national.com%2Fappinfo%2Fmcu%2Ffiles%2FFeature_user.pdf&amp;rft.pub=National+Semiconductor&amp;rft_val_fmt=info%</span>
3Aofi%2Ffmt%3Akev%3Amtx%3Abook">

<span>
</span>

# <span id="General" class="mw-headline">General</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Von_Neumann_architecture&amp;action=edit&amp;section=11" title="Edit section: General">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

- <span id="CITEREFBowden1953" class="citation">Bowden, B.V., ed. (1953), <em>Faster Than Thought: A Symposium on Digital Computing Machines</em>, London: Sir Isaac Pitman and Sons Ltd.</span>

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.btitle=Faster+Than+Thought%3A+A+Symposium+on+Digital+Computing+Machines&amp;rft.date=1953&amp;rft.genre=book&amp;rft.place=London&amp;rft.pub=Sir+Isaac+Pitman+and+Sons+Ltd.&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

- <span id="CITEREFRojasHashagen2000" class="citation">Rojas, Raúl; Hashagen, Ulf, eds. (2000), <em>The First Computers: History and Architectures</em>, MIT Press, <a href="https://en.wikipedia.org/wiki/International_Standard_Book_Number" title="International Standard Book Number">ISBN</a> <a href="https://en.wikipedia.org/wiki/Special:BookSources/0-262-18197-5" title="Special:BookSources/0-262-18197-5">0-262-18197-5</a></span>

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.btitle=The+First+Computers%3A+History+and+Architectures&amp;rft.date=2000&amp;rft.genre=book&amp;rft.isbn=0-262-18197-5&amp;rft.pub=MIT+Press&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

- <span id="CITEREFDavis2000" class="citation"><a href="https://en.wikipedia.org/wiki/Martin_Davis" title="Martin Davis">Davis, Martin</a> (2000), <em>The universal computer: the road from Leibniz to Turing</em>, New York: W W Norton &amp; Company Inc., <a href="https://en.wikipedia.org/wiki/International_Standard_Book_Number" title="International Standard Book Number">ISBN</a> <a href="https://en.wikipedia.org/wiki/Special:BookSources/0-393-04785-7" title="Special:BookSources/0-393-04785-7">0-393-04785-7</a></span>

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.au=Davis%2C+Martin&amp;rft.aufirst=Martin&amp;rft.aulast=Davis&amp;rft.btitle=The+universal+computer%3A+the+road+from+Leibniz+to+Turing&amp;rft.date=2000&amp;rft.genre=book&amp;rft.isbn=0-393-04785-7&amp;rft.place=New+York&amp;rft.pub=W+W+Norton+%26+Company+Inc.&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

   re-published as: 

  <span id="CITEREFDavis2001" class="citation"><a href="https://en.wikipedia.org/wiki/Martin_Davis" title="Martin Davis">Davis, Martin</a> (2001), <em>Engines of Logic: Mathematicians and the Origin of the Computer</em>, New York: W. W. Norton &amp; Company, <a href="https://en.wikipedia.org/wiki/International_Standard_Book_Number" title="International Standard Book Number">ISBN</a> <a href="https://en.wikipedia.org/wiki/Special:BookSources/978-0-393-32229-3" title="Special:BookSources/978-0-393-32229-3">978-0-393-32229-3</a></span>

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.au=Davis%2C+Martin&amp;rft.aufirst=Martin&amp;rft.aulast=Davis&amp;rft.btitle=Engines+of+Logic%3A+Mathematicians+and+the+Origin+of+the+Computer&amp;rft.date=2001&amp;rft.genre=book&amp;rft.isbn=978-0-393-32229-3&amp;rft.place=New+York&amp;rft.pub=W.+W.+Norton+%26+Company&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

- _Can Programming be Liberated from the von Neumann Style?_, John Backus, 1977 ACM Turing Award Lecture. Communications of the ACM, August 1978, Volume 21, Number 8 [Online PDF](http://www.stanford.edu/class/cs242/readings/backus.pdf)
- C. Gordon Bell and Allen Newell (1971), _Computer Structures: Readings and Examples_, McGraw-Hill Book Company, New York. Massive (668 pages)
- <span id="CITEREFCopeland2006" class="citation">Copeland, Jack (2006), “Colossus and the Rise of the Modern Computer”, in Copeland, B. Jack, <em>Colossus: The Secrets of Bletchley Park’s Codebreaking Computers</em>, Oxford: Oxford University Press, <a href="https://en.wikipedia.org/wiki/International_Standard_Book_Number" title="International Standard Book Number">ISBN</a> <a href="https://en.wikipedia.org/wiki/Special:BookSources/978-0-19-284055-4" title="Special:BookSources/978-0-19-284055-4">978-0-19-284055-4</a></span>

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.atitle=Colossus%3A+The+Secrets+of+Bletchley+Park%27s+Codebreaking+Computers&amp;rft.au=Copeland%2C+Jack&amp;rft.aufirst=Jack&amp;rft.aulast=Copeland&amp;rft.btitle=Colossus+and+the+Rise+of+the+Modern+Computer&amp;rft.date=2006&amp;rft.genre=bookitem&amp;rft.isbn=978-0-19-284055-4&amp;rft.place=Oxford&amp;rft.pub=Oxford+University+Press&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

- <span id="CITEREFGanesan2009" class="citation">Ganesan, Deepak (2009), <a href="http://none.cs.umass.edu/~dganesan/courses/fall09/handouts/Chapter4.pdf" class="external text">
    <em>The Von Neumann Model</em>
  </a><span class="reference-accessdate">, retrieved October 22, 2011</span></span>

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.aufirst=Deepak&amp;rft.au=Ganesan%2C+Deepak&amp;rft.aulast=Ganesan&amp;rft.btitle=The+Von+Neumann+Model&amp;rft.date=2009&amp;rft.genre=book&amp;rft_id=http%3A%2F%2Fnone.cs.umass.edu%2F~dganesan%2Fcourses%2Ffall09%2Fhandouts%2FChapter4.pdf&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

<!--  -->

- <span id="SCOTT" class="citation book">McCartney, Scott (1999). <em>ENIAC: The Triumphs and Tragedies of the World’s First Computer</em>. Walker &amp; Co. <a href="https://en.wikipedia.org/wiki/International_Standard_Book_Number" title="International Standard Book Number">ISBN</a> <a href="https://en.wikipedia.org/wiki/Special:BookSources/0-8027-1348-3" title="Special:BookSources/0-8027-1348-3">0-8027-1348-3</a>.</span>

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.aufirst=Scott&amp;rft.aulast=McCartney&amp;rft.au=McCartney%2C+Scott&amp;rft.btitle=ENIAC%3A+The+Triumphs+and+Tragedies+of+the+World%27s+First+Computer&amp;rft.date=1999&amp;rft.genre=book&amp;rft.isbn=0-8027-1348-3&amp;rft.pub=Walker+%26+Co&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

- <span id="HHG" class="citation book">Goldstine, Herman H. (1972). <em>The Computer from Pascal to von Neumann</em>. Princeton University press. <a href="https://en.wikipedia.org/wiki/International_Standard_Book_Number" title="International Standard Book Number">ISBN</a> <a href="https://en.wikipedia.org/wiki/Special:BookSources/0-691-08104-2" title="Special:BookSources/0-691-08104-2">0-691-08104-2</a>.</span>

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.aufirst=Herman+H.&amp;rft.au=Goldstine%2C+Herman+H.&amp;rft.aulast=Goldstine&amp;rft.btitle=The+Computer+from+Pascal+to+von+Neumann&amp;rft.date=1972&amp;rft.genre=book&amp;rft.isbn=0-691-08104-2&amp;rft.pub=Princeton+University+press&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

- <span id="JOEL" class="citation book">Shurkin, Joel (1984). <em>Engines of the Mind - a history of the computer</em>. New York, London: W.W. Norton &amp; Company. <a href="https://en.wikipedia.org/wiki/International_Standard_Book_Number" title="International Standard Book Number">ISBN</a> <a href="https://en.wikipedia.org/wiki/Special:BookSources/0-393-01804-0" title="Special:BookSources/0-393-01804-0">0-393-01804-0</a>.</span>

  <span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3AVon+Neumann+architecture&amp;rft.aufirst=Joel&amp;rft.aulast=Shurkin&amp;rft.au=Shurkin%2C+Joel&amp;rft.btitle=Engines+of+the+Mind+-+a+history+of+the+computer&amp;rft.date=1984&amp;rft.genre=book&amp;rft.isbn=0-393-01804-0&amp;rft.place=New+York%2C+London&amp;rft.pub=W.W.+Norton+%26+Company&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span>

## <span id="External_links" class="mw-headline">External links</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Von_Neumann_architecture&amp;action=edit&amp;section=12" title="Edit section: External links">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

- [Harvard vs von Neumann](http://infocenter.arm.com/help/index.jsp?topic=/com.arm.doc.faqs/ka11516.html)
- [A tool that emulates the behavior of a von Neumann machine](http://home.gna.org/vov/)
- [JOHNNY – A simple Open Source simulator of a von Neumann machine for educational purposes](http://sourceforge.net/projects/johnnysimulator/)

<span id="interwiki-uk-fa">
</span>

![](https://en.wikipedia.org/wiki/Special:CentralAutoLogin/start?type=1x1)

Retrieved from "<http://en.wikipedia.org/w/index.php?title=Von_Neumann_architecture&oldid=605819272>"

[Categories](https://en.wikipedia.org/wiki/Help:Category "Help:Category"):

- [Computer architecture](https://en.wikipedia.org/wiki/Category:Computer_architecture "Category:Computer architecture")
- [Flynn's taxonomy](https://en.wikipedia.org/wiki/Category:Flynn%27s_taxonomy "Category:Flynn's taxonomy")
- [Reference models](https://en.wikipedia.org/wiki/Category:Reference_models "Category:Reference models")
- [Classes of computers](https://en.wikipedia.org/wiki/Category:Classes_of_computers "Category:Classes of computers")
- [School of Computer Science, University of Manchester](https://en.wikipedia.org/wiki/Category:School_of_Computer_Science,_University_of_Manchester "Category:School of Computer Science, University of Manchester")
- [Open problems](https://en.wikipedia.org/wiki/Category:Open_problems "Category:Open problems")
- [1945 introductions](https://en.wikipedia.org/wiki/Category:1945_introductions "Category:1945 introductions")
