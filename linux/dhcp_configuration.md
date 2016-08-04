# DHCP configuration

[Original URL](http://www.packettracernetwork.com/tutorials/dhcpconfiguration.html)

> Details Last Updated: Friday, 06 February 2015 21:57 Hits: 52677 IntroductionDHCP service is a key component of your network infrastructure by allowing centralized ip address management on a single...

Details<br>
Last Updated: Friday, 06 February 2015 21:57

Hits: 52677

## Introduction

DHCP service is a key component of your network infrastructure by allowing centralized ip address management on a single pool of servers. DHCP configuration is also part of CCNA and CCNP Switch certification exams curricula. This skill can be tested in lab environnement during exams ans it's important for students to get used to DHCP configuration before taking the exam.

Packet Tracer 6.0 implements two methods for setting up a DHCP server in your network :

- Configuration of DHCP pools on Cisco routers or multlayer switches.
- Configuration of a standalone DHCP server appliance on the network and usage of the "ip helper-address" command on network devices for DHCP traffic forwarding outside each local broadcast domain. This tutorial will describe this method for implementing DHCP service in your network.

## Tutorial description

This tutorial will show you how to configure dynamic IP address assignment on multiple VLAN with a unique DHCP server appliance on the network.

Two VLANs are configured on Switch0 with Router0 as default gateway :

- VLAN 10 - Nework : 192.168.10.0/24 - Gateway : 192.168.10.1 (FA 0/0.10)
- VLAN 20 - Network : 192.168.20.0/24 - Gateway : 192.168.20.1 (FA 0/0.20)

The unique DHCP server is located on a remote subnet with IP 172.16.24.2.

![DHCP lab overview on packet tracer 5]( "DHCP lab overview on packet tracer 5")

## 

## Learning DHCP : recommended books

Declare IP address pools on the DHCP management tab of the server like on the picture below. One pool has to be declared for each VLAN. Don't forget to configure the right network settings and default gateway (Router0 FA 0/0.10 and FA 0.0.20 IP address) for each VLAN.

![DHCP server configuration on packet tracer 5.3]( "DHCP server configuration on packet tracer 5.3")

## Configure router0 for DHCP forwarding

Router(config)# interface FastEthernet0/0.10

Router(config-subif)# encapsulation dot1Q 10

Router(config-subif)# ip address 192.168.10.1 255.255.255.0

Router(config-subif)# ip helper-address 172.16.24.2

Router(config)# interface FastEthernet0/0.20

Router(config-subif)# encapsulation dot1Q 20

Router(config-subif)# ip address 192.168.20.1 255.255.255.0

Router(config-subif)# ip helper-address 172.16.24.2
