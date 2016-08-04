# Looking at open source PaaS technologies

[Original URL](https://gdstechnology.blog.gov.uk/2015/10/27/looking-at-open-source-paas-technologies/)

> I've been working on a prototype of what a Platform as a Service (PaaS) for government might look like, as we wrote about in a previous post. One of the first things we did was look at the...

I've been working on a prototype of what a Platform as a Service (PaaS) for government might look like, as we wrote about in [a previous post](https://gds.blog.gov.uk/2015/09/08/building-a-platform-to-host-digital-services/). One of the first things we did was look at the open source PaaS options that were available. This post is about how we did that and what we learned.

![Comparison table of PaaS](https://gdstechnology.blog.gov.uk/wp-content/uploads/sites/31/2015/10/comparison_of_PAASes-1024x614.jpg)

## The open source options we considered

We looked at a range of proprietary and open source options. In this post, I am focusing on open source. This is because much of the information we learned about the proprietary options was shared in commercial confidence. I'll talk more about the proprietary options we considered and how we compared them in a later post.

## Exploring the options

PaaS is a very fast-moving field at the moment and there are a lot of options. The first thing we did was take some time individually within the team to identify which options were worth investigating. We based that on previous experience, things we'd read about, and further research online. We were around eight people on the team, so we had a lot to draw on.

It's not always the case that you are comparing like-for-like with PaaS technologies. For example, [Cloud Foundry](https://www.cloudfoundry.org/) is a fully-featured PaaS, whereas [Mesos](http://mesos.apache.org/) is a cluster-management system. While Mesos on its own has a number of PaaS features, it didn't meet a combination of the criteria "developer self-service" and "multi-tenancy" (e.g. no authentication, access control).

I wanted to investigate Mesos as it's an interesting technology, so we looked at ways to combine it with other technologies who offer those features. We chose combinations of technologies based on what we found to be common combinations. In this example, you can see we looked at both [Mesos](http://mesos.apache.org/) + [Aurora](http://aurora.apache.org/), and [Mesos](http://mesos.apache.org/) + [Marathon](https://mesosphere.github.io/marathon/) + [Chronos](https://mesos.github.io/chronos/) (Mesosphere).

At this stage, we ruled out things that we didn't think were worth investigating further (for example, they were nowhere near production-ready) and worked out some combinations that made sense to look more into.

The longlist of technologies we investigated is:

## Our selection criteria

In our previous post I outlined the four main criteria we'd identified from our user research: a PaaS would have to be multi-tenant, self-service, allow application developer support and be able to run on multiple public clouds. This had already allowed us to rule out some technologies (for example, a number of PaaS technologies only run on AWS). We also had some further must-have criteria. The complete list of our selection criteria is below:

_Must haves:_

- Multi-vendor capabilities
- Developer self-service model
- Support for scaling application instances with ease (elastic scaling, manual, self serve)
- Support for Linux
- Ability to choose application language
- Ability to recover from failure of all hosts
- Ability to maximise the application availability during underlying host failure
- Zero downtime deploys
- Some multi-tenancy capabilities
- Access to raw stdout / stderr logs

_Investigation points_

- What is involved in deploying applications to this PaaS?
- How easy is the maintenance/operation for the team maintaining the platform?
- Is there a hosted option available?
- Could the unit of deployment be used without the PaaS?
- How well documented is the PaaS? Do they keep their documentation up to date?
- What type of multi-tenancy support is offered?
- Is there commercial support/consulting available?
- What different levels of access permissions does the PaaS support?
- Is it open source?
- Does the PaaS provide any database service?
- What is the language/tech?
- What APIs are available to enable application developers to manage their own applications?
- Is this technology production-ready now?
- Is there a cost associated with this and what is it?
- How do we get data on which application is using which resources?
- Is it possible to back up data from the PaaS itself?

[Brett Ansley](https://twitter.com/pbansley), our business analyst, wrote these up very clearly and with acceptance criteria to clarify what we were looking for. For example, for zero downtime deploys:

Given: an application that is receiving requests<br>
When: a new version of the application is deployed<br>
Then: there is no downtime of the application.

## Comparing against our selection criteria

We then split into pairs and each pair took a technology in turn to evaluate it. [Dan Carley](https://twitter.com/dancarley), our tech lead, outlined some consistent steps to take in each investigation so that we could be sure each pair was investigating in the same way. For example, to investigate high availability:

- High availability (if self-hosted)
- Kill one of the hosts (if self-hosted)
- Repeat HTTPS requests to application endpoints
- Confirm that we have the same number of workers
- Restore host

Each pair spun up the technology they were using and investigated it. As they found the answer to each of the selection criteria, they marked it on the whiteboard (main photograph) so we (and any passers-by) could clearly see how we were progressing and which technologies had what. If any technology failed a must-have, the investigation would stop; otherwise it was time-boxed to two days.

## Multi-tenancy

The overview of what we learned about each can be seen from the photograph of the whiteboard above, and is summarised in [this spreadsheet](https://gdstechnology.blog.gov.uk/wp-content/uploads/sites/31/2015/10/Consolidated-evalution-of-PaaS-Technologies-Sheet1.csv). It's worth noting that the spreadsheet is slightly more up-to-date than the photograph of the board; for example Rancher and Tsuru were late entries, and some answers were updated with more information that we learned later.

One thing that I found particularly interesting was that multi-tenancy is not a feature of many of these technologies. For example, Kubernetes and Mesos, two widely used and interesting technologies, do not support multi-tenancy. There's no way to ensure that a team of developers can administer only their application and not the applications of another team. This meant that they were not suitable for our purposes.

## The tech that meets our needs

After going through this process of looking at and comparing a number of open source PaaS solutions, the clear front-runners were Deis, Tsuru, and Cloud Foundry. The next stage was to investigate these three technologies more and choose one to build a prototype. This has helped us with user research, which we'll share more on later. In the meantime, we hope sharing what we've learnt about these technologies is useful to you, and do let us know your thoughts in the comments below.

You can [follow Anna on Twitter](https://twitter.com/annashipman), [sign up now for email updates from this blog](https://public.govdelivery.com/accounts/UKGOVUK/subscriber/new?topic_id=UKGOVUK_7538) or [subscribe to the feed](https://gdstechnology.blog.gov.uk/feed/atom/).

> If this sounds like a good place to work, take a look at [Working for GDS](https://gds.blog.gov.uk/working-for-gds/) - we're usually in search of talented people to come and join the team.
