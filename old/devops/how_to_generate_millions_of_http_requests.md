# How to Generate Millions of HTTP Requests

[Original URL](http://dak1n1.com/blog/14-http-load-generate)

> Created: 21 May 2012 Written by dakini Hits: 32881 This article is part 1 of a series about building a high-performance web cluster powerful enough to handle 3 million requests per second. This first...

- [![Print](http://dak1n1.com/media/system/images/printButton.png)](http://dak1n1.com/blog/14-http-load-generate?tmpl=component&print=1&layout=default&page= "Print")
- [![Email](http://dak1n1.com/media/system/images/emailButton.png)](http://dak1n1.com/component/mailto/?tmpl=component&template=rt_paradox_j16&link=9ee47eebefca433e2965c13e4e65338e7b0960b0 "Email")

This article is part 1 of a series about building a high-performance web cluster powerful enough to handle 3 million requests per second. This first article describes my limited experience with a few load generator tools, and will hopefully save someone the time of having to stumble through each one like I did.

Load generators are programs that generate traffic of some type for the purpose of testing. They can show you how your server performs under heavy loads and can give you an idea of where the server's weaknesses may be. Learning your server's limitations through load testing is an excellent way to gauge your server's resilience and can help you be prepared for anything.

## Load-Generating Tools

One important thing to keep in mind when load-testing is that there are only so many socket connections you can have in Linux. This is a hard-coded kernel limitation, known as the [Ephemeral Ports Issue](http://aleccolocco.blogspot.com/2008/11/ephemeral-ports-problem-and-solution.html). You can extend it (to some extent) in /etc/sysctl.conf; but basically, a Linux machine can only have about 64,000 sockets open at once. So when load testing, we have to make the most of those sockets by making as many requests as possible over a single connection. In addition to that, we'll need more than one machine to do the load generation. Otherwise, the load generators will run out of available sockets and fail to generate enough load.

### Apache Bench

I started with 'ab', Apache Bench. This is the simplest general-use http benchmarking tool that I know of. And it ships with Apache, so it's probably already on your system. Unfortunately, I could only get about 900 requests/sec using this. I've seen other people get up to 2,000 with it, but I could tell right away that 'ab' wasn't the tool for this job.

### Httperf

Next, I tried 'httperf'. This tool is more powerful, but still relatively simple and limited in its capabilities. Figuring out how many req/sec you'll be generating is not as straightforward as just passing it a number. It took me several tries to get more than a couple hundred req/sec. For example:

This creates 100,000 sessions, at a rate of 1,000 per second. Each session makes 5 calls, which are spread out by 2 seconds.

```
httperf --hog --server=192.168.122.10 --wsess=100000,5,2 --rate 1000 --timeout 5

Total: connections 117557 requests 219121 replies 116697 test-duration 111.423 s
Connection rate: 1055.0 conn/s (0.9 ms/conn, <=1022 concurrent connections)
Connection time [ms]: min 0.3 avg 865.9 max 7912.5 median 459.5 stddev 993.1
Connection time [ms]: connect 31.1
Connection length [replies/conn]: 1.000
Request rate: 1966.6 req/s (0.5 ms/req)
Request size [B]: 91.0
Reply rate [replies/s]: min 59.4 avg 1060.3 max 1639.7 stddev 475.2 (22 samples)
Reply time [ms]: response 56.3 transfer 0.0
Reply size [B]: header 267.0 content 18.0 footer 0.0 (total 285.0)
Reply status: 1xx=0 2xx=116697 3xx=0 4xx=0 5xx=0
CPU time [s]: user 9.68 system 101.72 (user 8.7% system 91.3% total 100.0%)
Net I/O: 467.5 KB/s (3.8*10^6 bps)
```

Eventually, I was able to get 6,622 connections/sec with these settings:

```
httperf --hog --server 192.168.122.10 --num-conn 100000 --ra 20000 --timeout 5
```

(A total of 100,000 connections created, and the connections are created at a fixed rate of 20,000 per second.)

It has potential, and a few more features than 'ab'. But not quite the heavy-lifter that I need for this project. I need something that supports multiple load-testing nodes in a distributed fashion. Hence, my next attempt: Jmeter.

### Apache Jmeter

This is a full-featured web application test suite, that can simulate all kinds of real-life user behavior. You can use Jmeter's proxy to visit your website, click around, login, do whatever users do, and then have Jmeter record that behavior as the test case. Jmeter then performs those actions over and over, using as many simulated "users" as you like. This was very interesting to see! Though considerably more complex to [configure](http://jmeter.apache.org/usermanual/test_plan.html) than 'ab' and 'httperf'.

Immediately upon running this test, I was getting 14,000 requests per second! This was definitely a step in the right direction.

I used some of the plugins from the [Google Code project](http://code.google.com/p/jmeter-plugins/wiki/), and eventually was able to generate about 30,000 req/sec using their "Stepping Threads", and "HTTP RAW" requests. But at that point I hit a ceiling, and moved on. Here is one of my old [Jmeter configs](http://pastebin.com/54vJTNVE), in case that helps anyone else. It's far from perfect, but sometimes a starting point is all you need.

### Tsung: the heavy-duty, distributed, multi-protocol testing tool

This was a clear winner. Almost instantly, I was getting 40,000 requests/sec with this tool. Like Jmeter, you can record behaviors for the tests to run through, and test many protocols like SSL, HTTP, WebDAV, SOAP, PostgreSQL, MySQL, LDAP and Jabber/XMPP. Unlike Jmeter, there's no confusing GUI to get lost in. Just an XML config file, and some SSH keys to the distributed nodes of your choice. The minimalism and efficiency of this tool appealed to me as much as its robustness and scalability. I found it to be extremely powerful, generating millions of http requests per second with the right config.

In addition to all that, Tsung also generates graphs and writes out a detailed report of your test runs in html. Test results are easy to understand, and there's pictures so you can even show your boss! ;)

This is the tool I'll be referencing in the remainder of this 3-part article. See configuration instructions below, or skip to the [next page](http://dak1n1.com/blog/12-nginx-performance-tuning).

### Installing Tsung in CentOS 6.2

The first thing you'll need is the EPEL repository (for Erlang). So [set those up](http://dak1n1.com/blog/3-getting-more-from-yum-with-rpmforge-and-epel-repos) before continuing. Once that's done, install the required packages on each of the nodes that you'll be using to generate load. If you don't already have passwordless SSH keys set up between the nodes, do that too.

```
yum -y install erlang perl perl-RRD-Simple.noarch perl-Log-Log4perl-RRDs.noarch gnuplot perl-Template-Toolkit firefox
```

Download the latest Tsung from Github, or from their website.

```
wget http://tsung.erlang-projects.org/dist/tsung-1.4.2.tar.gz
```

Untar and compile.

```
tar zxfv  tsung-1.4.2.tar.gz
cd tsung-1.4.2
./configure && make && make install
```

Copy the example config into ~/.tsung. This is the location of the Tsung config files, and log files.

```
cp  /usr/share/doc/tsung/examples/http_simple.xml /root/.tsung/tsung.xml
```

You can edit this file to your specifications, or use the one that works for me. This is my config that, after much trial and error, now generates 5 million http requests per second, when used with 7 distributed nodes.

```
<?xml version="1.0"?>
<!DOCTYPE tsung SYSTEM "/usr/share/tsung/tsung-1.0.dtd">
<tsung loglevel="notice" version="1.0">

<clients>
 <client host="localhost" weight="1" cpu="10" maxusers="40000">
 <ip value="192.168.122.2"/>
 </client>
 <client host="loadnode1" weight="1" cpu="9" maxusers="40000">
 <ip value="192.168.122.2"/>
 </client>
 <client host="loadnode2" weight="1" maxusers="40000" cpu="8">
 <ip value="192.168.122.3"/>
 </client>
 <client host="loadnode3" weight="1" maxusers="40000" cpu="9">
 <ip value="192.168.122.21"/>
 </client>
 <client host="loadnode4" weight="1" maxusers="40000" cpu="9">
 <ip value="192.168.122.11"/>
 </client>
 <client host="loadnode5" weight="1" maxusers="40000" cpu="9">
 <ip value="192.168.122.12"/>
 </client>
 <client host="loadnode6" weight="1" maxusers="40000" cpu="9">
 <ip value="192.168.122.13"/>
 </client>
 <client host="loadnode7" weight="1" maxusers="40000" cpu="9">
 <ip value="192.168.122.14"/>
 </client>
 </clients>

 <servers>
 <server host="192.168.122.10" port="80" type="tcp"/>
 </servers>

 <load>
 <arrivalphase phase="1" duration="10" unit="minute">
 <users maxnumber="15000" arrivalrate="8" unit="second"/>
 </arrivalphase>

 <arrivalphase phase="2" duration="10" unit="minute">
 <users maxnumber="15000" arrivalrate="8" unit="second"/>
 </arrivalphase>

 <arrivalphase phase="3" duration="30" unit="minute">
 <users maxnumber="20000" arrivalrate="3" unit="second"/>
 </arrivalphase>

 </load>

 <sessions>
 <session probability="100" name="ab" type="ts_http">
 <for from="1" to="10000000" var="i">
 <request> <http url="/test.txt" method="GET" version="1.1"/> </request>
 </for>
 </session>
 </sessions>
</tsung>
```

It's a lot to take in at first, but it's really quite simple once you understand it.

- **<client>** is simply the host(s) to run Tsung on. You can specify IPs, and the max number of CPUs that you want Tsung to use. You can also set a limit on the number of users that the node will simulate with **maxusers**. Each of these users will perform an operation that we will define later.
- **<servers>** is the name(s) of the [http] server you want to test. We will be using this option to test the cluster IP, as well as individual servers.
- **<load>**defines when our simulated users will "arrive" at our website, and how quickly they will arrive.

  - **<arrivalphase>** In phase 1, which lasts 10 minutes, 15,000 users will arrive, at a rate of 8 per second.<br>
    <arrivalphase phase="1" duration="10" unit="minute"><br>
    <users maxnumber="15000" arrivalrate="8" unit="second"/>
  - There are two more arrivalphases, in which users arrive in a similar fashion.
  - Altogether, these arrivalphases make up a <load>, which controls how many requests per second we'll be generating.

- **<sessions>** This section defines what those users will be doing once they've arrived at your website.
- **probability** allows you to define random things that users might do. Sometimes they may click this, other times they may click that. **Probabilities must add up to equal** **100%**.
- In the configuration above, the users only ever do one thing, so it has a probability of 100%.
- **<for from="1" to="10000000" var="i">** This is what the users do, 100% of the time. They loop through 10,000,000 times and **<request>** a single web page, /test.txt.
- This looping construct allows us to use less user-connections to achieve a very high number of requests per second.

Once you've got that in place, you can create this handy alias to quickly view your Tsung reports.

```
vim ~/.bashrc
alias treport="/usr/lib/tsung/bin/tsung_stats.pl; firefox report.html"

source ~/.bashrc
```

Then start up Tsung.

```
[root@loadnode1 ~] tsung start
Starting Tsung
"Log directory is: /root/.tsung/log/20120421-1004"
```

And view the report when finished.

```
cd /root/.tsung/log/20120421-1004
treport
```

[![tsung-thumb](http://dak1n1.com/images/tsung-thumb.png)](http://zim.gina.alaska.edu/temp/tsung-8.3mil/report.html)

#### Using Tsung to Plan Your Cluster Build

Now that we have a powerful enough load-testing tool, we can plan the rest of the cluster build:

1. Use Tsung to test a single http server. Get a base benchmark.
2. Tune the heck out of those web servers, testing with Tsung regularly to see improvements.
3. Tune the TCP sockets of those systems to obtain optimal network performance. Again, test, test, test.
4. Build the LVS cluster, which contains those fully-tuned web servers.
5. Stress-test LVS by using Tsung on the cluster IP.

In the next two articles, I'll show you how to get your web server performing at top speed, and how to bring it altogether with the LVS cluster software.

[<span>Part 2: Tuning Nginx for Best Performance</span>](http://dak1n1.com/blog/12-nginx-performance-tuning)
