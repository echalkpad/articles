# Advance Port Scanning with Nmap And Custom Idle Scan - InfoSec Institute

[Original URL](http://resources.infosecinstitute.com/nmap-cheat-sheet-discovery-exploits-part-2-advance-port-scanning-nmap-custom-idle-scan/)

> This is our second installment of Nmap cheat sheet. Basically, we will discuss some advanced techniques for Nmap scanning and we will conduct a Man In The Middle Attack (MITM). Let's start our...

This is our second installment of Nmap cheat sheet. Basically, we will discuss some advanced techniques for Nmap scanning and we will conduct a Man In The Middle Attack (MITM). Let's start our game now.

**TCP SYN Scan**

SYN scan is the default and most popular scan option, for good reasons. It can be performed quickly, scanning thousands of ports per second on a fast network not hampered by restrictive firewalls. It is also relatively unobtrusive and stealthy, since it never completes TCP connections.

**Command: nmap –sS target**

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

**TCP Connect Scan**

TCP connect scan is the default TCP scan type when SYN scan is not an option. This is the case when a user does not have raw packet privileges. Instead of writing raw packets as most other scan types do, Nmap asks the underlying operating system to establish a connection with the target machine and port by issuing the connect system call.

**Command: nmap –sT target**

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

**UDP SCANS**

While most popular services on the Internet run over the TCP protocol, [UDP](http://www.rfc-editor.org/rfc/rfc768.txt) services are widely deployed. DNS, SNMP, and DHCP (registered ports 53, 161/162, and 67/68) are three of the most common. Because UDP scanning is generally slower and more difficult than TCP, some security auditors ignore these ports. This is a mistake, as exploitable UDP services are quite common and attackers certainly don't ignore the whole protocol.

**Command: nmap –sU target**

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

The –data-length option can be used to send a fixed-length random payload to every port or (if you specify a value of 0) to disable payloads. If an ICMP port unreachable error (type 3, code 3) is returned, the port is closed. Other ICMP unreachable errors (type 3, codes 1, 2, 9, 10, or 13) mark the port as filtered. Occasionally, a service will respond with a UDP packet, proving that it is open. If no response is received after retransmissions, the port is classified as open|filtered.

**Command: nmap –sU –data-length=value target**

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

**SCTP INIT Scan**

SCTP is a relatively new alternative to the TCP and UDP protocols, combining most characteristics of TCP and UDP, and also adding new features like multi-homing and multi-streaming. It is mostly being used for SS7/SIGTRAN related services but has the potential to be used for other applications as well. SCTP INIT scan is the SCTP equivalent of a TCP SYN scan. It can be performed quickly, scanning thousands of ports per second on a fast network not hampered by restrictive firewalls. Like SYN scan, INIT scan is relatively unobtrusive and stealthy, since it never completes SCTP associations.

**Command: nmap –sY target**

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

**TCP NULL, FIN, and Xmas scans**

- NULL scan (-sN)

  Does not set any bits (TCP flag header is 0).

- FIN scan (-sF)

  Sets just the TCP FIN bit.

- Xmas scan (-sX)

  Sets the FIN, PSH, and URG flags, lighting the packet up like a Christmas tree.

  ![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

  **TCP ACK Scan**

  This scan is different than the others discussed so far in that it never determines open (or even open|filtered) ports. It is used to map out firewall rulesets, determining whether they are stateful or not, and which ports are filtered.

  **Command: nmap –scanflags=value –sAtarget**

  ![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

  The ACK scan probe packet has only the ACK flag set (unless you use –scanflags). When scanning unfiltered systems, open and closed ports will both return a RST packet. Nmap then labels them as unfiltered, meaning that they are reachable by the ACK packet.

  **TCP Window Scan**

  Window scan is exactly the same as ACK scan, except that it exploits an implementation detail of certain systems to differentiate open ports from closed ones, rather than always printing unfiltered when an RST is returned.

  **Command: nmap –sW target**

  ![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

  **See the valuable and juicy information which is useful for a hacker to attack further:**

  ![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

  **TCP Maimon Scan**

  The Maimon scan is named after its discoverer, Uriel Maimon. He described the technique in _Phrack_ Magazine issue #49 (November 1996). Nmap, which included this technique, was released two issues later. This technique is exactly the same as NULL, FIN, and Xmas scans, except that the probe is FIN/ACK.

  **Command: nmap –sM target**

  ![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

  **Custom TCP Scan Using –scanflag Options**

  For advance pentesting, a pentester will not use a general TCP scan like ACK, FIN, etc. because these things may be blocked by IDS/IPS. So they will use some different techniques by specifying "-scanflag" options. This also can be used for firewall evading.

  The –scanflags argument can be a numerical flag value such as 9 (PSH and FIN), but using symbolic names is easier. Just mash together any combination of URG, ACK, PSH, RST, SYN, and FIN. For example, –scanflags URGACKPSHRSTSYNFIN sets everything, though it's not very useful for scanning.

  **Command: nmap –-scanflags target**

  ![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

  **SCTP COOKIE ECHO Scan**

  SCTP COOKIE ECHO scan is a more advanced SCTP scan. It takes advantage of the fact that SCTP implementations should silently drop packets containing COOKIE ECHO chunks on open ports, but send an ABORT if the port is closed. The advantage of this scan type is that it is not as obvious a port scan as an INIT scan. Also, there may be non-stateful firewall rulesets blocking INIT chunks, but not COOKIE ECHO chunks. A good IDS will be able to detect SCTP COOKIE ECHO scans too. The downside is that SCTP COOKIE ECHO scans cannot differentiate between open and filtered ports, leaving you with the state open|filtered in both cases.

  **Command: nmap –-sZ target**

  ![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

  **TCP Idle Scan**

  This advanced scan method allows for a truly blind TCP port scan of the target (meaning no packets are sent to the target from your real IP address). Instead, a unique side-channel attack exploits predictable IP fragmentation ID sequence generation on the zombie host to glean information about the open ports on the target. IDS systems will display the scan as coming from the zombie machine you specify. This is very useful for conducting MITM (Man In The Middle Attack).

  **Command: nmap –sI zombie target<br>
  ![fig1](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)**

  Victim thought that Zombie was the Attacker machine, which it was actually not. So here the Attacker tried to fool the Victim.

- **Here Zombie means the middle man that you have trusted. Zombie can be any machine which acts like a middle machine between Attacker and Victim.**However, we are in advanced pentesting, so let's try to move ahead with details regarding Idle Scan.![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

  **History And Details**

  In 1998, security researcher Antirez (who also wrote the hping2 tool used in parts of this book) posted to the [Bugtraq](http://www.kyuzz.org/antirez/papers/dumbscan.html) mailing list an ingenious new port scanning technique. He called it "dumb scan". Attackers can actually scan a target without sending a single packet to the target from their own IP address! Instead, a clever side-channel attack allows for the scan to be bounced off a dumb "zombie host". Intrusion detection system (IDS) reports will finger the innocent zombie as the attacker. Besides being extraordinarily stealthy, this scan type permits discovery of IP-based trust relationships between machines.

  What Is the Actual Game?

  Actually, for an attacker to conduct this attack, he does not need to be an expert in TCP/IP, but it is more advanced than other techniques as discussed so far. The below steps are put together to conduct this attack.

- One way to determine whether a TCP port is open is to send a SYN (session establishment) packet to the port. The target machine will respond with a SYN/ACK (session request acknowledgment) packet if the port is open, and RST (reset) if the port is closed. This is the basis of the previously discussed SYN scan.

- A machine that receives an unsolicited SYN/ACK packet will respond with a RST. An unsolicited RST will be ignored.

- Every IP packet on the Internet has a fragment identification number (IP ID). Since many operating systems simply increment this number for each packet they send, probing for the IPID can tell an attacker how many packets have been sent since the last probe.

  By combining these traits, it is possible to scan a target network while forging your identity so that it looks like an innocent zombie machine did the scanning.

  **Idle Scan Explained**

  To conduct this attack, the following steps may be followed for successful exploitation.

- Probe the zombie's IP ID and record it.

- Forge a SYN packet from the zombie and send it to the desired port on the target. Depending on the port state, the target's reaction may or may not cause the zombie's IP ID to be incremented.

- Probe the zombie's IP ID again. The target port state is then determined by comparing this new IP ID with the one recorded in step 1.

  **How to Determine from IP ID**

  From the IP ID value, an attacker will try to learn about port status, whether it is open or filtered or closed. Read below for details:

- After the above idle scan process, the zombie's IP ID should have increased by either one or two. An increase of one indicates that the zombie hasn't sent out any packets, except for its reply to the attacker's probe. This lack of sent packets means that the port is not open (the target must have sent the zombie either a RST packet, which was ignored, or nothing at all).

- An increase of two indicates that the zombie sent out a packet between the two probes. This extra packet usually means that the port is open (the target presumably sent the zombie a SYN/ACK packet in response to the forged SYN, which induced a RST packet from the zombie).

- Increases larger than two usually signify a bad zombie host. It might not have predictable IP ID numbers, or might be engaged in communication unrelated to the idle scan.

See the below images that relate Attacker, Zombie, and Victim, and how the attack is conducted.

**Idle Scan of an Open Port**

**Step 1: Probe The Zombie's IP address**

SYN/ACK

RST IPID=31337

The Attacker sends SYN/ACK packets to Zombie. But Zombie is not expecting to get these packets. So in response, he discloses IP ID value by responding with an RST packet to the attacker.

**Step 2: Forge SYN packet from zombie**

SYN request from Zombie which is spoofed

SYN/ACK to Zombie

RST packet.IP ID:31338

The Victim sends a SYN/ACK packet in response to the SYN packet that appears to come from Zombie. The Zombie sends back RST by incrementing IP ID value.

**Step3: Probe Zombie's IP ID again**

**SYN/ACK**

Zombie

RST,IP ID:31339

The Zombie's IP ID increased by two, which is learned from step one.

So here we know that the port is open from the IPID value.

Note**:** If the port is closed, then the IPID value will be increased by one. If the Zombie's IP ID increased by one as in the first step, we can say that it may be closed or filtered. In the case of filtered, the Victim has no response to Zombie for the SYN request of Attacker.

In this situation, an Attacker will learn that there may be IDS/IPS which have rules to block some certain scan attempts by Zombie machines. For that, he will again use decoy options for Nmap to evade that. We will discuss that later.

**Step 1: Finding Zombie Host for Idle Scan**

The first step in executing an IP ID idle scan is to find an appropriate zombie. It needs to assign IP ID packets incrementally on a global (rather than per-host it communicates with) basis. It should be idle (hence the scan name), as extraneous traffic will bump up its IP ID sequence, confusing the scan logic.

A common approach is to simply execute a Nmap ping scan of some network. We can use Nmap's random IP selection mode (-iR), but that is likely to result in far away zombies with substantial latency.

Performing a port scan and OS identification (-O) on the zombie candidate network, rather than just a ping scan, helps in selecting a good zombie. As long as verbose mode (-v) is enabled, OS detection will usually determine the IP ID sequence generation method and print a line such as "IP ID Sequence Generation: Incremental". If the type is given as Incremental or Broken little-endian incremental, the machine is a good zombie candidate.

**Want to learn more??** The InfoSec Institute [Ethical Hacking course](http://www2.infosecinstitute.com/l/12882/2013-05-28/6g66w)goes in-depth into the techniques used by malicious, black hat hackers with attention getting lectures and **hands-on lab exercises**. While these hacking skills can be used for malicious purposes, this class teaches you how to use the same hacking techniques to perform a white-hat, ethical hack, on your organization. You leave with the ability to quantitatively assess and measure threats to information assets; and discover where your organization is most vulnerable to black hat hackers. Some features of this course include:

- **Dual Certification** - CEH and [CPT](http://www.iacertification.org/cpt_certified_penetration_tester.html)
- 5 days of Intensive **Hands-On Labs**
- Expert Instruction
- CTF exercises in the evening
- Most up-to-date proprietary courseware available

Another approach to identifying zombie candidates is to run the ipidseq NSE script against a host. This script probes a host to classify its IP ID generation method, then prints the IP ID classification, much like the OS detection does.

Command: (nmap –script ipidseq [–script-args probeport=port] target)

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

Now we can tell that it is incremental and a good candidate for Zombie.

**Using Hping**

We can also use hping for discovering a zombie. The hping method for idle scanning provides a lower level example for how idle scanning is performed. In this example, the target host (target1) will be scanned using an idle host (target2). An open and a closed port will be tested to see how each scenario plays out.

First, establish that the idle host is actually idle, send packets using hping2 and observe the ID numbers increase incrementally by one. If the ID numbers increase haphazardly, the host is not actually idle, or has an OS that has no predictable IP ID.

hping3 -S target

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

Send a spoofed SYN packet to the target host on a port you expect to be open.

hping3 –spoof Zombie -S p 22 target

*Note: Here I do not want to include the screenshot. Though this is a part of research and this document is for only educational purposes, the owner of the website does not want to disclose it. If you have any doubt you can contact me here or email me.

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

As you can see, there is no response and it shows 100% packet loss. That means we have failed to find the zombie. Again we will check the following step for confirmation.

Check the IPID value for any increment:

hping3 -S target

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

No response.

It includes that the port is filtered.

**Attack using Nmap**

See the image below. We are attacking the target machine using a zombie host.

**Command: nmap –Pn –p- -sI zombie target**

First we will do an Nmap scan for ports:

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

Based on that, let's try port 22, which is already running.

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

Here we are unable to attack to the target, as it is showing the port is already used for some other purpose. By default, Nmap forges probes to the target from the source port 80 of the zombie. You can choose a different port by appending a colon and port number to the zombie name (e.g. -sI zombie:113). The chosen port must not be filtered from the attacker or the target. A SYN scan of the zombie should show the port in the open or closed state.

Here –Pn: prevents Nmap from sending the initial packets to the target machine.

-p-: will scan all 65535 ports.

-sI: used for idle scan and sending spoof packets.

Here what happens, the attacker's IDS will think that the packet is coming from a zombie machine, not from the target machine. So he will be confused.

**Understanding Nmap Internally**

As a pentester, we must understand internal workings of Nmap's idle scan, so that we will craft the same thing in our own implementation. Even we can write our own code based on Python to do the same thing. We must understand the basic flow or algorithm of Nmap's idle scan. For that, we will use packet trace options in Nmap.

**Command: nmap -sI Zombie:113 -Pn -p20-80,110-180 -r –packet-trace -v target**

<span>-</span>

Pn is necessary for stealth, otherwise pinged packets would be sent to the target from the attacker's real address. Version scanning would also expose the true address, -sV is _not_ specified. The -r option (turns off port randomization) is only used to make this example easier.

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

As I said before, use a suitable zombie port for successful attack.

Process of this attack:

Nmap firsts tests Zombie's IP ID sequence generation by sending six SYN/ACK packets to it and analyzing the responses. Here R means Reset packet. That means that is not reachable through that port, though that is already used for other services. For more details, follow the Idle Scan by Nmap Manual (<http://nmap.org/book/idlescan.html>). Here is a vulnerable machine with a suitable zombie for a successful attack.

So the below mentioned C code is for idle scan. Compile the C program and run the code.

This is an extraordinary scan code that can allow for completely blind scanning (eg. no packets sent to the target from your own IP address) and can also be used to penetrate firewalls and scope out router ACLs.

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

```
#include "idle_scan.h"
#include "timing.h"
#include "osscan2.h"
#include "nmap.h"
#include "NmapOps.h"
#include "services.h"
#include "Target.h"
#include "utils.h"
#include "output.h"

#include "struct_ip.h"

#include

extern NmapOps o;

struct idle_proxy_info { create a constructer and take all variable into it
 Target host; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old contains name, IP, source IP, timing info, etc. */
 int seqclass; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old IP ID sequence class (IPID_SEQ_* defined in nmap.h) */
 u16 latestid; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old The most recent IP ID we have received from the proxy */
 u16 probe_port; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old The port we use for probing IP ID infoz */
 u16 max_groupsz; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old We won't test groups larger than this ... */
 u16 min_groupsz; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old We won't allow the group size to fall below this
         level. Affected by --min-parallelism */
 double current_groupsz; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Current group size being used ... depends on
 conditions ... won't be higher than
 max_groupsz */
 int senddelay; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Delay between sending pr0be SYN packets to target
 (in microseconds) */
 int max_senddelay; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Maximum time we are allowed to wait between
 sending pr0bes (when we send a bunch in a row.
 In microseconds. */

 pcap_t *pd; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old A Pcap descriptor which (starting in
 initialize_idleproxy) listens for TCP packets from
 the probe_port of the proxy box */
 int rawsd; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Socket descriptor for sending probe packets to the proxy */
 struct eth_nfo eth; // For when we want to send probes via raw IP instead.
 struct eth_nfo *ethptr; // points to eth if filled out, otherwise NULL
};

/* Sends an IP ID probe to the proxy machine and returns the IP ID.
 This function handles retransmissions, and returns -1 if it fails.
 Proxy timing is adjusted, but proxy->latestid is NOT ADJUSTED --
 you'll have to do that yourself. Probes_sent is set to the number
 of probe packets sent during execution */
static int ipid_proxy_probe(struct idle_proxy_info *proxy, int *probes_sent,
         int *probes_rcvd) {
 struct timeval tv_end;
 int tries = 0;
 int trynum;
 int sent=0, rcvd=0;
 int maxtries = 3; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old The maximum number of tries before we give up */
 struct timeval tv_sent[3], rcvdtime;
 int ipid = -1;
 int to_usec;
 unsigned int bytes;
 int base_port;
 struct ip *ip;
 struct tcp_hdr *tcp;
 static u32 seq_base = 0;
 static u32 ack = 0;
 static int packet_send_count = 0; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Total # of probes sent by this program -- to ensure that our sequence # always changes */

 if (o.magic_port_set)
 base_port = o.magic_port;
 else base_port = o.magic_port + get_random_u8();

 if (seq_base == 0) seq_base = get_random_u32();
 if (!ack) ack = get_random_u32();

 do {
 gettimeofday(&tv_sent[tries], NULL);

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Time to send the pr0be!*/
 send_tcp_raw(proxy->rawsd, proxy->ethptr,
        proxy->host.v4sourceip(), proxy->host.v4hostip(),
        o.ttl, false,
        o.ipoptions, o.ipoptionslen,
        base_port + tries, proxy->probe_port,
        seq_base + (packet_send_count++ articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii 500) + 1, ack, 0, TH_SYN|TH_ACK, 0, 0,
        (u8 *) "x02x04x05xb4", 4,
        NULL, 0);
 sent++;
 tries++;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Now it is time to wait for the response ... */
 to_usec = proxy->host.to.timeout;
 gettimeofday(&tv_end, NULL);
 while((ipid == -1 || sent > rcvd) && to_usec > 0) {

 to_usec = proxy->host.to.timeout - TIMEVAL_SUBTRACT(tv_end, tv_sent[tries-1]);
 if (to_usec < 0) to_usec = 0; // Final no-block poll ip = (struct ip *) readipv4_pcap(proxy->pd, &bytes, to_usec, &rcvdtime, NULL, true);
 gettimeofday(&tv_end, NULL);
 if (ip) {
    if (bytes < ( 4 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii ip->ip_hl) + 14U)
     continue;

    if (ip->ip_p == IPPROTO_TCP) {

     tcp = ((struct tcp_hdr *) (((char *) ip) + 4 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii ip->ip_hl));
     if (ntohs(tcp->th_dport) < base_port || ntohs(tcp->th_dport) - base_port >= tries || ntohs(tcp->th_sport) != proxy->probe_port || ((tcp->th_flags & TH_RST) == 0)) {
     if (ntohs(tcp->th_dport) > o.magic_port && ntohs(tcp->th_dport) < (o.magic_port + 260)) {   if (o.debugging) {         error("Received IP ID zombie probe response which probably came from an earlier prober instance ... increasing rttvar from %d to %d",        proxy->host.to.rttvar, (int) (proxy->host.to.rttvar articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii 1.2));
     }
     proxy->host.to.rttvar = (int) (proxy->host.to.rttvar articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii 1.2);
     rcvd++;
     }
     else if (o.debugging > 1) {
     error("Received unexpected response packet from %s during IP ID zombie probing:", inet_ntoa(ip->ip_src));
     readtcppacket( (unsigned char *) ip,MIN(ntohs(ip->ip_len), bytes));
     }
     continue;
     }

     trynum = ntohs(tcp->th_dport) - base_port;
     rcvd++;

     ipid = ntohs(ip->ip_id);
     adjust_timeouts2(&(tv_sent[trynum]), &rcvdtime, &(proxy->host.to));
    }
 }
 }
 } while(ipid == -1 && tries < maxtries); if (probes_sent) *probes_sent = sent; if (probes_rcvd) *probes_rcvd = rcvd; return ipid; } /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Returns the number of increments between an early IP ID and a later one, assuming the given IP ID Sequencing class. Returns -1 if the distance cannot be determined _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ playground/ swift/ thesrc/ ucii/ static int ipid_distance(int seqclass , u16 startid, u16 endid) { if (seqclass == IPID_SEQ_INCR) return endid - startid; if (seqclass == IPID_SEQ_BROKEN_INCR) { /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Convert to network byte order _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ playground/ swift/ thesrc/ ucii/ startid = htons(startid); endid = htons(endid); return endid - startid; } return -1; } static void initialize_proxy_struct(struct idle_proxy_info *proxy) { proxy->seqclass = proxy->latestid = proxy->probe_port = 0;
 proxy->max_groupsz = proxy->min_groupsz = 0;
 proxy->current_groupsz = 0;
 proxy->senddelay = 0;
 proxy->max_senddelay = 0;
 proxy->pd = NULL;
 proxy->rawsd = -1;
 proxy->ethptr = NULL;
}

/* takes a proxy name/IP, resolves it if necessary, tests it for IP ID
 suitability, and fills out an idle_proxy_info structure. If the
 proxy is determined to be unsuitable, the function whines and exits
 the program */
#define NUM_IPID_PROBES 6
static void initialize_idleproxy(struct idle_proxy_info *proxy, char *proxyName,
             const struct in_addr *first_target, const struct scan_lists articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii ports) {
 int probes_sent = 0, probes_returned = 0;
 int hardtimeout = 9000000; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Generally don't wait more than 9 secs total */
 unsigned int bytes, to_usec;
 int timedout = 0;
 char *p, *q;
 char *endptr = NULL;
 int seq_response_num;
 int newipid;
 int i;
 char filter[512]; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Libpcap filter string */
 char name[MAXHOSTNAMELEN + 1];
 struct sockaddr_storage ss;
 size_t sslen;
 u32 sequence_base;
 u32 ack = 0;
 struct timeval probe_send_times[NUM_IPID_PROBES], tmptv, rcvdtime;
 u16 lastipid = 0;
 struct ip *ip;
 struct tcp_hdr *tcp;
 int distance;
 int ipids[NUM_IPID_PROBES];
 u8 probe_returned[NUM_IPID_PROBES];
 struct route_nfo rnfo;
 assert(proxy);
 assert(proxyName);

 ack = get_random_u32();

 for(i=0; i < NUM_IPID_PROBES; i++) probe_returned[i] = 0; initialize_proxy_struct(proxy); initialize_timeout_info(&proxy->host.to);

 proxy->max_groupsz = (o.max_parallelism)? o.max_parallelism : 100;
 proxy->min_groupsz = (o.min_parallelism)? o.min_parallelism : 4;
 proxy->max_senddelay = 100000;

 Strncpy(name, proxyName, sizeof(name));
 q = strchr(name, ':');
 if (q) {
 *q++ = '\0';
 proxy->probe_port = strtoul(q, &endptr, 10);
 if (*q==0 || !endptr || *endptr != '\0' || !proxy->probe_port) {
 fatal("Invalid port number given in IP ID zombie specification: %s", proxyName);
 }
 } else {
 if (ports->syn_ping_count > 0) {
 proxy->probe_port = ports->syn_ping_ports[0];
 } else if (ports->ack_ping_count > 0) {
 proxy->probe_port = ports->ack_ping_ports[0];
 } else {
 u16 *ports;
 int count;

 getpts_simple(DEFAULT_TCP_PROBE_PORT_SPEC, SCAN_TCP_PORT, &ports, &count);
 assert(count > 0);
 proxy->probe_port = ports[0];
 free(ports);
 }
 }

 proxy->host.setHostName(name);
 if (resolve(name, 0, 0, &ss, &sslen, o.pf()) == 0) {
 fatal("Could not resolve idle scan zombie host: %s", name);
 }
 proxy->host.setTargetSockAddr(&ss, sslen);

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Lets figure out the appropriate source address to use when sending
 the pr0bez */
 proxy->host.TargetSockAddr(&ss, &sslen);
 if (!nmap_route_dst(&ss, &rnfo))
 fatal("Unable to find appropriate source address and device interface to use when sending packets to %s", proxyName);

 if (o.spoofsource) {
 o.SourceSockAddr(&ss, &sslen);
 proxy->host.setSourceSockAddr(&ss, sslen);
 proxy->host.setDeviceNames(o.device, o.device);
 } else {
 proxy->host.setDeviceNames(rnfo.ii.devname, rnfo.ii.devfullname);
 proxy->host.setSourceSockAddr(&rnfo.srcaddr, sizeof(rnfo.srcaddr));
 }
 if (rnfo.direct_connect) {
 proxy->host.setDirectlyConnected(true);
 } else {
 proxy->host.setDirectlyConnected(false);
 proxy->host.setNextHop(&rnfo.nexthop,
             sizeof(rnfo.nexthop));
 }
 proxy->host.setIfType(rnfo.ii.device_type);
 if (rnfo.ii.device_type == devt_ethernet)
 proxy->host.setSrcMACAddress(rnfo.ii.mac);

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Now lets send some probes to check IP ID algorithm ... */
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old First we need a raw socket ... */
 if ((o.sendpref & PACKET_SEND_ETH) && proxy->host.ifType() == devt_ethernet) {
 if (!setTargetNextHopMAC(&proxy->host))
 fatal("%s: Failed to determine dst MAC address for Idle proxy",
     __func__);
 memcpy(proxy->eth.srcmac, proxy->host.SrcMACAddress(), 6);
 memcpy(proxy->eth.dstmac, proxy->host.NextHopMACAddress(), 6);
 proxy->eth.ethsd = eth_open_cached(proxy->host.deviceName());
 if (proxy->eth.ethsd == NULL)
 fatal("%s: Failed to open ethernet device (%s)", __func__, proxy->host.deviceName());
 proxy->rawsd = -1;
 proxy->ethptr = &proxy->eth;
 } else {
#ifdef WIN32
 win32_fatal_raw_sockets(proxy->host.deviceName());
#endif
 if ((proxy->rawsd = socket(AF_INET, SOCK_RAW, IPPROTO_RAW)) < 0 ) pfatal("socket troubles in %s", __func__); unblock_socket(proxy->rawsd);
 broadcast_socket(proxy->rawsd);
#ifndef WIN32
 sethdrinclude(proxy->rawsd);
#endif
 proxy->eth.ethsd = NULL;
 proxy->ethptr = NULL;
 }

/* Now for the pcap opening nonsense ... */
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Note that the snaplen is 152 = 64 byte max IPhdr + 24 byte max link_layer
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii header + 64 byte max TCP header. */
 if((proxy->pd=my_pcap_open_live(proxy->host.deviceName(), 152, (o.spoofsource)? 1 : 0, 50))==NULL)
 fatal("%s", PCAP_OPEN_ERRMSG);

 p = strdup(proxy->host.targetipstr());
 q = strdup(inet_ntoa(proxy->host.v4source()));
 Snprintf(filter, sizeof(filter), "tcp and src host %s and dst host %s and src port %hu", p, q, proxy->probe_port);
 free(p);
 free(q);
 set_pcap_filter(proxy->host.deviceFullName(), proxy->pd, filter);
 if (o.debugging)
 log_write(LOG_STDOUT, "Packet capture filter (device %s): %sn", proxy->host.deviceFullName(), filter);
/* Windows nonsense -- I am not sure why this is needed, but I should
 get rid of it at sometime */

 sequence_base = get_random_u32();

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Yahoo! It is finally time to send our pr0beZ! */

 while(probes_sent < NUM_IPID_PROBES) { if (o.scan_delay) enforce_scan_delay(NULL); else if (probes_sent) usleep(30000); /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old TH_SYN|TH_ACK is what the proxy will really be receiving from the target, and is more likely to get through firewalls. But TH_SYN allows us to get a nonzero ACK back so we can associate a response with the exact request for timing purposes. So I think I'll use TH_SYN, although it is a tough call. _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ playground/ swift/ thesrc/ ucii/ /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old We can't use decoys 'cause that would screw up the IP IDs _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ playground/ swift/ thesrc/ ucii/ send_tcp_raw(proxy->rawsd, proxy->ethptr,
        proxy->host.v4sourceip(), proxy->host.v4hostip(),
        o.ttl, false,
        o.ipoptions, o.ipoptionslen,
         o.magic_port + probes_sent + 1, proxy->probe_port,
        sequence_base + probes_sent + 1, ack, 0, TH_SYN|TH_ACK, 0, 0,
        (u8 *) "x02x04x05xb4",4,
        NULL, 0);
 gettimeofday(&probe_send_times[probes_sent], NULL);
 probes_sent++;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Time to collect any replies */
 while(probes_returned < probes_sent && !timedout) { to_usec = (probes_sent == NUM_IPID_PROBES)? hardtimeout : 1000; ip = (struct ip *) readipv4_pcap(proxy->pd, &bytes, to_usec, &rcvdtime, NULL, true);

 gettimeofday(&tmptv, NULL);

 if (!ip) {
    if (probes_sent < NUM_IPID_PROBES)    break;     if (TIMEVAL_SUBTRACT(tmptv, probe_send_times[probes_sent - 1]) >= hardtimeout) {
     timedout = 1;
    }
    continue;
 } else if (TIMEVAL_SUBTRACT(tmptv, probe_send_times[probes_sent - 1]) >=
         hardtimeout) {
    timedout = 1;
 }

 if (lastipid != 0 && ip->ip_id == lastipid) {
    continue; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old probably a duplicate */
 }
 lastipid = ip->ip_id;

 if (bytes < ( 4 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii ip->ip_hl) + 14U)
    continue;

 if (ip->ip_p == IPPROTO_TCP) {
    tcp = ((struct tcp_hdr *) (((char *) ip) + 4 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii ip->ip_hl));
    if (ntohs(tcp->th_dport) < (o.magic_port+1) || ntohs(tcp->th_dport) - o.magic_port > NUM_IPID_PROBES || ntohs(tcp->th_sport) != proxy->probe_port || ((tcp->th_flags & TH_RST) == 0)) {
     if (o.debugging > 1) error("Received unexpected response packet from %s during initial IP ID zombie testing", inet_ntoa(ip->ip_src));
     continue;
    }

    seq_response_num = probes_returned;

    /* The stuff below only works when we send SYN packets instead of
     SYN|ACK, but then are slightly less stealthy and have less chance
     of sneaking through the firewall. Plus SYN|ACK is what they will
     be receiving back from the target */
    probes_returned++;
    ipids[seq_response_num] = (u16) ntohs(ip->ip_id);
    probe_returned[seq_response_num] = 1;
    adjust_timeouts2(&probe_send_times[seq_response_num], &rcvdtime, &(proxy->host.to));
 }
 }
 }

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Yeah! We're done sending/receiving probes ... now lets ensure all of our responses are adjacent in the array */
 for(i=0,probes_returned=0; i < NUM_IPID_PROBES; i++) { if (probe_returned[i]) { if (i > probes_returned)
    ipids[probes_returned] = ipids[i];
 probes_returned++;
 }
 }

 if (probes_returned == 0)
 fatal("Idle scan zombie %s (%s) port %hu cannot be used because it has not returned any of our probes -- perhaps it is down or firewalled.",
     proxy->host.HostName(), proxy->host.targetipstr(),
     proxy->probe_port);

 proxy->seqclass = get_ipid_sequence(probes_returned, ipids, 0);
 switch(proxy->seqclass) {
 case IPID_SEQ_INCR:
 case IPID_SEQ_BROKEN_INCR:
 log_write(LOG_PLAIN, "Idle scan using zombie %s (%s:%hu); Class: %sn", proxy->host.HostName(), proxy->host.targetipstr(), proxy->probe_port, ipidclass2ascii(proxy->seqclass));
 break;
 default:
 fatal("Idle scan zombie %s (%s) port %hu cannot be used because IP ID sequencability class is: %s. Try another proxy.", proxy->host.HostName(), proxy->host.targetipstr(), proxy->probe_port, ipidclass2ascii(proxy->seqclass));
 }

 proxy->latestid = ipids[probes_returned - 1];
 proxy->current_groupsz = MIN(proxy->max_groupsz, 30);

 if (probes_returned < NUM_IPID_PROBES) { /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Yikes! We're already losing packets ... clamp down a bit ... _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ playground/ swift/ thesrc/ ucii/ if (o.debugging) error("Idle scan initial zombie qualification test: %d probes sent, only %d returned", NUM_IPID_PROBES, probes_returned); proxy->current_groupsz = MIN(12, proxy->max_groupsz);
 proxy->current_groupsz = MAX(proxy->current_groupsz, proxy->min_groupsz);
 proxy->senddelay += 5000;
 }

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old OK, through experimentation I have found that some hosts (*cough*
 Solaris) APPEAR to use simple IP ID incrementing, but in reality they
 assign a new IP ID base to each host which connects with them. This
 is actually a good idea on several fronts, but it totally
 frustrates our efforts (which rely on side-channel IP ID info
 leaking to different hosts). The good news is that we can easily
 detect the problem by sending some spoofed packets "from" the first
 target to the zombie and then probing to verify that the proxy IP ID
 changed. This will also catch the case where the Nmap user is
 behind an egress filter or other measure that prevents this sort of
 sp00fery */
 if (first_target) {
 for (probes_sent = 0; probes_sent < 4; probes_sent++) { if (probes_sent) usleep(50000); send_tcp_raw(proxy->rawsd, proxy->ethptr,
        first_target, proxy->host.v4hostip(),
        o.ttl, false,
        o.ipoptions, o.ipoptionslen,
        o.magic_port, proxy->probe_port,
        sequence_base + probes_sent + 1, ack, 0, TH_SYN|TH_ACK, 0, 0,
        (u8 *) "x02x04x05xb4",
        4, NULL, 0);

 }

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Sleep a little while to give packets time to reach their destination */
 usleep(300000);
 newipid = ipid_proxy_probe(proxy, NULL, NULL);
 if (newipid == -1)
 newipid = ipid_proxy_probe(proxy, NULL, NULL); /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old OK, we'll give it one more try */

 if (newipid < 0) fatal("Your IP ID Zombie (%s; %s) is behaving strangely -- suddenly cannot obtain IP ID", proxy->host.HostName(), proxy->host.targetipstr());

 distance = ipid_distance(proxy->seqclass, proxy->latestid, newipid);
 if (distance <= 0) { fatal("Your IP ID Zombie (%s; %s) is behaving strangely -- suddenly cannot obtain valid IP ID distance.", proxy->host.HostName(), proxy->host.targetipstr());
 } else if (distance == 1) {
 fatal("Even though your Zombie (%s; %s) appears to be vulnerable to IP ID sequence prediction (class: %s), our attempts have failed. This generally means that either the zombie uses a separate IP ID base for each host (like Solaris), or because you cannot spoof IP packets (perhaps your ISP has enabled egress filtering to prevent IP spoofing), or maybe the target network recognizes the packet source as bogus and drops them", proxy->host.HostName(), proxy->host.targetipstr(), ipidclass2ascii(proxy->seqclass));
 }
 if (o.debugging && distance != 5) {
 error("WARNING: IP ID spoofing test sent 4 packets and expected a distance of 5, but instead got %d", distance);
 }
 proxy->latestid = newipid;
 }

}

/* Adjust timing parameters up or down given that an idle scan found a
 count of 'testcount' while the 'realcount' is as given. If the
 testcount was correct, timing is made more aggressive, while it is
 slowed down in the case of an error */
static void adjust_idle_timing(struct idle_proxy_info *proxy,
            Target *target, int testcount,
            int realcount) {

 static int notidlewarning = 0;

 if (o.debugging > 1)
 log_write(LOG_STDOUT,
     "%s: tested/true %d/%d -- old grpsz/delay: %f/%d ",
     __func__, testcount, realcount, proxy->current_groupsz, proxy->senddelay);
 else if (o.debugging && testcount != realcount) {
 error("%s: testcount: %d realcount: %d -- old grpsz/delay: %f/%d", __func__, testcount, realcount, proxy->current_groupsz, proxy->senddelay);
 }

 if (testcount < realcount) { /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old We must have missed a port -- our probe could have been    dropped, the response to proxy could have been dropped, or we   didn't wait long enough before probing the proxy IP ID. The     third case is covered elsewhere in the scan, so we worry most   about the first two. The solution is to decrease our group      size and add a sending delay _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ playground/ swift/ thesrc/ ucii/ /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old packets could be dropped because too many were sent at once _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ playground/ swift/ thesrc/ ucii/ proxy->current_groupsz = MAX(proxy->min_groupsz, proxy->current_groupsz articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii 0.8);
 proxy->senddelay += 10000;
 proxy->senddelay = MIN(proxy->max_senddelay, proxy->senddelay);
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old No group size should be greater than .5s of send delays */
 proxy->current_groupsz = MAX(proxy->min_groupsz, MIN(proxy->current_groupsz, 500000 / (proxy->senddelay + 1)));

 } else if (testcount > realcount) {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Perhaps the proxy host is not really idle ... */
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old I guess all I can do is decrease the group size, so that if the proxy is not really idle, at least we may be able to scan chunks more quickly in between outside packets */
 proxy->current_groupsz = MAX(proxy->min_groupsz, proxy->current_groupsz articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii 0.8);

 if (!notidlewarning && o.verbose) {
    notidlewarning = 1;
    error("WARNING: idle scan has erroneously detected phantom ports -- is the proxy %s (%s) really idle?", proxy->host.HostName(), proxy->host.targetipstr());
 }
 } else {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old W00p We got a perfect match. That means we get a slight increase
     in allowed group size and we can lightly decrease the senddelay */

 proxy->senddelay = (int) (proxy->senddelay articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii 0.9);
 if (proxy->senddelay < 500) proxy->senddelay = 0;
 proxy->current_groupsz = MIN(proxy->current_groupsz articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii 1.1, 500000 / (proxy->senddelay + 1));
 proxy->current_groupsz = MIN(proxy->max_groupsz, proxy->current_groupsz);

 }
 if (o.debugging > 1)
 log_write(LOG_STDOUT, "-> %f/%dn", proxy->current_groupsz, proxy->senddelay);
}

/* OK, now this is the hardcore idle scan function which actually does
 the testing (most of the other cruft in this file is just
 coordination, preparation, etc). This function simply uses the
 idle scan technique to try and count the number of open ports in the
 given port array. The sent_time and rcv_time are filled in with
 the times that the probe packet & response were sent/received.
 They can be NULL if you don't want to use them. The purpose is for
 timing adjustments if the numbers turn out to be accurate. */

static int idlescan_countopen2(struct idle_proxy_info *proxy,
            Target *target, u16 *ports, int numports,
            struct timeval *sent_time, struct timeval *rcv_time)
{

#if 0 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Testing code */
 int i;
 for(i=0; i < numports; i++) if (ports[i] == 22) return 1; return 0; #endif int openports; int tries; int proxyprobes_sent = 0; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old diff. from tries 'cause sometimes we             skip tries _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ playground/ swift/ thesrc/ ucii/ int proxyprobes_rcvd = 0; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old To determine if packets were dr0pped _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ playground/ swift/ thesrc/ ucii/ int sent, rcvd; int ipid_dist; struct timeval start, end, latestchange, now; struct timeval probe_times[4]; int pr0be; static u32 seq = 0; int newipid = 0; int sleeptime; int lasttry = 0; int dotry3 = 0; struct eth_nfo eth; if (seq == 0) seq = get_random_u32(); memset(&end, 0, sizeof(end)); memset(&latestchange, 0, sizeof(latestchange)); gettimeofday(&start, NULL); if (sent_time) memset(sent_time, 0, sizeof(*sent_time)); if (rcv_time) memset(rcv_time, 0, sizeof(*rcv_time)); if (proxy->rawsd < 0) { if (!setTargetNextHopMAC(target)) fatal("%s: Failed to determine dst MAC address for Idle proxy",     __func__); memcpy(eth.srcmac, target->SrcMACAddress(), 6);
 memcpy(eth.dstmac, target->NextHopMACAddress(), 6);
 eth.ethsd = eth_open_cached(target->deviceName());
 if (eth.ethsd == NULL)
 fatal("%s: Failed to open ethernet device (%s)", __func__, target->deviceName());
 } else eth.ethsd = NULL;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old I start by sending out the SYN pr0bez */
 for(pr0be = 0; pr0be < numports; pr0be++) { if (o.scan_delay) enforce_scan_delay(NULL); else if (proxy->senddelay && pr0be > 0) usleep(proxy->senddelay);

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Maybe I should involve decoys in the picture at some point --
 but doing it the straightforward way (using the same decoys as
 we use in probing the proxy box is risky. I'll have to think
 about this more. */
 send_tcp_raw(proxy->rawsd, eth.ethsd? ð : NULL,
        proxy->host.v4hostip(), target->v4hostip(),
        o.ttl, false,
        o.ipoptions, o.ipoptionslen,
        proxy->probe_port, ports[pr0be], seq, 0, 0, TH_SYN, 0, 0,
        (u8 *) "x02x04x05xb4", 4,
        o.extra_payload, o.extra_payload_length);
 }
 gettimeofday(&end, NULL);

 openports = -1;
 tries = 0;
 TIMEVAL_MSEC_ADD(probe_times[0], start, MAX(50, (target->to.srtt articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii 3/4) / 1000));
 TIMEVAL_MSEC_ADD(probe_times[1], start, target->to.srtt / 1000 );
 TIMEVAL_MSEC_ADD(probe_times[2], end, MAX(75, (2 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii target->to.srtt +
                         target->to.rttvar) / 1000));
 TIMEVAL_MSEC_ADD(probe_times[3], end, MIN(4000, (2 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii target->to.srtt +
                         (target->to.rttvar << 2 )) / 1000)); do { if (tries == 2) dotry3 = (get_random_u8() > 200);
 if (tries == 3 && !dotry3)
 break; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old We usually want to skip the long-wait test */
 if (tries == 3 || (tries == 2 && !dotry3))
 lasttry = 1;

 gettimeofday(&now, NULL);
 sleeptime = TIMEVAL_SUBTRACT(probe_times[tries], now);
 if (!lasttry && proxyprobes_sent > 0 && sleeptime < 50000)
 continue; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old No point going again so soon */

 if (tries == 0 && sleeptime < 500) sleeptime = 500; if (o.debugging > 1) error("In preparation for idle scan probe try #%d, sleeping for %d usecs", tries, sleeptime);
 if (sleeptime > 0)
 usleep(sleeptime);

 newipid = ipid_proxy_probe(proxy, &sent, &rcvd);
 proxyprobes_sent += sent;
 proxyprobes_rcvd += rcvd;

 if (newipid > 0) {
 ipid_dist = ipid_distance(proxy->seqclass, proxy->latestid, newipid);
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old I used to only do this if ipid_sit >= proxyprobes_sent, but I'd
     rather have a negative number in that case. */
 if (ipid_dist < proxyprobes_sent) {     if (o.debugging) error("%s: Must have lost a sent packet because ipid_dist is %d while proxyprobes_sent is %d.", __func__, ipid_dist, proxyprobes_sent);    /* I no longer whack timing here ... done at bottom. _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ playground/ swift/ thesrc/ ucii/ } ipid_dist -= proxyprobes_sent; if (ipid_dist > openports) {
    openports = ipid_dist;
    gettimeofday(&latestchange, NULL);
 } else if (ipid_dist < openports && ipid_dist >= 0) {
    /* Uh-oh. Perhaps I dropped a packet this time */
    if (o.debugging > 1) {
     error("%s: Counted %d open ports in try #%d, but counted %d earlier ... probably a proxy_probe problem", __func__, ipid_dist, tries, openports);
    }
    /* I no longer whack timing here ... done at bottom. */
 }
 }

 if (openports > numports || (numports <= 2 && (openports == numports)))
 break;
 } while(tries++ < 3); if (proxyprobes_sent > proxyprobes_rcvd) {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Uh-oh. It looks like we lost at least one proxy probe packet */
 if (o.debugging) {
 error("%s: Sent %d probes; only %d responses. Slowing scan.", __func__, proxyprobes_sent, proxyprobes_rcvd);
 }
 proxy->senddelay += 5000;
 proxy->senddelay = MIN(proxy->max_senddelay, proxy->senddelay);
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old No group size should be greater than .5s of send delays */
 proxy->current_groupsz = MAX(proxy->min_groupsz, MIN(proxy->current_groupsz, 500000 / (proxy->senddelay+1)));
 } else {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Yeah, we got as many responses as we sent probes. This calls for a
 very light timing acceleration ... */
 proxy->senddelay = (int) (proxy->senddelay articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii 0.95);
 if (proxy->senddelay < 500) proxy->senddelay = 0;
 proxy->current_groupsz = MAX(proxy->min_groupsz, MIN(proxy->current_groupsz, 500000 / (proxy->senddelay+1)));
 }

 if ((openports > 0) && (openports <= numports)) { /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Yeah, we found open ports... lets adjust the timing ... _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ playground/ swift/ thesrc/ ucii/ if (o.debugging > 2) error("%s: found %d open ports (out of %d) in %lu usecs", __func__, openports, numports, (unsigned long) TIMEVAL_SUBTRACT(latestchange, start));
 if (sent_time) *sent_time = start;
 if (rcv_time) *rcv_time = latestchange;
 }
 if (newipid > 0) proxy->latestid = newipid;
 if (eth.ethsd) { eth.ethsd = NULL; } /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old don't need to close it due to caching */
 return openports;
}

/* The job of this function is to use the idle scan technique to count
 the number of open ports in the given list. Under the covers, this
 function just farms out the hard work to another function. */
static int idlescan_countopen(struct idle_proxy_info *proxy,
         Target *target, u16 *ports, int numports,
         struct timeval *sent_time, struct timeval *rcv_time) {
 int tries = 0;
 int openports;

 do {
 openports = idlescan_countopen2(proxy, target, ports, numports, sent_time,
                 rcv_time);
 tries++;
 if (tries == 6 || (openports >= 0 && openports <= numports)) break; if (o.debugging) { error("%s: In try #%d, counted %d open ports out of %d. Retrying", __func__, tries, openports, numports); } /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Sleep for a little while -- maybe proxy host had brief birst of traffic or similar problem _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ playground/ swift/ thesrc/ ucii/ sleep(tries articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii tries); if (tries == 5) sleep(45); /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old We're gonna give up if this fails, so we will be a bit       patient _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ playground/ swift/ thesrc/ ucii/ /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Since the host may have received packets while we were sleeping, lets update our proxy IP ID counter _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ playground/ swift/ thesrc/ ucii/ proxy->latestid = ipid_proxy_probe(proxy, NULL, NULL);
 } while(1);

 if (openports < 0 || openports > numports ) {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Oh f*ck!!!! */
 fatal("Idle scan is unable to obtain meaningful results from proxy %s (%s). I'm sorry it didn't work out.", proxy->host.HostName(),
     proxy->host.targetipstr());
 }

 if (o.debugging > 2) error("%s: %d ports found open out of %d, starting with %hu", __func__, openports, numports, ports[0]);

 return openports;
}

/* Recursively idle scans scans a group of ports using a depth-first
 divide-and-conquer strategy to find the open one(s). */

static int idle_treescan(struct idle_proxy_info *proxy, Target *target,
         u16 *ports, int numports, int expectedopen) {

 int firstHalfSz = (numports + 1)/2;
 int secondHalfSz = numports - firstHalfSz;
 int flatcount1, flatcount2;
 int deepcount1 = -1, deepcount2 = -1;
 struct timeval sentTime1, rcvTime1, sentTime2, rcvTime2;
 int retrycount = -1, retry2 = -1;
 int totalfound = 0;
 /bin /boot /dev /etc /git /home /initrd.img /initrd.i
```
mg.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Scan the first half of the range */

```
 if (o.debugging > 1) {
 error("%s: Called against %s with %d ports, starting with %hu. expectedopen: %d", __func__, target->targetipstr(), numports, ports[0], expectedopen);
 error("IDLE SCAN TIMING: grpsz: %.3f delay: %d srtt: %d rttvar: %d",
     proxy->current_groupsz, proxy->senddelay, target->to.srtt,
     target->to.rttvar);
 }

 flatcount1 = idlescan_countopen(proxy, target, ports, firstHalfSz,
                 &sentTime1, &rcvTime1);

 if (firstHalfSz > 1 && flatcount1 > 0) {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old A port appears open! We dig down deeper to find it ... */
 deepcount1 = idle_treescan(proxy, target, ports, firstHalfSz, flatcount1);
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Now we assume deepcount1 is right, and adjust timing if flatcount1 was
 wrong. */
 adjust_idle_timing(proxy, target, flatcount1, deepcount1);
 }

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old I guess we had better do the second half too ... */

 flatcount2 = idlescan_countopen(proxy, target, ports + firstHalfSz,
                 secondHalfSz, &sentTime2, &rcvTime2);

 if ((secondHalfSz) > 1 && flatcount2 > 0) {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old A port appears open! We dig down deeper to find it ... */
 deepcount2 = idle_treescan(proxy, target, ports + firstHalfSz,
             secondHalfSz, flatcount2);
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Now we assume deepcount1 is right, and adjust timing if flatcount1 was
 wrong */
 adjust_idle_timing(proxy, target, flatcount2, deepcount2);
 }

 totalfound = (deepcount1 == -1)? flatcount1 : deepcount1;
 totalfound += (deepcount2 == -1)? flatcount2 : deepcount2;

 if ((flatcount1 + flatcount2 == totalfound) &&
 (expectedopen == totalfound || expectedopen == -1)) {

 if (flatcount1 > 0) {
 if (o.debugging > 1) {
    error("Adjusting timing -- idlescan_countopen correctly found %d open ports (out of %d, starting with %hu)", flatcount1, firstHalfSz, ports[0]);
 }
 adjust_timeouts2(&sentTime1, &rcvTime1, &(target->to));
 }

 if (flatcount2 > 0) {
 if (o.debugging > 2) {
    error("Adjusting timing -- idlescan_countopen correctly found %d open ports (out of %d, starting with %hu)", flatcount2, secondHalfSz,
     ports[firstHalfSz]);
 }
 adjust_timeouts2(&sentTime2, &rcvTime2, &(target->to));
 }
 }

 if (totalfound != expectedopen) {
 if (deepcount1 == -1) {
 retrycount = idlescan_countopen(proxy, target, ports, firstHalfSz, NULL,
                 NULL);
 if (retrycount != flatcount1) {
    /* We have to do a deep count if new ports were found and
     there are more than 1 total */
    if (firstHalfSz > 1 && retrycount > 0) {
     retry2 = retrycount;
     retrycount = idle_treescan(proxy, target, ports, firstHalfSz,
                 retrycount);
     adjust_idle_timing(proxy, target, retry2, retrycount);
    } else {
     if (o.debugging)
     error("Adjusting timing because my first scan of %d ports, starting with %hu found %d open, while second scan yielded %d", firstHalfSz, ports[0], flatcount1, retrycount);
     adjust_idle_timing(proxy, target, flatcount1, retrycount);
    }
    totalfound += retrycount - flatcount1;
    flatcount1 = retrycount;

    /* If our first count erroneously found and added an open port,
     we must delete it */
    if (firstHalfSz == 1 && flatcount1 == 1 && retrycount == 0)
     target->ports.forgetPort(ports[0], IPPROTO_TCP);

 }
 }

 if (deepcount2 == -1) {
 retrycount = idlescan_countopen(proxy, target, ports + firstHalfSz,
                 secondHalfSz, NULL, NULL);
 if (retrycount != flatcount2) {
    if (secondHalfSz > 1 && retrycount > 0) {
     retry2 = retrycount;
     retrycount = idle_treescan(proxy, target, ports + firstHalfSz,
                 secondHalfSz, retrycount);
     adjust_idle_timing(proxy, target, retry2, retrycount);
    } else {
     if (o.debugging)
     error("Adjusting timing because my first scan of %d ports, starting with %hu found %d open, while second scan yeilded %d", secondHalfSz, ports[firstHalfSz], flatcount2, retrycount);
     adjust_idle_timing(proxy, target, flatcount2, retrycount);
    }

    totalfound += retrycount - flatcount2;
    flatcount2 = retrycount;

    /* If our first count erroneously found and added an open port,
     we must delete it. */
    if (secondHalfSz == 1 && flatcount2 == 1 && retrycount == 0)
     target->ports.forgetPort(ports[firstHalfSz], IPPROTO_TCP);

 }
 }
 }

 if (firstHalfSz == 1 && flatcount1 == 1)
 target->ports.setPortState(ports[0], IPPROTO_TCP, PORT_OPEN);

 if ((secondHalfSz == 1) && flatcount2 == 1)
 target->ports.setPortState(ports[firstHalfSz], IPPROTO_TCP, PORT_OPEN);
 return totalfound;

}

/* The very top-level idle scan function -- scans the given target
 host using the given proxy -- the proxy is cached so that you can keep
 calling this function with different targets. */
void idle_scan(Target *target, u16 *portarray, int numports,
     char *proxyName, const struct scan_lists articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii ports) {

 static char lastproxy[MAXHOSTNAMELEN + 1] = ""; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old The proxy used in any previous call */
 static struct idle_proxy_info proxy;
 int groupsz;
 int portidx = 0; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Used for splitting the port array into chunks */
 int portsleft;
 char scanname[128];
 Snprintf(scanname, sizeof(scanname), "idle scan against %s", target->NameIP());
 ScanProgressMeter SPM(scanname);

 if (numports == 0) return; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old nothing to scan for */
 if (!proxyName) fatal("idle scan requires a proxy host");

 if (*lastproxy && strcmp(proxyName, lastproxy))
 fatal("%s: You are not allowed to change proxies midstream. Sorry", __func__);
 assert(target);

 if (target->timedOut(NULL))
 return;

 if (target->ifType() == devt_loopback) {
 log_write(LOG_STDOUT, "Skipping Idle Scan against %s -- you can't idle scan your own machine (localhost).n", target->NameIP());
 return;
 }

 target->startTimeOutClock(NULL);

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old If this is the first call, */
 if (!*lastproxy) {
 initialize_idleproxy(&proxy, proxyName, target->v4hostip(), ports);
 strncpy(lastproxy, proxyName, sizeof(lastproxy));
 }

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old If we don't have timing infoz for the new target, we'll use values
 derived from the proxy */
 if (target->to.srtt == -1 && target->to.rttvar == -1) {
 target->to.srtt = MAX(200000,2 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii proxy.host.to.srtt);
 target->to.rttvar = MAX(10000, MIN(proxy.host.to.rttvar, 2000000));
 target->to.timeout = target->to.srtt + (target->to.rttvar << 2); } else { target->to.srtt = MAX(target->to.srtt, proxy.host.to.srtt);
 target->to.rttvar = MAX(target->to.rttvar, proxy.host.to.rttvar);
 target->to.timeout = target->to.srtt + (target->to.rttvar << 2);
 }

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Now I guess it is time to let the scanning begin! Since idle
 scan is sort of tree structured (we scan a group and then divide
 it up and drill down in subscans of the group), we split the port
 space into smaller groups and then call a recursive
 divide-and-counquer function to find the open ports */
 while(portidx < numports) {
 portsleft = numports - portidx;
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old current_groupsz is doubled below because idle_subscan cuts in half */
 groupsz = MIN(portsleft, (int) (proxy.current_groupsz articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii 2));
 idle_treescan(&proxy, target, portarray + portidx, groupsz, -1);
 portidx += groupsz;
 }

 char additional_info[14];
 Snprintf(additional_info, sizeof(additional_info), "%d ports", numports);
 SPM.endTask(NULL, additional_info);

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Now we go through the ports which were scanned but not determined
 to be open, and add them in the "closed|filtered" state */
 for(portidx = 0; portidx < numports; portidx++) { if (target->ports.portIsDefault(portarray[portidx], IPPROTO_TCP)) {
 target->ports.setPortState(portarray[portidx], IPPROTO_TCP, PORT_CLOSEDFILTERED);
 target->ports.setStateReason(portarray[portidx], IPPROTO_TCP, ER_NOIPIDCHANGE, 0, NULL);
 } else
 target->ports.setStateReason(portarray[portidx], IPPROTO_TCP, ER_IPIDCHANGE, 0, NULL);
 }

 target->stopTimeOutClock(NULL);
 return;
}
```

As you can see, this is my screenshot of the C++ source file. Just compile it and run it to get the results.

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

OK, let's start with our original scan method.

**IP Protocol Scan**

IP protocol scan allows you to determine which IP protocols (TCP, ICMP, IGMP, etc.) are supported by target machines. This isn't technically a port scan, since it cycles through IP protocol numbers rather than TCP or UDP port numbers.

**Command: nmap –sO target**

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/plugins/jquery-image-lazy-loading/images/grey.gif)

**FTP Bounce Scan**

This allows a user to connect to one FTP server, then ask that files be sent to a third-party server. Simply it asks the FTP server to send a file to each interesting port of a target host in turn. The error message will describe whether the port is open or not. This is a good way to bypass firewalls, because organizational FTP servers are often placed where they have more access to other internal hosts than any old Internet host would. It takes an argument of the form <username>:<password>@<server>:<port>. <Server> is the name or IP address of a vulnerable FTP server.

**Command: nmap –b ftp rely host.** Also, this can be used for port bounce attack.

**nmap -T0 -b username:password@ftpserver.tld:21 victim.tld**

This uses the username "username", the password "password", the FTP server "ftpserver.tld" and port 21 on said server to scan victim.tld.

If the FTP server supports anonymous logins, just forget about the username:password@ part and Nmap will assume it allows -anonymous. You may omit :21 if the FTP port is 21, however, some people configure FTP on weird ports as an attempt at "security".

**Port Specification and Scan Order**

In addition to all of the scan methods discussed previously, Nmap offers options for specifying which ports are scanned and whether the scan order is randomized or sequential. By default, Nmap scans the most common 1,000 ports for each protocol.

-p <port ranges> (Only scan specified ports)

This option specifies which ports you want to scan and overrides the default. Individual port numbers are OK, as are ranges separated by a hyphen (e.g. 1-1023). The beginning and/or end values of a range may be omitted, causing Nmap to use 1 and 65535, respectively. So you can specify -p- to scan ports from 1 through 65535\. Scanning port zero is allowed if you specify it explicitly.

Nmap –p 1-1023 target

When scanning a combination of protocols (e.g. TCP and UDP), you can specify a particular protocol by preceding the port numbers by T: for TCP, U: for UDP, S: for SCTP, or P: for IP Protocol.

nmap -p U:53,111,137,T:21-25,80,139,8080 target

**Want to learn more??** The InfoSec Institute [Ethical Hacking course](http://www2.infosecinstitute.com/l/12882/2013-05-28/6g66w)goes in-depth into the techniques used by malicious, black hat hackers with attention getting lectures and **hands-on lab exercises**. While these hacking skills can be used for malicious purposes, this class teaches you how to use the same hacking techniques to perform a white-hat, ethical hack, on your organization. You leave with the ability to quantitatively assess and measure threats to information assets; and discover where your organization is most vulnerable to black hat hackers. Some features of this course include:

- **Dual Certification** - CEH and [CPT](http://www.iacertification.org/cpt_certified_penetration_tester.html)
- 5 days of Intensive **Hands-On Labs**
- Expert Instruction
- CTF exercises in the evening
- Most up-to-date proprietary courseware available

**-F (Fast (limited port) scan)**

Specifies that you wish to scan fewer ports than the default. Normally Nmap scans the most common 1,000 ports for each scanned protocol. With -F, this is reduced to 100.

**nmap –F target**

**-r (Don't randomize ports)**

By default, Nmap randomizes the scanned port order (except that certain commonly accessible ports are moved near the beginning for efficiency reasons). This randomization is normally desirable, but you can specify -r for sequential (sorted from lowest to highest) port scanning instead.

nmap –r target

So this is the end of this part of the series. In the next part, I will go through advanced firewall evasion and custom creation of exploits with Nmap.

**Warning: The above mentioned malicious attack conducted on the lab has been given prior permission by the owner of website or admin. The above is meant for only educational purposes. So do not use for any personal intent, as it is prone to cyber attack.**

**References:**

<http://www.kyuzz.org/antirez/papers/dumbscan.html>

<http://www.kyuzz.org/antirez/papers/moreipid.html>

<http://en.wikipedia.org/wiki/Idle_scan>
