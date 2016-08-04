# How to Perform Network Sniffing with Tshark

[Original URL](http://linoxide.com/linux-how-to/network-sniffing-tshark/)

> This time let's talk about Tshark, a powerful command-line network analyzer that comes with the well known Wireshark. It works like Tcpdump, but with powerful decoders and filters, capable to...

This time let's talk about Tshark, a powerful command-line network analyzer that comes with the well known Wireshark. It works like Tcpdump, but with powerful decoders and filters, capable to capture information of different network layers or protocols, and display in different format and layouts.

Used to analyze network traffic in real-time or read pcap/pcapng files to look for information, digging into details of connections, helping to identify network anomalies, problems or trends. Helping network and security professionals to be ahead of the user and their needs, prevent problems and security threats or solve them before it is too late.

[![sample Tshark session](http://blog.linoxide.com/wp-content/uploads/2016/02/tshark-simple-1024x564.jpg)](http://blog.linoxide.com/wp-content/uploads/2016/02/tshark-simple.jpg) sample Tshark session

## Table of contents

- Intro
- Filters

  - Capture filters
  - Display filters

- Output

## Intro

### Why Tshark

Tshark works like tcpdump, ngrep and others, however as it provides the protocol decoding features of Wireshark, you will be much more confortable reading its output as it makes network analysis on terminal more human.

Tshark is a terminal application capable of doing virtually anything you do with Wireshark, but with no need for clicks or screens. This makes it great when you need to do some scripting, such as cron scheduled captures, send the data to sed, awk, perl, mail, database or so.

Tshark is a great fit for remote packet capture, on devices such as gateways, you just need to login ssh and use as you would do on localhost.

## Capture, read and write packets

In our first run on Tshark try to call it with no parameters, this will start capturing packets on the default network interface.

> tshark

There may be more than one interface on your machine and you may need to specify which one you want use. To get a list of available interfaces use the **-D**

> tshark -D

Once you find out which interface to use, call Tshark with the **-i** option and an interface name or number reported by the -D option.

> tshark -i eth1

> tshark -i 3

Now that you can capture the packets over the network, you may want to save them for later inspection, this can be done with the **-w** option.

> tshark -i wlan0 -w /tmp/traffic.pcap

To analyze the packets from the previously saved traffic.pcap file, use the **-r** option, this will read packets from the instead of a network interface. Note also that you don't need superuser rights to read from files.

> tshark -r /tmp/traffic.pcap

By default name resolution is performed, you may use **-n** and disable this for a best performance in some cases.

> tshark -n

## Filters

If you are on a busy network, you may have screen like on the Matrix movies, with all kind information, flowing too fast and almost impossible to read. To solve this problem Tshark provides two types of filters that will let you see beyond the chaos.

### Capture filters

You can use the traditional pcap/bpf filter to select what to capture from your interface.

Search for packets relaated to the 192.168.1.100 host on port 80 or 53.

> tshark -i 2 -f "host 192.168.1.100 and (dst port 53 or 80)"

Ignore packets on multicast and broadcast domains

> tshark -i eth3 -f "not broadcast and not multicast"

### Display filters

Display filters are set with **-Y** and have the following syntax

To see all connections from host 192.168.1.1

> tshark -i eth0 -Y "ip.addr==192.168.1.1"

Display HTTP requests on TCP port 8800

> tshark -i eth0 -Y "tcp.port== 8800 and http.request"

Display all but ICMP and ARP packets

> tshark -i eth0 -Y "not arp or icmp"

## Formatting

Sometimes you need more or less information from the network packets to be displayed, also you may need to specify how/where to show this information. The following options let you do exactly this.

Use **-V** to make Tshark verbose and display details about packets, from frame number, protocol field, to packet data or flags.

The **-O** option is much like the -V option, however this will show details of a specific protocol

> tshark -i eth2 -O icmp

Use the **-T** option to output data in different formats, this can be very handy when you need a specific format to your analysis. such as when you are using Tshark to fill some database

> tshark -i wlan0 -O icmp -T fields -e frame.number -e data

If you choose fields to the -T option, you must set the **-e** option at least once, this will tell Tshark wich field of information to display, you can use this option multiple times to display more fields

> tshark -r nmap_OS_scan_succesful -Y "tcp.ack" -T fields -e frame.number -e ip.src -e tcp.seq -e tcp.ack -e tcp.flags.str -e tcp.flags -e tcp.analysis.acks_frame

[![Nmap standard scan](http://blog.linoxide.com/wp-content/uploads/2016/02/nmap_standard_scan.jpg)](http://blog.linoxide.com/wp-content/uploads/2016/02/nmap_standard_scan.jpg) Nmap standard scan

To get a complete list of the possible fields to use with the -e flag use -G option as below

> tshark -G fields | less

Further formatting can be done with the -E flag, you can show/hide headers, set quote character and more.

Look at the following command that combine these options to produce a CSV file.

> tshark -r captured.cap -T fields -e frame.number -e frame.encap_type -e frame.protocols -e frame.len -e ip.addr -E separator=, -E quote=d > outfile.csv

[![Tshark output of selected fields in CSV format](http://blog.linoxide.com/wp-content/uploads/2016/02/tshark-csv-fields1.jpg)](http://blog.linoxide.com/wp-content/uploads/2016/02/tshark-csv-fields1.jpg) Tshark output of selected fields in CSV format

## Statistics

Sometimes you may want an analytical report, in this case use **-z** option followed by one of the many report types available.

Report on SMB , DNS and IP protocols.

> tshark -i ens1 -z smb,srt -z dns,tree -z http,tree -z hosts

[![Tshark SMB statistics](http://blog.linoxide.com/wp-content/uploads/2016/02/tshark_statistics_smb_ip.jpg)](http://blog.linoxide.com/wp-content/uploads/2016/02/tshark_statistics_smb_ip.jpg) Tshark SMB statistics

List of IP conversations

> tshark -r cap.pcap -z conv,ip

[![IP conversations report](http://blog.linoxide.com/wp-content/uploads/2016/02/statistic_conv_ip.jpg)](http://blog.linoxide.com/wp-content/uploads/2016/02/statistic_conv_ip.jpg) IP conversations report

To get a list of the available reports try the -z help option.

> tshark -z help

## Secure sockets

You can also analyze encrypted connections like SSL, the following example is showing the HTTP within the secure socket layer

[![Details of an HTTP connection over SSL](http://blog.linoxide.com/wp-content/uploads/2016/02/tshark-ssl.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/tshark-ssl.png) Details of an HTTP connection over SSL

Here we are displaying packets on the TCP port 443, telling Tshark to be verbose with the HTTP protocol, do segmentation on SSL, search the private key on the PEM formatted server-x.key file and dump debug information on the debug-ssl.log file.

> tshark -r encrypted-packets.pcap -Y "tcp.port == 443" -O http \

> -o "ssl.desegment_ssl_records: TRUE" \

> -o "ssl.desegment_ssl_application_data: TRUE" \

> -o "ssl.keys_list: 127.0.0.1,443,http,server-x.key" \

> -o "ssl.debug_file: debug-ssl.log"

## Conclusion

This is how to start using Tshark to sniff out packets from your networks, help you to figure out where to look when problems arise, debug your network services, inspect security and analyze the general health.

For more information take a look the links below

- [Wireshark/Tshark homepage](http://www.wireshark.org)
- [Display Filter Referrence](https://www.wireshark.org/docs/dfref/)
- [Sample cature files](https://wiki.wireshark.org/SampleCaptures)

Thanks for reading!

**Category**: [LINUX HOWTO](http://linoxide.com/category/linux-how-to/), [NETWORK](http://linoxide.com/category/netwrk/)
