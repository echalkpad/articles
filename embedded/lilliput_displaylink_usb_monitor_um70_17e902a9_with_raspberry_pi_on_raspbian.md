# Lilliput DisplayLink USB Monitor UM-70 (17e9:02a9) with Raspberry Pi on raspbian

[Original URL](http://karuppuswamy.com/wordpress/2012/08/17/lilliput-displaylink-usb-monitor-um-70-17e902a9-with-raspberry-pi-on-raspbian/)

> Some times before I posted about how I got this monitor working in Ubuntu How to get Lilliput DisplayLink based USB Monitor UM-70 (17e9:02a9) working in Ubuntu Linux. This post is about...

Some times before I posted about how I got this monitor working in Ubuntu [How to get Lilliput DisplayLink based USB Monitor UM-70 (17e9:02a9) working in Ubuntu Linux](http://karuppuswamy.com/wordpress/2010/07/19/how-to-get-lilliput-displaylink-based-usb-monitor-um-70-17e902a9-working-in-ubuntu-linux/). This post is about DisplayLink's USB monitor from Lilliput working on Raspberry Pi. The configuration is as below:

Hardware: Raspberry Pi, UM-70 Lilliput monitor connected to USB Port through powered USB Hub. Powered USB hub is mandatory for this device, since the current capacity of on board USB port is not enough to drive this monitor.

Software: [raspbian](http://www.raspbian.org/), a distro derived from Debian with optimization for Raspberry Pi.

The idea is to use this as media display for home. This monitor is configured to display Slide show, a Analog Clock, a monthly Calendar, Weather forecast for 5 days and Quote of the day. Weather forecast uses home internet connection to retrieve live data.

[![Displaylink Monitor connected to Raspberry Pi through USB hub](http://karuppuswamy.com/wordpress/wp-content/uploads/2012/08/2012-08-10-00.25.08-1024x768.jpg "Displaylink Monitor connected to Raspberry Pi through USB hub")](http://karuppuswamy.com/wordpress/wp-content/uploads/2012/08/2012-08-10-00.25.08.jpg "Displaylink Monitor connected to Raspberry Pi through USB hub") Displaylink Monitor connected to Raspberry Pi through USB hub

The first step in getting this done is to compile the raspbian kernel with changed we do. [Here is a nice article on compiling your kernel for Raspberry Pi](http://mitchtech.net/raspberry-pi-kernel-compile/).

Before compiling, in **menuconfig** we include **udlfb** module, which supports DisplayLink Display. Follow "Device Drivers -> Graphics Support -> Support for Frame buffer devices" . Enable "Displaylink USB Framebuffer support" by selecting M for module or articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii for kernel built-in. Optionally if you don't want HDMI port enabled, you can unselect "BCM2708 framebuffer support". The device node for displaylink is /dev/fb0 if it is the only module you enable in framebuffer. In case you enable BCM2708 also, /dev/fb0 is allocated for that. Displaylink will get /dev/fb1\. You need to know this to configure /etc/X11/xorg.conf in following steps.

[![](http://karuppuswamy.com/wordpress/wp-content/uploads/2012/08/displaylink-module.png "displaylink-module")](http://karuppuswamy.com/wordpress/wp-content/uploads/2012/08/displaylink-module.png "displaylink-module")

Once kernel build is over, you can overwrite the kernel and modules on top of raspbian image as per build article link.

Power on Raspberry Pi, you should be able to see green screen which shows that displaylink kernel module is loaded and works fine.

Now it is time to configure X server for this new hardware. I have already done this job for QNAP NAS Server (again Debian ARM). It is basically get displaylink xorg driver compiled in Raspberry Pi (not on other build machine). [Here is the article on this](http://karuppuswamy.com/wordpress/2011/04/11/lilliput-displaylink-usb-monitor-um-70-17e902a9-with-qnap-ts-110-on-debian-linux-armel/). You may follow the instructions starting from step-3 to get X Server working.

Here is my Displaylink display working on Raspberry Pi.

[![Display Manager shown on DisplayLink 7" Monitor](http://karuppuswamy.com/wordpress/wp-content/uploads/2012/08/2012-08-10-00.24.42-1024x768.jpg "Display Manager shown on DisplayLink 7" Monitor")](http://karuppuswamy.com/wordpress/wp-content/uploads/2012/08/2012-08-10-00.24.42.jpg "Display Manager shown on DisplayLink 7" Monitor") Display Manager shown on DisplayLink 7″ Monitor
