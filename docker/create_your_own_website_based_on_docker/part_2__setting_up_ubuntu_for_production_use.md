---
title: "Series: How to create your own website based on Docker (Part 2 - Setting up Ubuntu for production use)"
url: "http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part2-security.html"
date_saved: "07/19/2016 08:04-39"
excerpt: "Let's get started This is part 2 of the series: How to create your own website based on Docker. So why do I want to create my website completely based on Docker containers? Well, that's pretty easy,&hellip;"
thumbnail: [![](png/series_how_to_create_your_own_website_based_on_docker_part_2__setting_up_ubuntu_for_production_use.png)](png/full/series_how_to_create_your_own_website_based_on_docker_part_2__setting_up_ubuntu_for_production_use.png)
---

# Series: How to create your own website based on Docker (Part 2 - Setting up Ubuntu for production use)

[Original URL](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part2-security.html)

> Let's get started This is part 2 of the series: How to create your own website based on Docker. So why do I want to create my website completely based on Docker containers? Well, that's pretty easy,...

## [![Setting up Ubuntu as docker host](http://4.bp.blogspot.com/-XLXQb0a-Bkc/VVCgpRaWHbI/AAAAAAAAroU/vGEe6BXLF-o/s200/200px-Logo-ubuntu_cof-orange-hex.svg.png)](http://4.bp.blogspot.com/-XLXQb0a-Bkc/VVCgpRaWHbI/AAAAAAAAroU/vGEe6BXLF-o/s1600/200px-Logo-ubuntu_cof-orange-hex.svg.png)

## Let's get started

This is part 2 of the series: [How to create your own website based on Docker](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part1.html).

So why do I want to create my website completely based on Docker containers? Well, that's pretty easy, because a) Docker is cool and b) Docker allows me to move my containers to new providers quickly.

Let's imagine, that a new cheap & fast cloud service is brought to the web or my hosting provider gives me a chance to move to a newer/faster hardware, then it would be cool to quickly move my whole page (including all databases, apps and other services that might be running on my "old" machine) to the new appliance.

That's exactly what I need Docker for, because then my "dockerized" apps are completely portable and can run everywhere - on a cloud, on a virtual machine or on my local computer - Linux is pretty much mandatory, though.

If you still don't know what Docker is or what it does? Just read the official "[What is docker](http://www.docker.com/whatisdocker/)" document!

## Setting up Ubuntu for production use

The big advantage of using Docker is that we don't have to spend that much time creating a production-ready server. This machine will basically only act as platform for all my containers (from now on called "Docker host") and therefore only needs the minimum security configuration. But still - later - in the docker containers - you have to take care of the application security of course - but we'll get to that later.

Although Docker is supported on Ubuntu from 13.10 & up, I'm using the latest greatest Ubuntu distribution: [Ubuntu 15.04 (Vivid Vervet)](http://releases.ubuntu.com/15.04/). It comes with the latest kernel and is therefore well-prepared for my Docker installation (remember Docker requires a 64-bit installation regardless of your Ubuntu version. Additionally, your kernel must be 3.10 at minimum).

I'm going to install everything on this (small) machine to test its performance and have the opportunity to move to a faster one once everything has been set up: [Hetzner vServer VX11 (Dual Core, 2GB of RAM)](https://www.hetzner.de/en/hosting/produkte_vserver/vx11). Let's see how this small machine performs in the real world, running nginx, nodejs and mongodb containers.

Let's start setting up the Ubuntu machine - our new Docker host.

### Add a new user to the system

First of all you need make sure that no one (besides you) can access your machine via SSH, so we need to create a new user. **Let's say our new user is called "johndoe" - please use your own user name here - but for the sake of simplicity I'll keep using johndoe as our new user in this series.**

> <span># adduser johndoe</span>

This command will ask you some questions (including your [password](http://lifehacker.com/four-methods-to-create-a-secure-password-youll-actually-1601854240)) and will then create a new user for you.

Since you want to be able to use 

<span>sudo</span>

later, you need to add root privileges to that user.

Just type the following command and an editor will appear that allows you to add the user to the:

> <span># visudo</span>

Now find the following section: _#user privilege specification_ and add the following line below the root entry:

> root ALL=(ALL:ALL) ALL<br>
> **johndoe ALL=(ALL:ALL) ALL**

Now hit _Control+O_ to save then _Control+X_ to exit Nano editor.

Before you exit, you should also change the root password - just to make sure that no one else knows it - just enter the following command and enter your new password twice:

> <span># passwd</span>

### Secure your SSH access correctly

Now that you have your user set up, you can log out from your machine (just type 

<span>exit</span>

) and log in with your new user again. Although it's not necessary, I recommend to do so, so that you can test whether your SSH login (with your new user) works as expected.

On a Unix based machine (e.g. Linux or OSX), you would connect to SSH like that:

> <span># ssh johndoe@yourmachine.com</span>

Now that we're logged in as johndoe, we will secure our SSH access. So type the following command to get into the ssh daemon configuration - I will be using [vi](http://www.cs.rit.edu/~cslab/vi.html) from now on - but you can also use nano as editor:

> <span># sudo vi /etc/ssh/sshd_config</span>

Now we will change the standard SSH port, so that port sniffers will have a hard time to guess your port - for that please change the following line (in vi just press "i" to change to insert mode):

> <span># What ports, IPs and protocols we listen for<br>Port 2233</span>

We've now changed the port from 

<span>22</span>

 to 

<span>2233</span>

. **Please write it down the port number and don't forget the value you have specified here - otherwise you won't be able to login via SSH anymore! This will not prevent hackers from trying to port scan your server, but it will prevent scripts trying to access your machine on the standard SSH port.**<br>
**** Now we'll tell SSH to **not** allow the root user to login - so we're changing the value of 

<span>PermitRootLogin</span>

 to "no"!

> AuthenticationLoginGraceTime 120<br>
> PermitRootLogin no<br>
> StrictMode yes

If you're the only one accessing this machine you can also add the following line to the end of the file:

> <span>AllowUsers johndoe</span>

Having changed that, only johndoe can access this machine via SSH now.

Now just his 

<span>ESC</span>

 and enter "

<span>:wq</span>

" to write the changes to the file and reload the SSH daemon:

> <span># sudo service sshd restart</span>

Ok... let's test our new secured SSH service. Just 

<span>exit</span>

 from the machine and log in via SSH again, but this time you'll have to specify the port:

> <span># ssh johndoe@yourmachine.com -p 2233</span>

You can also try to login as root user, but **that should not work** as we've told the daemon not to allow the root user to login:

> <span># ssh root@yourmachine.com -p 2233</span>

### Install & enable the Ubuntu firewall

Since we have now set up our SSH access we should set up our firewall now - this will make sure that you can only access the machine via port 

<span>2233 </span>

(your new SSH port):

> <span># sudo apt-get install ufw</span>

Once the firewall is installed, check its status:

> <span># sudo ufw status</span>

It will probably tell you that it's not enabled - that's ok for now. So we'll tell it to allow incoming requests to our new port:

> <span># sudo ufw allow 2233/tcp</span>

And we'll also tell it to deny all incoming and allow all outgoing requests by default:

> <span># sudo ufw default deny incoming<br># sudo ufw default allow outgoing</span>

Now let's enable the firewall:

> <span># sudo ufw enable</span>

Now check the status of the firewall again:

> <span># sudo ufw status</span>

It should now look similar to this:

> Status: active<br>
> To Action From<br>
> – ---- ---<br>
> 2233/tcp ALLOW Anywhere

**That's pretty much it - your Ubuntu machine is now secured from illegal SSH access.**

Ok... let's test our new more-secured SSH service. Just exit from the machine and log in via SSH again - again, you'll have to specify the new port:

> <span># ssh johndoe@yourmachine.com -p 2233</span>

Now you should be logged in and ready to install docker ([next part of the series](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part3-docker.html))!
