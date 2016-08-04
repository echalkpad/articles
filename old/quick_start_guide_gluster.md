# Quick Start Guide -- Gluster

[Original URL](http://www.gluster.org/documentation/quickstart/)

> Here are the bare minimum steps you need to get Gluster up and running. If this is your first time setting things up, it is recommended that you choose your desired setup method (AWS, virtual...

Here are the bare minimum steps you need to get Gluster up and running. If this is your first time setting things up, it is recommended that you choose your desired setup method (AWS, virtual machines or baremetal) after step four, adding an entry to fstab. If you want to get right to it (and don't need more information), the steps below are all you need to get started.

You will need to have at least two X86 machines with a 64 bit OS and a working network connection. At least one gig of RAM is the bare minimum recommended for testing, and you will want at least 8GB in any system you plan on doing any real work on. A single cpu is fine for testing, as long as it is 64 bit. Each node should have a dedicated disk for Gluster - we'll be calling them "bricks". One of the nodes will be serving the gluster volume to clients, we'll be calling that "node01" and the other one "node02", and so on.

## Partition, Format and mount the bricks

Assuming you have a brick at /dev/sdb:

Create a single partition on the brick that uses the whole capacity.

## Format the partition

```
sudo mkfs.xfs -i size=512 /dev/sdb1
```

## Mount the partition as a Gluster "brick"

```
sudo mkdir -p /export/sdb1 && mount /dev/sdb1 /export/sdb1 && mkdir -p /export/sdb1/brick
```

## Add an entry to /etc/fstab

```
sudo echo "/dev/sdb1 /export/sdb1 xfs defaults 0 0" >> /etc/fstab
```

## Install Gluster packages on both nodes

```
sudo yum install glusterfs{,-server,-fuse,-geo-replication}
```

_Note: This example assumes Fedora 18 or later, where gluster packages are included in the official repository_

## Run the gluster peer probe command

**Note!** From node01 to the other nodes (do not peer probe the first node)

```
sudo gluster peer probe <ip or hostname of node02>
```

## Configure your Gluster volume

```
sudo gluster volume create testvol rep 2 transport tcp node01:/export/sdb1/brick node02:/export/sdb1/brick
```

## Test using the volume

```
sudo mkdir /mnt/gluster; mount -t glusterfs node01:/testvol; cp -r /var/log /mnt/gluster
sudo mkdir /mnt/gluster/hello_world
df -h
```
