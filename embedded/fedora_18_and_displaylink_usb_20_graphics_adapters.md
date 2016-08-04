# Fedora 18 and DisplayLink USB 2.0 Graphics Adapters

[Original URL](http://plugable.com/2013/01/18/fedora-18-and-displaylink-usb-2-0-graphics-adapters)

> With the recent release of Fedora 18, we have been testing the many changes and improvements to the DisplayLink software stack that have landed for this latest version of the popular open-source...

[![](http://dxg49ziwjgkgt.cloudfront.net/wp-content/uploads/2013/01/IMG_0418-520x346.jpg "IMG_0418")](http://plugable.com/2013/01/18/fedora-18-and-displaylink-usb-2-0-graphics-adapters/img_0418)With the recent release of Fedora 18, we have been testing the many changes and improvements to the DisplayLink software stack that have landed for this latest version of the popular open-source distribution. A large amount of work has gone into improving functionality and ease of use with DisplayLink USB Graphics devices. Most of these changes are not Fedora-specific, but are present in all recent versions of the Linux kernel and Xorg display server. Unfortunately, DisplayLink support is still far from perfect under Linux, but it has come a long way in recent months. What follow is an account of what we saw while testing an assortment of our USB Display Adapters and docking stations under Fedora 18.

The system we used for testing is a custom-built PC with the following specifications:<br>
Intel Core i5-3570 Ivy Bridge<br>
ASUS P8Z77-V LK Motherboard<br>
8GB DDR3 RAM

Additionally, we performed tests using both the built-in Intel Ivy Bridge Graphics(using Intel's Open Source driver) and a Nvidia GeForce GT 430(using the Nouveau Open Source graphics driver).

_Note: We have yet to perform any testing using the Nvidia proprietary driver, but results for those tests will be posted as soon as they are completed._

There are 2 primary use-cases for USB Graphics on Linux. The most common one is to connect a greater number of Monitors than your computer has ports for, the other use-case is to set-up a Multi-Seat environment in which multiple users each have access to their own, separate desktop session while sharing the same computer. Fedora currently decides automatically if the device that you have connected to your PC should work as a multi-monitor or multi-seat device. What this mean is that our docking stations are automatically assigned to a separate desktop session, and there is no easy way to make them operate in multi-monitor mode(_a how-to on switching a docking station to multi-monitor mode will be added to this blog post at a later time_). Likewise, our USB Display Adapters will automatically work as multi-monitor devices and manual configuration is required to change this behavior. With these limitations in mind, I have split the rest of this article into 2 categories.

**[![](http://dxg49ziwjgkgt.cloudfront.net/wp-content/uploads/2013/01/IMG_0427-520x346.jpg "IMG_0427")](http://plugable.com/2013/01/18/fedora-18-and-displaylink-usb-2-0-graphics-adapters/img_0427)Multi-Monitor** (UGA-125, UGA-165, UGA-2K-A, USB2-HDMI, USB-VGA-165)

The Multi-Monitor user experience has improved in many ways from previous versions of Fedora, but there are also a number of bugs that can seriously hurt a user's enjoyment of this technology. Using the Intel Ivy Bridge graphics, we were able to connect a UGA-165 and a UGA-2K-A to our system and to a DVI display, and the display was instantly recognized in System Settings->Monitors. We were then able to enable said display, and set-up its position in our virtual desktop, and just like that, we had a working secondary display running of USB.

Unfortunately, that's where the good news ended, there are a number of bugs that still plague this setup. For starters, we were only able to do this on the Intel Ivy Bridge graphics, when we attempted to do the same thing on the Nvidia card running Nouveau, all we saw was corrupted graphics on the USB display along with a severe performance slow down on the main display. Additionally, booting the system with a DisplayLink device connected will result in the primary monitor never reaching the login manager, so the DisplayLink device needs to be connected once you have made it into the desktop session. Finally, if you log out of your desktop session with a DisplayLink device still connected, the login manager will also fail to re-appear on your primary display. On the GUI/Ease of use side of things, we ran into problems with selecting the proper resolution for our display, no matter what display we connected to our USB Display Adapters we only had the option to set it to 800×600 or 1024×768.

As far as performance goes, a non-composite desktop will obviously render the best results. A full gnome-shell session was certainly usable on our setup, but moving windows around was choppy. Switching to fallback mode provided a much smoother experience. Scrolling websites was also very smooth, and video playback is varied. YouTube video played at an acceptable frame-rate, even when going full-screen(at 360p or 480p), but VLC gave some odd issues in which new video frames would stop rendering if we expanded the window past a certain size.

**[![](http://dxg49ziwjgkgt.cloudfront.net/wp-content/uploads/2013/01/IMG_0401-520x346.jpg "IMG_0401")](http://plugable.com/2013/01/18/fedora-18-and-displaylink-usb-2-0-graphics-adapters/img_0401)Multi-Seat** (UD-160-A, UD-160-M, DC-125)

Multi-seat has not changed much functionality-wise since Fedora 17, however, many bugs have been fixed. We were able to connect a few DC-125s and UD-160s to our Fedora 18 box and login managers spawned on their respective screens with no issues. A major issue we encountered on Fedora 17 has been resolved with these release. We were able to boot our PC with multiple docking stations connected and they all initialized properly, none of them were sent to that dreaded green screen we sometimes saw before. Unlike the Mutli-Monitor scenario, Multi-seat worked with no issues on both the Intel and Nvidia graphics chipsets.

The performance of gnome-shell still leaves a lot to be desired when running on these USB Graphics chips, but switching to fallback mode fixes these issues. As a stress-test we even ran a Youtube video(at 480p) on each of our thin-clients and the experience was fairly smooth across the board, scaling this is obviously CPU-constrained.

With all of that said, it is pretty clear that there are still quite a few issues surrounding DisplayLink graphics under Linux, mostly in the Multi-Monitor use-case. If you are an Open Source Kernel or User-Space developer that would like to help improve this situation, we'd like to point you to our [Plugable Open Source Hardware Samples Program](http://plugable.com/projects/plugable-open-source-hardware-samples-program "Plugable Open Source Hardware Samples Program") where you may sign up to receive free sample hardware to help on your development efforts.

## _Related_
