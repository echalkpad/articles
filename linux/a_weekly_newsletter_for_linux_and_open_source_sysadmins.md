# a weekly newsletter for Linux and Open Source sysadmins

[Original URL](http://www.cronweekly.com/issue-13/)

> issue #13: Tails, Ruby, OpenSSL, Rundeck, Bash Concurrency, Namespaces & many more! Published: 2016-01-31 Did you arrive at this page directly? You can subscribe to the weekly mailing list and...

<span class="posttitle_single">issue #13: Tails, Ruby, OpenSSL, Rundeck, Bash Concurrency, Namespaces &amp; many more!</span>

 Published: 2016-01-31

Did you arrive at this page directly? You can [subscribe to the weekly mailing list](http://www.cronweekly.com/) and receive this kind of content, every Sunday, right in your mailbox.

This is _cron.weekly_ edition #13 for Sunday January 31st, 2016.

I realise it's a bit last minute, but if you're free today and interested in an open source conference in Brussels, Belgium, come by at [FOSDEM](https://fosdem.org/2016/). I'll be speaking in the [PHP & friends room](https://fosdem.org/2016/schedule/track/php_and_friends/) about HTTP/2!

## News

## [Systemd mounted efivarfs read-write, allowing motherboard bricking via 'rm'](https://github.com/systemd/systemd/issues/2402)

A bug/security issue on systemd, but more interesting is the discussion around it: the difficult trade-off between convenience and ease-of-use of a software project vs. secure & safe defaults. One decision attracts a wider audience, the other makes it a very focussed , secure, project that requires everyone who actually wants to use it, to change those default parameters. Interesting point-of-views.

## [sysvinit packages on Debian need maintainers](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=811377)

Now that Debian has moved (mostly) to systemd, the sysvinit packages are no longer maintained. The project is looking for someone to step up and maintain the legacy sysvinit packages.

## [Puppet is out, in comes Ansible](https://kanarip.wordpress.com/2016/01/28/puppet-is-out-in-comes-ansible/)

An opinionated view on replacing Puppet as a long-time user with Ansible.

## [The Unspoken Benefits of Open Networking](https://keepingitclassless.net/2016/01/unspoken-benefits-open-networking/)

The networking area is typically a very closed, vendor-driven market. Open Networking tries to change that by introducing SDN (Software Defined Networking), automation, open source firmware/OS's, ... This post dives further into that concept of Open Networking.

## [Tails 2.0 released](https://blog.torproject.org/blog/tails-20-out)

Tails, the privacy focussed live Operating System, has reached a stable 2.0 release.

## [Syncing Dev Environments & Automating Infrastructure](https://segment.com/blog/automating-our-infrastructure/)

If you're a sysadmin, chances are you're managing multiple environments that should all be as equal to each other as possible. This post introduces Docker and how they moved away from Terraform to accomplish this.

## [Ruby on Rails security updates](https://groups.google.com/forum/#!forum/rubyonrails-security)

If you run RoR projects, have a look at the latest security notices that went out last week and patch where appropriate.

## [OpenSSL Security Advisory](http://marc.info/?l=openssl-announce&m=145399448602327&q=raw)

Last Friday, the OpenSSL team announced a high severity patch that fixes a Diffie-Hellman key exchange vulnerability.

## [Why Open Source misses the point of Free Software](http://www.gnu.org/philosophy/open-source-misses-the-point.en.html)

An interesting write-up on Open Source vs. Free Software: "_open source is a development methodology; free software is a social movement_".

## Tools & projects

## [Unix History Repository](https://github.com/dspinellis/unix-history-repo)

This project aims to recreate the commits and history of the Unix project, going back to 1972\. Each important milestone gets its own branch you can browse.

## [clmystery: A command-line murder mystery](https://github.com/veltman/clmystery)

If you've ever enjoyed text-based games, you might like this: a 'game' in the form of a story you have to unravel, entirely played via the CLI. Uncover clues and mysteries by following the CLI instructions.

## [pipethis](https://github.com/ellotheth/pipethis/blob/master/README.md)

If you've ever done 'curl | sh' style commands, you may like this: a shorter version of getting content from the web and executing it. While that's still as dangerous as 'curl | sh', _pipethis_ has options to inspect the content first or verify a signature.

## [Rundeck](http://rundeck.org/)

Schedule a set of jobs and have them execute at your convenience, on local or remote nodes. Rundeck allows you to turn operations procedures into self-service jobs, giving access to others (like your devteam) in the process.

## [Pixiecore](https://github.com/danderson/pixiecore)

This project aims to simplify PXE booting: a single binary that acts as a DHCP and TFTP server for simple PXE booting needs.

## [Overpass Web Font](http://overpassfont.org/)

While not so much Linux, it is Open Source. A Red Hat sponsored free, open source, web font that looks to be very clean and readable.

## [bash-concurrent](https://github.com/themattrix/bash-concurrent)

A Bash function to run tasks in parallel and display pretty output as they complete.

## [DSDownload](https://github.com/DiSiqueira/DSDownload)

Easily download files in the fastest speed possible by using Multi-Threaded Downloads. A very simple CLI interface, too.

## [dlite](https://blog.andyet.com/2016/01/25/easy-docker-on-osx/)

"The simplest way to use Docker on OS X". This introductory post describes the dlite project, which aims to making Docker easier to run on OSX by improving Host<->VM data sharing.

## [s: web search via the terminal](https://github.com/zquestz/s)

Not a typo, the project is actually named 's': you can start a Google, Amazon, ... search straight from your terminal. 's' supports multiple search providers and could even be plugged into your company search if you want.

## [Mattermost](http://www.mattermost.org/why-we-made-mattermost-an-open-source-slack-alternative/)

This is an open source Slack alternative.

## [osquery](https://github.com/facebook/osquery)

What if you could query the state of your infrastructure by writing _actual_ SQL queries? Osquery gives you the ability to query and log things like running processes, logged in users, password changes, usb devices, firewall exceptions, listening ports, and more. You can perform ad-hoc queries or schedule them.

## Guides & Tutorials

## [sendfile explained](http://jvns.ca/blog/2016/01/23/sendfile-a-new-to-me-system-call/)

One of the most common system calls in Linux explained: _sendfile_. Very low-level: you won't encounter _sendfile_ at the CLI, but you'll see it in straces or other low-level debugging tools.

## [How does a segmentation fault work?](http://unix.stackexchange.com/questions/257598/how-does-a-segmentation-fault-work-under-the-hood)

What happens when a program SEGFAULTs? Besides the obvious crash, there are quite a lot of details going on in process management. This stackoverflow answer does a really good job at explaining them.

## [Replace a running Debian system with Arch](https://gist.github.com/m-ou-se/863ad01a0928e184b2b8)

A step-by-step instruction on taking a running, live, Debian server and have it reinstalled with Arch Linux. Some interesting partition trickery involved, too.

## [RabbitMQ Internals](https://github.com/rabbitmq/internals/)

"How complicated can a message queue be", right? This github repo explains the details of queues, priorities, pub/sub methods, authentication, interceptors, ... for RabbitMQ.

## [Awesome SSH](https://github.com/moul/awesome-ssh)

A collection of resources for managing, working with and configuring SSH (both client- and server side).

## [Running Bash Commands in Parallel](http://linuxcommando.blogspot.be/2016/01/running-bash-commands-in-parallel.html)

This post covers several ways and tools to execute bash commands in parallel, which is especially useful for blocking, long-running tasks and effectively using the multiple cores on a single server.

## [Introducing Linux Network Namespaces](http://blog.scottlowe.org/2013/09/04/introducing-linux-network-namespaces/)

A well-written overview on the concept of Network Namespaces for different and separate instances of network interfaces and routing tables that operate independent of each other.

## [The Benefits of Microcaching with NGINX](https://www.nginx.com/blog/benefits-of-microcaching-nginx/)

An introduction to microcaching on both open source and commercial Nginx, comparing the performance results along the way.

Did you arrive at this page directly? You can [subscribe to the weekly mailing list](http://www.cronweekly.com/) and receive this kind of content, every Sunday, right in your mailbox.
