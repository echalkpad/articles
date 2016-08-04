# Ubuntu setup a bonding device and enslave two real Ethernet devices

[Original URL](http://www.cyberciti.biz/faq/ubuntu-setup-a-bonding-device-and-enslave-two-real-ethernet-devices/)

> How do I setup a bonding device bond0 and enslave two real Ethernet (say eth0 and eth2) devices to it on Ubuntu Linux 140.04 or 16.04 LTS server? How can I create 802.3ad bonding mode (IEEE 802.3ad...

[![](http://s0.cyberciti.org/images/category/old/ubuntu-logo.jpg)](http://www.cyberciti.biz/faq/category/ubuntu-linux/ "See all Ubuntu Linux related FAQ")

<span class="drop_cap">H</span>

ow do I setup a bonding device bond0 and enslave two real Ethernet (say eth0 and eth2) devices to it on Ubuntu Linux 140.04 or 16.04 LTS server? How can I create 802.3ad bonding mode (IEEE 802.3ad Dynamic link aggregation) on Ubuntu Linux 16.04 LTS server?

You need to install and use a tool called ifenslave. It will attach and detach slave network devices to a bonding device. The Linux kernel comes with bonding driver which provides a method for aggregating multiple network interfaces into a single logical "bonded" interface. You need **bonding to get higher data rates and as well as link failover**.

## Install ifenslave on Ubuntu

Type the following command:<br>
`$ sudo apt install ifenslave`

## Our sample setup

- Bonding interface name: bond0
- Link aggregation type: 802.3ad (mode 4)
- Physical interfaces: eth0 and eth2 (as per your needs)
- IP address assigned only to: bond0 (do not assign any IP address to eth0 and eth2 interfaces)

## How to setup a bonding device on Ubuntu Linux

The following **example shows how to setup a bonding device and enslave two real Ethernet devices** to it from command prompt:

1. Load driver, run: # modprobe bonding
2. Setup bond0 with an IP address, run: # ifconfig bond0 192.168.1.254 netmask 255.255.255.0
3. Enslave two interfaces, run: # ifenslave bond0 eth0 eth2
4. Verify it: # ifconfig bond0
5. View bonding info: # more /proc/net/bonding/bond0

## Persistent bond0 configuration

Edit /etc/network/interfaces file, run:<br>
`$ sudo vi /etc/network/interfaces`<br>
Edit/update your config file as follows:

| ```go
auto bond0
iface bond0 inet static
# Example add eth0 speed if needed (I'm going with auto detect/nego)
#pre-up /usr/sbin/ethtool -s bond0 speed 1000 duplex full autoneg on
post-up ifenslave bond0 eth0 eth2
pre-down ifenslave -d bond0 eth0 eth2
bond-slaves none
# 802.3ad mode
bond-mode 4
bond-lacp-rate fast
bond-miimon 100
bond-downdelay 0
bond-updelay 0
bond-xmit_hash_policy 1
address 192.168.1.254
netmask 255.255.255.192
# Add static route example if you needed #
# up route add -net xx.tt.yy.zz/X gateway a.b.c.d
# down route del -net xx.tt.yy.zz/X

auto eth0
iface eth0 inet manual
# Example add eth0 speed if needed
#pre-up /usr/sbin/ethtool -s eth0 speed 1000 duplex full autoneg on

auto eth2
iface eth2 inet manual
# Example add eth2 speed if needed
#pre-up /usr/sbin/ethtool -s eth2 speed 1000 duplex full autoneg on```
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Save and close the file. Restart the the networking service on Ubuntu Linux 14.04 LTS or older:<br>
`$ sudo /etc/init.d/networking restart`<br>
For Ubuntu Linux 16.04 LTS, enter:<br>
`$ sudo systemctl restart networking`

### Verify new settings

`$ ip a show bond0`<br>
OR<br>
`$ ifconfig bond0`<br>
Sample outputs:

```go
6: bond0:  mtu 1500 qdisc noqueue state UP group default qlen 1000
 link/ether 00:25:90:4f:b0:6c brd ff:ff:ff:ff:ff:ff
  inet 192.168.1.254/26 brd 10.86.115.127 scope global bond0
 valid_lft forever preferred_lft forever
 inet6 fe80::225:90ff:fe4f:b06c/64 scope link
 valid_lft forever preferred_lft forever
```

To see the current status of Linux kernel bonding driver and bond0 interface type:<br>
`$ more /proc/net/bonding/bond0`<br>
Sample outputs:

![Fig.01: bond0 status](http://s0.cyberciti.org/uploads/faq/2016/07/bond0-proc.jpg) Fig.01: bond0 status

## See also:

**Share this tutorial on:**[![='Buy](http://s0.cyberciti.org/images/5892587866cdf0d229b6b3e3305d997b/amz-sysadmin-tshirt.jpg)](http://amzn.to/20CvCJj)
