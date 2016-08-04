# The Mission To Save The Internet By Rewiring It From The Name Up

[Original URL](http://motherboard.vice.com/read/the-internet-of-names)

> While most of us have been binge-streaming or strapping computers to our bodies or wrapping our heads around the ins and outs of net neutrality, an international team of academics and some of the...

While most of us have been binge-streaming or strapping computers to our bodies or wrapping our heads around the ins and outs of net neutrality, an international team of academics and some of the world's biggest technology companies have been quietly pondering how to rewrite the basic structure of the internet--for our sakes.

Their idea sounds simple: instead of numbers, use names. Focus not on the locations of things, but on the things themselves.

The proposal, called [Named Data Networking](http://named-data.net/project/execsummary/), shifts the focus from the numbered locations of data--IP addresses like 174.16.254.1--to the very names of data--something like motherboard/stories/NDN/photo1\. Under this system, for example, when your computer makes a packet request for a new Netflix release, you could retrieve the video from the nearest computer that has it, rather than wait to get it from Netflix's heavily-trafficked centralized servers.

"As far as the network is concerned," the project's website says, "the name in an NDN packet can be anything: an endpoint, a chunk of movie or book, a command to turn on some lights, etc." An internet not of numbers, but, if you will, of things.

What that means, in practice, could be big. An internet focused on the what, not the where, could be a more flexible internet, less likely to get clogged up as a steady stream of new devices join the party. An internet that no longer relies on [the aging architecture known as TCP/IP](http://en.wikipedia.org/wiki/Internet_protocol_suite) could also be an internet with fewer of the middlemen that currently throttle speeds, gather our data, or control what can and can't be seen.

One person who thinks a lot about the need for switching to NDN is [Lixia Zhang](http://www.cs.ucla.edu/~lixia/), a professor of computer science at UCLA who is known for research into protocols and security that have impacted[how we use the internet today](https://alum.mit.edu/news/AlumniProfiles/Archive/zhang). (It's a far cry from her first job: as a teenage tractor driver in northern China during the Cultural Revolution.) Her own interest in NDN--she's now one of the project's leaders--emerged out of what she saw as changing needs for the network: The current internet simply doesn't fit how we want to use our devices anymore.

Over lunch recently, a colleague at UCLA asked her to clarify that: Why, exactly, does the internet need a new architecture?

"I said, 'There's a simple answer.' I had my phone on the table, and he had his phone on the table. I said, 'Look, our phones are next to each other, can they talk to each other?' He said, 'of course they can.' I said, 'directly?' And he said, 'no, they cannot.' And this is not because physically they can not, physically [you can connect] phone-to phone using WiFi, Bluetooth. But the TCP/IP protocol is the roadblock for phones to directly talk to each other. The protocols need to change because it doesn't enable the communication that we want."

![](https://motherboard-images.vice.com/content-images/contentimage/20819/1430003100348428.png)

The evolution of networking, from infrastructure to a distributed architecture. Image courtesy Van Jacobsen

The plan for NDN is one approach to what's known as information centric networking (ICN), an idea with roots in Ted Nelson's 1980s experiments with hypertext. It wasn't until 2006, however, when projects [at Stanford](http://www.sigcomm.org/ccr/papers/2007/October/1282427.1282402), the University of Texas at Austin, and at [the legendary Xerox PARC](http://www.newyorker.com/magazine/2011/05/16/creation-myth) launched the idea into the networking mainstream. Internet pioneer and PARC researcher Van Jacobson, whose [talk at Google in 2006](https://www.youtube.com/watch?v=gqGEMQveoqg) has been widely cited as the start of the ICN movement, is now a leader of the NDN project alongside Zhang.

Now there are a range of smaller NDN-like initiatives globally, and last September, the ICN's various research questions were the topic of [its own conference](http://conferences2.sigcomm.org/acm-icn/2014/). Other ambitious projects seeking to address current problems with the internet include [software defined networking](https://www.opennetworking.org/sdn-resources/sdn-definition) (SDN) and [network function virtualization](http://en.wikipedia.org/wiki/Network_functions_virtualization) (NFV).

Zhang said the various approaches aren't competing so much as complementary, tackling related problems. But NDN is the best funded, with $15 million through 2016 under the [National Science Foundation](http://www.nets-fia.net/), in addition to corporate assistance (more on that below). "I'd rather say that they are near-term solutions, and not really competition. NDN is specific as a new protocol architecture [to] replace TCP/IP."

![](http://motherboard-images.vice.com/content-images/contentimage/20819/1430234869738476.jpg)

Professor Lixia Zhang, UCLA and Xiaoming Li, Peking University, at a UCLA-Peking University Joint Research Institute Mini-Symposium. Photo: Rebecca Kendall

When it was [first built in the 1970s](http://en.wikipedia.org/wiki/Internet_protocol_suite#Early_research), the current iteration of the global network was designed around IP addresses, numbers that specify and communicate the location of data, be it on a server, on your laptop, or on your phone (like 100.40.80.60). But this system, known as Transmission Control Protocol/Internet Protocol, or TCP/IP--designed for shuttling around small bits of data between relatively few machines having "conversations"--has been called inefficient, insecure, and terribly inadequate for the real world. Researchers back then didn't anticipate a far larger internet, brimming with heavy content like video and a wealth of new devices sucking that data down. A new system called IPv6, designed to handle far more devices (its numbers look like 2001:0db8:85a3:0042:1000:8a2e:0370:7334), debuted in 2008, but it has struggled to reach mainstream adoption.

The plan for a more distributed internet that's not dependent on scarce or lengthy IP addresses should appeal to proponents of security and of the Internet of Things, the project's researchers say. On an inter-network increasingly crowded with all sorts of devices, relying on names would allow for more flexibility, speed, and security, said [Jeff Burke](http://remap.ucla.edu/jburke/), assistant dean of technology and innovation at the UCLA School of Theater, Film and Television, and a principal investigator for the [project](https://twitter.com/uclaremap). NDN would also, he said, "make writing the kind of apps we use simpler and easier to do, making internet innovation easier to achieve."

![](https://lh5.googleusercontent.com/-p4ISzk5-nKlD12xVtKKKkfJZ6lkQXM7EBRhLIVT4dFQTNjxmJk7fZ3GK9WGYopBWfpszNOj66mR12L_-Ymn35wJnGX97ANsnnMbgXeu-kohGZ7j69IwTYinQkoYET1AcTLWkK4)

Using NDNVideo, researchers streamed a live performance from UCLA to Beiing March 2013\. Image: UCLA

"This is a lot of people who have done work on the internet for many, many years who are investigating what worked and what didn't work, and are proposing evolution for the underlying fundamental protocols," he added. Their hope is to build a network architecture that isn't just more efficient and flexible but, in a sense, more democratic too. With a named-data architecture, major media providers like Hulu and YouTube would not be the sole key-holders to digital content, nor the only methods of publication.

"Computers can store something others want instead of just [media providers] distributing it," said Burke. "Instead of there being a single server that's sourcing, lots and lots of people are able to access things." In other words, he said, "it's decentralizing publication."

How it works

Think of the current internet as a "point-to-point system," like the way we use telephones: to get information, computers (clients) call servers and vice versa. Named Data Networking switches the focus from where digital information comes from to what users are looking for. Under this rubric, the request your computer or phone makes to the network is not "connect me to the computer with this name," but "give me the data associated with this name."

![](https://motherboard-images.vice.com/content-images/contentimage/20819/1430003835715281.png)

The "hourglass" design of the internet, under IP and NDN. Image courtesy Van Jacobsen

Imagine walking into a room full of people and asking everyone the time. Anyone with a watch can respond, and you don't have to rely on one clock on the wall. This is how Named Data Networking works: As long as you name it, anyone who has the digital content you are looking for can provide it, making you less dependent on receiving information--or distributing information--through a middleman.

Currently, when you stream content from websites like YouTube, your computer pulls data from the closest YouTube server. [Since the late '90s](http://www.globaldots.com/the-history-of-content-delivery-networks-cdn/), media providers and e-commerce networks have paid for content delivery networks (CDNs), like those operated by Akamai, to provide high-speed availability to you by having servers in many different locations.

But this means that not all data is served equally: Content producers who don't use content delivery networks have trouble providing access and often face slow connections, which of course tend to get slower if their content becomes popular. NDN's efficient architecture would do away with a heavy reliance on CDNs, and better accommodate upswings in traffic with other, lighter kinds of infrastructure.

[Alexander Afanasyev](http://lasr.cs.ucla.edu/afanasyev/index.html), a postdoc at UCLA's Internet Research Lab, said NDN would eliminate the need for "complex cloud infrastructure," often owned by Internet heavyweights. That's because, he said, "the NDN network itself inherently provides functions of traffic engineering and direct requests towards the closest available replica [of the data], splitting the load into multiple paths if necessary." Data would be stored, he explained, on "in-network storage that will be installed and maintained by new providers," and "would potentially help all applications, not just HTTP/HTTPS based ones."

![](http://motherboard-images.vice.com/content-images/contentimage/20819/143000790859159.png)

How data gets distributed over IP and NDN architectures. Image courtesy Van Jacobsen

> ### "BitTorrent is implemented on top of the current internet. NDN makes all of those features available at the lower layers for all applications. It makes peer-to-peer publishing simpler."

The capabilities of NDN echo the peer-to-peer design of the BitTorrent protocol, which lets anyone publish and request data based on what they are looking for, rather than where it lives on the network. But BitTorrent sits on top of and accommodates TCP/IP; NDN replaces it entirely.

For BitTorrent that difference means that the BitTorrent system must rely, said Burke, on "a lot of machinery to find out the names for content, to figure out who to issue requests to, a lot of things like that. NDN makes all of those features available at the lower layers for all applications."

A network based around content, not IP addresses, could also make the distribution of data more efficient and equal, a prospect appealing if you favor network neutrality--the principle that everyone deserves equal access to the same bandwidth. It might not sound as favorable if, say, you're the head of a large telecom company, in control of the pipes that NDN would effectively decentralize.

Some big companies however are interested in being a part of NDN's development. PARC, where NDN was largely born, [continues to be involved in the project](https://www.parc.com/work/focus-area/content-centric-networking/), along with seven other [members from industry](http://named-data.net/consortium-members/), including Intel, Cisco Systems, and Huawei. This arrangement augments the project's academic research--the companies contribute their own work and funding--and allows these companies to guide the evolution of a nascent architecture. "Each of the companies involved contributes and has technical ideas about the particular approach that we're taking," Burke said.

On the software side, for example, "Intel has people working on applications actively, developing and using NDN tools. They give us their ideas, they get to know that there will be resources they have provided, and they get to help make sure there's a community of people working on it." The presence of hardware vendors suggests that the current NDN protocol, while designed to be a "universal overlay" on the existing internet, would eventually run on hardware specifically designed for NDN.

Despite the corporate influence, however, Burke underscores the project's commitment to remaining public and open source. While NDN may not be widely available to consumers for a decade, the codebase [is already open to the public](http://named-data.net/codebase/platform/) on [Github](https://github.com/named-data) so that others can begin exploring and implementing it. Researchers have already been using and testing Named Data Networking principles with applications for [video communication](http://named-data.net/geni-2013/) and health tracking, and restructuring them for simpler access and easier data sharing.

One application that researchers have been testing, [Open mHealth](http://www.openmhealth.org/), relies on open standards for data exchange, as part of its approach to interoperability and data protection. "This is a good fit for NDN, and is an application area that encourages us to look at privacy and security carefully, too," Burke said.

Security is a major concern for networking researchers--especially in a system where data is no longer coming from the server where it originates, but rather from the nearest computers, which may not be as trustworthy.

To that end, NDN embeds a kind of digital trust into the data itself, so that your computer can retrieve that data from anywhere with confidence. NDN's emphasis on the name of the data, rather than its location, extends to its security philosophy. The current internet's security model allows for an encrypted connection between end points; for example, your computer browser and Gmail's servers. It's a connection-oriented approach to security. Named Data Networking aims to encrypt the data itself, as it's created, rather than just having encrypted--and vulnerable--end-points on the routes that data takes.

![](http://motherboard-images.vice.com/content-images/contentimage/20819/1430456926395721.png)

The United States section of [the NDN testbed](http://motherboard.vice.com/read/www.arl.wustl.edu/~jdd/ndnstatus/ndn_prefix/tbs_ndnx.html), the project's experimental global network. Map: [Washington University in St. Louis](http://ndnmap.arl.wustl.edu/).

"In the current internet, the fundamental protocols don't have any inherent security primitives, so we've built a lot of machinery on top of them to do security that essentially is based on a conversational model," Burke said.

That machinery also allows for interesting new opportunities to prevent web content from disappearing (consider the problem of [link rot](http://www.newyorker.com/magazine/2015/01/26/cobweb)), and to protect the intellectual property of a piece of data, or at least to attach it to a particular creator. In NDN, a signature is built into every data packet, creating a binding for the name for the data, the data itself and then the cryptographic identity of the publisher. What of this metadata would be available, and how it would be used, would be up to individual users and apps.

## Narrow Waists and Bottlenecks

While it stretches across sixteen universities--half in the U.S., half overseas--linking up to [the NDN testbed](http://named-data.net/ndn-testbed/) isn't easy: in addition to agreeing to [a lengthy set of policies](http://named-data.net/ndn-testbed/policies-connecting-nodes-ndn-testbed/), NDN presently requires installing software applications and "the protocol stack," with its naming system at the core. Over time, as more nodes join the network, the hope is that IP--now the "narrow waist" at the center of the internet's hourglass architecture--will be replaced by NDN, with the internet as we know it--including servers and our own browsers--migrating along with it.

Of course, this won't be easy. Consider the adoption of IPv6: In [a recent study](http://arstechnica.com/business/2014/08/ipv6-adoption-starting-to-add-up-to-real-numbers-0-6-percent/) of as many as half of the packets flowing across the Internet at certain times, researchers found that IPv6 packets numbered about 0.6 percent. This represented a big advance on previous years, but also demonstrated how hard it is to convince people to upgrade to a new kind of network.

Open research questions in NDN. Video: [UCLA REMAP](https://vimeo.com/uclaremap) / [Vimeo](https://vimeo.com).

There are plenty of challenges ahead. Aside from the not insignificant matter of converting to a new protocol, however, one lingering challenge to wider adoption is figuring out, how, exactly, to handle a network that would be overflowing with names instead of numbers. Because each set of data in the NDN architecture is specified by a panoply of names and hosts rather than simple IP addresses, the count of the number of things each person routes to is orders of magnitude greater than under an IP system. That unbounded "namespace" could make routing table sizes astronomically large and make the system unwieldy.

But Afanasyev and other researchers have proposed a solution: By building a catalog that stores network locations and points requests in the right direction, the system stem a flood of lots of individual names. This more conventional routing approach would, they write in [a new paper](http://lasr.cs.ucla.edu/afanasyev/data/files/Afanasyev/SNAMP-NDN-Scalability.pdf), "separate what is required to be in the global routing/forwarding system from all names that can exist in the NDN universe," enabling NDN to scale with the Internet's well-understood routing protocols, while keeping all the benefits of the new NDN architecture.

Burke is certain that NDN, or some form of it, will be adopted in his lifetime. That's partly because the system was designed with existing applications and concerns in mind, from video to security to IP protection. The project is "being done in such a way that you could introduce NDN-engineered communication in challenging existing applications much sooner than you'd see evolution across a broader spectrum of the internet."

Afanasyev notes that there is still skepticism about NDN, particularly related to its ability to perform reliably, "whether the system can scale, how would routers be able to process packets fast enough, etc. For some of these questions/challenges we know partial answers, for some not yet. That's why NDN (and I would say information-centric networking area in general) is still in research stage :)"

"Although," he wrote in an email, "I have a feeling that people who dive deeper into NDN's details generally like the idea."

_With additional reporting by Alex Pasternack_
