# DisplayLink USB 2.0 Graphics Adapters on Linux – 2014 Edition

[Original URL](http://plugable.com/2014/03/06/displaylink-usb-2-0-graphics-adapters-on-linux-2014-edition)

> A little over a year ago I wrote a blog post discussing the state of USB Graphics on Linux systems, specifically, Fedora 18\. What follows is an update on the situation, looking at both Fedora 20 and...

[<embed src="http://dxg49ziwjgkgt.cloudfront.net/wp-content/uploads/2014/03/IMG_20140306_164523-150x83.jpg%20150w,%20http://plugable.com/wp-content/uploads/2014/03/IMG_20140306_164523-300x166.jpg%20300w,%20http://plugable.com/wp-content/uploads/2014/03/IMG_20140306_164523-520x288.jpg%20520w,%20http://plugable.com/wp-content/uploads/2014/03/IMG_20140306_164523-672x372.jpg%20672w" class="alignleft size-large wp-image-12075" width="474">](http://dxg49ziwjgkgt.cloudfront.net/wp-content/uploads/2014/03/IMG_20140306_164523.jpg)

A little over a year ago I wrote a [blog post](http://plugable.com/2013/01/18/fedora-18-and-displaylink-usb-2-0-graphics-adapters) discussing the state of USB Graphics on Linux systems, specifically, Fedora 18\. What follows is an update on the situation, looking at both Fedora 20 and Ubuntu 13.10, and examining how far we have come, and how far we still have to go.

**The short story**

Multi-monitor on Linux, especially with multiple graphics cards and USB graphics adapters, remains problematic. You can find many distros and configurations where it just won't work. We'd recommend staying away unless you're an advanced Linux user who is willing to play with different distros, install optional components and do hand configuration. Unfortunately, it's just not plug and play yet today, as it is on Windows.

**The long story**

That said, it is possible to get things working in limited scenarios for USB 2.0 generation DisplayLink-based adapters. We used all Plugable products in the tests for this post. Our test systems included Intel, Nvidia, and AMD primary graphics adapters. For Nvidia and AMD, we tested both the open-source and proprietary drivers.

Intel is the most compatible, providing decent results under all configurations.<br>
Nvidia graphics cards, when running the open source nouveau driver, only work in Multi-Seat mode. Attempting multi-monitor setup with a DisplayLink adapter and an Nvidia graphics card results in garbage graphics being displayed on your DisplayLink-attached monitor. The Nvidia proprietary drivers do not work under any scenario.<br>
The AMD open-source drivers work under both multi-seat and multi-monitor setups, but the performance, at least in our tests, is significantly worse than with the Intel drivers.<br>
The AMD proprietary drivers are unavailable in any easy to install package under Fedora 20, but we installed them in Ubuntu, and were unable to get any results, they simply do not work with DisplayLink graphics.

**Fedora 20**

Fedora has always provided the best support for DisplayLink graphics on Linux. We noticed some regressions on Fedora 19, but these have largely been resolved in Fedora 20\. There isn't much new to report.

You should be able to connect one of our docking stations and create a plug-and-play multi-seat setup, or connect one of our display adapters, and expand your desktop to an extra monitor using the Arandr utility, or something similar.

**Ubuntu 13.10**

Ubuntu still has several issues with DisplayLink graphics, and they do not work out of the box.

In order to enable DisplayLink adapters to work on Ubuntu, one must download the latest Mainline Kernel build from the Ubuntu Kernel PPA, install it, and then reboot with that kernel. Once this is done, DisplayLink graphics adapters will work in multi-monitor mode. Simply enable them from System Settings like you would enable any extra monitor attached to your PC.

A step-by-step guide to accomplish this will not be provided because switching away from your distro-provided kernel is something only expert users should attempt.

It should be noted that the reason this is necessary is due to the Ubuntu team making some changes to the kernel that they ship with Ubuntu, resulting in broken DisplayLink graphics support. A bug report has been submitted, [here](https://bugs.launchpad.net/ubuntu/+source/linux-lts-saucy/+bug/1287966).

[You can also keep track of the USB Graphics situation under Ubuntu in this blueprint.](https://blueprints.launchpad.net/ubuntu/+spec/desktop-q-xorg-usb-video-support)

**Performance**

Performance continues to be an issue with DisplayLink on Linux. Using a composited window manger(Gnome 3, Unity, Cinnamon, etc) will result in poor performance across all of your displays. Compositing re-renders far more pixels than non-compositing desktops. When you're just going through a GPU, you'll only notice battery loss. But when all those pixels have to get processed by the CPU and sent over USB, it's a huge hit.

Switching to a lighter-weight window manager or desktop environment (XFCE, LMDE, Mate, etc) results in a quite usable setup, provided your main display adapter is a recent Intel chip.

Unfortunately, due to the discontinuation of Gnome 3's fallback mode, that is no longer an option for improved performance.

**Other Outstanding Bugs**

Besides the issues I mentioned above, there's still a few other problems.

When rebooting your Linux computer, or simply logging out and back in, your USB-attached displays will not always come back without having to disconnect and reconnect them.

Changing the location of your DisplayLink screens in your virtual desktop can sometimes cause strange issues (like only half of the monitor rendering). Toggling the screen on and off inside of your Display management UI usually solves this.

Nvidia's and AMD's proprietary drivers are still entirely incompatible with DisplayLink graphics on Linux.

**Conclusion**

We hope this background helps. We don't recommend or support USB graphics on Linux yet, because of the problems above -- but if you do have questions, please feel free to comment below. We want to get as much information out as possible about what works and doesn't, so things can improve here. There's no reason Linux can't have the same or better multi-monitor support as any other platform in time!

If you are an Open Source Kernel or User-Space developer that would like to help improve this situation, we'd like to point you to our [Plugable Open Source Hardware Samples Program](http://plugable.com/projects/plugable-open-source-hardware-samples-program "Plugable Open Source Hardware Samples Program") where you may sign up to receive free sample hardware to help on your development efforts.

## _Related_
