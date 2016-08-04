# High-Availability Storage with HA-LVM

[Original URL](http://www.linuxjournal.com/content/high-availability-storage-ha-lvm)

> In recent years, there has been a trend in which data centers have been opting for commodity hardware and software over proprietary solutions. Why shouldn't they? It offers extremely low costs and...

In recent years, there has been a trend in which data centers have been opting for commodity hardware and software over proprietary solutions. Why shouldn't they? It offers extremely low costs and the flexibility to build an ecosystem the way it is preferred. The only limitation is the extent of the administrator's imagination. However, a question needs to be asked: "How would such a customized solution compare to its proprietary and more costly counterpart?"

Open-source projects have evolved and matured enough to stay competitive and provide the same feature-rich solutions that include volume management, data snapshots, data deduplication and so on. Although an often overlooked and longtime-supported concept is high availability.

The idea behind high availability is simple: eliminate any single point of failure. This ensures that if a server node or a path to the underlying storage goes down (planned or unplanned), data requests still can be served. Now there are multiple layers to a storage-deployed solution that can be configured for high availability and that is why this article focuses strictly on HA-LVM.

## HA-LVM

High Availability Logical Volume Manager (HA-LVM) is an add-on to the already integrated LVM suite. It enables a failover configuration for shared volumes--that is, if one server in a cluster fails or is taken down for maintenance, the shared storage configuration will fail over to the secondary server where all I/O requests will resume, uninterrupted. An HA-LVM configuration is an active/passive configuration. This means that a single server accesses the shared storage at any one time. In many cases, this is an ideal approach, as some of advanced LVM features, such as snapshot and data deduplication, are not supported in an active/active environment (when more than one server accesses the shared storage).

A very important component to HA-LVM is the CLVM or Clustered LVM dæmon. When enabled, the CLVM dæmon prevents corruption of LVM metadata and its logical volumes, which occurs if multiple machines make overlapping changes. Although in an active/passive configuration, this becomes less of a concern. To accomplish this, the dæmon relies on a Distributed Lock Manager or DLM. The purpose of the DLM is to coordinate disk access for CLVM.

The following example will cluster two servers that have access to the same external storage (Figure 1). This external storage could be a RAID-enabled or JBOD enclosure of disk drives, connected to the servers via a Fibre Channel, Serial Attached SCSI (SAS), iSCSI or other Storage Area Network (SAN) mapping. The configuration is storage protocol-agnostic and requires only that the clustered servers see the same shared block devices.

![](http://www.linuxjournal.com/files/linuxjournal.com/ufiles/imagecache/large-550px-centered/u1002061/11723f1.png) Figure 1\. A Sample Configuration of Two Servers Accessing the Same Shared Storage

## CLVM

CLVM is not compatible with MD RAID, as it does not support clusters yet.

## CLVM Dæmon

The CLVM dæmon distributes LVM metadata updates across the cluster, and it must be running on all nodes in that cluster.

## JBOD

A JBOD (or Just a Bunch Of Disks) is an architecture using multiple hard drives, but not in a redundant configuration.

## Configuring the Cluster

Almost all Linux distributions offer the required packages. However, the names may differ in each. You need to install lvm2-cluster (in some distributions, the package may be named clvm), the Corosync cluster engine, the Red Hat cluster manager (or cman), the Resource Group manager dæmon (or rgmanager) and all their dependencies on all participating servers. Even though the Red Hat cluster manager contains the Linux distribution of the same name in its package description, most modern distributions unrelated to Red Hat will list it in their repositories.

Once the appropriate clustering packages have been installed on all participating servers, the cluster configuration file must be configured to enable the cluster. To accomplish this, create and modify /etc/cluster/cluster.conf with the following:

```
<cluster name="lvm-cluster" config_version="1">
 <cman two_node="1" expected_votes="1" />
 <clusternodes>
 <clusternode name="serv-0001" nodeid="1">
 <fence>
 </fence>
 </clusternode>
 <clusternode name="serv-0002" nodeid="2">
 <fence>
 </fence>
 </clusternode>
 </clusternodes>
 <logging debug="on">
 </logging>
 <dlm protocol="tcp" timewarn="500">
 </dlm>
 <fencedevices>
 </fencedevices>
 <rm>
 </rm>
</cluster>
```

Note that the clusternode name is the server's hostname (change where necessary). Also, make sure the cluster.conf file is identical on all servers in the cluster.

The Red Hat cluster manager needs to be started:

```
$ sudo /etc/rc.d/init.d/cman start
Starting cluster: 
 Checking if cluster has been disabled at boot... [ OK ]
 Checking Network Manager... [ OK ]
 Global setup... [ OK ]
 Loading kernel modules... [ OK ]
 Mounting configfs... [ OK ]
 Starting cman... [ OK ]
 Waiting for quorum... [ OK ]
 Starting fenced... [ OK ]
 Starting dlm_controld... [ OK ]
 Tuning DLM kernel config... [ OK ]
 Starting gfs_controld... [ OK ]
 Unfencing self... [ OK ]
 Joining fence domain... [ OK ]
```

If a single node in the cluster is not active, it will appear as off-line:

```
$ sudo clustat
Cluster Status for lvm-cluster @ Sun Aug 3 11:31:51 2014
Member Status: Quorate

 Member Name ID Status
 ------ ---- ---- ------
 serv-0001 1 Online, Local
 serv-0002 2 Offline
```

Otherwise, when all servers are configured appropriately and the cman service is enabled, all nodes will appear with an `Online` status:

```
$ sudo clustat
Cluster Status for lvm-cluster @ Sun Aug 3 11:36:43 2014
Member Status: Quorate

 Member Name ID Status
 ------ ---- ---- ------
 serv-0001 1 Online
 serv-0002 2 Online, Local
```

You now have a working cluster. The next step is to enable the Clustered LVM in High Availability mode. In this scenario, you have a single volume from the shared storage enclosure mapped to both servers. Both servers are able to observe and access this volume as /dev/sdb.

The /etc/lvm/lvm.conf file needs to be modified for this. The locking_type parameter in the global section has to be set to the value 3\. It is set to 1 by default:

```
# Type of locking to use. Defaults to local file-based 
# locking (1).
# Turn locking off by setting to 0 (dangerous: risks metadata 
# corruption if LVM2 commands get run concurrently).
# Type 2 uses the external shared library locking_library.
# Type 3 uses built-in clustered locking.
# Type 4 uses read-only locking which forbids any operations 
# that might change metadata.
locking_type = 3
```

On one of the servers, create a volume group, logical volume and filesystem from the designated shared volume:

```
$ sudo pvcreate /dev/sdb

$ sudo vgcreate -cy shared_vg /dev/sdb

$ sudo lvcreate -L 50G -n ha_lv shared_vg

$ sudo mkfs.ext4 /dev/shared_vg/ha_lv

$ sudo lvchange -an shared_vg/ha_lv
```

The example above carves out a 50GB logical volume from the volume group and then formats it with an Extended 4 filesystem. The `cy` option used with the `vgcreate` (volume group create) command enables the volume group for clustered locking. The `an` option with the `lvchange` (logical volume change) command deactivates the logical volume. You will be relying on the CLVM and resource manager (read below) dæmons to handle activations based on the failover feature additions made in the same /etc/cluster/cluster.conf file created earlier. When active, the the shared volume will be accessible from /dev/shared_vg/ha_lv.

Add the necessary failover details to the cluster.conf file:

```
<rm> 
 <failoverdomains>
 <failoverdomain name="FD" ordered="1" restricted="0">
 <failoverdomainnode name="serv-0001" priority="1"/>
 <failoverdomainnode name="serv-0002" priority="2"/>
 </failoverdomain>
 </failoverdomains>
 <resources>
 <lvm name="lvm" vg_name="shared_vg" lv_name="ha-lv"/>
 <fs name="FS" device="/dev/shared_vg/ha-lv" 
 ↪force_fsck="0" force_unmount="1" fsid="64050" 
 ↪fstype="ext4" mountpoint="/mnt" options="" 
 ↪self_fence="0"/>
 </resources>
 <service autostart="1" domain="FD" name="serv" 
 ↪recovery="relocate">
 <lvm ref="lvm"/>
 <fs ref="FS"/>
 </service>
</rm>
```

The "rm" portion of the cluster.conf file utilizes the resource manager (or rgmanager). In this addition to the configuration file, you inform the cluster manager that serv-0001 should have ownership and sole access to the shared volume first. It will be mounted locally at the /mnt absolute path. If and when serv-0001 goes down for any reason, the resource manager then will perform a failover that will enable sole access to the shared volume, mounted at /mnt on serv-0002\. All pending I/O requests sent to serv-0001 will resume on serv-0002.

--------------------------------------------------------------------------------

On all servers, restart the cman service to enable the new configuration:

```
$ sudo /etc/rc.d/init.d/cman restart
```

Also, on all servers, start the rgmanager and clvmd services:

```
$ sudo /etc/rc.d/init.d/rgmanager start
Starting Cluster Service Manager: [ OK ]

$ sudo /etc/rc.d/init.d/clvmd start
Starting clvmd: [ OK ]
```

Assuming that no errors were observed, you now should have a running cluster configured in an active/passive configuration. You can validate this by checking the accessibility of the shared volume on all servers. It should be seen, enabled and mounted on serv-0001 and not on serv-0002\. Now comes the moment of truth--that is, testing the failover. Manually power down serv-0001\. You will notice the rgmanager kicking in and enabling/mounting the volume on serv-0002.

## Note:

To enable these services automatically on reboot, use chkconfig to start the services on all appropriate runlevels.

## Summary

In an ideal configuration, fencing agents will need to be configured in the /etc/cluster/cluster.conf file. The purpose of the fencing agent is to handle a problematic node before it causes noticeable issues to the cluster. For example, if a server suffers from a kernel panic, is not communicating with the other servers in the cluster, or something else just as devastating, the IPMI utilities can be configured to reboot the server in question:

```
<clusternode name="serv-0001" nodeid="1">
 <fence>
 <method name="1">
 <device name="ipmirecover1"/>
 </method>
 </fence>
 </clusternode>
 <clusternode name="serv-0002" nodeid="2">
 <fence>
 <method name="1">
 <device name="ipmirecover2"/>
 </method>
 </fence>
 </clusternode>

[ ... ]

 <fencedevices>
 <fencedevice agent="fence_ipmilan" ipaddr="192.168.1.50" 
 ↪login="ADMIN" passwd="ADMIN" name="ipmirecover1"/>
 <fencedevice agent="fence_ipmilan" ipaddr="192.168.10" 
 ↪login="ADMIN" passwd="ADMIN" name="ipmirecover2"/>
 </fencedevices>
```

The primary objective of HA-LVM is to provide the data center with enterprise-class fault tolerance at a fraction of the price. No one ever wants to experience server downtimes, and with an appropriate configuration, no one has to. From the data center to your home office, this solution can be deployed almost anywhere.

## Resources

clvmd(8): Linux man page

Appendix F. High Availability LVM (HA-LVM): <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Cluster_Administration/ap-ha-halvm-CA.html>
