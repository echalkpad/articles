# Little man computer

[Original URL](https://en.wikipedia.org/wiki/Little_man_computer)

> The Little Man Computer (LMC) is an instructional model of a computer, created by Dr. Stuart Madnick in 1965.[1] The LMC is generally used to teach students, because it models a simple von Neumann...

The **Little Man Computer** (**LMC**) is an instructional [model](https://en.wikipedia.org/wiki/Model_(abstract) "Model (abstract)") of a [computer](https://en.wikipedia.org/wiki/Computer "Computer"), created by Dr. [Stuart Madnick](https://en.wikipedia.org/wiki/Stuart_Madnick "Stuart Madnick") in 1965.

<sup>
  <a href="https://en.wikipedia.org/wiki/Little_man_computer#cite_note-1"><span>[</span>1<span>]</span></a>
</sup>

 The LMC is generally used to teach students, because it models a simple [von Neumann architecture](https://en.wikipedia.org/wiki/Von_Neumann_architecture "Von Neumann architecture") computer - which has all of the basic features of a modern computer. It can be programmed in machine code (albeit in decimal rather than binary) or assembly code.

<sup>
  <a href="https://en.wikipedia.org/wiki/Little_man_computer#cite_note-2"><span>[</span>2<span>]</span></a>
  <a href="https://en.wikipedia.org/wiki/Little_man_computer#cite_note-3"><span>[</span>3<span>]</span></a>
  <a href="https://en.wikipedia.org/wiki/Little_man_computer#cite_note-4"><span>[</span>4<span>]</span></a>
</sup>

## Contents

- [<span class="tocnumber">1</span>

   

  <span class="toctext">System Architecture</span>](https://en.wikipedia.org/wiki/Little_man_computer#System_Architecture)

- [<span class="tocnumber">2</span>

   

  <span class="toctext">Execution cycle</span>](https://en.wikipedia.org/wiki/Little_man_computer#Execution_cycle)

- [<span class="tocnumber">3</span>

   

  <span class="toctext">Commands</span>](https://en.wikipedia.org/wiki/Little_man_computer#Commands)

  - [<span class="tocnumber">3.1</span>

     

    <span class="toctext">Instructions</span>](https://en.wikipedia.org/wiki/Little_man_computer#Instructions)

  - [<span class="tocnumber">3.2</span>

     

    <span class="toctext">Examples</span>](https://en.wikipedia.org/wiki/Little_man_computer#Examples)

    - [<span class="tocnumber">3.2.1</span>

       

      <span class="toctext">Using Numeric Instruction Codes</span>](https://en.wikipedia.org/wiki/Little_man_computer#Using_Numeric_Instruction_Codes)

    - [<span class="tocnumber">3.2.2</span>

       

      <span class="toctext">Using Mnemonics and Labels</span>](https://en.wikipedia.org/wiki/Little_man_computer#Using_Mnemonics_and_Labels)

- [<span class="tocnumber">4</span>

   

  <span class="toctext">Labels</span>](https://en.wikipedia.org/wiki/Little_man_computer#Labels)

  - [<span class="tocnumber">4.1</span>

     

    <span class="toctext">Example</span>](https://en.wikipedia.org/wiki/Little_man_computer#Example)

- [<span class="tocnumber">5</span>

   

  <span class="toctext">See also</span>](https://en.wikipedia.org/wiki/Little_man_computer#See_also)

- [<span class="tocnumber">6</span>

   

  <span class="toctext">References</span>](https://en.wikipedia.org/wiki/Little_man_computer#References)

- [<span class="tocnumber">7</span>

   

  <span class="toctext">External links</span>](https://en.wikipedia.org/wiki/Little_man_computer#External_links)

  - [<span class="tocnumber">7.1</span>

     

    <span class="toctext">Simulators</span>](https://en.wikipedia.org/wiki/Little_man_computer#Simulators)

## <span id="System_Architecture" class="mw-headline">System Architecture</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Little_man_computer&amp;action=edit&amp;section=1" title="Edit section: System Architecture">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

The LMC model is based on the concept of a little man shut in a closed mail room (analogous to a computer in this scenario). At one end of the room, there are 100 mailboxes ([memory](https://en.wikipedia.org/wiki/Memory_(computers) "Memory (computers)")), numbered 0 to 99, that can each contain a 3 digit instruction or data (ranging from 000 to 999). Furthermore, there are two mailboxes at the other end labeled **INBOX** and **OUTBOX** which are used for receiving and outputting data. In the center of the room, there is a work area containing a simple two function (addition and subtraction) calculator known as the [Accumulator](https://en.wikipedia.org/wiki/Accumulator_(computing) "Accumulator (computing)") and a resettable counter known as the Program Counter. The Program Counter holds the address of the next instruction the Little Man will carry out. This Program Counter is normally incremented by 1 after each instruction is executed, allowing the Little Man to work through a program sequentially. [Branch](https://en.wikipedia.org/wiki/Branch_(computer_science) "Branch (computer science)") instructions allow iteration (loops) and [conditional](https://en.wikipedia.org/wiki/Conditional_(programming) "Conditional (programming)") programming structures to be incorporated into a program. The latter is achieved by setting the Program Counter to a non-sequential memory address if a particular condition is met (typically the value stored in the accumulator being zero or positive).

As specified by the [von Neumann architecture](https://en.wikipedia.org/wiki/Von_Neumann_architecture "Von Neumann architecture"), each mailbox (signifying a unique memory location) contains both instructions and data. Care therefore needs to be taken to stop the Program Counter from reaching a memory address containing data - or the Little Man will attempt to treat it as an instruction. One can take advantage of this by writing instructions into mailboxes that are meant to be interpreted as code, to create self-modifying code. To use the LMC, the user loads data into the mailboxes and then signals the Little Man to begin execution, starting with the instruction stored at memory address zero. Resetting the Program Counter to zero effectively restarts the program, albeit in a potentially different state.

## <span id="Execution_cycle" class="mw-headline">Execution cycle</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Little_man_computer&amp;action=edit&amp;section=2" title="Edit section: Execution cycle">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

See also: [Instruction cycle](https://en.wikipedia.org/wiki/Instruction_cycle "Instruction cycle")

To execute a program, the little man performs these steps:

1. Check the Program Counter for the mailbox number that contains a program instruction (i.e. zero at the start of the program)
2. Fetch the instruction from the mailbox with that number. Each instruction contains two fields: An opcode (indicating the operation to perform) and the address field (indicating where to find the data to perform the operation on).
3. Increment the Program Counter (so that it contains the mailbox number of the next instruction)
4. Decode the instruction. If the instruction utilizes data stored in another mailbox, this includes using the address field to find the mailbox number for the data it will work on, e.g. "get data from mailbox 42")
5. Fetch the data (from the input, accumulator, or mailbox with the address determined in step 4)
6. Execute the instruction based on the opcode given
7. Branch or store the result (in the output, accumulator, or mailbox with the address determined in step 4)
8. Return to the Program Counter to repeat the cycle or halt

## <span id="Commands" class="mw-headline">Commands</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Little_man_computer&amp;action=edit&amp;section=3" title="Edit section: Commands">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

While the LMC does reflect the actual workings of [binary](https://en.wikipedia.org/wiki/Binary_numeral_system "Binary numeral system") processors, the simplicity of [decimal](https://en.wikipedia.org/wiki/Decimal "Decimal") numbers was chosen to minimize the complexity for students who may not be comfortable working in binary/[hexadecimal](https://en.wikipedia.org/wiki/Hexadecimal "Hexadecimal").

### <span id="Instructions" class="mw-headline">Instructions</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Little_man_computer&amp;action=edit&amp;section=4" title="Edit section: Instructions">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

Some LMC simulators are programmed directly using 3-digit numeric instructions and some use 3-letter mnemonic codes and labels. In either case, the instruction set is deliberately very limited (_typically about ten instructions_) to simplify understanding. If the LMC uses mnemonic codes and labels then these are converted into 3-digit numeric instructions when the program is assembled. The first digit of a numeric instruction

The table below shows a typical numeric instruction set and the equivalent mnemonic codes.

Numeric code | Mnemonic code                                      | Instruction                                                                                                         | Description
------------ | -------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
1xx          | ADD                                                | ADD                                                                                                                 | Add the value stored in mailbox xx to whatever value is currently on the accumulator (calculator). Note: the contents of the mailbox are not changed, and the actions of the accumulator (calculator) are not defined for add instructions that cause sums larger than 3 digits. Similarly to SUBTRACT, one could set the negative flag on overflow.
2xx          | SUB                                                | SUBTRACT                                                                                                            | Subtract the value stored in mailbox xx from whatever value is currently on the accumulator (calculator). Note: the contents of the mailbox are not changed, and the actions of the accumulator are not defined for subtract instructions that cause negative results - however, a negative flag will be set so that **7xx (BRZ)** and **8xx (BRP)** can be used properly.
3xx          | STA                                                | STORE                                                                                                               | Store the contents of the accumulator in mailbox xx (destructive). Note: the contents of the accumulator (calculator) are not changed (non-destructive), but contents of mailbox are replaced regardless of what was in there (destructive)
5xx          | LDA                                                | LOAD                                                                                                                | Load the value from mailbox xx (non-destructive) and enter it in the accumulator (destructive).
6xx          | BRA                                                | [BRANCH](https://en.wikipedia.org/wiki/Branch_(computer_science) "Branch (computer science)") (unconditional)       | Set the program counter to the given address (value xx). That is, value xx will be the next instruction executed.
7xx          | BRZ                                                | BRANCH IF ZERO ([conditional](https://en.wikipedia.org/wiki/Conditional_(programming) "Conditional (programming)")) | If the accumulator (calculator) contains the value 000, set the program counter to the value xx. Otherwise, do nothing. Whether the negative flag is taken into account is undefined. When a SUBTRACT underflows the accumulator, this flag is set, after which the accumulator is undefined, potentially zero, causing behavior of BRZ to be undefined on underflow. Suggested behavior would be to branch if accumulator is zero and negative flag is not set. Note: since the program is stored in memory, data and program instructions all have the same address/location format.
8xx          | BRP                                                | BRANCH IF POSITIVE (conditional)                                                                                    | If the accumulator (calculator) is 0 or positive, set the program counter to the value xx. Otherwise, do nothing. As LMC memory cells can only hold values between 0 and 999, this instruction depends solely on the negative flag set by an underflow on SUBTRACT and potentially on an overflow on ADD (undefined). Note: since the program is stored in memory, data and program instructions all have the same address/location format.
901          | INP                                                | INPUT                                                                                                               | Go to the INBOX, fetch the value from the user, and put it in the accumulator (calculator) Note: this will overwrite whatever value was in the accumulator (destructive)
902          | OUT                                                | OUTPUT                                                                                                              | Copy the value from the accumulator (calculator) to the OUTBOX. Note: the contents of the accumulator are not changed (non-destructive).
000          | [HLT](https://en.wikipedia.org/wiki/HLT "HLT")/COB | HALT/COFFEE BREAK                                                                                                   | Stop working/end the program.
             | DAT                                                | DATA                                                                                                                | This is an [assembler](https://en.wikipedia.org/wiki/Assembly_language "Assembly language") instruction which simply loads the value into the next available mailbox. DAT can also be used in conjunction with labels to declare variables. For example, DAT 984 will store the value 984 into a mailbox at the address of the DAT instruction.

### <span id="Examples" class="mw-headline">Examples</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Little_man_computer&amp;action=edit&amp;section=5" title="Edit section: Examples">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

#### <span id="Using_Numeric_Instruction_Codes" class="mw-headline">Using Numeric Instruction Codes</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Little_man_computer&amp;action=edit&amp;section=6" title="Edit section: Using Numeric Instruction Codes">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

This program (instruction **901** to instruction **000**) is written just using numeric codes. The program takes two numbers as input and outputs the difference. Notice that execution starts at Mailbox 00 and finishes at Mailbox 07\. The disadvantages of programming the LMC using numeric instruction codes are discussed below.

Mailbox | Numeric code | Operation                              | Comments
------- | ------------ | -------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------
00      | **901**      | INBOX –> ACCUMULATOR                   | INPUT the first number, enter into calculator (erasing whatever was there)
01      | **308**      | ACCUMULATOR –> MEMORY[08]              | STORE the calculator's current value (to prepare for the next step...)
02      | **901**      | INBOX –> ACCUMULATOR                   | INPUT the second number, enter into calculator (erasing whatever was there)
03      | **309**      | ACCUMULATOR –> MEMORY[09]              | STORE the calculator's current value (again, to prepare for the next step...)
04      | **508**      | MEMORY[08] –> ACCUMULATOR              | (Now that both INPUT values are STORED in Mailboxes 08 and 09...) 

LOAD the first value back into the calculator (erasing whatever was there)
05      | **209**      | ACCUMULATOR = ACCUMULATOR - MEMORY[09] | SUBTRACT the second number from the calculator's current value (which was just set to the first number)
06      | **902**      | ACCUMULATOR –> OUTBOX                  | OUTPUT the calculator's result to the OUTBOX
07      | **000**      | (no operation performed)               | HALT the LMC

#### <span id="Using_Mnemonics_and_Labels" class="mw-headline">Using Mnemonics and Labels</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Little_man_computer&amp;action=edit&amp;section=7" title="Edit section: Using Mnemonics and Labels">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

[Assembly language](https://en.wikipedia.org/wiki/Assembly_language "Assembly language") is a low-level programming language that uses mnemonics and labels instead of numeric instruction codes. Although the LMC only uses a limited set of mnemonics, the convenience of using a [mnemonic](https://en.wikipedia.org/wiki/Mnemonic "Mnemonic") for each instruction is made apparent from the assembly language of the same program shown below - the programmer is no longer required to memorize a set of anonymous numeric codes and can now program with a set of more memorable mnemonic codes. If the mnemonic is an instruction that involves a memory address (_either a branch instruction or loading/saving data_) then a label is used to name the memory address.

_This example program can be compiled and run on the LMC simulator

<sup>
  <a href="https://en.wikipedia.org/wiki/Little_man_computer#cite_note-5"><span>[</span>5<span>]</span></a>
</sup>

 available on the website of [York University](https://en.wikipedia.org/wiki/York_University "York University") ([Toronto](https://en.wikipedia.org/wiki/Toronto "Toronto"), [Canada](https://en.wikipedia.org/wiki/Canada "Canada")) or on the desktop application written by Mike Coley.

<sup>
  <a href="https://en.wikipedia.org/wiki/Little_man_computer#cite_note-6"><span>[</span>6<span>]</span></a>
</sup>

 All these simulators include full instructions and sample programs, an assembler to convert the assembly code into machine code, control interfaces to execute and monitor programs, and a step-by-step detailed description of each LMC instruction._ INP STA FIRST INP STA SECOND LDA FIRST SUB SECOND OUT HLT FIRST DAT SECOND DAT

## <span id="Labels" class="mw-headline">Labels</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Little_man_computer&amp;action=edit&amp;section=8" title="Edit section: Labels">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

Without labels the programmer is required to manually calculate mailbox (_memory_) addresses. In the **numeric code example**, if a new instruction was to be inserted before the final HLT instruction then that HLT instruction would move from address 07 to address 08 (address labelling starts at address location 00). Suppose the user entered 600 as the first input. The instruction 308 would mean that this value would be stored at address location 08 and overwrite the 000 (HLT) instruction. Since 600 means "branch to mailbox address 00" the program, instead of halting, would get stuck in an endless loop.

To work around this difficulty, most assembly languages (_including the LMC_) combine the mnemonics with [labels](https://en.wikipedia.org/wiki/Label_(programming_language) "Label (programming language)"). A label is simply a word that is used to either name a memory address where an instruction or data is stored, or to refer to that address in an instruction.

When a program is assembled:

- A label to the left of an instruction mnemonic is converted to the memory address the instruction or data is stored at. i.e. **loopstart INP**
- A label to the right of an instruction mnemonic takes on the value of the memory address referred to above. i.e. **BRA loopstart**
- A label combined with a DAT statement works as a variable, it labels the memory address that the data is stored at. i.e. **one DAT 1** or **number1 DAT**

In the **[assembly language](https://en.wikipedia.org/wiki/Assembly_language "Assembly language") example** which uses mnemonics and labels, if a new instruction was inserted before the final HLT instruction then the address location labelled FIRST would now be at memory location 09 rather than 08 and the STA FIRST instruction would be converted to 309 (STA 09) rather than 308 (STA 08) when the program was assembled.

Labels are therefore used to:

- identify a particular instruction as a target for a BRANCH instruction.
- identify a memory location as a named variable (using DAT) and optionally load data into the program at assembly time for use by the program (this use is not obvious until one considers that there is no way of adding 1 to a counter. One could ask the user to input 1 at the beginning, but it would be better to have this loaded at the time of assembly using **one DAT 1**)

### <span id="Example" class="mw-headline">Example</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Little_man_computer&amp;action=edit&amp;section=9" title="Edit section: Example">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

This program will take a user input, and count down to zero.

```
 INP
LOOP SUB ONE // Label this memory address as LOOP, The instruction will then subtract the value stored at address ONE from the accumulator
 OUT
 BRZ QUIT // If the accumulator value is 0, jump to the memory address labeled QUIT
 BRA LOOP // If the accumulator value is not 0, jump to the memory address labeled LOOP
QUIT HLT // Label this memory address as QUIT
ONE DAT 1 // Store the value 1 in this memory address, and label it ONE (variable declaration)
```

This program will take a user input, square it, output the answer and then repeat. Entering a zero will end the program.<br>
(_Note: an input that results in an output greater than 999 will cause an error due to the LMC 3 digit number limit_).

```
START LDA ZERO // Initialize for multiple program run
 STA RESULT
 STA COUNT
 INP // User provided input
 BRZ END // Branch to program END if input = 0
 STA VALUE // Store input as VALUE
LOOP LDA RESULT // Load the RESULT
 ADD VALUE // Add VALUE, the user provided input, to RESULT
 STA RESULT // Store the new RESULT
 LDA COUNT // Load the COUNT
 ADD ONE // Add ONE to the COUNT
 STA COUNT // Store the new COUNT
 SUB VALUE // Subtract the user provided input VALUE from COUNT
 BRZ ENDLOOP // If zero (VALUE has been added to RESULT by VALUE times), branch to ENDLOOP
 BRA LOOP // Branch to LOOP to continue adding VALUE to RESULT
ENDLOOP LDA RESULT // Load RESULT
 OUT // Output RESULT
 BRA START // Branch to the START to initialize and get another input VALUE
END HLT // HALT - a zero was entered so done!
RESULT DAT // Computed result (defaults to 0)
COUNT DAT // Counter (defaults to 0)
ONE DAT 1 // Constant, value of 1
VALUE DAT // User provided input, the value to be squared (defaults to 0)
ZERO DAT // Constant, value of 0 (defaults to 0)
```

_Note: If there is no data after a DAT statement then the default value 0 is stored in the memory address._

In the example above, [BRZ ENDLOOP] depends on undefined behaviour, as COUNT-VALUE can be negative, after which the ACCUMULATOR value is undefined, resulting in BRZ either branching or not (ACCUMULATOR may be zero, or wrapped around). To make the code compatible with the specification, replace:

```
 ...
 LDA COUNT // Load the COUNT
 ADD ONE // Add ONE to the COUNT
 STA COUNT // Store the new COUNT
 SUB VALUE // Subtract the user provided input VALUE from COUNT
 BRZ ENDLOOP // If zero (VALUE has been added to RESULT by VALUE times), branch to ENDLOOP
 ...
```

with the following version, which does VALUE-COUNT instead of COUNT-VALUE, making sure the accumulator never underflows:

```
 ...
 LDA COUNT // Load the COUNT
 ADD ONE // Add ONE to the COUNT
 STA COUNT // Store the new COUNT
 LDA VALUE // Load the VALUE
 SUB COUNT // Subtract COUNT from the user provided input VALUE
 BRZ ENDLOOP // If zero (VALUE has been added to RESULT by VALUE times), branch to ENDLOOP
 ...
```

## <span id="See_also" class="mw-headline">See also</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Little_man_computer&amp;action=edit&amp;section=10" title="Edit section: See also">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

- [CARDboard Illustrative Aid to Computation](https://en.wikipedia.org/wiki/CARDboard_Illustrative_Aid_to_Computation "CARDboard Illustrative Aid to Computation") (another instructional model)

## <span id="References" class="mw-headline">References</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Little_man_computer&amp;action=edit&amp;section=11" title="Edit section: References">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

1. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Little_man_computer#cite_ref-1">^</a>
  </strong>
  </span>

   

  <span class="reference-text"><a href="http://www.acs.ilstu.edu/faculty/javila/lmc/" class="external text">“Little Man Computer”</a>. <a href="https://en.wikipedia.org/wiki/Illinois_State_University" title="Illinois State University">Illinois State University</a>. May 1, 2000<span class="reference-accessdate">. Retrieved <span class="nowrap">March 8,</span> 2009</span>.<span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ALittle+man+computer&amp;rft.btitle=Little+Man+Computer&amp;rft.date=2000-05-01&amp;rft.genre=unknown&amp;rft_id=http%3A%2F%2Fwww.acs.ilstu.edu%2Ffaculty%2Fjavila%2Flmc%2F&amp;rft.pub=Illinois+State+University&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span></span>

2. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Little_man_computer#cite_ref-2">^</a>
  </strong>
  </span>

   

  <span class="reference-text">Yurcik, W.; Osborne, H. (2001). “A crowd of Little Man Computers: Visual computer simulator teaching tools”. <em>Proceeding of the 2001 Winter Simulation Conference (Cat. No.01CH37304)</em> <strong>2</strong>. p. 1632\. <a href="https://en.wikipedia.org/wiki/Digital_object_identifier" title="Digital object identifier">doi</a>:<a href="https://dx.doi.org/10.1109%2FWSC.2001.977496" class="external text">10.1109/WSC.2001.977496</a>. <a href="https://en.wikipedia.org/wiki/International_Standard_Book_Number" title="International Standard Book Number">ISBN</a> <a href="https://en.wikipedia.org/wiki/Special:BookSources/0-7803-7307-3" title="Special:BookSources/0-7803-7307-3">0-7803-7307-3</a>.<span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ALittle+man+computer&amp;rft.atitle=A+crowd+of+Little+Man+Computers%3A+Visual+computer+simulator+teaching+tools&amp;rft.aufirst=W.&amp;rft.aulast=Yurcik&amp;rft.au=Osborne%2C+H.&amp;rft.btitle=Proceeding+of+the+2001+Winter+Simulation+Conference+%28Cat.+No.01CH37304%29&amp;rft.date=2001&amp;rft.genre=bookitem&amp;rft_id=info%3Adoi%2F10.1109%2FWSC.2001.977496&amp;rft.isbn=0-7803-7307-3&amp;rft.pages=1632&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span></span>

3. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Little_man_computer#cite_ref-3">^</a>
  </strong>
  </span>

   

  <span class="reference-text">Yurcik, W.; Brumbaugh, L. (2001). “A web-based little man computer simulator”. <em>Proceedings of the thirty-second SIGCSE technical symposium on Computer Science Education - SIGCSE ’01</em>. p. 204\. <a href="https://en.wikipedia.org/wiki/Digital_object_identifier" title="Digital object identifier">doi</a>:<a href="https://dx.doi.org/10.1145%2F364447.364585" class="external text">10.1145/364447.364585</a>. <a href="https://en.wikipedia.org/wiki/International_Standard_Book_Number" title="International Standard Book Number">ISBN</a> <a href="https://en.wikipedia.org/wiki/Special:BookSources/1581133294" title="Special:BookSources/1581133294">1581133294</a>.<span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ALittle+man+computer&amp;rft.atitle=A+web-based+little+man+computer+simulator&amp;rft.au=Brumbaugh%2C+L.&amp;rft.aufirst=W.&amp;rft.aulast=Yurcik&amp;rft.btitle=Proceedings+of+the+thirty-second+SIGCSE+technical+symposium+on+Computer+Science+Education++-+SIGCSE+%2701&amp;rft.date=2001&amp;rft.genre=bookitem&amp;rft_id=info%3Adoi%2F10.1145%2F364447.364585&amp;rft.isbn=1581133294&amp;rft.pages=204&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span></span>

4. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Little_man_computer#cite_ref-4">^</a>
  </strong>
  </span>

   

  <span class="reference-text">Osborne, H.; Yurcik, W. (2002). “The educational range of visual simulations of the Little Man Computer architecture paradigm”. <em>32nd Annual Frontiers in Education</em>. pp. S4G–S19\. <a href="https://en.wikipedia.org/wiki/Digital_object_identifier" title="Digital object identifier">doi</a>:<a href="https://dx.doi.org/10.1109%2FFIE.2002.1158742" class="external text">10.1109/FIE.2002.1158742</a>. <a href="https://en.wikipedia.org/wiki/International_Standard_Book_Number" title="International Standard Book Number">ISBN</a> <a href="https://en.wikipedia.org/wiki/Special:BookSources/0-7803-7444-4" title="Special:BookSources/0-7803-7444-4">0-7803-7444-4</a>.<span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ALittle+man+computer&amp;rft.atitle=The+educational+range+of+visual+simulations+of+the+Little+Man+Computer+architecture+paradigm&amp;rft.aufirst=H.&amp;rft.aulast=Osborne&amp;rft.au=Yurcik%2C+W.&amp;rft.btitle=32nd+Annual+Frontiers+in+Education&amp;rft.date=2002&amp;rft.genre=bookitem&amp;rft_id=info%3Adoi%2F10.1109%2FFIE.2002.1158742&amp;rft.isbn=0-7803-7444-4&amp;rft.pages=S4G-S19&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span></span>

5. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Little_man_computer#cite_ref-5">^</a>
  </strong>
  </span>

   

  <span class="reference-text">Chen, Stephen Y.; Cudmore, William C. <a href="http://www.yorku.ca/sychen/research/LMC/index.html" class="external text">“The Little Man Computer”</a>. York University<span class="reference-accessdate">. Retrieved <span class="nowrap">October 7,</span> 2010</span>.<span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ALittle+man+computer&amp;rft.au=Cudmore%2C+William+C&amp;rft.aufirst=Stephen+Y.&amp;rft.aulast=Chen&amp;rft.btitle=The+Little+Man+Computer&amp;rft.genre=unknown&amp;rft_id=http%3A%2F%2Fwww.yorku.ca%2Fsychen%2Fresearch%2FLMC%2Findex.html&amp;rft.pub=York+University&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span></span>

6. <span class="mw-cite-backlink">
    <strong>
    <a href="https://en.wikipedia.org/wiki/Little_man_computer#cite_ref-6">^</a>
  </strong>
  </span>

   

  <span class="reference-text">Coley, Mike. <a href="http://www.gcsecomputing.org.uk/lmc/lmc.html" class="external text">“The Little Man Computer”</a><span class="reference-accessdate">. Retrieved <span class="nowrap">April 12,</span> 2012</span>.<span class="Z3988" title="ctx_ver=Z39.88-2004&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3ALittle+man+computer&amp;rft.aufirst=Mike&amp;rft.aulast=Coley&amp;rft.btitle=The+Little+Man+Computer&amp;rft.genre=unknown&amp;rft_id=http%3A%2F%2Fwww.gcsecomputing.org.uk%2Flmc%2Flmc.html&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook">
    <span>
  </span>
  </span></span>

## <span id="External_links" class="mw-headline">External links</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Little_man_computer&amp;action=edit&amp;section=12" title="Edit section: External links">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

- [Richard J. Povinelli:Teaching:Introduction to Computer Hardware and Software:Little Man Computer](http://www.povinelli.org/teaching/eece2710/lmc.html)
- [The "Little Man" Computer](http://elearning.algonquincollege.com/coursemat/dat2343/lectures.f03/12-LMC.htm)

### <span id="Simulators" class="mw-headline">Simulators</span>

<span class="mw-editsection">
  <span class="mw-editsection-bracket">[</span>
  <a href="https://en.wikipedia.org/w/index.php?title=Little_man_computer&amp;action=edit&amp;section=13" title="Edit section: Simulators">edit</a>
  <span class="mw-editsection-bracket">]</span>
</span>

- [Microsoft Excel LMC simulator](http://www.ictcool.com/2011/12/16/download-lmc-simulation-v-1-5-2-requires-microsoft-excel/)
- [Java Applet](http://www.yorku.ca/sychen/research/LMC/index.html)
- [Windows Executable](http://gcsecomputing.org.uk/lmc/lmc.html)
- [JavaScript](http://matt.krutar.org/LMC4/)
- [JavaScript (in browser) LMC with mnemonic support and error detection](http://robowriter.info/little-man-computer/)
- [Windows Executable](http://superdecade.blogspot.co.uk/p/little-man-computer.html)
- [Emacs package](http://elpa.gnu.org/packages/lmc.html)
- [Adobe Flash Version](http://ssjx.co.uk/games/educational/lmc.php)
- [Adobe Director Version](http://alexisk.heliohost.org/LMCv1/LMCv1.htm)
- [Windows Executable](http://alexisk.heliohost.org/LMCv1/LMCv1.exe)
- [Adobe Director Version with Graphic Little Man](http://alexisk.heliohost.org/LMCv2/LMCv2.htm)
- [Windows Executable with Graphic Little Man](http://alexisk.heliohost.org/LMCv2/LMCv2.exe)
- [GCSE Computing](http://www.gcsecomputing.org.uk/lmc/lmc_java.html)
- [Little Man Computer Simulation (LMC) in JavaScript with Fetch/Execute](http://www.peterhigginson.co.uk/LMC/)

<!--  -->

- [CPU BattleTanks:Control a tank in your browser with a Little Man Computer CPU](https://github.com/pddring/cpu-battle-tank/wiki)

![](https://en.wikipedia.org/wiki/Special:CentralAutoLogin/start?type=1x1)

Retrieved from "<https://en.wikipedia.org/w/index.php?title=Little_man_computer&oldid=700962633>"

[Categories](https://en.wikipedia.org/wiki/Help:Category "Help:Category"):

- [Educational abstract machines](https://en.wikipedia.org/wiki/Category:Educational_abstract_machines "Category:Educational abstract machines")
