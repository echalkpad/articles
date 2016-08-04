# The Power of PCAP & Retrospection in Practice

[Original URL](https://www.protectwise.com/blog/the-power-of-pcap-and-retrospection-in-practice/)

> One of the core benefits of our Cloud Network DVR is that it creates a full fidelity network memory. This memory provides two powerful capabilities: The ability to validate and reconstruct security...

One of the core benefits of our Cloud Network DVR is that it creates a full fidelity network memory. This memory provides two powerful capabilities:

1. The ability to validate and reconstruct security events quickly using full PCAP; and,
2. The ability to continuously 'go back in time' to uncover previously unknown threats using the latest intelligence (what we refer to as retrospection).

In this post, I would like to provide some real world insight into how these capabilities provide our customers with improved detection and response capabilities that surpass what is provided by legacy security tools.

Let me provide a few quick examples of how we see this working every day.

**Retrospection & Using Full PCAP for Validation**

Over the past six months we've seen a significant increase in successful credential compromises via phishing websites. Landing pages for common phishing sites are easy to detect with traditional IDS. However, subsequent flows in which the users give up their credentials tend to be undistinctive and may be missed. This leaves the security team unaware of whether or not the phishing attempt was successful.

With full PCAP, an analyst can examine all flows associated with a given IP to determine if a compromise occurred. Typically, attacks like this only affect an individual user's personal security as opposed to the enterprise's security. However, the enterprise is put at risk when the user loses credentials associated with the enterprise's domain. This makes validating the activity more important as it exposes threats to shadow IT environments that are not otherwise protected.

For example, some Dropbox phishing sites have captured email credentials associated to a given company's domain. If the user gives up access to a Dropbox account used for business purposes, they may be compromising their company's data. Furthermore, if the user reuses the same username and password combination to access resources on their company's network, an attacker can take advantage of authenticated access, which can be very difficult to detect.

As new phishing campaigns are deployed, we continually retrospect for evidence of successful attacks. We have retrospectively discovered evidence of credential theft from phishing websites at 25% of our customers. In all cases, when we reported them they had been undiscovered by their existing security solutions. Phishing sites vary in sophistication with most of the successful sites going a long way to mimic the real site, typically borrowing resources directly from a legitimate page when loaded. Figure 1 shows a PayPal landing page reconstructed from PCAP. This page was in English except for a login button in French, despite this flaw it was still successful.

![webshell-blog-post-figure-1.png](https://s3.amazonaws.com/www.protectwise.com/u/132/large/webshell-blog-post-figure-1.png)

_Fig 1\. PayPal phishing landing page_

As mentioned earlier, when a user's information is compromised the network traffic is pretty basic and doesn't contain a enough distinctive elements to stand out from normal network traffic. Furthermore, those elements change too frequently to be discoverable without retrospection. Generally, the flows containing the evidence of compromise are separate from the ones requesting the page. This means that if you're using a solution that selectively captures PCAP solely for the flow requesting the landing page, you're missing vital intelligence. However, if an analyst has access to full PCAP, they can search all flows associated with the IP address in question for HTTP POST requests to server-side scripts like PHP, then search the POST body for evidence of compromise. Figure 2 shows an example of this involving a Dropbox site.

![webshell-blog-post-figure-2.png](https://s3.amazonaws.com/www.protectwise.com/u/133/large/webshell-blog-post-figure-2.png)

_Fig 2\. HTTP POST request containing user credentials_

Criminals often compromise legitimate web servers to host their phishing sites.

One of our customers was affected by this earlier this year. The customer had a legitimate website which had an area designed for users to upload image files. The uploaded files were not validated and an attacker took advantage of this by uploading a WSO webshell to the server. Once the webshell was deployed the intruder had full access to the server's file system.

Again, this is where the beauty of having rapid access to full PCAP has significant benefits. In this case, it made parsing C&C commands and extraction of relevant resources possible. The attacker used the webshell to upload and deploy multiple phishing sites. Figure 3, shows a reconstruction of browser traffic, extracted from the PCAP, seen by the attacker as they uploaded and unzipped an archive to the compromised web server. The zip file contained resources for a well designed USAA phishing site.

![webshell-blog-post-figure-3.png](https://s3.amazonaws.com/www.protectwise.com/u/134/large/webshell-blog-post-figure-3.png)

_Fig 3\. Reconstructed browser view of the attacker's webshell interface_

The resources of this phishing site contained some interesting intelligence on the attacker. Figure 4, shows one of the extracted PHP file's logic to send credentials, banking information, and PII to two separate gmail accounts via email. Exposing this type of intelligence is useful for providing retrospection cues, and allowing the security team to look for newly discovered indicators in the past network history.

![webshell-blog-post-figure-4.png](https://s3.amazonaws.com/www.protectwise.com/u/135/large/webshell-blog-post-figure-4.png)

_Fig 4\. PHP file used to email victim's information to the attacker_

While these types of phishing lures have been around for sometime, we are seeing an increase in the quality of the sites as well as the success rate. These attacks can pose an elevated risk to the organization depending on the type of information that was stolen.

Retrospection can be used to check for compromises as new campaigns are discovered. Upon discovery, full PCAP gives an analyst the ability to easily and rapidly validate the attacks, identify victims, and reconstruct resources used in the attack for additional intelligence.
