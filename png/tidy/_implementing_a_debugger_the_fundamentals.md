# Implementing a Debugger: The Fundamentals

[Original URL](http://backtrace.io/blog/blog/2016/08/11/debugger-internals/)

> This is the first of a two-part series describing the implementation of a generic debugging tool. Part one covers the core internals of a debugger; part two will focus on extending a debugger to...

_This is the first of a two-part series describing the implementation of a generic debugging tool. Part one covers the core internals of a debugger; part two will focus on extending a debugger to support a specific programming language – Go._

Implementing a debugging tool may seem like a monumental task. `gdb`, one of the most popular debuggers, is over 500,000 [SLOC](https://en.wikipedia.org/wiki/Source_lines_of_code) (according to `cloc`); it's not exactly easy to pick up and read through, and it has many auxiliary features not fundamental to a debugger's purpose. Engineers aren't required to understand a debugger's internals to use one, and rightfully so – if we had to fundamentally understand something in order to use it, we'd use precious few things. We're more familiar with a debugger's features: print a backtrace, hex dump some memory, set some watchpoints, or write some macros combining these simpler tasks.

> Any sufficiently advanced technology is indistinguishable from magic.

– Arthur C. Clarke

Let's make the world a less magical place. Read on and dive into the guts of debuggers and systems programming.

## In this article

Content:

- Explanation of how debuggers work

Helpful background knowledge:

- Basic knowledge of Unix/Linux operating systems
- Basic knowledge of programming with languages like C or Go
- Basic experience using a debugger like `gdb`

Target audience:

- Software engineers who use debuggers

## Magnets. How do they work?

To really understand what's involved in creating a debugging tool for a language like Go, we must first understand general debuggers.

What is a debugger?

It's a tool that indicates what a program is doing during execution (otherwise known as introspection); some debuggers let you control the program's execution without recompiling or restarting it (commonly deemed interaction).

From the introspective perspective, a debugger extracts the state of an application – which functions your threads are executing, or the values of variables, or the data contained in arbitrary memory locations.

On the interactive front, one might use a debugger to set a breakpoint, which stops execution of an application before it reaches a certain point. `break a_crashing_function` tells the debugger that the program should execute as normal until it's about to execute instructions from that function. At that point, the debugger will stop execution and transfer control back to the user, who can then inspect state using the aforementioned introspection features.

We'll explore the introspective aspect in this article, which covers much of the architecture and interesting features of debuggers.

**For simplicity, we will focus on the flow of debugging a live process on an x86_64 Linux operating system – qualify everything below with "for a live process on Linux." Some differences arise in other environments, but many of the techniques are similar. We will also ignore many edge cases.**

### The introspective guts

You have a long-running process and want to see what it's currently doing. You execute a debugging tool on it and receive output describing, among other things, callstacks for every thread and local variable data for each function.

How do we, the debugger, extract this information?

#### The sauces

We use two major data sources: the `/proc` pseudo-filesystem and the executable file of the process itself.

The `/proc` filesystem (hereafter `procfs`) contains, among other things, a representation of processes currently running on the system. This representation is comprised of pseudo-files organized into a hierarchy providing access to each process' memory and its various attributes, such as executable path, thread list, and resource utilization.

They're called pseudo-files because they aren't real files stored on disk; they're just nice interfaces to kernel memory that allow for normal filesystem calls such as `open` and `pread`. One such pseudo-file is `/proc/[pid]/mem`. This is what provides direct access to the process' memory.

The alternative to `procfs` would be using heavier-weight system calls such as `ptrace` – which we still use for a small subset of process state, as we'll see later.

Our second data source, the executable for a process, is also retrieved from `procfs`. In the simple case, we can use the `/proc/[pid]/exe` file, which is a symbolic link to the path of the executable. We call `open` on the executable file itself and parse it to retrieve its debug information.

This changes slightly when a process is linked to shared libraries; each shared object will also have its own debug information. We use `/proc/[pid]/maps` in this case. It provides descriptions for each region of memory mapped into the process, such as the address range, permission bits (read-write-execute-shared) for that range, and, most importantly for a debugger, the file backing the memory region. This file can be the aforementioned executable file or a shared library object file. When we're interested in a particular instruction address, we search for a range containing it in `/proc/[pid]/maps` and parse the associated backing file, if any, for debug information, as we did above with just the executable file. We'll assume we're working with the executable file for the remainder of this article.

The debug information stored in the executable file tells us how to translate raw memory locations into recognizable program objects such as functions, variables, and types. This is what debuggers like `gdb` use. Debug information includes:

- The locations of the program's functions and global variables
- The stack layout of each function, dependent on the current location of execution within the function – i.e., the instruction pointer

  - Expressions that evaluate to the locations of local variables and parameters on each function's stack

- The types of the variables, which, combined with the above location information, indicate the memory range to read for any particular variable

We'll explore debug information in more detail later.

There are other sources of data we use to provide more complete snapshots of a system, but the above two are the most relevant.

The data sources:

- The process' memory through `procfs`
- The debugging information contained in the process' executable file

#### Be like water

Now that we've identified our data sources, we can focus on the overall flow of a debugger. We'll go from the initial step of attaching a debugger to a process to the final stage of generating the debugger's output. Throughout these steps, we'll always read from the two major data sources using some mechanism.

First, we must identify the process to debug using this process' `pid`, or process id, which is a system-wide unique integer identifier. This is assigned to each process by the kernel and can be retrieved by running a command on a running executable's name, such as `pgrep a_buggy_process`.

##### Attach and stop

Before we can extract any data from the process, we need to pause its execution. Failing to do so would cause data and liveness races throughout the debugging process – for example, we could try to read data from a thread as it's exiting and afterwards. There are strategies for minimizing stop duration, but those will be the subject of a future post.

To stop a process, we use the monolithic `ptrace` syscall.

> The ptrace() system call provides a means by which one process (the "tracer") may observe and control the execution of another process (the "tracee"), and examine and change the tracee's memory and registers. It is primarily used to implement breakpoint debugging and system call tracing.

– `ptrace` man page

Though `ptrace` provides means to affect many aspects of a process' execution, memory, and register state, the only operations we'll need for now are `PTRACE_ATTACH` and `PTRACE_DETACH`.

`PTRACE_ATTACH` establishes the tracer(debugger)-tracee(target process) relationship and does a few useful things for us, some being:

- Stops the target process. Note – `ptrace(PTRACE_ATTACH, ...)` must be called on all target threads; it stops only the single thread provided to the command. The main process thread is stopped first.
- Allows the debugger to read from process memory/registers.

`PTRACE_DETACH` simply reverses these effects and allows the target process to continue executing. Debuggers call `ptrace(PTRACE_DETACH, ...)` after they finish their operations.

##### Extract the thread list

We've now attached to and stopped the target process. The next step is data extraction.

Let's assume we want just the state of the application at the time of the snapshot request. The minimum set of information required to be useful is the stacktrace of each running thread – the series of calls leading up to the currently executing function of the thread, beginning with the thread's start function.

To do this, we'll need the list of threads owned by the process and the current execution contexts of these threads, which is represented by their register sets. The most important registers in such a set are `rip`, or `instruction pointer`, and `rsp`, or `stack pointer`. The former is the memory location of the instruction about to be executed on the thread; the latter is the current memory location of the top (bottom, really, since the stack typically grows downwards) of the stack.

To get the thread list, debuggers commonly use a library called `libthread_db`, which essentially traverses and extracts information from the in-memory representations of the threads of a given process. This isn't always viable; some applications don't use threading libraries which create the data structures `libthread_db` expects. There are operating system-dependent alternatives in such cases (like `procfs`), as we'll see later with Go. For now, assume `libthread_db` has worked. This will read data directly from the target process' memory – think of it as using `procfs`.

##### Copy the registers

So we've made a few calls to this thread debugging library and now have a list of thread objects for the process we're debugging. We need their register sets to begin determining what the threads are doing. For this, we'll again use the `ptrace` syscall.

`PTRACE_GETREGS` and `PTRACE_GETFPREGS` are the `ptrace` actions we're interested in; they will retrieve copies of the specified thread's general-purpose and floating-point registers, respectively. As mentioned previously, with respect to debugging flow, we really just need the `rip` and `rsp` registers from these sets.

##### Unwind

Now we have the current register sets for each thread. The `rip` specifically indicates the currently executing function. Remember, we want the entire callstack for each thread, not just the currently executing function. Raw register values are also of little use to an end-user; people know their functions' names, not their functions' locations in memory. We'll need to translate these instruction addresses to their associated symbols.

This is where debugging information finally makes its entrance. It's used to translate raw addresses and memory values to program elements familiar to the programmer, such as function names.

Let's quickly go through generating the callstack for a specific thread. This is generally referred to as unwinding (another topic for a future post).

To unwind a callstack, we want to know which function called the currently executing function, and which function called the caller, and so on, up until we hit the thread's starting function. We use the return address for this. It'll be within the range of instruction addresses for the calling function (we're ignoring edge cases – like tail call optimization – remember?).

When a function is called, the return address – i.e., the address of the instruction to execute after the called function completes – is saved on the stack before the callee executes anything. As this callee function executes, it pushes and pops data onto and from the stack – this data being the values (like variables) the function uses to perform its work. Thus, depending on where in a function execution is, the return address is stored at different offsets relative to the current stack pointer.

The executable's debug information tells us what these offsets are at each point the stack grows or shrinks. This is organized into a matrix of offsets relative to a known register value, such as the current `rsp`, for a range of addresses. We can find the matrix associated with a function by searching for an entry – called a Frame Description Entry, or `FDE` – whose address range contains the current `rip`.

As an example, consider the first few assembly instructions for a simple `strtol` wrapper function:

```
1
2
3
4
5
6
7
8``` | ```
0000000000405450 <strtol_wrapper>:
 405450: 41 55 push %r13
 405452: 41 54 push %r12
 405454: 49 89 f5 mov %rsi,%r13
 405457: 55 push %rbp
 405458: 53 push %rbx
...```
---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Assume the `rip` is `0x405458`. Searching the debug information for that (I usually use `readelf --debug-dump=frames-interp <executable_path>` when searching manually), we see this:

```
1
2
3
4
5
6
7
8``` | ```
000000f0 000000000000004c 000000c4 FDE cie=00000030 pc=0000000000405450..00000000004054ce
 LOC CFA rbx rbp r12 r13 ra
0000000000405450 rsp+8 u u u u c-8
0000000000405452 rsp+16 u u u c-16 c-8
0000000000405454 rsp+24 u u c-24 c-16 c-8
0000000000405458 rsp+32 u c-32 c-24 c-16 c-8
...```
---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Each row in the debug output is the state of the stack before that particular instruction executes (which is why there's no entry for `0x405457`). The values we're interested in here are `CFA` and `ra`. The other register values are useful for reasons we'll see soon, but they can be ignored for unwinding.

`CFA` stands for Canonical Frame Address, which is the value of `rsp` when the calling function called the current function. Put more simply, the `CFA` is the `rsp` for the calling function, which we'll need when we continue unwinding from that function.

`ra` stands for Return Address. In the above case, it's been specified as an offset relative to `c`, which is the current frame's `CFA` we've just extracted above. This is where the return address is stored, not the return address itself, so we'll need to read this value from the process' memory through `procfs`.

Match the instruction addresses from the assembly snippet to the ones in the debug snippet, and you'll see the offsets change as expected. When we push an 8-byte register, such as `%r12`, onto the stack, the offsets increase in magnitude by 8 bytes. The starting offset from `rsp` to the `CFA` is `+8` because the return address is stored in the first 8 bytes of the stack before anything in the function executes and modifies the stack. Thus, the function's starting rsp is already 8 bytes past its beginning. The offset of `ra` always being `-8` (from the `CFA`) confirms this.

We now use the return address value and search this debug information again for the frame description entry including that address. We use the `CFA` we've just derived as the new `rsp` and repeat the above process, stopping when there's no return address (or we can't derive a valid one).

Unwinding is thus summarized as:

1. Use a thread's current instruction address to find the frame description in the debug information.
2. Use that description to find the calling frame's next instruction address and current stack pointer.
3. Use this caller's information to repeat this process until we reach a stopping point.

##### Variables

Now we have the instructions representing the callstacks for all threads. Translated to their actual function names, these stacks are already useful when debugging a program – how often have you run a crashing application under `gdb` and found the issue immediately after printing the backtrace with `bt`?

We'll often need more, however. A function could look fine even after reading its code knowing there's a crash condition there. To go further, we'll need variable data.

To get that, we again turn to the debugging information found in the executable file. When evaluating the variables for each frame in a callstack, we use the frame's `rip` and search for associated data, this time looking for entries describing the function and its local variables (rather than the frame's unwinding instructions). These entries indicate which local variables the function creates; in which address ranges they are in scope; the types of the variables, which specify their sizes in memory; the locations of the variables, which can be expressions comprised of memory locations, registers, and offsets; the names of everything as they appear in the source code; and so on, giving us a complete picture of the state of a function. With this information, a debugger can read what it needs to from specific regions of a process' memory space and translate that into what is familiar to the programmer.

We'll see some examples of function and variable debug information below, but knowing the specifics of this isn't important for understanding the overall flow.

#### And then...

That's it. We now have all callstacks for all threads with full local variable information. There's much more a debugger can do to provide further introspection, but these are the fundamentals.

The flow condensed:

- Use `ptrace` to attach to and stop a process
- Extract the thread list using `libthread_db` or `procfs`
- Use `ptrace` to extract the current register set of each thread
- Use the debugging information in the executable file to unwind each thread's callstack from the initial `rip` to the thread's starting function
- Use the debugging information to retrieve variable state

#### And my axe

Beyond the fundamentals, it's important to know the specifics of some major components of debug information; without such knowledge, it's difficult to understand what debuggers are capable of and how language internals affect those capabilities. We've already seen some of these components above, but there are a few more to cover. First, however, some introductions are in order.

DWARF is the most widely used specification of debug information on Unix systems.

Compilers and linkers emit DWARF debug information and store it in specific sections in an executable, assuming it is compiled with debug information enabled. If you're on a system using the [ELF](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format) object file format, these sections are typically prefixed with `.debug_`.

(Debug information may sometimes live elsewhere – see [.gnu_debuglink](https://sourceware.org/gdb/onlinedocs/gdb/Separate-Debug-Files.html).)

There are several (mostly compatible) [standards](http://dwarfstd.org/Download.php) for DWARF. Some differences between standards can result in some hairy bugs in debuggers themselves, as we will see in a future post.

From the perspective of a programmer, the main structure in DWARF debug information is the debugging information entry, or `DIE`. These represent elements of the source program – types, variables, functions, compilation units, etc. Each DIE has an identifying tag, which defines the type of the entry (e.g. variable vs. function), and an attribute set, which defines the specific fields describing the entry (e.g. this particular variable entry has an `external` attribute, which means variables described by this entry are visible outside of their enclosing compilation units).

As an example, consider the following function:

```
1
2
3
4
5
6
7
8
9
10
11
12
13
14``` | ```
static int
simple_function(bool a_parameter)
{
 int a_variable;
 int another_variable;
 a_variable = 3;
 another_variable = a_variable + 2;
 if (a_parameter == true)
 return a_variable + another_variable;
 return a_variable - another_variable;
}```
--------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The DIE for this function, retrieved with `readelf --debug-dump=info <executable>`:

```
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30``` | ```
 <1><73>: Abbrev Number: 4 (DW_TAG_subprogram)
 <74> DW_AT_name : (indirect string, offset: 0x48): simple_function
 <78> DW_AT_decl_file : 1
 <79> DW_AT_decl_line : 5
 <7a> DW_AT_prototyped : 1
 <7a> DW_AT_type : <0x57>
 <7e> DW_AT_low_pc : 0x400536
 <86> DW_AT_high_pc : 0x31
 <8e> DW_AT_frame_base : 1 byte block: 9c (DW_OP_call_frame_cfa)
 <90> DW_AT_GNU_all_call_sites: 1
 <90> DW_AT_sibling : <0xbf>
 <2><94>: Abbrev Number: 5 (DW_TAG_formal_parameter)
 <95> DW_AT_name : (indirect string, offset: 0x76): a_parameter
 <99> DW_AT_decl_file : 1
 <9a> DW_AT_decl_line : 5
 <9b> DW_AT_type : <0xbf>
 <9f> DW_AT_location : 2 byte block: 91 5c (DW_OP_fbreg: -36)
 <2><a2>: Abbrev Number: 6 (DW_TAG_variable)
 <a3> DW_AT_name : (indirect string, offset: 0x9b): a_variable
 <a7> DW_AT_decl_file : 1
 <a8> DW_AT_decl_line : 7
 <a9> DW_AT_type : <0x57>
 <ad> DW_AT_location : 2 byte block: 91 68 (DW_OP_fbreg: -24)
 <2><b0>: Abbrev Number: 6 (DW_TAG_variable)
 <b1> DW_AT_name : (indirect string, offset: 0x29): another_variable
 <b5> DW_AT_decl_file : 1
 <b6> DW_AT_decl_line : 8
 <b7> DW_AT_type : <0x57>
 <bb> DW_AT_location : 2 byte block: 91 6c (DW_OP_fbreg: -20)
 <2><be>: Abbrev Number: 0```
--------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Ignore most of the specific attributes; we are looking for a 1:1 mapping between program elements and DIEs. The first DIE is the function, `simple_function`, as we'd expect – this is the scope containing other program elements, like local variables. This function DIE has several child DIEs, one for each of its parameters and variables.

DIEs are organized into a graph. The portion of the graph representing ownership (e.g. compilation units owning functions owning variables) is really represented as a tree, with each compilation unit being a root node owning subprogram (function) and global variable DIEs. Certain DIEs may reference others in a non-ownership-related manner – a variable DIE may reference a common type DIE representing a `struct` used in multiple places.

We can see this graph in the `readelf` output above. Each DIE is prefixed by its depth – the `simple_function` DIE has a depth of 1, the `a_parameter` parameter has a depth of 2, and so on. Each increase in depth indicates the aforementioned parent-child ownership relationship. The `DW_AT_type` attribute of the variable DIEs is an example non-ownership edge. Consider the `a_variable` and `another_variable` DIEs. Their `DW_AT_type` attributes both reference the location `0x57`, which is:

```
1
2
3
4``` | ```
 <1><57>: Abbrev Number: 3 (DW_TAG_base_type)
 <58> DW_AT_byte_size : 4
 <59> DW_AT_encoding : 5 (signed)
 <5a> DW_AT_name : int```
-------------- | ---------------------------------------------------------------------------------------------------------------------------------------

As both variables are `int` types in the source code, their types both reference the common `int` type DIE.

To save space, DIEs are stored by writing just the values of their attributes; the identifiers for each individual attribute are not written. Instead, the entire DIE is preceded by an abbreviation code; these codes are defined as attribute sets (with identifying information) stored in a separate debug section of the executable file. Abbreviation codes are shared amongst DIEs. Any identifying tag, like `DW_TAG_variable`, may have more than one abbreviation code/attribute set – for example, only some variables have an `external` attribute, and so those variables would use a different attribute set while still using `DW_TAG_variable`.

Though there are other structures in addition to DIEs, they aren't necessary to understand to do significant work with debuggers.

## Fin

With the information we've discussed thus far, we now have a good framework for understanding the introspective tasks debuggers do and how they do it. In part two, we'll use this framework to investigate some of the specific work involved in extending a debugger to support a [new language](http://backtrace.io/blog/blog/2016/07/21/go-support/).
