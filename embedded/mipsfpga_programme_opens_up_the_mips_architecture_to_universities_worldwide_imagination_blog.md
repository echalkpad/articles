# MIPSfpga programme opens up the MIPS architecture to universities worldwide - Imagination Blog

[Original URL](http://blog.imgtec.com/mips-processors/mipsfpga-opens-up-the-mips-architecture-to-universities-worldwide)

> Please please please don't obfuscate it! said a famous professor when I mentioned the outline of MIPSfpga, an exciting project part of the Imagination University Programme (IUP) I was working...

_Please please please don't obfuscate it!_ said a famous professor when I mentioned the outline of **_MIPSfpga_**, an exciting project part of [the Imagination University Programme (IUP)](http://community.imgtec.com/university/) I was working on at the time.

## The journey from IP to real hardware

I'm a hardware guy by background. For me _a tool_ is a solid object, usually a board with a processor in the middle. So when I arrived in the world of IP, it took some time to get used to the idea that a tool was a piece of software. It was also a challenge to get used to the idea of teaching computer architectures – how could people get so excited by a five stage pipeline and the _purity_ of a RISC architecture?

[![How IP becomes silicon](http://blog.imgtec.com/wp-content/uploads/2015/04/How-IP-becomes-silicon.png)](http://blog.imgtec.com/wp-content/uploads/2015/04/How-IP-becomes-silicon.png)How IP becomes silicon

Then I read the wonderful book [**_Digital Design and Computer Architecture_**](http://www.amazon.com/dp/0123944244) by Dr. David Harris and Dr. Sarah Harris. Here was something I could relate to: logic gates! And they led me gently into the concept of micro-architectures. Then it began to dawn on me, _this MIPS stuff is really fundamental!_

This is the story of how the new and exciting MIPSfpga programme from Imagination became reality.

I realized that if you wanted to teach real computer architectures, there are really only three available choices today. You could choose x86, but this has metamorphosed many times and has become quite complex for university students to understand; you could pick ARM, but that's also become increasingly complex, and was arguably never true RISC in the first place. It's also fraught with difficulties because the internal architecture is a fiercely guarded secret.

That leaves MIPS.

[Invented at Stanford University](http://blog.imgtec.com/mips-processors/blog.imgtec.com/mips-processors/the-incredible-evolution-of-the-mips-architecture), it has always been _open_ to the extent that the original design was published, and an expert reading the wonderful [**_Computer Architecture: A Quantitative Approach_**](http://www.amazon.com/Computer-Architecture-Fifth-Edition-Quantitative/dp/012383872X) (written by Dr. John L. Hennessy and Dr. David A. Patterson) can understand how to build their own. Indeed, many have done: _MIPS-like_, _MIPS-compatible_ and such phrases are frequently heard in academic circles.

[![MIPS_Hennessy_Stanford_university](http://blog.imgtec.com/wp-content/uploads/2015/04/MIPS_Hennessy_Stanford_university.jpg)](http://blog.imgtec.com/wp-content/uploads/2015/04/MIPS_Hennessy_Stanford_university.jpg)

<span class="irc_su" dir="ltr">Prof. John L. Hennessy (center) and his colleagues <a href="http://www.computerhistory.org/fellowawards/hall/bios/John,Hennessy/">inspecting a MIPS chip layout</a> in 1984</span>

Imagination did (and still does) defend its intellectual property, but those that want to make their own MIPS CPU have flourished for over a decade; for example, one can find several open source MIPS simulators in the wild written by respectable universities.

However, what this highlighted to me as the non-expert observer was that we already had a thriving industry formed around [teaching MIPS as the most elegant example of a RISC processor](https://www.youtube.com/watch?v=3paiCK3dlK0), supported by many implementations. I resolved to take it to the next level.

## Presenting MIPSfpga to universities

I tried releasing a MIPS CPU to a university with almost immediate failure: _It's very complex. Can you give us training on it? Can we have a suitable hardware platform? Can you configure it for us? The design tools are very expensive, can you loan them to us?_. Conclusion: all universities want a MIPS CPU, but for many a standard off-the-shelf package is just too difficult to use.

Therefore I spent most of 2014 consulting widely: San Francisco, Houston, Claremont, Munich, Southampton, Edinburgh, London, Southampton again, Newcastle, Tokyo, Seoul, Shanghai, Beijing, Hong Kong, Pune, Hyderabad. I asked many Universities: _If we were to make a real MIPS CPU available for academic use, would it be useful to you?_ The response was unanimous: _Yes! When can we have it?_

[![Imagination University Programme - IUP (1)](http://blog.imgtec.com/wp-content/uploads/2015/03/Imagination-University-Programme-IUP-1.png)](http://blog.imgtec.com/wp-content/uploads/2015/03/Imagination-University-Programme-IUP-1.png)

The issue of obfuscation came up over and over again. Teachers explained to me why access to the _real industrial RTL_ was so valuable to the education process. To me it was like a Swiss watch – if you let people take the back off, they could see how it works, but if the back was non-removable, it would just be a black-box with three hands and a winding knob. Interesting, but not educational. Obfuscation was for those with something to hide – I was convinced we had to go the non-obfuscated route.

> You've been here for two months, and now you want to give our IP away!

The above was a memorable reaction when I first mooted my idea. But I thought _we've got to give something if we are going to help universities_. Nine months later, I secured approval for my plan from the executive board. I felt really good that day, and blessed that we have a CEO who understands my vision.

All we had to do now was to make this reality. I thought _we've done the hard part, it should be easy now_.

Have you any idea how difficult this is? I didn't!

## Picking a suitable MIPS CPUs, the software and the right tools

Firstly, which CPU to choose? Well, that was easier. I wanted [microAptiv](http://www.imgtec.com/mips/aptiv/microaptiv.asp) because it's already in silicon (e.g. [the Microchip PIC32MZ family of microcontrollers](http://blog.imgtec.com/mips-processors/new-microaptiv-based-pic32mz-32-bit-mcu-family-microchip)). It's real – students touch it every day. But what about the configuration? There are tens of options!

For this, I did what I always do and let the voice of the academic be paramount. Professor David Harris of [Harvey Mudd College](http://en.wikipedia.org/wiki/Harvey_Mudd_College) did the configuration. He made a number of variants, but as his co-author, the indefatigable Professor Sarah Harris ([University of Nevada, Las Vegas](http://en.wikipedia.org/wiki/University_of_Nevada,_Las_Vegas)) started to develop teaching materials, it became clear that the microprocessor configuration (featuring an AHB-Lite memory interface and other useful blocks) was the way to go.

[![MIPSfpga-architecture](http://blog.imgtec.com/wp-content/uploads/2015/04/MIPSfpga-architecture.png)](http://blog.imgtec.com/wp-content/uploads/2015/04/MIPSfpga-architecture.png)The internal architecture of the MIPSfpga package

But there is so much that needs to go around such a CPU: the simulator, the choice of FPGA platforms, boot code, wrappers, the debug chain, JTAG probes, the development tools, access to C libraries... and on and on. It was a long journey and required a huge team effort.

Is it _open source_? Not completely. For academic users the license is simple: you can use it as you wish, but you cannot put it into silicon. If you modify it, you must talk to us first if you wish to patent the changes. In summary, universities can go as deep as they like _under the hood_.

Now that the MIPSfpga initiative is public, let me tell you what is available and what is coming:

- The getting started package includes **_the MIPS microAptiv CPU_** and all the other elements you need to get started. This includes a detailed guide that enables you to check the CPU is running on the FPGA and that you can program and debug it. The guide gives examples for the Terasic DE2-115 (Altera FPGA) platform and the Digilent Nexys4 DDR (Xilinx FPGA) platforms, although we are sure users will port it to many other platforms.
- Soon after the launch package, we will offer **_MIPSfpga Fundamentals_** – a complete set of teaching materials using the CPU.
- Then later **_MIPSfpga Advanced_** teaching materials will take things to a deeper level.

You may be wondering: can it run Linux? The answer is yes because the CPU includes the key elements that are required (e.g. an MMU), but this will take a little longer. There are still some extra IP blocks to be configured for the FPGA and software drivers that need to be written, but we are working on it.

The very first people to get their hands on MIPSfpga will be the attendees at the first Workshops at Harvey Mudd College on May 13th and 14th (more details at [www.imgtec.com/MIPSfpga](http://www.imgtec.com/MIPSfpga)).

## Conclusion

I've been involved in the University relations business for 21 years, but nothing has given me more satisfaction than this project, because I know that we are breaking new ground and doing something of genuine educational value.

I also know for sure that the value of Patterson and Hennessy's elegantly designed MIPS RISC processor will live for another generation as the architecture of choice for those vital classes.

I'd like to thank Imagination for giving the opportunity to do something really important for education, I'd like to thank all the Universities who encouraged me to pursue this project, and I'd like to thank the team who made it possible, including people in Santa Clara, Portland and Austin in the US, in Kings Langley and Leeds in the UK, in Shanghai and in Pune.

And of course, my sincere thanks to our gurus: [Dr. Sarah Harris](https://faculty.unlv.edu/sarahharris/) and [Dr. David Harris](http://pages.hmc.edu/harris/).

I hope you enjoy going _under the hood_ with MIPSfpga. If you would you like to use MIPSfpga, you can [register your interest here](http://community.imgtec.com/university/) and we will notify you when the download package is online.

For more news and updates, follow us on Twitter ([@UniPgm](https://twitter.com/@UniPgm), [@ImaginationPR](http://twitter.com/ImaginationPR), [@MIPSGuru](http://twitter.com/MIPSGuru), [@MIPSdev](http://twitter.com/MIPSdev)), [LinkedIn](http://www.linkedin.com/company/imagination-technologies), [Facebook](https://www.facebook.com/imgtec) and [Google+](https://plus.google.com/u/0/b/108353772237821496705/108353772237821496705/posts).
