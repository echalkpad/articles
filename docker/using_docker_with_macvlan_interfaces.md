# Using Docker with macvlan Interfaces

[Original URL](http://blog.scottlowe.org/2016/01/28/docker-macvlan-interfaces/)

> 28 January 2016 In this post, I'm going to show you how to use macvlan interfaces with Docker for networking. The use of macvlan interfaces presents an interesting networking configuration for...

<span class="post-date">28 January 2016</span>

 In this post, I'm going to show you how to use macvlan interfaces with [Docker](http://www.docker.com/) for networking. The use of macvlan interfaces presents an interesting networking configuration for Docker containers that may (depending on your use case) address issues with the standard Linux bridge configuration.

Macvlan interfaces, if you're unfamiliar with them, are a (somewhat) recent addition to the Linux kernel that enables users to add multiple MAC address-based logical interfaces to a single physical interface. These logical interfaces must reside in the same broadcast domain as the associated physical interface, which means that Docker containers attached to macvlan interfaces _also_ will be in the same broadcast domain as the associated physical interface. In other words, the Docker containers will be on the same network as the host--no IPTables rules, no Linux bridge, just attached directly to the host's network. This introduces some interesting possibilities (and potential challenges), but I'll save that discussion for a future post.

Right now, macvlan supported is implemented via [an unsupported Docker Network plugin hosted on GitHub](https://github.com/gopher-net/macvlan-docker-plugin). However, I suspect that the macvlan functionality found in this plugin will find its way into the core of Docker Network, and probably sooner rather than later.

You'll want to start by first confirming that your Linux distribution has support for macvlan interfaces. An easy way of doing this is to run the following commands (run these as root, or use `sudo`):

```
modprobe macvlan
lsmod | grep macvlan
```

If you get an error, or if the `lsmod` command returns no results, then you don't have macvlan support in your kernel. I tested this on Ubuntu 14.04 as well as Debian 8.1 and had no issues.

Assuming that your Linux distribution supports macvlan interfaces, the next step is to get the appropriate Linux plugin onto your system by cloning the GitHub repo:

```
git clone https://github.com/gopher-net/macvlan-docker-plugin
```

This will clone the repository into a directory named `macvlan-docker-plugin` beneath your current directory.

For the next few steps, you may want to use `screen` or `tmux`, at least at first. I couldn't find a way to daemonize the macvlan plugin (aside from adding a `&` at the end of the command).

First, launch the macvlan plugin with the following set of commands:

```
cd macvlan-docker-plugin/binaries
./macvlan-docker-plugin-0.2-Linux-x86_64 \
--macvlan-subnet "192.168.100.0/24" --gateway "192.168.100.1" \
--host-interface "eth1"
```

A quick note on these options:

- The `--macvlan-subnet` option needs to correspond to the subnet associated with the physical interface named with the `--host-interface` parameter.
- The `--host-interface` parameter should list the physical interface to which the macvlan interfaces should be associated.
- The `--gateway` option needs to correspond to the gateway on the network.

Next (in another terminal window or another session in `tmux`), create a Docker network using the macvlan driver:

```
docker network create -d macvlan --subnet=192.168.100.0/24 \
--gateway=192.168.100.1 -o host_iface=eth1 network-name
```

The values given to the `--subnet`, `--gateway`, and `-o host_iface` parameters all need to match the ones given to the macvlan driver when it was launched. In my testing with this plugin, I found that omitting the parameters when creating the network caused errors, even though the parameters used when creating the network are duplicates of the values used when launching the macvlan driver.

Finally, launch a Docker container attached to the Docker network you just created:

```
docker run -it --rm --net=network-name ubuntu
```

This will drop you at a Bash prompt in an Ubuntu container. From here, you can use `ping` to verify connectivity to other systems. You can also run `ip -d link list` within the container; note the last line of the output (shown here):

```
7: eth0@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN mode DEFAULT group default
 link/ether 5a:7b:f3:63:51:68 brd ff:ff:ff:ff:ff:ff promiscuity 0
 macvlan mode bridge
```

This clearly shows that the interface in the Docker container is a macvlan interface.

So there you have it--how to use macvlan interfaces to provide network connectivity to your Docker containers.

## More Resources

If you're interested in trying this out for yourself, check out the `docker-macvlan` directory in [my GitHub "learning-tools" repository](https://github.com/lowescott/learning-tools).

<span class="post-date">Tags: <a href="http://blog.scottlowe.org/tags/#CLI">CLI</a> · <a href="http://blog.scottlowe.org/tags/#Docker">Docker</a> · <a href="http://blog.scottlowe.org/tags/#Linux">Linux</a> · <a href="http://blog.scottlowe.org/tags/#Networking">Networking</a></span>

 

<span class="post-date">
  <strong> Previous Post: <a href="http://blog.scottlowe.org/2016/01/21/looking-ahead-2016-projects/">Looking Ahead: My 2016 Projects</a></strong>
</span>

**<span class="post-date"> Next Post: <a href="http://blog.scottlowe.org/2016/01/29/technology-short-take-60/">Technology Short Take #60</a></span>**

 Be social and share this post!<br>
[**](https://www.facebook.com/sharer/sharer.php?u=http://blog.scottlowe.org/2016/01/28/docker-macvlan-interfaces/ "Share on Facebook") [**](https://twitter.com/intent/tweet?url=http://blog.scottlowe.org/2016/01/28/docker-macvlan-interfaces/&text=Using%20Docker%20with%20macvlan%20Interfaces "Share on Twitter") [**](https://plus.google.com/share?url=http://blog.scottlowe.org/2016/01/28/docker-macvlan-interfaces/ "Share on Google Plus")
