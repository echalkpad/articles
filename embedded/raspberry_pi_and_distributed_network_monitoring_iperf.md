# Raspberry Pi and Distributed Network Monitoring: Iperf

[Original URL](https://netbeez.net/2014/08/19/raspberry-pi-and-distributed-network-monitoring-iperf/)

> The Raspberry Pi (RPi) has been a smash hit in so many different areas. Hobbyists love them and professionals have found a reliable, low-cost, and small-footprint platform to build commercial...

[![RPi+Iperf](https://netbeez.net/wp-content/uploads/2014/08/RPi+Iperf1-300x288.jpg)](https://netbeez.net/wp-content/uploads/2014/08/RPi+Iperf1.jpg)The Raspberry Pi (RPi) has been a smash hit in so many different areas. Hobbyists love them and professionals have found a reliable, low-cost, and small-footprint platform to build commercial products. For these reasons, the Raspberry Pi is our preferred platform for implementing end-user [distributed network monitoring](http://netbeez.net/2014/05/20/distributed-network-monitoring/) here at NetBeez.

In this series of blog posts we'll cover several different ways on how to use the $35 Linux platform to perform network tests that can help you troubleshoot network issues. What we'll cover applies to any Linux platform, but with a focus on RPi's idiosyncrasies. Their main benefit is their price point that allows the deployment of dozens or hundreds of independent Linux platforms at each network locations readily available for your troubleshooting needs.

<span>
  <strong><br>Raspberry Pi Set Up  
</strong>
</span>

To set up an RPi with its operating system you can follow the instructions [here](http://www.raspberrypi.org/documentation/installation/installing-images/). Alternatively, you can order a kit that includes the RPi, the power supply, and the SD card with the OS installed. The preferred distributors or the RPi Foundation are[www.element14.com](http://www.element14.com) and [egoman.com.cn](http://www.egoman.com.cn/).

<span>
  <strong>Iperf</strong>
</span>

[Iperf](http://en.wikipedia.org/wiki/Iperf)is an open source Linux tool that can be used for bandwidth testing. You can find two very good tutorials [here](https://iperf.fr/#multicast)and [here](http://openmaniak.com/iperf.php)that provide an introduction on Iperf and all of its command-line options.

To run an Iperf test you need two RPis: one to act as a client and one to act as a server. The client pushes traffic through the network while the server receives the data and measures statistics. Note that Iperf pushes actual traffic through your network, and you need to take this into consideration to avoid clogging your pipes.

To install Iperf log into the RPi, open a console and type:

sudo apt-get install iperf

Now you can start playing with Iperf. Here is a list of the most useful types of tests your can perform:

1. <span>
    <strong>TCP:</strong>
  </span>

   The TCP test pushes as much traffic as possible between the two RPis. All current RPi models have a 10/100 interface, so the maximum theoretical bandwidth they can push is 100Mbps. Note that Iperf is CPU intensive and the maximum achievable bandwidth is also limited by the RPi's CPU capabilities. If you are not running any other heavy applications on the RPi, you might be able to achieve up to 75Mbps. If you overclock the CPU, you might get close to the theoretical maximum of 100MBps. Since TCP Iperf is CPU intensive it can skew any other tests you might be running on the RPi.

Commands (start the server command first):<br>
(Note: command line outputs have been shortened for clarity)

Client agent:<br>
pi@172.30.0.20 :~:$ iperf -s<br>
[ 4] 0.0-10.0 sec 87.2 MBytes 73.0 Mbits/secServer agent:<br>
pi@172.30.0.11 :~:$ iperf -c 172.30.0.20<br>
[ 3] 0.0-10.0 sec 87.2 MBytes 73.0 Mbits/sec

1. **<span>UDP (jitter/packet loss)</span>

  :** WithUDP you can control the amount of traffic to be pushed from the client to the server. E.g. you can test your pipes with a 10Mbps test. UDP also measures the jitter and the packet loss between the two ends. These two metrics are used by many network engineers as an early indication of network problems (e.g. run a 1Mbps UPD Iperf every 1 hour and raise an alarm when jitter goes beyond 15 milliseconds).

Commands (if you start the server after the client, some packets will get lost. It is advised to start the server before the client):

Server agent:<br>
pi@172.30.0.20 :~:$ iperf -s -u<br>
[ ID] Interval Transfer Bandwidth Jitter Lost/Total Datagrams<br>
[ 3] 0.0-10.0 sec 11.9 MBytes 10.0 Mbits/sec 0.059 ms 0/ 8504 (0%)<br>
[ 3] 0.0-10.0 sec 1 datagrams received out-of-orderClient agent:

pi@172.30.0.11 :~:$ iperf -c 172.30.0.20 -u -b 10M<br>
[ 3] 0.0-10.0 sec 11.9 MBytes 10.0 Mbits/sec 0.058 ms 0/ 8504 (0%)<br>
[ 3] 0.0-10.0 sec 1 datagrams received out-of-order

1. **<span>Multicast</span>

  :** If you have multicast enabled in your network, you can test if your forwarding works as expected. You can start more than one multicast servers and bind them to a specific group address, and then broadcast from a client to all servers. Also, you can set the maximum Time-To-Live (ttl) value to make sure your multicast packets reach each server within the desired hop limit.

Commands (if you start the server after the client, some packets will get lost. It is advised to start the servers before the client):

Server agent 1:<br>
pi@172.30.0.20 :~:$ iperf -s -u -B 224.0.67.67<br>
[ ID] Interval Transfer Bandwidth Jitter Lost/Total Datagrams<br>
[ 3] 0.0-10.0 sec 1.25 MBytes 1.05 Mbits/sec 0.061 ms 0/ 893 (0%)Server agent 2:<br>
pi@172.30.0.13 :~:$ iperf -s -u -B 224.0.67.67<br>
[ ID] Interval Transfer Bandwidth Jitter Lost/Total Datagrams<br>
[ 3] 0.0-10.0 sec 801 KBytes 655 Kbits/sec 0.068 ms 335/ 893 (38%)Client agent:<br>
pi@172.30.0.11 :~:$iperf -c 224.0.67.67 -u –ttl 5<br>
[ 3] 0.0-10.0 sec 1.25 MBytes 1.05 Mbits/sec<br>
[ 3] Sent 893 datagrams

1. **<span>Type of Service</span>

  :** If you want to test how your routers forward packets based on their type-of-service (TOS) marking you can use the option –tos.

Commands (start the server command first):

Server agent:<br>
pi@172.30.0.20 :~:$ iperf -s<br>
[ 4] 0.0-10.0 sec 89.9 MBytes 75.2 Mbits/secClient agent:<br>
pi@172.30.0.11 :~:$ iperf -c 172.30.0.20 –tos 96<br>
[ 3] 0.0-10.0 sec 89.9 MBytes 75.2 Mbits/sec

1. **<span>Other options</span>

  :** There are other useful functionalities of Iperf, such as MTU discovery, daemonize the server, run concurrent client-to-server and server-to-client tests, and others. The interested reader can delve into more details <here> and <here>.

<span>
  <strong>Use Case</strong>
</span>

When a user complains about an application being slow, the network is blamed first. Until proven otherwise, the networking group jumps onto the problem. A quick way to check if a connection can pass a certain amount of traffic is to run, for example, a 10Mbps Iperf test between the RPi deployed at the location that opened the ticket (Iperf client) and the RPi sitting at the datacenter that runs the application (Iperf server). If the results show that the bandwidth is in good standing, then you can pass the ticket to the next level of support.

Ipef is only one of many open source tools that can be used for network and application troubleshooting. In following blog posts I will cover how to test DNS resolution time, TCP ports, HTTP applications, and others. Stay tuned!

**Do you want to learn how you can monitor your network with NetBeez? :** [Download How To Monitor DNS and Cloud Services](https://netbeez.net/resources/download-how-to-monitor-dns-and-cloud-services/)
