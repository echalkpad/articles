# Containers 101

[Original URL](http://www.developer.com/design/containers-101.html)

> About six months ago, I started to notice that there is a lot of hubbub going around in the tech-o-sphere about containers as a new way to approach virtual computing. I like exploring new technology,...

About six months ago, I started to notice that there is a lot of hubbub going around in the tech-o-sphere about containers as a new way to approach virtual computing. I like exploring new technology, so I've spent the last few months getting the ins and outs of them. Here's what I can tell you: Containers are an important technology that is not going away anytime soon. There are a lot of players in the space, and new ones enter all the time. If you are a developer in the modern world, understanding and using containers are necessary skills to have in your professional life.

So, in the spirit of moving a good idea along, I am going to share with you the basics of container technology by answering the following questions:

- What are containers?
- What's so special about containers?
- How do I use them?

My desire in answering these question is to give you the basic knowledge and understanding that you need to start using containers when making and deploying code.

Let's get started.

## What Are Containers?

- [Email Article](http://www.developer.com/e-mail/design/containers-101.html)
- [Print Article](http://www.developer.com/print/design/containers-101.html)

Container technology is a way to create a virtual environment by using an isolated process on a host computer. The isolated process, the container, has its own set of file system resources and subordinate processes. And, the container does not intrude on the host system, nor does the host system intrude on the container.

A container runs on the kernel of the host computer (see Figure 1). A component called, a _container manager_, makes it so that the container can run on the host. (You'll learn more about container managers later in this article.)

![Contain1](http://www.developer.com/imagesvr_ce/2432/Contain1.jpg)<br>
**Figure 1:** A container provides the ability to run an isolated process that has its own resources in a shared environment

## What's So Special About Containers?

To understand what makes containers so special, we need to review the concept of the virtual machine. A _virtual machine_ is a piece of software that emulates a computer. The benefit of a virtual machine is that you get a lot more efficiency out of your hardware usage. You can have a lot of virtual machines running on a single piece of hardware (see Figure 2).

![Contain2](http://www.developer.com/imagesvr_ce/9209/Contain2.gif)<br>
**Figure 2:** When you use virtual machines, resource allocation is fixed for each machine.

However, there is a drawback to virtual machine technology. Take a look at Figure 2\. You'll notice that in the illustration there are three virtual machines running on a computer that has 48 GB of RAM, a 12 core CPU, and 3 terabytes of disk storage. Each virtual machine is allocated 16 GB of RAM, 4 cores, and a terabyte of storage. So far, so good. But, here is the drawback. Regardless of whether any one machine is using all of it resources or a fraction of its resources, the hardware allocation is static. In other words, if one of the virtual machines never uses more than 1 GB of RAM, and stores only 100 MB of the file system, that machine still has dibs on 4 GB of RAM and a full terabyte of storage, **_despite the fact that the virtual machine is using only 25% of its RAM and 1% of it storage_**. This is not so good.

A container, on the other hand, does not tie up resources. When you make a container, you do not assign it RAM, processors, or storage. Rather, the container uses the RAM, CPU, and storage on the host in a shared manner. You can load as many containers on a physical machine as the machine can handle. A container will take only what it needs at the the time of need. Again, there is no fixed allocation of resources.

And, there's more. Typically, hardware that runs containers uses a light-weight version of Linux as the operating system. This light-weight host OS provides kernel level service. [CoreOS](https://coreos.com/) is one of the popular host operating systems. There are others, such as Ubuntu [Snappy](https://developer.ubuntu.com/en/snappy/start/) and Red Hat [Atomic](https://www.redhat.com/en/about/blog/os-behind-curtain).

When you create a container, you can add in an operating system of your choosing; say, you want to use Ubuntu. The Ubuntu operating system that's in the container will leverage the host kernel, but have packages that are part of Ubuntu. For example, whereas the kernel knows nothing about package management, Ubuntu will provide [apt-get](https://help.ubuntu.com/12.04/serverguide/apt-get.html). And, you can configure the container to use a special Web server, script interpreter, and so forth.... The result is that you get the environment configuration that a virtual machine provides without tying up the resources on the host system with static allocation. Also, the container will have an IP address that is visible within the host. Thus, the host has direct access to the container via its IP address (see Figure 3).

![Contain3](http://www.developer.com/imagesvr_ce/775/Contain3.jpg)<br>
**Figure 3:** A container shares host resources and leverage the host's operating system for kernel level services.

| **What About Windows?**

A constraint of containers is that you cannot mix and match operating systems. In the world of virtual machines, it is quite usual to have a Windows host support a virtual machine running Linux, or an OSX system running a Windows VM. Not so with containers because, by definition, a container uses the host kernel. Thus, you need to match up operating systems. Linux containers run on Linux hosts. You can run Windows containers on Windows Server as of [Windows Server 2016](https://msdn.microsoft.com/en-us/virtualization/windowscontainers/about/about_overview). Also, there is a [Windows version](https://docs.docker.com/installation/windows/) of the container manager, Docker.

If you do find yourself in a place where you need to run Linux containers on Windows, you can always start up a Linux virtual machine under Windows and use the Linux VM as your host system.
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## How Do I Use Them?

There are three concepts that you need to understand in order to work with containers. The first is the notion of the container manager. The second is understanding container layer architecture. The third is container registries.

### Enter the Container Manager

A container manager is a tool that you use to create a container, deploy it, and get a container running on a host. The most popular container managers in play as of this writing are [Docker](https://www.docker.com/) and [Rocket](https://coreos.com/blog/rocket/).

A container manager works this way: Say you've written a small Hello World Web application in PHP. You'll create a container configuration file in which you'll describe the PHP files you application requires. Also in the configuration file, you'll define the layers that your application requires. In this case, let's say your application needs the PHP 5.6 interpreter and you want to run under the Apache web server. You'll issue a command via the container manager to run your application. Behind the scenes, the container manager will (1) read the configuration file to determine the layers it needs to get from a registry, (2) create your container image, (3) including your apps source file, and (4) fire it up on the host machine (see Figure 4).

![Contain4](http://www.developer.com/imagesvr_ce/9879/Contain4.jpg)<br>
**Figure 4:** Configuration files, images, and registries are all part of the container ecosystem.

Simple, huh? Yet, you are probably asking, "hey, what's a layer, what's an image, what's registry, what's going on?" These are good questions that deserve to be answered. So, let's answer them.

### Understanding Layers and Images

A container is made up of layers. A layer is a container image. (Yes, the notion of a container made up of other container might be a bit confusing at first.) You can start with an operating system base layer. Then, you declare other other layers, say a Web server and a script runtime. Once you have your container defined, you'll create an image based on your defined container. An image is a template of your container that you can reuse on one or many hosts.

Figure 5 illustrates a scenario in which the host computer is running CoreOs and using Docker as the container manager. There are three containers running on the host. One container uses Debian as the base OS layer, Apache as the web server, PHP 5.5 as the script run time, and a custom PHP application for which the container has been created. Another container is for an additional customer PHP app that uses [nginx](http://wiki.nginx.org/Main) as the Web server, and PHP 5.6 as the script interpreter, all running on a RHEL base image. Finally, a third container has a JSP app running over Tomcat and Apache, on an Ubuntu base image.

![Contain5](http://www.developer.com/imagesvr_ce/9379/Contain5.jpg)<br>
**Figure 5:** A container image can define a special host as the base image, and then add layers for your web server, script run time, and your script files.

As you can see, each container is distinct. Yet, they live side by side as isolated processes leveraging the CoreOS kernel. No hardware allocation is in place. All environment resources are shared.

### Understanding Registries

Container images are stored in a registry. The concept of a registry is similar to that of a [Maven](https://maven.apache.org/) repository, for those of you familiar with the Java framework. The analogous technology in the .NET world is a [NuGet](https://www.nuget.org/) server.

There are a number of public repositories where you can store container images. Docker has [Docker Hub](https://hub.docker.com/). There are others. Also, there is a growing number of private registry providers emerging.

Using a registry is almost automagical. When you tell your container manager to run a container, it will check the configuration file to determine the layers the container requires and then check for the required layer images out on a registry, either the default registry or ones you define. The container manager will assemble the layers that your container needs from the registry and then load your container onto the host.

Using a registry allows you to have a high degree reusability with your container images. Also, using container images means that you can scale your application, as you'll see in the next section.

## Scaling Containers

Containers work really well in dynamic environments that need to scale up or down on demand. Unlike a virtual machine, which can take a long time to load into the host environment, a container loads quickly. Quick loading, coupled with the ubiquitous nature of a container registry, means that you can create highly available, load balanced environments fast.

Please take a look at Figure 6\. The diagram illustrates an application cluster environment based on containers. These days, it's quite common to have a load balancer negotiating load among many containers made from the same image. In fact, the load balancer itself can be a container. In other words, you can use a [nxginx container as the load balancer](http://nginx.org/en/docs/http/load_balancing.html) that coordinates traffic among a cluster of identical application nodes.

![Contain6](http://www.developer.com/imagesvr_ce/1115/Contain6.jpg)<br>
**Figure 6:** You can use a load balancer container to management clusters of application containers.

## Now What?

The ecosystem for containers is growing quickly. For example, there is Google's open source project, [Kubernetes](http://kubernetes.io/v1.0/docs/whatisk8s.html) that provides a platform for deploying and scaling applications containers at the enterprise level, across clusters of hosts. [etcd](https://coreos.com/etcd/docs/latest/getting-started-with-etcd.html), another open source project, allows you to share key-value information between containers. Sharing key-value information is important when you need to have containers use a common data store or cache. There's [fleet](https://coreos.com/using-coreos/clustering/), which makes sure that if you are running a lot of homogenous containers over a variety of machines and one of a machine goes down, your container will be moved to another machine. This is a very power technology in large scale, computing environments where it is very important that your containers run all the time. The list of projects and products is getting very large, very fast.

As I said at the beginning of this article, containers are not going away anytime soon. In fact, Microsoft, Google, IBM, Redhat, Amazon, and a lot of other major technology companies have teamed up to form the [Open Container Initiative](https://www.opencontainers.org/), which is formed under the [Linux Foundation](http://www.linuxfoundation.org/). Imagine that: Microsoft working with Linux heads. So, if the folks in Redmond are jumping on the container bandwagon full blast, it's only a matter of time before containers will be part of your world. So, if you haven't started working with containers, now is the time to start.

Tags: [Linux,](http://www.developer.com/tags/Linux-1870.htm) [Windows,](http://www.developer.com/tags/Windows-2470.htm) [Ubuntu,](http://www.developer.com/tags/Ubuntu-5320.htm) [virtual machine,](http://www.developer.com/tags/virtual-machine-10490.htm) [kernel,](http://www.developer.com/tags/kernel-12850.htm) [virtual environment,](http://www.developer.com/tags/virtual-environment-90180.htm) [containers,](http://www.developer.com/tags/containers-138200.htm) [CoreOS,](http://www.developer.com/tags/CoreOS-13544910.htm) [Red Hat Atomic Host,](http://www.developer.com/tags/Red-Hat-Atomic-Host-15263410.htm) [Snappy,](http://www.developer.com/tags/Snappy-15997410.htm) [Windows Server 2016,](http://www.developer.com/tags/Windows-Server-2016-18189710.htm) [container manager](http://www.developer.com/tags/container-manager-18812310.htm)

[]()
