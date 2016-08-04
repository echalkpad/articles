# Time Machine for every Unix out there

[Original URL](https://blog.interlinked.org/tutorials/rsync_time_machine.html)

> Using rsync to mimic the behavior of Apple's Time Machine feature rsync is one of the tools that have gradually infiltrated my day to day tool-box (aside Vim and Zsh). Using rsync it's very...

## Using rsync to mimic the behavior of Apple's Time Machine feature

[rsync](http://rsync.samba.org) is one of the tools that have gradually infiltrated my day to day tool-box (aside Vim and Zsh).

Using rsync it's very easy to mimic Mac OS X new feature called Time Machine. In this article I'll show how to do it, but there is still a nice 

<span class="caps">GUI</span>

 missing – for those who like it shiny.

## What Time Machine does

Time Machine makes a snapshot of your files every hour. The files are usually stored on a external hard drive connected to your Mac via 

<span class="caps">USB</span>

 or Firewire. Earlier Leopard versions (

<span class="caps">ADC</span>

 preview versions) had the ability to make the backups to a remote drive (I've heard).

So if you lose a file, or did a devastating change to one of your files, simply go back in time until you find your file or a version that's not corrupted.

Incrementally backing up all files every hour so that you can access them in reversed chronological order isn't that hard with standard Unix utilities like rsync. The only missing thing is a nice 

<span class="caps">GUI</span>

 for which Apple is known to be quite good at.

## Making full backups in no time every hour

You can use this method to make a backup every hour or every ten minutes if you like. There are many many features you can tune or configure to your own taste – excluding files that are larger than 1GB for example.

So, here the command to make the backup:

```
rsync -aP --link-dest=PATHTO/$PREVIOUSBACKUP $SOURCE $CURRENTBACKUP
```

Lets go through the parameters step by step.

- `-a` means Archive and includes a bunch of parameters to recurse directories, copy symlinks as symlinks, preserve permissions, preserve modification times, preserve group, preserve owner, and preserve device files. You usually want that option for all your backups.
- `-P` allows rsync to continue interrupted transfers and show a progress status for each file. This isn't really necessary but I like it.
- `--link-dest` this is a neat way to make full backups of your computers without losing much space. rsync links unchanged files to the previous backup (using hard-links, see below if you don't know hard-links) and only claims space for changed files. This only works if you have a backup at hand, otherwise you have to make at least one backup beforehand.
- `PATHTO/$PREVIOUSBACKUP` is the path to the previous backup for linking. Note: if you delete this directory, **no other backup is harmed** because rsync uses **hard-links** and the operating system (or filesystem) takes care of releasing space if no link points to that region anymore.
- `$SOURCE` is the directory you'd like to backup.
- `$CURRENTBACKUP` is the directory to which you'd like to make the backup. This should be a **non-existing directory**.

As said earlier, rsync has many many features. To exclude files over a certain size for example, use the option `--max-size` (unfortunately this is not available on the rsync version shipped with Mac OS X Leopard). The man page or the [documentation](http://rsync.samba.org/documentation.html) can give you plenty of ideas in this direction.

So much for the theory of the most important command for our purpose. Here a simple script that makes an incremental backup every time you call it:

```
#!/bin/sh

date=`date "+%Y-%m-%dT%H:%M:%S"`
rsync -aP --link-dest=$HOME/Backups/current /path/to/important_files $HOME/Backups/back-$date
rm -f $HOME/Backups/current
ln -s back-$date $HOME/Backups/current
```

The script creates a file called "back" appended by the current date and time, for example `back-2007-11-13T22:03:32` which contains the **full backup**. Then there is a symbolic link called "current" which points to the most recent directory. This directory-link is used for the `--link-dest` parameter.

You should look at the `--exclude` parameter (or better, `--exclude-from=` parameter) and learn how to exclude certain files or directories from the backup (you shouldn't backup your backup for example).

The script above only works on the local machine because making links on a remote machine needs some extra work. But not much:

```
#!/bin/sh

date=`date "+%Y-%m-%dT%H:%M:%S"`
rsync -azP --link-dest=PATHTOBACKUP/current $SOURCE $HOST:PATHTOBACKUP/back-$date
ssh $HOST "rm -f PATHTOBACKUP/current && ln -s back-$date PATHTOBACKUP/current"
```

The `-f` parameter for the `rm` command is used to supress error messages if the `current` directory is not present, which would in turn prevent the link to be created.

To get that working you either use a public/private key authentication scheme or something else to avoid typing in your password. Another possibility is, of course, to mount the remote file-system on the local computer using the above script.

On my setup the script takes about 6 seconds to synchronize 46968 files and 29GB – this takes 20MB for the file structure (with no actual files to transfer of course). But afterwards, I have a complete backup of my system in a new directory.

On a much bigger setup (1.2 million files and 50GB of data) the backup takes about 30 minutes and takes about 3GB of space (just for links!), so it isn't exactly **free**, but very convenient.

The space needed for the backup is determined by the shape of your directory structure. On the larger setup I have lots of Maildirs and a very deep directory structure so it takes much more space than my home-directory backup above. 3GB is quite a lot, but 20MB doesn't hurt.

## Advanced `rsync` parameters

Additional to the parameters described above, I usually employ a combination of these parameters in my backup:

- `--delete` and `--delete-excluded` this tells rsync to remove files from my backups either if they are gone on my local machine, or if I decided to exclude them from my backup.
- `--exclude-from=FILE` the file specified here is a simple list of directories of files (one per line) which should **not** be backed up. My `Trash` folder oder some `.cache` folders are candidates for this file.
- `-P` is used to give more information on how far the backup is, and how many files are to be backed up. Additional it could resume an interrupted transfer (which doesn't apply here because we create a blank backup each time we call the script).
- `-x` this one is **important** because it prohibits rsync to go beyond the local filesystem. For example if you backup you Linux-root partition, you should not include the /proc directory because rsync will get stuck in it. `-x` excludes all mounted filesystems from the backup which is probably what you want in most cases.

## Hard-Links

Each file in a directory is a link to the actual data on your hard-disk. The file-system keeps track of how many links to a area point, and only if the last link is deleted, the whole area gets deleted (in contrast to soft-links, these are pointers to the file-name, not the contents).

Here an illustration of two backups with three files each. `File1` and `File2` are the _same_ in both backups, only `File3` changed between `Backup1` and `Backup2`. So in `Backup2`, `File3 (changed)` has to point to a different area than `File3` in `Backup1`.

![](http://blog.interlinked.org/static/images/hardlinks.png)

<span class="caps">BTW</span>

, there is a [nice project for Linux](http://code.google.com/p/flyback/) out there which provides the same functionality as Time Machine including a nice 

<span class="caps">GUI</span>

 which is also based on **`rsync`** and the procedure presented here.

## The End

Credit: The initial idea for this approach came from [Mike Rubel – rsync snapshots](http://www.mikerubel.org/computers/rsync_snapshots/).

Also interesting if you have to cope with Windows: [Optimal remote backups with rsync over Samba](http://www.softlab.ntua.gr/~ttsiod/backup.html).

There are quite a few approaches out there which more or less do the same, but rsync is available on virtually every Unix out there (even the 

[<span class="caps">DSL</span>](http://damnsmalllinux.org/)

 with its 50MB footprint includes rsync). So using other tools might be more convenient, but I'll stick with the omnipresent rsync.

rsync offers the possibility to store only the differences to the previous backup (using `--compare-dest` which should point to a full-backup instead of `--link-dest`). It then doesn't make links to the unchanged files, it just leaves them out. This way you get an incremental backup without the "directory-overhead" of the `--link-dest` approach. **But** you have to be **extremely** cautious which one of older backups you delete because the newer backups just don't contain some of these files (think of full-backups as checkpoints)! Using the `--link-dest` you can delete all backups but the last and you still got all the files, so I'm happy to pay 20MB per backup for this safety.

## Full script

Here my full script with additional features:

```
#!/bin/sh

date=`date "+%Y-%m-%dT%H_%M_%S"`
HOME=/home/user/

rsync -azP \
 --delete \
 --delete-excluded \
 --exclude-from=$HOME/.rsync/exclude \
 --link-dest=../current \
 $HOME user@backupserver:Backups/incomplete_back-$date \
 && ssh user@backupserver \
 "mv Backups/incomplete_back-$date Backups/back-$date \
 && rm -f Backups/current \
 && ln -s back-$date Backups/current"
```
