# Netflow configuration

[Original URL](http://www.packettracernetwork.com/tutorials/packet-tracer-netflow.html)

> Details Last Updated: Friday, 06 February 2015 21:57 Hits: 4276 What is Netflow?Netflow is a network analysis protocol that was initially created by Cisco to give the ability to collect detailled...

Details<br>
Last Updated: Friday, 06 February 2015 21:57

Hits: 4276

## What is Netflow?

Netflow is a network analysis protocol that was initially created by Cisco to give the ability to collect detailled informations about network traffic as it flows through a router interface.

The data processed by Netflow collectors provides the network administrator with detailled traffic informations such as the source and destination of the traffic, class of service, .... Netflow version 9 was standardized in 2008 as IPFIX by the IETF organization. This feature is used by network operators for billing network users.

NetFlow records are exported to a Netflow collector using User Datagram Protocol (UDP). The IP address and the destination UDP port of the NetFlow collector have to be configured on the sending device (router or l3 switch). The standard value is UDP port 2055, but other values like 9555 or 9995 can also be used.

## Netflow in Packet Tracer 6.1

The following Netflow components are available in Packet Tracer 6.1.1 :

- Netflow version 9 on Cisco ISR routers. This feature is not available on Layer 3 switches.
- A netflow collector software on PC, laptop and server devices.

![Packet Tracer 6.1 - ISR router netflow records]( "Packet Tracer 6.1 - ISR router netflow records") | ![Packet Tracer 6.1 - Netflow collector software]( "Packet Tracer 6.1 - Netflow collector software")
---------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------

## 

## Netflow configuration using Cisco 2811 ISR routers

### Network topology and configuration instructions**

**

**![]()**

**IP configuration :**

- IP Phone n°1 : 192.168.10.2/24
- IP Phone n°2 : 192.168.10.4/24
- Router Fa0/0 : 192.168.10.1/24
- Router Fa0/1 : 192.168.20.1/24
- Netflow Collector : 192.168.20.2/24

**First step** : Configure netflow collection on router interfaces using the "ip flow" IOS command. The flow can be configured on either ingress or egress direction. It is recommended to configure the flow on ingress.

```
interface FastEthernet0/0
 ip flow ingress
 ip address 192.168.10.1 255.255.255.0
 duplex auto
 speed auto
```

**Second step** : Configure the ISR router to send the flow records to the netflow collector. Packet Tracer 6.1 netflow collection software deployed on end devices (servers or PCs) uses UDP port 9996.

```
ip flow-export destination 192.168.20.2 9996
ip flow-export version 9
```

### Testing the configuration

Make a phone call from IP Phone n°1 to IP Phone n°2 and use the "**show ip cache flow**" command on the ISR router to display the router's netflow cache. In this example, the router shows a SCCP (TCP-SKINNY) flow between the two IP Phones (192.168.10.2 & 192.168.10.4).

```
RouterA#show ip cache flow 
IP packet size distribution (729 total packets):
 1-32 64 96 128 160 192 224 256 288 320 352 384 416 448 480
 .000 1.00 .000 .000 .000 .000 .000 .000 .000 .000 .000 .000 .000 .000 .000 

 512 544 576 1024 1536 2048 2560 3072 3584 4096 4608
 .000 .000 .000 .000 .000 .000 .000 .000 .000 .000 .000 

IP Flow Switching Cache, 278544 bytes
 2 active, 4094 inactive, 337 added
 3 ager polls, 0 flow alloc failures
 Active flows timeout in 30 minutes
 Inactive flows timeout in 15 seconds
IP Sub Flow Cache, 34056 bytes
 0 active, 1024 inactive, 0 added, 0 added to flow
 0 alloc failures, 0 force free
 1 chunk, 1 chunk added
 last clearing of statistics never
Protocol Total Flows Packets Bytes Packets Active(Sec) Idle(Sec)
-------- Flows /Sec /Flow /Pkt /Sec /Flow /Flow
TCP-SKINNY 335 0.1 2 40 0.1 1.3 15.0
Total: 335 0.1 2 40 0.1 1.3 15.0

SrcIf SrcIPaddress DstIf DstIPaddress Pr SrcP DstP Pkts
Fa0/0 192.168.10.4 Local 192.168.10.1 06 0401 07d0 8
Fa0/0 192.168.10.2 Local 192.168.10.1 06 0401 07d0 23
RouterA#
```
