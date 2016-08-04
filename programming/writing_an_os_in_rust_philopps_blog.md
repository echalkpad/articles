# Writing an OS in Rust · phil-opp's blog

[Original URL](http://os.phil-opp.com/)

> A minimal x86 kernel 18 Aug 2015 This post explains how to create a minimal x86 operating system kernel. In fact, it will just boot and print OK to the screen. The following blog posts we will extend...

## [A minimal x86 kernel](http://os.phil-opp.com/multiboot-kernel.html)

<span class="post-date"> 18 Aug 2015 </span>

 This post explains how to create a minimal x86 operating system kernel. In fact, it will just boot and print `OK` to the screen. The following blog posts we will extend it using the [Rust](http://www.rust-lang.org/) programming language.

## [Entering Long Mode](http://os.phil-opp.com/entering-longmode.html)

<span class="post-date"> 25 Aug 2015 (updated on 29 Oct 2015) </span>

 In the [previous post](http://os.phil-opp.com/multiboot-kernel.html) we created a minimal multiboot kernel. It just prints `OK` and hangs. The goal is to extend it and call 64-bit [Rust](https://www.rust-lang.org/) code. But the CPU is currently in [protected mode](https://en.wikipedia.org/wiki/Protected_mode) and allows only 32-bit instructions and up to 4GiB memory. So we need to setup _Paging_ and switch to the 64-bit [long mode](https://en.wikipedia.org/wiki/Long_mode) first.

## [Setup Rust](http://os.phil-opp.com/setup-rust.html)

<span class="post-date"> 02 Sep 2015 </span>

 In the previous posts we created a [minimal Multiboot kernel](http://os.phil-opp.com/multiboot-kernel.html) and [switched to Long Mode](http://os.phil-opp.com/entering-longmode.html). Now we can finally switch to [Rust](https://www.rust-lang.org/) code. Rust is a high-level language without runtime. It allows us to not link the standard library and write bare metal code. Unfortunately the setup is not quite hassle-free yet.

## [Printing to Screen](http://os.phil-opp.com/printing-to-screen.html)

<span class="post-date"> 23 Oct 2015 </span>

 In the [previous post](http://os.phil-opp.com/setup-rust.html) we switched from assembly to [Rust](https://www.rust-lang.org/), a systems programming language that provides great safety. But so far we are using unsafe features like [raw pointers](https://doc.rust-lang.org/book/raw-pointers.html) whenever we want to print to screen. In this post we will create a Rust module that provides a safe and easy-to-use interface for the VGA text buffer. It will support Rust's [formatting macros](https://doc.rust-lang.org/std/fmt/#related-macros), too.

## [Allocating Frames](http://os.phil-opp.com/allocating-frames.html)

<span class="post-date"> 15 Nov 2015 </span>

 In this post we create an allocator that provides free physical frames for a future paging module. To get the required information about available and used memory we use the Multiboot information structure. Additionally, we improve the `panic` handler to print the corresponding message and source line.
