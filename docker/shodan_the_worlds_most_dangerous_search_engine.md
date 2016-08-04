# Shodan: The World's Most Dangerous Search Engine

[Original URL](http://www.mikecarthy.com/offensive-security/shodan-worlds-dangerous-search-engine/)

> Each and every day we're becoming increasingly connected. This has been driven by an acceleration of the Internet of Things (IoT) – a highly complex network of physical...

**Each and every day we're becoming increasingly connected.** This has been driven by an acceleration of the Internet of Things (IoT) – a highly complex network of physical devices and systems with embedded electronics and network connectivity – that enable devices to communicate and exchange data. This would have been science-fiction merely 20 years ago – however the reality today is that cyber-physical systems permeate throughout every aspect of our modern lives. Their applications are varied and wide-ranging, and include environmental monitoring, manufacturing, infrastructure management, healthcare, domestic and transportation.

This rapid uptake has been largely made possible by the transition into IPv6 – the latest version of the IP networking protocol that underpins every aspect of our digital livess. This new protocol provides us with **340 Trillion Trillion available addresses**, which to give you some perspective – is 10

<sup>21</sup>

 addresses per square meter on earth. This new version solved a serious problem that was inherent in its predecessor IPv4 – that there were not enough addresses available to cope with the blistering expansion of the internet.

In the future every single device that we own will will be interconnected to every other – but has anybody thought about the security implications that this presents? The evidence to date suggests not. Building security into these devices appears to be an after-thought. Security has become a bolt-on addition to products following their development cycle, rather than being integrated into the product design from the ground up.

**The result?**

A network of interconnected & insecure devices that are publicly accessible from the internet. You may not have known but a project exists that aims to automate the detection and cataloguing of these devices.

**Shodan.**

[<embed src="http://www.mikecarthy.com/wp-content/uploads/2016/02/shodan.png%201901w,%20http://www.mikecarthy.com/wp-content/uploads/2016/02/shodan-300x138.png%20300w,%20http://www.mikecarthy.com/wp-content/uploads/2016/02/shodan-768x353.png%20768w,%20http://www.mikecarthy.com/wp-content/uploads/2016/02/shodan-1024x471.png%201024w" class="alignnone size-full wp-image-653" width="1901">](http://www.mikecarthy.com/wp-content/uploads/2016/02/shodan.png)

Shodan is a search engine much like Google, however that's where the similarities end. Rather than indexing web content over ports 80 (HTTP) or 443 (HTTPS) like Google – Shodan crawls the web searching for devices that respond on a host of other ports including 21 (FTP), 22 (SSH), 23 (Telnet), 25 (SMTP), 80, 443, 3389 (RDP) and 5900 (VNC). Once Shodan discovers a host that's responding on a given port it connects to the machine and pulls down the port banner. This information then becomes indexed along with the devices **geolocation data.**

Since launching in 2009 Shodan has discovered and indexed a wide range of internet connected devices, including webcams, traffic signalling equipment, routers, firewalls, CCTV systems, industrial control systems for nuclear power plants and electrical grids, domestic home appliances and much more. These devices have been connected to the internet without any thought for security – often without even implementing basic protections such as a strong username and password.

**Some of the webcam feeds alone include images of marijuana plantations, back rooms of banks, children**, kitchens, living rooms, garages, front gardens, back gardens, ski slopes, swimming pools, colleges and schools, laboratories, and cash register cameras. Shodan also includes a feature for paid members on [images.shodan.io](http://images.shodan.io) where you can view a constant live stream of internet connected webcams and computer desktops.

[<embed src="http://www.mikecarthy.com/wp-content/uploads/2016/02/webcam.png%201901w,%20http://www.mikecarthy.com/wp-content/uploads/2016/02/webcam-300x153.png%20300w,%20http://www.mikecarthy.com/wp-content/uploads/2016/02/webcam-768x391.png%20768w,%20http://www.mikecarthy.com/wp-content/uploads/2016/02/webcam-1024x522.png%201024w" class="alignnone size-full wp-image-658" width="1901">](http://www.mikecarthy.com/wp-content/uploads/2016/02/webcam.png)

Searching on Shodan is simple and powerful and gives you the ability to find what you're looking for with ease. Your number of results is limited with a basic account – so you may need to upgrade if you'd like to access and make use of premium features. These include accessing the full search listings, plotting the host locations on maps and finding exploits for ports and services based upon version information.

[<embed src="http://www.mikecarthy.com/wp-content/uploads/2016/02/shodanresults.png%201013w,%20http://www.mikecarthy.com/wp-content/uploads/2016/02/shodanresults-300x121.png%20300w,%20http://www.mikecarthy.com/wp-content/uploads/2016/02/shodanresults-768x309.png%20768w" class="alignnone size-full wp-image-666" width="1013">](http://www.mikecarthy.com/wp-content/uploads/2016/02/shodanresults.png)

Like any good search engine Shodan also gives you the option to search using various filters – which makes it much easier to narrow your results down and find what you're looking for.

- **`city`**: find devices in a particular city
- **`country`**: find devices in a particular country
- **`geo`**: you can pass it coordinates
- **`hostname`**: find values that match the hostname
- **`net`**: search based on an IP
- **`os`**: search based on operating system
- **`port`**: find particular ports that are open
- **`before/after`**: find results within a timeframe

We could for example use these filters to search for 

<span class="type">apache </span>

<span class="setting">city</span>

:

<span class="value">“London” to find Apache servers in London or even Server: “SQ-WEBCAM” country:”US” to find all webcams in the United States. </span>

While it's frightening to learn how many Internet of Things devices are completely unsecured – there's also another story behind the ones that are. Many of the devices that Shodan detects and indexes do have some security in place – requiring authentication for example, but even these devices aren't 100% safe from unauthorised access. In the ever-changing world of cyber security nothing remains static, and new exploits and vulnerabilities are being discovered and disclosed all of the time.

A significant example involves one of the largest and most well-known computer networking companies in the world – **Juniper**. In a recent public disclosure Juniper revealed that the firmware running on some of their devices contained a hard-coded back-door password that would allow anybody connecting to a vulnerable device to simply supply that password against a valid user account to gain full administrative access to the device over Telnet or SSH. Oh and believe me it works – I used this exploit against a vulnerable NetScreen firewall at a recent security conference in Brisbane. You can read the full disclosure here: [CVE-2015-7755.](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2015-7755)

[<embed src="http://www.mikecarthy.com/wp-content/uploads/2016/02/cve.png%201891w,%20http://www.mikecarthy.com/wp-content/uploads/2016/02/cve-300x50.png%20300w,%20http://www.mikecarthy.com/wp-content/uploads/2016/02/cve-768x127.png%20768w,%20http://www.mikecarthy.com/wp-content/uploads/2016/02/cve-1024x169.png%201024w" class="alignnone size-full wp-image-677" width="1891">](http://www.mikecarthy.com/wp-content/uploads/2016/02/cve.png)

Now this is where it becomes **super scary.** Using Shodan we can search for Juniper firewalls and browse through the list to find those that are running a vulnerable version of the ScreenOS firmware. Once we're connected we'd be able to supply the known backdoor password with a default ScreenOS user account (hint: system) and be able to begin remote management of those devices.

We're not talking about firewalls that belong to Dave the greengrocer either – these boxes are enterprise grade kit and would be live on mission critical networks all over the world. And how many of these potentially vulnerable NetScreen firewalls has Juniper indexed? **More than 18,000\.** Assuming only 10% of those are vulnerable (which is an extremely conservative estimate) that's **1,800 vulnerable Juniper firewalls** that are currently sitting targets right now on the internet.

[<embed src="http://www.mikecarthy.com/wp-content/uploads/2016/02/juniper.png%201903w,%20http://www.mikecarthy.com/wp-content/uploads/2016/02/juniper-300x93.png%20300w,%20http://www.mikecarthy.com/wp-content/uploads/2016/02/juniper-768x238.png%20768w,%20http://www.mikecarthy.com/wp-content/uploads/2016/02/juniper-1024x317.png%201024w" class="alignnone size-full wp-image-678" width="1903">](http://www.mikecarthy.com/wp-content/uploads/2016/02/juniper.png)

At DefCon 2012 an independent security researcher and penetration tester Dan Tentler demonstrated how he was able to use Shodan to find control systems for evaporative coolers, pressurised water heaters and even garage doors. He was also able to find a hydroelectric plant in France, a car wash that could be turned on and off and a hockey rink in Denmark that could be defrosted with the click of a button. He even found a city's entire traffic control system was connected to the internet and could be interrupted with some simple commands.

While researching for this article I was also able to find similar systems – and managed to uncover some vulnerable Juniper firewalls in Brisbane which is close to where I live. If these large enterprises haven't got the resources to lock down and protect their infrastructure then what chance do we have? It's up to manufacturers to build security into our products and services so that it removes the responsibility from ourselves.

[<embed src="http://www.mikecarthy.com/wp-content/uploads/2016/02/6.png%20830w,%20http://www.mikecarthy.com/wp-content/uploads/2016/02/6-300x59.png%20300w,%20http://www.mikecarthy.com/wp-content/uploads/2016/02/6-768x152.png%20768w" class="alignnone size-full wp-image-692" width="830">](http://www.mikecarthy.com/wp-content/uploads/2016/02/6.png)

Attacks on critical infrastructure until now have been minimal to non-existent. Unfortunately it's only a matter of time before this changes. Attacks on networked industrial control systems are going to become a significant threat to our safety and security – given that computer systems regulate the treatment plants that deliver our drinking water, the traffic lights that allow us to drive safely, the signalling systems on a transport networks and the nuclear reactors that deliver our energy.

**Make no mistake – these are incredibly valuable targets.**

As consumers we need to think carefully about the smart products that we purchase and consider the security implications that come with many of these devices. As businesses we need to make sure that we have a proper risk management framework in place – and that the person or organisation that's looking after our technology is also capable of looking after our security.

So what do you think about the Internet of Things? Leave your thoughts and comments below!
