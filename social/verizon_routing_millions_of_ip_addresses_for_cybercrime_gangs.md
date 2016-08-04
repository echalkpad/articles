# Verizon Routing Millions of IP Addresses for Cybercrime Gangs

[Original URL](http://www.spamhaus.org/news/article/726/)

> Over the past few years, spammers have sought out large ranges of IP addresses. By spreading out their sending patterns across a wide range of IP addresses, they can attempt to defeat spam filters...

Over the past few years, spammers have sought out large ranges of IP addresses. By spreading out their sending patterns across a wide range of IP addresses, they can attempt to defeat spam filters and get spam and malware emails delivered where they are not wanted. However, [IPv4 addresses](https://en.wikipedia.org/wiki/IPv4) are getting scarce and hard to come by. In fact, as of September 2015, the Internet Registry ARIN (American Registry for Internet Numbers)[allocated the final block of IPv4 addresses from its free pool](https://www.arin.net/announcements/2015/20150924.html).

Because spammers can't easily obtain new IP addresses through legitimate means, they frequently resort to stealing IP address blocks that are dormant and aren't being utilized by the rightful owners. There is a thriving black market in IP addresses; spammers don't care whether the source of their IP addresses is legitimate or even legal. A cybercriminal that can steal a large IP address block (for example, a /16 or 65,536 IP addresses) can generate thousands of dollars per month.

For cybercriminals to make use of their stolen blocks however, a crucial step is to find an Internet Service Provider(ISP) or network with the ability to route these IP addresses to the rest of the Internet by using an [autonomous system number](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) (ASN). Also crucial is finding an ISP who won't look too closely at the highly suspicious routing request. To get the routes to their stolen IP addresses announced, criminals will present forged authorization documents (which constitutes felony [wire fraud](https://www.law.cornell.edu/uscode/text/18/1343) under U.S. law). In addition, spamming from these stolen IP addresses is a felony under the US [CAN-SPAM Act](https://en.wikipedia.org/wiki/CAN-SPAM_Act_of_2003#Criminal_Offenses).

Researchers at The Spamhaus Project have identified over **_4 MILLION IP addresses_** currently in the hands of American cybercriminals that are routed by US network operator [Verizon Communications](https://en.wikipedia.org/wiki/Verizon_Communications), which is currently by far the largest single source of [snowshoe spam](http://www.spamhaus.org/faq/section/Glossary#233) in operation today. We also will mention that at the time of this article, [Verizon has more than 80 SBL listings](https://www.spamhaus.org/sbl/listings/verizon.com) and ranks sixth in our ["The World's Worst Spam Support ISPs"](https://www.spamhaus.org/statistics/networks/) list. Being on this list is [not new](http://spamwatchers.com/verizon-is-worlds-fifth-worst-internet-service-provider-spamhaus/) to Verizon, but it has been some years since their concern for security/abuse issues has been this poor.

Some of the IP address ranges routed via Verizon and used for spamming are listed below:

```
14.4.0.0/15 PUBNETPLUS (Korea)
14.6.0.0/15 PUBNETPLUS (Korea)
42.128.0.0/12   North Star Information Hi.tech Ltd. Co. (China)
42.160.0.0/13   North Star Information Hi.tech Ltd. Co. (China)
42.168.0.0/13   North Star Information Hi.tech Ltd. Co. (China)
43.250.64.0/22  Infolink Communications (China)
103.41.180.0/22 Infolink Communications (Hong Kong)
116.129.0.0/16  New Guoxin Telecom Corporation (China)
116.132.0.0/15  New Guoxin Telecom Corporation (China)
116.136.0.0/15  New Guoxin Telecom Corporation (China)
116.138.0.0/15  New Guoxin Telecom Corporation (China)
116.140.0.0/15  New Guoxin Telecom Corporation (China)
116.142.0.0/15  New Guoxin Telecom Corporation (China)
116.148.0.0/15  New Guoxin Telecom Corporation (China)
116.150.0.0/16  New Guoxin Telecom Corporation (China)
116.152.0.0/15  New Guoxin Telecom Corporation (China)
116.156.0.0/14  New Guoxin Telecom Corporation (China)
116.160.0.0/14  New Guoxin Telecom Corporation (China)
116.164.0.0/14  New Guoxin Telecom Corporation (China)
116.168.0.0/15  New Guoxin Telecom Corporation (China)
116.179.0.0/16  New Guoxin Telecom Corporation (China)
116.184.0.0/13  New Guoxin Telecom Corporation (China)
120.46.0.0/15   CITIC Networks Management Co.,Ltd. (China)
120.48.0.0/15   CITIC Networks Management Co.,Ltd. (China)
155.40.0.0/16   Information Access Center (United States)
```

Most of the IP address ranges listed above are owned primarily by Chinese and Korean ISPs. Until 2013, there was no history of them being used at all for at least a decade. After being terminated from a few Asian hosts for spamming, these routes have found a safe home, being announced by [AS7046](http://www.cidr-report.org/cgi-bin/as-report?as=AS7046) (actually registered to [UUnet Technologies](https://en.wikipedia.org/wiki/UUNET), a company that was acquired by Verizon in 2006).

![](http://www.spamhaus.org/news/images/NewGuoxinTelecom.png "New Guoxin Telecom routing history, source: https://stat.ripe.net/")

New Guoxin Telecom routing history, terminated from several Asian ISPs before Verizon announcements.<br>
Source: <https://stat.ripe.net/>

At least one of the networks affected still exists: [Pubnet Plus](http://www1.american.edu/initeb/hp2566a/Telecommunication%20Infrastructure/new_page_4.htm) started out in the 1990s as a project to increase connectivity of public institutions in Korea. The Pubnet Plus assets are now owned by South Korean cellular carrier LG Uplus, however there was no reaction to the notification sent to them by Spamhaus. In addition, if any of the other ISPs involved were still in existence and wanted to announce their IP address ranges, they would most likely utilize their own AS Network instead of paying an American company like Verizon. For example, "New Guoxin Telecom Corporation" is assigned its own ASN ([AS38348](http://www.cidr-report.org/cgi-bin/as-report?as=AS38348)).

Spamhaus does not know for sure whether most of these Chinese and Korean ISPs are still in business and knowingly leasing their IP addresses to spam operations, but this seems unlikely. At least one of the IP address ranges, 155.40.0.0/16, belonged to a US based company named "[Information Access Center](https://whois.arin.net/rest/net/NET-155-40-0-0-1)", which was eventually acquired by the Thompson-Reuters Corporation ([TRI](https://www.google.com/search?q=NYSE%3ATRI&ie=utf-8&oe=utf-8&aq=t&channel=sb)). It seems very unlikely that Thompson-Reuters, a highly reputable news and information powerhouse, would be willing to lease their IP addresses to spammers. Spamhaus is therefore quite confident that this range was simply hijacked. If Verizon was deceived into announcing this hijacked range, we believe that the other Chinese and Korean ranges could very well have been hijacked as well (Verizon finally stopped announcing the 155.40.0.0/16 range on November 24th, 2015).

For a start, it seems very strange that a large US-based ISP can be so easily convinced by abusers to route huge IP address blocks assigned to entities in the Asian-Pacific area. Such blocks are not something that can go unnoticed in the noise of everyday activity. They are very anomalous, and should call for an immediate accurate verification of the customer. Internal vetting processes at large ISPs should easily catch situations so far from normality. Furthermore, since July 2015, Spamhaus has repeatedly informed Verizon about this problem, approaching every single contact known to us. In addition to contacts within Verizon Abuse and Security, we have also approached people in Verizon management. Various Verizon staff have promised to look into the situation, but the announcements continue and the spam and cybercrime keeps flowing.

Verizon would get few if any complaints about spam and abuse from these IP address blocks, because the [Whois](https://en.wikipedia.org/wiki/WHOIS#Regional_Internet_registries) contacts for these ranges don't belong to Verizon, but to the Chinese and Korean companies that still officially "own" the IP addresses. These companies are apparently either defunct, or are controlled by the the spammers or their conspirators. This would result in spam and abuse complaints sent to these companies simply being ignored. It would take a highly knowledgeable spam victim to realize that complaints should go to Verizon rather than the IP address Whois contacts.

Verizon is presently failing to properly vet IP address ranges for which it provides transit. While Verizon has an anti-spam policy and has participated in working groups such as [M3AAWG](https://www.m3aawg.org/), its present defacto policy of routing illicitly obtained IP address space for spammers means that it is directly responsible for facilitating massive sources of spam and cybercrime affecting millions of Internet users and networks. In addition to that spam and abuse, such behavior erodes trust and confidence in global IP address allocations and routings. Spamhaus strongly encourages Verizon to clean its routing tables without delay of such illicitly obtained IP address ranges.

«»
