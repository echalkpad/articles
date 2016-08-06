---
title: "Series: How to create your own website based on Docker (Part 3 - Installing Docker)"
url: "http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part3-docker.html"
date_saved: "07/19/2016 08:04-44"
excerpt: "This is part 3 of the series:&#xA0;How to create your own website based on Docker. If you still don't know what Docker is or what it does? Just read the official "What is docker" document! In this&hellip;"
thumbnail: [![](png/series_how_to_create_your_own_website_based_on_docker_part_3__installing_docker.png)](png/full/series_how_to_create_your_own_website_based_on_docker_part_3__installing_docker.png)
---

# Series: How to create your own website based on Docker (Part 3 - Installing Docker)

[Original URL](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part3-docker.html)

> This is part 3 of the series: How to create your own website based on Docker. If you still don't know what Docker is or what it does? Just read the official "What is docker" document! In this...

[![](http://3.bp.blogspot.com/-AoyShqyep_k/VVDH5T73l-I/AAAAAAAAros/PIrlLKiNbII/s1600/docker250.png)](http://3.bp.blogspot.com/-AoyShqyep_k/VVDH5T73l-I/AAAAAAAAros/PIrlLKiNbII/s1600/docker250.png)<br>
This is part 3 of the series: [How to create your own website based on Docker](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part1.html).

If you still don't know what Docker is or what it does? Just read the official "[What is docker](http://www.docker.com/whatisdocker/)" document!

In this part of the series, we're going to install Docker and Docker Compose - although Docker does not recommend to use Docker Compose for production use, we'll still give it a shot. If you've never heard of Docker Compose, let me tell you in a few words what it does.

Docker Compose is a tool for defining and running complex applications with Docker. With Compose, you define a multi-container application in a single file, then spin your application up in a single command which does everything that needs to be done to get it running. In short: You'll define a YAML file in which you'll specify how the Docker containers must be started and how they are linked together. Docker Compose will then start them for you and will make sure that they are started in the right order. It will also take care of naming these containers baed on your settings. But we'll get into that in one of the next parts.<br>
As I've mentioned before, Docker requires a 64-bit installation regardless of your Ubuntu version. Additionally, your kernel must be 3.10 at minimum. The latest 3.10 minor version or a newer maintained version are also acceptable.

Run the following command, which will download a shell script and will trigger the installation of Docker. When running this command, it will ask you for your password. Just provide the password that you have set for 

<span>johndoe</span>

 in [part 2 of this series](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part2-security.html).

> <span># wget -qO- <a href="https://get.docker.com/">https://get.docker.com/</a> | sh</span>

To verify that Docker has been installed correctly, just type the following to see the options that you have when running Docker:

> # sudo docker –help

Now let's install Docker Compose by typing the following:

> # curl -L <https://github.com/docker/compose/releases/download/1.2.0/docker-compose-\`uname> -s`-`uname -m` > /usr/local/bin/docker-compose<br>
> # chmod +x /usr/local/bin/docker-compose

Let's add johndoe to a new group called docker:

> # sudo usermod -aG docker johndoe

Now you should be able to run docker containers without using sudo.

In order to start our service automatically when we reboot the system, we need to add it to the default runlevel:

> <span class="s1">sudo update-rc.d docker defaults</span>

Finally we also need to setup our UFW again, so that it can work with docker, by exposing another port (remember we've done that already with the SSH port):

> #sudo ufw allow 2375/tcp

We also need to change some UFW settings to make Docker work correctly:

> # sudo vi /etc/default/ufw

Set the 

<span>DEFAULT_FORWARD_POLICY</span>

 policy to:

> DEFAULT_FORWARD_POLICY="ACCEPT"

Reload UFW to use the new setting.

> # sudo ufw reload

Now check the status of the firewall again:

> <span># sudo ufw status</span>

It should now look similar to this:

> Status: active<br>
> To Action From<br>
> – ---- ---<br>
> 2233/tcp ALLOW Anywhere<br>
> 2375/tcp ALLOW Anywhere

Now Docker as well Docker Compose are installed - [let's think about a Docker architecture!](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part4-docker-container-architecture.html) :)
