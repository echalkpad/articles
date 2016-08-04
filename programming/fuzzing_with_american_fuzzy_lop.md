# Fuzzing with american fuzzy lop

[Original URL](http://lwn.net/Articles/657959/)

> In September 2014 a serious security vulnerability that became known as Shellshock was found in Bash, which is the default shell in most Linux distributions. But it quickly turned out that the...

In September 2014 a [serious security vulnerability](http://lwn.net/Articles/614218/) that became known as Shellshock was found in Bash, which is the default shell in most Linux distributions. But it quickly turned out that the initial fix for Shellshock was incomplete. Various other related bugs were found only days after the publication, amongst them [two severe vulnerabilities discovered by Michał Zalewski](http://lcamtuf.blogspot.dk/2014/10/bash-bug-how-we-finally-cracked.html) from the Google security team. In the blog post, Zalewski mentioned that he had found these bugs with a fuzzing tool that he wrote, which almost nobody knew back then: [american fuzzy lop (afl)](http://lcamtuf.coredump.cx/afl/). It was the beginning of a remarkable success story.

Fuzzing is an old technique to find bugs in software. The basic idea is simple: just feed an application lots of input data with randomly introduced errors in it and see if anything happens that would indicate a bug. The easiest thing to watch for is a crash. If an application crashes from invalid input, it is often a sign of an invalid memory access--a [buffer overflow](https://en.wikipedia.org/wiki/Buffer_overflow), for example. And these often are security vulnerabilities.

## Between dumb and template-based fuzzers

In the past, most fuzzing tools fell into two categories: "dumb" fuzzers that take a valid input and only modify it randomly, and template-based fuzzing tools that are specific to a certain input data format. Both have their limitations. While dumb fuzzers can still be surprisingly successful, they will only find the most obvious bugs because they have no knowledge about the underlying data format. Template-based fuzzers can find more bugs, but creating them is a lot of work. They have to be adapted for every kind of input data they are applied to.

American fuzzy lop tries a new strategy. To use it, the first step is to recompile an application with a special compiler wrapper that adds assembly instrumentation code to the binary. Both Clang and GCC are supported. This instrumentation allows the fuzzer itself (`afl-fuzz`) to observe the code paths that a certain input file will trigger. If `afl-fuzz` finds an input sample that triggers a new code path, it uses that sample as a starting point for further fuzzing.

This strategy causes `afl-fuzz` to reach a high level of code coverage for its testing. At some point, the fuzzing process may transform an input file into one that has a certain rarely used feature enabled. The fuzzer will detect that a new code path has been triggered and the fuzzing process will create further input files that use that code path and may find bugs there. The big advantage is that the person using the fuzzing tool doesn't need to have any knowledge about this rarely used feature. The fuzzer will just find that out for itself.

## Magically creating JPEG headers

An experiment by Zalewski shows how remarkably successful this strategy can be. He used a bogus file to start the fuzzing process on the `djpeg` tool that comes with [libjpeg](http://libjpeg.sourceforge.net/). After a while the [fuzzer automatically created input files](http://lcamtuf.blogspot.com/2014/11/pulling-jpegs-out-of-thin-air.html) that contained a valid JPEG header. This does not mean that it's advisable to start a fuzzing process with bogus files. It saves time to start the fuzzing process with a valid input file. In addition, this will likely not work in all situations. For example, the fuzzer is unable to create large string sequences by chance.

American fuzzy lop has been responsible for the discovery of bugs and security vulnerabilities in many important free software package, including security-sensitive software like OpenSSL, GnuPG, and OpenSSH. Some of the vulnerabilities in the Android media framework, Stagefright, that were recently [discovered by Joshua Drake [PDF]](https://www.blackhat.com/docs/us-15/materials/us-15-Drake-Stagefright-Scary-Code-In-The-Heart-Of-Android.pdf) were attributed to it. In order to do this, Drake had to port Stagefright to Linux, since it was designed to only run on Android. More recently, two denial-of-service issues ([CVE-2015-5722](https://kb.isc.org/article/AA-01287) and [CVE-2015-5477](https://kb.isc.org/article/AA-01272/0/CVE-2015-5477%3A-An-error-in-handling-TKEY-queries-can-cause-named-to-exit-with-a-REQUIRE-assertion-failure.html)), which would allow attackers to remotely crash DNS servers using BIND, were also found by american fuzzy lop.

Apart from finding new bugs, some people have experimented to see if american fuzzy lop would have been able to find certain already known bugs that were believed to be hard to discover. I was able to [rediscover the Heartbleed bug](https://blog.hboeck.de/archives/868-How-Heartbleed-couldve-been-found.html) by fuzzing the handshake of an old, vulnerable version of OpenSSL. One notable aspect of the bug was that it involved the heartbeat extension of TLS, which is a feature that almost nobody knew about before Heartbleed hit the news. Codenomicon, the company that found Heartbleed, also used a fuzzing tool, but their fuzzer had prior knowledge of the heartbeat extension and specifically targeted it with bogus inputs. In my experiment, I used no knowledge about this specific extension. American fuzzy lop was able to generate the correct handshake packet that would contain the extension, together with the data that would trigger Heartbleed, within six hours.

Another example of a hindsight finding was a [bug in the OpenSSL function `BN_sqr()`](https://www.openssl.org/news/secadv/20150108.txt), which is used to square large numbers (bignums). With some inputs, it would produce wrong results due to an error in the carry propagation. These inputs were rare corner cases--only one out of 2^128 input numbers would trigger that bug. Testing with random inputs would never have led to the discovery of such a bug. However, Ralf-Philipp Weinmann was able to rediscover this bug with the help of american fuzzy lop. In a [talk given at the Black Hat conference](https://www.blackhat.com/us-15/briefings.html#assessing-and-exploiting-bignum-vulnerabilities) in Las Vegas, he presented a small testing application that would compare the output of the squaring with the result of multiplying a number by itself. By definition these two calculations should produce the same output, so the application would give an assertion error if the results differed. Using that test program to recognize when `BN_sqr()` failed, american fuzzy lop was able to find an input that triggered the bug within an hour.

Although interesting, these results should be interpreted with caution. It is obviously easy to find a bug if one knows where to look. But they still indicate that even bugs that seem hard to find without code analysis may be susceptible to fuzz testing.

## Easy to use and free software

Its novel fuzzing strategy is not the only reason for the success of american fuzzy lop. Two factors that likely play a major role are that the code is openly available under a free license--it uses the Apache 2 license--and that the tool is relatively easy to use. This separates it from many other tools in the security area. Lots of commercial tools only target a limited audience, because they are expensive or not available to the public at all. And IT security tools from the academic world often have the reputation of being hard to install and even harder to use without a background in the corresponding field.

Zalewski puts a high value on the usability of his tool and often implements recommendations from its users. While the first versions were very cumbersome to use, this has changed dramatically. Also, by now packages for it are available for most Linux distributions and BSD systems. A package for Mac OS X is also available. Currently, there is no version of american fuzzy lop for Windows or for Android.

To use american fuzzy lop, one first needs to recompile an application with the compiler wrapper shipped with afl (afl-gcc/afl-g++ or afl-clang/afl-clang++). The fuzzer needs a command-line tool that takes an input file. In most cases, libraries ship some small tools that allow parsing input files and should be suitable.

When compiling libraries, it's often advisable to statically link the library against the shipped tools, which will avoid having to do library preloading when running the executable. With software using the GNU autotools, this can usually be achieved with the `configure` parameter `--disable-shared`. Therefore the `configure` call should look something like this:

```
 ./configure --disable-shared CC=afl-gcc CXX=afl-g++
```

Next, the user needs one or more sample input files. It is usually advisable to create small input files; if possible, they shouldn't be larger than a few kilobytes. These need to be put into a directory. Then start the fuzzing:

```
 afl-fuzz -i [input_directory] -o [output_directory] ./[executable_to_fuzz] @@
```

The command `afl-fuzz` will replace `@@` with the fuzzed inputs. If there is no `@@` in the command line, the input will be passed via the standard input (stdin).

## AddressSanitizer finds more memory access bugs

There are many more options and variants in how to use afl. One notable feature is the ability to combine the fuzzing with the use of a compiler feature called [AddressSanitizer](https://en.wikipedia.org/wiki/AddressSanitizer) (ASan), which is part of the GCC and Clang compilers and can be enabled with the parameter `-fsanitize=address`. It adds detection code for invalid memory accesses to an executable. Many memory access bugs, such as out-of-bounds reads or use-after-free errors, often don't cause an application to crash. With normal fuzzing, they would pass by unnoticed. AddressSanitizer changes that and stops the application execution on every read or write to an invalid memory location. In american fuzzy lop, the use of AddressSanitizer can be enabled by setting the `AFL_USE_ASAN` environment variable to 1.

There are, however, some caveats. While AddressSanitizer is remarkably fast compared to other similar tools like Valgrind, it still slows down the execution significantly. It is therefore sometimes suggested to run the fuzzing process without it. The queue directory generated by `afl-fuzz` contains all the input samples it considered interesting because they triggered new code paths. These can then be manually tested with an ASan-compiled version of the software.

AddressSanitizer allocates a huge amount of virtual memory--on 64-bit systems, several terabytes. As it is only virtual memory and only small parts of it are actually used, this doesn't stop it from working. But american fuzzy lop limits the memory for its tested applications. One easy way to work around this is to disable the memory limit of `afl-fuzz` (using the parameter `-m none`). In rare cases, this could lead to system instabilities because some inputs may cause an application to use large amounts of memory. American fuzzy lop also ships a more elegant solution that limits the memory via control groups (cgroups).

## Network fuzzing is a challenge

The biggest limitation of american fuzzy lop is that it only supports file inputs. In many cases, the most interesting pieces of code from a security perspective are the parsers for networking functions. Sometimes this limitation can be worked around. In the Heartbleed experiment mentioned earlier, it was possible to create a small [tool for the OpenSSL handshake](https://github.com/hannob/selftls) that would take a raw network packet on the command line and swap it with parts of the real handshake. This was possible because the OpenSSL API allows doing a handshake, without any real network connection, just by passing buffers between different contexts. But in many other situations it is not that easy.

Some attempts have been made to intercept networking functions by preloading a library with `LD_PRELOAD` that would then use a file input and pass it as networking input to an application. One such attempt, called [Preeny](https://github.com/zardus/preeny), is publicly available. However, these attempts turned out to be relatively fragile and only work on a small fraction of real-world applications. Combining american fuzzy lop with network fuzzing in a way that works on a wide variety of applications is still an open challenge.

An active community has built a number of [extensions and additional tools](https://github.com/mirrorer/afl/blob/master/docs/sister_projects.txt) that can be used alongside american fuzzy lop. There are variants for Python, Rust, and Go, a special QEMU mode that allows fuzzing binary applications on Linux without having access to the source code, and many more. The developers of the LLVM compiler framework have implemented a [special fuzzing mode for libraries](http://blog.llvm.org/2015/04/fuzz-all-clangs.html) that borrows several ideas from american fuzzy lop.

The origin of the name american fuzzy lop--a rabbit breed--can be traced back to an earlier tool Zalewski wrote: [Bunny the Fuzzer](http://code.google.com/p/bunny-the-fuzzer/). "

<span>Bunny wasn’t particularly great, but when I decided to revisit the idea many years later, I felt that it’s appropriate to allude to that in some way</span>

", Zalewski [wrote](https://groups.google.com/forum/#!topic/afl-users/wRoLaG73rG4) in a post to the afl-users mailing list.

American fuzzy lop has helped redefine and revive the technique of fuzzing. In combination with AddressSanitizer, it is a powerful method to improve the quality of software and to find a lot of hidden and otherwise hard-to-find bugs. It has its limitations, but the biggest limitation is probably that it is not used widely enough yet. Almost every piece of software written in C or C++ that takes input from potentially dangerous sources can and should be tested using american fuzzy lop.

[The author started [The Fuzzing Project](https://fuzzing-project.org/) last year, in an effort to fuzz as many free software applications as possible. His work for The Fuzzing Project is supported by the Linux Foundation's [Core Infrastructure Initiative](https://www.coreinfrastructure.org/).]

([Log in](https://lwn.net/login?target=/Articles/657959/) to post comments)
