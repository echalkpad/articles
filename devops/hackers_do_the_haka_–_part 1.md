# Hackers do the Haka – Part 1

[Original URL](http://thisissecurity.net/2015/11/23/hackers-do-the-haka-part-1/)

> Haka is an open source network security oriented language that allows writing security rules and protocol dissectors. In this first part of a two-part series, we will focus on writing security rules....

<span class="st">Haka is an open source network security oriented language that allows writing security rules and protocol dissectors. In this first part of a two-part series, we will focus on writing security rules.</span>

## What is Haka

Haka is an open source security oriented language that allows specifying and applying security policies on live captured traffic. Haka is based on Lua. It is a simple, lightweight (~200 kB) and fast (a JiT compiler is available) scripting language.

The scope of Haka is twofold. First of all, it enables the specification of security rules to filter unwanted streams and report malicious activities. Haka provides a simple API for advanced packet and stream manipulation. One can drop packets or create new ones and inject them. Haka also supports on-the-fly packet modification. This is one of the main features of Haka since all complex tasks such as resizing packets, setting correctly sequence numbers are done transparently to the user. This is done live without the need of a proxy.

Secondly, Haka is endowed with a grammar allowing the specification of protocols and their underlying state machine. Haka supports both type of protocols : binary-based protocols (e.g. dns) and text-based protocols (e.g. http). The specification covers packet-based protocols such as ip as well as stream-based protocols like http.

Haka is embedded into a modular framework. It includes several packet capture modules (pcap, nfqueue) that enable end users to apply their security policy on live captured traffic or to replay it on a packet trace file. The framework provides also logging (syslog) and alerting modules (syslog, elasticsearch). Alerts follow an IDMEF-like format. Finally, the framework has auxiliary modules such as a pattern matching engine and an instruction disassembler module. These modules allow writing fine-grained security rules to detect obfuscated malware for instance. Haka was designed in a modular fashion enabling users to extend it with additional modules.

[![arch](https://thisiscybersec.files.wordpress.com/2015/10/arch.png?w=450&h=585)](https://thisiscybersec.files.wordpress.com/2015/10/arch.png)

## Haka Tool Suite

Haka provides a collection of four tools:

- **haka.** It is the main program of the collection. It is intended to be used as a daemon to monitor packets in the background. Packets are dissected and filtered according to the specified security policy file. Haka takes as input a configuration file. For example, the following configuration sample file instructs Haka to capture packets from the interface eth0 using nfqueue module and to filter them using the policy file myrules.lua. This script file loads typically user-defined or built-in protocol dissectors and defines a set of security rules. Additionally, users can select the alerting and reporting module to be used and set some specific module options:

  ```
  [general]
  # Select the haka configuration file to use
  configuration = "myrules.lua"

  # Optionally select the number of thread to use
  # By default, all system thread will be used
  #thread = 4

  [packet]
  # Select the capture model, nfqueue or pcap
  module = "packet/nfqueue"

  # Select the interfaces to listen to
  #interfaces = "any"
  interfaces = "eth0"

  # Select packet dumping for nfqueue
  #dump = yes
  #dump_input = "/tmp/input.pcap"
  #dump_output = "/tmp/output.pcap"

  [log]
  # Select the log module
  module = "log/syslog"

  # Set the default logging level
  #level = "info,packet=debug"

  [alert]
  # Select the alert module
  module = "alert/syslog"
  #module = "alert/file"
  #module = "alert/elasticsearch"

  # Disable alert on standard output
  #alert_on_stdout = no
  ```

- **hakactl.** This tool allows controling a running Haka daemon. One can get live statistics on captured packets, inspect logs or simply shutdown/restart the daemon.

- **hakapcap.** This tool allows replaying a policy file offline on a packet capture trace using the pcap module. For instance, this is useful to perform network forensics.
- **hakabana.** This tool allows visualizing and monitoring network traffic in real time using Kibana and Elasticsearch. Hakabana consists in a set of custom security rules that pushes information about the traffic that passes though Haka on an elastisserach server and made them available through a Kibana dashboard. An additional dashboard is also available to visualize Haka alerts.

[![Kibana dashboard to visualize alerts](https://thisiscybersec.files.wordpress.com/2015/10/kibana_ips.png?w=300&h=145)](https://thisiscybersec.files.wordpress.com/2015/10/kibana_ips.png)[![Kibana dashboard to monitor network trafic](https://thisiscybersec.files.wordpress.com/2015/10/hakabana_dashboard.png?w=300&h=147)](https://thisiscybersec.files.wordpress.com/2015/10/hakabana_dashboard.png)

## Writing security rules

Haka provides a simple way to write security rules in order to filter, modify, create and inject packets and streams. When a flow is detected as malicious, users can report an alert or drop the flow. Users can define even more complex scenarios to mitigate the impact of an attack. For instance, one can alter http requests to force obsolete browsers to update or forge specific packets to fool scanning port tools.

### Packet Filtering

The following rule is a basic packet filtering rule that blocks all connections from a given network address.

```
local ipv4 = require("protocol/ipv4")
local tcp = require("protocol/tcp_connection")

local net = ipv4.network("192.168.101.0/24")

haka.rule{
 hook = tcp.events.new_connection,
 eval = function (flow, pkt)
 haka.log("tcp connection %s:%i -> %s:%i",
 flow.srcip, flow.srcport,
 flow.dstip, flow.dstport)

 if net:contains(flow.dstip) then
 haka.alert{
 severity = "low",
 description = "connection refused",
 start_time = pkt.ip.raw.timestamp
 }
 flow:drop()
 end
 end
}
```

The first lines load the required protocol dissectors, namely, ipv4 and tcp connection dissectors. The first one handles ipv4 packets. The latter is a stateful tcp dissector that maintains a connection table and manages tcp streams. The next line, defines the network address that must be blocked.

The security rule is defined through _haka.rule_ keyword. A security rule is made of a _hook_ and a evaluation function _eval._ The _hook_ is an event that will trigger the evaluation of the security rule. In this example, the security rule will be evaluated at each tcp connection establishment attempt. The parameters passed to the evaluation function depend on the event. In the case of _new_connection_ event, _eval_ takes two parameters: _flow_ and _pkt._ The first one holds details about the connection and the latter is a table containing all tcp (and lower layer) packet fields.

In the core of the security rule, we log _(haka.log)_ first some information about the current connection. Then, we check if the source address belongs to the range of non-authorized IP addresses defined previously. If this test succeeds, we raise an alert _(haka.alert)_ and drop the connection. Note that we reported only few details in the alert. One can add more information such as the source and the targeted service.

We use _hakapcap_ tool to test our [rule filter.lua on a pcap trace file filter.pcap](https://thisiscybersec.files.wordpress.com/2015/11/filter.zip):

```
$ hakapcap filter.lua filter.pcap
```

Hereafter, is the output of Haka which dumps some info about loaded dissectors and registered rules. The output shows that Haka succeeded to block connections targeting 192.168.101.62 address:

[![filter](https://thisiscybersec.files.wordpress.com/2015/11/filter.png?w=600&h=306)](https://thisiscybersec.files.wordpress.com/2015/11/filter.png)

In the above example, we have defined a single rule to block connections. One can write a complete firewall-like rule set using the _haka.group_ keyword. In this configuration case, one can choose a default behavior (e.g. block all connections) if none of the security rule belonging to the group explicitly authorizes the traffic.

### Packet Injection

In Haka, one can create new packets and inject them. The following rule crafts an RST packet in order to fool a Xmas nmap scan. As as result, nmap will conclude that all ports are closed on target side.

```
raw = require("protocol/raw")
ipv4 = require("protocol/ipv4")
tcp = require("protocol/tcp")

haka.rule {
 hook = tcp.events.receive_packet,
 eval = function(pkt)
 local flags = pkt.flags
 -- test for xmas nmap scans
 if flags.fin and flags.psh and flags.urg then
 -- raw packet
 local rstpkt = raw.create()

 -- ip packet
 rstpkt = ipv4.create(rstpkt)
 rstpkt.ttl = pkt.ip.ttl
 rstpkt.dst = pkt.ip.src
 rstpkt.src = pkt.ip.dst

 -- tcp packet
 rstpkt = tcp.create(rstpkt)
 rstpkt.srcport = pkt.dstport
 rstpkt.dstport = pkt.srcport
 rstpkt.flags.rst = true
 rstpkt.flags.ack = true
 rstpkt.ack_seq = pkt.seq + 1

 -- inject forged packet and
 -- drop malicious scanning packet
 rstpkt:send()
 pkt:drop()
 end
 end
}
```

### Packet Altering

Packet modification is one of the most advanced feature of Haka. Haka handles automatically all internal modifications at stream and packet level: resizing and fragmenting packets, resetting sequence numbers, etc. The following example shows how easy it is to access and modify protocol fields. This rule alters some headers of http protocol. More precisely, the _user-agent_ header will be modified (or added to the list of headers if not set), and the _accept-encoding_ header will be removed.

```
local http = require("protocol/http")

http.install_tcp_rule(80)

haka.rule{
 hook = http.events.request,
 eval = function (flow, request)
 request.headers["User-Agent"] = "HAKA User Agent"
 request.headers["Accept-Encoding"] = nil
 end
}
```

[blurring-the-web](https://www.paulfariello.fr/assets/blurring-the-web.lua) and [inject_ponies](https://thisiscybersec.files.wordpress.com/2015/11/inject-ponies.zip) are funny scripts that alter http response traffic in order to blur and pollute (inject garbage) requested web pages, respectively:

[![blur](https://thisiscybersec.files.wordpress.com/2015/11/blur.png?w=300&h=160)](https://thisiscybersec.files.wordpress.com/2015/11/blur.png)[![ponies](https://thisiscybersec.files.wordpress.com/2015/11/ponies.png?w=300&h=160)](https://thisiscybersec.files.wordpress.com/2015/11/ponies.png)

### Stream Filtering

Before presenting stream filtering, we will present first how Haka manages packets and streams internally. In Haka, all packets and streams are represented by virtual buffers (see figure below). Virtual buffers are a unified view of non-adjacent blocks of memory. They allow an easy and efficient modification of memory data. Virtual buffers use scattered lists to represent non-contiguous chunks which avoids allocating and copying superfluous block of memory. Haka provides iterators to navigate through these blocks of memory. These iterators could be blocking which would enable some functions to suspend and then resume transparently their execution when more data is available on the stream for instance.

[![vbuffer](https://thisiscybersec.files.wordpress.com/2015/11/vbuffer.png?w=700)](https://thisiscybersec.files.wordpress.com/2015/11/vbuffer.png)The following rule collects http streams and dumps them on stdout. This rule is equivalent to the "follow tcp stream" feature of Wireshark.

```
local ipv4 = require('protocol/ipv4')
local tcp_connection = require('protocol/tcp_connection')

haka.rule{
 hook = tcp_connection.events.receive_data,
 options = {
 streamed = true
 },
 eval = function (flow, iter, dir)
 local data = flow.ccdata or {}
 flow.ccdata = data

 while iter:wait() do
 data[#data+1] = iter:sub('available'):asstring()
 end
 haka.log("%s -> %s:\n", flow.srcip, flow.dstip)
 io.write(table.concat(data))
 end
}
```

### Interactive Packet Filtering

> Wait, it's like gdb but for packets !! – Anonymous Haka user

This is my favorite feature of Haka. It allows inspecting the traffic packet per packet. All the magic starts with the following rule which will prompt a shell for each http POST request.

```
local http = require("protocol/http")

http.install_tcp_rule(80)

haka.rule {
 hook = http.events.request_data,
 eval = function (http, data)
 haka.interactive_rule("interactive mode")(http, data)
 end
}

haka.rule {
 hook = http.events.request,
 eval = function (http, request)
 http:enable_data_modification()
 end
}
```

The shell gives access to the full Haka API to play with packet content: accessing and modifying packet fields, dropping packets, logging suspicious events, alerting, etc. The Lua console supports auto-completion and therefore is a good starting point to dive into the Haka API.

As shown by the following output, Haka breaks on the first POST request. Http data are available through the _inputs_ variable. In this example, we alter the user credentials on the fly.

[![interactive](https://thisiscybersec.files.wordpress.com/2015/11/interactive.png?w=650&h=537)](https://thisiscybersec.files.wordpress.com/2015/11/interactive.png)

Note that it is best to use the interactive rule on pcap files as the edition will add a substantial delay.

### Advanced Stream Filtering

Haka features a pattern matching engine and disassembler modules. These two modules are stream-based which enables us to detect a malicious payload scattered over multiple packets for instance. The following rule, uses a regular expression to detect a nop sled. We enable the streamed option which means that the matching function will block and wait for data to be available to proceed with matching. If a nop sled is detected, we raise an alert and dump the shellcode instruction. Note that the pattern matching function updates the iterator position which points afterwards to the shellcode.

```
local tcp = require("protocol/tcp_connection")

local rem = require("regexp/pcre")
local re = rem.re:compile("%x90{100,}")

local asm = require("misc/asm")
local dasm = asm.new_disassembler("x86", "32")

haka.rule{
 hook = tcp.events.receive_data,
 options = {
 streamed = true,
 },
 eval = function (flow, iter, direction)
 if re:match(iter, false) then
 -- raise an alert
 haka.alert{
 description = "nop sled detected",
 }
 -- dump instructions following nop sled
 dasm:dump_instructions(iter)
 end
 end
}
```

Replaying this rule on the well-known [network forensic challenge](https://www.honeynet.org/node/504) results on the following output. More details about disassembling network traffic into instruction are available [here](http://www.haka-security.org/blog/2015/06/23/instruction-disassembly.html).

[![asm](https://thisiscybersec.files.wordpress.com/2015/11/asm.png?w=600&h=410)](https://thisiscybersec.files.wordpress.com/2015/11/asm.png)

### To be continued ...

### Links

### Like this:

<span class="button">
  <span>Like</span>
</span>

 

<span class="loading">Loading…</span>

[]()

### _Related_
