# How to create a network backup with Apple's TimeMachine

[Original URL](https://foliovision.com/2010/05/network-backup-apple-timemachine)

> If you have tried to set up network backup on OS X and you ran into the message "the backup disk image could not be created", probably this article will help you. OS X's TimeMachine software had...

If you have tried to set up network backup on OS X and you ran into the message "the backup disk image could not be created", probably this article will help you.

OS X's TimeMachine software had native support for network backup until the OS X Leopard 10.5.2 was released. Apple had its own [reasons](http://www.kuro5hin.org/story/2008/5/11/131026/867) for the decision to remove network backup, but many advanced users including us at Foliovision would still like to be able to back up over the network.

We have a bunch of Mac Minis in a mixed network of Linux and Windows computers. We'd like to use all our Minis for work and not for backup and use one of our older Linux towers to store the backup.

How do you do it?

Fortunately Apple left us an option to turn-on network support for TimeMachine by running the following command in terminal:

`defaults write com.apple.systempreferences TMShowUnsupportedNetworkVolumes 1`

We can finally see network hardrives in selection for backup places. The problem might seems to be solved. No dice: it is only the beginning.

If you select a network drive for backup, you will see a very familiar message:

> the backup disk image could not be created

Google automatically completes the phrase as soon as you type "the backup d".

The real problem is in the file system which must be used for TimeMachine backup. TimeMachine only supports Mac OS Extended (Journaled) file system.

The solution is to create a place on the network, which will trick TimeMachine, into thinking that it holds data in Mac OS Extended (Journaled) file system. This trick is accomplished via copying a sparsebundle image (with special name) to a network share. You have to do following steps for to make TimeMachine successfully run a backup.

## OVERVIEW

1. **Enable network hardrive support in TimeMachine** 
2. **Mount network shared place for backup data** 
3. **Create a sparsebundle virtual image**
4. **Copy the sparsebundle virtual image to the network shared place** 
5. **Set up TimeMachine for network backup**
6. **Optimisation and other information**

## 1\. Enable network hardrive support for the TimeMachine software

Network hardrive support for TimeMachine is turn off by default. For changing this fact we have to type following command to the terminal:<br>
`defaults write com.apple.systempreferences TMShowUnsupportedNetworkVolumes 1`

After that, you should see mounted network harddrive in TimeMachine's locations for backup. If it is not this case, probably a restart is needed.

## 2\. Mount Network shared place for backup data

If you have a shared location for backup data on Linux server, you can map the directory as a drive to Mac in Finder application. You have to do following steps:

1. Click GO and then "_connect to server_" in finder menu.

  ##### [![Finder Menu](https://cdn.foliovision.com/images/2010/04/400/Finder-Menu.png)](https://foliovision.com/images/2010/04/Finder-Menu.png "Finder Menu")

  #####

2. Write following path if your sharing is based on protocol samba (SMB):<br>
  `SMB://<IP_ADDRESS_or_COMPUTER_NAME>/<NAME_of_SHARED_PLACE>` (e.g. `SMB://192.168.1.25/Backup`)

  ##### [![Connect Server](https://cdn.foliovision.com/images/2010/04/400/Connect-Server.png)](https://foliovision.com/images/2010/04/Connect-Server.png "Connect Server")

## 3\. Create a sparsebundle virtual image

This step is very important, because the TimeMachine doesn't allow to backup data to a network drive, which has any file system except "Mac OS Extended (Journaled)". So we have to create a disk image in "Mac OS Extended (Journaled)" format.<br>
We have two ways to do it:

1. Open disk utility (Disk Utility) and create new virtual disk with following atributes:

  - `Save As: <computer hostname>_<hex mac address of en0 interface>.sparsebundle` (e.g. `MacAlec_00ef9a048c4f.sparsebundle`, if you forget to add `.sparsebundle`, it will be added automatically)
  - `Volume Name: Backup of <computer hostname>` (e.g. `Backup of MacAlec`)
  - `Volume Size:` the max amount of space you're going to set aside for backups. (The volume size is depends on amounts of backup data. I have chosen `150 GB`)
  - `Volume Format: Mac OS Extended (Journaled)`
  - `Encryption: None`
  - `Partitions: No partition map`
  - `Image Format: Sparse bundle disk image`

    [![Disk Utility](https://cdn.foliovision.com/images/2010/04/400/Disk-Utility.png)](https://foliovision.com/images/2010/04/Disk-Utility.png "Disk Utility")

  - Note: Disk utility automatically mounts the image to system volumes. We won't need the image mounted, so we can safely unmount it.

2. The previous step can be done by one command in terminal:<br>
  `hdiutil create -size 150g -fs HFS+J -volname "Backup of MacAlec" MacAlec_00ef9a048c4f.sparsebundle`

## 3\. Copy the sparsebundle virtual image to the network shared place

You can copy the sparsebundle image to the network shared place using one of the following steps:

- Copy the file to shared place by drag and drop operation in Finder application.
- If you prefer to work with the terminal, you can type this instead:<br>
  `cp -r <sparsebundle_image_disc_location>/<computer hostname>_<hex mac address of en0 interface>.sparsebundle /Volumes/<NAME_of_SHARED_PLACE>/`(e.g. `cp -r /Users/Alec/Documents/MacAlec_00ef9a048c4f.sparsebundle /Volumes/Backup/`)

Now we can safely delete the sparsebundle image copy on local computer once we are sure that it has been copied to the shared location.

## **Set up the TimeMachine for network backup**

1. Open TimeMachine preferences (in SystemPreferences).
2. Click on **Change Disk** button for select the network drive for backup. (We have to select the "Backup"in our case.)
3. [![TimeMachine](https://cdn.foliovision.com/images/2010/04/400/TimeMachine.png)](https://foliovision.com/images/2010/04/TimeMachine.png "TimeMachine")

4. The first backup will start in two minutes. TimeMachine supports incremental backup, thus the first backup can take very long time (it is depends on network bandwidth and amount of backup data).

If you want to eject the mapped shared volume (`/Volumes/Backup` in our case), you have to do it within the two minutes countdown before backup starts or after backup is finished. TimeMachine has its own mechanism for mapping network hardrives, so a backup process isn't interrupted by your hard drive mounting or unmounting. TimeMachine will automatically mount the virtual sparsebundle image, when the backup starts. You will see `Backup of <computer_name>` as connected device on your desktop, so you won't need to manually mount the network hard drive later.

## 4\. Optimisation and other information

in order to avoid long delays in backup process caused by Spotlight indexing, You should set the Spotlight application to not index the mapped network drive. Here's how to remove spotlight indexing for a hard drive:

- Open up the Spotlight software preferences in system preferences window
- Move to privacy options and add the network drive by plus symbol

  [![Spotlight](https://cdn.foliovision.com/images/2010/04/400/Spotlight.png)](https://foliovision.com/images/2010/04/Spotlight.png "Spotlight")

If you'd like to back up a little less often, you can modify the file `com.apple.backupd-auto.plist` which is located in `/System/Library/LaunhDaemons/` to change backup time interval. Open up the file in text editor and find the section:

`<key>StartInterval</key> <integer>3600</integer>`

You should change the number 3600 to the number of seconds of your backup interval.

## Result

I deliberately deleted some files to test backup reliability. This worked just fine. I was able to restore data from backup using TimeMachine's restore feature. TimeMachine restore is easy to use and lets you choose data from any date and hour. If you have followed the exact steps above, you should have a working network backup now on a non-Apple computer.

I would like to thank [Nick Hilliard](http://www.foobar.org/blog/time-machine-on-samba-shares) and [MacCorner](http://www.readynas.com/?p=253) for useful information.

This entry was posted in [IT](https://foliovision.com/weblog/it). Bookmark the [permalink](https://foliovision.com/2010/05/network-backup-apple-timemachine "Permalink to How to create a network backup with Apple’s TimeMachine").
