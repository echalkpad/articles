# Connecting Docker Containers, Part One

[Original URL](https://deis.com/blog/2016/connecting-docker-containers-1)

> Docker containers are self-contained, isolated environments. However, they're often only useful if they can talk to each other. There are many ways to connect containers. And we won't...

Docker containers are self-contained, isolated environments. However, they're often only useful if they can talk to each other.

There are many ways to connect containers. And we won't attempt to cover them all. But in this miniseries, we will look at some common ways.

This topic seems elementary, but grasping these techniques and the underlying design concepts is important for working with Docker.

Understanding this topic will:

- Help developers and ops people explore the broad spectrum of container deployment choices
- Let developers and ops people to embark more confidently with a microservice design architecture
- Empower developers and ops people to better orchestrate more complex distributed applications

Fortunately, the large number of connection options for containers enables a broad range of approaches, giving us the flexibility to choose an architecture that suits the needs of any application.

[]()

## Setup

Before we can demonstrate how containers can be connected, we need to create a pair of them for use in our examples.

The first image will be derived from a simple Ubuntu installation. It will act as a client container.

First, we create the container and attach to it:

```
$ sudo docker run -itd --name=client_setup ubuntu /bin/bash
$ sudo docker attach client_setup
```

Then, once we have a shell inside the container, we run:

```
$ apt-get install curl
```

If you do not see the shell, click the up arrow.

Now, detach from the client container using CTRL+p then CTRL+q.

Then, stop it, and commit:

```
$ sudo docker stop client_setup
$ sudo docker commit client_setup client_img
```

We now have an image called `client_img` to use.

The second container we want to use is, again, derived from an Ubuntu installation. But this time, we'll modify it to run a Apache HTTP server.

First, we create it and attach, like before:

```
$ sudo docker run -itd --name=server_setup ubuntu /bin/bash
$ sudo docker attach server_setup
```

Then, once we have a shell inside the container, we install the Apache HTTP server:

```
$ apt-get install apache2
```

When the installation is complete, detach from the container using CTRL+p and CTRL+q.

Now, stop the container, and commit it:

```
$ sudo docker stop server_setup
$ sudo docker commit server_setup server_img
```

We now have two images: `client_img` and `server_img`.

Now we have this set up, we can explore the various connection possibilities.

## The Docker Bridge

By default, a Docker container is isolated from other containers and the external network. Docker provides a bridge, the `docker0` interface, which is created with the installation of the [Docker Engine](https://www.docker.com/products/docker-engine).

It is through the Docker bridge that communication is possible amongst containers and between containers and the host machine.

You can see the Docker bridge by running this command on a Docker host:

```
$ ifconfig docker0
```

You should see something like this in the output:

```
docker0 Link encap:Ethernet HWaddr 02:42:a2:dc:0f:a8 
 inet addr:172.17.0.1 Bcast:0.0.0.0 Mask:255.255.0.0
 inet6 addr: fe80::42:a2ff:fedc:fa8/64 Scope:Link
 UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1
 RX packets:1477 errors:0 dropped:0 overruns:0 frame:0
 TX packets:2436 errors:0 dropped:0 overruns:0 carrier:0
 collisions:0 txqueuelen:0 
 RX bytes:83901 (83.9 KB) TX bytes:3606039 (3.6 MB) 
```

The bridge interface works locally, on a single Docker host, and is the connection mechanism behind all three approaches we cover in this post. In the next post, we'll move on to the _overlay_ interface, which allows us to network containers across multiple Docker hosts.

## Exposing Ports

First, let's see how we can run a server container so that it exposes port 80 (HTTP) to other containers.

To do so, I run the container with the `expose` command, which tells Docker to expose port number specified when it runs the container. An exposed port is one that other containers can reach.

Let's run `server_img` as a container called `server1`, exposing port 80:

```
$ sudo docker run -itd --expose=80 --name=server1 server_img /bin/bash
```

We'll name our containers sequentially (`server1`, `server2`, and so on) as we go.

Then, attach to the container:

```
$ sudo docker attach server1
```

Again, If you do not see the shell, click the up arrow.

Start the Apache HTTP server:

```
$ /etc/init.d/apache2 start
```

And get the IP address:

```
$ ifconfig
eth0 Link encap:Ethernet HWaddr 02:42:ac:11:00:03 
 inet addr:172.17.0.3 Bcast:0.0.0.0 Mask:255.255.0.0
```

Okay, so we have an IP address of `172.17.0.3`. Let's test we can see that from a client container.

Open a second terminal.

Start the `client1` container:

```
$ sudo docker run -itd --name=client1 client_img /bin/bash
```

Attach to it:

```
$ sudo docker attach client1
```

If you do not see the shell, click the up arrow.

Make a test connection to the `server1` container's IP address:

```
$ curl 172.17.0.3 
```

If everything works, you should see the HTML of the Apache HTTP server's default page. This indicates the the `client1` container is able to make connections to the `server1` container's HTTP port.

## Port Binding

What if we want to expose our HTTP server to the host network, including application on the host machine, and other machines on the host network? In this scenario, we need to _bind_ a host port to a container port.

To expose the Apache HTTP server to the host network, we need to bind port 80 on the server container to port 8080 on the host machine.

We can do this like so:

```
$ sudo docker run -itd -p 8080:80 --name=server2 server_img /bin/bash
```

The `-p 8080:80` option is the thing to pay attention to here.

Now, attach to the container:

```
$ sudo docker attach server2 
```

If you do not see the shell, click the up arrow. And start the Apache HTTP server:

```
$ /etc/init.d/apache2 start
```

Now, from the host system, visit <http://localhost:8080/> and you should see the Apache HTTP server default page.

![](https://deis.com/images/blog-images/docker-networking-0-0.png)

Any machine on your host network that can access port 8080 of your host machine will also be able to access this.

## Linking Containers

Another approach to connecting containers involves something Docker calls _linking_.

When you link one container to another, Docker will make some information about the linked container available via environment variables.

Let's take a look.

First, start the server container:

```
$ sudo docker run -itd --name=server3 server_img /bin/bash
```

Now, start the client container and link it to the server container, like so:

```
$ sudo docker run -itd --link server3 --name=client3 client_img /bin/bash
```

Notice the `--link server3` option being used here.

Now attach to the client container:

```
$ sudo docker attach client3
```

And check out the available environment variables:

```
$ env | grep SERVER3
SERVER3_PORT_80_TCP_PROTO=tcp
SERVER3_PORT=tcp://172.17.0.2:80
SERVER3_PORT_80_TCP_PORT=80
SERVER3_NAME=/client3/server3
SERVER3_PORT_80_TCP=tcp://172.17.0.2:80
SERVER3_PORT_80_TCP_ADDR=172.17.0.
```

Docker also updates the `/etc/hosts` file in the client container to add `server3` as a local hostname pointing to the server container.

To demonstrate this, run:

```
$ curl server3
```

You should see the same default HTML again.

## Wrap-Up

In part one of this miniseries, we introduced the Docker bridge interface, which lets us connect containers on the same host.

We looked at three connection methods:

- Connection through port exposure
- Binding of the host port to the container port
- Linking two containers through the link option

In part two, we'll look at isolating containers inside user-defined networks. We'all also introduce the overlay interface, and take a look at how to use it for networking Docker containers across multiple Docker hosts. Even across datacenters and cloud providers!
