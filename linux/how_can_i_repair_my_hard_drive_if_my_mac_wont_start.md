# How Can I Repair My Hard Drive If My Mac Won't Start?

[Original URL](http://macs.about.com/od/faq1/f/emergencystart.htm)

> Question: How Can I Repair My Hard Drive If My Mac Won't Start?My Mac fails to start up and just ends up displaying the blue screen. Or it may log in but then fail to show the desktop....

**Question:** How Can I Repair My Hard Drive If My Mac Won't Start?

My Mac fails to start up and just ends up displaying the blue screen. Or it may log in but then fail to show the desktop. I've heard I should run Repair Disk, but I can't do that when my Mac won't start. What can I do?

**Answer:** When a Mac fails to start up normally, one of the common troubleshooting practices is to verify and repair the startup drive. A startup drive that is experiencing problems is likely to prevent your Mac from starting, so you may find yourself in a catch 22\. You need to run Disk Utility's First Aid tools, but you can't get to Disk Utility because your Mac won't start.

There are three methods of getting around this problem.

- **Booting from a different device.** This can be another hard drive that has a bootable system on it, or your OS X Install DVD, which also contains the Disk Utility tools.
- **Safe Mode.** This is a special booting method that forces your Mac to perform an automatic disk check and repair as it tries to start up.
- **Single User Mode (fsck).** This is another special startup method that allows you to run command line utilities, such as fsck, which can verify and repair hard drives.

## Boot From Alternate Device

The easiest solution by far is to boot from a different device. The three most popular options are another bootable hard drive, an emergency startup device, such as a bootable USB flash device, or a current OS X Install DVD.

To boot from another hard drive or a USB flash device, hold down the option key and start up your Mac. The OS X startup manager will appear, allowing you to select the device to boot from.

To boot from your OS X Install DVD, insert the DVD into your Mac, and then restart your Mac while holding down the letter 'c' key.

Once your Mac finishes booting, use [Disk Utility's First Aid](http://macs.about.com/od/applications/ss/firstaid.htm) feature to verify and repair your hard drive. Or if you have more serious drive issues, check out our guide to [Reviving a Hard Drive for Use With Your Mac](http://macs.about.com/od/MacTroubleshootingTips/ss/Reviving-A-Hard-Drive-For-Use-With-Your-Mac.htm).

## Boot Using Safe Mode

To start up in Safe Mode, hold down the shift key and then start your Mac. Safe Mode takes a while, so don't be alarmed when you don't see the desktop right away. While you're waiting, the operating system is verifying the directory structure of your startup volume, and repairing it, if necessary. It will also delete some of the startup caches that may also be preventing your Mac from starting successfully.

Once the desktop appears, you can access and run [Disk Utility's First Aid](http://macs.about.com/od/applications/ss/firstaid.htm) tool just as you normally would. When First Aid is finished, restart your Mac normally.

Please note that not all applications and OS X features will work when you boot into Safe Mode. You should use this startup mode only for troubleshooting and not for running day-to-day applications.

## Boot Into Single User Mode

Start up your Mac and immediately hold down the command key plus the letter 's' key (command + s). Your Mac will start up in a special environment that looks like an old-fashioned command line interface (because that's exactly what it is).

At the command line prompt, type the following:

> /sbin/fsck –fy

Press return or enter after you type the above line. Fsck will start and display status messages about your startup disk. When it finally finishes (this can take a while), you will see one of two messages. The first indicates that no problems were found.

> ** The volume xxxx appears to be OK.

The second message indicates that problems were encountered and fsck attempted to correct the errors on your hard drive.

> ***** FILE SYSTEM WAS MODIFIED *****

If you see the second message, you should repeat the fsck command again. Continue to repeat the command until you see the "volume xxx appears to be OK" message.

If you don't see the volume OK message after five or more attempts, your hard drive has serious problems that it may not be able to recover from.
