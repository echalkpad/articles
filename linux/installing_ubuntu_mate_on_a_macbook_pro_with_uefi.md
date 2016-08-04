# Installing Ubuntu MATE on a MacBook Pro with UEFI

[Original URL](http://freedompenguin.com/articles/how-to/installing-ubuntu-mate-macbook-pro-uefi/)

> Back in October, I explained how you can install Ubuntu on a Mac. It was a complete guide that included everything from dual-booting with OS X down to making sure the you're able to use the...

![Ubuntu MATE 1510](http://freedompenguin.freedompenguin.netdna-cdn.com/wp-content/uploads/2016/02/mate1510-150x150.jpg)Back in October, I explained how you can install [Ubuntu on a Mac](http://freedompenguin.com/articles/just-ask-matt/old-imac-ubuntu-studio-installation/). It was a complete guide that included everything from dual-booting with OS X down to making sure the you're able to use the right tools for the initial partitioning.

In this article, we take it to the next level by doing the same with an older MacBook Pro while still using UEFI ala [rEFit](http://refit.sourceforge.net/). Note: rEFit has been depreciated and you may wish to try [rEFind](http://www.rodsbooks.com/refind/) instead. For the sake of my tests, I found rEFit did the job just fine on under OS X Mavericks.

Some have claimed it's possible to do this **_without_** UEFI, I've found that this isn't really duplicatable – at least not reliably. This will work flawlessly even as the video driver and kernel updates roll in. Also, this guide assumes you followed the previous guide and have successfully installed [Ubuntu MATE](https://ubuntu-mate.org/) (or other Ubuntu related distro) onto the MacBook Pro circa 2010 ([MacBookPro6,2](http://www.everymac.com/ultimate-mac-lookup/?search_keywords=MacBookPro6,2)).

You can determine which model of MacBook Pro you have from a bootable USB Ubuntu MATE drive by typing this into the terminal.

```
sudo dmidecode -s system-product-name
```

This command will provide you with the exact build of MacBook Pro you have. In my case, it let me know that I was running a MacBookPro6,2 unit. If you'd like to do the same using a GUI, simply boot back into OS X and go to About this Mac, then click Overview.

> **NOTE:** _Before we go one, let me be clear – this guide "should" help you to install Ubuntu MATE 15.10 onto any model MacBook Pro that has a shared Intel/NVIDIA graphics configuration. If you follow this guide carefully, you should be able to begin using the proprietary NVIDIA drivers without the usual black screen headaches._

> _You should also be aware that unlike the FOSS driver for your graphics card, the proprietary NVIDIA drivers do not appear to support automatic graphics switching. Oddly enough though, you'll still get a cooler laptop and better battery life using the proprietary NVIDIA driver. Using the prime command will absolutely undo the fix I provide here, yielding a black screen upon your next reboot._

> _In short, do not run prime-select or mess with Optimus. If you choose to use either, you do so at your own peril, as I've found it just breaks stuff (needing a re-install) on this Mac._

**Cool your MacBook Pro down**

The first order of business after installing Ubuntu onto your MacBook Pro is to get the fans working correctly. From a terminal or from Synaptic, you need to install macfanctld.

```
 sudo apt-get update && sudo apt-get upgrade -y
```

then

```
 sudo apt-get install macfanctld
```

Before the package is even installed, you should hear the fans kick on full speed and over the next few minutes, the fans will begin to calm down. This is normal and from now on, the fans will work properly.

Another element to keeping your MacBook Pro cool will be to "get off of" the default (Open Source) NVIDIA driver. While it does offer automatic switching between the Intel and NVIDIA video processing, it also runs stupid hot and wears out your battery in a hurry. This isn't really a huge selling point if you ask me. Luckily, the proprietary NVIDIA driver runs much cooler. So if you're willing to lose the automatic video card switching, you're better off with the proprietary driver. More on that in a few.

**Function keys and backlight**

Having had a bit to type on the keyboard, you might have noticed that some of the function keys aren't responding as expected. This includes the backlight controls also not working. Now folks might be inclined to install [pommed](http://manpages.ubuntu.com/manpages/utopic/man1/pommed.1.html) and use this to control the function keys. I chose to take another approach and simply append the following to my grub menu. My reasons for this range from not being dependent on an aging tool to having an easily duplicable method of getting the function keys working. Most casual users will prefer this since it's easier.

It's worth noting the following steps are merely something that won't take effect until you've installed the proprietary NVIDIA driver for your video card. However, getting this done ahead of time means not having to contend with it later on.

In a terminal, nano or whatever:

```
sudo nano /etc/default/grub
```

Change this...

```
GRUB_DEFAULT=0
#GRUB_HIDDEN_TIMEOUT=0
GRUB_HIDDEN_TIMEOUT_QUIET=true
GRUB_TIMEOUT=10
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
GRUB_CMDLINE_LINUX=""
```

to this...

```
GRUB_DEFAULT=0
#GRUB_HIDDEN_TIMEOUT=0
GRUB_HIDDEN_TIMEOUT_QUIET=true
GRUB_TIMEOUT=10
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash acpi_backlight=vendor"
GRUB_CMDLINE_LINUX=""
```

**_Don't run the update grub command just yet._** We'll be doing that a bit later.

The next step is a little trick I picked up that essentially "forces" the OS to activate certain elements of the MacBook Pro that aren't being triggered at the firmware level. Basically, it means when you boot this with Ubuntu MATE, the MacBook Pro doesn't activate your video as the PCI-E bus master. For those of us who like to actually see what is happening when using our computers, this tends to pose a problem. Lucky for you, I'm going to share the solution.

**Look up the correct identifiers**

In order to get the MacBook Pro to "activate" our video detection at boot, we need to first identify the correct graphics card and PCI-E bridge that is connected. To do this, simply open a terminal and type the following list hardware command.

```
 sudo lshw -businfo -class bridge -class display-E bus master
```

On my tested MacBook Pro that I was borrowing, the output was something like this:

```
Bus info Device Class Description
========================================================
pci@0000:00:00.0 bridge Core Processor DRAM Controller
pci@0000:00:01.0 bridge Core Processor PCI Express x16 Root Port
pci@0000:01:00.0 display GT216M [GeForce GT 330M]
pci@0000:00:02.0 display Core Processor Integrated Graphics Controller
pci@0000:00:1c.0 bridge 5 Series/3400 Series Chipset PCI Express Root Port 1
pci@0000:00:1c.1 bridge 5 Series/3400 Series Chipset PCI Express Root Port 2
pci@0000:00:1c.2 bridge 5 Series/3400 Series Chipset PCI Express Root Port 3
pci@0000:00:1c.3 bridge 5 Series/3400 Series Chipset PCI Express Root Port 4
pci@0000:00:1e.0 bridge 82801 Mobile PCI Bridge
pci@0000:00:1f.0 bridge Mobile 5 Series Chipset LPC Interface Controller
pci@0000:ff:00.0 bridge Core Processor QuickPath Architecture Generic Non-core Registers
pci@0000:ff:00.1 bridge Core Processor QuickPath Architecture System Address Decoder
pci@0000:ff:02.0 bridge Core Processor QPI Link 0
pci@0000:ff:02.1 bridge 1st Generation Core i3/5/7 Processor QPI Physical 0
pci@0000:ff:02.2 bridge 1st Generation Core i3/5/7 Processor Reserved
pci@0000:ff:02.3 bridge 1st Generation Core i3/5/7 Processor Reserved
```

In my case, the two options I needed were right there at the top. How did I know which ones I needed? Easy, I needed the NVIDIA 330M card (duh) and the PCI-E bridge that interfaces with it.

For the purpose of this workaround, you'll need the bus ids for these two entries. In my case, that's 00:01.0 and 01:00.0\. The first part of both entries (pci@0000) is not needed. Expect your bus ids to be different, don't simply copy mine...that won't cut it.

Now let's get this show on the road and make sure the NVIDIA card is activated when we reboot this Mac. We're going to create a script to do the heavy lifting for us.

In a terminal, type this to create the needed conf file – again, using nano, vim or whatever:

```
sudo nano /etc/grub.d/01_enable_vga.conf
```

Using the bus ids I retrieved previously (remember, yours will differ), the next step is to type them into the script as follows:

```
cat << EOF
setpci -s "00:01.0" 3e.b=8
setpci -s "01:00.0" 04.b=7
EOF
```

Save the file, then chmod it to be executable:

```
sudo chmod 755 /etc/grub.d/01_enable_vga.conf
```

And now we can finally update grub to reflect the backlight fix and this script as well.

```
sudo update-grub
```

Now we're ready to safely install the proprietary NVIDIA driver.

**Installing proprietary NVIDIA driver**

At this point, your screen's backlight controls and the graphics detection script are set up. The next step is to install your proprietary NVIDIA driver. Since we're running Ubuntu MATE, all you need to do is run the Software and Updates option, then select the Additional Drivers tab. You're going to want to choose the recommended option.

With the new driver install, you're ready to reboot...and cross your fingers. Assuming you did everything correctly, you should be looking at an NVIDIA splash screen as you reboot. Even if you don't see one, you're seeing a desktop environment. This means everything worked.

To make sure it's the NVIDIA card you're using, run this from a terminal:

```
glxinfo | grep "OpenGL renderer"
```

This will indicate that you're working with your NVIDIA card. However, based on this configuration, your Intel video module is also still active as demonstrated below.

```
lspci -nnk | grep -i vga -A3 | grep 'in use'
```

At the end of the day, I have no complaints with this setup. Everything just works, including suspend. When I resume from a suspend state, the wireless reconnects flawlessly and I can get back to business as well.

**Final touches**

Because we decided to use Ubuntu MATE for this exercise, some important items have already been addressed for us. Power management is running at its best thanks to [TLP](http://linrunner.de/en/tlp/tlp.html) already being installed by default. Also, you won't have the hassle of a jumping touchpad thanks to the provided "touchpad disable while typing" support.<br>
The only items I would recommend adding are applets for hardware monitoring. Speaking for myself, I added applets for the fans and the CPU. The biggest thing I like to monitor are the fans (via [libsensors](http://linux.die.net/man/3/libsensors)). Assuming you 

<span>
  <em>remembered to install macfanctld earlier</em>
</span>

, you'll be able to add the sensors applet representing the fans. The key is to add the sensors applet, then check off the sensors you want to see.

**Troubleshooting**

Somethings to double check if you have any trouble:<br>
Verify you've entered YOUR bus ids correctly.

```
cat << EOF
setpci -s "00:01.0" 3e.b=8
setpci -s "01:00.0" 04.b=7
EOF
```

Note the " are not smart quotes (Microsoft Word) or single quotes like '.

Are you running a compatible MacBook Pro? This has been tested with hardware from mid-2010\. Specifically tested with MacBookPro6,2.<br>
Did you setup and update grub correctly?

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash acpi_backlight=vendor"
```

Note the use of " and the underscore with acpi_backlight.

This approach has worked for and allows me to dual-boot the MacBook Pro. Good luck!
