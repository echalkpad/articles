# How to use Docker in a practical way (part 1 - Introduction)

[Original URL](https://www.howtoforge.com/tutorial/how-to-use-docker-introduction/)

> Part 1: Introduction to the terminology Preface It is not an uncommon situation, for early adopters of newly introduced concepts and technologies, to be totally confused when these can fundamentally...

[![Docker](https://www.howtoforge.com/images/how-to-use-docker-introduction/pic_1.png)](https://www.howtoforge.com/images/how-to-use-docker-introduction/big/pic_1.png)

## Part 1: Introduction to the terminology

### Preface

It is not an uncommon situation, for early adopters of newly introduced concepts and technologies, to be totally confused when these can fundamentally change the ways of developing and delivering services. Especially when everybody talks about something like Docker, and how awesome and game changing it is. This confusion happens when we try things early on and rushing straight to testing them without grasping the whole concept and background of this newly introduced technology.

This is why you may have abandoned the whole trend of Linux containers, or because you read some controversial article from yea sayers and naysayers. In this first part, of a series of articles, we will try our best to clear things up and put everything on the right perspective for any developer, sysadmins, Q/A engineers or even enthusiasts who just need the right inspiration to use Linux containers and solve their special IT problems.

We will start from the beginning, with some necessary description of the historical events and concepts and then I will showcase how we can start working with Docker containers. This way, you will be able to understand "what led to the creation of containers", "what are the building blocks" and "how do they work".

### 

How did containers came up

For many years, servers were used as they are, with a single hardware configuration, containing a single operating system (OS for short), to provide thousands of websites and some early versions of enterprise software on the same machine.

To accomplish the necessary scaling, companies needed to buy new hardware and plan carefully the steps for any new implementations. In this period of time, automation was in its early days and most of the configurations were done by hand.

### Virtualization

Then virtualization came along which let people utilize the same hardware server so they could spawn multiple virtual servers for different purposes and sell them individually. This has not only, enormously reduced the costs of server hardware ownership, but also provided the means to automate and easily mange hundreds of server instances.

Virtualization, is actually assembling hardware components like CPU, RAM, Disks, Network Cards etc that are emulated by a special software. It is like building a PC, with its core components mentioned above, within an operating system and assemble them to work as if it was a real PC. This way, the virtual PC becomes a "guest" inside an actual PC which with its operating system is called a host.

[![Virtualization](https://www.howtoforge.com/images/how-to-use-docker-introduction/pic_2.JPG)](https://www.howtoforge.com/images/how-to-use-docker-introduction/big/pic_2.JPG)

Although hardware virtualization was introduced roughly 10 years ago, it got its widespread use on various types of servers by developers and sysadmins, using the KVM (Kernel-based Virtual Machine) feature that is built in the Linux Kernel since 2007\. With Intel's "VT-x" and AMD's "AMD-V" extensions that help create any number of virtualized operating systems (limited just by the hardware resources), every developer could cheaply create multiple virtual machines (VM for short) with OS's, even with different versions of them. This way they are able to test out their applications without the need to buy a whole new rig or multiple of them, depending on the OS they need.

One of the big advantages of Virtualization is the ability to create snapshots. A snapshot is the state of a virtual machine at an exact point in time. You can think of it as a "frozen" state of the virtual machine at a particular time. This way a developer or a sysadmin can create a virtual machine, install a particular OS and the tools that he needs and the create a snapshot out of the whole thing. Then he can start testing, configuring or any tasks that he needs to accomplish and then revert back to the previous snapshot instantly, at any given time.

Also Virtual Machines can be migrated from one host machine to another and continue their working state without any special configuration. This is because, the entire virtual machine is actually some huge files which are usually called images. You can think of images, like your favorite Linux distributions ISO file. As the ISO file contains all the essential components to actually run an entire OS from a live USB/DVD, the same way a virtual machine image, contains the OS and virtual hardware components.

All the above, created an entire industry of Virtual Private Server (VPS) hosting companies where the customers could instantly launch pre-configured images of servers for any purpose. These VPS providers usually have 10 or 20 snapshots of VM's on a main host server that are cloned multiple times per customer request to provide them the virtual servers that they need

### Containers

As you can imagine a Virtual machine is an entire operating system running inside a host operating system. The guest operating systems, although they are isolated, they share and utilize the hardware resources of the host machine. Some times, there are multiple guest operating systems that are running the same whole stack of a specific operating system.

[![Containers vs. Virtual Machines.](https://www.howtoforge.com/images/how-to-use-docker-introduction/pic_3.jpg)](https://www.howtoforge.com/images/how-to-use-docker-introduction/big/pic_3.jpg)

This situation, created an opportunity for the Linux kernel developers and hackers to come up with an idea that is called lightweight process virtualization. So, instead of using an entire operating system, they could cut down the "unnecessary" components of the virtual OS to create a minimal version of it. This lead to the creation of LXC (Linux Containers).

Before we dig deeper, we should mention that the lightweight process virtualization is not a new thing. Solaris has Zones, BSD has jails and there are other similar technologies like OpenVZ. The problem is that they frequently change their name or purpose when the same base concept is used on other projects. It's true that not all are the same, but the fundamental principles are pretty much the same. They all want to isolate, deploy and create a disposable way of delivering software services without the hassle of rebuilding everything and every time from the bottom up.

The LXC project differs from the aforementioned Virtual machines in that it is an operating system level virtualization environment and not a hardware virtualization environment. They both do the same thing, but LXC provides operating system-level virtualization through a virtual environment that has its own process and network space, instead of creating a full-fledged virtual machine. As a consequence, a LXC virtual OS has minimal resource requirements and it boots in a matter of seconds.

As you can see in the following image, the LXC virtual Ubuntu on the left, uses 11MB in a default installation.

[![LXC vs. Host](https://www.howtoforge.com/images/how-to-use-docker-introduction/pic_4.png)](https://www.howtoforge.com/images/how-to-use-docker-introduction/big/pic_4.png)

### Docker

As usually, things didn't stop just by removing the unnecessary parts of an OS. Various technologies came to life by pushing the boundaries of lightweight process virtualization even further. In 2013, developers of dotCloud (a company which later changed its name to Docker Inc.) introduced Docker.

Docker is an open source engine which its primary focus is to automate the deployment of applications inside software containers and the automation of operating system level virtualization on Linux. A docker container, unlike a virtual machine and lxc, does not require or include a separate operating system. Instead, it relies on the Linux kernel's functionality and uses resource isolation.

Docker containers are created from docker images (remember the snapshots). You can imagine a docker container as the live state of a web application running from an iso file. But this time the iso, which in our example is the equivalent of the docker image, contains just the application and its dependencies.

[![Docker Scheme](https://www.howtoforge.com/images/how-to-use-docker-introduction/pic_5.png)](https://www.howtoforge.com/images/how-to-use-docker-introduction/big/pic_5.png)

One great feature of docker, which we will discuss on the second part, is the docker file. A docker file is the recipe that contains all the necessary steps that are needed to create a docker image. There are literally tons of "ready to use" docker files that you can customize are use them as they are.

As you can imagine, the scope of getting from full-fledged servers, to OS virtualization and then to containers is to remove the burden of building, deploying and maintaining an entire operating system when what they only need is just the application layer.

With this introduction, we tried to present you with some fundamental events that led us to the creation of the Docker containers. I also tried to simplify some of its concepts, so that you can understand the differences between the various virtualization technologies and where they are applicable.

The [second part](https://www.howtoforge.com/tutorial/docker-how-to-use-it-in-a-practical-way-on-ubuntu/) which will get published next week, will showcase exactly how we can install and use Docker containers in a practical way, so stay tuned.

![](https://www.howtoforge.com/images/pdficon_small.png) [view as pdf](https://www.howtoforge.com/subscription/) | ![](https://www.howtoforge.com/images/print.gif) [print](https://www.howtoforge.com/subscription/)

**Share this page:**
