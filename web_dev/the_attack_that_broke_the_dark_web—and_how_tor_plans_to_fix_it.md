# The Attack That Broke the Dark Web--and How Tor Plans to Fix It

[Original URL](http://fusion.net/story/238742/tor-carnegie-mellon-attack/)

> Law enforcement has been complaining for years about the Web "going dark," saying that encryption and privacy tools are frustrating their ability to track criminals online. But massive...

Law enforcement has been complaining for years about the Web "going dark," saying that encryption and privacy tools are frustrating their ability to track criminals online. But massive FBI operations over the last year that have busted 'hidden sites' used for the sale of drugs, hacking tools, and child pornography suggest the digital criminal world has gotten lighter, with law enforcement [bragging](https://www.fbi.gov/newyork/press-releases/2014/dozens-of-online-dark-markets-seized-pursuant-to-forfeiture-complaint-filed-in-manhattan-federal-court-in-conjunction-with-the-arrest-of-the-operator-of-silk-road-2.0) that criminals can't "hide in the shadows of the Dark Web anymore." While mysterious about its tactics, law enforcement indicated that it had found a way to circumvent the tool on which these sites relied, a software called Tor. But criminals are not the only ones who rely on it.

Tor, or The Onion Router, is a [browser](https://www.torproject.org/projects/torbrowser.html.en) that lets people use the Internet without being tracked and access hidden sites, as well as a software project that supports the 'Dark Web,' allowing websites (or "hidden services") to be hosted in such a way that their location is impossible to determine. Last year, Tor suffered a large-scale attack that compromised the anonymity of its users over a period of at least six months. The attack was launched by academic researchers affiliated with Carnegie Mellon University whose motives remain murky because they now refuse to talk about it. In subsequent prosecutions of people who used Tor hidden services for criminal purposes, government lawyers have said evidence came from a "university-based research institute," meaning that the academic exploration of the anonymity tool's vulnerabilities may send some Tor users to prison.

A review of emails sent on Tor's public list-serv reveals that Tor saw the attack coming, but failed to stop it. It raises questions about Tor's ability to maintain the privacy of the 2 million people who use it every day--most of them activists, human rights workers, journalists, and security-minded computer users, not criminals--as well as how far academic researchers and law enforcement should go to undermine the privacy protections people seek online.

In a phone interview last week, Tor chief architect Nick Mathewson explained for the first time exactly what happened and what Tor is doing to try to ensure it never happens again.

Tor saw the attack coming, but failed to stop it.

🔓🔓🔓

In February 2014, Sebastian "bastik" G.--a Tor supporter who contributes to the maintenance of the anonymity network Tor in his free time--noticed something amiss with the backbone of the Dark Web.

Tor depends on a world-wide network of computers that mask users' identities by encrypting their activity and bouncing it through a bunch of different stops on the way to its final destination; it's like 100 people whispering secrets in gibberish to each other during a huge game of Telephone, so that it's hard for an outsider to tell where a message started or where it ends. Tor relies on thousands of volunteers to run the servers that power the network, sometimes at [great personal risk](http://motherboard.vice.com/read/the-operators). Bastik saw that an internal monitoring program called "[DocTor](https://gitweb.torproject.org/doctor.git)," which scans the network for "hiccups," was reporting that a ton of new computers from the same IP address were rapidly joining the network as new relay points.

Bastik sent an [alarmed email](https://lists.torproject.org/pipermail/tor-talk/2014-February/032002.html) to the Tor mailing list saying that it looked like someone was launching an attack: if a single party controls enough relay points, it could undo the anonymity of the network. It's a phenomenon called a Sybil attack, named after a book about a woman with multiple personalities. It's as if in that giant game of Telephone above, 40 of the 100 people were actually one person, making it more likely they'd figure out you were the one who told a terrible secret.

A Tor developer [responded](https://lists.torproject.org/pipermail/tor-talk/2014-February/032009.html) dismissively, saying he would loop back in a week and that Tor wasn't overly concerned because they weren't exit relays, which are the last stop in the game of whispers. Tor decided the relays didn't pose a risk and ultimately did nothing to block them, a terrible mistake when it came to protecting the privacy of its users.

"I don't think this is the best response we've ever done to an attack situation," said Mathewson by phone.

Five months later, Michael McCord and Alexander Volynkin, two researchers at Pittsburgh-based Carnegie Mellon, announced that they had "broken" Tor, and discovered a way to identify hundred of thousands of users and find the true locations of thousands of 'hidden' websites.

"We know because we tested it, in the wild," they bragged in the abstract for a security conference talk that was [canceled](http://www.theguardian.com/technology/2014/jul/22/is-tor-truly-anonymising-conference-cancelled) shortly after it was announced. A Carnegie Mellon attorney [told the Black Hat conference organizers](http://www.reuters.com/article/2014/07/21/us-cybercrime-conference-talk-idUSKBN0FQ1QB20140721) that the talk relied on materials the university hadn't approved for public release. The researchers refused to comment, saying questions should be directed to Carnegie Mellon's Software Engineering Institute [SEI], the [Department of Defense-funded center](https://www.sei.cmu.edu/about/) at which they were employed. The university refused to answer further questions about the project, or to say whether the information gathered was shared with law enforcement.

The attack was launched by academic researchers affiliated with Carnegie Mellon University whose motives remain murky because they now refuse to talk about it.

"We are not able to comment on Tor," said SEI spokesperson Richard Lynch in an email this week.

But the answer [seemed clear](http://www.forbes.com/sites/kashmirhill/2014/11/07/how-did-law-enforcement-break-tor/) when, four months later, in November 2014, the FBI announced Operation Onymous (as in no longer Anonymous)--a [global crackdown on the Dark Web,](http://www.wired.com/2014/11/operation-onymous-dark-web-arrests/) that included the seizure of hidden websites and the arrest of dozens of Tor users involved in online drug markets. (Recent court documents citing a "university-based research institute" [support the link](http://motherboard.vice.com/read/court-docs-show-a-university-helped-fbi-bust-silk-road-2-child-porn-suspects).) And this year, in July, the crackdown continued with [Operation Shrouded Horizon](https://www.fbi.gov/news/stories/2015/july/cyber-criminal-forum-taken-down), in which a site for cyber-criminals called Darkode, which was hosted on Tor hidden services, was dismantled and [hundreds](http://www.breakingnews.ie/world/uk-police-arrest-660-in-massive-child-pornography-crackdown-636518.html) around the world were arrested. The FBI said in the press release that the global case was led by its [field office in Pittsburgh](https://www.fbi.gov/news/stories/2015/july/cyber-criminal-forum-taken-down), where Carnegie Mellon is based. The FBI would not comment this week on whether Carnegie Mellon's research had been used in its operations.

For as much as the Dark Web relies on Tor, it's a rinky-dink operation.

Mathewson and Tor founder Roger Dingledine, who met at MIT, have spent the last decade building up and maintaining Tor, which was originally a Naval Research Lab project to protect government communications. Eighty percent of its $2.5 million budget still comes from governments, including funding from the U.S. Defense Department and the U.S. State Department. For as much as the Dark Web relies on Tor, it's a rinky-dink operation. There are 22 full- and part-time paid employees dispersed around the world and about 50 volunteers and academics who contribute time and code (just 10 of them solidly dedicated to it currently, said Mathewson). Tor depends on academic researchers to identify ways to improve the technology and shore up vulnerabilities, so it regularly sees people running experiments on the network, most of which become papers [like these](http://freehaven.net/anonbib/).

"It's fairly normal for researchers to do benign but shifty looking activities," said Mathewson. "Activity in the past has looked suspicious at the time, but ultimately did stuff that helped advance our art."

The publication of the Black Hat schedule online in May 2014 was the first notice Tor got about what Carnegie Mellon had been up to. Tor reached out to the CMU researchers Volynkin and McCord but were told they couldn't say more because of "institutional confidentiality issues."

As the summer progressed, Tor slowly began realizing just how devastating the CMU project was. On June 12, 2014, someone from the Black Hat program committee sent Mathewson a copy of the researchers' paper, alarmed that the attack, which involved injecting signals into Tor protocol headers, might be actively affecting Tor. After reading the paper, Mathewson began working on a countermeasure.

"It didn't occur to me that they would run the attack in the wild on random users," said Mathewson. "The way the attack was structured, it was a bad attack for anyone to get away with it. Once detected, it was very easy to block. It didn't seem to me like a deep threat."

On June 23, 2014, Mathewson says the researchers sent Tor an email that described their attack, but with fewer details than were in the paper, omissions that would have made the attack harder to block.

Two weeks later, on July 4, Mathewson was in Paris for a Tor developers' meeting, an event that happens twice a year so that Tor's far-flung network of contributors and volunteers can meet each other and discuss pressing issues. More than fifty people gathered at Mozilla's offices in the center of Paris. It was productive but exhausting, a week of intense conversation, coding, and late nights with Internet friends rarely seen in person. On the last night of the week, Mathewson got back to his hotel room late and began running a test of his defense code to see if his countermeasure would work.

"Around 1 or 2 a.m., I discovered I was under attack," said Mathewson. "The hidden services I was visiting were sending a signal saying what I was connecting to."

He was shocked and immediately concerned about the danger for users. "Everyone who worked on this, including me, were about to get on airplanes," Mathewson said. "I contacted Roger [Dingledine] and as many core developers as I could find who were awake at that hour. Not many were. I reached out to everyone at different hotels and figured out the best, immediate defense."

There were only a few developers Mathewson trusted enough to work on it. They were spread thin but got enough trusted Tor directory authorities online to block-list the relays and servers involved in the attack.

Dingledine emailed the CMU researchers asking, "Is that you?" From that point on, the researchers stopped responding to emails from Tor. Their work, as it's understood, has been decried as a [huge breach of research ethics](http://motherboard.vice.com/read/academics-livid-concerned-over-allegations-that-cmu-helped-fbi-attack-tor).

🔔🔔🔔

By the end of July 2014, Tor had issued a new version of its software with fixes for the attack and published [a blog post](https://blog.torproject.org/blog/tor-security-advisory-relay-early-traffic-confirmation-attack) about what had happened. Tor's staff still believed at that point that the researchers had simply designed a reckless experiment with no intent to out users. But as the months went by, and law enforcement announced more and more operations that involved "breaking" the Dark Web, Tor's anger at Carnegie Mellon grew. This month, Tor claimed, based on conversations with people it believes to be credible, that the FBI paid Carnegie Mellon [$1 million](https://blog.torproject.org/blog/did-fbi-pay-university-attack-tor-users) to hack its network--a claim that the FBI and the university [deny](http://www.forbes.com/sites/thomasbrewster/2015/11/18/fbi-cmu-tor-million-dollar-payment-innacurate/).

"The allegation that we paid CMU $1 million is inaccurate," said a FBI spokesperson.

In the abstract for their Black Hat talk, the researchers said the attack cost only $3,000--presumably the hosting costs for its relay nodes. Putting aside Tor's claim that the government ordered the attack, once it was known that the researchers were sitting on top of a bunch of IP addresses associated with Dark Web activity, the government would certainly approach them for the evidence, which CMU could have handed over willingly or under legal pressure.

What the researchers gathered wouldn't just be the IP addresses of child pornographers and drug dealers, but activists, human rights workers, whistleblowers, and other noncriminals simply trying to navigate the Web privately.

Whether and what they handed over exactly, we still don't know. But what the researchers gathered wouldn't just be the IP addresses of child pornographers and drug dealers, but presumably anyone who used Tor between January and July 2014, which would include activists and human rights workers communicating in repressive countries, whistleblowers trying to stay anonymous while providing revealing documents to journalists, and other noncriminals simply trying to navigate the Web privately. Journalist and documentary director Laura Poitras [has said](https://blog.torproject.org/blog/what-tor-supporter-looks-laura-poitras) she couldn't have made contact with Edward Snowden or made _Citizenfour_ without Tor.

"There's an argument that this attack hurts all of the bad users of Tor so it's a good thing," said Mathewson. "But this was not a targeted attack going after criminals. This was broad. They were injecting their signals into as much hidden services traffic as they could without determining whether it was legal or illegal."

"Civil liberties are under attack if law enforcement believes it can circumvent the rules of evidence by outsourcing police work to universities," wrote Dingledine in a [Tor blog post](https://blog.torproject.org/blog/did-fbi-pay-university-attack-tor-users), which also questioned whether Carnegie Mellon had gotten approval from an institutional review board, a process that exists to ensure that academics don't harm human research subjects.

Theoretically, Tor could sue the university and the researchers for, essentially, hacking its network. Tor spokesperson Kate Krauss says Tor is in the early stages of figuring out what it's going to do legally. "We're evaluating our options in this area," she said.

It's the difference between studying epidemiology by looking at a virus in skin grafts and releasing the virus in the wild.

"This attack was done without any regard for user privacy," said Mathewson. "It's the difference between studying epidemiology by looking at a virus in skin grafts and releasing the virus in the wild. The responsible thing to do when you come up with an attack is to get it fixed, not to carry it out on random strangers. That crosses the line from security research into malicious behavior."

🔒🔒🔒

So, the big question many security-minded people have been asking since this attack was revealed is, 'Can you still trust Tor?'

Mathewson says Tor has made major changes to its operation to prevent this kind of attack from working again, starting with "not extending security researchers the benefit of the doubt on anything." It now has a set, strict procedure for how to respond when it sees a bunch of servers join its network. It will remove them by default rather than taking a 'wait and see if they do something weird' approach.

We now have a 'block first, ask questions later' policy.

- Tor chief architect Nick Mathewson

"We seriously revamped our code that scans the network for suspicious behavior," said Mathewson. "We now have a 'block first, ask questions later' policy."

A Tor server now needs to do more to control a bunch of relay nodes to be considered a reliable [hidden services directory](https://www.torproject.org/docs/hidden-services.html.en), said Mathewson. Those are the places in the Tor network that point people to otherwise "dark" sites not exposed to the open Web. Tor is also working on what Mathewson calls a "new cryptographic trick" that will allow a hidden services directory to send someone to a hidden site (which they identify with a .onion Web address) without the directory knowing where it's sending them.

"We've been working on a revamp of the hidden services design over the last year," said Mathewson. "The implementation is in progress but it's not done."

A larger problem is a lack of manpower at Tor; this attack was successful because a concerning development didn't get the attention it deserved. This is indicative of a larger problem in the security ecosystem: many of the critical tools we rely on for the privacy and security of our online activity are understaffed and underfunded. At the same time that Tor was under attack in 2014, a security researcher discovered the [Heartbleed](http://fusion.net/story/5289/the-first-wave-of-heartbleed-attacks-have-struck/) bug, a software flaw that affected a large chunk of the Internet, which stemmed from a mistake made in an OpenSSL codebase relied on by scores of Internet companies but supported by just [one full-time nonprofit employee](http://arstechnica.com/information-technology/2014/04/tech-giants-chastened-by-heartbleed-finally-agree-to-fund-openssl/). Tor's decentralized, crowdsourced model has strengths, but its tiny operation, with few full-time employees, has weaknesses as well--one of which was exploited here.

Tor recently launched a [crowdfunding campaign](https://blog.torproject.org/blog/what-tor-supporter-looks-laura-poitras) to try to increase its number of individual funders so that it has more freedom in how it spends. "

<span class="s1">We are internally obsessed with getting more diverse with our funding and having unrestricted money,” said spokesperson Kate Krauss. “</span>

<span class="s1">We want to solve problems as we see them as opposed to what an institutional funder is focused on.”</span>

As for the question of 'Can people trust Tor?', Mathewson had a pragmatic response.

"There is no computer security program out there with 100% confidence that everything you do is going to be safe," said Mathewson. "We can provide a high probability of safety and get better all the time. But no computer software ever written is able to provide absolute certainty. Have a back-up plan."
