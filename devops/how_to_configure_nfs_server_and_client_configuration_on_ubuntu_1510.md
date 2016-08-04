# How to configure NFS Server and Client Configuration on Ubuntu 15.10

[Original URL](http://www.ubuntugeek.com/16480.html)

> NFS was developed at a time when we weren't able to share our drives like we are able to today – in the Windows environment. It offers the ability to share the hard disk space of a big server with...

NFS was developed at a time when we weren't able to share our drives like we are able to today – in the Windows environment. It offers the ability to share the hard disk space of a big server with many smaller clients. Again, this is a client/server environment. While this seems like a standard service to offer, it was not always like this. In the past, clients and servers were unable to share their disk space. Thin clients have no hard drives and thus need a "virtual" hard-disk. The NFS mount their hard disk from the server and, while the user thinks they are saving their documents to their local (thin client) disk, they are in fact saving them to the server. In a thin client environment, the root, usr and home partitions are all offered to the client from the server via NFS.

**NFS Advantages**

• Local workstations use less disk space because commonly used data can be stored on a single machine and still remain accessible to others over the network.

• There is no need for users to have separate home directories on every network machine. Home directories could be set up on the NFS server and made available throughout the network.

• Storage devices such as floppy disks, CDROM drives, and Zip® drives can be used by other machines on the network. This may reduce the number of removable media drives throughout the network.

**Install NFS Server on Ubuntu 15.10**

Open the terminal and run the following command

> sudo apt-get install nfs-kernel-server nfs-common portmap

When configuring portmap do **not** bind loopback. If you do you can either edit /etc/default/portmap using the following

> sudo vi /etc/default/portmap

or use the following command

> sudo dpkg-reconfigure portmap

Restart Portmap using the following command

> sudo /etc/init.d/portmap restart

**NFS Server Configuration**

NFS exports from a server are controlled by the file /etc/exports. Each line begins with the absolute path of a directory to be exported, followed by a space-seperated list of allowed clients.

You need to edit the exports file using the following command

> sudo vi /etc/exports

Here are some quick examples of what you could add to your /etc/exports

For Full Read Write Permissions allowing any computer from 192.168.1.1 through 192.168.1.255

> /files 192.168.1.1/24(rw,no_root_squash,async)

Or for Read Only from a single machine

> /files 192.168.1.2 (ro,async)

save this file and exit

A client can be specified either by name or IP address. Wildcards (*) are allowed in names, as are netmasks (e.g. /24) following IP addresses, but should usually be avoided for security reasons.

A client specification may be followed by a set of options, in parenthesis. It is important not to leave any space between the last client specification character and the opening parenthesis, since spaces are intrepreted as client seperators.

Now you need to restart NFS server using the following command

> sudo /etc/init.d/nfs-kernel-server restart

If you make changes to /etc/exports on a running NFS server, you can make these changes effective by issuing the command

> sudo exportfs -a

**Install NFS client support on Ubuntu**

Open the terminal and run the following command

> sudo apt-get install portmap nfs-common

This will install all the required packages for nfs client

**Mounting manually**

Example to mount server.mydomain.com:/files to /files. In this example server.mydomain.com is the name of the server containing the nfs share, and files is the name of the share on the nfs server

The mount point /files must first exist on the client machine.

Create files directory using the following command

> sudo mkdir files

You need to mount the share using the following command

> sudo mount server.mydomain.com:/files /files

Now you may need to restart services using the following command

> sudo /etc/init.d/portmap restart

> sudo /etc/init.d/nfs-common restart

Mounting at boot using /etc/fstab

If you want to mount using fstab file

> sudo vi /etc/fstab

In this example my /etc/fstab was like this

> server.mydomain.com:/files /files nfs rsize=8192,wsize=8192,timeo=14,intr

Change "servername.mydomain.com:/files", and "/files" to match your server name,share name, and the name of the mount point you created.

**Firewall Ports for NFS**

If you have a firewall you need to make sure ports 32771, 111 and 2049 are open

**Testing Your Configuration**

Use the following command in terminal to test

> mount /files

the mount point /files will be mounted from the server.

**Possible error and Solution**

If you are getting the following error

rpcinfo: can't contact portmapper: RPC: Remote system error – No such file or directory

**Solution**

From the terminal run the following command

sudo update-rc.d rpcbind enable && sudo update-rc.d nfs-common enable

## **Sponsored Link**

### Related posts
