# How to create an OS X Mavericks install drive [Updated]

[Original URL](http://www.tips-and-tricks-in-mavericks.com/how-to-create-an-os-x-mavericks-install-drive/)

> Apple just launched OS X Mavericks as a free update for most Mac users who are running at least OS X 10.6.8 Snow Leopard. Ever since OS X 10.8 Mountain Lion, those major new OS X versions were only...

[![How to create an OS X Mavericks install drive](http://cdn.tips-and-tricks-in-mavericks.com/images/2013/10/How-to-create-an-OS-X-Mavericks-install-drive.jpg)](http://cdn.tips-and-tricks-in-mavericks.com/images/2013/10/How-to-create-an-OS-X-Mavericks-install-drive.jpg) Apple [just launched OS X Mavericks as a free update](http://www.tips-and-tricks-in-mavericks.com/os-x-10-9-mavericks-everything-you-need-to-know/ "OS X 10.9 Mavericks: Everything you need to know") for most Mac users who are running at least OS X 10.6.8 Snow Leopard. Ever since OS X 10.8 Mountain Lion, those major new OS X versions were only made available as a download via the Mac App Store. You simply can't buy them on physical media anymore.

In some cases, you may want to create a bootable USB OS X install drive. This has been a rather easy task in the past. Well, it has gotten a bit harder this time around.

Here's what you will need:

- a Mac with access to the Internet and to the Mac App Store
- a USB flash drive with 8GB or more (note that everything on that drive will be erased during this process!)

**Step 1: Download the OS X Mavericks installation app**

[![OS X Mavericks App Store download](http://cdn.tips-and-tricks-in-mavericks.com/images/2013/10/OS-X-Mavericks-App-Store-download.png)](http://cdn.tips-and-tricks-in-mavericks.com/images/2013/10/OS-X-Mavericks-App-Store-download.png) Open the Mac App Store on your Mac and [find the OS X Mavericks page to download the installation app](https://itunes.apple.com/de/app/os-x-mavericks/id675248567?l=en&mt=12 "OS X Mavericks in the App Store"). Downloading the app may take a while since it's roughly around 5.2GB in size.

You may proceed with step 2 while the app is still downloading:

**Step 2: Make hidden files viewable on your Mac**

Open Finder, go to Applications, open the Utilities folder and double click on the Terminal app.

[![Terminal window](http://cdn.tips-and-tricks-in-mavericks.com/images/2013/10/Terminal-window.png)](http://cdn.tips-and-tricks-in-mavericks.com/images/2013/10/Terminal-window.png) Copy and paste the following into the Terminal window, then hit enter to make all hidden files viewable:

_defaults write com.apple.finder AppleShowAllFiles TRUE && killall Finder_

**Further down this post, I will show you how to hide those files again.

**Step 3: Open the OS X Mavericks installation app**

[![OS X Mavericks Show Pakage Contents](http://cdn.tips-and-tricks-in-mavericks.com/images/2013/10/OS-X-Mavericks-Show-Pakage-Contents.png)](http://cdn.tips-and-tricks-in-mavericks.com/images/2013/10/OS-X-Mavericks-Show-Pakage-Contents.png) Once the download is finished, open Finder, go to Applications, right click on the "Install OS X Mavericks" app and then click on "Show Package Contents".

**Step 4: Find the InstallESD.dmg file**

In this step, you need to activate the InstallESD.dmg image file by double clicking on it.

[![OS X Mavericks InstallESD DMG](http://cdn.tips-and-tricks-in-mavericks.com/images/2013/10/OS-X-Mavericks-InstallESD-DMG.png)](http://cdn.tips-and-tricks-in-mavericks.com/images/2013/10/OS-X-Mavericks-InstallESD-DMG.png) The File can be found under Contents -> SharedSupport in the just opened package contents of the OS X Mavericks installation app.

**Step 5: Find the BaseSystem.dmg file**

By double clicking on the aforementioned InstallESD.dmg file, a new window should open.

[![OS X Mavericks Install ESD](http://cdn.tips-and-tricks-in-mavericks.com/images/2013/10/OS-X-Mavericks-Install-ESD.png)](http://cdn.tips-and-tricks-in-mavericks.com/images/2013/10/OS-X-Mavericks-Install-ESD.png) In that window, find the BaseSystem.dmg file. Note that the file will look grayed out since it's a hidden file.

**Step 6: Open Disk Utility**

_**_In the Utilities folder that you have opened in step 2, find the Disk Utility app and open it by double clicking on it.

Insert your USB thumb drive into your Mac.

[![OS X Mavericks Disk Utility 1](http://cdn.tips-and-tricks-in-mavericks.com/images/2013/10/OS-X-Mavericks-Disk-Utility-1.png)](http://cdn.tips-and-tricks-in-mavericks.com/images/2013/10/OS-X-Mavericks-Disk-Utility-1.png) Now, drag the BaseSystem.dmg file from step 5 into the left list of drives in Disk Utility.

**Step 7: Start copying onto the USB drive**

[![OS X Mavericks Disk Utility 2](http://cdn.tips-and-tricks-in-mavericks.com/images/2013/10/OS-X-Mavericks-Disk-Utility-2.png)](http://cdn.tips-and-tricks-in-mavericks.com/images/2013/10/OS-X-Mavericks-Disk-Utility-2.png) In Disk Utility, click on your USB drive's partition, then click on "Unmount" in tool bar (the blue and white button right next to the famous black and yellow Burn button).

Now, click on BaseSystem.dmg in the drive list and drag you USB drive (8.03GB SanDisk Cruzer M, in my case) into the Destination field in the Restore panel.

If you're ready to start copying, click on Restore in the lower right hand side.

**Step 8: Delete Packages alias**

[![Delete Packages Alias](http://cdn.tips-and-tricks-in-mavericks.com/images/2013/10/Delete-Packages-Alias.png)](http://cdn.tips-and-tricks-in-mavericks.com/images/2013/10/Delete-Packages-Alias.png) Go to Finder, look for the USB drive you've just created, find System -> Installation and delete the Packages file (alias).

**Step 9: Copy the actual Packages folder**

[![Copy actual Packages folder 1](http://cdn.tips-and-tricks-in-mavericks.com/images/2013/10/Copy-actual-Packages-folder-1.png)](http://cdn.tips-and-tricks-in-mavericks.com/images/2013/10/Copy-actual-Packages-folder-1.png) Open another Finder window by selecting a Finder window and then clicking cmd + N. Find the OS X Install ESD image that you've opened previously. From there, copy the Packages folder to the Installation folder on your USB drive (right where you just deleted the alias file in step 8).

Note that this step will take a while.

**Step 10: Hide hidden files again**

[![Hide hidden Files in OS X](http://cdn.tips-and-tricks-in-mavericks.com/images/2013/10/Hide-hidden-Files-in-OS-X.png)](http://cdn.tips-and-tricks-in-mavericks.com/images/2013/10/Hide-hidden-Files-in-OS-X.png) Open Terminal (see step 2) and copy and paste the following, then hit enter:

_defaults write com.apple.finder AppleShowAllFiles FALSE && killall Finder_

**Step 11: Install OS X Mavericks from your USB drive**

That's it: your USB drive is now ready to be used to install OS X Mavericks from it.

Make sure you insert your USB drive, restart your Mac and press the alt key once your hear the sound when your Mac is booting up.

**+++ Update +++**

[In a comment below](http://www.tips-and-tricks-in-mavericks.com/how-to-create-an-os-x-mavericks-install-drive/#comment-1472 "Comment by Alex Pupin"), our reader Alex has pointed out **another way to create bootable OS X USB media in way that requires less steps**. Note that it involves some interaction with the root shell, so it's **not recommended for the less tech-savvy folks** out there.
