# Network Switching Tutorial

[Original URL](http://www.lantronix.com/resources/networking-tutorials/network-switching-tutorial/)

> Network Switching Switches can be a valuable asset to networking. Overall, they can increase the capacity and speed of your network. However, switching should not be seen as a cure-all for network...

Network Switching

Switches can be a valuable asset to networking. Overall, they can increase the capacity and speed of your network. However, switching should not be seen as a cure-all for network issues. Before incorporating network switching, you must first ask yourself two important questions: First, how can you tell if your network will benefit from switching? Second, how do you add switches to your network design to provide the most benefit?

This tutorial is written to answer these questions. Along the way, we'll describe how switches work, and how they can both harm and benefit your networking strategy. We'll also discuss different network types, so you can profile your network and gauge the potential benefit of network switching for your environment.

## What is a Switch?

Switches occupy the same place in the network as hubs. Unlike hubs, switches examine each packet and process it accordingly rather than simply repeating the signal to all ports. Switches map the Ethernet addresses of the nodes residing on each network segment and then allow only the necessary traffic to pass through the switch. When a packet is received by the switch, the switch examines the destination and source hardware addresses and compares them to a table of network segments and addresses. If the segments are the same, the packet is dropped or "filtered"; if the segments are different, then the packet is "forwarded" to the proper segment. Additionally, switches prevent bad or misaligned packets from spreading by not forwarding them.

Filtering packets and regenerating forwarded packets enables switching technology to split a network into separate collision domains. The regeneration of packets allows for greater distances and more nodes to be used in the total network design, and dramatically lowers the overall collision rates. In switched networks, each segment is an independent collision domain. This also allows for parallelism, meaning up to one-half of the computers connected to a switch can send data at the same time. In shared networks all nodes reside in a single shared collision domain.

Easy to install, most switches are self learning. They determine the Ethernet addresses in use on each segment, building a table as packets are passed through the switch. This "plug and play" element makes switches an attractive alternative to hubs.

Switches can connect different network types (such as Ethernet and Fast Ethernet) or networks of the same type. Many switches today offer high-speed links, like Fast Ethernet, which can be used to link the switches together or to give added bandwidth to important servers that get a lot of traffic. A network composed of a number of switches linked together via these fast uplinks is called a "collapsed backbone" network.

Dedicating ports on switches to individual nodes is another way to speed access for critical computers. Servers and power users can take advantage of a full segment for one node, so some networks connect high traffic nodes to a dedicated switch port.

Full duplex is another method to increase bandwidth to dedicated workstations or servers. To use full duplex, both network interface cards used in the server or workstation and the switch must support full duplex operation. Full duplex doubles the potential bandwidth on that link.

## Network Congestion

![tutors_p6-ethcapacity](http://www.lantronix.com/wp-content/uploads/img/tutors_p6-ethcapacity.gif)

As more users are added to a shared network or as applications requiring more data are added, performance deteriorates. This is because all users on a shared network are competitors for the Ethernet bus. A moderately loaded 10 Mbps Ethernet network is able to sustain utilization of 35 percent and throughput in the neighborhood of 2.5 Mbps after accounting for packet overhead, inter-packet gaps and collisions. A moderately loaded Fast Ethernet or Gigabit Ethernet shares 25 Mbps or 250 Mbps of real data in the same circumstances. With shared Ethernet and Fast Ethernet, the likelihood of collisions increases as more nodes and/or more traffic is added to the shared collision domain.

Ethernet itself is a shared media, so there are rules for sending packets to avoid conflicts and protect data integrity. Nodes on an Ethernet network send packets when they determine the network is not in use. It is possible that two nodes at different locations could try to send data at the same time. When both PCs are transferring a packet to the network at the same time, a collision will result. Both packets are retransmitted, adding to the traffic problem. Minimizing collisions is a crucial element in the design and operation of networks. Increased collisions are often the result of too many users or too much traffic on the network, which results in a great deal of contention for network bandwidth. This can slow the performance of the network from the user's point of view. Segmenting, where a network is divided into different pieces joined together logically with switches or routers, reduces congestion in an overcrowded network by eliminating the shared collision domain.

Collision rates measure the percentage of packets that are collisions. Some collisions are inevitable, with less than 10 percent common in well-running networks.

| The Factors Affecting Network Efficiency
| --------------------------------------------------------------------------
| - Amount of traffic
- Number of nodes
- Size of packets
- Network diameter

| Measuring Network Efficiency
| --------------------------------------------------------------------
| - Average to peak load deviation
- Collision Rate
- Utilization Rate

Utilization rate is another widely accessible statistic about the health of a network. This statistic is available in Novell's console monitor and WindowsNT performance monitor as well as any optional LAN analysis software. Utilization in an average network above 35 percent indicates potential problems. This 35 percent utilization is near optimum, but some networks experience higher or lower utilization optimums due to factors such as packet size and peak load deviation.

A switch is said to work at "wire speed" if it has enough processing power to handle full Ethernet speed at minimum packet sizes. Most switches on the market are well ahead of network traffic capabilities supporting the full "wire speed" of Ethernet, 14,480 pps (packets per second), and Fast Ethernet, 148,800 pps.

### Routers

Routers work in a manner similar to switches and bridges in that they filter out network traffic. Rather than doing so by packet addresses, they filter by specific protocol. Routers were born out of the necessity for dividing networks logically instead of physically. An IP router can divide a network into various subnets so that only traffic destined for particular IP addresses can pass between segments. Routers recalculate the checksum, and rewrite the MAC header of every packet. The price paid for this type of intelligent forwarding and filtering is usually calculated in terms of latency, or the delay that a packet experiences inside the router. Such filtering takes more time than that exercised in a switch or bridge which only looks at the Ethernet address. In more complex networks network efficiency can be improved. An additional benefit of routers is their automatic filtering of broadcasts, but overall they are complicated to setup.

| Switch Benefits
| ----------------------------------------------------------------------------------------------------------------------------------------------
| - Isolates traffic, relieving congestion
- Separates collision domains, reducing collisions
- Segments, restarting distance and repeater rules

| Switch Costs
| ------------------------------------------------------------------------------------------------------------------------------------------------
| - Price: currently 3 to 5 times the price of a hub
- Packet processing time is longer than in a hub
- Monitoring the network is more complicated

## General Benefits of Network Switching

Switches replace hubs in networking designs, and they are more expensive. So why is the desktop switching market doubling ever year with huge numbers sold? The price of switches is declining precipitously, while hubs are a mature technology with small price declines. This means that there is far less difference between switch costs and hub costs than there used to be, and the gap is narrowing.

Since switches are self learning, they are as easy to install as a hub. Just plug them in and go. And they operate on the same hardware layer as a hub, so there are no protocol issues.

There are two reasons for switches being included in network designs. First, a switch breaks one network into many small networks so the distance and repeater limitations are restarted. Second, this same segmentation isolates traffic and reduces collisions relieving network congestion. It is very easy to identify the need for distance and repeater extension, and to understand this benefit of network switching. But the second benefit, relieving network congestion, is hard to identify and harder to understand the degree by which switches will help performance. Since all switches add small latency delays to packet processing, deploying switches unnecessarily can actually slow down network performance. So the next section pertains to the factors affecting the impact of switching to congested networks.

## Network Switching

The benefits of switching vary from network to network. Adding a switch for the first time has different implications than increasing the number of switched ports already installed. Understanding traffic patterns is very important to network switching – the goal being to eliminate (or filter) as much traffic as possible. A switch installed in a location where it forwards almost all the traffic it receives will help much less than one that filters most of the traffic.

Networks that are not congested can actually be negatively impacted by adding switches. Packet processing delays, switch buffer limitations, and the retransmissions that can result sometimes slows performance compared with the hub based alternative. If your network is not congested, don't replace hubs with switches. How can you tell if performance problems are the result of network congestion? Measure utilization factors and collision rates.

| Good Candidates for Performance Boosts from Switching
| -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
| - Utilization more than 35%
- Collision rates more than 10%
| Utilization load is the amount of total traffic as a percent of the theoretical maximum for the network type, 10 Mbps in Ethernet, 100 Mbps in Fast Ethernet. The collision rate is the number of packets with collisions as a percentage of total packages

Network response times (the user-visible part of network performance) suffers as the load on the network increases, and under heavy loads small increases in user traffic often results in significant decreases in performance. This is similar to automobile freeway dynamics, in that increasing loads results in increasing throughput up to a point, then further increases in demand results in rapid deterioration of true throughput. In Ethernet, collisions increase as the network is loaded, and this causes retransmissions and increases in load which cause even more collisions. The resulting network overload slows traffic considerably.

Using network utilities found on most server operating systems network managers can determine utilization and collision rates. Both peak and average statistics should be considered.

## Replacing a Central Hub with a Switch

This switching opportunity is typified by a fully shared network, where many users are connected in a cascading hub architecture. The two main impacts of switching will be faster network connection to the server(s) and the isolation of non-relevant traffic from each segment. As the network bottleneck is eliminated performance grows until a new system bottleneck is encountered – such as maximum server performance.

## Adding Switches to a Backbone Switched Network

Congestion on a switched network can usually be relieved by adding more switched ports, and increasing the speed of these ports. Segments experiencing congestion are identified by their utilization and collision rates, and the solution is either further segmentation or faster connections. Both Fast Ethernet and Ethernet switch ports are added further down the tree structure of the network to increase performance.

## Designing for Maximum Benefit

Changes in network design tend to be evolutionary rather than revolutionary-rarely is a network manager able to design a network completely from scratch. Usually, changes are made slowly with an eye toward preserving as much of the usable capital investment as possible while replacing obsolete or outdated technology with new equipment.

Fast Ethernet is very easy to add to most networks. A switch or bridge allows Fast Ethernet to connect to existing Ethernet infrastructures to bring speed to critical links. The faster technology is used to connect switches to each other, and to switched or shared servers to ensure the avoidance of bottlenecks.

Many client/server networks suffer from too many clients trying to access the same server which creates a bottleneck where the server attaches to the LAN. Fast Ethernet, in combination with switched Ethernet, creates the perfect cost-effective solution for avoiding slow client server networks by allowing the server to be placed on a fast port.

Distributed processing also benefits from Fast Ethernet and switching. Segmentation of the network via switches brings big performance boosts to distributed traffic networks, and the switches are commonly connected via a Fast Ethernet backbone.

| Good Candidates for Performance Boosts from Switching
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
| - Important to know network demand per node
- Try to group users with the nodes they communicate with most often on the same segment
- Look for departmental traffic patterns
- Avoid switch bottlenecks with fast uplinks
- Move users switch between segments in an iterative process until all nodes seeing less than 35% utilization

![clsrvtrf](http://www.lantronix.com/wp-content/uploads/img/clsrvtrf.gif)![distitrf](http://www.lantronix.com/wp-content/uploads/img/distitrf.gif)

## Advanced Switching Technology Issues

There are some technology issues with switching that do not affect 95% of all networks. Major switch vendors and the trade publications are promoting new competitive technologies, so some of these concepts are discussed here.

### Managed or Unmanaged

Management provides benefits in many networks. Large networks with mission critical applications are managed with many sophisticated tools, using SNMP to monitor the health of devices on the network. Networks using SNMP or RMON (an extension to SNMP that provides much more data while using less network bandwidth to do so) will either manage every device, or just the more critical areas. VLANs are another benefit to management in a switch. A VLAN allows the network to group nodes into logical LANs that behave as one network, regardless of physical connections. The main benefit is managing broadcast and multicast traffic. An unmanaged switch will pass broadcast and multicast packets through to all ports. If the network has logical grouping that are different from physical groupings then a VLAN-based switch may be the best bet for traffic optimization.

Another benefit to management in the switches is Spanning Tree Algorithm. Spanning Tree allows the network manager to design in redundant links, with switches attached in loops. This would defeat the self learning aspect of switches, since traffic from one node would appear to originate on different ports. Spanning Tree is a protocol that allows the switches to coordinate with each other so that traffic is only carried on one of the redundant links (unless there is a failure, then the backup link is automatically activated). Network managers with switches deployed in critical applications may want to have redundant links. In this case management is necessary. But for the rest of the networks an unmanaged switch would do quite well, and is much less expensive.

### Store-and-Forward vs. Cut-Through

LAN switches come in two basic architectures, cut-through and store-and-forward. Cut-through switches only examine the destination address before forwarding it on to its destination segment. A store-and-forward switch, on the other hand, accepts and analyzes the entire packet before forwarding it to its destination. It takes more time to examine the entire packet, but it allows the switch to catch certain packet errors and collisions and keep them from propagating bad packets through the network.

Today, the speed of store-and-forward switches has caught up with cut-through switches to the point where the difference between the two is minimal. Also, there are a large number of hybrid switches available that mix both cut-through and store-and-forward architectures.

### Blocking vs. Non-Blocking Switches

Take a switch's specifications and add up all the ports at theoretical maximum speed, then you have the theoretical sum total of a switch's throughput. If the switching bus, or switching components cannot handle the theoretical total of all ports the switch is considered a "blocking switch". There is debate whether all switches should be designed non-blocking, but the added costs of doing so are only reasonable on switches designed to work in the largest network backbones. For almost all applications, a blocking switch that has an acceptable and reasonable throughput level will work just fine.

Consider an eight port 10/100 switch. Since each port can theoretically handle 200 Mbps (full duplex) there is a theoretical need for 1600 Mbps, or 1.6 Gbps. But in the real world each port will not exceed 50% utilization, so a 800 Mbps switching bus is adequate. Consideration of total throughput versus total ports demand in the real world loads provides validation that the switch can handle the loads of your network.

### Switch Buffer Limitations

As packets are processed in the switch, they are held in buffers. If the destination segment is congested, the switch holds on to the packet as it waits for bandwidth to become available on the crowded segment. Buffers that are full present a problem. So some analysis of the buffer sizes and strategies for handling overflows is of interest for the technically inclined network designer.

In real world networks, crowded segments cause many problems, so their impact on switch consideration is not important for most users, since networks should be designed to eliminate crowded, congested segments. There are two strategies for handling full buffers. One is "backpressure flow control" which sends packets back upstream to the source nodes of packets that find a full buffer. This compares to the strategy of simply dropping the packet, and relying on the integrity features in networks to retransmit automatically. One solution spreads the problem in one segment to other segments, propagating the problem. The other solution causes retransmissions, and that resulting increase in load is not optimal. Neither strategy solves the problem, so switch vendors use large buffers and advise network managers to design switched network topologies to eliminate the source of the problem – congested segments.

### Layer 3 Switching

A hybrid device is the latest improvement in internetworking technology. Combining the packet handling of routers and the speed of switching, these multilayer switches operate on both layer 2 and layer 3 of the OSI network model. The performance of this class of switch is aimed at the core of large enterprise networks. Sometimes called routing switches or IP switches, multilayer switches look for common traffic flows, and switch these flows on the hardware layer for speed. For traffic outside the normal flows, the multilayer switch uses routing functions. This keeps the higher overhead routing functions only where it is needed, and strives for the best handling strategy for each network packet.

Many vendors are working on high end multilayer switches, and the technology is definitely a "work in process". As networking technology evolves, multilayer switches are likely to replace routers in most large networks.
