# group test: NAS distros

[Original URL](https://www.linuxvoice.com/group-test-nas-distros/)

> FreeNAS NAS4Free Open Media Vault Openfiler Community Edition EasyNAS Turnkey Linux File Server What's a terabyte to a data connoisseur? If you're like us, you probably have more data...

- FreeNAS
- NAS4Free
- Open Media Vault
- Openfiler Community Edition
- EasyNAS
- Turnkey Linux File Server

<span class="_idGenDropcap-1">W</span>

hat's a terabyte to a data connoisseur? If you're like us, you probably have more data than spare USB ports. While external drives are a great way to quickly and conveniently add extra storage, they have their drawbacks. For one, their data retrieval capabilities are restricted to the computer they are connected to. This might work for individual users with single PCs but isn't a practical solution for a household with a variety of devices.

To add flexibility to your data storage and retrieval you need to use a network-attached storage (NAS) solution. With a NAS you can essentially share the storage with everyone on the network. While you can pick a prefabricated NAS box from PC World, it doesn't take much effort to build one yourself. In this feature we'll test some of the best NAS solutions that offer you the features and flexibility of a commercial NAS minus the cost of proprietary software.

Almost all NAS solutions also offer additional advantages. Instead of simply pooling together the attached disks, they let you arrange the available space into different RAID configurations and give you control over how you want to store your data. You can decide to spread your data across the drives or create different levels of redundancy for an effective backup solution. Most NAS solutions support a variety of protocols and can be accessed from multiple operating systems and devices. Some even allow access to remote machines outside the network.

Many NAS solutions can do a lot more than just back up and restore files – you can extend them with plugins to do a variety of tasks. Some enable you to stream media to computers and others devices. Others can hook up with apps and services and allow them to use the NAS for storing and retrieving data. Read on to find out which NAS solution works best for you.

Let's go shopping.

<span class="CharOverride-13">A</span>

 NAS solution requires both software and hardware. While this feature will help sort out the software bit, you'll need to put together the hardware for your DIY NAS box. The most important part of the NAS box is the storage. Although you can use a solitary hard disk, you should definitely start with at least a couple. You'll also need a smaller one on which to install the NAS software: most NASes requires a disk exclusively for themselves, which makes it pointless to use a 1TB disk for installing the NAS distro. Some NAS distros can also live off USB disks.

You'll also need a mini-ITX motherboard to power the NAS box. Look for one that supports multiple SATA drives. Memory is important too, and since it isn't as expensive as it used to be, we recommend you pick up at least 8GB to be future proof.

If your requirements are modest, you can even turn a Raspberry Pi into a cheap NAS server. Just attach a large portable USB disk to the RPi and install and configure Samba on it to make it accessible from anywhere on the network. If you need redundancy, you can attach another USB disk and duplicate the data in one to the other with a simple 

<span class="CharOverride-14">rsync</span>

 command and then make the process automatic with 

<span class="CharOverride-14">cron</span>

.

Lock and load.

<span class="_idGenDropcap-1">T</span>

he OpenMediaVault (OMV) NAS distro is designed for small businesses and home users, and has modest hardware requirements. Installation is pretty straight-forward since OMV takes over the entire disk. This might seem like an odd choice, but you can install OMV on to a removable USB disk as well. Due to its specialised nature, OMV lacks the baggage of a normal distro and can easily fit inside a 4GB USB disk.

Once it's up and running, you can manage the distro from its browser-based administration interface, which is well laid out, with the options listed in a logical manner. OMV will detect all attached disks and even lets you wipe them securely. You can also enable SMART monitoring for the disks and schedule tests.

You can use the disks attached to the OMV NAS individually or assemble them in a RAID array. OMV defaults to RAID level 5 but supports all RAID levels. You can also format the individual disks or the RAID device from the web interface.

OMV can create and manage EXT3/4, JFS, and XFS filesystems. You also get the option to assign disk quotas to individual users, and the distro has ample options for managing users. There's also the option to import multiple users in a particular format, and you can define per-user access permissions for every shared folder.

[![Image](https://www.linuxvoice.com/wp-content/uploads/2016/01/omv-small.jpg)](https://www.linuxvoice.com/wp-content/uploads/2016/01/omv-large.jpg)

OMV, built on Debian Wheezy, is chock-full of features and can easily take on new ones with plugins.

All-rounder

Once the storage has been added, you can access the NAS from anywhere on the network using a variety of ways. OMV supports various popular protocols and services, including NFS, SMB/CIFS, FTP, TFTP, SSH, 

<span class="CharOverride-16">rsync</span>

 and more. Each service has its own configuration and management screen. You can configure various aspects of each service before enabling them, and can define the shares for the different services individually.

You can conduct regular system maintenance tasks such as installing updates from the web interface. The distro has custom command-line scripts for tasks such as upgrading to new releases, and you can schedule them via the web interface.

One of OMV's strongest suits is its ability to take on new features with plugins. The distro ships with 11 officially supported plugins and you can add a variety of third-party plugins hosted on 

<span class="CharOverride-14">
  <a href="http://omv-extras.org">omv-extras.org</a>
</span>

. The officially supported plugins add a couple of features that ship as standard on some NAS distros but are missing from the base OMV installation. For example, OMV doesn't let you pool multiple disks into a logical volume by default. Similarly, OMV can't interface with a directory server, but with the LDAP plugin it can be made to fetch user authentication information via LDAP. Then there's a plugin that lets you stream the music stored on the NAS and another that can automatically synchronise a shared folder to a plugged-in device.

The project has plenty of support infrastructure, with enough documentation on its wiki and an active forum board. You can also sample OMV's admin interface using the demo installation on its website.

<span class="CharOverride-18">A feature-rich NAS distro that’s easy to deploy and manage. 4/5</span>

Go go gadget.

<span class="_idGenDropcap-1">T</span>

urnkey Linux produces a range of self-contained distros all based on the latest stable Debian Wheezy release. You can download all Turnkey Linux distros as installable live ISO images or as virtual disks optimised for various virtualisation platforms such as 

<span class="CharOverride-16">VirtualBox</span>

, OpenVZ and Docker.

The File Server appliance includes a pre-configured Samba installation. Think of it as a bare Debian installation with a fully configured and working instance of the Samba server. The server will show up on your network as soon as you're done installing it. By default, the Samba installation has configured shares for every user's home directory and a public storage area readable and writeable by all users.

The distro also ships with Webmin for managing various aspects of the underlying distro from the browser. The customised Webmin installation ships with the Samba module with which you can graphically configure Samba. Using the Samba module you can change the default workgroup and Netbios name of the Samba installation as well as add and remove Samba shares and fine tune their permissions. You can also use Webmin to add users to the base Debian distro.

While the Turnkey Linux File Server distro is the simplest to deploy and use, it's also very bare in terms of features. Unlike most NAS distros, it'll let you use the free space on the disk it's installed on. You can also add additional disks and share them via Samba. But to use them together as a virtual volume, you'll need to be familiar with logical volume management (LVM) on Debian. Furthermore, the distro doesn't include the ability to configure RAID like most other NAS solutions on test, nor do you get a multitude of protocols, so you're restricted to using SMB.

[![Image](https://www.linuxvoice.com/wp-content/uploads/2016/01/turnkeylinux-small.jpg)](https://www.linuxvoice.com/wp-content/uploads/2016/01/turnkeylinux-large.jpg)

The distro includes AjaXplorer for accessing your files from the browser and mobile devices.

<span class="CharOverride-18">A no-fuss distro that’ll set up a fully functional file sharing server in no time. <strong>2/5</strong></span>

Open for business.

<span class="_idGenDropcap-1">O</span>

penfiler is one of the most comprehensive solutions on test. It's based on the now defunct rPath Linux and is distributed as an installable image for 64-bit machines and also as pre-installed disk images for various virtual machine monitors including 

<span class="CharOverride-16">Qemu</span>

 and 

<span class="CharOverride-16">Xen</span>

.

In addition to common NAS features, Openfiler supports a variety of enterprise-specific features such as support for LDAP, Active Directory and authentication protocols such as Kerberos 5\. Furthermore, its share management also leaves little to be desired. Besides arranging attached disks into RAIDs, it can create an iSCSI target and initiator. One handy feature is the ability to bond multiple Ethernet cards into one network interface for faster data transfers between the NAS server and the users on the network.

However, all these features come at the cost of usability. The Openfiler web interface is one of the most complex and unintuitive. It's the KDE Control Centre of NAS interfaces. It presents an endless sea of options and sub-options that depend on each other but aren't coherently presented. For example, to pool multiple disks into a simple virtual volume, you'll first have to partition the disks, then hunt for the option to arrange them in a volume group and then find options to create a volume inside them. If you still can't access the disks, that's probably because you haven't enabled and configured the sharing services. The process to arrange the disks in a RAID array is similarly cumbersome.

[![Image](https://www.linuxvoice.com/wp-content/uploads/2016/01/openfiler-small.jpg)](https://www.linuxvoice.com/wp-content/uploads/2016/01/openfiler-large.jpg)

Openfiler switched to CentOS in 2013 and there's a CentOS-based version for testing, but no final release.

Where is the book?

To top it all, Openfiler has virtually no freely official documentation, besides an installation guide and a skeletal FAQ with just two questions. If you need an administration guide you'll have to shell out €9.99 (about £7.50). There are several support packages on sale as well, but the community forum board listed on the website is replete with unanswered hails from users.

<span class="CharOverride-18">There is a target segment for Openfiler, but we can’t spot it. <strong>1/5</strong></span>

Easy does it.

<span class="_idGenDropcap-1">R</span>

olling and managing your own NAS server doesn't have to be an involved process. The EasyNAS distro takes away the complexities by making several assumptions on the user's behalf and in essence simplifies the entire process. The distro is built using the online SUSE Studio tool and is based on OpenSUSE 13.2

There's not much to installing the distro. EasyNAS is designed to take over the entire disk, and all you have to do during installation is to point it to the hard disk you want it to take over. As with most NAS distros, you can't use the installation drive to store additional data.

You can carry out some common administration tasks, such as changing the admin password from the console of the installed distro. For setting up the NAS you can use the distro's web administration console. Unlike most other distros, EasyNAS's web interface has many fewer options and is easy to navigate. You can use it to arrange disks in multiple types of RAID arrays and even concatenate multiple disks into one<br>
virtual volume.

When creating a filesystem on the disks, you can also choose a compression level. The distro gives you two options (better and faster) without going into details about them. Unlike other NAS distros that support multiple filesystems, EasyNAS only supports the Brtfs filesystem. Also, you can define multiple volumes but don't get the option to specify their size. In essence, every volume can grow until it takes over the complete disk.

[![Image](https://www.linuxvoice.com/wp-content/uploads/2016/01/easynas-small.jpg)](https://www.linuxvoice.com/wp-content/uploads/2016/01/easynas-large.jpg)

Minor releases can be upgraded to from within the interfaces but to upgrade to a new major release you'll have to reinstall the distro and then mount the existing volumes manually.

Easy but not kiddie

The distro also has all the essential user management abilities, and while adding users, you can mark them as EasyNAS admins. Furthermore, when you're creating volumes, you can assign the user and group that owns the volume as well as access permissions for them. By default new volumes are automatically added as Samba and NFS shares, and you can optionally add them as TFTP or AFP shares as well (AFP is the Apple Filing Protocol, which is used for sharing files with Mac OS X).

While the distro has some useful features, such as the ability to schedule automated backups of added volumes, it lacks advanced features that you get with other solutions, such as the ability to hook up with a directory server. Also, while the distro supports a variety of protocols and services, you get no options whatsoever to configure them. There is also a web service option that runs a simple web server and enables you to look at and download files from a web browser.

<span class="CharOverride-18">A simple NAS distro that balances the availability of features with reasonable assumptions. <strong>3/5</strong></span>

More NAS solutions abound!

<span class="_idGenDropcap-1">I</span>

f we're going to recommend a piece of software that you can install at home or at work, we want you to be able to get security and feature updates for it, and for that reason we've featured some of the most popular and actively developed NAS solutions. One potentially useful option that isn't in active development is CryptoNAS. The USP of this distro is that it pays special attention to encrypting the data and ships with multiple encryption algorithms. The Debian-based CryptoNAS is available as an installable distro and also as a Deb package. If you aren't averse to freeware, there's also the Slackware-based unRAID Server. It's available as a USB image and can support up to three disks.

Besides these open source solutions, there are several commercial ones as well. Most charge for their enterprise-specific features, while some charge for support and other conveniences. Server Elements has three NAS products that run entirely from RAM. NASLite-2 is a general purpose NAS solution while NASLite-M2 specialises in streaming media. The company also sells a cheaper NAS solution for home users called NanoNAS.

The open source edition of Openfiler also has a commercial edition that adds a host of enterprise-specific features such as block-level replication and High Availability and support for iSCSI and Fibre channel. There's also Open-E DSS, which is replete with Enterprise-specific features and also has a feature-curtailed Lite version that's available as a<br>
free download.

[![Image](https://www.linuxvoice.com/wp-content/uploads/2016/01/box-unraid-small.jpg)](https://www.linuxvoice.com/wp-content/uploads/2016/01/box-unraid-large.jpg)

Tower Media Server is based on Linux but tries its best to camouflage the fact so as to not scare away users.

<span class="_idGenDropcap-1">F</span>

reeNAS is probably the most recognisable NAS distro and one of the elite group of open source software projects that has made a name for itself in the enterprise space. In 2011 its development was taken over by iXsystems, which also sponsors the PC-BSD desktop distro. The new sponsors made changes that didn't go down well with a section of the developers, who forked the project and created the NAS4Free distro.

Besides a common foundation, there are several similarities between the two distros. Both bring the advantages of the ZFS filesystem to the network with the use of popular protocols including SMB, NFS, FTP, AFP, iSCSI and more. Both distros have a similar installation process and can happily reside on a removable USB disk as well.

While FreeNAS is designed to take over the entire installation disk, NAS4Free offers a bunch of options. You can either let NAS4Free completely take over the drive or install it and create a couple of partitions using the remaining space. The second option helps you use the excess space on the installation drive for housing files.

Once installed, the two distros boot to a console with similar administrative options to configure network and change passwords. However, as with other NAS distros the actual NAS configuration is done via a web interface. While the Django-based FreeNAS interface looks modern, NAS4Free uses a modified version of 

<span class="CharOverride-16">Monowall</span>

's aged web interface.

Adding disks is fairly straightforward with both distros. Remember, however, that these distros are both based on FreeBSD, and to use them effectively you need to be familiar with the ZFS filesystem and associated terminology such as zpools and datasets. In this regard, FreeNAS scores over NAS4Free, as its process is more intuitive and visually pleasing and selects good defaults. That said, NAS4Free aids first-time users by pointing to any missed steps. So for example, if you went straight to add a ZFS disk, NAS4Free will tell you that you first have to add a virtual device and point you towards the relevant section. You'll get a similar warning when you try to add a virtual disk and be pointed towards the section to first add a disk.

Then there are times when it'll leave you high and dry. So if you wish to use the devices in a RAID, make sure the disks are formatted as Software RAID, as NAS4Free wouldn't even recognise devices formatted as UFS or FAT32.

[![Image](https://www.linuxvoice.com/wp-content/uploads/2016/01/freenas-small.jpg)](https://www.linuxvoice.com/wp-content/uploads/2016/01/freenas-large.jpg)

FreeNAS has a guided wizard to take you through the necessary setup steps.

RichNAS

Both distros are designed to handle standalone authentication and can also fetch authentication information from a directory server via LDAP. The two distros also support both Linux-style ownership and Windows-style access control lists for fine-grained control. Furthermore, FreeNAS can also be used as an Active Directory domain controller. In fact, some of the most useful NAS features are handled better by FreeNAS. For example, one of the great things about ZFS is its snapshot capability. NAS4Free doesn't offer as many options, nor is it as flexible as FreeNAS when configuring snapshots.

FreeNAS can also be extended with plugins, and uses FreeBSD's Jails mechanism to run them inside isolated silos. This ensures that even if the plugins are compromised they can't affect the NAS. There are plugins that'll convert the NAS into a streaming server and can even run web apps such as OwnCloud. NAS4Free doesn't have plugins as such but has built-in support for several tasks in addition to that of a NAS. You can enable these and use the NAS4Free server as a UPnP Server, Torrent client, DAAP server, and even a simple web server.

Both distros are complex pieces of software, but are well documented, with FreeNAS scoring over NAS4Free in this department as well. FreeNAS also hosts a free webinar daily to help users get started with the distro.

[![Image](https://www.linuxvoice.com/wp-content/uploads/2016/01/nas4free-small.jpg)](https://www.linuxvoice.com/wp-content/uploads/2016/01/nas4free-large.jpg)

You can easily back up (and restore) the server configuration in an XML file.

<span class="CharOverride-25">NAS4Free</span>

 

<span class="CharOverride-18">An advanced NAS distro that’s designed for advanced users.</span>

 **3/5**

<span class="CharOverride-25">FreeNAS</span>

 

<span class="CharOverride-18">The most feature-rich NAS distribution requires some getting used to.</span>

 **3/5**

<span class="_idGenDropcap-1">I</span>

f we had to award this group test to the distro with the biggest number of features then the top two challengers would have been FreeNAS and its protegée NAS4Free. While both of these solutions pitch themselves to users outside the corporate environment, they'd simply be overkill for most home users. Furthermore, their FreeBSD base and the ZFS filesystem, while a boon to enterprise users, virtually makes them alien technology to the average Linux household.

Instead we'd rather rate the distros based on how they manage features with respect to approachability. This is why we rate the Turnkey Linux File Server distro higher than the FreeBSD-based solutions. It the simplest of solutions on test but does what it's supposed to do – provide a network-accessible storage server – without much effort. It ships with a reasonably configured Samba installation and doesn't support any other protocol. You can use the distro to browse its contents from a smartphone, and it can also store backups from any app that supports SMB shares.

But if you need to set up a NAS solution and can trust the system to make smart choices on your behalf, EasyNAS is a wonderful option. It doesn't have advanced features such as the ability to connect with a directory server, but is very adept at collating disks in a virtual volume or a RAID array. The distro also lets you access data using a variety of protocols including AFP, which lets you use the NAS as a target for Mac OS X's 

<span class="CharOverride-16">Time Machine</span>

 backup app. The distro masks complexities such as the settings for the various supported protocols and simplifies setting up useful features such as creating snapshots of added volumes. Despite its simplicity, EasyNAS lets you control access to the added volumes using Linux-style ownership controls, which makes it an ideal NAS distro for non-technical users.

However, we'll award this group test to the Debian-based Open Media Vault distro. In our opinion it offers the greatest number of features without compromising on usability. The distro has all the commonly-used features you'd expect in a NAS distro and can also be extended easily with dozens of plugins, which makes it approachable, extremely versatile and our winner.

- 1st Open Media Vault
- - Licence GNU GPL v3
  - Version 1.9
  - [www.openmediavault.org](http://www.openmediavault.org)
  - Its familiar underpinnings, navigable interface, and adaptability makes it our top choice.

- 2nd EasyNAS
- - Licence Several free software licences
  - Version 0.5.3
  - [www.easynas.org](http://www.easynas.org)
  - Ideal for extending the benefits of NAS to non-technical users.

- 3rd Turnkey Linux File Server
- - 4th FreeNAS

- - Licence BSD Licence
  - Version 9.3
  - [www.freenas.org](http://www.freenas.org)
  - Comprehensive solution that'll appeal to enterprise users.

- 5th NAS4Free
- - Licence BSD Licence
  - Version 9.3
  - [www.nas4free.org](http://www.nas4free.org)
  - If FreeNAS doesn't work for you, this just might.

- 6th Openfiler Community Edition
- - Licence GNU GPL v2
  - Version 2.99
  - [www.openfiler.com](http://www.openfiler.com)
  - A feature-curtailed version that doesn't offer anything worth recommending it over the others.
