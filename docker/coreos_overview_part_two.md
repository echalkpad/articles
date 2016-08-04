# CoreOS Overview, Part Two

[Original URL](https://deis.com/blog/2016/coreos-overview-p2)

> This is the second post in a series looking at CoreOS. In my last post, we looked at how CoreOS is different from other Linux systems, atomic upgrades and release channels, and the basics of cluster...

This is the second post in a series looking at CoreOS.

In my [last post](https://deis.com/blog/2016/coreos-overview-p1), we looked at how CoreOS is different from other Linux systems, atomic upgrades and release channels, and the basics of cluster discovery.

In this post, we take a closer look at cloud-config and etcd. We'll also look at a few common cluster architectures.

## Cloud-Config

Cloud-config allows you to declaratively customize various OS-level items, such as network configuration, user accounts, and systemd units (which we'll cover the next post). This came from Ubuntu and was modified a bit to fit the needs of CoreOS.

At the core of every CoreOS cluster machine, there is the bootstrap mechanic _coreos-cloudinit_. The coreos-cloudinit program the cloud-config file when it configures the OS after startup or during runtime.

[]()

### What Is Cloud-Config Anyway?

It all starts with the `cloud-config` file, which uses [YAML](https://en.wikipedia.org/wiki/YAML), with whitespace and new lines to delimit lists, associative arrays, and values.

Formating is crucial. Before you deploy your cloud-config on a node, you should validate it with the [online tool](https://coreos.com/validate/) provided by CoreOS.

A cloud-config file must start with `#cloud-config`, followed by an associative array which has zero or more keys.

Here are some frequently used keys:

- `coreos`

  - Handles all CoreOS specifics like `update strategy`, `etcd2`, `fleet`, `flannel`, `units` and so on

- `write_files`

  - Directive defines a set of files to create on the local filesystem

- `ssh_authorized_keys`

  - Adds public SSH keys which will be authorized for the core user

Here's an example cloud-config file:

```
#cloud-config
hostname: core-01
coreos:
 update:
 reboot-strategy: off
 etcd2:
 name: core-01
 initial-advertise-peer-urls: http://127.0.0.1:2380
 initial-cluster-token: core-01_etcd
 initial-cluster: core-01=http://127.0.0.1:2380
 initial-cluster-state: new
 listen-peer-urls: http://0.0.0.0:2380,http://0.0.0.0:7001
 listen-client-urls: http://0.0.0.0:2379,http://0.0.0.0:4001
 advertise-client-urls: http://0.0.0.0:2379,http://0.0.0.0:4001
 units:
 - name: etcd2.service
 command: start
 - name: fleet.service
 command: start
 - name: docker-tcp.socket
 command: start
 enable: true
 content: |
 [Unit]
 Description=Docker Socket for the API

 [Socket]
 ListenStream=2375
 BindIPv6Only=both
 Service=docker.service

 [Install]
 WantedBy=sockets.target
 - name: docker.service
 command: start
 drop-ins:
 - name: 50-insecure-registry.conf
 content: |
 [Unit]
 [Service]
 Environment=DOCKER_OPTS='--insecure-registry="0.0.0.0/0"'
write-files:
 - path: /etc/conf.d/nfs
 permissions: '0644'
 content: |
 OPTS_RPC_MOUNTD=""
ssh_authorized_keys:
 - ssh-rsa 121313dqx1e123e12… user@my_mac
```

For more information about the cloud-config configuration parameters, see [the documentation](https://coreos.com/os/docs/latest/cloud-config.html).

## Etcd 2.x

etcd is an open source, distributed, key-value store that provides a reliable way to store data across a cluster of machines.

etcd runs on each etcd cluster's central services machine (don't worry, we'll explain this later in the post) and tolerates machine failure, including failure of the leader (a new leader will be elected if that happens).

Data is replicated across etcd nodes using [the Raft consensus algorithm](https://raftconsensus.github.io/). Consensus is a fundamental problem in fault-tolerant distributed systems. Consensus involves multiple servers agreeing on values. Once they reach a decision on a value, that decision is final.

Deis uses etcd to store its data. Kubernetes (an open source orchestration system for containers) uses etcd to store its data too.

### Optimal etcd Cluster Size

The recommended etcd cluster size is three, five, or seven. Although a larger cluster provides better fault tolerance, the write performance is reduced because data needs to be replicated to more machines.

In most cases, three or five members in a cluster is enough.

It is recommended to have an odd number of members in a cluster. Having an odd number of members doesn't change the number needed for majority, but you gain a higher tolerance for failure by adding the extra member.

You can see this in practice when comparing even and odd-sized clusters:

#### Fault Tolerance Table

Cluster Size | Majority | Failure Tolerance
------------ | -------- | -----------------
1            | 1        | 0
3            | 2        | 1
4            | 3        | 1
5            | 3        | 2
6            | 4        | 2
7            | 4        | 3
8            | 5        | 3

As you can see, adding another member to bring the size of cluster up to an odd size is always worth it.

During a network partition, an odd number of members also guarantees that there will almost always be a majority of the cluster that can continue to operate and be the source of truth when the partition ends.

For more about the etcd discovery see [the documentation](https://github.com/coreos/etcd/blob/master/Documentation/clustering.md#etcd-discovery).

An example of etcd central services machines setup:

```
#cloud-config
coreos:
 etcd2:
 # generate a new token for each unique cluster from https://discovery.etcd.io/new
 discovery: https://discovery.etcd.io/60887e46255f4efew37077ccf12b5f06a54a
 initial-advertise-peer-urls: http://$private_ipv4:2380
 # listen on both the official ports and the legacy ports
 # legacy ports can be omitted if your application doesn't depend on them
 listen-client-urls: http://0.0.0.0:2379,http://0.0.0.0:4001
 listen-peer-urls: http://$private_ipv4:2380,http://$private_ipv4:7001
 data-dir: /var/lib/etcd2
```

The <https://discovery.etcd.io/new?size=3> URL was used, so the initial etcd cluster size must be 3 machines.

### Proxy Mode

As you bootstrap a separate etcd cluster for central services, your worker machines need to connect to that etcd cluster to register themselves as a cluster member and to be able to receive deployed cluster fleet units, get OS updates control, and so on.

The easiest way to do that is to run each cluster worker node in the etcd _proxy mode_ instead of starting etcd server locally.

Running etcd as a proxy allows for easy discovery of etcd on your network, because it can run on each machine as a local service. In this mode, etcd acts as a reverse proxy and forwards client requests to an active etcd node. Because the etcd proxy does not participate in the consensus replication of the etcd cluster, it neither increases the resilience nor decreases the write performance of the etcd cluster.

Services that needs to talk to etcd can connect to localhost.

etcd currently supports two proxy modes: `readwrite` and `readonly`.

The default mode is `readwrite`, which forwards both read and write requests to the etcd cluster.

The proxy rotates through the list of cluster members periodically to avoid forwarding all traffic to a single member.

An example of etcd proxy setup:

```
#cloud-config
coreos:
 etcd2:
 listen-client-urls: http://0.0.0.0:2379,http://0.0.0.0:4001
 advertise-client-urls: http://0.0.0.0:2379,http://0.0.0.0:4001
 discovery: https://discovery.etcd.io/60887e46255f4efew37077ccf12b5f06a54a
 proxy: on
```

This accepts client connectons via localhost and forwards them to the etcd central services cluster via this discovery URL:

`https://discovery.etcd.io/60887e46255f4efew37077ccf12b5f06a54a`

For more about the etcd proxy see [the documentation](https://github.com/coreos/etcd/blob/master/Documentation/proxy.md).

## Cluster Architectures

Depending on the size of your cluster and how it's going to be used, there are a few common cluster architectures that _must_ be followed.

It is strongly recommended that you set aside a fixed number of machines to be dedicated to running etcd central cluster services and a separate fixed number of machines for the distributed controllers for applications like Deis, Kubernetes, Mesos, and OpenStack.

Separating out these services into fixed number machines allows you to make sure they are distributed across datacenter cabinets and availability zones and setting up static networking to allow for easy cluster bootstrapping.

If you're worried about relying on the discovery service, this architecture will remove your worries.

Then you can set up your worker machines to be connected to central services. In such a setup you can scale up/down your machines to meet your demand and not to be worried about breaking quorum of your etcd cluster.

For more information about setting up different cluster architectures with provided cloud-config files, see [the documentation](https://coreos.com/os/docs/latest/cluster-architectures.html).

Let's go into a few examples.

### Docker Dev Environment on a Laptop

If you're developing locally, but plan to run containers in production, it helps to mirror that environment locally.

This can easily be done by running Docker commands on your laptop to control a CoreOS VM in VMware Fusion or VirtualBox.

Here's a laptop development environment with a CoreOS VM:

![](https://deis.com/images/blog-images/coreos-overview-2-0.png)

### An Easy Development and Testing Cluster

Here's a CoreOS cluster optimized for development and testing:

![](https://deis.com/images/blog-images/coreos-overview-2-1.png)

A setup like this is great for running your development, testing, and staging environments on bare metal or cloud VM instances.

When you're first getting started with CoreOS, it's common to frequently tweak your cloud-config. This requires booting, rebooting, and destroying many machines. Instead of being slowed down by generating new discovery URLs and bootstrapping etcd, it's easier to start a single etcd node. This way you are free to boot as many machines as you'd like that read from the etcd node.

All the features of fleet, locksmith, and etcdctl continue to work properly, but connect to the etcd cluster via etcd proxy instead of using the worker's local etcd instance. Since etcd is running only in a proxy mode on all of the worker machines, you gain a little extra CPU and RAM.

This environment is now set up for optimal performance.

Pull the plug on a machine and watch fleet reschedule the units, max out the CPU, and so on!

### A Production Cluster With Central Services

A CoreOS cluster separated into central services and workers:

![](https://deis.com/images/blog-images/coreos-overview-2-2.png)

This setup is good for production. The cluster is separated into central services and workers.

For large clusters, it's recommended to set aside three to five machines to run central services.

After those are set up, you can boot as many workers as you want.

Each of the workers uses the distributed etcd cluster on the central machines via local etcd proxies.

fleet is used to bootstrap both the central services and jobs on the worker machines by taking advantage of machine metadata and global fleet units. We cover this in the [next post](https://deis.com/blog/2016/coreos-overview-p3)!

_Note: If you have Deis, Kubernetes, or other services which need dedicated machines, do not put those services onto etcd cluster machines. Instead, dedicate a fixed number machines for them also. It is not recommended to use etcd cluster machines to run anything other than etcd services._

## Wrap-Up

In this post, we looked at:

- The cloud-config file
- Why the ideal etcd cluster size is an odd number
- Running etcd in proxy mode
- Some common etcd cluster setups

In the [next post](https://deis.com/blog/2016/coreos-overview-p3) we take a look at systemd and fleet in a bit more detail.

--------------------------------------------------------------------------------

This is the third and final post in a series looking at CoreOS.

In my [last post](https://deis.com/blog/2016/coreos-overview-p2), we looked at the cloud-config file, running etcd in proxy mode, and some common etcd cluster setups.

In this post, we take a closer look at systemd, unit files, Fleet, and fleetctl.

## systemd Overview

systemd is an init system used by CoreOS that provides many powerful features for starting, stopping, monitoring, and restarting process. On CoreOS, systemd is used to manage the lifecycle of your Docker containers and also for different system bootstrap tasks.

Learning systemd would need a series of blog posts in itself. Here we only cover systemd to the extent that we need to run systemd units for Docker containers on CoreOS.

For more information about systemd, see [the documentation](http://www.freedesktop.org/wiki/Software/systemd/).

[]()

### Unit Files

systemd records initialization instructions for each daemon in a configuration file called _a unit_.

This system replaces the traditional System V per-daemon [init scripts](https://www.wikiwand.com/en/Init).

There are many different unit file types, but we'll only cover the _services_ type used to run Docker containers. We call these _service units_.

Here's a list of the most useful features, presented in the order they'll occur in the lifecycle of a service unit:

- `ExecStartPre`: commands that run before `ExecStart`
- `ExecStart`: main commands to run for this unit
- `ExecStartPost`: commands that run after all `ExecStart` commands have completed
- `ExecReload`: commands that run when this unit is reloaded via `systemctl reload foo.service`
- `ExecStop`: commands that run when this unit is considered failed or if it is stopped via `systemctl stop foo.service`
- `ExecStopPost`: commands that run after `ExecStop` has completed
- `RestartSec`: the amount of time to sleep before restarting a service (useful to prevent your failed service from attempting to restart itself every 100ms)

Let's create a simple unit file called `hello_world.service`:

```
[Unit]
Description=HelloWorldApp
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker rm busybox1
ExecStartPre=/usr/bin/docker pull busybox
ExecStart=/usr/bin/docker run --rm --name busybox1 busybox /bin/sh -c "while true; do echo Hello World; sleep 1; done"
ExecStop=/usr/bin/docker stop busybox1

[Install]
WantedBy=multi-user.target
```

Let's review the unit file we just created:

- The `Description` shows up in the systemd log (a unit's log can be checked with _journalctl_ or _systemdctl_).
- `After=docker.service` and `Requires=docker.service` means this unit only starts after `docker.service` is active. You can define as many of these as you want. You can delimit multiple `After` requirements using space as a separator.
- The `=-` is systemd syntax to ignore errors for this command. In our case, Docker will return a non-zero exit code if we try to stop a container that doesn't exist, so this is not an error for us.
- `ExecStartPre=-/usr/bin/docker rm busybox1` will remove the Docker image `busybox1` if such image exists. If there's no Docker image to remove, the script will just move on.
- `ExecStartPre=/usr/bin/docker pull busybox` will pull the `busybox` image from the Docker registry.
- `ExecStart=` allows you to specify any command that you'd like to run when this unit is started. Do not run Docker containers with `-d` because this will prevent the container from starting as a child of this process. systemd will think the process has exited and the unit will be stopped.
- `ExecStop=/usr/bin/docker stop busybox1` will stop the Docker container `busybox1`.
- `WantedBy=multi-user.target`" tells systemd to pull in the unit when starting `multi-user.target`.

Now to start a new unit, we need to tell systemd to create the symlink and then start the file:

```
$ sudo systemctl enable /etc/systemd/system/hello.service
$ sudo systemctl start hello.service
```

To verify the unit started, check the list of containers running with:

```
docker ps
```

Then, read the unit's output with journalctl, like so:

```
$ journalctl -f -u hello.service
-- Logs begin at Fri 2014-02-07 00:05:55 UTC. --
Feb 11 17:46:26 localhost docker[23470]: Hello World
Feb 11 17:46:27 localhost docker[23470]: Hello World
Feb 11 17:46:28 localhost docker[23470]: Hello World
[...]
```

Here, `-u` means unit, and `-f` means follow. Press CTRL-c to exit.

The systemd service units can only run and be controlled on a single machine. They are best used for simple tasks, like downloading files on reboot, and so on.

For more information about systemd units, see [the documentation](https://coreos.com/docs/launching-containers/launching/getting-started-with-systemd/).

## Fleet Overview

Fleet runs on top of systemd and controls systemd at the cluster level, creating a distributed systemd:

![](https://deis.com/images/blog-images/coreos-overview-3-0.png)

To run your services in the cluster, you must submit regular systemd units combined with [fleet-specific properties](https://coreos.com/docs/launching-containers/launching/fleet-unit-files/).

### fleetctl

fleetctl is a command line tool allowing you to control fleet units on local and remote CoreOS Clusters.

The fleetctl commands are very similar to systemctl commands, and we do not have to use sudo with fleetctl.

fleetctl commands can be run directly on a cluster machine.

Or, if you prefer to execute fleetctl from an external host (i.e. your workstation), a system environment variable can be used to tunnel communication with your fleet cluster over SSH, like so:

```
$ export FLEETCTL_TUNNEL=remote_machines_ip
```

Let's look at some of the tasks you can perform, listed with the required commands.

Loading and starting the unit:

```
$ fleetctl start hello_world.service
```

Checking the status of the unit:

```
$ fleetctl status hello_world.service
```

Stopping the service:

```
$ fleetctl stop hello_world.service
```

Viewing the content of service file:

```
$ fleetctl cat hello_world.service
```

If you want to upload the unit file without starting it:

```
$ fleetctl submit hello_world.service
```

Check the hello_world.service logs:

```
$ fleetctl journal hello_world.service
```

You can even ssh to the host that runs the unit:

```
$ fleetctl ssh hello_world.service
```

Listing all running fleet units:

```
$ fleetctl list-units
```

Listing fleet cluster machines:

```
$ fleetctl list-machines
```

### Unit Files

Unit files are the primary interaction with fleet.

As with systemd units, fleet units define what you want to do and how fleet should provision them. Fleet schedules a valid unit file to one or more machines in a cluster, taking in mind fleet's special properties from the `[X-Fleet]` section, which replace the systemd unit's `[Install]` section.

The rest of systemd's sections remain the same in fleet units.

Let's review fleet specific options:

- `MachineID`: Unit will be scheduled to the machine identified by a given string
- `MachineOf`: Limit eligible machines to the one that hosts a specific unit
- `MachineMetadata`: Limit eligible machines to those with this specific metadata
- `Conflicts`: Prevent a unit from being collocated with other units using glob-matching on the other unit names
- `Global`: Schedule this unit on all machines in the cluster

All of these go in the `[X-Fleet]` section of the unit file.

A unit is considered invalid if options other than `MachineMetadata` are provided alongside `Global=true`. Global units are very useful if you want to schedule the same unit on all machines or on some machines which `MachineMetadata` matches some value.

Let's convert the systemd unit file `hello_world.service` we used above to a fleet unit:

```
[Unit]
Description=HelloWorldApp
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker rm busybox1
ExecStartPre=/usr/bin/docker pull busybox
ExecStart=/usr/bin/docker run --rm --name busybox1 busybox /bin/sh -c "while true; do echo Hello World; sleep 1; done"
ExecStop=/usr/bin/docker stop busybox1

[X-Fleet]
MachineMetadata=group=hw_webservers
Conflicts=hello_world@*
```

What does `hello_world@*` do in this example?

Well, let's say we have two identical `hello_world@1.service` and `hello_world@2.service` files.

We can start them on the cluster like so:

```
$ fleetctl start hello_world@*
```

These two fleet units will be deployed to servers with `MachineMetadata=group=hw_webservers` metadada set, and `Conflicts=hello_world@*` will prevent a unit from being collocated with other units using glob-matching on the other unit names.

Let's look at a few more examples with other fleet options for `hello_world.service` unit.

This schedules this unit on all machines in the cluster:

```
[X-Fleet]
Global=true
```

This schedules the unit on the machine that has the `some_other_unit.service` unit.

```
[X-Fleet]
MachineOf=some_other_unit.service
```

This schedules the unit to the machine with the ID 562999f8:

```
[X-Fleet]
MachineID="562999f8"
```

You can check machine ID with:

```
$ fleetctl list-machines
```

For more information about fleet units, see [the documentation](https://coreos.com/fleet/docs/latest/unit-files-and-scheduling.html).

### Run a Container in the Cluster

Running a single container is very easy.

All you need to do is provide a regular unit file without an `[Install]` section, like so:

```
[Unit]
Description=hello_docker
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker rm busybox1
ExecStartPre=/usr/bin/docker pull busybox
ExecStart=/usr/bin/docker run --rm --name busybox1 busybox /bin/sh -c "while true; do echo Hello Docker; sleep 1; done"
ExecStop=/usr/bin/docker stop busybox1
```

_Note: If you've been running Docker commands manually, make sure you don't copy a Docker run command that starts a container in detached mode (indicated by the `-d` switch). This will cause the unit to run for just a few seconds and then exit._

In this post, we looked at:

- Writing unit files for systemd
- How Fleet is used to create a distributed systemd
- Writing unit files for Fleet

Don't miss the previous two parts of this miniseries. In [part one](https://deis.com/blog/2016/coreos-overview-p1), we looked the basics of CoreOS, atomic upgrades and release channels, and cluster discovery. And in [part two](https://deis.com/blog/2016/coreos-overview-p2), we looked at cloud-config and etcd.

In my next miniseries, I'll be looking at Docker.
