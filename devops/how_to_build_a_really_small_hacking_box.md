# How to Build a Really Small Hacking Box

[Original URL](http://null-byte.wonderhowto.com/how-to/build-really-small-hacking-box-0167942/)

> picoFlamingo

[![picoFlamingo](http://img.wonderhowto.com/img/00/00/00/0/user-default.18x24.jpg) 

<span class="txt-size-normal txt-clr-med txt-title">picoFlamingo</span>](http://creator.wonderhowto.com/picoflamingoproject/)

 

[<span class="user-rep-small txt-size-small mgl-small txt-clr-meddark txt-bld txt-shadow-btm-med fl tooltip" title="picoFlamingo's posts have been given 48 Kudos"> 48 </span>](http://creator.wonderhowto.com/picoflamingoproject/kudos/)

 

<span class="txt-size-normal txt-clr-meddark mgl-big fl tooltip" title="1,744 Views"> 2 weeks ago </span>

Follow

![](http://img.wonderhowto.com/img/32/56/63588881255435/0/build-really-small-hacking-box.1280x600.jpg)

For some reason, when somebody want to use a small computer for security purposes, everybody comes up with a [Raspberry Pi](https://www.raspberrypi.org/) solution. It is, indeed, a pretty neat solution, but it is not the only one, and it may not always be the best choice.

I'm myself a big fan of [BeagleBoneBlack](http://beagleboard.org/BLACK). [Kali Linux](http://docs.kali.org/category/kali-on-arm) has been ported to Both, BBB and RPi. Most of the Hardkernel 0droid boards can also run Kali. Each board has its pros and cons and, at the end, the one you use is a matter of personal taste.

In this post, I'm going to talk about yet another board. It is not as well-known as the ones mentioned above, but it's pretty awesome and I really love it: [Arietta G25.](http://www.acmesystems.it/arietta)

This one has some pretty interesting features.

1. It is small. Really small. Chances are that, whatever battery you use to power it up will be a lot bigger than the device (check my setup below).

![How to Build a Really Small Hacking Box](http://img.wonderhowto.com/img/20/47/63588881220721/0/build-really-small-hacking-box.w654.jpg)

1. Power consumption is pretty low. Below [200mA](http://www.acmesystems.it/arietta_power_consumption) most of the time, so it well suited to run on batteries.

<!--  -->

1. It has a special connector to add a small Wifi adapter, providing a quite compact device (you have to solder the pins yourself though).

![How to Build a Really Small Hacking Box](http://img.wonderhowto.com/img/32/56/63588881255435/0/build-really-small-hacking-box.w654.jpg)

On the other hand, the computational power for this board is quite low. The CPU is an ARM9 @ 400MHz and you can have a maximum of 256Mb of RAM. Yep, do not try to crack passwords with this little thingy. However it is powerful enough for a bunch of other applications.

## Step 1: Installing and Bring It Up

Booting up the Arietta is quite straightforward. You have to flash an SD-Card and then power up the device. The board features a USB OTG that allows us to do quite some nifty tricks, as for instance, converting the USB interface into a network interface ([BadUSB-like attacks)](https://srlabs.de/badusb/) or simulate HID or massive storage devices.

All the details to bring up the board can be found in the [Arietta's Official Page](http://www.acmesystems.it/arietta_getting_started). Details on [How to Configure Wifi](http://www.acmesystems.it/WIFI-2) are also provided in the official web.

You can use any USB Wifi dongle, but you will need to do some soldering to attach a USB connector to the expansion header. Otherwise, you can use one of the small dongles that fit in this 5 pin connector in the middle of the board... In case you are wondering?. Yes, they do support _monitor mode_. Chipset is RaLink RT5370N chip, and this is what **iw** says:

![How to Build a Really Small Hacking Box](http://img.wonderhowto.com/img/08/02/63588881370764/0/build-really-small-hacking-box.w654.jpg)

However note that the whole set is power limited, and that includes the wifi. There is version with external antenna that should perform better, but then we are losing a bit of the small form factor.

## Step 2: Setting the System Up. Choosing OS

Setting up the board will depend on the operating system you decide to install. Two main options are possible and documented by the official page, but you are free to try something else... That's one of the Open Source wonders.

- **Option 1** is to install a standard ARM Debian Distro. This is a full fledged Debian distribution with a lot of stuff to try. You can install any software you want from the repository via _apt-get_ or recompile from sources directly on the board. Re-compiling on the board is possible but it takes a while. You've been warned.
- **Option 2** is [Buildroot](https://buildroot.org/). This is a lot lighter and the device will boot up in a few seconds. In principle, there is no package support (this is not fully true, but for the time being we can assume that), so if you want to install new software you will have to build it yourself using the builroot environment... It is a lot simpler that you may think, [but it requires a bit of reading.](https://buildroot.org/downloads/manual/manual.html#_developer_guide)

Now you can setup whatever tool you want to use on the box, as you would do on any GNU/Linux box. In worst case you may have to cross-compile the package. In best case, you can just get it from an on-line repository.

## Step 3: Powering the Device

Now, if you want to just dump this small thingy somewhere to do some monitoring (for instance), you need to power the device. If you are lucky enough to have a power source to plug your device in, good... you are done. Otherwise you will need a battery.

There are many different options to power the device from a battery:

1. You can just use one of those USB emergency batteries for mobile phones. This is the easiest and the safest.
2. You can use the same USB emergency battery but connecting it to the pins in the expansion header instead of using the USB power. The only difference with the previous option is that you have more chances to build a more compact device, and in principle your USB interface is available
3. You can use any stabilized 5V power source... For instance from a USB Hub
4. It is also possible to boot the device using a single cell LiPo battery (3.7V) directly on the 5V rail. In this case, some USB devices (those that need 5V) may not work. In principle, the small Wifi adapter provided by the same manufacturer should be find, as it works at 3.3V.
5. You can use a step-up adapter on a 3.7V battery to rise the voltage to 5V. Then you can use normal USB devices. This scenario, specially when using additional USB devices will have an impact on the battery duration.
6. Finally, you can power the board with a 3.3V source using the pins in the expansion header. In this case, the microUSB connector cannot be used. It is possible to use the USB connector for data (not power), but you need to remove a small resistor on the board.

So, these are the options. Chose whatever better suits your needs. I'm using a small external battery on the standard USB connector... The safest/simplest solution. My external battery is way bigger than the Arietta so there is no real space save on using the expansion header.

![How to Build a Really Small Hacking Box](http://img.wonderhowto.com/img/04/88/63588881502034/0/build-really-small-hacking-box.w654.jpg)

**_Be careful when the board is powered up. Unless you put some casing on, it is easy to do a short-cut and damage the board just touching it._**

FYI I haven't tried the others options yet, so I cannot provide further feedback on them. I can only say that the external USB battery solution works fine.

More details can be found [HERE](http://www.acmesystems.it/arietta_power_supply) and also in this [THREAD](https://groups.google.com/forum/#!topic/acmesystems/XXyXhRADlhU)

**_DISCLAIMER_**

_Whenever you fiddle with electronics there is a chance to fry your device. The best way to avoid the destruction of your electronics is to know what you are doing. If you do not know what you are doing, go for the standard USB battery... and do not touch the board when switched on (yes, I said it again). You may produce a short-cut._

_Anyway, I'm not responsible of any damage to your hardware as a result of trying what is described in this tutorial. Do it at your own risk :)._

## Step 4: Things to Try

Now you have your little GNU/Linux box up and running, you can try many different things.

You can install any tool you like. Just for fun, I recompile _aircrack-ng_ on the Arietta. There is nothing special about this, just follow the instructions (installing the dependencies) and compile. As said before, compiling on the board will take a while.

You can do the Mr Robot thingy as described in the great post from occupytheweb [The Hacks of Mr. Robot: How to Build a Hacking Raspberry Pi](http://null-byte.wonderhowto.com/how-to/hacks-mr-robot-build-hacking-raspberry-pi-0163143/).

Instead of netcat (which is great), you can try my [NetKitty](http://savannah.nongnu.org/projects/netkitty/). It has some [useful features](http://www.papermint-designs.com/dmo-blog/2016-01-netkitty.-updated-tutorial), and source code is less than 600 lines in case you want to take a look.

To access the device using NetKitty. You have to run this command on the Arietta

_$ nk -shell -s T,5000_

And then, from any other machine, you can either use netcat or NetKitty again:

_$ nk -c T,IP_ADRESS,5000_

Note that NetKitty, just _dups_ the standard input/output/err when the _-shell_ option is used. It is not a real terminal emulator. That basically mean that some applications may not work. That's normal.

So, this is it for my first howto. Hope it was a bit interesting.

## See Also

Remember to Give Kudos, Tweet, Like, & Share
