# Raspberry Pi as a Network Monitoring Node

[Original URL](http://www.networkworld.com/article/2225683/cisco-subnet/raspberry-pi-as-a-network-monitoring-node.html)

> The Raspberry Pi is an amazing little inexpensive Linux-based computer. It has been remarkably successful and there have been over 1 million Raspberry Pi systems sold. The Raspberry Pi can also be a...

The [Raspberry Pi](http://en.wikipedia.org/wiki/Raspberry_Pi) is an amazing little inexpensive Linux-based computer. It has been remarkably successful and there have been over [1 million](http://www.networkworld.com/news/2013/100813-raspberry-pi-274631.html) Raspberry Pi systems sold. The Raspberry Pi can also be a useful tool in conventional IT environments and can be an inexpensive way to perform remote network monitoring. In this article we cover how to get a Raspberry Pi up and going and how to configure it for network monitoring purposes. For less than $50 you can have a remotely accessible network device for testing and troubleshooting up and working in less than 30 minutes.

The [Raspberry Pi](http://www.raspberrypi.org/) was a project that grew out of the University of Cambridge's Computer Laboratory as an inexpensive computer to aid in the teaching of computer programming to students. The Raspberry Pi supports Python and Scratch (a graphical programming language). The inventors of the Raspberry Pi created the [Raspberry Pi Foundation](http://www.raspberrypi.org/about). For less than $50 you can get a fully-functional networked computer with many optional external interfaces.

**Equipment You'll Need:**

There are several items that you will need to obtain to get a fully-functional Raspberry Pi system. The most important component is to get a Raspberry Pi Model B with [512MB of RAM](http://www.networkworld.com/news/2012/101612-raspberry-pi-ram-263406.html). This is the most power currently available Pi (eventually there will be a Model C). There are many online retailers where you can acquire the Raspberry Pi base system so it is easy to buy one.

It comes with an on-board FastEthernet interface, but you can use a USB wireless adapter if you prefer. Just be sure to check the [compatibility of your peripherals](http://elinux.org/RPi_VerifiedPeripherals) first and you may need a powered USB hub.

You will need at least an 8GB (up to 32GB) Class 10 SD card flash which acts like the hard drive. This SD card will contain the file system and all the system storage.

The Raspberry Pi needs power through a micro USB cable connected to a power source. This is similar to the connector you use to charge your mobile devices so you may already have this on-hand.

It is helpful to start off with a USB keyboard and mouse. You likely already have these already. I use a [Logitech K520](http://www.logitech.com/en-us/product/wireless-combo-mk520) keyboard and Logitech M310 mouse combo kit that uses a single USB interface for both the keyboard and mouse. The Raspberry Pi only has 2 onboard USB 2.0 interfaces so to connect more USB devices you will need a powered USB hub.

It will also be nice to have a monitor with an HDMI interface. You will also need an HDMI cable. You probably already have these and don't need to buy them. Having a HDMI interface monitor is an optional nice-to-have for initial setup, but is not needed for long-term use.

You will also want a case to keep your Pi safe from electrical issues and to protect it from stresses on the connectors. You want to be careful because the connector pins 1 and 2 (closes to the end of the board nearest the SD card) contain 3.3v and 5v of electricity and you do not want to accidentally cross those. A case is always a good idea when dealing with exposed hardware and avoid the risks of electrostatic discharge ([ESD](http://en.wikipedia.org/wiki/Electrostatic_discharge)).

![Raspberry Pi](http://www.hoggnet.com/NWWPics/RPi.jpg) **Steps for Raspbian Installation:**

Now that we have all the parts required to power the Raspberry Pi and connect it to human interfaces and a network, it is time to get the OS loaded. The first step in preparing our Raspberry Pi is to download the OS software for the Raspberry Pi from the following location.

<http://www.raspberrypi.org/downloads>

Then we need to download the Win32 disk imager software to load the OS image onto the SD card. This utility can be downloaded at this location.

<http://sourceforge.net/projects/win32diskimager/>

Then we run the binary that is contained with the compressed file win32diskimager-v0.9-binary.zip

Then we proceed to use the tool to making the [SD image](http://elinux.org/RPi_Easy_SD_Card_Setup) for the Raspbian OS.

Then run the disk imager software and point it to the mounted SD card and the Raspbian disk image.

Now you can insert the SD card into the Raspberry Pi and then connect it to the micro USB power. Be sure that all the other peripherals are connected to the R-Pi at this time (keyboard, mouse, network, other USB interfaces, etc.).

Then we watch the boot up output on the HDMI screen.

The first time you boot up the Raspberry Pi it should load the Raspberry Pi Software Configuration Tool. At this point we need to use the option "Expand the Filesystem". From this Config Tool you can also change your password, enable SSH, timezone, keyboard layout, and perform other configurations.

At this point, you can change the default username and password (pi/raspberry) to something else.

If you forgot this step or need to change something in the future, you can always re-launch the Config Tool by typing "sudo raspi-config" from the shell prompt.

Then you will need to exit the Config Tool and then reboot the Raspberry Pi.

Now you should be at the command prompt or if you booted to the desktop then you can open up LXTerminal and get to the CLI.

At this point you the network interface will use DHCP and you should have Internet access with either the onboard FastEthernet interface or with a wireless USB interface. Check to see if you have network connectivity with the CLI tools ifconfig and then try to ping a host on the Internet. Once you have Internet access, then we are ready to install some software from Internet repositories.

The default user "pi" is not the root user so we need to use the Super User Do ([sudo](https://wiki.debian.org/sudo)) command before we run privileged level commands. The [apt-get](https://wiki.debian.org/apt-get) utility is the Debian utility to load sources and binaries from the software repositories.

We first need to update the repositories. sudo apt-get update

Then we need to update the software. sudo apt-get upgrade

Now we need to update our distribution, however, since this is a new installation there may not be much to upgrade. sudo apt-get dist-upgrade

At this point we are ready to proceed with installing and configuring other network utilities.

**Web Browser for Raspberry Pi:**

On Raspbian, the default web browsers are Midori, NetSurf Web Browser, and Dillo. While these are perfectly capable web browsers, you may want to install something different based on your personal preferences. If you want a more functional web browser that supports Adobe Flash you may want to explore the Firefox derivative [Iceweasel](http://en.wikipedia.org/wiki/Mozilla_Corporation_software_rebranded_by_the_Debian_project). Here are the steps to install it. sudo apt-get install iceweasel sudo apt-get install gnash sudo apt-get install browser-plugin-gnash

You can also install Chromium which is a lightweight version of the Google chrome browser. This is the command to load that browser. sudo apt-get install chromium

**Installing Network Tools:**

Now is the time to install our network tools on the R-Pi. We will want to install the Pi Store onto our system. This is a location to download games and other applications. sudo apt-get install pistore The Pi Store icon may already be on the GUI desktop.

We can install [MTR](http://www.bitwizard.nl/mtr/) for end-to-end testing. sudo apt-get install mtr Then we can run MTR to perform a detailed "traceroute" to a destination. sudo mtr [destination]

We may want to perform a packet capture. sudo apt-get install tcpdump Then we can use tcpdump to capture IPv6 packets. sudo tcpdump -i eth0 -s0 -n ip6 Or we can look at all HTTP traffic going to and from the Raspberry Pi sudo tcpdump -i eth0 -s0 port 80 You can also use [Wireshark](http://www.wireshark.org/) or [Tshark](http://www.wireshark.org/docs/man-pages/tshark.html).

We may want to install the [NetCat](http://en.wikipedia.org/wiki/Netcat) utility (the Swiss-Army knife of all network tools) sudo apt-get install netcat Set up a netcat listener on one system's IPv6 address sudo netcat -6 -l 6667 Set up the client to connect to the netcat listener on port 6667\. telnet 6667 Then we have an interactive chat session. This could be a simple method to test a firewall rule or to test a QoS policy traffic matching configuration.

We may want to do some end-to end performance testing using the [Iperf](http://en.wikipedia.org/wiki/Iperf) utility. sudo apt-get install iperf We can set up the server sudo iperf -s Then we can set up the client to connect to the server and see the estimated throughput. sudo iperf -c [server-ip]

We may want to use our favorite port scanner ([NMAP](http://insecure.org/)) for testing. sudo apt-get install nmap Then we can run NMAP toward a target and perform a simple TCP port scan. sudo nmap -sT [Destination-IP]

We can use the [NetHogs](http://nethogs.sourceforge.net/) tool to view the top-talker applications on the system. sudo apt-get install nethogs Then we can run the tool to see what is on the network. sudo nethogs

Can use the [IPTraf](http://iptraf.seul.org/) utility to get another view of the IP Traffic on the system. sudo apt-get install iptraf Then we can run iptraf to observe traffic on the specific interface. sudo iptraf

There are also many other network utilities that can be easily installed on a Raspberry Pi to monitor networks.

Over time, you may forget all the tools that you have loaded on your Pi. You can always get a listing of the applications that are installed on the R-Pi using this command: dpkg –get-selections | grep -v deinstall

**IPv6 Capabilities:**

You may be wondering how to get IPv6 working on the R-Pi. IPv6 is not enabled by default like many other modern operating systems (Microsoft Windows 7/8, Mac OS X, Linux, ...). The reasoning is that not every Raspberry Pi will need IPv6 and in order to conserve memory and CPU it is only enabled if needed. To enable IPv6 it is really easy. Just type the following command. sudo modprobe ipv6

At this point you can use ifconfig to see your IPv6 address (link-local and/or global address).

To enable IPv6 permanently we need to add IPv6 to the list of kernel loadable modules. "sudo nano /etc/modules", add the word "ipv6" on its own line at the end of the file.

Reboot the Raspberry Pi.

If you do not have native IPv6 connectivity, then you can either ask your provider for IPv6 connectivity or you can switch ISPs to one that has IPv6\. However, another alternative is to use your Raspberry Pi to connect to a [Hurricane Electric](http://www.he.net) [tunnel](http://www.linuxhome.co.uk/?p=274) using your current IPv4-only service provider.

**Remotely Accessing the Raspberry Pi:**

There are several methods to remotely access your Raspberry Pi. You can easily SSH into the system as this is enabled by default. The default username is "pi" and the default password is "raspberry".

Initially the Raspberry Pi systems use DHCP to acquire IP addresses for the eth0 interface. For easier remote accessibility you may want to enable a static IP address. To do this you need to edit the interfaces file. sudo nano /etc/network/interfaces

And make it look something like this: auto eth0 iface eth0 inet static address 192.168.1.21 netmask 255.255.255.0 gateway 192.168.1.1 iface eth0 inet6 static address 2001:db8:1111:1111::dead:beef:cafe:face netmask 64 gateway 2001:db8:1111:1111::1

Then we will need to restart the networking service to load these new settings sudo /etc/init.d/networking stop sudo /etc/init.d/networking start

Then we want to check the settings took effect with the ifconfig command and use ping and ping6 to test connectivity.

If you want to access the GUI desktop remotely then you might want to use VNC. To use VNC, we must first install the Tight VNC server on the Raspberry Pi with the following commands. sudo apt-get install tightvncserver

Then we need to launch TightVNCServer and enter the access password. tightvncserver

Create a startup file that will allow for tightvncserver to start up upon boot-up. sudo nano /etc/init.d/tightvncserver --- >% --- #! /bin/sh # /etc/init.d/tightvncserver tightvncserver under VNCUSER='pi' case "$1" in start) echo "Starting VNC Server" su $VNCUSER -c '/usr/bin/tightvncserver :1' ;; stop) echo "Stopping VNC Server" pkill Xtightvnc ;; *) echo "Usage: /etc/init.d/tightvncserver {start|stop}" exit 1 ;; esac exit 0 --- >% ---

Change the permissions on this startup file and set VNC to run by default and then reboot. sudo chmod 755 /etc/init.d/tightvncserver sudo update-rc.d tightvncserver defaults sudo reboot

Now you can use a program like [RealVNC](http://www.realvnc.com/) to connect from your computer to the Raspberry Pi.

Connect to :1 and enter the password for the VNC server.

At this point you can run the Raspberry Pi in a headless method without a monitor, keyboard, or mouse.

**Raspberry Pi Placement:**

Many organizations often use [RDP](http://en.wikipedia.org/wiki/Remote_Desktop_Protocol) to connect to a desktop computer at a remote site to test network connectivity and the end-user experience from specific locations. That can sometimes disrupt end-users. The issue is that the network and security teams do not have a low-cost way to perform testing from other connectivity perspectives in the network topology. The Raspberry Pi could be easily placed around the network environment in a headless configuration and used for remote testing. An organization could put one Pi at each of their branch sites for remote testing of end-to-end network testing.

Raspberry Pi systems could also connect to multiple networks at the same time. A single Raspberry Pi system could function as a "jump box". A Raspberry Pi could be placed on the DMZ and secured using iptables and ip6tables. A Raspberry Pi could also be used as a simple router between different networks. To get this configured, simply install a routing system and configure it accordingly. sudo apt-get install quagga sudo apt-get install radvd

There are many different uses for the Raspberry Pi and how it can be connected to networks to facilitate remote testing or gaining visibility to distant networks.

**Examples of Other Similar Systems:**

There are several commercially available examples similar to what could be accomplished with a small computer like the Raspberry Pi.

One good example is the [Pwnie Express](http://pwnieexpress.com/). This is a small network security penetration tester's tool that can be installed innocuously in

to a target's network and remotely controlled. It is also something that could be installed internally to a network for proactive internal security testing.

[PwnPi](http://pwnpi.sourceforge.net/) is an open source security tester's distribution for Raspberry Pi that can also give you a remote security testing capab

ility.

Another company called [NetBeez](http://netbeez.net/landing/) that uses Pis to simulate end-user traffic for the purposes of managing network environments.

You can also use a [FingBox](http://www.overlooksoft.com/features) account with a remote Raspberry Pi acting as a [sentinel](http://www.raspberrypi.org/phpBB3/viewtopic.php?f=49&t=50698) device.

[AppNeta](http://www.appneta.com/) has several solutions for monitoring application performance. They use a little [PathView](http://www.appneta.com/products/pathview/) microAppliance ([m20](http://www.appneta.com/blog/why-a-microappliance/)) for remote testing and generation of synthetic application traffic.

**Conclusion:**

There are more and more vendors starting to use small embedded devices for network and security testing. This is the beginning of the [Internet of Things](http://en.wikipedia.org/wiki/Internet_of_things) era. It is easy to build your own low-cost remote monitoring system. Along the way you will learn about computers and possibly programming (which may serve you well with [Software Defined Networking](http://en.wikipedia.org/wiki/Software-defined_networking)). Setting up you own little computer is easy as Pi (Pun Intended).

Scott
