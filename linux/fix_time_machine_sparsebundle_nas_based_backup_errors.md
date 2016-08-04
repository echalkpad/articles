# Fix Time Machine Sparsebundle NAS Based Backup Errors

[Original URL](http://www.garth.org/archives/2011,08,27,169,fix-time-machine-sparsebundle-nas-based-backup-errors.html)

> This is a modification of an original post for use when you have a corrupt sparsebundle backup on a NAS (as opposed to an external drive attached to a router) and it needs to be repaired. The NAS is...

[![Time Machine](http://www.garth.org/wp-content/uploads/2011/08/time-machine-icon-150x150.png "Time Machine")](http://www.garth.org/wp-content/uploads/2011/08/time-machine-icon.png)

This is a modification of an [original post](http://www.garth.org/archives/2010,07,16,124,fixing-time-machine-sparsebundle-network-backup-errors.html) for use when you have a corrupt sparsebundle backup on a NAS (as opposed to an [external drive attached to a router](http://www.garth.org/archives/2010,07,16,124,fixing-time-machine-sparsebundle-network-backup-errors.html "Fix Time Machine Sparsebundle Network Backup Errors")) and it needs to be repaired. The NAS is likely a hardware product from the likes of Netgear, Synology, Buffalo or QNap – or for those of us with a home-grown backup server running FreeNAS.

The error you may see is "Time Machine completed a verification of your backups. To improve reliability, Time Machine must create a new backup for you." This can be fixed by following the below.

From your Mac, connect to the network share that houses the sparsebundle.

At the top level of the drive are the various sparsebundles that make up your individual computer backups.

Do not double click on these sparsebundles or try to repair with Disk Utility.

Open Terminal and then switch to root by typing

`sudo su -`

and then enter your password.

The verication that has already run has marked your sparsebundle as bad, so first we need to make it look normal.

From the command line

`chflags -R nouchg /Volumes/{name of your network share}/{name of}.sparsebundle`

This may take a little while.

Now type

`hdiutil attach -nomount -noverify -noautofsck /Volumes/{name of your network share/{name of}.sparsebundle`

You will then see something like

`/dev/diskx Apple_partition_scheme /dev/diskxs1 Apple_partition_map /dev/diskxs2 Apple_HFSX`

Where x is the disk id for the external disk. You are interested in the one labeled Apple_HFSX or Apple_HFS. It might be 2, 3, 4 or higher.

At this point, I have found that the filesystem check is already happening. You can check for activity by tail'ing the fsck_hfs.log

`tail -f /var/log/fsck_hfs.log`

If fsck is going then in my experience it will be able to repair the sparsebundle. Go away for a few hours and let it chug away.

When it is done, you will either see

'The Volume was repaired successfully'

'The Volume could not be repaired'

If the latter you can run disk repair again:

`fsck_hfs -drfy /dev/diskxs2`

(Optionally if you have the available RAM, you can set a RAM cache in the command above to help speed up this command like so:

`fsck_hfs -drfy -c 750 /dev/diskxs2`

This will use 750MB of RAM – feel free to change this amount to best fit your system (amount of RAM vs size of your Time Machine Sparsebundle). If you are unsure about this, use the first command.

Make sure to replace x with whatever number your disk is from the output above.

The letters "drfy" tell the filecheck utility different things. d for 'Show Debug' – r for 'Rebuild Catalog Tree' – f for 'Force' and y for assume 'yes' to any prompts.

Now go do something for an hour or two. Come back and

`tail -f /var/log/fsck_hfs.log`

If all went well, the last output you will see is

'The Volume was repaired successfully'

Now you need to type<br>
`hdiutil detach /dev/diskxs2`

You can redo the above for any other Time Machine sparse bundles you have permission to modify while you have the network share attached to your computer.

Final step.

When complete, you need to edit an plist file within the sparsebundle that records the state of the backup. On the top level of the sparsebundle find a file called com.apple.TimeMachine.MachineID.plist. Edit it and remove these two nodes

`<key>RecoveryBackupDeclinedDate</key> <date>{whatever-the-date}</date>`

Finally you want to change

`<key>VerificationState</key> <integer>2</integer>`

to<br>
`<key>VerificationState</key> <integer>0</integer>`

Now you can eject the network share and have Time Machine give it another go. After the (long) verification step, backups should proceed once again.

Notes:

Ideally this should be done over a gigabit wired network connection. Do not attempt using Wi-Fi. You also want to make sure your machine does not go to sleep during the above operation.

[Update: 1.1.2013]

I appreciate all the warm feedback from people all over the world who have been helped by this post. This site helps to fund my hobbies, so if this post has helped you please consider a USD $1.99 donation to my hobby fund.

[Update: 12.23.2012]

If after running the initial

`fsck_hfs -drfy /dev/diskxs2`

command you get a message in the fsck_hfs.log along the lines of

`RebuildBTree – record x in node y is not recoverable.`

then try

`fsck_hfs -p /dev/diskxs2`

followed by

`fsck_hfs -drfy /dev/diskxs2`

And see if that works. It did for me today.
