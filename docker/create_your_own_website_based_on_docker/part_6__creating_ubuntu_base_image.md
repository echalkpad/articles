---
title: "Series: How to create your own website based on Docker (Part 6 - Creating Ubuntu Base Image)"
url: "http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part6-ubuntu-docker-image.html"
date_saved: "07/19/2016 08:05-00"
excerpt: "This is part 6 of the series:&#xA0;How to create your own website based on Docker. Before we can run a Docker container, we need to create a Docker image. So why is that? Well, Docker images are&hellip;"
thumbnail: [![](png/series_how_to_create_your_own_website_based_on_docker_part_6__creating_ubuntu_base_image.png)](png/full/series_how_to_create_your_own_website_based_on_docker_part_6__creating_ubuntu_base_image.png)
---

# Series: How to create your own website based on Docker (Part 6 - Creating Ubuntu Base Image)

[Original URL](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part6-ubuntu-docker-image.html)

> This is part 6 of the series: How to create your own website based on Docker. Before we can run a Docker container, we need to create a Docker image. So why is that? Well, Docker images are...

[![](http://upload.wikimedia.org/wikipedia/commons/thumb/9/9d/Ubuntu_logo.svg/320px-Ubuntu_logo.svg.png)](http://upload.wikimedia.org/wikipedia/commons/thumb/9/9d/Ubuntu_logo.svg/320px-Ubuntu_logo.svg.png)

This is part 6 of the series: [How to create your own website based on Docker](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part1.html).

Before we can run a Docker container, we need to create a Docker image. So why is that? Well, Docker images are read-only templates to create Docker containers and these images define everything Docker needs to know to create containers (you can run several containers based on the same image).

In this part of the series, we're going to create the base image for all our other images (as they should all run on Ubuntu). This image will be a basic Ubuntu image that brings all the software/tools/drivers that is going to be needed by all images depending on it - that's why I'm calling it the "base image".

## Source code

<span>All files mentioned in this series are </span>

[available on Github](https://github.com/mastix/project-webdev-docker-demo)

<span>, so you can play around with it! :)</span>

## Using the official Ubuntu Base Image

There are two cool things that Docker provides and we will make use of:

- You can use the [Docker Hub](https://registry.hub.docker.com/search?q=library) to get already created images (you can also upload your image to Docker Hub, but we're not doing it here).
- Whatever we install/set up in our Ubuntu image,can be reused in all other containers that are built on this base image - so these settings are shared among all other containers that are based on that image.

**Important notice:** When running servers in production I do always recommend to use official images as base image, since you can't be sure if some bad guy added some malicious stuff to that image and let's be honest, it's much cooler to create the image yourself! To get a list of available (official) images, just visit the [Docker Hub Registry](https://registry.hub.docker.com/).

For our 

<span>projectwebdev</span>

 website we're using the [official Ubuntu image](https://registry.hub.docker.com/_/ubuntu/) and will add our custom software and settings. You will see that the steps we're doing now are pretty much the same every time, so I'll write them down more generic first, before we get into details:

1. Create a directory within your Docker directory (in our case that would be 

  <span>/opt/docker/ubuntu-base</span>

  ).
2. Create a Dockerfile (the template that describes the image) within that new directory (so the file would be 

  <span>/opt/docker/ubuntu-base/Dockerfile</span>

  ).
3. Create additional directories that contain either scripts or config files that should be added to the image/container.
4. Create additional directories to act as volume mounts for the containers.

As mentioned before, we're going to use the official image and will then install the software that will be needed by all containers that are based on that image. So let's create our Dockerfile (

<span>/opt/docker/ubuntu-base/Dockerfile</span>

) by adding the image to pull from the Docker Hub.

## Get the base image

If we want to use the latest available Ubuntu distribution, we can type something like this:

> # Pull base image.<br>
> FROM ubuntu:latest

In our case that would be _Ubuntu 15.04 (Vivid Vervet)_, but **I'll always recommend to specify the version number explicitly** so we don't get surprised with a new Ubuntu version, as soon as we build our images after October when _Ubuntu 15.10 (Wily Werewolf)_ is released.

> # Pull base image.<br>
> FROM ubuntu:15.04

As you can see [here](https://registry.hub.docker.com/_/ubuntu/), there are more versions of Ubuntu available, so you can also pull _Ubuntu 14.04 LTS (Trusty Thar)_ from the hub.

## Install all needed packages

Since we're working with several technologies later, it's good to have a solid software base under the hood. That's why I've installed the following packages in my base image, just to make sure they are available:

- build-essential
- curl
- git
- man
- software-properties-common
- unzip
- vim
- wget

I do always run 

<span>apt-get update</span>

 and 

<span>apt-get dist-upgrade</span>

 every time I build my images, to make sure that I'm on the latest packages, whenever I rebuild everything - I also clean up some space by deleting all unnecessary package stuff.

So the second part of our Dockerfile looks like this:

> # Install the software/packages needed for all other containers<br>
> RUN \<br>
> sed -i 's/# \(.*multiverse$\)/\1/g' /etc/apt/sources.list && \<br>
> apt-get update && \<br>
> apt-get -y upgrade && \<br>
> apt-get install -y build-essential software-properties-common curl git man unzip vim wget && \<br>
> rm -rf /var/lib/apt/lists/*

## Setup some variables and run

The next step is to set an environment variable (ENV) and a WORKDIR (sets the working directory for the following CMD instruction).

> # Set environment variables.<br>
> ENV HOME /root

> # Define working directory.<br>
> WORKDIR /root

No we're telling our image what command to run when the container is started - in our case that would be 

<span>bash</span>

:

> # Define default command.<br>
> CMD ["bash"]

That's it - that's our Dockerfile - so here it is completely:

> # Pull base image.<br>
> FROM ubuntu:15.04<br>
> # Install the software needed for all other containers<br>
> RUN \<br>
> sed -i 's/# \(.*multiverse$\)/\1/g' /etc/apt/sources.list && \<br>
> apt-get update && \<br>
> apt-get -y upgrade && \<br>
> apt-get install -y build-essential software-properties-common curl git man unzip vim wget && \<br>
> rm -rf /var/lib/apt/lists/*<br>
> # Set environment variables.<br>
> ENV HOME /root<br>
> # Define working directory.<br>
> WORKDIR /root<br>
> # Define default command.<br>
> CMD ["bash"]

Source: <https://github.com/mastix/project-webdev-docker-demo/blob/master/ubuntu-base/Dockerfile>

## Testing the Ubuntu Base Image/Container without Docker Compose

You can basically test you new container without using Docker Compose. First build you new image like this:

> # cd /opt/docker/ubuntu-base<br>
> # docker build -t projectwebdev/ubuntu-base .

Result:

> Sending build context to Docker daemon 3.584 kB<br>
> Sending build context to Docker daemon<br>
> Step 0 : FROM ubuntu:15.04<br>
> 15.04: Pulling from ubuntu<br>
> b68f8c8d2140: Pull complete<br>
> 1d57666667e5: Pull complete<br>
> a216ec781532: Pull complete<br>
> bd94ae587483: Already exists<br>
> ubuntu:15.04: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.<br>
> Digest: sha256:6994d0f1e915ff22a9b77433c19ce619eda61e5a431a7ba89230327b2f289a95<br>
> Status: Downloaded newer image for ubuntu:15.04<br>
> --> bd94ae587483<br>
> Step 1 : RUN sed -i 's/# \(.*multiverse$\)/\1/g' /etc/apt/sources.list && apt-get update && apt-get -y upgrade && apt-get install -y build-essential && apt-get install -y software-properties-common && apt-get install -y byobu curl git htop man unzip vim wget && rm -rf /var/lib/apt/lists/*<br>
> --> Running in 2d49adfb4fce<br>
> Ign <http://archive.ubuntu.com> vivid InRelease<br>
> Ign <http://archive.ubuntu.com> vivid-updates InRelease<br>
> [...]<br>
> Step 2 : ENV HOME /root<br>
> --> Running in 1923ed0e21a0<br>
> --> a5b574f11a0f<br>
> Removing intermediate container 1923ed0e21a0<br>
> Step 3 : WORKDIR /root<br>
> --> Running in 4bfbeeea1733<br>
> --> dff8fc1d0b06<br>
> Removing intermediate container 4bfbeeea1733<br>
> Step 4 : CMD bash<br>
> --> Running in 6b086dd626ac<br>
> --> cfad3f94c992<br>
> Removing intermediate container 6b086dd626ac<br>
> Successfully built cfad3f94c992

Now we can run our container and connect to 

<span>bash </span>

(remember that we have set 

<span>bash</span>

 as CMD in our Dockerfile):

> # docker run –name ubuntu-base-test -t -i projectwebdev/ubuntu-base

Here's the result - we've been able to trigger commands withing our new Ubuntu container - I've also called 

<span>env</span>

 to show you the environment variable 

<span>HOME</span>

 that we have set:

> # docker run –name ubuntu-base-test -t -i projectwebdev/ubuntu-base<br>
> root@06913990a3c8:~# env<br>
> HOSTNAME=06913990a3c8<br>
> [...]<br>
> **HOME=/root**<br>
> [...]

## Cleanup image file and docker container

Since this was just a basic test, we'll have to remove the image and the container to save some space.

> # docker rm -f ubuntu-base-test<br>
> # docker rmi -f projectwebdev/ubuntu-base

That's it! Our Ubuntu base image is up and running and can therefore be started via Docker Compose. [Let's create the mongodb container in the next part](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part7-mongodb-docker-image.html)!
