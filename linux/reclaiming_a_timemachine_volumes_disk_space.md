# Reclaiming a TimeMachine Volume's Disk Space

[Original URL](http://blog.hawkimedia.com/2012/08/reclaiming-a-timemachine-volumes-disk-space/)

> A slight departure from the thing we all love doing (writing code) to a thing some of us may have to spend time on (system administration). We added a fourth Mac to our home network recently, and I...

A slight departure from the thing we all love doing (writing code) to a thing some of us may have to spend time on (system administration). We added a fourth Mac to our home network recently, and I wanted to get it backed up automatically. The solution was obvious: TimeMachine! I run a Linux server with gobs of storage. It runs Netatalk 3.0 and serves up (among other volumes) a 500GB TimeMachine volume. It works really well, except when the volume is full with TimeMachine backups from other machines. There's a way to reclaim this space, but it's not obvious or automatic.

If you're a TimeMachine user, you know that when your TimeMachine volume fills, TimeMachine will automatically remove the oldest backup(s) to make room for new backups. What may not be obvious is that when space is needed and old backups are removed, the total disk usage of the sparse bundle in which TimeMachine actually stores your backups does not change! If you have a single Mac backing up to a dedicated TimeMachine volume, this really isn't an issue. But in my case, I have one large volume which I share with multiple Macs. Two Macs have been merrily backed up for well over a year each, or more. The volume is nearly full, divided between 2 sparse bundle files.

It would be nice if TimeMachine had some advanced controls to limit how much actual disk space any one machine could use, but it doesn't. Luckily, though, there are some command line tools available to help us.

Basically, it's a two step process:

1. Remove old backups manually
2. Compact the sparse bundle

Apple provides a command line utility named **tmutil**. You can use it from a Terminal window. While it can do a lot of things, what we're interested in are two commands. The first simply lists all your backup sets:

```
% tmutil listbackups
```

You'll get output similar to this:

```
/Volumes/Time Machine Backups/Backups.backupdb/Mark Granoff’s iMac/2011-02-28-004805
/Volumes/Time Machine Backups/Backups.backupdb/Mark Granoff’s iMac/2011-03-07-004637
/Volumes/Time Machine Backups/Backups.backupdb/Mark Granoff’s iMac/2011-03-21-003726
...
/Volumes/Time Machine Backups/Backups.backupdb/Mark Granoff’s iMac/2012-08-30-071401
/Volumes/Time Machine Backups/Backups.backupdb/Mark Granoff’s iMac/2012-08-30-081501
```

These are the paths to all your backup sets in the sparse bundle TimeMachine uses for your Mac's backups. They are listed from oldest to newest. As you can see here, my oldest backup is from February 28, 2011, and the next oldest is the following week, March 7, 2011\. These are pretty old and really not worth keeping around, especially if you want to reclaim some disk space on the volume. Notice that these old backups are essentially "weekly" backups. TimeMachine will keep an unlimited number of these weekly backups. More recent backups are hourly and daily, and these are the more valuable backups to keep around.

The first thing you need to do is to remove the oldest backups, starting with the very oldest, and working your way forward in time. Be as aggressive as you feel you need to be to both reclaim disk space and feel confident that your backup sets are as complete as you need them to be. DO NOT DELETE ALL YOUR BACKUP SETS!

Deleting a backup set is simple, but requires root privilege:

```
% sudo tmutil delete '/Volumes/Time Machine Backups/Backups.backupdb/Mark Granoff’s iMac/2011-02-28-004805'
```

You'll be asked for the administrator's password, and then tmutil will chug away. If the operation succeeds, the tool simply exits without any output. If there is a problem, you'll see an error message.

Notice that I've wrapped the path to the backup in single quotes. This is because there are spaces in the path.

The tmutil delete command only removes the backup from the sparse bundle. It doesn't actually free the disk space. To do that, you have to go a little deeper.

On your Mac is a mount point called /Volumes. You can examine the contents of this mount point with ls:

```
% cd /Volumes
% ls -1
Macintosh HD
NO NAME
Recovery HD
Time Machine Backups
TimeMachine
```

These are the names of all the mounted disks (or things that look like disks) on your Mac. Notice two likely candidates for your actual TimeMachine volume. Yours may be named slightly differently, but the one you want is the one that actually shows files of type .sparsebundle . In my case, it is the volume TimeMachine:

```
% sudo ls -l TimeMachine/
...
drwxr-x---@ 1 root wheel 264 Aug 30 08:21 Mark Granoff’s iMac.sparsebundle
...
```

Notice that you don't actually own the file. (Had I not used the sudo command with ls I could not have listed the contents of /Volumes/TimeMachine)

That .sparsebundle file for your Mac is where all your backup sets live. TimeMachine manages the contents of this file, but doesn't do anything automatically to reduce its size. Luckily there is another tool for that, but you'll have to be root to run it:

```
% sudo su - root
% hdiutil compact /Volumes/TimeMachine/YourBackup.sparsebundle
Starting to compact…
Reclaiming free space…
...................................................
Finishing compaction…
Reclaimed 3.1 GB out of 304.1 GB possible.
```

That's it! In this example I reclaimed 3.1GB of actual disk space on my TimeMachine volume. While working at removing old backups on my wife's machine, a half dozen or so old backups accounted for over 63GB of reclaimed disk space! So, the results can be dramatic.

So lately I've been deleting a few backup sets and then compacting the .sparsebundle, and then repeating. I have a long way to go, between my Mac and my wife's, before there is enough space available to backup the new Mac for the first time. It's a time consuming process, but well worth the time in order to tame TimeMachine's appetite for disk space.
