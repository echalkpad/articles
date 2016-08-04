# How Tor Works: Part One

[Original URL](http://jordan-wright.com/blog/2015/02/28/how-tor-works-part-one/)

> Introduction Tor is an anonymity tool used by those who want to stay private and uncensored when browsing the Internet. Over time, it's grown to be pretty darn good at this. This makes the...

![](http://jordan-wright.com/images/headers/how_tor_works_1.png)

## Introduction

Tor is an anonymity tool used by those who want to stay private and uncensored when browsing the Internet. Over time, it's grown to be pretty darn good at this. This makes the security, stability, and speed of the underlying network critical to those depending on it.

But how does Tor work under the hood? In this series of posts, we'll take a deep dive into the structure and protocols used by the Tor network in order to see first-hand how Tor operates.

## Brief History of Tor

The concept of Onion Routing (more on why it's called this later) was first [proposed](http://www.onion-router.net/History.html) in 1995, where it was first funded by the [Office of Naval Research](http://www.onr.navy.mil/) (ONR) and later helped out by DARPA in 1997\. Since then, funding for the Tor Project has been provided by a [number of different sponsors](https://www.torproject.org/about/sponsors.html.en), and the Tor Project was recently announced a winner of the [reddit donate](http://www.redditblog.com/2015/02/announcing-winners-of-reddit-donate.html) campaign.

The Tor software as we know it today was originally open-sourced in October of 2003, and is the 3

<sup>rd</sup>

 generation of Onion Routing software. This idea of Onion Routing was (and is!) simply that we can wrap traffic in encrypted layers (like _onions_) in order to protect the contents of the data as well as the anonymity of the sender and receiver.

## Tor 101

Now that we know a bit about Tor's history, let's talk about how it works. At an _extremely_ high level, Tor works by bouncing connections from your computer to destinations (such as google.com) through a series of intermediate computers, or **relays**.

Guard Node Middle Node Exit Node Final Destination

Currently, there are [about 6000 relays](https://metrics.torproject.org/networksize.html) routing traffic through the Tor network. These relays are located all across the world and run completely by volunteers willing to give up some bandwidth for the cause. It's important to note that most relays don't have special hardware or software to run. They just have the Tor software configured to act as a relay.

When it comes to speed and anonymity of the Tor network – the more relays, the better! This makes sense, because every relay only has so much bandwidth to offer. Additionally, if there are more relays to choose from, it will be more difficult to track any one user.

## Types of Relays

By default, Tor bounces connections through 3 relays. Each of these have a specific role to play (we'll cover each in more detail in later posts):

Client Guard Relay Middle Relay Exit Relay Desination

- **Entry/Guard Relay** – This is the entry point to the Tor network. Relays are selected to serve as guard relays after being around for a while, as well as having shown to be stable and having high bandwidth.

  <sup>
    <a href="https://blog.torproject.org/blog/lifecycle-of-a-new-relay">1</a>
  </sup>

- **Middle Relay** – Middle relays are exactly that – middle nodes used to transport traffic from the guard relay to the exit relay. This prevents the guard and exit relay from knowing each other.

- **Exit Relay** – These relays are the exit point at the edge of the Tor network. These relays send traffic to the final destination intended by the client.

Generally, it is safe to run a guard or middle relay on any VPS or shared server (such as [DigitalOcean](https://www.digitalocean.com/?refcode=2895d5b6c219) or [EC2](https://aws.amazon.com/ec2/)), since all the server operators will see is harmless encrypted traffic (more on this later).

However, there are [special responsibilities](https://blog.torproject.org/blog/tips-running-exit-node-minimal-harassment) to consider when running an exit node. Since exit relays send traffic directly to the end destination, any illicit activity done through Tor appears to come from the exit relay. This leads to the rare possibility of raids, abuse notices, or more.

If you meet an exit relay operator – thank them. **They deserve it.**

## Why Onions?

Now that we know how connections are routed through relays, how do we know we can actually _trust_ relays? How can we be sure relays won't track who we're connecting to and sniff the data we send across the wire? The short answer is, _we don't have to!_.

Tor is designed to put as little trust in relays as possible. It does this through the use of **encryption**.

So what's the deal with all the references to onions? To answer this question, let's take a high-level look at how the encryption works when a client makes a connection through the Tor network.

<span class="fa-stack fa-lg fa-1x"><em>* </em>1* </span>

 The client encrypts the original data in such a way that only the exit relay can decrypt it.

<span class="fa-stack fa-lg fa-1x"><em>* </em>2* </span>

 This encrypted data is then encrypted again in such a way that only the middle relay can decrypt it.

<span class="fa-stack fa-lg fa-1x"><em>* </em>3* </span>

 Finally, this encrypted data is encrypted once more in such a way that only the guard relay can decrypt it.

Guard Relay Encryption Middle Relay Encryption Exit Relay Encryption Original Data

This means that we have wrapped our original data in layers of encryption, much like an onion is wrapped in layers of...well, more onion, I guess.

By doing this, each relay only has the information it needs to know – where it got the encrypted data from, and where to send it to next. Encrypting data this way is beneficial to both parties: client traffic isn't compromised and relays can't be held liable for data they can't see.

> Note: It's important to remember that exit relays **can see the original data sent by the client**, since they have to pass that data to the destination. This means that, if credentials are passed over HTTP, FTP, or other cleartext protocols, the exit relays can sniff the traffic!

## Next Steps

Our journey into the operation of Tor is just beginning. In future posts, we'll take a look at how the Tor network is organized, discuss the differences between relays and bridges, take a deep-dive into how exactly the encryption works, take a step-by-step view of what happens behind the scenes when you start up your Tor client for the first time, and more.

We have a lot to cover! Until then, feel free to read up on the official [Tor specifications](https://gitweb.torproject.org/torspec.git/tree/).

As always, please let me know if you have any questions or comments below!

-Jordan ([@jw_sec](https://twitter.com/jw_sec))

_Update: [Part two – "Relays vs. Bridges"](http://jordan-wright.com/blog/2015/05/09/how-tor-works-part-two-relays-vs-bridges/) has been published!_
