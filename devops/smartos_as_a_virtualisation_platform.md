# SmartOS as a Virtualisation Platform

[Original URL](http://www.procmind.com/blog/2016/01/31/smartos-as-a-virtualisation-platform/)

> Virtualisation platforms and technologies represent a big focal point of the technology scene these days. Recently I've watched a dockercon 2015 presentation by Bryan Cantrill, CTO of Joyent,...

Virtualisation platforms and technologies represent a big focal point of the technology scene these days.

Recently I've watched a dockercon 2015 presentation by Bryan Cantrill, CTO of Joyent, an OS kernel developer for 20 years and father of Dtrace as he calls himself [1], about how to debug docker containers gone bad in production. [2]

I recommend to anyone working or thinking of working with linux containers and docker especially to watch this presentation !

I have to say that this is one of the best presentations I've seen when it comes to showing the full picture of the docker tooling, lifecycle and ecosystem.

In his presentation, Bryan brings the operational point of view of running applications, in docker, in a production environment and how to deal with and debug failure when applications inside docker containers go wrong.

It is very rare to see a good presentation on the failure modes of docker since most presentation and talks focus on why docker is amazing and it will solve all your problems.

Irrespective of using docker or not, applications have bugs, they go wrong, it is very important to have adequate tooling and discipline to debug and improve them.

Towards the end of his talk Bryan shows some amazing tools and services that the Joyent team has built since 2004 when they've started their journey as a "cloud platform" company.

All these tools are built upon their platform hypervisor called SmartOS. [3]

The presentation plus the details I've read about SmartOS intrigued me, and I gave SmartOS a spin in a KVM virtual machine to see what it can do.

## What is SmartOS

Disclaimer: I'm no authority on SmartOS, I'm relaying to you what I've found out about it until now.

Go and search for yourself to find out more.

Historically speaking SmartOS derives from the Solaris OS. [4]

A fork of Solaris, called OpenSolaris, was created in 2004.

After the Oracle aquisition of SUN Microsystems in 2010, a group of Solaris engineers created the illumos kernel [5] which was used subsequently to power OpenIndiana from which SmartOS sprang.

The Solaris kernel developers have started working on OS virtualisation since 2005, it looks like they are 10 years or so ahead of the Linux containers and it shows. [6]

SmartOS is not a general purpose OS, it appears to be designed from the ground up to run virtual workloads.

It is effectively a Read-Only (almost full RO) platform hypervisor running in RAM and managing different kinds of virtual workloads.

SmartOS can run these **virtual workloads** at the same time using the same tooling:

- fully emulated virtual hardware VMs, achieved by using the KVM hypervisor
- 3 types of OS virtualisation, sharing one OS kernel between multiple partitioned zones ( called containers in Linux land ):

  - it can run SmartOS zones, called **joyent brand** zones
  - it can run Linux zones, called **lx brand** zones. This allows a user to run a full Linux userland on the SmartOS UNIX kernel
  - docker containers from the docker hub, still called **lx brand** zones and running on the same SmartOS UNIX kernel

Because SmartOS is built on the powerfull legacy of Solaris zones, it has a very useful and powerfull feature compared to Linux containers: complete zone isolation !

From a security point of view SmartOS zones ( read containers ) are fully isolated, an attacker that has been able to gain root privileges in the zone cannot gain root access on the host hypervisor. [11]

I've heard that this is why the Joyent cloud runs containers on bare-metal, while other cloud providers like AWS or Google run containers in VMs.

## Ramdisk is where SmartOS feels at home

General purpose OSes have to be installed on disk to function.

SmartOS on the other hand boots of and ISO or USB stick or PXE booted and it runs entirely in RAM. It has no installation to disk option.

Here are some arguments about why booting from RAM is a feature in SmartOS. [7]

The SmartOS hypervisor/OS, or what is called the **global zone**, [10] is mostly Read-Only.

I've seen recently in the Linux world this kind of approach by the people behind CoreOS. Surely they can draw more inspiration from the SmartOS/OpenSolaris developers.

## How can anyone test it ?

I've tested it by using the SmartOS iso and booting it in a KVM VM.

I could have achieved the same thing by booting of the SmartOS USB drive.

If you have a type of virtualisation on your laptop/desktop ( KVM, Virtualbox, VMware ...) than you can give it a spin in a VM. [8]

## What can a user run on SmartOS ?

A user can run KVM VMs and SmartOS OS virtualisation zones.

Since I'm running SmartOS in KVM, even if I have enabled KVM passthrough on my desktop, I haven't tried to run KVM VMs because the boot sequence of SmartOS says that KVM is not supported on my VM, therefore I've only been able to run zones.

## SmartOS hypervisor

After booting from the iso image or the USB image, you'll follow a few basic questions to setup networking and the ZFS pools in the global zone.

<span>SmartOS global zone </span>

```
1
2
3
4
5
6
7
8
9
10
11
12
13``` | ```
[root@smartos ~]# ifconfig -a
lo0: flags=2001000849<UP,LOOPBACK,RUNNING,MULTICAST,IPv4,VIRTUAL> mtu 8232 index 1
 inet 127.0.0.1 netmask ff000000
rtls0: flags=1004943<UP,BROADCAST,RUNNING,PROMISC,MULTICAST,DHCP,IPv4> mtu 1500 index 2
 inet 10.110.110.131 netmask ffffff00 broadcast 10.110.110.255
 ether 52:54:0:33:ea:3a
lo0: flags=2002000849<UP,LOOPBACK,RUNNING,MULTICAST,IPv6,VIRTUAL> mtu 8252 index 1
 inet6 ::1/128
[root@smartos ~]# 
[root@smartos ~]# uname -a
SunOS smartos 5.11 joyent_20160121T174331Z i86pc i386 i86pc
[root@smartos ~]# zonename
global```
------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Once that is done you're all setup to start running virtual workloads.

Another very useful feature of SmartOS is that SmartOS treats all 4 types of virtualisation described above as the same thing:

- a disk image of some type
- a bit of json metadata
- a virtualisation wrapper( KVM, zones) that starts using that disk image and the json metadata

All 4 types of virtualisation are created, lifecycle managed, and destroyed using the exact same tools:

- disk image manager `imgadm`
- virtual machine manager `vmadm`

That is it!

No more running `docker ...` or `rkt ..` for a container workload, then `qemu-system-x86_64` or interfacing with libvirt for KVM VM, each coming with its own tool for creating, lifecycle managing and destroying the virtual workloads.

Disclaimer: all zones that I'll show you how to start are started using the "admin" networking, which basically means they'll all be in bridged network mode and you'll be able to access them on your internal network as if they were other separate physical hardware.

## SmartOS zone

Lets run another instance of SmartOS, as an isolated zone, lets say for SmartOS package building !

- find some SmartOS disk images provided by Joyent:

<span>find SmartOS datasets </span>

```
1
2
3
4
5
6
7
8
9
10
11``` | ```
imgadm avail |grep base | tail -n10
3c0e76fe-0563-11e5-a0d7-9fe1e24b554c base-multiarch 15.1.1 smartos zone-dataset 2015-05-28
2bd52afe-3474-11e5-b07d-c7fb14b2c9e8 base-32 15.2.0 smartos zone-dataset 2015-07-27
5c7d0d24-3475-11e5-8e67-27953a8b237e base-64 15.2.0 smartos zone-dataset 2015-07-27
9caff6c6-3476-11e5-9951-bf98c6cb8636 base-multiarch 15.2.0 smartos zone-dataset 2015-07-27
7bcfc9c8-6e9a-11e5-8d57-73e262d7338e base-32 15.3.0 smartos zone-dataset 2015-10-09
842e6fa6-6e9b-11e5-8402-1b490459e334 base-64 15.3.0 smartos zone-dataset 2015-10-09
9250f5a8-6e9c-11e5-9cdb-67fab8707bfd base-multiarch 15.3.0 smartos zone-dataset 2015-10-09
543ef738-beb5-11e5-bf3d-675487324488 base-32-lts 15.4.0 smartos zone-dataset 2016-01-19
96bcddda-beb7-11e5-af20-a3fb54c8ae29 base-64-lts 15.4.0 smartos zone-dataset 2016-01-19
f58ce4f2-beb9-11e5-bb02-e30246d71d58 base-multiarch-lts 15.4.0 smartos zone-dataset 2016-01-19```
------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

- download the zfs volume into your local pool

```
1
2``` | ```
imgadm import 96bcddda-beb7-11e5-af20-a3fb54c8ae29
...```
---------- | -------------------------------------------------------------

- create a json description of the zone you'd like to start

```
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17``` | ```
{
 "brand": "joyent",
 "image_uuid": "96bcddda-beb7-11e5-af20-a3fb54c8ae29",
 "alias": "smartosz01",
 "hostname": "smartosz01",
 "max_physical_memory": 512,
 "quota": 10,
 "resolvers": ["8.8.8.8", "208.67.220.220"],
 "nics": [
 {
 "nic_tag": "admin",
 "ip": "10.110.110.142",
 "netmask": "255.255.255.0",
 "gateway": "10.110.110.1"
 }
 ]
}```
------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

- start the SmartOS zone from the disk image downloaded and the json description

```
1
2``` | ```
vmadm create -f smartos-zone.json
Successfully created VM 16021e9e-7e2f-4294-f7db-86dea02198be```
---------- | -----------------------------------------------------------------------------------------------------

- we can see the zone running by interogating the global zone stat tool ( equivalent to top )

```
1
2
3
4
5
6
7
8
9``` | ```
prstat -Z
...
 2933 root 23M 15M sleep 1 0 0:00:00 0.0% fmd/28
 7582 root 6992K 1128K sleep 51 0 0:00:00 0.0% sshd/1
 168 root 4516K 2752K sleep 29 0 0:00:00 0.0% devfsadm/8
ZONEID NPROC SWAP RSS MEMORY TIME CPU ZONE
 6 15 55M 33M 0.8% 0:00:02 1.4% 16021e9e-7e2f-4294-f7db-86d _0 55 322M 194M 4.7% 0:00:20 0.7% global 2 10 251M 31M 0.7% 0:00:00 0.0% eb5b5aad-54c6-6915-c1c8-9cc_```
------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

- either login in the zone from the SmartOS console via `zlogin` or simply ssh using the ip in the json description

```
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24``` | ```
zlogin 16021e9e-7e2f-4294-f7db-86dea02198be
...
[Connected to zone '16021e9e-7e2f-4294-f7db-86dea02198be' pts/4]
 **. . _| |_ | .-. . . .-. :--. |- |__| ;| || |(.-' | | | |**| `--'`-' `;-|`-' ' ' `-' / ; Instance (base-64-lts 15.4.0)`-' <https://docs.joyent.com/images/smartos/base>
[root@smartosz01 ~]# zonename
16021e9e-7e2f-4294-f7db-86dea02198be
[root@smartosz01 ~]# uname -a
SunOS smartosz01 5.11 joyent_20160121T174331Z i86pc i386 i86pc Solaris
[root@smartosz01 ~]#
[root@smartosz01 ~]# psrinfo -v
Status of virtual processor 0 as of: 01/31/2016 20:15:15
 on-line since 01/31/2016 20:12:58.
 The i386 processor operates at 3600 MHz,
 and has an i387 compatible floating point processor.
Status of virtual processor 1 as of: 01/31/2016 20:15:15
 on-line since 01/31/2016 20:12:59.
 The i386 processor operates at 3600 MHz,
 and has an i387 compatible floating point processor.```
--------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

- install packages and start building

## LX branded zone - full Linux userland

This type of virtualisation resembles to OpenVZ or LXC virtualisation in Linux, a full OS operating system running in a "container"

This time we'll boot a full debian8 userland on the SmartOS kernel using the lx branded zone.

We'll follow the same steps and use the same tools to boot into the debian8 zone as we did for the SmartOS zone.

- find some debian disk images provided by Joyent:

```
1
2
3
4
5
6
7
8
9
10
11``` | ```
imgadm avail |grep debian|tail -n10
a781a350-07f4-11e5-9372-5f2886027fbc lx-debian-7 20150601 linux lx-dataset 2015-06-01
1187b54a-15ca-11e5-a80c-275e2f64f91e debian-7 20150618 linux lx-dataset 2015-06-18
82d952c4-1b7b-11e5-a299-bb55cb08eab1 debian-7 20150625 linux lx-dataset 2015-06-25
a00cef0e-1e73-11e5-b628-0f24cabf6a85 debian-7 20150629 linux lx-dataset 2015-06-29
d8d81aee-20cf-11e5-8503-2bc101a1d577 debian-7 20150702 linux zvol 2015-07-02
2f56d126-20d0-11e5-9e5b-5f3ef6688aba debian-8 20150702 linux zvol 2015-07-02
380539c4-3198-11e5-82c8-bf9eeee6a395 debian-7 20150724 linux lx-dataset 2015-07-24
7c815c22-4606-11e5-8bb5-9f853c19be54 debian-7 20150819 linux lx-dataset 2015-08-19
5fb104e4-6af5-11e5-a952-ff6eb14ca518 debian-7 20151005 linux lx-dataset 2015-10-05
1adf7176-8679-11e5-9ff7-3beedf8060b9 debian-8 20151109 linux lx-dataset 2015-11-09```
------------------------------ | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

- download the **lx-dataset** zfs volume into your local pool, the **zvol** volume is for KVM VMs

```
1
2``` | ```
imgadm import 1adf7176-8679-11e5-9ff7-3beedf8060b9
....```
---------- | --------------------------------------------------------------

- get the kernel_version from the zfs volume metadata

```
1
2``` | ```
imgadm show 1adf7176-8679-11e5-9ff7-3beedf8060b9 |grep kern
 "kernel_version": "3.16.0"```
---------- | ----------------------------------------------------------------------------------------------

- create a json description of the zone you'd like to start

```
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18``` | ```
{
 "brand": "lx",
 "image_uuid": "1adf7176-8679-11e5-9ff7-3beedf8060b9",
 "alias": "debianz01",
 "hostname": "debianz01",
 "kernel_version": "3.16.0",
 "max_physical_memory": 512,
 "quota": 10,
 "resolvers": ["8.8.8.8", "208.67.220.220"],
 "nics": [
 {
 "nic_tag": "admin",
 "ip": "10.110.110.145",
 "netmask": "255.255.255.0",
 "gateway": "10.110.110.1"
 }
 ]
}```
--------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

- start the debian zone from the disk image downloaded and the json description

```
1
2``` | ```
vmadm create < /root/zones-specs/debian-lx-zone.json
Successfully created VM 28bef743-dc95-c0c9-ed90-9c0bcf31bef8```
---------- | ------------------------------------------------------------------------------------------------------------------------

- either login in the zone from the SmartOS console via `zlogin` or simply ssh using the ip in the json description ( note the **virtual linux** in the output of `uname` )

```
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16``` | ```
zlogin 28bef743-dc95-c0c9-ed90-9c0bcf31bef8
[Connected to zone '28bef743-dc95-c0c9-ed90-9c0bcf31bef8' pts/11]
Linux 28bef743-dc95-c0c9-ed90-9c0bcf31bef8 3.16.0 BrandZ virtual linux x86_64 __ . ._ | | _| .-. . . .-. :--. |- |_ _| ;| || |(.-' | | | |__| `--'`-' `;-|`-' ' ' `-' / ; Instance (Debian 8.1 (jessie) 20151109)`-' <https://docs.joyent.com/images/container-native-linux>
...
apt --version
apt 1.0.9.8.1 for amd64 compiled on Jun 10 2015 09:42:07
Usage: apt [options] command
...
 uname -a
Linux eb5b5aad-54c6-6915-c1c8-9cca817b4b4b 3.16.0 BrandZ virtual linux x86_64 GNU/Linux```
--------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

- use the debian8 zone as you see fit

## LX branded zone - docker container

This is still an LX branded zone ( Linux userland on SmartOS kernel ) but it will boot and run a docker disk container from docker hub. [9]

The interesting part is that docker containers on smartOS appear on the network bridge like any other VMs if you launch them on the "admin" network.

Lets launch a docker container in a SmartOS zone:

- add the docker hub source for `imgadm`

```
1``` | ```
imgadm sources --add-docker-hub```
-------- | --------------------------------------

- import the disk image ( this import downloads from the docker hub, _not_ from joyent )

```
1
2
3``` | ```
imgadm import busybox
Importing 0be24e0e-04e4-6110-9ea4-dd6264d65cb0 (docker.io/busybox:latest) from "[https://docker.io"](https://docker.io&quot);
...```
------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------

- create the zone specification

```
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34``` | ```
{
"alias": "busybox",
"image_uuid": "0be24e0e-04e4-6110-9ea4-dd6264d65cb0",
"nics": [
 {
 "interface": "net0",
 "nic_tag": "admin",
 "gateway": "10.110.110.1",
 "netmask": "255.255.255.0",
 "primary": true,
 "ip": "10.110.110.146"
 }
],
"brand": "lx",
"kernel_version": "3.13.0",
"docker": true,
"cpu_shares": 1000,
"zfs_io_priority": 1000,
"max_lwps": 2000,
"max_physical_memory": 256,
"max_locked_memory": 256,
"max_swap": 1024,
"cpu_cap": 1000,
"tmpfs": 1024,
"maintain_resolvers": true,
"resolvers": [
 "10.10.1.7",
 "8.8.8.8"
],
"internal_metadata": {
 "docker:cmd": "[\"/bin/sleep\", \"300\"]"
},
"quota": 7
}```
--------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

- start the docker container zone from the disk image downloaded and the json description

```
1
2``` | ```
vmadm create -f docker-busybox-lx-zone.json
Successfully created VM e931e355-4b09-e248-b8fe-c538c279dfe3```
---------- | ---------------------------------------------------------------------------------------------------------------

```
1
2
3
4
5``` | ```
[root@smartos ~]# vmadm list
UUID TYPE RAM STATE ALIAS
e931e355-4b09-e248-b8fe-c538c279dfe3 LX 256 stopped busybox
16021e9e-7e2f-4294-f7db-86dea02198be OS 512 running smartosz01
eb5b5aad-54c6-6915-c1c8-9cca817b4b4b LX 512 running debianz01```
---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

- depending on what you're running in the docker container either login in the zone from the SmartOS console via `zlogin` ( same result as `docker exec` ), or simply ssh using the ip in the json description, or just access the application running in the docker container

## Conclusions

SmartOS comes by default equiped with:

- `ZFS` as the default filesystem, ZFS being the most advanced filesystem today
- illumos kernel and zones for OS virtualisation which can give you a better resource utilisation and it has security features built-in
- `DTrace` which is the most advanced debugger to date
- KVM to be able to virtualise other operating systems other than SmartOS, running on SmartOS

From the SmartOS wiki: [12]

```
1
2
3
4
5
6
7``` | ```
An important aspect of SmartOS is that both OS (Zones) and KVM virtual machines are both built on Zones technology.
In the case of OS virtualization, the guest virtual machine is provided with a complete userland environment on which to run applications directly.
In the case of KVM virtualization, the KVM qemu process will run within a stripped down Zone.
This offers a variety of advantages for administration, including a common method for managing resource controls, network interfaces, and administration.
It also provides KVM guests with an additional layer of security and isolation not offered by other KVM platforms.
Finally, VM's are described in JSON. Both administrative tools, imgadm and vmadm, accept and return all data in JSON format.
This provides a simple, consistent, and programmatic interface for creating and managing VM's.```
-------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

I'm impressed by its virtualisation tooling consistency and by the OS feature set as a virtualisation platform !

To me SmartOS looks like the perfect virtualisation platform, one of the most advanced platform hypervisors OSes if not the most advanced platform hypervisor OS these days.

## Resources and inspiration

[1] - <https://www.reddit.com/r/IAmA/comments/31ny87/i_am_the_cto_of_joyent_the_father_of_dtrace_and/>

[2] - <https://www.youtube.com/watch?v=sYQ8j02wbCY>

[3] - <https://smartos.org/>

[4] - <http://docs.vrocket.io/pages/viewpage.action?pageId=557106>

[5] - <http://wiki.illumos.org/display/illumos/illumos+Home>

[6] - <https://en.wikipedia.org/wiki/Solaris_Containers>

[7] - <https://www.youtube.com/watch?v=ieGWbo94geE>

[8] - <https://wiki.smartos.org/display/DOC/Welcome+to+SmartOS>

[9] - <http://www.smllr.nl/2015/10/11/docker-on-smartos-a-retry/>

[10] - <http://www.perkin.org.uk/posts/smartos-and-the-global-zone.html>

[11] - <https://www.reddit.com/r/IAmA/comments/31ny87/i_am_the_cto_of_joyent_the_father_of_dtrace_and/cq41qa5>

[12] - <https://wiki.smartos.org/display/DOC/Home>
