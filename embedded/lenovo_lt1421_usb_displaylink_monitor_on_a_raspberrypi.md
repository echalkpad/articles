# Lenovo LT1421 USB DisplayLink monitor on a RaspberryPi

[Original URL](https://www.igorkromin.net/index.php/2015/03/14/lenovo-lt1421-usb-displaylink-monitor-on-a-raspberrypi/)

> I started a project to build a photo frame out of a Raspberry Pi (Model A) and an old Lenovo LT1421 USB monitor that I've had laying around. There were lots of confusing posts on whether DisplayLink...

I started a project to build a photo frame out of a Raspberry Pi (Model A) and an old Lenovo LT1421 USB monitor that I've had laying around. There were lots of confusing posts on whether DisplayLink USB monitors work with a Raspberry Pi and to what lengths you have to go to make them work. The only option I had was to just try it for myself, so I did and these are the results. It's a lot easier than you'd think! Here is the result, the monitor works as expected, everything is running via the USB hub. The response times are not great, in fact the monitor would be unusable for actual work because the mouse and refresh rate lag quite badly, however for my purpose of using it as a picture frame, it was perfect.

[![IMG_1581.jpg](https://www.igorkromin.net/fp-content/images/photoframe/.thumbs/IMG_1581.jpg)](https://www.igorkromin.net/fp-content/images/photoframe/IMG_1581.jpg "IMG_1581.jpg")

So lets see how I got to this point. The first thing I did was make sure that I had a small SD card, in this case a microSD and an adapter. The boot up files do not need much space, so a 1Gb SD card is a complete overkill, but it's the smallest I could find. I then dug out an 8Gb USB key, this is where the operating system and the rest of my files would go. It's a lot quicker using USB, especially for things like building the kernel, which is required to get the DisplayLink USB monitor running.

[![IMG_1574.jpg](https://www.igorkromin.net/fp-content/images/photoframe/.thumbs/IMG_1574.jpg)](https://www.igorkromin.net/fp-content/images/photoframe/IMG_1574.jpg "IMG_1574.jpg")

With all that in my hands, I proceeded [to download Raspbian](http://www.raspberrypi.org/downloads/ "Go to http://www.raspberrypi.org/downloads/"). Once that finished, I followed [this guide](http://www.raspipress.com/2013/05/install-and-run-raspbian-from-a-usb-flash-drive/ "Go to http://www.raspipress.com/2013/05/install-and-run-raspbian-from-a-usb-flash-drive/") on how to transfer the image to the USB key and how to set up the SD card and then how to expand the file system on the USB key to fill the available space.

The next step was to compile the kernel. I followed instructions [here](http://elinux.org/Raspberry_Pi_Kernel_Compilation#1._On_the_Raspberry_Pi "Go to http://elinux.org/Raspberry_Pi_Kernel_Compilation#1._On_the_Raspberry_Pi"). I opted for compiling directly on the Pi itself, it took overnight to complete the compilation.

Before I did the compilation, I went through the _menuconfig_ and removed some things that I thought were not necessary, like all of the sound support, etc. In addition to this, and this is the most important step here, I enabled the option for _Displaylink USB Framebuffer support_. This option is found in:<br>
_Device Drivers_ -> _Graphics Support_ -> _Support for Frame buffer devices_.

I left support for HDMI in place, but strictly speaking this is something that I could have disabled too.

Now I put the kernel on to compile and went to sleep. By morning this was completed, I got the new kernel and modules installed next.

So now my new kernel was working and I confirmed that I had a new framebuffer device at _/dev/fb1_. Now it was time to configure Xorg to use the new framebuffer. I found [this page](http://plugable.com/2011/12/23/usb-graphics-and-linux "Go to http://plugable.com/2011/12/23/usb-graphics-and-linux") useful for this purpose.

I created a file named _60-plugable.conf_ in the _/usr/share/X11/xorg.conf.d_ directory with the following content:

After this I cleaned up the file system a little by deleting the compilation directory and the firmware git checkout. This cleared out around 4.9Gb of space for me, which is significant considering there is only 8Gb of total storage for this setup.

After a restart, the DisplayLink monitor worked with Xorg, perfect! I've noticed that immediately after Xorg starts there are some artefacts on the monitor like in the photo below. These quickly disappear and the usual desktop comes to life.

[![IMG_1584.jpg](https://www.igorkromin.net/fp-content/images/photoframe/.thumbs/IMG_1584.jpg)](https://www.igorkromin.net/fp-content/images/photoframe/IMG_1584.jpg "IMG_1584.jpg")

So there you go, the only steps are to rebuild the kernel with DisplayLink support and to configure Xorg to use the newly created framebuffer.
