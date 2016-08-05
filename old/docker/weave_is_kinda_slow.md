# Weave is kinda slow

[Original URL](http://www.generictestdomain.net/docker/weave/networking/stupidity/2015/04/05/weave-is-kinda-slow/)

> In our new world of containers Docker, many old problems have been rediscovered. Thankfully, the fact that these problems were solved decades ago has not stopped people from coming up with their own...

In our new world of ~~containers~~ Docker, many old problems have been rediscovered. Thankfully, the fact that these problems were solved decades ago has not stopped people from coming up with their own solutions, and we now all get to witness the resulting disasters.

The particular problem I'll talk about today is IP level overlay networks. The basic problem these overlay networks try to solve is "I have 1 IP per machine, but I need ~~a subnet~~ multiple IPs per machine". This was originally relevant because you might want have a few networks and want to do some funky networking, then became relevant because you might have a few VMs and want to do some funky networking, and now, in 2015, it is relevant because you might have a few containers and want to do some funky networking. Obviously, these use cases are distinct enough to require their own implementations and protocols, as we will see.

The way you solve this problem is usually via some sort of IP encapsulation, though the specific implementation will vary wildly. The [IP encapsulation RFC](https://www.ietf.org/rfc/rfc2003.txt) talks about a structure that would look like

```
| Encapsulated |
| Packet |
+--------------+
| Inner IP |
| Header |
+--------------+
| Outer IP |
| Header |
+--------------+
| Link |
| Layer |
```

However, due to there being only 2 transport layer protocols that can traverse a firewall, we more often see

```
| Encapsulated |
| Packet |
+--------------+
| Inner IP |
| Header |
+--------------+
| UDP |
| Header |
+--------------+
| Outer IP |
| Header |
+--------------+
| Link |
| Layer |
```

In some cases, we get the following, due to the need for software to justify its existence via "features":

```
| Encapsulated |
| Packet |
+--------------+
| Inner IP |
| Header |
+--------------+
| Overlay |
| Header |
+--------------+
| UDP |
| Header |
+--------------+
| Outer IP |
| Header |
+--------------+
| Link |
| Layer |
```

But usually that is just the sign of a [poor implementation](http://www.dwheeler.com/secure-class/Secure-Programs-HOWTO/data-vs-control.html).

There are a couple of other standards for this, [GRE](http://tools.ietf.org/html/rfc2784) and [VXLan](http://tools.ietf.org/html/rfc7348). GRE (generic routing encapsulation) is a network layer protocol that is most commonly used to do things such as IPv4 over IPv6, extending LANs over VPNs etc etc. VXLan (Virtual Extensible LAN) is a more recent protocol that was designed specifically to enable funky networking when working in VM heavy environments. The encapsulation provided by VXLan looks quite different however, as VXLan encapsulates link layer frames (though it itself is an application layer protocol; the frames are transmitted via UDP). This looks a bit like this:

```
| Encapsulated |
| frame |
+--------------+
| VXLAN |
| Header |
+--------------+
| UDP |
| Header |
+--------------+
| Outer IP |
| Header |
+--------------+
| Link |
| Layer |
```

Cisco have a [nice diagram](http://www.cisco.com/c/dam/en/us/products/collateral/switches/nexus-9000-series-switches/white-paper-c11-729383.doc/_jcr_content/renditions/white-paper-c11-729383_1.jpg) that goes into some more detail.

## Weave

[Weave](http://weave.works/) is a company/open source project that provides an overlay network for your Docker containers. Due to their unique use case of providing each container with an IP, they have developed their own custom protocol, which looks something like this (courtesy of the [weave documentation](http://weaveworks.github.io/weave/how-it-works.html)):

```
+-----------------------------------+
| Name of sending peer |
+-----------------------------------+
| Frame 1: Name of capturing peer |
+-----------------------------------+
| Frame 1: Name of destination peer |
+-----------------------------------+
| Frame 1: Captured payload length |
+-----------------------------------+
| Frame 1: Captured payload |
+-----------------------------------+
| Frame 2: Name of capturing peer |
+-----------------------------------+
| Frame 2: Name of destination peer |
+-----------------------------------+
| Frame 2: Captured payload length |
+-----------------------------------+
| Frame 2: Captured payload |
+-----------------------------------+
| ... |
+-----------------------------------+
| Frame N: Name of capturing peer |
+-----------------------------------+
| Frame N: Name of destination peer |
+-----------------------------------+
| Frame N: Captured payload length |
+-----------------------------------+
| Frame N: Captured payload |
+-----------------------------------+
| UDP Header |
+-----------------------------------+
| IP Header |
```

This is quite different from the examples I talked about above. Weave captures data on the frame level, a la VXLan, but then collates multiple frames and transmits them together via UDP. This means that 2 packets sent by the container are not guaranteed to cross the network as 2 packets; if they are sent sufficiently close together, and the sum of their size is sufficiently smaller than the MTU, they may travel as a single packet. We'll see how this affects the connection speed.

I have two boxes, `$IP1` and `$IP2`. They're both $5 digital ocean boxes, so should be representative of the standard machines used in enterprise settings today. I'll start off the test by running `qperf`, a network testing tool, on the first machine, and then running `qperf $IP1 tcp_bw tcp_lat` on the other. This will run a test on TCP bandwidth and latency between the two IPs:

```
$ qperf $IP1 tcp_bw tcp_lat
tcp_bw:
 bw = 116 MB/sec
tcp_lat:
 latency = 91.8 us
```

So I guess you get roughly what you pay for. Anyway, the defining feature of the cloud is ~~clueless CTOs~~ poor networks, so this shouldn't be a problem. Let's try running the test under two Weave connected containers.

## Weave

So running things under Weave is a little more complicated. I've annotated the commands below (this requires a Weave network to have been set up that includes the two machines).

For the server:

```
$ weave run \ # Weave requires you not use the standard docker command
 10.2.1.1/24 \ # The ip we're going to use
 -p 4001:4001 \ # 4001 is used for testing
 -p 4000:4000 \ # 4000 for coordination
 -v $(which qperf):$(which qperf) \ # Avoiding building qperf in the container
 tianon/gentoo-stage3 \ # At least it's not Ubuntu
 qperf -lp 4000 # Tell qperf to use port 4000 for coordination
```

And for the client:

```
$ C=$(weave run \ # `weave run` runs commands in daemon mode by default
 10.2.1.2/24 \ # as above
 -v $(which qperf):$(which qperf) \ # as above
 tianon/gentoo-stage3 \
 qperf 10.2.1.1 \ # The IP of the server container
 -lp 4000 \ # Use 4000 for coordination
 -ip 4001 \ # Use 4001 for testing
 tcp_bw tcp_lat # Test tcp bandwidth
$ # Watch for the result in the logs
$ docker logs $C
tcp_bw:
 bw = 6.91 MB/sec
tcp_lat:
 latency = 372 us
```

Boy, for all that work, that's pretty damn slow. Let's tabulate that data:

Name   | TCP BW (MB/s) | TCP Lat (µs) | BW % | Lat %
------ | ------------- | ------------ | ---- | -----
Native | 116           | 91.8         | 100  | 100
Weave  | 6.91          | 372          | 5.96 | 405

So two Weave networked container provide about a 6% of the throughput two native services might, at 4x the latency. Not great. I would guess that a lot of the time is spent simply getting the packet out of the kernel and into the Weave process.

## Flannel

Weave's main competitor in the giving-each-container-an-ip space is [flannel](https://github.com/coreos/flannel), by CoreOS. Flannel offers a range of encapsulation protocols, all working at the IP level. By default it uses the UDP based encapsulation I described above, but also supports [VXLan encapsulation](http://tools.ietf.org/html/rfc7348), a recent encapsulation standard that has in-kernel support. I don't know about you, but I view every packet that avoids userspace as another step towards salvation.

Flannel uses [etcd](https://github.com/coreos/etcd) as its control plane, so I dumped it on `$IP1`, and then loaded up the first configuration I wanted to test, the default UDP encapsulation:

```
$ etcdctl mk /coreos.com/network/config '{"Network":"10.0.0.0/16"}'
```

We then fire up flannel on each node, and tell Docker to use the flannel bridge

```
$ flanneld -etcd-endpoints="http://$IP1:4001" &
$ source /run/flannel/subnet.env
$ docker -d --bip=${FLANNEL_SUBNET} --mtu=${FLANNEL_MTU}
```

With that done on each machine, I'll now fire up a container on `$IP1`, figure out what IP flannel has given me, and then run qperf

```
$ docker run -ti --rm -v $(which qperf):$(which qperf) \
 -p 4000:4000 -p 4001:4001\
 tianon/gentoo-stage3 bash
container$ hostname -I
10.0.72.2
container$ qperf -lp 4000
```

And on the other host, simply start a container and run the qperf client against the virtual IP:

```
$ docker run --rm \
 -v $(which qperf):$(which qperf) \
 tianon/gentoo-stage3 \
 qperf -lp 4000 -ip 4001 10.0.72.2 tcp_bw tcp_lat
tcp_bw:
 bw = 23 MB/sec
tcp_lat:
 latency = 164 u
```

So, not exactly great, though a fair bit better than Weave. This is likely due to the fact that Weave captures data via packet capture, while flanneld uses ipmasq, a lesser known library that allows userspace to make decisions on the destiny of packets coming out of iptables chains. However, as mentioned before, in kernel routing is what we would like, and neither of these solutions provide it. Let's turn on flannel's VXLan backend:

```
$ etcdctl rm --recursive /coreos.com/network/subnets
$ etcdctl set /coreos.com/network/config '{"Network":"10.0.0.0/16", "Backend": {"Type": "vxlan"}}'
```

And after performing the same process to set up the benchmark, we get

```
tcp_bw:
 bw = 112 MB/sec
tcp_lat:
 latency = 129 us
```

So, yeah, avoiding userspace is good.

Name          | TCP BW (MB/s) | TCP Lat (µs) | BW %   | Lat %
------------- | ------------- | ------------ | ------ | ------
Native        | 116           | 91.8         | 100.00 | 100.00
Weave         | 6.91          | 372          | 5.96   | 405.23
Flannel UDP   | 23            | 164          | 19.83  | 178.65
Flannel VXLan | 112           | 129          | 96.55  | 140.52

I think that speaks for itself. The only other thing I should mention at this point is that if you are relying on Weave's encryption feature, I would recommend investing in an actual VPN implementation. Weave rolls its own crypto, and I would not suggest people rely on Weave's custom protocol for confidentiality on their network links.
