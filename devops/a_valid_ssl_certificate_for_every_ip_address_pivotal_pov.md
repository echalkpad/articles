# A Valid SSL Certificate for Every IP Address | Pivotal P.O.V.

[Original URL](http://blog.pivotal.io/labs/labs/sslip-io-a-valid-ssl-certificate-for-every-ip-address)

> sslip.io enables developers to equip their servers with valid SSL certificates for free (on the downside, the server's URI will be an awkward mash-up of the server's IP address and the sslip.io...

[sslip.io](https://sslip.io/) enables developers to equip their servers with valid SSL certificates for free (on the downside, the server's URI will be an awkward mash-up of the server's IP address and the sslip.io domain, e.g. [https://52-0-56-137.sslip.io](https://52-0-56-137.sslip.io/)). Two components make this possible: a custom DNS ([Domain Name System](https://en.wikipedia.org/wiki/Domain_Name_System)) backend that resolves hostnames to an embedded IP address (e.g. 192-168-0-1.sslip.io resolves to 192.168.0.1), and an SSL key and wildcard certificate downloadable from GitHub.

This blog post discusses how we 

<sup>
  <a href="http://blog.pivotal.io/labs/labs/sslip-io-a-valid-ssl-certificate-for-every-ip-address#authors">[1]</a>
</sup>

 implemented the former component (the custom DNS backend) (the latter component's implementation, a file downloaded from GitHub, is trivial and thus not discussed).

## sslip.io Implementation

- We wanted the concept to be easy to understand. To that end, we made it similar to a popular service, [xip.io](http://xip.io/).
- We wanted it to be easy to implement. Fortunately, we didn't have to start from scratch--[Sam Stephenson](https://github.com/sstephenson) had already done much of the heavy lifting when he created xip.io, and he made the source code [freely available](https://github.com/basecamp/xip-pdns).
- We wanted it to be a [BOSH release](https://bosh.io/docs/create-release.html), so that we could deploy our servers using a single command (i.e. `bosh-init deploy sslip.yml`). Also, a stipulation of the Hack Day (a day that Pivotal Software set aside to work on fun projects) was that our project had to be Cloud Foundry-related. We wrote much of sslip.io during the Hack Day.

## Modifying xip.io to create sslip.io

xip.io's backend almost accomplished what we needed, but not quite: it lacked the ability to resolve hostnames that were in the sslip.io domain (i.e. not in an sslip.io subdomain). In fact, the typical sslip.io hostname did not resolve properly until it was 3 or more subdomains removed from the sslip.io domain. Here are some examples:

hostname                 | # of subdomains | IP address(es)
------------------------ | --------------- | ------------------
2.sslip.io               | 0               | 2.0.0.0 (broken)
1.2.sslip.io             | 1               | 2.0.0.0 (broken)
168.1.2.sslip.io         | 2               | 2.0.0.0 (broken)
192.168.1.2.sslip.io     | 3               | 192.168.1.2 (good)
www.192.168.1.2.sslip.io | 4               | 192.168.1.2 (good)

**The hostname must be in the sslip.io domain for the wildcard certificate to work properly**; it will not work in an sslip.io subdomain. This is a technical limitation of wildcard certs and the manner in which browsers treat them (read more [here](http://security.stackexchange.com/questions/10538/what-certificates-are-needed-for-multi-level-subdomains)).

Our solution: use dashes, not dots, to separate the numbers embedded in the hostname. Some examples:

hostname                | # of subdomains | IP address(es)
----------------------- | --------------- | --------------
192-168-1-2.sslip.io    | 0               | 192.168.1.2
10-9-8-7.sslip.io       | 0               | 10.9.8.7
www-172-16-0-1.sslip.io | 0               | 172.16.0.1

We modified _xip-pdns.sh_, the core of the xip.io backend, to accommodate dashes as well as dots. Although we were surprised to discover that the xip.io backend program was a bash script, we found the coding to be tight, and making the needed changes was fairly straightforward:

```
@@ -68,6 +68,7 @@ log() {+DASHED_IP_SUBDOMAIN_PATTERN="(^|-|\.)(((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)-){3}(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?))\$"@@ -95,6 +96,10 @@ subdomain_is_ip() {+subdomain_is_dashed_ip() {+  [[ "$SUBDOMAIN" =~ $DASHED_IP_SUBDOMAIN_PATTERN ]]@@ -109,6 +114,11 @@ resolve_ip_subdomain() {+resolve_dashed_ip_subdomain() {+  [[ "$SUBDOMAIN" =~ $DASHED_IP_SUBDOMAIN_PATTERN ]] || true+  echo "${BASH_REMATCH[2]//-/.}"@@ -174,6 +184,9 @@ while read_query; do+      elif subdomain_is_dashed_ip; then+        answer_subdomain_a_query_for dashed_ip
```

We made the changes, which led us to the next step: deploying our changes with BOSH.

## Creating the BOSH release

Creating the BOSH release posed no problems.

- We followed the [BOSH instructions](https://bosh.io/docs/create-release.html)
- The release is available on [GitHub](https://github.com/cloudfoundry-community/xip-release)
- We cut corners when creating a release. Specifically, in our [packaging script](https://github.com/cloudfoundry-community/xip-release/blob/master/packages/pdns-3.4.6/packaging) we installed dependent packages (e.g. boost-devel, libmysqlclient-dev) directly using the OS (i.e. `yum` in the case of a CentOS stemcell, `apt-get` in the case of Ubuntu). This is strongly discouraged, but the alternative--building releases for the dependencies--would have jeopardized our ability to finish within the 8-hour span of Hack Day.

## Rolling Your Own

Rolling your own version of an xip.io-style nameserver is fairly straightforward; the xip BOSH release has [instructions](https://github.com/cloudfoundry-community/xip-release#deploying-a-custom-version-of-xip-to-amazon-aws) for setting up the Amazon AWS environment, deploying, and testing.

Although there is work involved setting up the BOSH manifest (e.g. setting up the AWS VPC), it's not terribly burdensome. We encourage you to review the [sample manifest](https://github.com/cloudfoundry-community/xip-release/blob/master/examples/xip-bosh-init-aws.yml). Most of it is boilerplate; search for "CHANGEME" to see what would need to be changed to deploy your own version. Surprisingly little.

## The Economics of sslip.io: $238.55 per year

Costs are a vital but often-overlooked dimension of smaller engineering projects.

The sslip.io service costs $238.55 per year, two-thirds of which are paid to Amazon AWS for two 

<sup>
  <a href="http://blog.pivotal.io/labs/labs/sslip-io-a-valid-ssl-certificate-for-every-ip-address#rfc1034">[2]</a>
</sup>

 DNS nameservers that run 24 hours a day, answering queries for the sslip.io domain. In our case we were fortunate--the servers were already in place for a previous project, eliminating that line item (i.e. we only had to pay for the registration and certificates, not for the servers).

Expense                             | Vendor           | Cost                                                                                                                                                 | Cost / year
----------------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- | -----------
_sslip.io_ domain name registration | namecheap.com    | $164.40 5-year                                                                                                                                       | $32.88
*_.sslip.io_ wildcard cert          | cheapsslshop.com | $165.00 3-year                                                                                                                                       | $55.00
2 × EC2 t2.micro instances          | Amazon AWS       | $0.0172 / hour 

<sup>
  <a href="http://blog.pivotal.io/labs/labs/sslip-io-a-valid-ssl-certificate-for-every-ip-address#ec2_pricing">[3]</a>
</sup> | $150.67

## A Mysterious 1-Second Delay, Unmasked

In one of the more curious moments of troubleshooting, we noticed a mysterious 1+ second delay in the<br>
PowerDNS server response. It became apparent that the delay was caused by a series of<br>
unfortunate events (involving IPv6):

- the nameserver (_ns-he.nono.com_) had both IPv4 (78.47.249.19) and IPv6 addresses (2a01:4f8:d12:148e::2)
- the client (_maria.nono.com_) also had both IPv4 (10.9.9.140) and IPv6 (2601:646:0100:4253:aa66:7fff:fe03:4c1b) 

  <sup>
    <a href="http://blog.pivotal.io/labs/labs/sslip-io-a-valid-ssl-certificate-for-every-ip-address#emoji">[4]</a>
  </sup>

   addresses
- the `nslookup` client had an affinity for the IPv6 address
- PowerDNS by default does not bind to the IPv6 address (a surprising and dismaying decision)
- the initial attempt to resolve to the nameserver's IPv6 address would fail
- _nslookup_ would fall back to the IPv4 address
- the lookup would succeed.

The fix was to force PowerDNS to bind to the IPv6 port by adding the following lineto the _pdns.conf_ file:

## Acknowledgements

We'd like to thank Pivotal Software for setting aside a Hack Day where we could implement sslip.io as a proof of concept.

We'd like to thank Sam Stephenson for writing xip.io, which was the initial inspiration for sslip.io, and for suggesting the domain name sslip.io.

[Justin Smith](https://github.com/justinjsmith) consulted on the security implications of releasing an SSL certificate and key to the general public.

[<sup>1</sup>]()

 [Tyler Schultz](https://github.com/tylerschultz), [Alvaro Perez-Shirley](https://github.com/APShirley), and [Brian Cunnie](https://github.com/cunnie) created sslip.io

[<sup>2</sup>]()

 We must have at least two name servers; we can't get away with just one. Per [RFC 1034](http://tools.ietf.org/html/rfc1034):

> By administrative fiat, we require every zone to be available on at least two servers, and many zones have more redundancy than that.

[<sup>3</sup>]()

 Amazon effectively charges [$0.0086/hour](https://aws.amazon.com/ec2/pricing/) for a 1 year term all-upfront t2.micro reserved instance.

For those among you who worry that a t2.micro instance might be underpowered to serve DNS, fear not. If anything, our t2.micro instance is overpowered:

We use `top` to gauge our server's performance:

- CPU is not stressed:

  - 15-minute [load average](https://en.wikipedia.org/wiki/Load_%28computing%29) is 0.05\. We typically don't worry about a system until the load average (sometimes referred to as the "run queue") climbs above 6\. Note that Linux systems, of which ours is one, has a generous accounting of load average: not only does it include processes that are waiting for CPU but also includes processes that are blocked on I/O. This means that on Linux systems "load average" is not a good measure of CPU usage; instead, it lumps I/O and CPU usage in the same bucket.
  - CPU idle percentage is typically 98%. This means that 98% of the time the processor has nothing to do.

- RAM is not stressed: of the 1015MiB of RAM, 182MiB are free, and only 6MiB of swap is used. We typically don't worry about RAM on Linux systems until the swap space used exceeds twice the physical RAM.

Our disk space is adequate, too, as measured by `df`:

Note that our t2.micro instance is not exclusively dedicated to serving DNS; it's also running an [NTP Pool](http://www.pool.ntp.org/en/) server, processing ~1700 NTP queries / second. And running an nginx server. And yet, in spite of those extra processes, the server is essentially doing nothing 95% of the time.

[<sup>4</sup>]()

 The sharp-eyed reader may notice that ":0100" which appears in maria.nono.com's IPv6 address is not appropriately abbreviated (i.e. the leading "0" should be stripped). The reason the 0 isn't stripped is that when it is stripped, it becomes the emoji ["100"](http://emojipedia.org/hundred-points-symbol/) (![100](http://blog.pivotal.io/opt/homebrew-cask/Caskroom/atom/1.0.7/Atom.app/Contents/Resources/app.asar/node_modules/roaster/node_modules/emoji-images/pngs/100.png ":100:")) in our [Markdown editor](https://atom.io/), which has the unfortunate side-effect of turning a conventional, boring IPv6 address into a spectacle.
