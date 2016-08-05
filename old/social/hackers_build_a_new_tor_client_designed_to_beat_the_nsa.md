# Hackers build a new Tor client designed to beat the NSA

[Original URL](http://www.dailydot.com/politics/tor-astoria-timing-attack-client/)

> Anonymity's toughest adversaries are hackers with the full-force and backing of Beijing, London, and Washington, D.C. With the threat of powerful intelligence agencies, like the NSA,...

Anonymity's toughest adversaries are hackers with the full-force and backing of Beijing, London, and Washington, D.C.

With the threat of powerful intelligence agencies, like the [NSA](http://dailydot.com/tags/nsa), looming large, researchers have built a new [Tor](http://dailydot.com/tags/tor) client called [Astoria](http://arxiv.org/pdf/1505.05173.pdf) designed specifically to make eavesdropping harder for the world's richest, most aggressive, and most capable spies.

Tor, the world's most popular anonymity network, works like this: A user fires up the client and connects to the network through what's called an entry node. To reach a website anonymously, the user's Internet traffic is then passed encrypted through a so-called middle relay and then an exit relay (and back again). That user-relay connection is called a circuit. The website on the receiving end doesn't know who is visiting, only that a faceless Tor user has connected.

An eavesdropper shouldn't be able to know who the Tor user is either, thanks to the encrypted traffic being routed through 6,000 nodes in the network.

But something called "timing attacks" change the situation. When an adversary takes control of both the entry and exit relays, research shows they can potentially deanonymize Tor users [within minutes](http://arxiv.org/abs/1503.03940).

A full 58 percent of Tor circuits are vulnerable to network-level attackers, such as the NSA or Britain's Government Communications Headquarters (GCHQ), when they access popular websites, according to new [research](http://arxiv.org/pdf/1505.05173.pdf) from American and Israeli academics. Chinese users are the most vulnerable of all to these kinds of attacks, with researchers finding 85.7 percent of all Tor circuits from the country to be vulnerable.

Even though Tor is designed to provide complete anonymity to its users, the NSA's position means they can potentially see and measure both traffic entering the Tor network and the traffic that comes out. When an intelligence agency can see both, [simple statistics](http://freehaven.net/anonbib/#danezis:pet2004) help an autonomous system at their control match the data up in a timing attack and discover the identity of the sender.

Anonymity over.

This kind of threat has been known to Tor developers for over a decade. They've been trying to make eavesdropping difficult for spy agencies for just as long.

To counter the threat, American-Israeli researchers built Astoria, a new Tor client focused on defeating autonomous systems that can break Tor's anonymity.

Astoria reduces the number of vulnerable circuits from 58 percent to 5.8 percent, the researchers say. The new solution is the first designed to beat even the most [recently](http://arxiv.org/abs/1503.03940) [proposed](http://dl.acm.org/citation.cfm?id=2673869) asymmetric correlation attacks on Tor.

Designed to beat such attacks, Astoria differs most significantly from Tor's default client in how it selects the circuits that connect a user to the network and then to the outside Internet. The tool, at its foundation, is an algorithm designed to more accurately predict attacks and then securely select relays that mitigate timing attack opportunities for top-tier adversaries.

Astoria adroitly considers how circuits should, according to the researchers, be made "when there are no safe possibilities," how to safely balance the growing bandwidth load across the Tor network, and how to keep Tor's performance "reasonable" and relatively fast even when Astoria is in its most secure configuration.

All this while under the unblinking gaze of the world's best intel services.

Defeating timing attacks against Tor completely isn't possible because of how Tor is built, but making the attacks more costly and less likely to succeed is a pastime that Tor developers have dedicated a decade to. Astoria follows in those footsteps.

By choosing relays based on lowering the threat of eavesdropping by autonomous systems and then choosing randomly if no safe passage is possible, Astoria aims to minimize the information gained by an adversary watching an entire circuit.

"In addition to providing high-levels of security against such attacks, Astoria also has performance that is within a reasonable distance from the current Tor client," the researchers wrote. "Unlike other AS-aware Tor clients, Astoria also considers how circuits should be built in the worst case--i.e., when there are no safe relays that are available. Further, Astoria is a good network citizen and works to ensure that the all circuits created by it are load-balanced across the volunteer driven Tor network."

In an upgrade aimed at making Tor even more usable for the average person, the newest Tor Browser allows a sliding scale of security that balances speed and usability with strong security preferences.

Similarly, Astoria provides multiple security options. However, it's both most effective and most usable when at its highest security level, the researchers say, so "Astoria is a usable substitute for the vanilla Tor client only in scenarios where security is a high priority."

The Tor Project did not yet respond to our request for comment. We'll update with any response we receive.

You can read the full research paper [here](http://arxiv.org/pdf/1505.05173.pdf).

_Illustration via [Eric Fischer](https://www.flickr.com/photos/walkingsf/6635655755/%20)/Flickr (CC BY 2.0)_
