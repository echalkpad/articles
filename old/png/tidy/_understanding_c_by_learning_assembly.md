# Understanding C by learning assembly

[Original URL](https://www.recurse.com/blog/7-understanding-c-by-learning-assembly)

> Last time, Alan showed how to use GDB as a tool to learn C. Today I want to go one step further and use GDB to help us understand assembly as well. Abstraction layers are great tools for building...

Last time, [Alan](https://github.com/happy4crazy) showed how to use [GDB as a tool to learn C](https://www.hackerschool.com/blog/5-learning-c-with-gdb). Today I want to go one step further and use GDB to help us understand assembly as well.

Abstraction layers are great tools for building things, but they can sometimes get in the way of learning. My goal in this post is to convince you that in order to rigorously understand C, we must also understand the assembly that our C compiler generates. I'll do this by showing you how to disassemble and read a simple program with GDB, and then we'll use GDB and our knowledge of assembly to understand how static local variables work in C.

_Note: All the code in this post was compiled on an x86_64 CPU running Mac OS X 10.8.1 using Clang 4.0 with optimizations disabled (_`-O0`_)._

## Learning assembly with GDB

Let's start by disassembling a program with GDB and learning how to read the output. Type the following program into a text file and save it as `simple.c`:

```
int main()
{
 int a = 5;
 int b = a + 6;
 return 0;
}
```

Now compile it with debugging symbols and no optimizations and then run GDB:

```
$ CFLAGS="-g -O0" make simple
cc -g -O0 simple.c -o simple
$ gdb simple
```

Inside GDB, we'll break on `main` and run until we get to the return statement. We put the number 2 after `next` to specify that we want to run `next` twice:

```
(gdb) break main
(gdb) run
(gdb) next 2
```

Now let's use the `disassemble` command to show the assembly instructions for the current function. You can also pass a function name to `disassemble` to specify a different function to examine.

```
(gdb) disassemble
Dump of assembler code for function main:
0x0000000100000f50 <main+0>: push %rbp
0x0000000100000f51 <main+1>: mov %rsp,%rbp
0x0000000100000f54 <main+4>: mov $0x0,%eax
0x0000000100000f59 <main+9>: movl $0x0,-0x4(%rbp)
0x0000000100000f60 <main+16>: movl $0x5,-0x8(%rbp)
0x0000000100000f67 <main+23>: mov -0x8(%rbp),%ecx
0x0000000100000f6a <main+26>: add $0x6,%ecx
0x0000000100000f70 <main+32>: mov %ecx,-0xc(%rbp)
0x0000000100000f73 <main+35>: pop %rbp
0x0000000100000f74 <main+36>: retq 
End of assembler dump.
```

The `disassemble` command defaults to outputting instructions in AT&T syntax, which is the same syntax used by the GNU assembler. Instructions in AT&T syntax are of the format `mnemonic source, destination`. The mnemonic is a human readable name for the instruction. Source and destination are operands and can be immediate values, registers, memory addresses, or labels. Immediate values are constants, and are prefixed by a `$`. For instance, `$0x5` represents the number 5 in hexadecimal. Register names are prefixed by a `%`.

### Registers

It's worth taking a quick detour to understand registers. Registers are data storage locations directly on the CPU. With some exceptions, the size, or _width_, of a CPU's registers define its architecture. So if you have a 64-bit CPU, your registers will be 64 bits wide. The same is true of 32-bit CPUs (32-bit registers), 16-bit CPUs, and so on. Registers are very fast to access and are often the operands for arithmetic and logic operations.

The x86 family has a number of general and special purpose registers. General purpose registers can be used for any operation and their value has no particular meaning to the CPU. On the other hand, the CPU relies on special purpose registers for its own operation and the values stored in them have a specific meaning depending on the register. In our example above, `%eax` and `%ecx` are general purpose registers, while `%rbp` and `%rsp` are special purpose registers. `%rbp` is the base pointer, which points to the base of the current stack frame, and `%rsp` is the stack pointer, which points to the top of the current stack frame. `%rbp` always has a higher value than `%rsp` because the stack starts at a high memory address and grows downwards. If you are unfamiliar with the call stack, you can find a [good introduction on Wikipedia](http://en.wikipedia.org/wiki/Call_stack).

One quirk of the x86 family is that it has maintained backwards compatibility all the way back to the 16-bit 8086 processor. As x86 moved from 16-bit to 32-bit to 64-bit, the registers were expanded and given new names so as to not break backwards compatibility with code that was written for older, narrower CPUs.

Take the general purpose register AX, which is 16 bits wide. The high byte can be accessed with the name AH, and the low byte with the name AL. When the 32-bit 80386 came out, the Extended AX register, or EAX, referred to the 32-bit register, while AX continued to refer to a 16-bit register that made up the lower half of EAX. Similarly, when the x86_64 architecture came out, the "R" prefix was used and EAX made up the lower half of the 64-bit RAX register. I've included a diagram below based on a [Wikipedia article](http://en.wikipedia.org/wiki/X86#x86_registers) to help visualize the relationships I described:

```
|__64__|__56__|__48__|__40__|__32__|__24__|__16__|__8___|
|__________________________RAX__________________________|
|xxxxxxxxxxxxxxxxxxxxxxxxxxx|____________EAX____________|
|xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|_____AX______|
|xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|__AH__|__AL__|
```

### Back to the code

This should be enough information to start reading our disassembled program.

```
0x0000000100000f50 <main+0>: push %rbp
0x0000000100000f51 <main+1>: mov %rsp,%rbp
```

The first two instructions are called the function prologue or preamble. First we push the old base pointer onto the stack to save it for later. Then we copy the value of the stack pointer to the base pointer. After this, `%rbp` points to the base of `main`'s stack frame.

```
0x0000000100000f54 <main+4>: mov $0x0,%eax
```

This instruction copies 0 into `%eax`. The x86 calling convention dictates that a function's return value is stored in `%eax`, so the above instruction sets us up to return 0 at the end of our function.

```
0x0000000100000f59 <main+9>: movl $0x0,-0x4(%rbp)
```

Here we have something we haven't encountered before: `-0x4(%rbp)`. The parentheses let us know that this is a memory address. Here, `%rbp` is called the base register, and `-0x4` is the displacement. This is equivalent to `%rbp + -0x4`. Because the stack grows downwards, subtracting 4 from the base of the current stack frame moves us into the current frame itself, where local variables are stored. This means that this instruction stores 0 at `%rbp - 4`. It took me a while to figure out what this line was for, but it seems that clang [allocates a hidden local variable](http://lists.cs.uiuc.edu/pipermail/cfe-dev/2012-February/019767.html) for an implicit return value from `main`.

You'll also notice that the mnemonic has the suffix `l`. This signifies that the operands will be `l`ong (32 bits for integers). Other valid suffixes are `b`yte, `s`hort, `w`ord, `q`uad, and `t`en. If you see an instruction that does not have a suffix, the size of the operands are inferred from the size of the source or destination register. For instance, in the previous line, `%eax` is 32 bits wide, so the `mov` instruction is inferred to be `movl`.

```
0x0000000100000f60 <main+16>: movl $0x5,-0x8(%rbp)
```

Now we're getting into the meat of our sample program! The first line of assembly is the first line of C in `main` and stores the number 5 in the next available local variable slot (`%rbp - 0x8`), 4 bytes down from our last local variable. That's the location of `a`. We can use GDB to verify this:

```
(gdb) x &a
0x7fff5fbff768: 0x00000005
(gdb) x $rbp - 8
0x7fff5fbff768: 0x00000005
```

Note that the memory addresses are the same. You'll notice that GDB sets up variables for our registers, but like all variables in GDB, we prefix it with a `$` rather than the `%` used in AT&T assembly.

```
0x0000000100000f67 <main+23>: mov -0x8(%rbp),%ecx
0x0000000100000f6a <main+26>: add $0x6,%ecx
0x0000000100000f70 <main+32>: mov %ecx,-0xc(%rbp)
```

We then move `a` into `%ecx`, one of our general purpose registers, add 6 to it and store the result in `%rbp - 0xc`. This is the second line of C in `main`. You've maybe figured out that `%rbp - 0xc` is `b`, which we can verify in GDB:

```
(gdb) x &b
0x7fff5fbff764: 0x0000000b
(gdb) x $rbp - 0xc
0x7fff5fbff764: 0x0000000b
```

The rest of `main` is just cleanup, called the function epilogue:

```
0x0000000100000f73 <main+35>: pop %rbp
0x0000000100000f74 <main+36>: retq 
```

We `pop` the old base pointer off the stack and store it back in `%rbp` and then `retq` jumps back to our return address, which is also stored in the stack frame.

So far we've used GDB to disassemble a short C program, gone over how to read AT&T assembly syntax, and covered registers and memory address operands. We've also used GDB to verify where our local variables are stored in relation to `%rbp`. Now we're going to use our newly acquired skills to explain how static local variables work.

## Understanding static local variables

Static local variables are a very cool feature of C. In a nutshell, they are local variables that only get initialized once and persist their values across multiple calls to the function where they are defined. A simple use case for static local variables is a Python-style generator. Here's one that generates all of the natural numbers up to `INT_MAX`:

```
/* static.c */
#include <stdio.h>
int natural_generator()
{
 int a = 1;
 static int b = -1;
 b += 1;
 return a + b;
}

int main()
{
 printf("%d\n", natural_generator());
 printf("%d\n", natural_generator());
 printf("%d\n", natural_generator());

 return 0;
}
```

When compiled and run, this program prints the first three natural numbers:

```
$ CFLAGS="-g -O0" make static
cc -g -O0 static.c -o static
$ ./static
1
2
3
```

But how does this work? To understand static locals, we're going to jump into GDB and look at the assembly. I've removed the address information that GDB adds to the disassembly so that everything fits on screen:

```
$ gdb static
(gdb) break natural_generator
(gdb) run
(gdb) disassemble
Dump of assembler code for function natural_generator:
push %rbp
mov %rsp,%rbp
movl $0x1,-0x4(%rbp)
mov 0x177(%rip),%eax # 0x100001018 <natural_generator.b>
add $0x1,%eax
mov %eax,0x16c(%rip) # 0x100001018 <natural_generator.b>
mov -0x4(%rbp),%eax
add 0x163(%rip),%eax # 0x100001018 <natural_generator.b>
pop %rbp
retq 
End of assembler dump.
```

The first thing we need to do is figure out what instruction we're on. We can do that by examining the instruction pointer or program counter. The instruction pointer is a register that stores the memory address of the next instruction. On x86_64, that register is `%rip`. We can access the instruction pointer using the `$rip` variable, or alternatively we can use the architecture independent `$pc`:

```
(gdb) x/i $pc
0x100000e94 <natural_generator+4>: movl $0x1,-0x4(%rbp)
```

The instruction pointer always contains the address of the _next_ instruction to be run, which means the third instruction hasn't been run yet, but is about to be.

Because knowing the next instruction is useful, we're going to make GDB show us the next instruction every time the program stops. In GDB 7.0 or later, you can just run `set disassemble-next-line on`, which shows all the instructions that make up the next line of source, but we're using Mac OS X, which only ships with GDB 6.3, so we'll have to resort to the `display` command. `display` is like `x`, except it evaluates its expression every time our program stops:

```
(gdb) display/i $pc
1: x/i $pc 0x100000e94 <natural_generator+4>: movl $0x1,-0x4(%rbp)
```

Now GDB is set up to always show us the next instruction before showing its prompt.

We're already past the function prologue, which we covered earlier, so we'll start right at the third instruction. This corresponds to the first source line that assigns 1 to `a`. Instead of `next`, which moves to the next source line, we'll use `nexti`, which moves to the next assembly instruction. Afterwards we'll examine `%rbp - 0x4` to verify our hypothesis that `a` is stored at `%rbp - 0x4`.

```
(gdb) nexti
7 b += 1;
1: x/i $pc mov 0x177(%rip),%eax # 0x100001018 <natural_generator.b>
(gdb) x $rbp - 0x4
0x7fff5fbff78c: 0x00000001
(gdb) x &a
0x7fff5fbff78c: 0x00000001
```

They are the same, just as we expected. The next instruction is more interesting:

```
mov 0x177(%rip),%eax # 0x100001018 <natural_generator.b>
```

This is where we'd expect to find the line `static int b = -1;`, but it looks substantially different than anything we've seen before. For one thing, there's no reference to the stack frame where we'd normally expect to find local variables. There's not even a `-0x1`! Instead, we have an instruction that loads `0x100001018`, located somewhere after the instruction pointer, into `%eax`. GDB gives us a helpful comment with the result of the memory operand calculation and a hint telling us that `natural_generator.b` is stored at this address. Let's run this instruction and figure out what's going on:

```
(gdb) nexti
(gdb) p $rax
$3 = 4294967295
(gdb) p/x $rax
$5 = 0xffffffff
```

Even though the disassembly shows `%eax` as the destination, we print `$rax`, because GDB only sets up variables for full width registers.

In this situation, we need to remember that while variables have types that specify if they are signed or unsigned, registers don't, so GDB is printing the value of `%rax` unsigned. Let's try again, by casting `%rax` to a signed `int`:

```
(gdb) p (int)$rax
$11 = -1
```

It looks like we've found `b`. We can double check this by using the `x` command:

```
(gdb) x/d 0x100001018
0x100001018 <natural_generator.b>: -1
(gdb) x/d &b
0x100001018 <natural_generator.b>: -1
```

So not only is `b` stored at a low memory address outside of the stack, it's also initialized to -1 before `natural_generator` is even called. In fact, even if you disassembled the entire program, you wouldn't find any code that sets `b` to -1\. This is because the value for `b` is hardcoded in a different section of the `sample` executable, and it's loaded into memory along with all the machine code by the operating system's loader when the process is launched.

With this out of the way, things start to make more sense. After storing `b` in `%eax`, we move to the next line of source where we increment `b`. This corresponds to the next two instructions:

```
add $0x1,%eax
mov %eax,0x16c(%rip) # 0x100001018 <natural_generator.b>
```

Here we add 1 to `%eax` and store the result back into memory. Let's run these instructions and verify the result:

```
(gdb) nexti 2
(gdb) x/d &b
0x100001018 <natural_generator.b>: 0
(gdb) p (int)$rax
$15 = 0
```

The next two instructions set us up to return `a + b`:

```
mov -0x4(%rbp),%eax
add 0x163(%rip),%eax # 0x100001018 <natural_generator.b>
```

Here we load `a` into `%eax` and then add `b`. At this point, we'd expect `%eax` to be 1\. Let's verify:

```
(gdb) nexti 2
(gdb) p $rax
$16 = 1
```

`%eax` is used to store the return value from `natural_generator`, so we're all set up for the epilogue which cleans up the stack and returns:

```
pop %rbp
retq 
```

Now we understand how `b` is initialized, let's see what happens when we run `natural_generator` again:

```
(gdb) continue
Continuing.
1

Breakpoint 1, natural_generator () at static.c:5
5 int a = 1;
1: x/i $pc 0x100000e94 <natural_generator+4>: movl $0x1,-0x4(%rbp)
(gdb) x &b
0x100001018 <natural_generator.b>: 0
```

Because `b` is not stored on the stack with other local variables, it's still zero when `natural_generator` is called again. No matter how many times our generator is called, `b` will always retain its previous value. This is because it's stored outside the stack and initialized when the loader moves the program into memory, rather than by any of our machine code.

## Conclusion

We began by going over how to read assembly and how to disassemble a program with GDB. Afterwards, we covered how static local variables work, which we could not have done without disassembling our executable.

We spent a lot of time alternating between reading the assembly instructions and verifying our hypotheses in GDB. It may seem repetitive, but there's a very important reason for doing things this way: the best way to learn something abstract is to make it more concrete, and one of the best way to make something more concrete is to use tools that let you peel back layers of abstraction. The best way to to learn these tools is to force yourself to use them until they're second nature.
