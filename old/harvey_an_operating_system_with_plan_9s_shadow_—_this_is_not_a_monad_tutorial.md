# Harvey, an operating system with Plan 9's shadow -- This is not a Monad tutorial

[Original URL](https://medium.com/this-is-not-a-monad-tutorial/harvey-an-operating-system-with-plan-9-s-shadow-3081414e5f0b)

> This is not a Monad tutorial interviewed Álvaro Jurado about the development of Harvey OS. If you haven't yet played around with Plan 9, this will make you want to do so. Be sure to check future...

![](https://d262ilb51hltx0.cloudfront.net/max/800/1*tGd9NQlW18pmX4UpST2xYQ.png)

[This is not a Monad tutorial](https://medium.com/this-is-not-a-monad-tutorial) interviewed Álvaro Jurado about the development of Harvey OS. If you haven't yet played around with Plan 9, this will make you want to do so. Be sure to check future posts because we will be keeping an eye on the development of Harvey OS, Plan 9 derivates and Plan 9 userland tools on other operating systems.

![](https://d262ilb51hltx0.cloudfront.net/max/600/1*NSlnD5Bctnl5iG_Ae1mXCw.png)

**What is Harvey OS?**

Harvey's website states that its aim is to provide a modern, distributed, 64­bit operating system that extends the elegance of the original Unix model, in which all resources are represented as files and directories, to a networked environment, and allows for new ways of working.

**Why do you take Plan 9 as an inspiration to achieve that objective?**

Plan 9 was created by the creators of Unix, and incorporates many ideas that were not possible when the original Unix was created.

Unix was a time sharing operating system for minicomputers, and its basic architecture was created ten years before the Internet.

Plan 9 was designed from the start as a distributed system, with networks integral to its architecture. Plan 9 supports the idea of _location transparency_, in which resources can be accessed without regard to where they might be. One can mount resources from 9p servers anywhere into a namespace, and from that point on, they appear to be local.

This changes your life; you never need to use scp again, and you never need to think about how to get to your files. They're always where you are. The distributed system nature of Plan 9, coupled with the idea that all resources look like files and directories, provides a unique environment. If I am on a machine, and I wish to use a different machine's network stack in one of my shells, I can type '_import <hostname> /net /net'_ and from that point on that shell and its children will use the network stack of the remote host (not all shells; just the shell which did the import). This is one reason Plan 9 systems never needed NAT, for example: to get out from where you are, import a gateway router /net onto your /net, and that shell now can go outside your local network. Mount is not a privileged operation in Plan 9, so any user may use this command. You can even import devices; if you wish to grab frames from a remote machine, you import the remote frame grabber device into your namespace and then process the frames locally. Plan 9 implements true resource sharing as opposed to remote access. M. A. Padlipsky succintly defines the difference in his classic [RFC 871 -- A PERSPECTIVE ON THE ARPANET REFERENCE MODEL](https://tools.ietf.org/html/rfc871), which is a great read. Most Unix ­derived systems today implement remote access, via tools like ssh, not resource sharing.

Plan 9 is unique even today in that it is a true distributed (or "resource sharing") system, starting at the very heart of the kernel, with all resources presented to processes in a common way, i.e. as a file system namespace. With the creation of 9pcloud.net the distributed nature of Plan 9 is very appealing. It is also a very small, simple, and understandable kernel. The sloccount tool reports that all the code for all architectures is 512 Kilo Lines of Code (KLOC). The kernel builds in under 20 seconds.

To contrast, another project Ron Minnich works on, [coreboot](http://www.coreboot.org/) firm, is 1500 KLOC, and that's not a kernel by any means.

Today, we thought that a really distributed operating system like Plan 9 and its kind of architecture is really needed in our networked and, now, cloudified world. Its central concept of everything accessible as a file through distributed file servers, and the articulation of the entire system around it is very useful and simple at the same time.

**What has been developed up to now?**

We have the kernel running and most of Plan 9 classic stuff working with GCC. But, for example, we are already working to have entire system working with clang. We are working to reduce the role of the kernel in some things, for example, the console and mouse drivers, to have major possible part of the system running as a file server out of kernel space.

Plan 9 is perhaps unique in that drivers and servers have basically the same API, and hence can be moved across the kernel boundary easy. For example, the PPP driver in Plan 9 is actually a process that presents a network IP stack as a 9P server.

We are working in ANSI POSIX environment to have most of well known tools and programs that programmers or end users expects to have in a modern operating system. Things that for traditional Plan 9 would be very difficult to have. We want that Harvey can really live with rest of systems out there, doing some things better, learning how to do others, but never isolated. It's the time to "put all together".

**What do you plan to do in the near future?**

A stable cpu server, with a robust kernel and classic and new file servers in order to provide even more and better services: that's our dream. For workstations concept, a GUI experience that users are familiar with, integrating Harvey in the actual eye candy desktop world, like other systems. Out there people want to use some basic software with Plan 9, for example, one of the most popular browsers, that until now it wasn't possible in Plan 9.

**Hardware support is an issue even for pretty established operating systems like FreeBSD. How are you tackling this issue?**

With patience. We are working at this moment in it, but we have basic drivers present in the majority of x86_64 platforms. USB, AHCI, common ethernets, etc. most of the drivers come from Plan 9, but are not fully compatible with our change of toolchain or other improvements in Harvey, so it's hard sometimes to make a driver working. We have made a lot of use of Coccinnelle source to source transformat tool to help us with the changes. But gcc/clang have many features that have to be considered in this kind of work, so the changes go beyond source to source transformation. We want a very compatible x86_64 system, but hardware part represents a big work. For that we are looking for programmers who want to help us improve Harvey.

Because we can share drivers with the [Akaros](http://akaros.cs.berkeley.edu/akaros-web/news.php) operating system, we will be able to build on their work, as they can on ours.

**What do you think about** [**9front**](http://9front.org/)**,** [**node9**](https://github.com/jvburnes/node9)**,** [**9base**](http://tools.suckless.org/9base)**,** [**Glendix**](http://www.glendix.org/) **and** [**plan9port**](https://swtch.com/plan9port/)**?**

Well, plan9port is widely used, not just by Plan 9 users. Russ's work was always a great inspiration and a very useful tool. One of our core team is a regular contributor to plan9port.

I think that 9front is a very innovative Plan 9 distribution, they did many improvements in kernel and native classic toolchain, even a new cached WORM file server. Many new users come to Plan 9 world from it.

I don't know much about node9, but sounds interesting. Javascript is in my list of "todo" things.

9base from suckless organization is a set of an essential Plan 9 ported tools, I think it's distributed in some Linux distros. It is a good effort to keep classic stuff from Plan 9 and learn how to do many big things with little tools.

I don't know if Glendix project is still alive. Merge Plan 9 syscalls and a binary compatibility is something that in Harvey we already have in a different but useful way. You can compile in Linux and run in Harvey and run in Linux as well with a couple of tricks available in our mailing list.

**Have modern operating systems been in any way influenced by Plan 9?**

There many examples, here are a few:

- The CLONE_NEWFS flag in Linux was inspired by a similar flag in Plan 9.

- Ron implemented the Plan 9 fork (rfork) for FreeBSD in the mid­90s, as he needed some of those capabilties such as shared­memory­after­fork; in that even the names from Plan 9 were used (see <http://www.freebsd.org/cgi/man.cgi?query=rfork>).

- The 9p vfs in Linux was first written by Ron in 1996 and rewritten and adopted into the Linux upstream 10 years later.

- The Linux kernel has its own version of private namespaces these days; discussions with the authors of some parts of it lead us to feel that Plan 9 inspired at least parts of their ideas.

- Distributed file systems across network in many Unix­like systems is one of the concepts from Plan 9 that makes of internet the place we know and use every day. Today most of cloud models, such Amazon, are moving in a network of CPU servers concept, with its RDS, AMI's etc.

- The UTF­8 character encoding was designed by Ken Thompson and Rob Pike and was first implemented in Plan 9\. After more than 20 years, it's now the most widely used character encoding in the world.

**I have read that some users are running Plan 9 on Raspberry PI and others in Amazon EC2\. Do you run Plan 9 or any of its derivatives as a server or in a workstation?**

I tried Richard Miller's Raspberry Pi port, of course. But not Amazon EC2\. I always have had my private home server to play with it. At this moment my home network is managed by a Plan 9 machine and a Linux machine for a couple of things. Until Harvey can do all that I want it could do, of course.

**Go has been developed by many of the creators of Plan 9\. What do you think about it? Are you going to use it in Harvey?**

I like very much Go. In fact, Harvey fast build tool is written in Go. And our team is working to have Go ported to Harvey at this moment. We almost have Hello, world running. Very useful, very powerful, very simple and easy. With Go in Harvey we could do many many things without porting many programs from other systems.

**The** [**Acme**](http://acme.cat-v.org/) **editor was originally developed by Rob Pike for Plan 9\. It is an editor which encourages to use the mouse a lot. I am an Emacs and Vim user and it has been interesting for me to find that there are quite a few really experienced developers that use Acme on their daily basis. Are you using it to develop Harvey? Do you think we should give it a try?**

![](https://d262ilb51hltx0.cloudfront.net/max/600/1*DNHGzoUAazEMZpRRzkLqrA.png)

Which editor a person uses is as personal a decision as what kind of car they like, or what their favorite music is. Well, I think is a matter of taste. And all programmers have their own taste. Personally I used what I like or need at any moment. But yes, I used many times Acme, usually from inside a Plan 9 system or sometimes from plan9port. I really don't know who is using what in Harvey's core team, but everybody should give a try to Acme. Sometimes I saw myself trying to edit a menu bar in a GTK window, so definitively Acme can hook up you. At the same time, we intend to allow users to be able to run whatever the way. Emacs and vi should be working soon.

![](https://d262ilb51hltx0.cloudfront.net/max/800/1*q-RmakqnSw6qyh8gTC_izQ.jpeg)
