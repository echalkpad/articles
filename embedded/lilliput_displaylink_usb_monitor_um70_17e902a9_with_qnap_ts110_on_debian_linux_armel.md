# Lilliput DisplayLink USB Monitor UM-70 (17e9:02a9) with QNAP TS-110 on Debian Linux (armel)

[Original URL](http://karuppuswamy.com/wordpress/2011/04/11/lilliput-displaylink-usb-monitor-um-70-17e902a9-with-qnap-ts-110-on-debian-linux-armel/)

> Some times before I posted about how I got this monitor working in Ubuntu How to get Lilliput DisplayLink based USB Monitor UM-70 (17e9:02a9) working in Ubuntu Linux. This post is about...

Some times before I posted about how I got this monitor working in Ubuntu [How to get Lilliput DisplayLink based USB Monitor UM-70 (17e9:02a9) working in Ubuntu Linux](http://karuppuswamy.com/wordpress/2010/07/19/how-to-get-lilliput-displaylink-based-usb-monitor-um-70-17e902a9-working-in-ubuntu-linux/). This post is about DisplayLink's USB monitor from Lilliput working on QNAP TS-110 home NAS server. The configuration is as below:

Hardware: QNAP TS-110 Home NAS box, UM-70 Lilliput monitor connected to USB Port

Software: Debian GNU/Linux 6.0 (Squeeze) with kernel 2.6.32-5-kirkwood

The idea is to use this NAS server as media display for home. This monitor is configured to display Slide show, a Analog Clock, a monthly Calendar, Weather forecast for 5 days and Quote of the day. Weather forecast uses home internet connection to retrieve live data.

Here is a quick view on what I did to get this monitor working with headless NAS server.

1. Installed Debian 6.0 (Squeeze) with kernel 2.6.32-5-kirkwood.

> root@debian:~# uname -a<br>
> Linux debian 2.6.32-5-kirkwood #1 Tue Mar 8 10:56:14 UTC 2011 armv5tel GNU/Linux

1. Ensure that **udlfb** module loaded.

root@debian:~# lsmod | grep udlfb

> udlfb 15115 2<br>
> fb 38994 5 udlfb<br>
> fb_sys_fops 1041 1 udlfb<br>
> sysimgblt 1717 1 udlfb<br>
> sysfillrect 2836 1 udlfb<br>
> syscopyarea 2604 1 udlfb<br>
> usbcore 122487 6 snd_usb_audio,snd_usb_lib,usbhid,udlfb,ehci_hcd

Ensure that you get green display when the system is up.

1. Download Displaylink xorg video module (xserver-xorg-video-displaylink_0.3.orig.tar.gz) from Ubuntu repository [here](http://archive.ubuntu.com/ubuntu/pool/universe/x/xserver-xorg-video-displaylink/xserver-xorg-video-displaylink_0.3.orig.tar.gz "xserver-xorg-video-displaylink_0.3.orig.tar.gz").

2. Install the dependent packages to compile the above module.

> root@debian:~# apt-get install xinit xserver-xorg xserver-xorg-dev xfonts-base libusb-dev xorg-dev git-core build-essential linux-headers-2.6

1. Extract the downloaded module

> root@debian:~# tar zxvf xserver-xorg-video-displaylink_0.3.orig.tar.gz<br>
> root@debian:~# cd xf86-video-displaylink/

1. Edit the source code of above module for a small change

Open **_src/displaylink.c_** with your favorite editor and comment the following lines:

> //#include "xf86Resources.h"<br>
> //#include "xf86RAC.h"<br>
> // pScrn->racMemFlags = RAC_FB | RAC_COLORMAP | RAC_CURSOR | RAC_VIEWPORT;<br>
> // pScrn->racIoFlags = RAC_FB | RAC_COLORMAP | RAC_CURSOR | RAC_VIEWPORT;<br>
> // xf86CrtcScreenInit (pScreen);

1. Compile and install this xorg module

> root@debian:~# ./configure

> root@debian:~# make && make install

This should install /usr/local/lib/xorg/modules/drivers/displaylink_drv.so

1. Configure the /etc/X11/xorg.conf file as shown below:

> #################################################

> Section "ServerLayout"<br>
> Identifier "Server Layout"<br>
> Screen 0 "DisplayLinkScreen" 0 0<br>
> EndSection

> #################################################

> Section "Files"<br>
> ModulePath "/usr/lib/xorg/modules"<br>
> ModulePath "/usr/local/lib/xorg/modules"<br>
> ModulePath "/usr/local/lib/xorg/modules/drivers"<br>
> EndSection

> ############### DisplayLink Stuff ###############

> Section "Device"<br>
> Identifier "DisplayLinkDevice"<br>
> Driver "displaylink"<br>
> Option "fbdev" "/dev/fb0"<br>
> Option "DPI" "140×140"<br>
> EndSection

> Section "Monitor"<br>
> Identifier "DisplayLinkMonitor"<br>
> # DisplaySize 152 92<br>
> EndSection

> Section "Screen"<br>
> Identifier "DisplayLinkScreen"<br>
> Device "DisplayLinkDevice"<br>
> Monitor "DisplayLinkMonitor"<br>
> SubSection "Display"<br>
> Depth 16<br>
> Modes "800×480"<br>
> EndSubSection<br>
> EndSection

> #################################################

You are done! Now install gdm or kdm or use startx to start your X session and enjoy the power of linux. Here is my media display working on top of fluxbox and gdesklets.

[![](http://karuppuswamy.com/wordpress/wp-content/uploads/2011/04/Media-Display-450x337.jpg "Media-Display")](http://karuppuswamy.com/wordpress/wp-content/uploads/2011/04/Media-Display.jpg "Media-Display") Media Display

**Reference**: <http://forum.doozan.com/read.php?2,2073,3169,quote=1>
