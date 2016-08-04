# CARDboard Illustrative Aid to Computation

[Original URL](https://en.wikipedia.org/wiki/CARDboard_Illustrative_Aid_to_Computation)

> CARDIAC (CARDboard Illustrative Aid to Computation) was a learning aid developed by David Hagelbarger and Saul Fingerman for Bell Telephone Laboratories in 1968 to teach high school students how...

**CARDIAC (CARDboard Illustrative Aid to Computation)** was a learning aid developed by [David Hagelbarger](https://en.wikipedia.org/w/index.php?title=David_Hagelbarger&action=edit&redlink=1 "David Hagelbarger (page does not exist)") and [Saul Fingerman](https://en.wikipedia.org/w/index.php?title=Saul_Fingerman&action=edit&redlink=1 "Saul Fingerman (page does not exist)") for [Bell Telephone Laboratories](https://en.wikipedia.org/wiki/Bell_Labs "Bell Labs") in 1968 to teach high school students how [computers](https://en.wikipedia.org/wiki/Computers "Computers") work. The kit consisted of an instruction manual and a die-cut cardboard "computer".

The computer "operated" by means of pencil and sliding cards. Any arithmetic was done in the head of the person operating the computer. The computer operated in base 10 and had 100 memory cells which could hold signed numbers from 0 to ±999\. It had an instruction set of 10 instructions which allowed CARDIAC to add, subtract, test, shift, input, output and jump.

## Contents

- [<span class="tocnumber">1</span>

   

  <span class="toctext">Hardware</span>](https://en.wikipedia.org/wiki/CARDboard_Illustrative_Aid_to_Computation#Hardware)

- [<span class="tocnumber">2</span>

   

  <span class="toctext">Programming</span>](https://en.wikipedia.org/wiki/CARDboard_Illustrative_Aid_to_Computation#Programming)

- [<span class="tocnumber">3</span>

   

  <span class="toctext">Instruction Set</span>](https://en.wikipedia.org/wiki/CARDboard_Illustrative_Aid_to_Computation#Instruction_Set)

- [<span class="tocnumber">4</span>

   

  <span class="toctext">Operation</span>](https://en.wikipedia.org/wiki/CARDboard_Illustrative_Aid_to_Computation#Operation)

- [<span class="tocnumber">5</span>

   

  <span class="toctext">User Group</span>](https://en.wikipedia.org/wiki/CARDboard_Illustrative_Aid_to_Computation#User_Group)

- [<span class="tocnumber">6</span>

   

  <span class="toctext">See also</span>](https://en.wikipedia.org/wiki/CARDboard_Illustrative_Aid_to_Computation#See_also)

- [<span class="tocnumber">7</span>

   

  <span class="toctext">External links</span>](https://en.wikipedia.org/wiki/CARDboard_Illustrative_Aid_to_Computation#External_links)

## <span id="Hardware" class="mw-headline">Hardware</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=CARDboard_Illustrative_Aid_to_Computation&amp;action=edit&amp;section=1" title="Edit section: Hardware">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

[![](https://upload.wikimedia.org/wikipedia/en/thumb/7/7a/CARDIAC_learning_aid_%28front%29.jpg/275px-CARDIAC_learning_aid_%28front%29.jpg)](https://en.wikipedia.org/wiki/File:CARDIAC_learning_aid_(front).jpg) [](https://en.wikipedia.org/wiki/File:CARDIAC_learning_aid_(front).jpg "Enlarge")

Front of CARDIAC device

The "[CPU](https://en.wikipedia.org/wiki/Central_processing_unit "Central processing unit")" of the computer consisted of 4 slides that moved various numbers and arrows to have the flow of the real CPU (the user's [brain](https://en.wikipedia.org/wiki/Brain "Brain")) move the right way. They had one flag (+/-), affected by the result in the accumulator.

[Memory](https://en.wikipedia.org/wiki/Computer_memory "Computer memory") consisted of the other half of the cardboard cutout. There were 100 cells. Cell 0 was "[ROM](https://en.wikipedia.org/wiki/Read-only_memory "Read-only memory")", always containing a numeric "1"; cells 1 to 98 were "[RAM](https://en.wikipedia.org/wiki/RAM "RAM")"; available for instructions and data; and cell 99 could best be described as "[EEPROM](https://en.wikipedia.org/wiki/EEPROM "EEPROM")".

Memory cells held signed [decimal](https://en.wikipedia.org/wiki/Decimal "Decimal") numbers from 0 to ±999 and were written with a pencil. Cells were erased with an eraser. A "bug" was provided to act as a program counter, and was placed in a hole beside the current memory cell.

## <span id="Programming" class="mw-headline">Programming</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=CARDboard_Illustrative_Aid_to_Computation&amp;action=edit&amp;section=2" title="Edit section: Programming">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

CARDIAC had a 10 instruction [machine language](https://en.wikipedia.org/wiki/Machine_language "Machine language"). An instruction was three decimal digits (the sign is ignored) in the form `OAA`. The first digit was the op code (`O`); the second and third digits were an address (`AA`). Addressing was one of accumulator to memory absolute, absolute memory to accumulator, input to absolute memory and absolute memory to output.

High level languages were never developed for CARDIAC, since they would defeat one of the purposes of the device, to introduce concepts of assembly language programming.

Programs were hand assembled, then written, by pencil into the appropriate memory cells.

## <span id="Instruction_Set" class="mw-headline">Instruction Set</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=CARDboard_Illustrative_Aid_to_Computation&amp;action=edit&amp;section=3" title="Edit section: Instruction Set">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

Opcode | Mnemonic | Instruction               | Description
------ | -------- | ------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
0      | INP      | Input                     | take a number from the input card and put it in a specified memory cell.
1      | CLA      | Clear and add             | clear the accumulator and add the contents of a memory cell to the accumulator.
2      | ADD      | Add                       | add the contents of a memory cell to the accumulator.
3      | TAC      | Test accumulator contents | performs a sign test on the contents of the accumulator; if minus, jump to a specified memory cell.
4      | SFT      | Shift                     | shifts the accumulator x places left, then y places right, where x is the upper address digit and y is the lower.
5      | OUT      | Output                    | take a number from the specified memory cell and write it on the output card.
6      | STO      | Store                     | copy the contents of the accumulator into a specified memory cell.
7      | SUB      | Subtract                  | subtract the contents of a specified memory cell from the accumulator.
8      | JMP      | Jump                      | jump to a specified memory cell. The current cell number is written in cell 99\. This allows for one level of subroutines by having the return be the instruction at cell 99 (which had '8' hardcoded as the first digit.
9      | HRS      | Halt and reset            | move bug to the specified cell, then stop program execution.

## <span id="Operation" class="mw-headline">Operation</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=CARDboard_Illustrative_Aid_to_Computation&amp;action=edit&amp;section=4" title="Edit section: Operation">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

Programs were run by first sliding three slides so that the number in the instruction register equaled the number in the memory cell the bug was sitting in. Once that was done the bug was moved to the next memory cell. The user then followed an arrow which would then tell them what to do next. This would continue for all of program execution.

## <span id="User_Group" class="mw-headline">User Group</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=CARDboard_Illustrative_Aid_to_Computation&amp;action=edit&amp;section=5" title="Edit section: User Group">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

There are user groups for the CARDIAC on [Facebook](https://www.facebook.com/groups/48856186627/), [Google+](https://plus.google.com/u/0/communities/105401067945055411018) and [Yahoo](https://groups.yahoo.com/neo/groups/cravcomp/conversations/topics/56)

## <span id="See_also" class="mw-headline">See also</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=CARDboard_Illustrative_Aid_to_Computation&amp;action=edit&amp;section=6" title="Edit section: See also">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

- [Little man computer](https://en.wikipedia.org/wiki/Little_man_computer "Little man computer") (another instructional model)

## <span id="External_links" class="mw-headline">External links</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=CARDboard_Illustrative_Aid_to_Computation&amp;action=edit&amp;section=7" title="Edit section: External links">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

- <http://cs.drexel.edu/~bls96/museum/cardiac.html> - a discussion of the CARDIAC with examples and a simulator in [Javascript](https://en.wikipedia.org/wiki/Javascript "Javascript")
- <http://www.sourceforge.net/projects/cinc/> - a simulator for the [Java platform](https://en.wikipedia.org/wiki/Java_platform "Java platform"), with both command-line and GUI interfaces, is a [Sourceforge](https://en.wikipedia.org/wiki/Sourceforge "Sourceforge") project called jcinc.
- <http://www.kaleberg.com/software/cardiac/> - a simulator hosted on Macintosh, Windows (untested), and Linux (untested). Simulator provides editor, simple assembler, and examples. As described on the page, opcodes 0 and 9 are interchanged. Simulator source code appears to be proprietary, free downloads.
- <http://www.youtube.com/watch?v=CW96m7R0u-s>
- <http://www.drdobbs.com/embedded-systems/cpu-design-on-paper/240153480> - Al Williams published a spreadsheet version of CARDIAC on the Dr. Dobb's site
- <http://www.drdobbs.com/embedded-systems/paper-to-fpga/240155922> - Al Williams' FPGA implementation of CARDIAC on an FPGA board (final article of a series on Dr. Dobb's web site)

![](https://en.wikipedia.org/wiki/Special:CentralAutoLogin/start?type=1x1)

Retrieved from "<https://en.wikipedia.org/w/index.php?title=CARDboard_Illustrative_Aid_to_Computation&oldid=689352534>"

[Categories](https://en.wikipedia.org/wiki/Help:Category "Help:Category"):

- [Models of computation](https://en.wikipedia.org/wiki/Category:Models_of_computation "Category:Models of computation")
