# A Beginner's Guide To LVM

[Original URL](https://www.howtoforge.com/linux_lvm)

> Version 1.0 Author: Falko Timme Last updated: 2015-02-09 This guide shows how to work with LVM (Logical Volume Management) on Linux. It also describes how to use LVM together with RAID1 in an extra...

Version 1.0<br>
Author: Falko Timme<br>
Last updated: 2015-02-09

This guide shows how to work with LVM (Logical Volume Management) on Linux. It also describes how to use LVM together with RAID1 in an extra chapter. As LVM is a rather abstract topic, this article comes with a Debian Etch VMware image that you can download and start, and on that Debian Etch system you can run all the commands I execute here and compare your results with mine. Through this practical approach you should get used to LVM very fast.

However, I do not issue any guarantee that this tutorial will work for you!

## 1 Preliminary Note

This tutorial was inspired by two articles I read:

These are great articles, but hard to understand if you've never worked with LVM before. That's why I have created [this Debian Etch VMware image](https://www.howtoforge.com/downloads/files/Debian_Etch_LVM.zip) that you can download and run in VMware Server or VMware Player (see <http://www.howtoforge.com/import_vmware_images> to learn how to do that).

I installed all tools we need during the course of this guide on the Debian Etch system (by running

apt-get install lvm2 dmsetup mdadm reiserfsprogs xfsprogs

) so you don't need to worry about that.

The Debian Etch system's network is configured through DHCP, so you don't have to worry about conflicting IP addresses. The root password is 

<span class="system">howtoforge</span>

. You can also connect to that system with an SSH client like [PuTTY](http://www.chiark.greenend.org.uk/%7Esgtatham/putty/). To find out the IP address of the Debian Etch system, run

ifconfig

The system has six SCSI hard disks, 

<span class="system">/dev/sda</span>

 - 

<span class="system">/dev/sdf</span>

. 

<span class="system">/dev/sda</span>

 is used for the Debian Etch system itself, while we will use 

<span class="system">/dev/sdb</span>

 - 

<span class="system">/dev/sdf</span>

 for LVM and RAID. 

<span class="system">/dev/sdb</span>

 - 

<span class="system">/dev/sdf</span>

 each have 80GB of disk space. In the beginning we will act as if each has only 25GB of disk space (thus using only 25GB on each of them), and in the course of the tutorial we will "replace" our 25GB hard disks with 80GB hard disks, thus demonstrating how you can replace small hard disks with bigger ones in LVM.

The article <http://www.linuxdevcenter.com/pub/a/linux/2006/04/27/managing-disk-space-with-lvm.html> uses hard disks of 250GB and 800GB, but some commands such as 

<span class="system">pvmove</span>

 take a long time with such hard disk sizes, that's why I decided to use hard disks of 25GB and 80GB (that's enough to understand how LVM works).

Download [this Debian Etch VMware image](http://downloads.howtoforge.com/files/Debian_Etch_LVM.zip) (~310MB) and start it [like this](http://www.howtoforge.com/import_vmware_images). Log in as root with the password 

<span class="system">howtoforge</span>

.

## 2 LVM Layout

Basically LVM looks like this:

[![](https://www.howtoforge.com/images/lvm/lvm_scheme_full.png)](https://www.howtoforge.com/images/lvm/big/lvm_scheme_full.png)

You have one or more physical volumes (

<span class="system">/dev/sdb1</span>

 - 

<span class="system">/dev/sde1</span>

 in our example), and on these physical volumes you create one or more volume groups (e.g. 

<span class="system">fileserver</span>

), and in each volume group you can create one or more logical volumes. If you use multiple physical volumes, each logical volume can be bigger than one of the underlying physical volumes (but of course the sum of the logical volumes cannot exceed the total space offered by the physical volumes).

It is a good practice to not allocate the full space to logical volumes, but leave some space unused. That way you can enlarge one or more logical volumes later on if you feel the need for it.

In this example we will create a volume group called 

<span class="system">fileserver</span>

, and we will also create the logical volumes 

<span class="system">/dev/fileserver/share</span>

, 

<span class="system">/dev/fileserver/backup</span>

, and 

<span class="system">/dev/fileserver/media</span>

 (which will use only half of the space offered by our physical volumes for now - that way we can switch to RAID1 later on (also described in this tutorial)).

## 3 Our First LVM Setup

Let's find out about our hard disks:

fdisk -l

The output looks like this:

server1:~# fdisk -l

Disk /dev/sda: 21.4 GB, 21474836480 bytes<br>
255 heads, 63 sectors/track, 2610 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Device Boot Start End Blocks Id System<br>
/dev/sda1 * 1 18 144553+ 83 Linux<br>
/dev/sda2 19 2450 19535040 83 Linux<br>
/dev/sda4 2451 2610 1285200 82 Linux swap / Solaris

Disk /dev/sdb: 85.8 GB, 85899345920 bytes<br>
255 heads, 63 sectors/track, 10443 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Disk /dev/sdb doesn't contain a valid partition table

Disk /dev/sdc: 85.8 GB, 85899345920 bytes<br>
255 heads, 63 sectors/track, 10443 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Disk /dev/sdc doesn't contain a valid partition table

Disk /dev/sdd: 85.8 GB, 85899345920 bytes<br>
255 heads, 63 sectors/track, 10443 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Disk /dev/sdd doesn't contain a valid partition table

Disk /dev/sde: 85.8 GB, 85899345920 bytes<br>
255 heads, 63 sectors/track, 10443 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Disk /dev/sde doesn't contain a valid partition table

Disk /dev/sdf: 85.8 GB, 85899345920 bytes<br>
255 heads, 63 sectors/track, 10443 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Disk /dev/sdf doesn't contain a valid partition table

There are no partitions yet on 

<span class="system">/dev/sdb</span>

 - 

<span class="system">/dev/sdf</span>

. We will create the partitions 

<span class="system">/dev/sdb1</span>

, 

<span class="system">/dev/sdc1</span>

, 

<span class="system">/dev/sdd1</span>

, and 

<span class="system">/dev/sde1</span>

 and leave 

<span class="system">/dev/sdf</span>

 untouched for now. We act as if our hard disks had only 25GB of space instead of 80GB for now, therefore we assign 25GB to 

<span class="system">/dev/sdb1</span>

, 

<span class="system">/dev/sdc1</span>

, 

<span class="system">/dev/sdd1</span>

, and 

<span class="system">/dev/sde1</span>

:

fdisk /dev/sdb

<span class="system">server1:~# fdisk /dev/sdb  </span>

The number of cylinders for this disk is set to 10443.<br>
There is nothing wrong with that, but this is larger than 1024,<br>
and could in certain setups cause problems with:<br>
1) software that runs at boot time (e.g., old versions of LILO)<br>
2) booting and partitioning software from other OSs<br>
(e.g., DOS FDISK, OS/2 FDISK)

Command (m for help): 

<span class="highlight">&lt;– m</span>

<br>

<span class="system">Command action<br>   a   toggle a bootable flag<br>   b   edit bsd disklabel<br>   c   toggle the dos compatibility flag<br>   d   delete a partition<br>   l   list known partition types<br>   m   print this menu<br>   n   add a new partition<br>   o   create a new empty DOS partition table<br>   p   print the partition table<br>   q   quit without saving changes<br>   s   create a new empty Sun disklabel<br>   t   change a partition’s system id<br>   u   change display/entry units<br>   v   verify the partition table<br>   w   write table to disk and exit<br>   x   extra functionality (experts only)  </span>

Command (m for help): 

<span class="highlight">&lt;– n</span>

<br>

<span class="system">Command action<br>   e   extended<br>   p   primary partition (1-4)</span>

<br>

<span class="highlight">&lt;– p</span>

<br>

<span class="system">Partition number (1-4):</span>

 

<span class="highlight">&lt;– 1</span>

<br>

<span class="system">First cylinder (1-10443, default 1):</span>

 

<span class="highlight">&lt;– &lt;ENTER&gt;</span>

<br>

<span class="system">Using default value 1<br>Last cylinder or +size or +sizeM or +sizeK (1-10443, default 10443):</span>

 

<span class="highlight">&lt;– +25000M</span>

<span class="system">Command (m for help):</span>

 

<span class="highlight">&lt;– t</span>

<br>

<span class="system">Selected partition 1<br>Hex code (type L to list codes):</span>

 

<span class="highlight">&lt;– L</span>

<span class="system">0  Empty           1e  Hidden W95 FAT1 80  Old Minix       be  Solaris boot<br> 1  FAT12           24  NEC DOS         81  Minix / old Lin bf  Solaris<br> 2  XENIX root      39  Plan 9          82  Linux swap / So c1  DRDOS/sec (FAT-<br> 3  XENIX usr       3c  PartitionMagic  83  Linux           c4  DRDOS/sec (FAT-<br> 4  FAT16 &lt;32M      40  Venix 80286     84  OS/2 hidden C:  c6  DRDOS/sec (FAT-<br> 5  Extended        41  PPC PReP Boot   85  Linux extended  c7  Syrinx<br> 6  FAT16           42  SFS             86  NTFS volume set da  Non-FS data<br> 7  HPFS/NTFS       4d  QNX4.x          87  NTFS volume set db  CP/M / CTOS / .<br> 8  AIX             4e  QNX4.x 2nd part 88  Linux plaintext de  Dell Utility<br> 9  AIX bootable    4f  QNX4.x 3rd part 8e  Linux LVM       df  BootIt<br> a  OS/2 Boot Manag 50  OnTrack DM      93  Amoeba          e1  DOS access<br> b  W95 FAT32       51  OnTrack DM6 Aux 94  Amoeba BBT      e3  DOS R/O<br> c  W95 FAT32 (LBA) 52  CP/M            9f  BSD/OS          e4  SpeedStor<br> e  W95 FAT16 (LBA) 53  OnTrack DM6 Aux a0  IBM Thinkpad hi eb  BeOS fs<br> f  W95 Ext’d (LBA) 54  OnTrackDM6      a5  FreeBSD         ee  EFI GPT<br>10  OPUS            55  EZ-Drive        a6  OpenBSD         ef  EFI (FAT-12/16/<br>11  Hidden FAT12    56  Golden Bow      a7  NeXTSTEP        f0  Linux/PA-RISC b<br>12  Compaq diagnost 5c  Priam Edisk     a8  Darwin UFS      f1  SpeedStor<br>14  Hidden FAT16 &lt;3 61  SpeedStor       a9  NetBSD          f4  SpeedStor<br>16  Hidden FAT16    63  GNU HURD or Sys ab  Darwin boot     f2  DOS secondary<br>17  Hidden HPFS/NTF 64  Novell Netware  b7  BSDI fs         fd  Linux raid auto<br>18  AST SmartSleep  65  Novell Netware  b8  BSDI swap       fe  LANstep<br>1b  Hidden W95 FAT3 70  DiskSecure Mult bb  Boot Wizard hid ff  BBT<br>1c  Hidden W95 FAT3 75  PC/IX<br>Hex code (type L to list codes):</span>

 

<span class="highlight">&lt;– 8e</span>

<br>

<span class="system">Changed system type of partition 1 to 8e (Linux LVM)  </span>

Command (m for help): 

<span class="highlight">&lt;– w</span>

<br>

<span class="system">The partition table has been altered!  </span>

Calling ioctl() to re-read partition table.<br>
Syncing disks.

Now we do the same for the hard disks 

<span class="system">/dev/sdc</span>

 - 

<span class="system">/dev/sde</span>

:

fdisk /dev/sdc<br>
fdisk /dev/sdd<br>
fdisk /dev/sde

Then run

fdisk -l

again. The output should look like this:

server1:~# fdisk -l

Disk /dev/sda: 21.4 GB, 21474836480 bytes<br>
255 heads, 63 sectors/track, 2610 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Device Boot Start End Blocks Id System<br>
/dev/sda1 * 1 18 144553+ 83 Linux<br>
/dev/sda2 19 2450 19535040 83 Linux<br>
/dev/sda4 2451 2610 1285200 82 Linux swap / Solaris

Disk /dev/sdb: 85.8 GB, 85899345920 bytes<br>
255 heads, 63 sectors/track, 10443 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Device Boot Start End Blocks Id System<br>
/dev/sdb1 1 3040 24418768+ 8e Linux LVM

Disk /dev/sdc: 85.8 GB, 85899345920 bytes<br>
255 heads, 63 sectors/track, 10443 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Device Boot Start End Blocks Id System<br>
/dev/sdc1 1 3040 24418768+ 8e Linux LVM

Disk /dev/sdd: 85.8 GB, 85899345920 bytes<br>
255 heads, 63 sectors/track, 10443 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Device Boot Start End Blocks Id System<br>
/dev/sdd1 1 3040 24418768+ 8e Linux LVM

Disk /dev/sde: 85.8 GB, 85899345920 bytes<br>
255 heads, 63 sectors/track, 10443 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Device Boot Start End Blocks Id System<br>
/dev/sde1 1 3040 24418768+ 8e Linux LVM

Disk /dev/sdf: 85.8 GB, 85899345920 bytes<br>
255 heads, 63 sectors/track, 10443 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Disk /dev/sdf doesn't contain a valid partition table

Now we prepare our new partitions for LVM:

pvcreate /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1

server1:~# pvcreate /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1<br>
Physical volume "/dev/sdb1" successfully created<br>
Physical volume "/dev/sdc1" successfully created<br>
Physical volume "/dev/sdd1" successfully created<br>
Physical volume "/dev/sde1" successfully created

Let's revert this last action for training purposes:

pvremove /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1

server1:~# pvremove /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1<br>
Labels on physical volume "/dev/sdb1" successfully wiped<br>
Labels on physical volume "/dev/sdc1" successfully wiped<br>
Labels on physical volume "/dev/sdd1" successfully wiped<br>
Labels on physical volume "/dev/sde1" successfully wiped

Then run

pvcreate /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1

again:

server1:~# pvcreate /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1<br>
Physical volume "/dev/sdb1" successfully created<br>
Physical volume "/dev/sdc1" successfully created<br>
Physical volume "/dev/sdd1" successfully created<br>
Physical volume "/dev/sde1" successfully created

Now run

pvdisplay

to learn about the current state of your physical volumes:

server1:~# pvdisplay<br>
-- NEW Physical volume --<br>
PV Name /dev/sdb1<br>
VG Name<br>
PV Size 23.29 GB<br>
Allocatable NO<br>
PE Size (KByte) 0<br>
Total PE 0<br>
Free PE 0<br>
Allocated PE 0<br>
PV UUID G8lu2L-Hij1-NVde-sOKc-OoVI-fadg-Jd1vyU

-- NEW Physical volume --<br>
PV Name /dev/sdc1<br>
VG Name<br>
PV Size 23.29 GB<br>
Allocatable NO<br>
PE Size (KByte) 0<br>
Total PE 0<br>
Free PE 0<br>
Allocated PE 0<br>
PV UUID 40GJyh-IbsI-pzhn-TDRq-PQ3l-3ut0-AVSE4B

-- NEW Physical volume --<br>
PV Name /dev/sdd1<br>
VG Name<br>
PV Size 23.29 GB<br>
Allocatable NO<br>
PE Size (KByte) 0<br>
Total PE 0<br>
Free PE 0<br>
Allocated PE 0<br>
PV UUID 4mU63D-4s26-uL00-r0pO-Q0hP-mvQR-2YJN5B

-- NEW Physical volume --<br>
PV Name /dev/sde1<br>
VG Name<br>
PV Size 23.29 GB<br>
Allocatable NO<br>
PE Size (KByte) 0<br>
Total PE 0<br>
Free PE 0<br>
Allocated PE 0<br>
PV UUID 3upcZc-4eS2-h4r4-iBKK-gZJv-AYt3-EKdRK6

![](https://www.howtoforge.com/images/pdficon_small.png) [view as pdf](https://www.howtoforge.com/subscription/) | ![](https://www.howtoforge.com/images/print.gif) [print](https://www.howtoforge.com/subscription/)

--------------------------------------------------------------------------------

Now let's create our volume group 

<span class="system">fileserver</span>

 and add 

<span class="system">/dev/sdb1</span>

 - 

<span class="system">/dev/sde1</span>

 to it:

vgcreate fileserver /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1

server1:~# vgcreate fileserver /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1<br>
Volume group "fileserver" successfully created

Let's learn about our volume groups:

vgdisplay

server1:~# vgdisplay<br>
-- Volume group --<br>
VG Name fileserver<br>
System ID<br>
Format lvm2<br>
Metadata Areas 4<br>
Metadata Sequence No 1<br>
VG Access read/write<br>
VG Status resizable<br>
MAX LV 0<br>
Cur LV 0<br>
Open LV 0<br>
Max PV 0<br>
Cur PV 4<br>
Act PV 4<br>
VG Size 93.14 GB<br>
PE Size 4.00 MB<br>
Total PE 23844<br>
Alloc PE / Size 0 / 0<br>
Free PE / Size 23844 / 93.14 GB<br>
VG UUID 3Y1WVF-BLET-QkKs-Qnrs-SZxI-wrNO-dTqhFP

Another command to learn about our volume groups:

vgscan

server1:~# vgscan<br>
Reading all physical volumes. This may take a while...<br>
Found volume group "fileserver" using metadata type lvm2

For training purposes let's rename our volumegroup 

<span class="system">fileserver</span>

 into 

<span class="system">data</span>

:

vgrename fileserver data

server1:~# vgrename fileserver data<br>
Volume group "fileserver" successfully renamed to "data"

Let's run 

<span class="system">vgdisplay</span>

 and 

<span class="system">vgscan</span>

 again to see if the volume group has been renamed:

vgdisplay

server1:~# vgdisplay<br>
-- Volume group --<br>
VG Name data<br>
System ID<br>
Format lvm2<br>
Metadata Areas 4<br>
Metadata Sequence No 2<br>
VG Access read/write<br>
VG Status resizable<br>
MAX LV 0<br>
Cur LV 0<br>
Open LV 0<br>
Max PV 0<br>
Cur PV 4<br>
Act PV 4<br>
VG Size 93.14 GB<br>
PE Size 4.00 MB<br>
Total PE 23844<br>
Alloc PE / Size 0 / 0<br>
Free PE / Size 23844 / 93.14 GB<br>
VG UUID 3Y1WVF-BLET-QkKs-Qnrs-SZxI-wrNO-dTqhFP

vgscan

server1:~# vgscan<br>
Reading all physical volumes. This may take a while...<br>
Found volume group "data" using metadata type lvm2

Now let's delete our volume group 

<span class="system">data</span>

:

vgremove data

server1:~# vgremove data<br>
Volume group "data" successfully removed

vgdisplay

No output this time:

server1:~# vgdisplay

vgscan

server1:~# vgscan<br>
Reading all physical volumes. This may take a while...

Let's create our volume group 

<span class="system">fileserver</span>

 again:

vgcreate fileserver /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1

server1:~# vgcreate fileserver /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1<br>
Volume group "fileserver" successfully created

Next we create our logical volumes 

<span class="system">share</span>

 (40GB), 

<span class="system">backup</span>

 (5GB), and 

<span class="system">media</span>

 (1GB) in the volume group 

<span class="system">fileserver</span>

. Together they use a little less than 50% of the available space (that way we can make use of RAID1 later on):

lvcreate –name share –size 40G fileserver

server1:~# lvcreate –name share –size 40G fileserver<br>
Logical volume "share" created

lvcreate –name backup –size 5G fileserver

server1:~# lvcreate –name backup –size 5G fileserver<br>
Logical volume "backup" created

lvcreate –name media –size 1G fileserver

server1:~# lvcreate –name media –size 1G fileserver<br>
Logical volume "media" created

Let's get an overview of our logical volumes:

lvdisplay

server1:~# lvdisplay<br>
-- Logical volume --<br>
LV Name /dev/fileserver/share<br>
VG Name fileserver<br>
LV UUID 280Mup-H9aa-sn0S-AXH3-04cP-V6p9-lfoGgJ<br>
LV Write Access read/write<br>
LV Status available<br>
# open 0<br>
LV Size 40.00 GB<br>
Current LE 10240<br>
Segments 2<br>
Allocation inherit<br>
Read ahead sectors 0<br>
Block device 253:0

-- Logical volume --<br>
LV Name /dev/fileserver/backup<br>
VG Name fileserver<br>
LV UUID zZeuKg-Dazh-aZMC-Aa99-KUSt-J6ET-KRe0cD<br>
LV Write Access read/write<br>
LV Status available<br>
# open 0<br>
LV Size 5.00 GB<br>
Current LE 1280<br>
Segments 1<br>
Allocation inherit<br>
Read ahead sectors 0<br>
Block device 253:1

-- Logical volume --<br>
LV Name /dev/fileserver/media<br>
VG Name fileserver<br>
LV UUID usfvrv-BC92-3pFH-2NW0-2N3e-6ERQ-4Sj7YS<br>
LV Write Access read/write<br>
LV Status available<br>
# open 0<br>
LV Size 1.00 GB<br>
Current LE 256<br>
Segments 1<br>
Allocation inherit<br>
Read ahead sectors 0<br>
Block device 253:2

lvscan

server1:~# lvscan<br>
ACTIVE '/dev/fileserver/share' [40.00 GB] inherit<br>
ACTIVE '/dev/fileserver/backup' [5.00 GB] inherit<br>
ACTIVE '/dev/fileserver/media' [1.00 GB] inherit

For training purposes we rename our logical volume 

<span class="system">media</span>

 into 

<span class="system">films</span>

:

lvrename fileserver media films

server1:~# lvrename fileserver media films<br>
Renamed "media" to "films" in volume group "fileserver"

lvdisplay

server1:~# lvdisplay<br>
-- Logical volume --<br>
LV Name /dev/fileserver/share<br>
VG Name fileserver<br>
LV UUID 280Mup-H9aa-sn0S-AXH3-04cP-V6p9-lfoGgJ<br>
LV Write Access read/write<br>
LV Status available<br>
# open 0<br>
LV Size 40.00 GB<br>
Current LE 10240<br>
Segments 2<br>
Allocation inherit<br>
Read ahead sectors 0<br>
Block device 253:0

-- Logical volume --<br>
LV Name /dev/fileserver/backup<br>
VG Name fileserver<br>
LV UUID zZeuKg-Dazh-aZMC-Aa99-KUSt-J6ET-KRe0cD<br>
LV Write Access read/write<br>
LV Status available<br>
# open 0<br>
LV Size 5.00 GB<br>
Current LE 1280<br>
Segments 1<br>
Allocation inherit<br>
Read ahead sectors 0<br>
Block device 253:1

-- Logical volume --<br>
LV Name /dev/fileserver/films<br>
VG Name fileserver<br>
LV UUID usfvrv-BC92-3pFH-2NW0-2N3e-6ERQ-4Sj7YS<br>
LV Write Access read/write<br>
LV Status available<br>
# open 0<br>
LV Size 1.00 GB<br>
Current LE 256<br>
Segments 1<br>
Allocation inherit<br>
Read ahead sectors 0<br>
Block device 253:2

lvscan

server1:~# lvscan<br>
ACTIVE '/dev/fileserver/share' [40.00 GB] inherit<br>
ACTIVE '/dev/fileserver/backup' [5.00 GB] inherit<br>
ACTIVE '/dev/fileserver/films' [1.00 GB] inherit

Next let's delete the logical volume 

<span class="system">films</span>

:

lvremove /dev/fileserver/films

<span class="system">server1:~# lvremove /dev/fileserver/films<br>Do you really want to remove active logical volume “films”? [y/n]:</span>

 

<span class="highlight">&lt;– y</span>

<br>

<span class="system">  Logical volume “films” successfully removed</span>

We create the logical volume 

<span class="system">media</span>

 again:

lvcreate –name media –size 1G fileserver

server1:~# lvcreate –name media –size 1G fileserver<br>
Logical volume "media" created

Now let's enlarge 

<span class="system">media</span>

 from 1GB to 1.5GB:

lvextend -L1.5G /dev/fileserver/media

server1:~# lvextend -L1.5G /dev/fileserver/media<br>
Extending logical volume media to 1.50 GB<br>
Logical volume media successfully resized

Let's shrink it to 1GB again:

lvreduce -L1G /dev/fileserver/media

<span class="system">server1:~# lvreduce -L1G /dev/fileserver/media<br>  WARNING: Reducing active logical volume to 1.00 GB<br>  THIS MAY DESTROY YOUR DATA (filesystem etc.)<br>Do you really want to reduce media? [y/n]:</span>

 

<span class="highlight">&lt;– y</span>

<br>

<span class="system">  Reducing logical volume media to 1.00 GB<br>  Logical volume media successfully resized</span>

![](https://www.howtoforge.com/images/pdficon_small.png) [view as pdf](https://www.howtoforge.com/subscription/) | ![](https://www.howtoforge.com/images/print.gif) [print](https://www.howtoforge.com/subscription/)

--------------------------------------------------------------------------------

Until now we have three logical volumes, but we don't have any filesystems in them, and without a filesystem we can't save anything in them. Therefore we create an ext3 filesystem in 

<span class="system">share</span>

, an xfs filesystem in 

<span class="system">backup</span>

, and a reiserfs filesystem in 

<span class="system">media</span>

:

mkfs.ext3 /dev/fileserver/share

server1:~# mkfs.ext3 /dev/fileserver/share<br>
mke2fs 1.40-WIP (14-Nov-2006)<br>
Filesystem label=<br>
OS type: Linux<br>
Block size=4096 (log=2)<br>
Fragment size=4096 (log=2)<br>
5242880 inodes, 10485760 blocks<br>
524288 blocks (5.00%) reserved for the super user<br>
First data block=0<br>
Maximum filesystem blocks=0<br>
320 block groups<br>
32768 blocks per group, 32768 fragments per group<br>
16384 inodes per group<br>
Superblock backups stored on blocks:<br>
32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,<br>
4096000, 7962624

Writing inode tables: done<br>
Creating journal (32768 blocks): done<br>
Writing superblocks and filesystem accounting information: done

This filesystem will be automatically checked every 23 mounts or<br>
180 days, whichever comes first. Use tune2fs -c or -i to override.

mkfs.xfs /dev/fileserver/backup

server1:~# mkfs.xfs /dev/fileserver/backup<br>
meta-data=/dev/fileserver/backup isize=256 agcount=8, agsize=163840 blks<br>
= sectsz=512 attr=0<br>
data = bsize=4096 blocks=1310720, imaxpct=25<br>
= sunit=0 swidth=0 blks, unwritten=1<br>
naming =version 2 bsize=4096<br>
log =internal log bsize=4096 blocks=2560, version=1<br>
= sectsz=512 sunit=0 blks<br>
realtime =none extsz=65536 blocks=0, rtextents=0

mkfs.reiserfs /dev/fileserver/media

server1:~# mkfs.reiserfs /dev/fileserver/media<br>
mkfs.reiserfs 3.6.19 (2003 www.namesys.com)

A pair of credits:<br>
Alexander Lyamin keeps our hardware running, and was very generous to our<br>
project in many little ways.

Chris Mason wrote the journaling code for V3, which was enormously more useful<br>
to users than just waiting until we could create a wandering log filesystem as<br>
Hans would have unwisely done without him.<br>
Jeff Mahoney optimized the bitmap scanning code for V3, and performed the big<br>
endian cleanups.

Guessing about desired format.. Kernel 2.6.17-2-486 is running.<br>
Format 3.6 with standard journal<br>
Count of blocks on the device: 262144<br>
Number of blocks consumed by mkreiserfs formatting process: 8219<br>
Blocksize: 4096<br>
Hash function used to sort names: "r5"<br>
Journal Size 8193 blocks (first block 18)<br>
Journal Max transaction length 1024<br>
inode generation number: 0<br>
UUID: 2bebf750-6e05-47b2-99b6-916fa7ea5398<br>
ATTENTION: YOU SHOULD REBOOT AFTER FDISK!<br>
ALL DATA WILL BE LOST ON '/dev/fileserver/media'!<br>
Continue (y/n):y<br>
Initializing journal - 0%....20%....40%....60%....80%....100%<br>
Syncing..ok

Tell your friends to use a kernel based on 2.4.18 or later, and especially not a<br>
kernel based on 2.4.9, when you use reiserFS. Have fun.

ReiserFS is successfully created on /dev/fileserver/media.

Now we are ready to mount our logical volumes. I want to mount 

<span class="system">share</span>

 in 

<span class="system">/var/share</span>

, 

<span class="system">backup</span>

 in 

<span class="system">/var/backup</span>

, and 

<span class="system">media</span>

 in 

<span class="system">/var/media</span>

, therefore we must create these directories first:

mkdir /var/media /var/backup /var/share

Now we can mount our logical volumes:

mount /dev/fileserver/share /var/share<br>
mount /dev/fileserver/backup /var/backup<br>
mount /dev/fileserver/media /var/media

Now run

df -h

You should see your logical volumes in the output:

server1:~# df -h<br>
Filesystem Size Used Avail Use% Mounted on<br>
/dev/sda2 19G 665M 17G 4% /<br>
tmpfs 78M 0 78M 0% /lib/init/rw<br>
udev 10M 88K 10M 1% /dev<br>
tmpfs 78M 0 78M 0% /dev/shm<br>
/dev/sda1 137M 17M 114M 13% /boot<br>
/dev/mapper/fileserver-share<br>
40G 177M 38G 1% /var/share<br>
/dev/mapper/fileserver-backup<br>
5.0G 144K 5.0G 1% /var/backup<br>
/dev/mapper/fileserver-media<br>
1.0G 33M 992M 4% /var/media

Congratulations, you've just set up your first LVM system! You can now write to and read from 

<span class="system">/var/share</span>

, 

<span class="system">/var/backup</span>

, and 

<span class="system">/var/media</span>

 as usual.

We have mounted our logical volumes manually, but of course we'd like to have them mounted automatically when the system boots. Therefore we modify 

<span class="system">/etc/fstab</span>

:

mv /etc/fstab /etc/fstab_orig<br>
cat /dev/null > /etc/fstab

vi /etc/fstab

Put the following into it:

| ```
# /etc/fstab: static file system information.
#
# <file system> <mount point> <type> <options> <dump> <pass>
proc /proc proc defaults 0 0
/dev/sda2 / ext3 defaults,errors=remount-ro 0 1
/dev/sda1 /boot ext3 defaults 0 2
/dev/hdc /media/cdrom0 udf,iso9660 user,noauto 0 0
/dev/fd0 /media/floppy0 auto rw,user,noauto 0 0
/dev/fileserver/share /var/share ext3 rw,noatime 0 0
/dev/fileserver/backup /var/backup xfs rw,noatime 0 0
/dev/fileserver/media /var/media reiserfs rw,noatime 0 0```
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

If you compare it to our backup of the original file, 

<span class="system">/etc/fstab_orig</span>

, you will notice that we added the lines:

/dev/fileserver/share /var/share ext3 rw,noatime 0 0<br>
/dev/fileserver/backup /var/backup xfs rw,noatime 0 0<br>
/dev/fileserver/media /var/media reiserfs rw,noatime 0 0

Now we reboot the system:

shutdown -r now

After the system has come up again, run

df -h

again. It should still show our logical volumes in the output:

server1:~# df -h<br>
Filesystem Size Used Avail Use% Mounted on<br>
/dev/sda2 19G 665M 17G 4% /<br>
tmpfs 78M 0 78M 0% /lib/init/rw<br>
udev 10M 88K 10M 1% /dev<br>
tmpfs 78M 0 78M 0% /dev/shm<br>
/dev/sda1 137M 17M 114M 13% /boot<br>
/dev/mapper/fileserver-share<br>
40G 177M 38G 1% /var/share<br>
/dev/mapper/fileserver-backup<br>
5.0G 144K 5.0G 1% /var/backup<br>
/dev/mapper/fileserver-media<br>
1.0G 33M 992M 4% /var/media

![](https://www.howtoforge.com/images/pdficon_small.png) [view as pdf](https://www.howtoforge.com/subscription/) | ![](https://www.howtoforge.com/images/print.gif) [print](https://www.howtoforge.com/subscription/)

--------------------------------------------------------------------------------

## On this page

1. [4 Resize Logical Volumes And Their Filesystems](https://www.howtoforge.com#-resize-logical-volumes-and-their-filesystems)

## 4 Resize Logical Volumes And Their Filesystems

In this chapter we will learn how to resize our logical volume 

<span class="system">share</span>

 which has an ext3 filesystem. (I will show how to resize logical volumes with xfs and reiserfs filesystems further down this tutorial.)

First we must unmount it:

umount /var/share

<span class="system">share</span>

 should not be listed anymore in the

df -h

output:

server1:~# df -h<br>
Filesystem Size Used Avail Use% Mounted on<br>
/dev/sda2 19G 665M 17G 4% /<br>
tmpfs 78M 0 78M 0% /lib/init/rw<br>
udev 10M 88K 10M 1% /dev<br>
tmpfs 78M 0 78M 0% /dev/shm<br>
/dev/sda1 137M 17M 114M 13% /boot<br>
/dev/mapper/fileserver-backup<br>
5.0G 144K 5.0G 1% /var/backup<br>
/dev/mapper/fileserver-media<br>
1.0G 33M 992M 4% /var/media

Now let's enlarge 

<span class="system">share</span>

 from 40GB to 50GB:

lvextend -L50G /dev/fileserver/share

server1:~# lvextend -L50G /dev/fileserver/share<br>
Extending logical volume share to 50.00 GB<br>
Logical volume share successfully resized

Until now we have enlarged only 

<span class="system">share</span>

, but not the ext3 filesystem on 

<span class="system">share</span>

. This is what we do now:

e2fsck -f /dev/fileserver/share

server1:~# e2fsck -f /dev/fileserver/share<br>
e2fsck 1.40-WIP (14-Nov-2006)<br>
Pass 1: Checking inodes, blocks, and sizes<br>
Pass 2: Checking directory structure<br>
Pass 3: Checking directory connectivity<br>
Pass 4: Checking reference counts<br>
Pass 5: Checking group summary information<br>
/dev/fileserver/share: 11/5242880 files (9.1% non-contiguous), 209588/10485760 blocks

Make a note of the total amount of blocks (

<span class="system">10485760</span>

) because we need it when we shrink 

<span class="system">share</span>

 later on.

resize2fs /dev/fileserver/share

server1:~# resize2fs /dev/fileserver/share<br>
resize2fs 1.40-WIP (14-Nov-2006)<br>
Resizing the filesystem on /dev/fileserver/share to 13107200 (4k) blocks.<br>
The filesystem on /dev/fileserver/share is now 13107200 blocks long.

Let's mount 

<span class="system">share</span>

:

mount /dev/fileserver/share /var/share

and in the

df -h

output share should now have 50GB instead of 40:

server1:~# df -h<br>
Filesystem Size Used Avail Use% Mounted on<br>
/dev/sda2 19G 665M 17G 4% /<br>
tmpfs 78M 0 78M 0% /lib/init/rw<br>
udev 10M 88K 10M 1% /dev<br>
tmpfs 78M 0 78M 0% /dev/shm<br>
/dev/sda1 137M 17M 114M 13% /boot<br>
/dev/mapper/fileserver-backup<br>
5.0G 144K 5.0G 1% /var/backup<br>
/dev/mapper/fileserver-media<br>
1.0G 33M 992M 4% /var/media<br>
/dev/mapper/fileserver-share<br>
50G 180M 47G 1% /var/share

Shrinking a logical volume is the other way round: first we must shrink the filesystem before we reduce the logical volume's size. Let's shrink share to 40GB again:

umount /var/share

df -h

server1:~# df -h<br>
Filesystem Size Used Avail Use% Mounted on<br>
/dev/sda2 19G 665M 17G 4% /<br>
tmpfs 78M 0 78M 0% /lib/init/rw<br>
udev 10M 88K 10M 1% /dev<br>
tmpfs 78M 0 78M 0% /dev/shm<br>
/dev/sda1 137M 17M 114M 13% /boot<br>
/dev/mapper/fileserver-backup<br>
5.0G 144K 5.0G 1% /var/backup<br>
/dev/mapper/fileserver-media<br>
1.0G 33M 992M 4% /var/media

e2fsck -f /dev/fileserver/share

server1:~# e2fsck -f /dev/fileserver/share<br>
e2fsck 1.40-WIP (14-Nov-2006)<br>
Pass 1: Checking inodes, blocks, and sizes<br>
Pass 2: Checking directory structure<br>
Pass 3: Checking directory connectivity<br>
Pass 4: Checking reference counts<br>
Pass 5: Checking group summary information<br>
/dev/fileserver/share: 11/6553600 files (9.1% non-contiguous), 251733/13107200 blocks

When resizing an ext3 filesystem to a certain size (instead of all available space), 

<span class="system">resize2fs</span>

 takes the number of blocks as argument (you can as well specify the new size in MB, etc. See

man resize2fs

for more details). From our previous operation we know the 40GB equals 

<span class="system">10485760</span>

 blocks so we run

resize2fs /dev/fileserver/share 10485760

server1:~# resize2fs /dev/fileserver/share 10485760<br>
resize2fs 1.40-WIP (14-Nov-2006)<br>
Resizing the filesystem on /dev/fileserver/share to 10485760 (4k) blocks.<br>
The filesystem on /dev/fileserver/share is now 10485760 blocks long.

We've shrinked the filesystem, now we must shrink the logical volume, too:

lvreduce -L40G /dev/fileserver/share

<span class="system">server1:~# lvreduce -L40G /dev/fileserver/share<br>  WARNING: Reducing active logical volume to 40.00 GB<br>  THIS MAY DESTROY YOUR DATA (filesystem etc.)<br>Do you really want to reduce share? [y/n]:</span>

 

<span class="highlight">&lt;– y</span>

<br>

<span class="system">  Reducing logical volume share to 40.00 GB<br>  Logical volume share successfully resized</span>

We can ignore the warning that data might be destroyed because we have shrinked the filesystem before.

Let's mount 

<span class="system">share</span>

 again:

mount /dev/fileserver/share /var/share

The output of

df -h

should now look like this:

server1:~# df -h<br>
Filesystem Size Used Avail Use% Mounted on<br>
/dev/sda2 19G 665M 17G 4% /<br>
tmpfs 78M 0 78M 0% /lib/init/rw<br>
udev 10M 88K 10M 1% /dev<br>
tmpfs 78M 0 78M 0% /dev/shm<br>
/dev/sda1 137M 17M 114M 13% /boot<br>
/dev/mapper/fileserver-backup<br>
5.0G 144K 5.0G 1% /var/backup<br>
/dev/mapper/fileserver-media<br>
1.0G 33M 992M 4% /var/media<br>
/dev/mapper/fileserver-share<br>
40G 177M 38G 1% /var/share

![](https://www.howtoforge.com/images/pdficon_small.png) [view as pdf](https://www.howtoforge.com/subscription/) | ![](https://www.howtoforge.com/images/print.gif) [print](https://www.howtoforge.com/subscription/)

--------------------------------------------------------------------------------

## On this page

1. [5 Adding A Hard Drive And Removing Another One](https://www.howtoforge.com#-adding-a-hard-drive-and-removing-another-one)

## 5 Adding A Hard Drive And Removing Another One

We haven't used 

<span class="system">/dev/sdf</span>

 until now. We will now create the partition 

<span class="system">/dev/sdf1</span>

 (25GB) and add that to our 

<span class="system">fileserver</span>

 volume group.

fdisk /dev/sdf

<span class="system">server1:~# fdisk /dev/sdf<br>Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel<br>Building a new DOS disklabel. Changes will remain in memory only,<br>until you decide to write them. After that, of course, the previous<br>content won’t be recoverable.  </span>

The number of cylinders for this disk is set to 10443.<br>
There is nothing wrong with that, but this is larger than 1024,<br>
and could in certain setups cause problems with:<br>
1) software that runs at boot time (e.g., old versions of LILO)<br>
2) booting and partitioning software from other OSs<br>
(e.g., DOS FDISK, OS/2 FDISK)<br>
Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): 

<span class="highlight">&lt;– m</span>

<br>

<span class="system">Command action<br>   a   toggle a bootable flag<br>   b   edit bsd disklabel<br>   c   toggle the dos compatibility flag<br>   d   delete a partition<br>   l   list known partition types<br>   m   print this menu<br>   n   add a new partition<br>   o   create a new empty DOS partition table<br>   p   print the partition table<br>   q   quit without saving changes<br>   s   create a new empty Sun disklabel<br>   t   change a partition’s system id<br>   u   change display/entry units<br>   v   verify the partition table<br>   w   write table to disk and exit<br>   x   extra functionality (experts only)  </span>

Command (m for help): 

<span class="highlight">&lt;– n</span>

<br>

<span class="system">Command action<br>   e   extended<br>   p   primary partition (1-4)</span>

<br>

<span class="highlight">&lt;– p</span>

<br>

<span class="system">Partition number (1-4):</span>

 

<span class="highlight">&lt;– 1</span>

<br>

<span class="system">First cylinder (1-10443, default 1):<br>Using default value 1<br>Last cylinder or +size or +sizeM or +sizeK (1-10443, default 10443):</span>

 

<span class="highlight">&lt;– +25000M</span>

<span class="system">Command (m for help):</span>

 

<span class="highlight">&lt;– t</span>

<br>

<span class="system">Selected partition 1<br>Hex code (type L to list codes):</span>

 

<span class="highlight">&lt;– 8e</span>

<br>

<span class="system">Changed system type of partition 1 to 8e (Linux LVM)  </span>

Command (m for help): 

<span class="highlight">&lt;– w</span>

<br>

<span class="system">The partition table has been altered!  </span>

Calling ioctl() to re-read partition table.<br>
Syncing disks.

Let's prepare 

<span class="system">/dev/sdf1</span>

 for LVM:

pvcreate /dev/sdf1

server1:~# pvcreate /dev/sdf1<br>
Physical volume "/dev/sdf1" successfully created

Add 

<span class="system">/dev/sdf1</span>

 to our 

<span class="system">fileserver</span>

 volume group:

vgextend fileserver /dev/sdf1

Run

vgdisplay

<span class="system">VG Size</span>

 should now be bigger than before:

server1:~# vgdisplay<br>
-- Volume group --<br>
VG Name fileserver<br>
System ID<br>
Format lvm2<br>
Metadata Areas 5<br>
Metadata Sequence No 12<br>
VG Access read/write<br>
VG Status resizable<br>
MAX LV 0<br>
Cur LV 3<br>
Open LV 3<br>
Max PV 0<br>
Cur PV 5<br>
Act PV 5<br>
VG Size 116.43 GB<br>
PE Size 4.00 MB<br>
Total PE 29805<br>
Alloc PE / Size 11776 / 46.00 GB<br>
Free PE / Size 18029 / 70.43 GB<br>
VG UUID iWr1Vk-7h7J-hLRL-SHbx-3p87-Rq47-L1GyEO

That's it. 

<span class="system">/dev/sdf1</span>

 has been added to the fileserver volume group.

Now let's remove 

<span class="system">/dev/sdb1</span>

. Before we do this, we must copy all data on it to 

<span class="system">/dev/sdf1</span>

:

pvmove /dev/sdb1 /dev/sdf1

This can take some minutes:

server1:~# pvmove /dev/sdb1 /dev/sdf1<br>
/dev/sdb1: Moved: 1.9%<br>
/dev/sdb1: Moved: 3.8%<br>
/dev/sdb1: Moved: 5.8%<br>
/dev/sdb1: Moved: 7.8%<br>
/dev/sdb1: Moved: 9.7%<br>
/dev/sdb1: Moved: 11.6%<br>
/dev/sdb1: Moved: 13.6%<br>
/dev/sdb1: Moved: 15.6%<br>
/dev/sdb1: Moved: 17.5%<br>
/dev/sdb1: Moved: 19.4%<br>
/dev/sdb1: Moved: 21.4%<br>
[...]<br>
/dev/sdb1: Moved: 85.7%<br>
/dev/sdb1: Moved: 87.7%<br>
/dev/sdb1: Moved: 89.7%<br>
/dev/sdb1: Moved: 91.7%<br>
/dev/sdb1: Moved: 93.6%<br>
/dev/sdb1: Moved: 95.5%<br>
/dev/sdb1: Moved: 97.5%<br>
/dev/sdb1: Moved: 99.4%<br>
/dev/sdb1: Moved: 100.0%

Next we remove 

<span class="system">/dev/sdb1</span>

 from the 

<span class="system">fileserver</span>

 volume group:

vgreduce fileserver /dev/sdb1

server1:~# vgreduce fileserver /dev/sdb1<br>
Removed "/dev/sdb1" from volume group "fileserver"

vgdisplay

server1:~# vgdisplay<br>
-- Volume group --<br>
VG Name fileserver<br>
System ID<br>
Format lvm2<br>
Metadata Areas 4<br>
Metadata Sequence No 16<br>
VG Access read/write<br>
VG Status resizable<br>
MAX LV 0<br>
Cur LV 3<br>
Open LV 3<br>
Max PV 0<br>
Cur PV 4<br>
Act PV 4<br>
VG Size 93.14 GB<br>
PE Size 4.00 MB<br>
Total PE 23844<br>
Alloc PE / Size 11776 / 46.00 GB<br>
Free PE / Size 12068 / 47.14 GB<br>
VG UUID iWr1Vk-7h7J-hLRL-SHbx-3p87-Rq47-L1GyEO

Then we run

pvremove /dev/sdb1

<span class="system">/dev/sdb1</span>

 shouldn't be listed as a physical volume anymore:

pvdisplay

server1:~# pvdisplay<br>
-- Physical volume --<br>
PV Name /dev/sdc1<br>
VG Name fileserver<br>
PV Size 23.29 GB / not usable 0<br>
Allocatable yes<br>
PE Size (KByte) 4096<br>
Total PE 5961<br>
Free PE 1682<br>
Allocated PE 4279<br>
PV UUID 40GJyh-IbsI-pzhn-TDRq-PQ3l-3ut0-AVSE4B

-- Physical volume --<br>
PV Name /dev/sdd1<br>
VG Name fileserver<br>
PV Size 23.29 GB / not usable 0<br>
Allocatable yes<br>
PE Size (KByte) 4096<br>
Total PE 5961<br>
Free PE 4681<br>
Allocated PE 1280<br>
PV UUID 4mU63D-4s26-uL00-r0pO-Q0hP-mvQR-2YJN5B

-- Physical volume --<br>
PV Name /dev/sde1<br>
VG Name fileserver<br>
PV Size 23.29 GB / not usable 0<br>
Allocatable yes<br>
PE Size (KByte) 4096<br>
Total PE 5961<br>
Free PE 5705<br>
Allocated PE 256<br>
PV UUID 3upcZc-4eS2-h4r4-iBKK-gZJv-AYt3-EKdRK6

-- Physical volume --<br>
PV Name /dev/sdf1<br>
VG Name fileserver<br>
PV Size 23.29 GB / not usable 0<br>
Allocatable yes (but full)<br>
PE Size (KByte) 4096<br>
Total PE 5961<br>
Free PE 0<br>
Allocated PE 5961<br>
PV UUID 1xgo2I-SBjj-0MAz-lmDu-OLZ1-3NdO-mLkS20

You could now remove 

<span class="system">/dev/sdb</span>

 from the system (if this was a real system and not a virtual machine).

![](https://www.howtoforge.com/images/pdficon_small.png) [view as pdf](https://www.howtoforge.com/subscription/) | ![](https://www.howtoforge.com/images/print.gif) [print](https://www.howtoforge.com/subscription/)

--------------------------------------------------------------------------------

## On this page

1. [6 Return To The System's Original State](https://www.howtoforge.com#-return-to-the-systems-original-state)
2. [7 LVM On RAID1](https://www.howtoforge.com#-lvm-on-raid)

## 6 Return To The System's Original State

In this chapter we will undo all changes from the previous chapters to return to the system's original state. This is just for training purposes so that you learn how to undo an LVM setup.

First we must unmount our logical volumes:

umount /var/share<br>
umount /var/backup<br>
umount /var/media

df -h

server1:~# df -h<br>
Filesystem Size Used Avail Use% Mounted on<br>
/dev/sda2 19G 665M 17G 4% /<br>
tmpfs 78M 0 78M 0% /lib/init/rw<br>
udev 10M 92K 10M 1% /dev<br>
tmpfs 78M 0 78M 0% /dev/shm<br>
/dev/sda1 137M 17M 114M 13% /boot

Then we delete each of them:

lvremove /dev/fileserver/share

<span class="system">server1:~# lvremove /dev/fileserver/share<br>Do you really want to remove active logical volume “share”? [y/n]:</span>

 

<span class="highlight">&lt;– y</span>

<br>

<span class="system">  Logical volume “share” successfully removed</span>

lvremove /dev/fileserver/backup

<span class="system">server1:~# lvremove /dev/fileserver/backup<br>Do you really want to remove active logical volume “backup”? [y/n]:</span>

 

<span class="highlight">&lt;– y</span>

<br>

<span class="system">  Logical volume “backup” successfully removed</span>

lvremove /dev/fileserver/media

<span class="system">server1:~# lvremove /dev/fileserver/media<br>Do you really want to remove active logical volume “media”? [y/n]:</span>

 

<span class="highlight">&lt;– y</span>

<br>

<span class="system">  Logical volume “media” successfully removed</span>

Next we remove the volume group 

<span class="system">fileserver</span>

:

vgremove fileserver

server1:~# vgremove fileserver<br>
Volume group "fileserver" successfully removed

Finally we do this:

pvremove /dev/sdc1 /dev/sdd1 /dev/sde1 /dev/sdf1

server1:~# pvremove /dev/sdc1 /dev/sdd1 /dev/sde1 /dev/sdf1<br>
Labels on physical volume "/dev/sdc1" successfully wiped<br>
Labels on physical volume "/dev/sdd1" successfully wiped<br>
Labels on physical volume "/dev/sde1" successfully wiped<br>
Labels on physical volume "/dev/sdf1" successfully wiped

vgdisplay

server1:~# vgdisplay<br>
No volume groups found

pvdisplay

should display nothing at all:

server1:~# pvdisplay

Now we must undo our changes in 

<span class="system">/etc/fstab</span>

 to avoid that the system tries to mount non-existing devices. Fortunately we have made a backup of the original file that we can copy back now:

mv /etc/fstab_orig /etc/fstab

Reboot the system:

shutdown -r now

Afterwards the output of

df -h

should look like this:

server1:~# df -h<br>
Filesystem Size Used Avail Use% Mounted on<br>
/dev/sda2 19G 666M 17G 4% /<br>
tmpfs 78M 0 78M 0% /lib/init/rw<br>
udev 10M 92K 10M 1% /dev<br>
tmpfs 78M 0 78M 0% /dev/shm<br>
/dev/sda1 137M 17M 114M 13% /boot

Now the system is like it was in the beginning (except that the partitions 

<span class="system">/dev/sdb1</span>

 - 

<span class="system">/dev/sdf1</span>

 still exist - you could delete them with 

<span class="system">fdisk</span>

 but we don't do this now - as well as the directories 

<span class="system">/var/share</span>

, 

<span class="system">/var/backup</span>

, and 

<span class="system">/var/media</span>

 which we also don't delete).

## 7 LVM On RAID1

In this chapter we will set up LVM again and move it to a RAID1 array to guarantee for high-availability. In the end this should look like this:

![](https://www.howtoforge.com/images/lvm/lvm_scheme_full_raid1.png)

This means we will make the RAID array 

<span class="system">/dev/md0</span>

 from the partitions 

<span class="system">/dev/sdb1</span>

 + 

<span class="system">/dev/sdc1</span>

, and the RAID array 

<span class="system">/dev/md1</span>

 from the partitions 

<span class="system">/dev/sdd1</span>

 + 

<span class="system">/dev/sde1</span>

. 

<span class="system">/dev/md0</span>

 and 

<span class="system">/dev/md1</span>

 will then be the physical volumes for LVM.

Before we come to that, we set up LVM as before:

pvcreate /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1<br>
vgcreate fileserver /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1<br>
lvcreate –name share –size 40G fileserver<br>
lvcreate –name backup –size 5G fileserver<br>
lvcreate –name media –size 1G fileserver

mkfs.ext3 /dev/fileserver/share<br>
mkfs.xfs /dev/fileserver/backup<br>
mkfs.reiserfs /dev/fileserver/media

Then we mount our logical volumes:

mount /dev/fileserver/share /var/share<br>
mount /dev/fileserver/backup /var/backup<br>
mount /dev/fileserver/media /var/media

The output of

df -h

should now look like this:

server1:~# df -h<br>
Filesystem Size Used Avail Use% Mounted on<br>
/dev/sda2 19G 666M 17G 4% /<br>
tmpfs 78M 0 78M 0% /lib/init/rw<br>
udev 10M 92K 10M 1% /dev<br>
tmpfs 78M 0 78M 0% /dev/shm<br>
/dev/sda1 137M 17M 114M 13% /boot<br>
/dev/mapper/fileserver-share<br>
40G 177M 38G 1% /var/share<br>
/dev/mapper/fileserver-backup<br>
5.0G 144K 5.0G 1% /var/backup<br>
/dev/mapper/fileserver-media<br>
1.0G 33M 992M 4% /var/media

Now we must move the contents of 

<span class="system">/dev/sdc1</span>

 and 

<span class="system">/dev/sde1</span>

 (

<span class="system">/dev/sdc1</span>

 is the second partition of our future 

<span class="system">/dev/md0</span>

, 

<span class="system">/dev/sde1</span>

 the second partition of our future 

<span class="system">/dev/md1</span>

) to the remaining partitions, because we will afterwards remove them from LVM and format them with the type 

<span class="system">fd</span>

 (Linux RAID autodetect) and move them to 

<span class="system">/dev/md0</span>

 resp. 

<span class="system">/dev/md1</span>

.

modprobe dm-mirror<br>
pvmove /dev/sdc1

vgreduce fileserver /dev/sdc1<br>
pvremove /dev/sdc1

pvdisplay

server1:~# pvdisplay<br>
-- Physical volume --<br>
PV Name /dev/sdb1<br>
VG Name fileserver<br>
PV Size 23.29 GB / not usable 0<br>
Allocatable yes (but full)<br>
PE Size (KByte) 4096<br>
Total PE 5961<br>
Free PE 0<br>
Allocated PE 5961<br>
PV UUID USDJyG-VDM2-r406-OjQo-h3eb-c9Mp-4nvnvu

-- Physical volume --<br>
PV Name /dev/sdd1<br>
VG Name fileserver<br>
PV Size 23.29 GB / not usable 0<br>
Allocatable yes<br>
PE Size (KByte) 4096<br>
Total PE 5961<br>
Free PE 4681<br>
Allocated PE 1280<br>
PV UUID qdEB5d-389d-O5UA-Kbwv-mn1y-74FY-4zublN

-- Physical volume --<br>
PV Name /dev/sde1<br>
VG Name fileserver<br>
PV Size 23.29 GB / not usable 0<br>
Allocatable yes<br>
PE Size (KByte) 4096<br>
Total PE 5961<br>
Free PE 1426<br>
Allocated PE 4535<br>
PV UUID 4vL1e0-sr2M-awGd-qDJm-ZrC9-wuxW-2lEqp2

pvmove /dev/sde1

vgreduce fileserver /dev/sde1<br>
pvremove /dev/sde1

pvdisplay

server1:~# pvdisplay<br>
-- Physical volume --<br>
PV Name /dev/sdb1<br>
VG Name fileserver<br>
PV Size 23.29 GB / not usable 0<br>
Allocatable yes (but full)<br>
PE Size (KByte) 4096<br>
Total PE 5961<br>
Free PE 0<br>
Allocated PE 5961<br>
PV UUID USDJyG-VDM2-r406-OjQo-h3eb-c9Mp-4nvnvu

-- Physical volume --<br>
PV Name /dev/sdd1<br>
VG Name fileserver<br>
PV Size 23.29 GB / not usable 0<br>
Allocatable yes<br>
PE Size (KByte) 4096<br>
Total PE 5961<br>
Free PE 146<br>
Allocated PE 5815<br>
PV UUID qdEB5d-389d-O5UA-Kbwv-mn1y-74FY-4zublN

Now we format 

<span class="system">/dev/sdc1</span>

 with the type 

<span class="system">fd</span>

 (Linux RAID autodetect):

fdisk /dev/sdc

<span class="system">server1:~# fdisk /dev/sdc  </span>

The number of cylinders for this disk is set to 10443.<br>
There is nothing wrong with that, but this is larger than 1024,<br>
and could in certain setups cause problems with:<br>
1) software that runs at boot time (e.g., old versions of LILO)<br>
2) booting and partitioning software from other OSs<br>
(e.g., DOS FDISK, OS/2 FDISK)

Command (m for help): 

<span class="highlight">&lt;– m</span>

<br>

<span class="system">Command action<br>   a   toggle a bootable flag<br>   b   edit bsd disklabel<br>   c   toggle the dos compatibility flag<br>   d   delete a partition<br>   l </span>
list known partition types<br>
m print this menu<br>
n add a new partition<br>
o create a new empty DOS partition table<br>
p print the partition table<br>
q quit without saving changes<br>
s create a new empty Sun disklabel<br>
t change a partition's system id<br>
u change display/entry units<br>
v verify the partition table<br>
w write table to disk and exit<br>
x extra functionality (experts only)

Command (m for help): 

<span class="highlight">&lt;– t</span>

<br>

<span class="system">Selected partition 1<br>Hex code (type L to list codes):</span>

 

<span class="highlight">&lt;– L</span>

<span class="system">0  Empty           1e  Hidden W95 FAT1 80  Old Minix       be  Solaris boot<br> 1  FAT12           24  NEC DOS         81  Minix / old Lin bf  Solaris<br> 2  XENIX root      39  Plan 9          82  Linux swap / So c1  DRDOS/sec (FAT-<br> 3  XENIX usr       3c  PartitionMagic  83  Linux           c4  DRDOS/sec (FAT-<br> 4  FAT16 &lt;32M      40  Venix 80286     84  OS/2 hidden C:  c6  DRDOS/sec (FAT-<br> 5  Extended        41  PPC PReP Boot   85  Linux extended  c7  Syrinx<br> 6  FAT16           42  SFS             86  NTFS volume set da  Non-FS data<br> 7  HPFS/NTFS       4d  QNX4.x          87  NTFS volume set db  CP/M / CTOS / .<br> 8  AIX             4e  QNX4.x 2nd part 88  Linux plaintext de  Dell Utility<br> 9  AIX bootable    4f  QNX4.x 3rd part 8e  Linux LVM       df  BootIt<br> a  OS/2 Boot Manag 50  OnTrack DM      93  Amoeba          e1  DOS access<br> b  W95 FAT32       51  OnTrack DM6 Aux 94  Amoeba BBT      e3  DOS R/O<br> c  W95 FAT32 (LBA) 52  CP/M            9f  BSD/OS          e4  SpeedStor<br> e  W95 FAT16 (LBA) 53  OnTrack DM6 Aux a0  IBM Thinkpad hi eb  BeOS fs<br> f  W95 Ext’d (LBA) 54  OnTrackDM6      a5  FreeBSD         ee  EFI GPT<br>10  OPUS            55  EZ-Drive        a6  OpenBSD         ef  EFI (FAT-12/16/<br>11  Hidden FAT12    56  Golden Bow      a7  NeXTSTEP        f0  Linux/PA-RISC b<br>12  Compaq diagnost 5c  Priam Edisk     a8  Darwin UFS      f1  SpeedStor<br>14  Hidden FAT16 &lt;3 61  SpeedStor       a9  NetBSD          f4  SpeedStor<br>16  Hidden FAT16    63  GNU HURD or Sys ab  Darwin boot     f2  DOS secondary<br>17  Hidden HPFS/NTF 64  Novell Netware  b7  BSDI fs         fd  Linux raid auto<br>18  AST SmartSleep  65  Novell Netware  b8  BSDI swap       fe  LANstep<br>1b  Hidden W95 FAT3 70  DiskSecure Mult bb  Boot Wizard hid ff  BBT<br>1c  Hidden W95 FAT3 75  PC/IX<br>Hex code (type L to list codes):</span>

 

<span class="highlight">&lt;– fd</span>

<br>

<span class="system">Changed system type of partition 1 to fd (Linux raid autodetect)  </span>

Command (m for help): 

<span class="highlight">&lt;– w</span>

<br>

<span class="system">The partition table has been altered!  </span>

Calling ioctl() to re-read partition table.<br>
Syncing disks.

Now do the same with 

<span class="system">/dev/sde1</span>

:

fdisk /dev/sde

The output of

fdisk -l

should now look like this:

server1:~# fdisk -l

Disk /dev/sda: 21.4 GB, 21474836480 bytes<br>
255 heads, 63 sectors/track, 2610 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Device Boot Start End Blocks Id System<br>
/dev/sda1 * 1 18 144553+ 83 Linux<br>
/dev/sda2 19 2450 19535040 83 Linux<br>
/dev/sda4 2451 2610 1285200 82 Linux swap / Solaris

Disk /dev/sdb: 85.8 GB, 85899345920 bytes<br>
255 heads, 63 sectors/track, 10443 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Device Boot Start End Blocks Id System<br>
/dev/sdb1 1 3040 24418768+ 8e Linux LVM

Disk /dev/sdc: 85.8 GB, 85899345920 bytes<br>
255 heads, 63 sectors/track, 10443 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Device Boot Start End Blocks Id System<br>
/dev/sdc1 1 3040 24418768+ fd Linux raid autodetect

Disk /dev/sdd: 85.8 GB, 85899345920 bytes<br>
255 heads, 63 sectors/track, 10443 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Device Boot Start End Blocks Id System<br>
/dev/sdd1 1 3040 24418768+ 8e Linux LVM

Disk /dev/sde: 85.8 GB, 85899345920 bytes<br>
255 heads, 63 sectors/track, 10443 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Device Boot Start End Blocks Id System<br>
/dev/sde1 1 3040 24418768+ fd Linux raid autodetect

Disk /dev/sdf: 85.8 GB, 85899345920 bytes<br>
255 heads, 63 sectors/track, 10443 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Device Boot Start End Blocks Id System<br>
/dev/sdf1 1 3040 24418768+ 8e Linux LVM

Next we add 

<span class="system">/dev/sdc1</span>

 to 

<span class="system">/dev/md0</span>

 and 

<span class="system">/dev/sde1</span>

 to 

<span class="system">/dev/md1</span>

. Because the second nodes (

<span class="system">/dev/sdb1</span>

 and 

<span class="system">/dev/sdd1</span>

) are not ready yet, we must specify 

<span class="system">missing</span>

 in the following commands:

mdadm –create /dev/md0 –auto=yes -l 1 -n 2 /dev/sdc1 missing

server1:~# mdadm –create /dev/md0 –auto=yes -l 1 -n 2 /dev/sdc1 missing<br>
mdadm: array /dev/md0 started.

mdadm –create /dev/md1 –auto=yes -l 1 -n 2 /dev/sde1 missing

server1:~# mdadm –create /dev/md1 –auto=yes -l 1 -n 2 /dev/sde1 missing<br>
mdadm: array /dev/md1 started.

![](https://www.howtoforge.com/images/pdficon_small.png) [view as pdf](https://www.howtoforge.com/subscription/) | ![](https://www.howtoforge.com/images/print.gif) [print](https://www.howtoforge.com/subscription/)

--------------------------------------------------------------------------------

Afterwards we prepare 

<span class="system">/dev/md0</span>

 and 

<span class="system">/dev/md1</span>

 for LVM:

pvcreate /dev/md0 /dev/md1

server1:~# pvcreate /dev/md0 /dev/md1<br>
Physical volume "/dev/md0" successfully created<br>
Physical volume "/dev/md1" successfully created

and extend our 

<span class="system">fileserver</span>

 volume group:

vgextend fileserver /dev/md0 /dev/md1

server1:~# vgextend fileserver /dev/md0 /dev/md1<br>
Volume group "fileserver" successfully extended

The outputs of

pvdisplay

and

vgdisplay

should look like this:

server1:~# pvdisplay<br>
-- Physical volume --<br>
PV Name /dev/sdb1<br>
VG Name fileserver<br>
PV Size 23.29 GB / not usable 0<br>
Allocatable yes (but full)<br>
PE Size (KByte) 4096<br>
Total PE 5961<br>
Free PE 0<br>
Allocated PE 5961<br>
PV UUID USDJyG-VDM2-r406-OjQo-h3eb-c9Mp-4nvnvu

-- Physical volume --<br>
PV Name /dev/sdd1<br>
VG Name fileserver<br>
PV Size 23.29 GB / not usable 0<br>
Allocatable yes<br>
PE Size (KByte) 4096<br>
Total PE 5961<br>
Free PE 146<br>
Allocated PE 5815<br>
PV UUID qdEB5d-389d-O5UA-Kbwv-mn1y-74FY-4zublN

-- Physical volume --<br>
PV Name /dev/md0<br>
VG Name fileserver<br>
PV Size 23.29 GB / not usable 0<br>
Allocatable yes<br>
PE Size (KByte) 4096<br>
Total PE 5961<br>
Free PE 5961<br>
Allocated PE 0<br>
PV UUID 7JHUXF-1R2p-OjbJ-X1OT-uaeg-gWRx-H6zx3P

-- Physical volume --<br>
PV Name /dev/md1<br>
VG Name fileserver<br>
PV Size 23.29 GB / not usable 0<br>
Allocatable yes<br>
PE Size (KByte) 4096<br>
Total PE 5961<br>
Free PE 5961<br>
Allocated PE 0<br>
PV UUID pwQ5AJ-RwVK-EebA-0Z13-d27d-2IdP-HqT5RW

server1:~# vgdisplay<br>
-- Volume group --<br>
VG Name fileserver<br>
System ID<br>
Format lvm2<br>
Metadata Areas 4<br>
Metadata Sequence No 14<br>
VG Access read/write<br>
VG Status resizable<br>
MAX LV 0<br>
Cur LV 3<br>
Open LV 3<br>
Max PV 0<br>
Cur PV 4<br>
Act PV 4<br>
VG Size 93.14 GB<br>
PE Size 4.00 MB<br>
Total PE 23844<br>
Alloc PE / Size 11776 / 46.00 GB<br>
Free PE / Size 12068 / 47.14 GB<br>
VG UUID dQDEHT-kNHf-UjRm-rmJ3-OUYx-9G1t-aVskI1

Now we move the contents of 

<span class="system">/dev/sdb1</span>

 to 

<span class="system">/dev/md0</span>

 and the contents of 

<span class="system">/dev/sdd1</span>

 to 

<span class="system">/dev/md1</span>

, then we remove 

<span class="system">/dev/sdb1</span>

 and 

<span class="system">/dev/sdd1</span>

 from LVM:

pvmove /dev/sdb1 /dev/md0

pvmove /dev/sdd1 /dev/md1

vgreduce fileserver /dev/sdb1 /dev/sdd1<br>
pvremove /dev/sdb1 /dev/sdd1

Now only 

<span class="system">/dev/md0</span>

 and 

<span class="system">/dev/md1</span>

 should be left as physical volumes:

pvdisplay

server1:~# pvdisplay<br>
-- Physical volume --<br>
PV Name /dev/md0<br>
VG Name fileserver<br>
PV Size 23.29 GB / not usable 0<br>
Allocatable yes (but full)<br>
PE Size (KByte) 4096<br>
Total PE 5961<br>
Free PE 0<br>
Allocated PE 5961<br>
PV UUID 7JHUXF-1R2p-OjbJ-X1OT-uaeg-gWRx-H6zx3P

-- Physical volume --<br>
PV Name /dev/md1<br>
VG Name fileserver<br>
PV Size 23.29 GB / not usable 0<br>
Allocatable yes<br>
PE Size (KByte) 4096<br>
Total PE 5961<br>
Free PE 146<br>
Allocated PE 5815<br>
PV UUID pwQ5AJ-RwVK-EebA-0Z13-d27d-2IdP-HqT5RW

Now we format 

<span class="system">/dev/sdb1</span>

 with 

<span class="system">fd</span>

 (Linux RAID autodetect):

fdisk /dev/sdb

<span class="system">server1:~# fdisk /dev/sdb  </span>

The number of cylinders for this disk is set to 32635.<br>
There is nothing wrong with that, but this is larger than 1024,<br>
and could in certain setups cause problems with:<br>
1) software that runs at boot time (e.g., old versions of LILO)<br>
2) booting and partitioning software from other OSs<br>
(e.g., DOS FDISK, OS/2 FDISK)

Command (m for help): 

<span class="highlight">&lt;– m</span>

<br>

<span class="system">Command action<br>   a   toggle a bootable flag<br>   b   edit bsd disklabel<br>   c   toggle the dos compatibility flag<br>   d   delete a partition<br>   l   list known partition types<br>   m   print this menu<br>   n   add a new partition<br>   o   create a new empty DOS partition table<br>   p   print the partition table<br>   q   quit without saving changes<br>   s   create a new empty Sun disklabel<br>   t   change a partition’s system id<br>   u   change display/entry units<br>   v   verify the partition table<br>   w   write table to disk and exit<br>   x   extra functionality (experts only)  </span>

Command (m for help): 

<span class="highlight">&lt;– t</span>

<br>

<span class="system">Selected partition 1<br>Hex code (type L to list codes):</span>

 

<span class="highlight">&lt;– fd</span>

<br>

<span class="system">Changed system type of partition 1 to fd (Linux raid autodetect)  </span>

Command (m for help): 

<span class="highlight">&lt;– w</span>

<br>

<span class="system">The partition table has been altered!  </span>

Calling ioctl() to re-read partition table.<br>
Syncing disks.

Do the same with 

<span class="system">/dev/sdd1</span>

:

fdisk /dev/sdd

Next add 

<span class="system">/dev/sdb1</span>

 to 

<span class="system">/dev/md0</span>

 and 

<span class="system">/dev/sdd1</span>

 to 

<span class="system">/dev/md1</span>

:

mdadm –manage /dev/md0 –add /dev/sdb1

server1:~# mdadm –manage /dev/md0 –add /dev/sdb1<br>
mdadm: added /dev/sdb1

mdadm –manage /dev/md1 –add /dev/sdd1

server1:~# mdadm –manage /dev/md1 –add /dev/sdd1<br>
mdadm: added /dev/sdd1

Now the two RAID arrays will be synchronized. This will take some time, you can check with

cat /proc/mdstat

when the process is finished. The output looks like this for an unfinished process:

server1:~# cat /proc/mdstat<br>
Personalities : [linear] [multipath] [raid0] [raid1] [raid5] [raid4] [raid6] [raid10]<br>
md1 : active raid1 sdd1[2] sde1[0]<br>
24418688 blocks [2/1] [U_]<br>
[=>...................] recovery = 6.4% (1586560/24418688) finish=1.9min speed=198320K/sec

md0 : active raid1 sdb1[2] sdc1[0]<br>
24418688 blocks [2/1] [U_]<br>
[==>..................] recovery = 10.5% (2587264/24418688) finish=2.8min speed=129363K/sec

unused devices: <none>

and like this when the process is finished:

server1:~# cat /proc/mdstat<br>
Personalities : [linear] [multipath] [raid0] [raid1] [raid5] [raid4] [raid6] [raid10]<br>
md1 : active raid1 sdd1[1] sde1[0]<br>
24418688 blocks [2/2] [UU]

md0 : active raid1 sdb1[1] sdc1[0]<br>
24418688 blocks [2/2] [UU]

unused devices: <none>

If you have a look at 

<span class="system">PV Size</span>

 in the output of

pvdisplay

you will see that 

<span class="system">2 10_years_of_git_an_interview_with_git_creator_linus_torvalds.md accuracy_in_the_palm_of_your_hand.md a_clear_view_of_the_brain.md a_liberator_but_never_free.md a_look_at_alteras_opencl_sdk_for_fpgas.md an_important_step_in_artificial_intelligence.md an_introduction_to_matlab_metaprogramming.md announcing_the_nulecule_specification_for_composite_applications.md a_quick_introduction_to_consul__scotts_weblog__the_weblog_of_an_it_pro_specializing_in_virtualization_networking_open_source_and_cloud_computing.md a_thorough_introduction_guide_to_docker_containers.md a_tmux_crash_course.md bayesian_inference_of_a_binomial_proportion__the_analytical_approach.md browser_monitoring_for_githubcom.md can_ecstasy_replace_xanax.md circles_sines_and_signals__introduction.md creating_kvm_machines_with_boxgrinder_and_vmbuilder.md creating_shazam_in_java.md crossplatform_development_with_nwjs__tuts_code_tutorial.md data_archeology.md data.json data_ml david_deutsch_explains_why_it’s_good_to_be_wrong.md deku_how_we_built_our_functional_alternative_to_react.md designing_a_state_machine_without_conditionals.md devops discovering_and_monitoring_hardware_in_linux.md docker does_long_term_use_of_psychiatric_drugs_cause_more_harm_than_good.md drug_perks_up_old_muscles_and_aging_brains.md electronics_for_the_javascript_developer_using_wifi_and_bluetooth_le.md elements_of_scale_composing_and_scaling_data_platforms.md elements_of_scale_composing_and_scaling_data platforms.md embedded eve_online_how_a_virtual_world_went_to_the_edge_of_apocalypse_and_back__simon_parkin.md explaining_a_cornerstone_of_game_theory_john_nash’s_equilibrium.md five_funny_little_linux_network_testers_and_monitors.md fixing_engineerings_loyalty_and_longevity_problem.md gaming github go golden_gate_bridge_builders_ate_special_meals_to_prevent_dizziness.md google_systems_guru_explains_why_containers_are_the_future_of_computing.md hackers_build_a_new_tor_client_designed_to_beat_the_nsa.md highavailability_storage_with_halvm.md historyfuture_proof_fingerprint.md how_i_got_converted_to_gmo_food.md how_shazam_works.md how_to_be_polite_—_the_message.md how_to_get_your_very_own_rstudio_server_and_shiny_server_with_digitalocean.md how_to_turn_your_pc_or_other_device_into_a_retro_arcade_with_lakka.md html implementing_a_trustless_security_solution_with_hardware_wallets_and_multisignature.md inside_the_mind_that_built_google_brain_on_life_creativity_and_failure.md ira_glass_public_radio_can_capitalize_on_its_popularity_without_selling_out_its_mission.md itspritecom.md javascript join_the_engineering_leisure_class.md just_don’t_hire_0x_engineers.md linux linux_commandline_video_editing.md make.md make_your_own_injection_molding_machine__make_diy_projects_howtos_electronics_crafts_and_ideas_for_makers.md mark_twain__helen_keller’s_special_friendship_he_treated_me_not_as_a_freak_but_as_a_person_dealing_with_great_difficulties.md mbuntu_y_macbuntu_transformation_pack_available_for_ubuntu_1504_vivid_vervet.md meeting_a_silk_road_drugs_boss.md mobile no_death_no_taxes.md null.md old on_edgar_allan_poe_by_marilynne_robinson.md optimize_python_with closures.md phd_is_the_doctoral_thesis_obsolete.md pisoc_learn_to_create.md png polymatheia__persistent_vector_performance_summarised.md popular_electric_brain_stimulation_method_tdcs_is_detrimental_to_iq_scores_study.md programming robust_landmarkbased_audio_fingerprinting.md roll_your_own_customized_ubuntu_with_uck.md running_a_small_docker_swarm_cluster.md rust_discovery_or_how_i_figure_things_out.md safe_connection.md scantool__obdii_car_diagnostic_software_for_linux.md science smart_regulation_for_smart_drugs.md social software_install_guide.md stigler_diet_–_how_i_became_a_data_scientist_despite_having_been_a_math_major.md tfidf_is_about_what_matters.md the_business_economics_and_opportunity_of_opensource_data_science.md the_entrepreneur_disrupting_the_tech_world_by_spilling_her_company’s_secrets.md the_identity_underground.md the_man_who_became_big bird.md the_most_common_illegal_job_interview_questions_you_should_watch_out_for.md theory.md the_psychology_of_ux.md the_roland_tr808_the_drum_machine_that_revolutionised_music.md the_shazam_effect.md the_sound_of_ted_a_case_for_distaste.md the_unreasonable_effectiveness_of_recurrent_neural_networks.md tidy understanding_c_by_learning_assembly.md url_to_filename.csv weave_is_kinda_slow.md web_dev what_richard_branson_and_30_amazing_entrepreneurs_taught_me_in_7_days_on_necker_island.md who_funds_the_future.md why_we_age_–_part_i_the_evolution_of_aging.md wireless_raspberry_pi_speaker.md 23.29GB = 46.58GB</span>

 are available, however only 

<span class="system">40GB (share) + 5GB (backup) + 1GB (media) = 46GB</span>

 are used which means we could extend one of our logical devices with about 0.5GB. I've already shown how to extend an ext3 logical volume (

<span class="system">share</span>

), so we will resize 

<span class="system">media</span>

 now which uses reiserfs. reiserfs filesystems can be resized without unmounting:

lvextend -L1.5G /dev/fileserver/media

server1:~# lvextend -L1.5G /dev/fileserver/media<br>
Extending logical volume media to 1.50 GB<br>
Logical volume media successfully resized

resize_reiserfs /dev/fileserver/media

server1:~# resize_reiserfs /dev/fileserver/media<br>
resize_reiserfs 3.6.19 (2003 www.namesys.com)

resize_reiserfs: On-line resizing finished successfully.

The output of

df -h

looks like this:

server1:~# df -h<br>
Filesystem Size Used Avail Use% Mounted on<br>
/dev/sda2 19G 666M 17G 4% /<br>
tmpfs 78M 0 78M 0% /lib/init/rw<br>
udev 10M 92K 10M 1% /dev<br>
tmpfs 78M 0 78M 0% /dev/shm<br>
/dev/sda1 137M 17M 114M 13% /boot<br>
/dev/mapper/fileserver-share<br>
40G 177M 38G 1% /var/share<br>
/dev/mapper/fileserver-backup<br>
5.0G 144K 5.0G 1% /var/backup<br>
/dev/mapper/fileserver-media<br>
1.5G 33M 1.5G 3% /var/media

If we want our logical volumes to be mounted automatically at boot time, we must modify 

<span class="system">/etc/fstab</span>

 again (like in chapter 3):

mv /etc/fstab /etc/fstab_orig<br>
cat /dev/null > /etc/fstab

vi /etc/fstab

Put the following into it:

| ```
# /etc/fstab: static file system information.
#
# <file system> <mount point> <type> <options> <dump> <pass>
proc /proc proc defaults 0 0
/dev/sda2 / ext3 defaults,errors=remount-ro 0 1
/dev/sda1 /boot ext3 defaults 0 2
/dev/hdc /media/cdrom0 udf,iso9660 user,noauto 0 0
/dev/fd0 /media/floppy0 auto rw,user,noauto 0 0
/dev/fileserver/share /var/share ext3 rw,noatime 0 0
/dev/fileserver/backup /var/backup xfs rw,noatime 0 0
/dev/fileserver/media /var/media reiserfs rw,noatime 0 0```
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

If you compare it to our backup of the original file, 

<span class="system">/etc/fstab_orig</span>

, you will notice that we added the lines:

/dev/fileserver/share /var/share ext3 rw,noatime 0 0<br>
/dev/fileserver/backup /var/backup xfs rw,noatime 0 0<br>
/dev/fileserver/media /var/media reiserfs rw,noatime 0 0

Now we reboot the system:

shutdown -r now

After the system has come up again, run

df -h

again. It should still show our logical volumes in the output:

server1:~# df -h<br>
Filesystem Size Used Avail Use% Mounted on<br>
/dev/sda2 19G 666M 17G 4% /<br>
tmpfs 78M 0 78M 0% /lib/init/rw<br>
udev 10M 100K 10M 1% /dev<br>
tmpfs 78M 0 78M 0% /dev/shm<br>
/dev/sda1 137M 17M 114M 13% /boot<br>
/dev/mapper/fileserver-share<br>
40G 177M 38G 1% /var/share<br>
/dev/mapper/fileserver-backup<br>
5.0G 144K 5.0G 1% /var/backup<br>
/dev/mapper/fileserver-media<br>
1.5G 33M 1.5G 3% /var/media

Now we are finished with our LVM on RAID1 setup.

![](https://www.howtoforge.com/images/pdficon_small.png) [view as pdf](https://www.howtoforge.com/subscription/) | ![](https://www.howtoforge.com/images/print.gif) [print](https://www.howtoforge.com/subscription/)

--------------------------------------------------------------------------------

1. [8 Replacing The Hard Disks With Bigger Ones](https://www.howtoforge.com#-replacing-the-hard-disks-with-bigger-ones)

We are currently using four hard disks with a size of 25GB each (at least we are acting like that). Now let's assume this isn't enough anymore, and we need more space in our RAID setup. Therefore we will replace our 25GB hard disks with 80GB hard disks (in fact we will still use the current hard disks, but use their full capacity now - in the real life you would replace your old, small hard disks with new, bigger ones).

The procedure is as follows: first we remove 

<span class="system">/dev/sdb</span>

 and 

<span class="system">/dev/sdd</span>

 from the RAID arrays, replace them with bigger hard disks, put them back into the RAID arrays, and then we do the same again with 

<span class="system">/dev/sdc</span>

 and 

<span class="system">/dev/sde</span>

.

First we mark 

<span class="system">/dev/sdb1</span>

 as failed:

mdadm –manage /dev/md0 –fail /dev/sdb1

server1:~# mdadm –manage /dev/md0 –fail /dev/sdb1<br>
mdadm: set /dev/sdb1 faulty in /dev/md0

The output of

cat /proc/mdstat

looks now like this:

server1:~# cat /proc/mdstat<br>
Personalities : [linear] [multipath] [raid0] [raid1] [raid5] [raid4] [raid6] [raid10]<br>
md0 : active raid1 sdc1[0] sdb1[2](F)<br>
24418688 blocks [2/1] [U_]

md1 : active raid1 sde1[0] sdd1[1]<br>
24418688 blocks [2/2] [UU]

unused devices: <none>

Then we remove 

<span class="system">/dev/sdb1</span>

 from the RAID array 

<span class="system">/dev/md0</span>

:

mdadm –manage /dev/md0 –remove /dev/sdb1

server1:~# mdadm –manage /dev/md0 –remove /dev/sdb1<br>
mdadm: hot removed /dev/sdb1

cat /proc/mdstat

server1:~# cat /proc/mdstat<br>
Personalities : [linear] [multipath] [raid0] [raid1] [raid5] [raid4] [raid6] [raid10]<br>
md0 : active raid1 sdc1[0]<br>
24418688 blocks [2/1] [U_]

md1 : active raid1 sde1[0] sdd1[1]<br>
24418688 blocks [2/2] [UU]

unused devices: <none>

Now we do the same with 

<span class="system">/dev/sdd1</span>

:

mdadm –manage /dev/md1 –fail /dev/sdd1

server1:~# mdadm –manage /dev/md1 –fail /dev/sdd1<br>
mdadm: set /dev/sdd1 faulty in /dev/md1

cat /proc/mdstat

server1:~# cat /proc/mdstat<br>
Personalities : [linear] [multipath] [raid0] [raid1] [raid5] [raid4] [raid6] [raid10]<br>
md0 : active raid1 sdc1[0]<br>
24418688 blocks [2/1] [U_]

md1 : active raid1 sde1[0] sdd1[2](F)<br>
24418688 blocks [2/1] [U_]

unused devices: <none>

mdadm –manage /dev/md1 –remove /dev/sdd1

server1:~# mdadm –manage /dev/md1 –remove /dev/sdd1<br>
mdadm: hot removed /dev/sdd1

cat /proc/mdstat

server1:~# cat /proc/mdstat<br>
Personalities : [linear] [multipath] [raid0] [raid1] [raid5] [raid4] [raid6] [raid10]<br>
md0 : active raid1 sdc1[0]<br>
24418688 blocks [2/1] [U_]

md1 : active raid1 sde1[0]<br>
24418688 blocks [2/1] [U_]

unused devices: <none>

On a real system you would now shut it down, pull out the 25GB 

<span class="system">/dev/sdb</span>

 and 

<span class="system">/dev/sdd </span>

 and replace them with 80GB ones. As I said before, we don't have to do this because all hard disks already have a capacity of 80GB.

Next we must format 

<span class="system">/dev/sdb</span>

 and 

<span class="system">/dev/sdd</span>

. We must create a 

<span class="system">/dev/sdb1</span>

 resp. 

<span class="system">/dev/sdd1</span>

 partition, type 

<span class="system">fd</span>

 (Linux RAID autodetect), size 25GB (the same settings as on the old hard disks), and a 

<span class="system">/dev/sdb2</span>

 resp. 

<span class="system">/dev/sdd2</span>

 partition, type 

<span class="system">fd</span>

, that cover the rest of the hard disks. As 

<span class="system">/dev/sdb1</span>

 and 

<span class="system">/dev/sdd1</span>

 are still present on our hard disks, we only have to create 

<span class="system">/dev/sdb2</span>

 and 

<span class="system">/dev/sdd2</span>

 in this special example.

fdisk /dev/sdb

<span class="system">server1:~# fdisk /dev/sdb  </span>

The number of cylinders for this disk is set to 10443.<br>
There is nothing wrong with that, but this is larger than 1024,<br>
and could in certain setups cause problems with:<br>
1) software that runs at boot time (e.g., old versions of LILO)<br>
2) booting and partitioning software from other OSs<br>
(e.g., DOS FDISK, OS/2 FDISK)

Command (m for help): 

<span class="highlight">&lt;– p</span>

<span class="system">Disk /dev/sdb: 85.8 GB, 85899345920 bytes<br>255 heads, 63 sectors/track, 10443 cylinders<br>Units = cylinders of 16065 * 512 = 8225280 bytes  </span>

Device Boot Start End Blocks Id System<br>
/dev/sdb1 1 3040 24418768+ fd Linux raid autodetect

Command (m for help): 

<span class="highlight">&lt;– n</span>

<br>

<span class="system">Command action<br>   e   extended<br>   p   primary partition (1-4)</span>

<br>

<span class="highlight">&lt;– p</span>

<br>

<span class="system">Partition number (1-4):</span>

 

<span class="highlight">&lt;– 2</span>

<br>

<span class="system">First cylinder (3041-10443, default 3041):</span>

 

<span class="highlight">&lt;– &lt;ENTER&gt;</span>

<br>

<span class="system">Using default value 3041<br>Last cylinder or +size or +sizeM or +sizeK (3041-10443, default 10443):</span>

 

<span class="highlight">&lt;– &lt;ENTER&gt;</span>

<br>

<span class="system">Using default value 10443  </span>

Command (m for help): 

<span class="highlight">&lt;– t</span>

<br>

<span class="system">Partition number (1-4):</span>

 

<span class="highlight">&lt;– 2</span>

<br>

<span class="system">Hex code (type L to list codes):</span>

 

<span class="system">&lt;– fd</span>

<br>

<span class="system">Changed system type of partition 2 to fd (Linux raid autodetect)  </span>

Command (m for help): 

<span class="highlight">&lt;– w</span>

<br>

<span class="system">The partition table has been altered!  </span>

Calling ioctl() to re-read partition table.<br>
Syncing disks.

Do the same for 

<span class="system">/dev/sdd</span>

:

fdisk /dev/sdd

The output of

fdisk -l

looks now like this:

server1:~# fdisk -l

Disk /dev/sda: 21.4 GB, 21474836480 bytes<br>
255 heads, 63 sectors/track, 2610 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Device Boot Start End Blocks Id System<br>
/dev/sda1 * 1 18 144553+ 83 Linux<br>
/dev/sda2 19 2450 19535040 83 Linux<br>
/dev/sda4 2451 2610 1285200 82 Linux swap / Solaris

Disk /dev/sdb: 85.8 GB, 85899345920 bytes<br>
255 heads, 63 sectors/track, 10443 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Device Boot Start End Blocks Id System<br>
/dev/sdb1 1 3040 24418768+ fd Linux raid autodetect<br>
/dev/sdb2 3041 10443 59464597+ fd Linux raid autodetect

Disk /dev/sdc: 85.8 GB, 85899345920 bytes<br>
255 heads, 63 sectors/track, 10443 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Device Boot Start End Blocks Id System<br>
/dev/sdc1 1 3040 24418768+ fd Linux raid autodetect

Disk /dev/sdd: 85.8 GB, 85899345920 bytes<br>
255 heads, 63 sectors/track, 10443 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Device Boot Start End Blocks Id System<br>
/dev/sdd1 1 3040 24418768+ fd Linux raid autodetect<br>
/dev/sdd2 3041 10443 59464597+ fd Linux raid autodetect

Disk /dev/sde: 85.8 GB, 85899345920 bytes<br>
255 heads, 63 sectors/track, 10443 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Device Boot Start End Blocks Id System<br>
/dev/sde1 1 3040 24418768+ fd Linux raid autodetect

Disk /dev/sdf: 85.8 GB, 85899345920 bytes<br>
255 heads, 63 sectors/track, 10443 cylinders<br>
Units = cylinders of 16065 * 512 = 8225280 bytes

Device Boot Start End Blocks Id System<br>
/dev/sdf1 1 3040 24418768+ 8e Linux LVM

Disk /dev/md1: 25.0 GB, 25004736512 bytes<br>
2 heads, 4 sectors/track, 6104672 cylinders<br>
Units = cylinders of 8 * 512 = 4096 bytes

Disk /dev/md1 doesn't contain a valid partition table

Disk /dev/md0: 25.0 GB, 25004736512 bytes<br>
2 heads, 4 sectors/track, 6104672 cylinders<br>
Units = cylinders of 8 * 512 = 4096 bytes

Disk /dev/md0 doesn't contain a valid partition table

Now we add 

<span class="system">/dev/sdb1</span>

 to 

<span class="system">/dev/md0</span>

 again and 

<span class="system">/dev/sdd1</span>

 to 

<span class="system">/dev/md1</span>

:

mdadm –manage /dev/md0 –add /dev/sdb1

server1:~# mdadm –manage /dev/md0 –add /dev/sdb1<br>
mdadm: re-added /dev/sdb1

mdadm –manage /dev/md1 –add /dev/sdd1

server1:~# mdadm –manage /dev/md1 –add /dev/sdd1<br>
mdadm: re-added /dev/sdd1

Now the contents of both RAID arrays will be synchronized. We must wait until this is finished before we can go on. We can check the status of the synchronization with

cat /proc/mdstat

The output looks like this during synchronization:

server1:~# cat /proc/mdstat<br>
Personalities : [linear] [multipath] [raid0] [raid1] [raid5] [raid4] [raid6] [raid10]<br>
md0 : active raid1 sdb1[1] sdc1[0]<br>
24418688 blocks [2/1] [U_]<br>
[=>...................] recovery = 9.9% (2423168/24418688) finish=2.8min speed=127535K/sec

md1 : active raid1 sdd1[1] sde1[0]<br>
24418688 blocks [2/1] [U_]<br>
[=>...................] recovery = 6.4% (1572096/24418688) finish=1.9min speed=196512K/sec

unused devices: <none>

and like this when it's finished:

server1:~# cat /proc/mdstat<br>
Personalities : [linear] [multipath] [raid0] [raid1] [raid5] [raid4] [raid6] [raid10]<br>
md0 : active raid1 sdb1[1] sdc1[0]<br>
24418688 blocks [2/2] [UU]

md1 : active raid1 sdd1[1] sde1[0]<br>
24418688 blocks [2/2] [UU]

unused devices: <none>

![](https://www.howtoforge.com/images/pdficon_small.png) [view as pdf](https://www.howtoforge.com/subscription/) | ![](https://www.howtoforge.com/images/print.gif) [print](https://www.howtoforge.com/subscription/)

--------------------------------------------------------------------------------

Now we do the same process again, this time replacing 

<span class="system">/dev/sdc</span>

 and 

<span class="system">/dev/sde</span>

:

mdadm –manage /dev/md0 –fail /dev/sdc1<br>
mdadm –manage /dev/md0 –remove /dev/sdc1<br>
mdadm –manage /dev/md1 –fail /dev/sde1<br>
mdadm –manage /dev/md1 –remove /dev/sde1

fdisk /dev/sdc<br>
fdisk /dev/sde

mdadm –manage /dev/md0 –add /dev/sdc1<br>
mdadm –manage /dev/md1 –add /dev/sde1

cat /proc/mdstat

Wait until the synchronization has finished.

Next we create the RAID arrays 

<span class="system">/dev/md2</span>

 from 

<span class="system">/dev/sdb2</span>

 and 

<span class="system">/dev/sdc2</span>

 as well as 

<span class="system">/dev/md3</span>

 from 

<span class="system">/dev/sdd2</span>

 and 

<span class="system">/dev/sde2</span>

.

mdadm –create /dev/md2 –auto=yes -l 1 -n 2 /dev/sdb2 /dev/sdc2

server1:~# mdadm –create /dev/md2 –auto=yes -l 1 -n 2 /dev/sdb2 /dev/sdc2<br>
mdadm: array /dev/md2 started.

mdadm –create /dev/md3 –auto=yes -l 1 -n 2 /dev/sdd2 /dev/sde2

server1:~# mdadm –create /dev/md3 –auto=yes -l 1 -n 2 /dev/sdd2 /dev/sde2<br>
mdadm: array /dev/md3 started.

The new RAID arrays must be synchronized before we go on, so you should check

cat /proc/mdstat

server1:~# cat /proc/mdstat<br>
Personalities : [linear] [multipath] [raid0] [raid1] [raid5] [raid4] [raid6] [raid10]<br>
md3 : active raid1 sde2[1] sdd2[0]<br>
59464512 blocks [2/2] [UU]<br>
[=>...................] resync = 5.1% (3044224/59464512) finish=5.5min speed=169123K/sec

md2 : active raid1 sdc2[1] sdb2[0]<br>
59464512 blocks [2/2] [UU]<br>
[=>...................] resync = 5.5% (3312512/59464512) finish=9.3min speed=100379K/sec

md0 : active raid1 sdc1[0] sdb1[1]<br>
24418688 blocks [2/2] [UU]

md1 : active raid1 sde1[0] sdd1[1]<br>
24418688 blocks [2/2] [UU]

unused devices: <none>

After the synchronization has finished, we prepare 

<span class="system">/dev/md2</span>

 and 

<span class="system">/dev/md3</span>

 for LVM:

pvcreate /dev/md2 /dev/md3

server1:~# pvcreate /dev/md2 /dev/md3<br>
Physical volume "/dev/md2" successfully created<br>
Physical volume "/dev/md3" successfully created

and add 

<span class="system">/dev/md2</span>

 and 

<span class="system">/dev/md3</span>

 to our 

<span class="system">fileserver</span>

 volume group:

vgextend fileserver /dev/md2 /dev/md3

server1:~# vgextend fileserver /dev/md2 /dev/md3<br>
Volume group "fileserver" successfully extended

Now let's run our 

<span class="system">*display</span>

 commands:

pvdisplay

server1:~# pvdisplay<br>
-- Physical volume --<br>
PV Name /dev/md0<br>
VG Name fileserver<br>
PV Size 23.29 GB / not usable 0<br>
Allocatable yes (but full)<br>
PE Size (KByte) 4096<br>
Total PE 5961<br>
Free PE 0<br>
Allocated PE 5961<br>
PV UUID 7JHUXF-1R2p-OjbJ-X1OT-uaeg-gWRx-H6zx3P

-- Physical volume --<br>
PV Name /dev/md1<br>
VG Name fileserver<br>
PV Size 23.29 GB / not usable 0<br>
Allocatable yes<br>
PE Size (KByte) 4096<br>
Total PE 5961<br>
Free PE 18<br>
Allocated PE 5943<br>
PV UUID pwQ5AJ-RwVK-EebA-0Z13-d27d-2IdP-HqT5RW

-- Physical volume --<br>
PV Name /dev/md2<br>
VG Name fileserver<br>
PV Size 56.71 GB / not usable 0<br>
Allocatable yes<br>
PE Size (KByte) 4096<br>
Total PE 14517<br>
Free PE 14517<br>
Allocated PE 0<br>
PV UUID 300kTo-evxm-rfmf-90LA-4YOJ-2LG5-t4JHnf

-- Physical volume --<br>
PV Name /dev/md3<br>
VG Name fileserver<br>
PV Size 56.71 GB / not usable 0<br>
Allocatable yes<br>
PE Size (KByte) 4096<br>
Total PE 14517<br>
Free PE 14517<br>
Allocated PE 0<br>
PV UUID LXFSW6-7LQX-ZGGU-dV95-jQgg-TK44-U5JOjO

vgdisplay

server1:~# vgdisplay<br>
-- Volume group --<br>
VG Name fileserver<br>
System ID<br>
Format lvm2<br>
Metadata Areas 4<br>
Metadata Sequence No 26<br>
VG Access read/write<br>
VG Status resizable<br>
MAX LV 0<br>
Cur LV 3<br>
Open LV 3<br>
Max PV 0<br>
Cur PV 4<br>
Act PV 4<br>
VG Size 159.98 GB<br>
PE Size 4.00 MB<br>
Total PE 40956<br>
Alloc PE / Size 11904 / 46.50 GB<br>
Free PE / Size 29052 / 113.48 GB<br>
VG UUID dQDEHT-kNHf-UjRm-rmJ3-OUYx-9G1t-aVskI1

lvdisplay

server1:~# lvdisplay<br>
-- Logical volume --<br>
LV Name /dev/fileserver/share<br>
VG Name fileserver<br>
LV UUID bcn3Oi-vW3p-WoyX-QlF2-xEtz-uz7Z-4DllYN<br>
LV Write Access read/write<br>
LV Status available<br>
# open 1<br>
LV Size 40.00 GB<br>
Current LE 10240<br>
Segments 2<br>
Allocation inherit<br>
Read ahead sectors 0<br>
Block device 253:0

-- Logical volume --<br>
LV Name /dev/fileserver/backup<br>
VG Name fileserver<br>
LV UUID vfKVnU-gFXB-C6hE-1L4g-il6U-78EE-N8Sni8<br>
LV Write Access read/write<br>
LV Status available<br>
# open 1<br>
LV Size 5.00 GB<br>
Current LE 1280<br>
Segments 1<br>
Allocation inherit<br>
Read ahead sectors 0<br>
Block device 253:1

-- Logical volume --<br>
LV Name /dev/fileserver/media<br>
VG Name fileserver<br>
LV UUID H1gagh-wTwH-Og0S-cJNQ-BgX1-zGlM-LwLVzE<br>
LV Write Access read/write<br>
LV Status available<br>
# open 2<br>
LV Size 1.50 GB<br>
Current LE 384<br>
Segments 1<br>
Allocation inherit<br>
Read ahead sectors 0<br>
Block device 253:2

If your outputs look similar, you have successfully replaced your small hard disks with bigger ones.

Now that we have more disk space (

<span class="system">2* 23.29GB + 2 10_years_of_git_an_interview_with_git_creator_linus_torvalds.md accuracy_in_the_palm_of_your_hand.md a_clear_view_of_the_brain.md a_liberator_but_never_free.md a_look_at_alteras_opencl_sdk_for_fpgas.md an_important_step_in_artificial_intelligence.md an_introduction_to_matlab_metaprogramming.md announcing_the_nulecule_specification_for_composite_applications.md a_quick_introduction_to_consul__scotts_weblog__the_weblog_of_an_it_pro_specializing_in_virtualization_networking_open_source_and_cloud_computing.md a_thorough_introduction_guide_to_docker_containers.md a_tmux_crash_course.md bayesian_inference_of_a_binomial_proportion__the_analytical_approach.md browser_monitoring_for_githubcom.md can_ecstasy_replace_xanax.md circles_sines_and_signals__introduction.md creating_kvm_machines_with_boxgrinder_and_vmbuilder.md creating_shazam_in_java.md crossplatform_development_with_nwjs__tuts_code_tutorial.md data_archeology.md data.json data_ml david_deutsch_explains_why_it’s_good_to_be_wrong.md deku_how_we_built_our_functional_alternative_to_react.md designing_a_state_machine_without_conditionals.md devops discovering_and_monitoring_hardware_in_linux.md docker does_long_term_use_of_psychiatric_drugs_cause_more_harm_than_good.md drug_perks_up_old_muscles_and_aging_brains.md electronics_for_the_javascript_developer_using_wifi_and_bluetooth_le.md elements_of_scale_composing_and_scaling_data_platforms.md elements_of_scale_composing_and_scaling_data platforms.md embedded eve_online_how_a_virtual_world_went_to_the_edge_of_apocalypse_and_back__simon_parkin.md explaining_a_cornerstone_of_game_theory_john_nash’s_equilibrium.md five_funny_little_linux_network_testers_and_monitors.md fixing_engineerings_loyalty_and_longevity_problem.md gaming github go golden_gate_bridge_builders_ate_special_meals_to_prevent_dizziness.md google_systems_guru_explains_why_containers_are_the_future_of_computing.md hackers_build_a_new_tor_client_designed_to_beat_the_nsa.md highavailability_storage_with_halvm.md historyfuture_proof_fingerprint.md how_i_got_converted_to_gmo_food.md how_shazam_works.md how_to_be_polite_—_the_message.md how_to_get_your_very_own_rstudio_server_and_shiny_server_with_digitalocean.md how_to_turn_your_pc_or_other_device_into_a_retro_arcade_with_lakka.md html implementing_a_trustless_security_solution_with_hardware_wallets_and_multisignature.md inside_the_mind_that_built_google_brain_on_life_creativity_and_failure.md ira_glass_public_radio_can_capitalize_on_its_popularity_without_selling_out_its_mission.md itspritecom.md javascript join_the_engineering_leisure_class.md just_don’t_hire_0x_engineers.md linux linux_commandline_video_editing.md make.md make_your_own_injection_molding_machine__make_diy_projects_howtos_electronics_crafts_and_ideas_for_makers.md mark_twain__helen_keller’s_special_friendship_he_treated_me_not_as_a_freak_but_as_a_person_dealing_with_great_difficulties.md mbuntu_y_macbuntu_transformation_pack_available_for_ubuntu_1504_vivid_vervet.md meeting_a_silk_road_drugs_boss.md mobile no_death_no_taxes.md null.md old on_edgar_allan_poe_by_marilynne_robinson.md optimize_python_with closures.md phd_is_the_doctoral_thesis_obsolete.md pisoc_learn_to_create.md png polymatheia__persistent_vector_performance_summarised.md popular_electric_brain_stimulation_method_tdcs_is_detrimental_to_iq_scores_study.md programming robust_landmarkbased_audio_fingerprinting.md roll_your_own_customized_ubuntu_with_uck.md running_a_small_docker_swarm_cluster.md rust_discovery_or_how_i_figure_things_out.md safe_connection.md scantool__obdii_car_diagnostic_software_for_linux.md science smart_regulation_for_smart_drugs.md social software_install_guide.md stigler_diet_–_how_i_became_a_data_scientist_despite_having_been_a_math_major.md tfidf_is_about_what_matters.md the_business_economics_and_opportunity_of_opensource_data_science.md the_entrepreneur_disrupting_the_tech_world_by_spilling_her_company’s_secrets.md the_identity_underground.md the_man_who_became_big bird.md the_most_common_illegal_job_interview_questions_you_should_watch_out_for.md theory.md the_psychology_of_ux.md the_roland_tr808_the_drum_machine_that_revolutionised_music.md the_shazam_effect.md the_sound_of_ted_a_case_for_distaste.md the_unreasonable_effectiveness_of_recurrent_neural_networks.md tidy understanding_c_by_learning_assembly.md url_to_filename.csv weave_is_kinda_slow.md web_dev what_richard_branson_and_30_amazing_entrepreneurs_taught_me_in_7_days_on_necker_island.md who_funds_the_future.md why_we_age_–_part_i_the_evolution_of_aging.md wireless_raspberry_pi_speaker.md 56.71GB = 160GB</span>

) we could enlarge our logical volumes. Until now you know how to enlarge ext3 and reiserfs partitions, so let's enlarge our 

<span class="system">backup</span>

 logical volume now which uses xfs:

lvextend -L10G /dev/fileserver/backup

server1:~# lvextend -L10G /dev/fileserver/backup<br>
Extending logical volume backup to 10.00 GB<br>
Logical volume backup successfully resized

To enlarge the xfs filesystem, we run

xfs_growfs /dev/fileserver/backup

server1:~# xfs_growfs /dev/fileserver/backup<br>
meta-data=/dev/fileserver/backup isize=256 agcount=8, agsize=163840 blks<br>
= sectsz=512 attr=0<br>
data = bsize=4096 blocks=1310720, imaxpct=25<br>
= sunit=0 swidth=0 blks, unwritten=1<br>
naming =version 2 bsize=4096<br>
log =internal bsize=4096 blocks=2560, version=1<br>
= sectsz=512 sunit=0 blks<br>
realtime =none extsz=65536 blocks=0, rtextents=0<br>
data blocks changed from 1310720 to 2621440

The output of

df -h

should now look like this:

server1:~# df -h<br>
Filesystem Size Used Avail Use% Mounted on<br>
/dev/sda2 19G 666M 17G 4% /<br>
tmpfs 78M 0 78M 0% /lib/init/rw<br>
udev 10M 116K 9.9M 2% /dev<br>
tmpfs 78M 0 78M 0% /dev/shm<br>
/dev/sda1 137M 17M 114M 13% /boot<br>
/dev/mapper/fileserver-share<br>
40G 177M 38G 1% /var/share<br>
/dev/mapper/fileserver-backup<br>
10G 272K 10G 1% /var/backup<br>
/dev/mapper/fileserver-media<br>
1.5G 33M 1.5G 3% /var/media

That's it! If you've made it until here, you should now be used to LVM and LVM on RAID.

![](https://www.howtoforge.com/images/pdficon_small.png) [view as pdf](https://www.howtoforge.com/subscription/) | ![](https://www.howtoforge.com/images/print.gif) [print](https://www.howtoforge.com/subscription/)
