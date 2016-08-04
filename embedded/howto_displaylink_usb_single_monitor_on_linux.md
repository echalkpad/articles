# Howto: DisplayLink USB Single Monitor on Linux

[Original URL](http://plugable.com/2011/12/23/usb-graphics-and-linux)

> Unfortunately, Linux doesn't support multiple graphics adapters the way Windows does, which means you can't just plug in USB graphics adapters and expect them to extend your desktop...

Unfortunately, Linux doesn't support multiple graphics adapters the way Windows does, which means you can't just plug in USB graphics adapters and expect them to extend your desktop (the good news is [there is progress](http://www.phoronix.com/scan.php?page=news_item&px=MTAzMjM) on this support).

What is possible, however, is running a single DisplayLink adapter, or several with a Xinerama or multiseat configuration -- just as long as you don't expect to use your main GPU at the same time.

The single-display case is relatively easy to set up, and we'll cover that here.

First, make sure you're running kernel version 2.6.35 or later (Ubuntu 10.10 or later). For older kernel versions, you'll need to update udlfb and run a modified fbdev X server (not covered in this post). On these kernel versions, when you plug in your DisplayLink-based USB graphics device, you should get a green screen. This means that at the driver built into the Linux kernel is happy, healthy, and talking to the device.

Second, if you are running Unity Desktop in Ubuntu 11.04 or later, you'll need to switch back to Classic Mode so you're running straight X. Here's how on Ubuntu:

Click on the power button in the upper right corner (mine looks like a light switch) and choose the last option, System Settings. Search for Login Screen, Double-click to display, Choose Unlock and enter your password, Select Ubuntu Classic as default session.

Third, if you're running kernel versions between 2.6.35 to 3.1, enable the fb_defio option of udlfb. To do this, create or edit a file like<br>
/etc/modprobe.d/50-displaylink.conf

and add the single line

```
options udlfb fb_defio=1
```

And reboot (or run "sudo depmod -a" and unplug/replug your adapter). This will turn on defio (page fault change detection) support. This option is already enabled by default in kernels 3.2+.

Lastly, create an X config file called 60-plugable.conf (or similar) with the following contents and place it in /usr/share/X11/xorg.conf.d (on recent distros; on older distros, make this your xorg.conf):

```
Section "Device" 
 Identifier "uga" 
 driver "fbdev" 
 Option "fbdev" "/dev/fb0" 
 Option "ShadowFB" "off"
EndSection 

Section "Monitor" 
 Identifier "monitor" 
EndSection 

Section "Screen" 
 Identifier "screen" 
 Device "uga" 
 Monitor "monitor" 
EndSection 

Section "ServerLayout" 
 Identifier "default" 
 Screen 0 "screen" 0 0 
EndSection
```

Note: if your main GPU creates a /dev/fb0 even when the USB display is not attached, then your USB display is probably getting assigned to /dev/fb1\. In that case, change /dev/fb0 in the "Device" section above to /dev/fb1

Now, on reboot, you should (hopefully!) see your login come up on your DisplayLink USB attached display!

This kind of simple setup is useful for:

- Testing or playing with your USB graphics adatper on Linux.
- Embedded systems with USB but no GPU.
- As a backup method when the main GPU or its driver isn't available or working.
- Systems where a USB graphics adapter enables higher modes (up to 2048×1152) than the main GPU screen.

Please comment if you have any trouble with this single display case. See our [past posts](http://plugable.com/category/project/udlfb/) for additional information about the DisplayLink Linux kernel driver and some more involved setups.

The instructed here work on all [Plugable USB 2.0 graphics adapters](http://plugable.com/products#USB%202.0%20Graphics%20Adapters) and [Plugable USB 2.0 docking stations and thin clients](http://plugable.com/products#USB%202.0%20Multiseat%20Thin%20Clients) (and should also generally work on all DisplayLink based products).

## _Related_
