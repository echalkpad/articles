# The rEFInd Boot Manager:rEFInd and System Integrity Protection

[Original URL](http://www.rodsbooks.com/refind/sip.html)

> by Roderick W. Smith, rodsmith@rodsbooks.com Originally written: 11/8/2015; last Web page update: 1/26/2016, referencing rEFInd 0.10.2 This Web page is provided free of charge and with no annoying...

by Roderick W. Smith, [rodsmith@rodsbooks.com](mailto:rodsmith@rodsbooks.com)

Originally written: 11/8/2015; last Web page update: 1/26/2016, referencing rEFInd 0.10.2

This Web page is provided free of charge and with no annoying outside ads; however, I did take time to prepare it, and Web hosting does cost money. If you find this Web page useful, please consider making a small donation to help keep this site up and running. Thanks!

Donate $1.00 | Donate $2.50 | Donate $5.00 | Donate $10.00 | Donate $20.00 | Donate another value
------------ | ------------ | ------------ | ------------- | ------------- | --------------------
             |              |              |               |               |

This page is part of the documentation for the rEFInd boot manager. If a Web search has brought you here, you may want to start at the [main page.](http://www.rodsbooks.com/refind/index.html)

Apple's OS X 10.11 (aka _El Capitan_) includes a new feature, known as System Integrity Protection (SIP), aka "rootless" mode. This feature is causing some consternation for advanced users, because it restricts what you can do with your computer, even as `root`. This page is dedicated to this new feature, including basic information on why SIP exists, how to install rEFInd on a computer with SIP enabled, and how to use rEFInd to manage SIP. Note that if you've come here for help installing rEFInd on a Mac with SIP enabled, you can click to one of the methods in the "Contents" box to the left of this paragraph. I recommend trying Recovery mode first; but if you have reason to try another method, you can do so.

[]()

## What Is SIP?

To understand SIP, you should first know that Unix-like systems, including OS X, have traditionally provided a model of security in which ordinary users can read and write their own files (word processor documents, their own digital photos, etc.), but cannot write to system files (programs, system configuration files, etc.)--and users cannot even read some system files. This system security model has worked well for decades on traditional Unix systems, which have been administered by computer professionals and used by individuals with less experience. For administrative tasks, the `root` account is used. On Macs, this access is generally granted by the `sudo` command or by various GUI tools. Most Macs, in contrast to traditional Unix mainframes and minicomputers from the 20th century, are single-user computers that are administered by their users. Such people often lack the knowledge of the professional system administrators who have traditionally managed Unix systems; but they must still perform system administration tasks such as installing new software and configuring network settings. OS X has always provided some measure of security by requiring users to enter their passwords before performing these dangerous tasks, and by providing GUI tools to help guide users through these tasks in a way that minimizes the risk of damage.

Apple has apparently decided that these safeguards are no longer sufficient, at least for certain tasks, such as writing files to certain system directories and installing boot loaders. I won't try to speak for Apple or explain their motivations, but the result of Apple's decisions is SIP. With SIP active, as is the default, OS X 10.11 limits your ability to perform some of these administrative tasks. You can still install and remove most third-party programs, configure your network, and so on; but some critical directories can no longer be written, even as `root`, and some utilities cannot be used in certain ways, even as `root`. These restrictions impact rEFInd because one of the affected tools, a command called `bless`, is required to tell the Mac to boot rEFInd rather than to boot OS X directly.

[]()

## Installing rEFInd with SIP Enabled

The end result of SIP is that rEFInd cannot be installed under OS X 10.11 in the way described on the [Installing rEFInd](http://www.rodsbooks.com/refind/installing.html) page--at least, not without first booting into [Recovery mode,](http://www.rodsbooks.com/refind/sip.html#recovery) in which SIP restrictions are ignored; or [disabling SIP](http://www.rodsbooks.com/refind/sip.html#disable) (either temporarily or permanently). This page covers these two options in more detail, as well as a third: [Using another OS](http://www.rodsbooks.com/refind/sip.html#another) to install rEFInd.

[]()

### Using Recovery Mode

Unless you've deleted it, the Recovery HD partition should be present on your Mac as a way to perform emergency recovery operations. The nature of this tool means that SIP cannot be enabled when using it, so you can install rEFInd from a boot to this partition. The trouble is that this installation is not a full-fledged OS X system, so you may have trouble using it if you're not comfortable with such a bare-bones environment. Nontheless, it is arguably the best way to install rEFInd on a Mac that runs OS X 10.11\. To do so, follow these steps:

1. [Download the rEFInd binary `.zip` file](http://www.rodsbooks.com/refind/getting.html) and unpack it. You can unpack it on your regular hard disk or on a USB flash drive. Pay attention to where it's located, though; you'll need to find it later. Pay attention to both the name of the volume and the _complete_ path to the directory in which it's stored. (Your home directory is normally `/Users/yourname`, where `yourname` is your username. Your Desktop is normally `/Users/yourname`/Desktop.
2. Reboot the computer.
3. At the startup chime, hold down the Command+R key combination. The computer should launch into the Recovery system. This is a very bare system, with only a window providing a way to launch a handful of utilities and a menu bar. You must use the latter.
4. Select Utilities -> Terminal from the menu bar. A Terminal window should open.
5. If you unpacked rEFInd on a USB flash drive, insert it and wait for its access light (if it has one) to stop blinking.
6. Increase the size of the Terminal a bit. (This just makes its output more legible, since the next step produces long lines.)
7. Type `df -h` in the Terminal. This produces a list of partitions that are mounted. Locate the one on which you unpacked the rEFInd files. It will normally be `/Volumes/Somename`, where `Somename` is the volume's name.
8. In the Terminal, use `cd` to change to the directory where the rEFInd files you unpacked earlier are stored. For instance, on my MacBook, I would type `cd /Volumes/Macintosh\ HD/Users/rodsmith/Destkop/refind-0.10.2`. Note that if any element of this path includes a space, you must either enclose the _entire path_ in quotes or precede the space with a backslash (`\`), as in this example's `Macintosh\ HD` volume name.
9. Type `ls` to verify that `refind-install` is present in this directory.
10. Type `./refind-install` to run the installation script. It should run normally, as described on the [Installing rEFInd](http://www.rodsbooks.com/refind/installing.html) page. You can add options, if you like, as described on that page. Alternatively, you can perform a manual installation, also as described on that page.
11. Reboot.

At this point, rEFInd should come up and enable you to boot into OS X and any other OS(es) that are already installed. You should not need to perform these steps again unless OS X re-installs its own boot loader or a subsequent OS installation overrides the default boot option. You can install an updated rEFInd and it should install correctly, provided you're installing it to the EFI System Partition (ESP). The `refind-install` script may complain about a failure, but because you're overwriting one rEFInd binary with another one, it should continue to boot. (If you installed rEFInd to an HFS+ partition, though, replacing the original file will require using `bless` to tell the firmware about the change, so updating such an installation probably won't work with SIP active.)

[]()

### Disabling SIP

Another option is to disable SIP for your regular boot. This is a viable option if you're an expert who needs regular access to tools with which SIP interferes, such as low-level disk utilities. Regular users should probably avoid this option unless the preceding procedure does not work--and in that case, you should disable SIP temporarily and then re-enable it when you've finished installing rEFInd. On this page, I describe two methods of disabling SIP: [using OS X's Recovery HD system](http://www.rodsbooks.com/refind/sip.html#disable_in_osx) and [using rEFInd on CD-R or USB flash drive.](http://www.rodsbooks.com/refind/sip.html#disable_in_refind)

[]()

#### Disabling SIP with Recovery HD

You can use the Recovery HD, as in the previous procedure, to disable SIP. To do so, boot it and launch a Terminal window, as described in the previous section. Instead of locating and running the `refind-install` script, though, you should type:

```
# csrutil disable
```

This command will disable SIP for all OSes that honor this setting. (In theory, multiple versions of OS X might be installed on a single computer, and all of them that support SIP should honor the SIP settings. To the best of my knowledge, no non-Apple OS honors SIP settings, although that could change.)

Once you've typed this command, you can reboot the computer. When you return to your regular OS X installation, SIP should be disabled and rEFInd should install normally, as described on the [Installing rEFInd](http://www.rodsbooks.com/refind/installing.html) page. You will also be able to use disk partitioning tools like my [GPT fdisk,](http://www.rodsbooks.com/gdisk/) write to directories that are normally off-limits, and so on. Note that disabling SIP does _not_ disable normal Unix-style protections--you'll still need to use `sudo` (or enter your password in a GUI dialog box) to acquire `root` privileges to perform these system-administration tasks. You'll be no less safe with SIP disabled under OS X 10.11 than you would be with OS X 10.10 or earlier; you simply won't have its added protections against user error or malicious software.

If you want to re-enable SIP, you can do so in exactly the way you disabled it, except that you should type `csrutil enable` rather than `csrutil disable` in the Recovery environment.

[]()

#### Disabling SIP with rEFInd

As described later on this page, rEFInd 0.10.0 and later provide SIP control features, but they're disabled by default--except on the USB flash drive and CD-R images available from the [rEFInd downloads page.](http://www.rodsbooks.com/refind/getting.html) On these images, the SIP control features are enabled, and can toggle between the two main modes you can set via `csrutil enable` and `csrutil disable` in the Recovery HD system. Thus, to disable SIP to install rEFInd, you can:

1. Download the USB flash drive or CD-R version of rEFInd, as suitable for your computer.
2. Prepare a boot medium. With the CD-R image, you can use your favorite disc-burning software. With the USB flash drive image, you can use `dd` to copy the image to a blank disk, as in `dd if=refind-flashdrive-0.10.2.img of=/dev/disk3` to write the image to `/dev/disk3`. **Any existing data on the target disk will be destroyed!** For this reason, it's **_imperative_** that you specify the correct target (`of=`) disk; if you accidentally point this command to your regular hard disk, recovery will be difficult!
3. Reboot and hold down the Option (or Alt) key to see the Mac's built-in boot manager.
4. Select your external boot medium to boot to rEFInd.
5. Use the SIP "shield" icon on the second row to toggle between SIP settings, as described in more detail in [Using rEFInd to Manage SIP.](http://www.rodsbooks.com/refind/sip.html#refind_manage)

Once you install rEFInd, you can leave SIP enabled, enable your newly-installed rEFInd's SIP features and use them to disable SIP, or boot again from your external rEFInd to disable SIP.

This procedure has the advantage of being a bit quicker than using the Recovery HD--at least, if you've already got rEFInd 0.10.0 or later on an external medium. It will also work if your Recovery HD installation is missing or broken. On the other hand, it's probably easier to boot to the Recovery HD once or twice than to download and prepare a rEFInd boot medium. Also, some Macs are a little flaky when it comes to booting from external media, so you may have trouble booting in this way. Finally, if you don't already have rEFInd on an external medium and if you don't have an optical drive, writing a USB flash drive with `dd` carries a small risk of accidentally trashing your hard disk, particularly if you're unfamiliar with disk devices and `dd`.

[]()

### Using Another OS

A final option for installing rEFInd on a Mac that runs with SIP enabled is to do the installation using another OS. This other OS could be an OS that's already installed or an emergency boot disk, such as an [Ubuntu](http://www.ubuntu.com) installation/recovery system.

If you follow this path, you'll need to know something about how to boot and use your non-Apple OS. The options are quite varied, so I can't provide every detail; however, I do have a few tips:

- If you've already installed another OS but can't boot it because of an upgrade to OS X 10.11, you can use rEFInd on CD-R or USB flash drive to boot to your other OS. You can download images for both media from the [rEFInd downloads page.](http://www.rodsbooks.com/refind/getting.html) Prepare a boot medium, insert it in your computer, reboot, and hold down the Option (or Alt) key. The Mac's built-in boot menu should appear, enabling you to boot rEFInd from the removable disk. It should then let you boot your already-installed OS, whereupon you can follow the [regular rEFInd installation instructions](http://www.rodsbooks.com/refind/installing.html) for that OS.
- It's imperative that your rEFInd installation occur in an _EFI-mode boot!_ Many Windows installations on Macs, in particular, are done in BIOS/CSM/legacy mode, and so cannot be used for installing rEFInd. rEFInd can boot most Linux installations in EFI mode (as above), but if a BIOS-mode GRUB is installed, you might accidentally boot it. See the [What's Your Boot Mode?](http://www.rodsbooks.com/refind/bootmode.html) page for information on how to determine your boot mode.
- You can use many Linux distributions' installers to run a minimal Linux system that you can use for installing rEFInd. This can be a useful trick even if you don't intend to run Linux normally. An [Ubuntu](http://www.ubuntu.com) image can be useful for this. You should insert the boot medium and hold down Option (or Alt) while booting to launch the installer, but be sure to pick the option to "try Ubuntu before installing" (or a similar option for other Linux distributions). You may need to install the `efibootmgr` package to install rEFInd. (Typing `sudo apt-get install efibootmgr` should do this in Ubuntu.)

I've tested this method of installing rEFInd on my MacBook Air (purchased in late 2014) and on my first-generation 32-bit Mac Mini, but I can't promise it will work on all Macs--or even on a Mac that's identical to one of mine but with a configuration that's different from mine. My preference is to install rEFInd under OS X on Macs, because Apple likes to do things differently from everybody else, and so a Mac's firmware might not react in the usual way to tools like `efibootmgr` in Linux or `bcdedit` in Windows.

[]()

## Using rEFInd to Manage SIP

Once rEFInd is installed, you can use it to manage SIP features; however, the rEFInd features needed to do this are disabled by default. You must uncomment or add two lines to your `refind.conf` file:

- `showtools`--This line specifies tools that appear on the second row of icons in rEFInd. The new tool for managing SIP is called `csr_rotate`, so you must uncomment `showtools` and add this option, or create a new `showtools` line.
- `csr_values`--This line lists the hexadecimal values through which you can rotate once `csr_rotate` is active on the `showtools` line. The trick to this token is selecting appropriate options. Several sites, such as [this one](http://www.idelta.info/archives/sip-rootless-internal-in-el-capitan/) and [this one,](http://osxarena.com/2015/10/guide-details-apples-system-integrity-protection-sip-for-hackintosh/) describe the meanings of the various options, but often not in much detail. Apple's own `csrutil` command sets values of 77 (disabled) or 10 (enabled). Note also that you specify hexadecimal values on this line, but without a leading `0x` or other hexadecimal-notation indicator. If you specify gibberish values, or hexadecimal values higher than those used by SIP, rEFInd ignores the bad entries. Thus, if some of your values are being ignored, you should check your `csr_values` line for typos.

Note that **_both_** of these options must be set appropriately. If either of them is missing or misconfigured, rEFInd will not display the SIP tool. A typical configuration using these features might look like this:

```
showtools shell,memtest,gdisk,csr_rotate,apple_recovery,windows_recovery,about,shutdown,reboot
csr_values 10,77
```

![The SIP rotation tool rotates through all the CSR values you set](http://www.rodsbooks.com/refind/func_csr_rotate.png) Once these options are set and you reboot into rEFInd, you should see a new shield icon on the second row, as shown at the right. When you select this tool, rEFInd identifies the next available CSR value from the list you specified and switches to that mode, rotating back to the start of the list once the end is reached. To confirm that the SIP mode has changed, rEFInd displays, for three seconds, a message identifying the new mode.

Whether or not you've enabled these SIP features in `refind.conf`, rEFInd displays the current SIP status on its "About" page:

![rEFInd presents a graphical menu for selecting your boot OS.](http://www.rodsbooks.com/refind/about.png)<br>
Note the line that reads "System Integrity Protection is disabled (0x77)" (highlighted in this screen shot). This line will be updated whenever you use the CSR rotation tool, so if you've specified a large number of values and have forgotten where you are in your rotation, you can use the About screen to figure it out.

Both the summary on the About page and the CSR rotation tool depend on the presence of the `csr-active-config` NVRAM variable, which is where this information is stored. Thus, these features will not be present on older Macs that have not seen the presence of an OS X version that sets this variable. Likewise, you probably won't see the SIP summary in About or be able to set these values via `csr_rotate` and `csr_values` on a UEFI-based PC. (You could always create the variable on such a system in some other way, in which case rEFInd would let you adjust it, but it would have no effect on any OS except OS X.)

I provide these features in rEFInd as a convenience for developers and other advanced users who have a need to adjust their SIP settings. Using rEFInd for this purpose is much faster than booting into the OS X Recovery system to make these adjustments. I discourage others from playing with these settings, since changing them inappropriately could cause problems; that's why they're not enabled by default.

[]()

## Conclusion

Although the goal of increased security is a good one, SIP is causing problems for intermediate and advanced users. The good news is that the process to install rEFInd on a system that runs OS X 10.11, although more complex than it used to be, is not an impossible one. Furthermore, once you've done it, you shouldn't have to do it again for a while. (An update to OS X's boot loader is entirely possible, though. If nothing else, the next major OS X update may require re-installing rEFInd.) For advanced users, rEFInd can adjust SIP settings, which can be helpful if you occasionally want to do something that require greater-than-typical privileges.

copyright © 2015-2016 by Roderick W. Smith

This document is licensed under the terms of the [GNU Free Documentation License (FDL), version 1.3.](http://www.rodsbooks.com/refind/FDL-1.3.txt)

If you have problems with or comments about this Web page, please e-mail me at [rodsmith@rodsbooks.com.](mailto:rodsmith@rodsbooks.com) Thanks.

[Go to the main rEFInd page](http://www.rodsbooks.com/refind/index.html)

[Learn how to use rEFInd](http://www.rodsbooks.com/refind/using.html)

[Return](http://www.rodsbooks.com/) to my main Web page.
