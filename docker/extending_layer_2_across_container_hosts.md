# Extending layer 2 across container hosts

[Original URL](https://www.flockport.com/flockport-labs-extending-layer-2-across-container-hosts/)

> This post is to consolidate all our LXC networking guides and also explore some advanced container networking that have limited use but are interesting nonetheless hence the Flockport labs monicker....

This post is to consolidate all our [LXC networking guides](http://www.flockport.com/news "Flockport news and guides") and also explore some advanced container networking that have limited use but are interesting nonetheless hence the Flockport labs monicker. Experimental containers will now be posted under this label in our [container section.](http://www.flockport.com/containers/ "Flockport containers") We previously looked at [basic LXC container networking; bridging, NAT, static IPs, public IPs](http://www.flockport.com/lxc-networking-guide/ "Flockport LXC Networking Guide") etc and then at [connecting LXC containers across hosts](http://www.flockport.com/lxc-advanced-networking-guide/ "Flockport LXC Advanced Networking Guide") with[GRE tunnels](http://www.flockport.com/connecting-lxc-hosts-with-gre-tunnels/ "Connect LXC hosts with GRE tunnels") or secure[Tinc](http://www.flockport.com/building-distributed-mesh-networks-of-lxc-hosts/ "LXC VPN TINC")or [IPSEC VPNs.](http://www.flockport.com/connect-lxc-hosts-and-containers-with-ipsec-vpns/ "Flockport LXC IPSEC VPN Guide")

We also covered [basic failover and load balancing with Keepalived and Nginx](http://www.flockport.com/load-balancing-and-failover-with-lxc-containers/ "LXC load balancing and failover")and [with LVS](http://www.flockport.com/load-balancing-and-failover-of-lxc-containers-part-ii/ "LXC failover and loadbalancing with Keepalived and LVS"). These networking guides apply to both LXC and VM networking in Linux in general with KVM or Xen for instance.

This would be a good time to brush up. This guide explores a few advanced LXC networking possibilities that depend on a fair understanding of LXC and VM networking.

We will cover extending layer 2 across remote LXC hosts with L2tpv3 or Ethernet over GRE in Part I and using LXC's support for multiple network interfaces to explore using a container as a router and touch on using VMs of software routers like Vyatta, Vyos or Pfsense to route your container or VM networks in Part II. We also cover [using VXLAN separately](https://www.flockport.com/flockport-labs-lxc-and-vxlan/ "Flockport labs - LXC and VXLAN").

Jump directly to [Extending Layer 2 across LXC hosts](https://www.flockport.com/flockport-labs-extending-layer-2-across-container-hosts/#extendingl2) if you are up to date on LXC networking.

**LXC Networking Refresh**<br>
The default LXC installation creates what is a known as a NAT bridge, this is a standalone software bridge that is created on the host (a software bridge is like a switch and is a basic functionality provided by the Linux kernel)

Your containers or VMs connect to this bridge and get IPs in a private subnet. The routing is done by some iptables rules.

The default lxcbr0 is this kind of a bridge. Bridging, DHCP and basic routing is configured by the lxc-net script. The virbr0 bridge used by Virt Manager for KVM is similar.

Take a look at the /etc/init.d/lxc-net script (in/etc/init/lxc-net in Ubuntu) Here is what the script does in short:

```
1\. brctl addbr lxcbr0 ----- adds bridge
2\. ifconfig lxcbr0 10.0.3.1 netmask 255.255.255.0 up ----- gives the bridge an IP and brings it up
3\. Starts a dnsmasq instance with a specified interface lxcbr0 with DHCP subnet range 10.0.3.2-10.0.3.254
4\. iptables -t nat -A POSTROUTING -s 10.0.3..0/24 ! -d 10.0.3.0/24 -j ACCEPT ----- Adds an iptables masquerading rule for lxcbr0 so containers can access the net
```

In the default lxcbr0 network, containers are isolated in a private 10.0.3.0/24 subnet within the host and can only be accessed by each other and the host.

To access the containers from beyond the host you would need to use port forwarding ie forward port 80 of the host to port 80 of the container to for instance make a web server in the container available on the host and the network. You can of course forward 'n' number of host ports to various containers but you cannot forward the same port to multiple containers.

If you are in an internal network you can use basic routing to connect containers across hosts with the IP route utility. A typical command to connect to let's say the 10.0.4.0/24 container network on a host with IP 192.168.1.10 from 192.168.1.5 would look like this.

`ip route add 10.0.4.0/24 via 192.168.1.10`

To make this kind of routing work you need to ensure containers subnets are different across hosts.

You can set static IPs inside the container using /etc/network/interfaces file (depending on how your container OS configures networking) or via the dnsmasq instance configured for the lxcbr0 network on the host by associating specific containers to IPs in the /etc/lxc/dnsmasq file.

In a NAT type network there is no way to associate a public IP to the container. If your host has a public IP you can associate that IP with a container with 1-1 NAT mapping or if you have 2 public IPs use basic IP aliasing to associate the public IP to a container via NAT mapping.

**LXC Host Bridge**<br>
That was the NAT bridge. You can also create a different network for LXC containers in which containers are on the same network as your host. This is a direct bridge creating by bridging your physical interface usually eth0 to a bridge say br0 which containers and VMs then connect to.

If the host is 192.168.1.5 the containers will be in the same subnet 192.168.1.0/24\. This is a flat network and easier to work with with no NAT layer between containers and the network. Containers connecting to this interfaces get their IP and networking services directly from the router your host is connected to.

In case this is a public network you can easily associate public IPs to the containers, and they can be directly accessed from the internet. If you bridge the other hosts on this network to their respective eth0 and connect their containers to br0 interfaces, all you hosts and containers will be on the same network and thus be directly accessible by all containers and hosts.

If you have 2 network interfaces in the host you can bridge eth1 to br1 for instance, to put containers across hosts in their own network via the br1 interface. Static IPs can be configured inside the container or the router.

**Container networking in the cloud**<br>
For cloud vps instances you can use either of the above methods depending on the cloud provider. You can use a private NAT network and use port forwarding to access resources on the container, associate public IP via NAT mapping or if the cloud provider allows you to bridge or gives you private networks, you can become more creative in building your container network.

At this point its important to remember a lot of cloud, vps, server providers may not support bridging and most do not support things like multicast so services like Keepalived, LVS or an overlay protocol like VXLAN that uses multicast may not work in these networks, unless they support unicast.

**Connect containers across several hosts over layer 3**<br>
We already showed you how to connect containers across hosts on the same network with a simple routing rule. You can connect containers across several remote hosts with IPSEC VPNs, plain GRE tunnels or the awesome tool Tinc for mesh networks and VPNs. Containers and VMs that you connect across hosts need to be on different subnets.

We have detailed guides on these in our [News and Guides section](http://www.flockport.com/news/ "Flockport News and Guides").

You can think of these as overlay networks. But remember building VPNs across the public internet has a performance penalty; via the encryption of packets, latencies between your hosts and mtu issues. But these are tried and tested methods to build resilient networks and offer distributed services.

You can actually extend layer 2 across hosts beyond your network through a layer 3 tunnel. This basically means creating a 'distributed virtual switch' across hosts that may not be in the same private network, and are connected by public IPs over networks. Please remember layer 2 over large latencies can be problematic and like all types of tunnels mtu issues need to be addressed. Simplicity is always the best way, and extending layer 2 across hosts is relatively simple to do but may not be simple to run.

A distributed switch across multiple hosts is an extremely powerful capability to have for networking containers and VMs opening up a host of possibilities. The normal bridges you create in Linux via the inbuilt bridge or soft switches like OpenVSwitch (OVS) are limited to the host. Linux does not have a distributed switch like [VMware's VDS](http://www.vmware.com/in/products/vsphere/features/distributed-switch "VMware virtual distributed switch").

Imagine if you could have a bridge that spans both local and remote hosts, that your containers or VMs connect to on the same layer 2 network! You can do this with Ethernet over GRE or l2tpv3, and use ipsec to encrypt the tunnels.

One of the host bridges can act as the router and offer DHCP service. Application and services which depend on layer 2 like Keepalived, LVS etc benefit from this.

We are going to create a bridge on 2 remote hosts over their public IPs and connect the bridges with Ethernet over GRE or L2tpv3 so containers connecting to these bridges are on the same layer 2 network. We are using Debian Wheezy with kernel 3.14 from backports, and LXC 1.0.1 from the [Flockport repo](http://www.flockport.com/start "Flockport getting started guide") for this guide.

We will first show you how to do this with Ethernet over GRE and then L2tpv3\. The main difference is Ethernet over GRE is less well known while L2tpv3 is more widely used for l2 extension and uses UDP, and thus could be more flexible.

**Extending L2 with Ethernet over GRE**<br>
First create a bridge on both the remote hosts and bring it up. Let's call this bridge superbr0, you are course free to use any name you want.

```
brctl addbr superbr0
ifconfig superbr0 up
```

Then lets connect the superbr0 across the 2 hosts with public IP 1.1.1.1 and 2.2.2.2 with a tunnel.

First on Host A<br>
`ip link add testgre type gretap remote 2.2.2.2 local 1.1.1.1\. ttl 255`

Then on Host B<br>
`ip link add testgre type gretap remote 1.1.1.1 local 2.2.2.2 ttl 255`

For Ethernet over GRE we use the gretap device. Our interface/tunnel name is testgre, this can be a name of your choice. Let's connect it to our bridge. Do this on both Host A and Host B

`brctl addif superbr0 testgre`

Now our raw network is ready. Both superbr0 bridges are connected to each other over the testgre tunnel and any VM or container which connects to the superbr0 bridge on either host will be on the same layer 2 network. Neat! You can extend to more hosts by adding more gretap tunnels.

You can add static IPs to containers on both sides and and test it, but let's use dnsmasq and iptables to add basic DHCP and routing capabilities to the bridge. To do this let's use the superbr0 on host A as our router. First let's give it an IP

`ifconfig superbr0 10.0.2.1 netmask 255.255.255.0 up`

Now let's configure dnsmasq to serve this interface. In /etc/dnsmasq.conf add our newly minted bridge superbr0 to interface, add 10.0.2.1 to listen-address, uncomment bind-interfaces and give it a dhcp range of 10.0.2.2,10.0.2.254\. Restart Dnsmasq and it should be ready to offer IPs on the superbr0 interface.

```
interface=superbr0
listen-address=10.0.2.1
bind-interfaces
dhcp-range=10.0.2.2,10.0.2.254,12h
```

You can also use a standalone instance of dnsmasq launched from a command line like this:

```
dnsmasq --strict-order --bind-interfaces --pid-file=/var/run/dnsmasq-superbr0.pid --listen-address 10.0.2.1 --dhcp-range 10.0.2.2,10.0.2.254 --dhcp-lease-max=253 --dhcp-no-override --interface=superbr0
```

Connect any containers on Host A and B to the superbr0 interface and you should see them getting IPs in the 10.0.2.0/24 range given by dnsmasq we just configured on Host A.

To provide internet access to your containers and VMs add a simple masquerading rule on Host A.

`iptables -t nat -A POSTROUTING -s 10.0.2.0/24 ! -d 10.0.2.0/24 -j MASQUERADE`

If you would prefer to have an external router serve the superbr0 interface then add the physical interface that connects to your router to superbr0\. In this case you DO NOT need to give the bridge an IP and configure dnsmasq and iptables.

So now containers on both side are on the same layer 2 network. But what if want to extend this to more physical container hosts?

To do this you need a switch and a second network interface card on your host A or B. What we do is connect the second network interface eth1 to the switch and add it to the superbr0 interface.

`brctl addif superbr0 eth1`

So now any physical device connecting to the switch is connected to the superbr0 interface and will get its IPs from the router configured for this interface.

To put any containers and VM in these additional hosts on the same interface you will have to bridge eth0 on the additional hosts to br0 and connect the containers and VMs to br0 on them. You can similarly do this on the other side of the tunnel on Host B to extend to other hosts.

Also remember containers on both sides do not have to be limited to the extended layer 2 network on superbr0\. You can add multiple network interfaces to the containers and connect them to local networks giving you more possibilities on routing and outbound connectivity.

**Extending Layer 2 with L2TPV3**<br>
The principle is the same, only l2tpv3 uses UDP by default. You need to load the l2tp_eth module on both hosts before starting.

First on Host A

```
modprobe l2tp_eth
ip l2tp add tunnel tunnel_id 1000 peer_tunnel_id 2000 encap udp local 1.1.1.1 remote 2.2.2.2 udp_sport 6000 udp_port 5000
ip l2tp add session tunnel_id 1000 session_id 3000 peer_session_id 4000
```

On Host B we switch the values

```
modprobe l2tp_eth
ip l2tp add tunnel tunnel_id 2000 peer_tunnel_id 1000 encap udp local 2.2.2.2 remote 2.2.2.2 udp_sport 5000 udp_port 6000
ip l2tp add session tunnel_id 2000 session_id 4000 peer_session_id 3000
```

Check the tunnel on both Hosts

`ip l2tp show tunnel`

Now add the l2tpv3 tunnel interface to your bridge on both hosts. Like for our Ethernet over GRE tunnel we will use the superbr0 bridge.

`brctl addif superbr0 l2tpeth0`

To configure DHCP, routing and other options you can use the instructions for the Ethernet over GRE above. Apart from the the tunnel interface everything else remains the same. You extend this to more hosts by adding more l2tpv3 tunnels. Both protocols have a lot more options to explore.

You can also use the awesome vpn/mesh network app Tinc in switch mode to extend layer 2 across hosts. The difference is l2tpv3, Ethernet over GRE operates in kernel mode while Tinc operates in user mode.

Mikrotik for instance uses Ethernet over GRE for their [EOIP solution](http://wiki.mikrotik.com/wiki/Manual:Interface/EoIP "Mikrotik EOIP") or layer 2 extension and provides hardware acceleration for up to 500 MBit/s. Cisco, Juniper etc have their own L2 extension protocols like [OVT](http://www.cisco.com/c/en/us/solutions/data-center-virtualization/overlay-transport-virtualization-otv/index.html "Cisco OVT"), using [MPLS](http://www.cisco.com/c/en/us/products/collateral/data-center-virtualization/data-center-interconnect/white_paper_c11_493718.html "Cisco MPLS") etc.

New standards for networking in the cloud and virtualization around building SDNs (software defined networking) are fast maturing to enable some of the capabilities we cover in this guide on a large scale.

[VXLAN which extends layer 2 across multiple hosts using VTEPs](http://blogs.vmware.com/vsphere/2013/05/vxlan-series-how-vtep-learns-and-creates-forwarding-table-part-5.html "VXLAN Overlays") is fairly interesting but depends on multicast which most organizations, service and cloud providers don't support. Cisco, Juniper and others are working on their own ['enhanced vxlan' extensions with unlticast support](http://www.cisco.com/c/en/us/products/collateral/switches/nexus-7000-series-switches/guide_c07-728863.html "Cisco VXLAN unicast").

Cumulus Linux along with Metacloud - recently acquired by Cisco, have been working on VXLAN unicast and have released a [VXFLD project](https://github.com/CumulusNetworks/vxfld "VXLAN VXFLD") with some tools.

The Linux kernel supports VXLAN in both multicast and unicast modes. You need a relatively recent version of the kernel 3.14+ and the iproute2 package 3.14+. We have a [follow up post on VXLAN here](http://www.flockport.com/flockport-labs-lxc-and-vxlan/ "Flockport labs - LXC and VXLAN").

But these are still maturing and not yet ready to go. If you are interested in networking [networkheresy](http://networkheresy.com "Networkheresy"), [Scott Lowe](http://blog.scottlowe.org/ "Scott Lowe") and [ipspace](http://www.ipspace.net/Main_Page "Ipspace")are 3 great blogs that cover next generation networking in depth.

[In Part II we are going to cover using LXC containers as routers](http://www.flockport.com/use-an-lxc-container-as-a-router/ "Use an LXC container as a router") which is similar to how one would use VMs of software routers like Vyatta, Vyos or Pfsense. LXC supports multiple network interfaces and is a great way to experiment with networking and networking namespaces. With 2 interfaces one could be connected to your wan and the other your lan letting an LXC container operate as a router. There are very limited use cases for this, but its a great way to simulate networks and manage routing for other containers in the cloud.

**Further reading and resources**

[Flockport App store](https://www.flockport.com/store/ "Flockport App Store")

[Flockport LXC Installer and VM](https://www.flockport.com/start/ "Start")

[Flockport LXC Guides](https://www.flockport.com/guides/ "Flockport LXC Guides")

**Flockport Networking Series**

[Flockport labs - using an LXC container as a router](http://www.flockport.com/use-an-lxc-container-as-a-router/ "Flockport labs - Use an LXC container as a router")

[LXC Networking Guide](http://www.flockport.com/lxc-networking-guide/ "Flockport LXC Networking Guide")

[LXC Advanced Networking Guide](http://www.flockport.com/lxc-advanced-networking-guide "Flockport LXC Advanced Networking Guide")

[Connect LXC hosts with GRE tunnels](http://www.flockport.com/connecting-lxc-hosts-with-gre-tunnels/ "Connect LXC hosts with GRE tunnels")

[Connect LXC hosts and containers with IPSEC VPNs](http://www.flockport.com/connect-lxc-hosts-with-an-ipsec-vpn/ "Connect LXC hosts with an IPSEC VPN")

[Connect LXC hosts and containers with Tinc VPNs](http://www.flockport.com/building-distributed-mesh-networks-of-lxc-hosts/ "Build Distributed Mesh Networks")

[Load balancing and failover with LXC containers](http://www.flockport.com/load-balancing-and-failover-with-lxc-containers/ "Load balancing and failover with LXC containers")

[Build distributed storage nodes with LXC and Gluster](http://www.flockport.com/build-distributed-storage-nodes-with-lxc-and-gluster/ "Build distributed storage nodes with LXC and Gluster")

<span class="content-box-heading">** </span>

## <span>Stay updated on Flockport news</span>
