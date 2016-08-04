# Creating KVM machines with BoxGrinder and VMBuilder

[Original URL](http://www.admin-magazine.com/Articles/Building-Virtual-Images-with-BoxGrinder-and-VMBuilder)

> By Michael Kofler Virtualization technologies are supposed to make life easier for the administrator, but after converting a number of servers to a few dozen VMs, what was supposed to make things...

<span class="author">By Michael Kofler</span>

**Virtualization technologies** are supposed to make life easier for the administrator, but after converting a number of servers to a few dozen VMs, what was supposed to make things easier can quickly become a burden. Tools that facilitate the process of creating new virtual machines are therefore quite useful.

The fastest and easiest way to set up a new virtual machine is by cloning. To do this, you can run the _virt-clone_ command in the shell:

```
virt-clone --original userver5 --name userver6 \
 --file /var/lib/libvirt/images/userver6.img
```

_virt-clone_ creates a new XML definition file for the virtual machine, copies the image file for the virtual disk, and gives the network adapter a new, random MAC address. The remaining hardware components remain unchanged.

After cloning, however, much manual work is required: You need to modify the virtual machine's network configuration and change the hostname. If the original virtual machine ran an SSH server, you also need to create a new SSH key for the new virtual machine.

**BoxGrinder**

The manual intervention required after cloning is prone to error, so why bother cloning if you can create virtual machines from scratch with a single command? In the Red Hat world, the [BoxGrinder project](http://boxgrinder.org/) can help you. With BoxGrinder, you can create virtual Fedora, RHEL, CentOS, and Scientific Linux machines in almost no time. Incidentally, BoxGrinder is not a KVM-specific tool: It can also cope with various other virtualization and cloud systems and – besides KVM – also supports VMware, Amazon EC2, and VirtualBox. Unfortunately, prebuilt BoxGrinder packages exist only for Fedora right now. The installation is straightforward:

```
yum install rubygem-boxgrinder-build
```

It looks like RHEL and CentOS users will have to wait for version 6.4 before official BoxGrinder packages become available. Even though you will probably not want to use Fedora as the KVM host in a production environment, this restriction is not as bad as it seems: For one thing, you can run BoxGrinder on Fedora to create, for example, a virtual CentOS machine and then install the VM on a KVM host running on CentOS. In other words, Fedora is only used as a vehicle for executing BoxGrinder, not as a virtualization system. Also, you can run BoxGrinder on the KVM host in a virtual machine. The BoxGrinder website even has a [prebuilt image](http://boxgrinder.org/tutorials/boxgrinder-build-meta-appliance/) for this purpose.

Many BoxGrinder features are implemented as plugins, of which there are three types:

- OS plugins manage the OS-specific details of the virtual machines.
- Platform plugins are responsible for accommodating the special features of the respective virtualization system. If you do not use a platform plugin, virtual KVM machines are created.
- Delivery plugins are responsible for how the virtual machine is delivered – that is, how it is transferred to the virtualization host.

After installing BoxGrinder, you can run the

```
boxgrinder-build --version
```

command for an overview of the available plugins (Listing 1).

**Listing 1: BoxGrinder Plugins**

```
01 boxgrinder-build --version
02   BoxGrinder Build 0.10.2
03
04   Available os plugins:
05    - rhel plugin for Red Hat Enterprise Linux
06    - centos plugin for CentOS
07    - fedora plugin for Fedora
08    - sl plugin for Scientific Linux
09
10   Available platform plugins:
11    - vmware plugin for VMware
12    - ec2 plugin for Amazon Elastic Compute Cloud (Amazon EC2)
13    - virtualbox plugin for VirtualBox
14    - virtualpc plugin for VirtualPC
15
16   Available delivery plugins:
17    - s3 plugin for Amazon Simple Storage Service (Amazon S3)
18    - cloudfront plugin for Amazon Simple Storage Service (Amazon S3)
19    - ami plugin for Amazon Simple Storage Service (Amazon S3)
20    - sftp plugin for SSH File Transfer Protocol
21    - ebs plugin for Elastic Block Storage
22    - local plugin for Local file system
23    - elastichosts plugin for ElasticHosts
24    - openstack plugin for OpenStack
25    - libvirt plugin for libvirt Virtualisation API
```

**The Appliance Definition File**

Before you create your first virtual machine with BoxGrinder, you must describe the virtual machine in a text file known as the appliance definition file. This file might look something like Listing 2.

**Listing 2: Application Definition File for CentOS**

```
01 name: centos
02 summary: CentOS installation with BoxGrinder
03 os:
04   name: centos
05   version: 6
06 hardware:
07   partitions:
08     "/":
09       size: 4
10     "/home":
11       size: 1
12     "swap":
13       size: 0.5
14   cpus: 1
15   memory: 1024
16 packages:
17   - @core
18   - @mysql
19   - acpid
```

The only mandatory items are the _name_ , the _os/name_ , and _os/version_ . If you specify _rhel_ , _centos_ , or _sl_ (for Scientific Linux) as the operating system, you only need to specify _5_ or _6_ as the version number. BoxGrinder then automatically uses the latest version (currently version 6.3). Default values are used for all other parameters. In the _os_ section, you can use _password_ to specify the desired root password (by default, it is _boxgrinder_ ).

The _hardware_ section is where you partition the virtual hard disk. Enter the desired partition sizes in gigabytes and the mountpoint. If you want a swap partition, enter _swap_ instead of the mountpoint. If the partition info is missing, BoxGrinder only sets up a 1GB root partition. Additionally, the _hardware_ section defines the number of CPU cores (by default, _1_ ) and the RAM size in megabytes (by default, _256_ ).

The _packages_ section determines which packages are installed on the virtual machine. Enter the desired packages line by line, starting each line with a dash. _@name_ designates a package group.

The _core_ package group is automatically selected. In Fedora and RHEL, this setup gives you a minimal, text-based installation (about 200 packages occupying 600MB). The names and scopes of the packages for Red Hat-based distributions are listed in the _repodata/xxx-comps.xml_ file on the installation DVD. Additionally,

```
yum grouplist -v
```

provides a list of all groups, where the group IDs relevant for BoxGrinder are indicated in brackets. However, _yum grouplist_ does not know about the minimum core group. Besides the keywords described here, you can use the appliance definition file to specify other package repositories (_packages_ ), integrate other [appliance files](http://boxgrinder.org/tutorials/appliance-definition/) (_appliances_ ), add individual files (_files_ ), and run commands after completing the installation (_post_ ). Detailed options are described on the BoxGrinder website.

**Build a Box**

In the simplest case, you can create the new virtual machine by a simple call to _boxgrinder-build_ , where the only parameter you specify is the name of the appliance definition file:

```
boxgrinder-build centos.appl
```

Note that _boxgrinder-build_ must be executed by root. The program will run for several minutes. The first time a distribution is generated, all required packages must be downloaded. BoxGrinder stores the packages in the _/var/cache/boxgrinder/rpms-cache_ directory; after this, subsequent runs of the commands are processed more quickly.

By default, BoxGrinder stores the resulting files (i.e., a RAW file with the virtual disk and a libvirt XML file) in the following directory:

```
build/appliances/x86_64/<osplugin>/<osversion>/<name>/1.0/<osplugin>-plugin/
```

Using the appliance definition file from Listing 2 thus results in the following:

```
build/appliances/x86_64/centos/6/centos/1.0/centos-plugin/
```

BoxGrinder normally uses the RAW format for image files. If you prefer the QCOW2 format, run BoxGrinder as follows:

```
boxgrinder-build centos.appl --os-config: format:qcow2
```

On the virtual machine, only the root user is set up. By default, the root password is _boxgrinder_ and should be changed immediately for security reasons when you first start the virtual machine.

Please note that, when executed, BoxGrinder tests whether a virtual machine with the specified name already exists. If so, the command does not create a new VM. This saves time but also means that changes to the appliance definition file are ignored. After each and every change to the file, you must explicitly tell BoxGrinder to set up the virtual machine using the _-f_ (for force) option.

**BoxGrinder and Libvirt**

At this point, you could now manually install the resulting image file and the associated XML file on the virtualization host, but BoxGrinder can help you with this task, too. To begin, select a delivery plugin with the _-d_ option. The libvirt plugin is best suited for KVM machines (i.e., you need _-d libvirt_ ).

Without any other options, BoxGrinder will then copy the image file to the _/var/lib/libvirt/images_ directory and add the virtual machine to the list of libvirt-managed machines. If the KVM host is running on a different machine, or if you want to put the disk image file in a different storage pool, you must specify two options: _connection_uri_ for delivering the virtual machine description (XML file) and _image_delivery_uri_ for transferring the image file:

```
boxgrinder centos.appl --os-config format:qcow2 -d libvirt \
 --delivery-config connection_uri:qemu+\
 ssh://root@kvmhost/system,image_delivery_uri:\
 sftp://root@kvmhost/var/lib/libvirt/images
```

This makes the BoxGrinder command quite confusing. As far as options for each BoxGrinder call are concerned, you will probably prefer to save them in _/root/.boxgrinder/config_ . This file might look something like Listing 3.

**Listing 3: Sample BoxGrinder Configuration**

```
01 plugins:
02    fedora:
03      format: qcow2
04    centos:
05      format: qcow2
06    libvirt:
07      connection_uri: qemu+ssh://root@kvmhost/system
08      image_delivery_uri: sftp://root@kvmhost/var/lib/libvirt/images
09      bus: virtio
10      overwrite: true
11      domain_type: kvm
```

This again simplifies the BoxGrinder command line to:

```
boxgrinder centos.appl -d libvirt
```

Unfortunately, BoxGrinder applies some strange default settings to the virtual machines that it produces. For example, the virtual disks are IDE by default. You can change this using the _bus:virtio_ option in _/root/.boxgrinder/config_ , which switches to the Virtio drivers. If you set up the virtual machine as a QCOW2 image, the XML file for the virtual machine gives you the wrong format. You can correct this setting using _virsh edit_ or in the Virt Manager (Figure 1).

[![](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/box-f01/68400-1-eng-US/box-F01_reference.jpg)](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/box-f01/68400-1-eng-US/box-F01_reference.jpg "Figure 1: The disk storage format is wrongly preset for QCOW2 images.") 

<span class="attribute-caption"> Figure 1: The disk storage format is wrongly preset for QCOW2 images. </span>

Qemu is used instead of KVM as the hypervisor, which is also why the virtual machines run so slowly. The solution here is the _domain_type:kvm_ option in _/root/.boxgrinder/config_ .

**Faster Networking**

Also, network connectivity uses a RTL8139 adapter rather than Virtio. The network runs faster if you change the network card to _virtio_ . Unfortunately, there is no corresponding option in the BoxGrinder libvirt plugin.

ACPI and APIC are disabled by default. ACPI lets you gracefully shut down or restart the virtual machine using an ACPI command. APIC stands for Advanced Programmable Interrupt Controller and is a schema for forwarding hardware interrupts to the CPUs. You will want to set both options in the _Overview_ dialog of the Virtual Machine Manager (under _Machine Settings_ ).

The virtual machine uses a US keyboard layout. To change this, you can set your preferred keyboard layout in the _/etc/sysconfig/keyboard_ file. Also, the virtual machine uses the EDT (Eastern Daylight Time) time zone. If you live elsewhere, copy the appropriate time zone file from _/usr/share/zoneinfo_ to _/etc/localtime_ . You will still need to modify the network settings manually on the virtual machine.

Of course, you always run the risk that some of these options will be overlooked, and the virtual machine will thus run less efficiently than it should. It is hard to understand why the default settings are not defined to be more meaningful from the outset. Because of the need to manually adjust various settings, very little of the time saved by BoxGrinder remains when setting up virtual machines. Scripts that modify the XML file of the new virtual machine on the KVM host and change various settings on the virtual machine (e.g., keyboard layout, time zone) can help.

Future versions of BoxGrinder will also have a graphical front end called BoxGrinder Studio, which uses a REST interface to collaborate with the build tools (Figure 2).

[![](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/box-f02/68404-1-eng-US/box-F02_large.jpg)](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/box-f02/68404-1-eng-US/box-F02_reference.jpg "Figure 2: BoxGrinder Studio is a graphical front end used to facilitate the creation of virtual machines with BoxGrinder.") 

<span class="attribute-caption"> Figure 2: BoxGrinder Studio is a graphical front end used to facilitate the creation of virtual machines with BoxGrinder. </span>

**VMBuilder**

The Ubuntu-specific counterpart to BoxGrinder is VMBuilder. It helps you set up virtual Ubuntu machines with minimal effort. Besides supporting KVM/libvirt virtualization systems, it also supports Amazon EC2, VirtualBox, VMware, and Xen. VMBuilder is implemented as a Python script in the _python-vm-builder_ package:

```
apt-get install python-vm-builder
```

The man page for VMBuilder command unfortunately provides very little help. The

```
vmbuilder kvm ubuntu --help
```

command provides a list of options, and the Ubuntu Server Guide has a [VMBuilder](https://help.ubuntu.com/12.04/serverguide/C/jeos-and-vmbuilder.html) section. VMBuilder must be run as root or with _sudo_ . In a minimalist scenario, the syntax is:

```
vmbuilder kvm ubuntu --suite precise --libvirt qemu:///system
```

The first parameter specifies the desired virtualization system, and the second parameter specifies the distribution. The _–suite_ option determines the Ubuntu version, where _precise_ is the code name for Ubuntu 12.04\. Finally, the _–libvirt <uri>_ option specifies the KVM host on which VMBuilder should set up the virtual machine.

VMBuilder always performs a minimal installation comprising approximately 190 packages and occupying 500MB of space on the hard disk. The installation does not include a graphical desktop.

VMBuilder creates the image file for the virtual disk in the _ubuntu-kvm_ subdirectory relative to the current working directory. If you want to save the virtual machine in another location, change directory before running VMBuilder and use the _–dest_ option to specify the destination directory. VMBuilder creates the target directory itself, so it must not already exist! If this is the case, VMBuilder returns an error message – but, annoyingly, only at the end of the build process.

A number of default settings exist for the virtual machines created by VMBuilder. For example, it uses the same CPU architecture as the computer on which you run VMBuilder. So, if you run VMBuilder on 64-bit Ubuntu, it also installs a 64-bit version of Ubuntu on the virtual machine. The virtual machine uses one CPU (core) and 128MB of RAM.

The filesystem is created as a QCOW2 image file of 4GB. Of these, 3GB are reserved for the system partition and another 1GB for swap. The image file is stored in the _KVM-ubuntu_ directory and not transferred to a libvirt storage pool. The image file is passed to the virtual machine via the IDE driver (i.e., not by the more efficient Virtio driver). In contrast, the network adapter uses the Virtio driver. The virtual machine, on which the _ubuntu_ user account is set up with a password of _ubuntu_ , resides on the default network of the libvirt tools. The host name is also _ubuntu_ . If you want to change any of these key settings, you must specify corresponding options (Table 1).

[![](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/box-t01/68408-1-eng-US/box-T01_reference.jpg)](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/box-t01/68408-1-eng-US/box-T01_reference.jpg "box-T01")

This makes the command line for VMBuilder quite confusing.

Listing 4 shows a real Ubuntu server installation.

**Listing 4: Virtual Ubuntu Server with VMBuilder**

```
vmbuilder kvm ubuntu --suite precise  --libvirt qemu:///system  \
 --mem 512 --user kofler  --dest /var/lib/libvirt/images/firma-abc.de \
 --hostname firma-abc.de --bridge br0  --ip 79.47.194.162 \
 --mask 255.255.255.248 --net 79.47.194.160  --gw 79.47.194.166 \
 --dns 213.211.98.98  --addpkg openssh-server --addpkg acpid  -v
```

If you configure these options (note –dest and –hostname in particular), _vmbuilder_ generates the following files, where _xxxxxx_ is a random string:

```
/etc/libvirt/qemu/firma-abc.de.xml
/var/lib/libvirt/images/firma-abc.de/xxxxxx.qcow2
```

The first line is the XML file for libvirt, and the second line specifies the image file.

**VMBuilder Peculiarities**

Unfortunately, VMBuilder also has some quirks. It refuses to set up the new virtual machine in Libvirt Manager if you already have a machine of the same name. This makes sense to a certain point, but it would be friendlier to run the corresponding test at the beginning of the build process, rather than after completing it. In other words, you should always state a unique name for _–hostname_ that is not only used as the hostname, but also as the name for the libvirt tools.

VMBuilder creates the _kvm-ubuntu_ directory to create virtual machines but does not delete it later, not even if the directory is empty (thanks to the _–dest_ option). The next time you run VMBuilder, the script outputs an error message telling you _ubuntu-kvm already exists_ . The solution is to delete the directory manually.

VMBuilder selects less than optimum hardware components for the virtual machine. In particular, hard disk access via a virtual IDE controller slows down the VM. If you want to use the faster Virtio driver, replace _/dev/sda_ with _/dev/vda_ in _/etc/fstab_ on the virtual machine and then shut down the virtual machine. Then, in the virtual machine configuration, change IDE to Virtio (the easiest way to do this is to use Virtual Machine Manager).

The _acpid_ package is not installed automatically. The virtual machine thus does not respond to ACPI events (e.g., shutdown requests sent by Virtual Machine Manager). To solve this problem, you can install the _acpid_ package or enter the _–addpkg acpid_ option when you set up the virtual machine.

The virtual machine uses a US keyboard layout by default. To change this, you can run the

```
dpkg-reconfigure keyboard-configuration
```

command. VMBuilder installs GRUB 0.97 as the bootloader on the virtual machine – not version 2, which is normally used by Ubuntu.

In contrast to BoxGrinder, VMBuilder does not use a cache for the required installation packages. Thus, all required packages must be downloaded again for each installation. It therefore makes sense to set up an APT-proxy (e.g., using _approx_ or _apt-cacher-ng_ ). When you call VMBuilder, you need to specify the proxy address for the _–install-mirror_ option.

**Conclusions**

BoxGrinder and VMBuilder can save you a huge amount of time if you set up virtual machines regularly. However, both of these tools are quirky, and both are designed for professional administrators. For KVM users who occasionally need to create a new virtual machine, the effort is just not worthwhile.

**The Author**

[Michael Kofler](http://kofler.info/) is a freelance computer book author and trainer. He recently published the Addison-Wesley book KVM for Server Virtualization in collaboration with Ralf Spenneberg.
