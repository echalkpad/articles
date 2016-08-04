# Going Over the Edge with your VMware NSX and Cisco Nexus

[Original URL](http://bradhedlund.com/2015/02/06/going-over-the-edge-with-your-vmware-nsx-and-cisco-nexus/)

> Hey! Cisco Nexus peeps! What could possibly be more fun than connecting your awesome new NSX gear to your Cisco Nexus gear? For the life of me I really don't know. All right then. Lets...

Hey! Cisco Nexus peeps! What could possibly be more fun than connecting your awesome new NSX gear to your Cisco Nexus gear? For the life of me I really don't know. All right then. Lets do it!

Lets kick things off with this email question I received from a reader.

> "Hi Brad,

> In our environment we have two prevailing server standards, rackmounts and UCS. I read your excellent NSX on UCS and 7K design guide and the section on not running routing protocols over the VPC links makes sense. My related question concerns how we can achieve a routing adjacency from the NSX Distributed Router to the N7K with a rack mount with 2x10gbe interfaces connecting to 2x7Ks via VPC? (we don't use the NSX Edge Router)."

This reader has politely pointed out that my [VMware NSX on Cisco UCS and Nexus 7000 design guide](http://bradhedlund.com/2014/02/24/new-design-guide-vmware-nsx-with-cisco-ucs-and-nexus-7000/) could have provided a bit more detail on NSX Edge design. I totally agree. There's no time like the present, so let's dive into that _now_ and stir up some content that might end up in the next version of the guide.

All right. We won't worry too much about the form factor of the servers right now. Whether it's a blade or a rack mount doesn't matter; lets just generalize that we have _servers._ And to make things extra difficult, these servers will only have **2 x 10GE interfaces --** no more, no less. Those interfaces [ultimately] connect to a vPC enabled VLAN, or they will connect to a non-VPC normal VLAN. Working with this baseline of 2 x 10GE also helps to keep everything easily applicable to either blades or rack mounts.

I'm going to present the **logical topology** of three different designs. How these translate into a physical topology is something I'll leave for the time being to your own expertise and imagination.

Any design discussion can have a number of variables and permutations, and especially here, and especially in the bottom half section depicting On Demand virtual networks. "What about inserting service X for this or that tenant?" etc. If I attempted to discuss all such nuances in completeness this post would get way off topic. Lets keep it simple for now and focus on the edge topology. At a later time we'll come back to the various flavors of On Demand virtual networks you can lay down underneath the Pre-Created edge topology of your choice.

## Lost Your Edge

We'll start with the scenario posed in the opening question; "we don't use the NSX Edge Router". Thus, the only NSX router is the distributed logical router (running in kernel on your ESX compute hosts) which is directly adjacent to your virtual machines (naturally); and it's also directly adjacent to your Nexus 7000s on a vPC enabled **VLAN**. The later constitutes the "Uplink" of the distributed router and allows for the possibility of running a dynamic routing protocol with an upstream router.

The motivation for the Lost Your Edge design might be simplicity, where-in you don't want -- or feel that you don't need -- an additional layer of NSX Edge virtual machines to worry about and manage.

[![](http://storage.googleapis.com/bradhedlund/blog/over-the-edge/lost-your-edge.png)](http://storage.googleapis.com/bradhedlund/blog/over-the-edge/lost-your-edge.png)

Notice that we've laid this topology down on all vPC-enabled VLANs. Remember the NSX distributed router is running in-kernel on your ESX compute hosts, and I presume that you _do_ want your ESX compute hosts attached via vPC. As a result our NSX distributed router is also vPC attached. By consequence, this prevents us from running a dynamic routing protocol between the NSX distributed router and the Nexus 7000s. The reason for this I have [explained here](http://bradhedlund.com/2010/12/16/routing-over-nexus-7000-vpc-peer-link-yes-and-no/).

We can most definitely do the Lost Your Edge design with **static routing**. Your NSX distributed router would have a simple default route pointing to the Nexus 7000s **HSRP** address on the "Edge VLAN". Meanwhile, the Nexus 7000s will have a static aggregate route (eg. 10.1.0.0/16) pointing to the NSX distributed router **forwarding address**. Later on, the individual subnets you create (On Demand) behind the NSX distributed router will of course fall into that aggregate route. The only thing left to do now is redistribute this static route into your enterprise backbone with BGP or OSPF.

One thing to be aware of in the Lost Your Edge design is the need for a **Designated Instance** (DI) on the NSX distributed router for the Uplink logical interface on the "Edge VLAN" facing the Nexus 7000s.

[![](http://storage.googleapis.com/bradhedlund/blog/over-the-edge/lost-your-edge-traffic.png)](http://storage.googleapis.com/bradhedlund/blog/over-the-edge/lost-your-edge-traffic.png)

When the NSX distributed router has an interface on a VLAN, one of the ESX hosts will be _designated_ as responsible for ARP handling and forwarding for the distributed router's forwarding MAC address on that VLAN. By consequence, that **one host will receive all traffic** coming from other devices on that VLAN (like the Nexus 7Ks). Once received, the designated host will locally route traffic to the proper VXLAN (or VLAN) containing the destination, and send it as a logical Layer 2 flow to the host where the VM resides (which might be on another host, or the same host).

The DI host is elected by the NSX Controller cluster. This is not something that you can easily influence or predict, any host could be elected DI. And when it fails, a new one needs to be re-elected. The failure detection and recovery of a new DI can take as long as **45-60 seconds**. This is something you might want to test for yourself in a lab.

The other important thing to point out about Lost Your Edge is that you're missing an opportunity to apply services like NAT, VPN, or perimeter Firewall inspection as traffic enters or exits the NSX domain.

In designs to follow you'll see how we can obtain faster failure recovery, services, better traffic distribution, and even dynamic routing.

## On the Edge

Let's assume for the moment that you're fine with static routing (or maybe you're stuck with all vPC VLANs in your physical design). Maybe it's the failure recovery and ingress choke of the Designated Instance that you're not cool with (heck, I don't blame you). No problem. In this On the Edge design we'll introduce the NSX Edge routing VMs and see what happens.

[![](http://storage.googleapis.com/bradhedlund/blog/over-the-edge/on-the-edge.png)](http://storage.googleapis.com/bradhedlund/blog/over-the-edge/on-the-edge.png)

Nothing has changed with the Nexus 7000s and the physical VLAN setup. We still have all vPC enabled VLANs, and we still have the previously discussed static aggregate route. The difference lies in the NSX topology. Our first hop into NSX is now an NSX Edge Router VM which we've protected by a **state-synced** shadow VM. Second, we've introduced a VXLAN Transit Logical Switch that will sit between our NSX Edge and NSX distributed router.

All of our hosts are still attached via vPC with 2 x 10GE NICs. Some of these hosts should be designated as Edge hosts and placed in an Edge Cluster for the purpose of running your NSX Edge VMs. This (must read) [VMware NSX Design Guide 2.1](https://communities.vmware.com/docs/DOC-27683) covers that approach quite thoroughly as a design best practice. That said, in a lab you can certainly mingle your NSX Edge with compute hosts just for the fun of it.

[![](http://storage.googleapis.com/bradhedlund/blog/over-the-edge/on-the-edge-traffic.png)](http://storage.googleapis.com/bradhedlund/blog/over-the-edge/on-the-edge-traffic.png)

For our distributed router, the concept of a Designated Instance does not apply on any VXLAN segment (such as our Transit Logical Switch) where traffic is flowing from an NSX Edge VM to the distributed router, and vice versa. When traffic arrives at the NSX Edge VM from the Nexus 7000, the Edge host machine also happens to be running the NSX distributed router in its kernel. Therefore, the next hop (.2) is **always local** to every Edge machine, along with the Logical Switches attached to that distributed router. In a nutshell, the Edge host machine is able route traffic from the Nexus 7000 directly to the ESX compute host where the destination VM resides. How cool is that?

You can see the On the Edge design -- when compared to Lost Your Edge -- has the same (if not better) traffic flow properties, faster failover (6 seconds), and the opportunity to add services like NAT, VPN, and perimeter Firewall. Not bad for a days work.

## On the Upgraded Edge

Now let's assume that you do have some flexibility in your physical design to vPC attach some hosts, and not others. With that luxury we'll take the Edge hosts running the NSX Edge VMs and have those non-vPC attached. Meanwhile we'll leave the compute hosts with their optimal vPC attachment. By doing this, we'll be able to _upgrade_ the On the Edge design with **dynamic routing**. Just as a reminder, this an exercise specific to Cisco Nexus 7000\. Other platforms may be able handle dynamic routing on vPC or MLAG connections just fine.

[![](http://storage.googleapis.com/bradhedlund/blog/over-the-edge/on-the-upgraded-edge.png)](http://storage.googleapis.com/bradhedlund/blog/over-the-edge/on-the-upgraded-edge.png)

From the diagram above you will notice that we've made the "Edge VLAN" a non-vPC VLAN and our Edge hosts will attach to it. You might also observe that we've added a second VTEP VLAN that is non-vPC, and we will attach our Edge host VXLAN vmkernel interfaces to it. Our Edge hosts are completely non-VPC attached while our compute hosts remain attached to all vPC enabled VLANs.

With our NSX Edge hosts free from vPC attachment, we are able run dynamic routing protocols with the Nexus 7000 without issue, such as BGP. Every new subnet created on the NSX distributed router will be advertised to the NSX Edge, and in-turn will be advertised by the NSX Edge to the upstream Nexus 7000s (or whatever) with BGP. Pretty cool, right?

[![](http://storage.googleapis.com/bradhedlund/blog/over-the-edge/on-the-upgraded-edge-traffic.png)](http://storage.googleapis.com/bradhedlund/blog/over-the-edge/on-the-upgraded-edge-traffic.png)

The traffic flow here is very similar to the previous design, only now our VXLAN traffic between Edge and Compute hosts will take a Layer 3 hop through the Nexus 7000 (before it was Layer 2). No biggie. Depending on your physical design and host placement, this might mean an extra hop through the Nexus 7000, or not. Such as with N7K-N2K (no difference) vs. N7K-N5K-N2K (maybe) or N7K-UCS (maybe). Keep in mind, Edge to Compute host traffic is North/South in nature and generally bottle-necked by some other smaller link further upstream. Fair enough?

## Over the Edge

Up to this point we've been placing one NSX Edge VM on that "Edge VLAN" to send/receive all traffic to/from our NSX distributed router. Well and good. A single NSX Edge VM can easily route 10Gbps of traffic. But you want more? No problem. We'll we just 8-way ECMP that mofo and call it a day. Check it out.

[![](http://storage.googleapis.com/bradhedlund/blog/over-the-edge/over-the-edge.png)](http://storage.googleapis.com/bradhedlund/blog/over-the-edge/over-the-edge.png)

What we've done here is deploy up to eight NSX Edge VMs on that "Edge VLAN", placed them on separate hosts, and enabled ECMP. We also went to our NSX distributed router and enabled ECMP there as well. Our Nexus 7000s see dynamic routing updates coming from 8 equal cost next hops and perform per-flow hashing, placing each unique flow on one of our eight NSX Edge VMs (each capable of 10Gbps). The reverse applies as well. IF you had up to eight Nexus 7000s on the Edge VLAN (seriously?) each NSX Edge VM would install eight equal cost next hops for each route upstream.

The same magic applies to our NSX distributed router. Each compute host sending traffic northbound will perform eight way per-flow hashing (in-kernel), picking a NSX Edge for each unique flow. If for whatever reason a NSX Edge drops off the network, only 13% of the traffic will be affected (in theory), and only for the period of time it takes routing protocol timeouts to detect and remove the failed next hop (3 seconds or so).

[![](http://storage.googleapis.com/bradhedlund/blog/over-the-edge/over-the-edge-traffic.png)](http://storage.googleapis.com/bradhedlund/blog/over-the-edge/over-the-edge-traffic.png)

When you're letting it rip with ECMP there's no guarantee that both directions of a flow will traverse the same NSX Edge. Because of that we need to turn off stateful services like NAT, VPN, and perimeter Firewall. That's the only bummer. Not much we can do about that right now with ECMP. But if you need lots of bandwidth (more than one Edge) with stateful services, you can always horizontally scale Edge and distributed router in pairs. For example, Edge1+DR1, Edge2+DR2, and so on.

## Design Poster

[**CLICK HERE**](http://storage.googleapis.com/bradhedlund/blog/over-the-edge/NSX_Over_the_Edge_poster.pdf) to download your own copy of this 48 x 36 design poster containing all of the cool diagrams from this post. Print that bad boy out and hang it up next to your [NSX + UCS + Nexus 7000 poster](http://storage.googleapis.com/bradhedlund/vmw-blog/NSX-UCS-vSphere-DG1/NSX_vSphere_UCS_Nexus_7000_poster.pdf).

[![](http://storage.googleapis.com/bradhedlund/blog/over-the-edge/over-the-edge-poster.PNG)](http://storage.googleapis.com/bradhedlund/blog/over-the-edge/NSX_Over_the_Edge_poster.pdf)

## Slides

[**CLICK HERE**](http://storage.googleapis.com/bradhedlund/blog/over-the-edge/Over_the_Edge_with_NSX_and_Cisco_Nexus.pdf) to download your own copy of these diagrams in PDF slides.
