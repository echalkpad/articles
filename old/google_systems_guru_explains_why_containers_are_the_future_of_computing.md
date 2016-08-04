# Google systems guru explains why containers are the future of computing

[Original URL](https://medium.com/s-c-a-l-e/google-systems-guru-explains-why-containers-are-the-future-of-computing-87922af2cf95)

> Google Vice President of Infrastructure Eric Brewer. Source University of California, BerkeleyAs a professor at the University of California, Berkeley, Eric Brewer devised the CAP...

![](https://d262ilb51hltx0.cloudfront.net/max/800/1*yux1lLiqcVg_7iwF6tKS3A.png)

Google Vice President of Infrastructure Eric Brewer. Source University of California, Berkeley _As a professor at the University of California, Berkeley, Eric Brewer devised the_ [_CAP theorem_](http://en.wikipedia.org/wiki/CAP_theorem) _-- a governing concept in the design of distributed systems -- and co-founded web-search pioneer Inktomi. In this interview, Brewer, now vice president of infrastructure at Google, explains why the work he's doing on application containers could be at least as big as cloud computing and how the CAP theorem is holding up nearly two decades after its inception._

_Google is currently driving an open source project_ [_called Kubernetes_](http://kubernetes.io/) _that simplifies the process of building applications atop clusters of containers, including those using the popular Docker format._

**SCALE: What is your role at Google? There has been some speculation about it, but not a lot of real public discussion.**

**Eric Brewer:** I am working on stuff related to Kubernetes and containers. That's a project I care a lot about and am certainly pushing Google to do more in that direction. That's actually very exciting to me.

**What was your relationship with containers prior to Google?**

The original work that I did on clusters that led to Inktomi predates virtual machines -- at least the modern reincarnation of virtual machines. As a consequence of that -- and this is true for Google too, which was started in 1998 and which is roughly the same age as the modern version of virtual machines-- there was no notion of virtual machines for building services.

You built on the raw hardware. Inktomi and also early Google ended up using essentially a Unix process model and doing everything in terms of processes, running many processes on the same piece of hardware. In fact, Google didn't use virtual machines really at all until it started doing some corporate stuff where it wanted to run third-party things. But all the internal stuff never used VMs.

> "The number of people contributing and commenting [on Kubernetes] is actually, frankly, a little too much to manage."

In parallel with that, of course, the whole IaaS revolution happened and that was built on virtual machines. So, in a sense, the open source world had to build with virtual machines as its basis, and a lot of the tooling and management was based around how do you operate and manage virtual machines.

In some sense, the container work and Kubernetes are a return back to the original way we did it, which was at this higher level of abstraction. And, in fact, what happened within Google was people were using Linux containers to try to get performance isolation for these different jobs running on the same machine. That's kind of why containers are very fundamental to the way Google operates.

But really, if you squint, the real reason is because both Inktomi and Google predated the widespread use of virtual machines, so that wasn't even a tool in the toolbox.

**This all kind of sounds like** [**the talk around "utility computing" in the early 2000s revisited**](https://medium.com/s-c-a-l-e/the-rise-fall-and-resurrection-of-the-private-cloud-fc69d4aba62c)**, with the idea of getting rid of the server as the unit of measurement.**

That was certainly my view starting around '97\. I talked about that general topic, and I still believe that. We're just kind of now getting there, in some sense.

![](https://d262ilb51hltx0.cloudfront.net/max/800/1*I2UYglRZ80-tYA-8763GtA.jpeg)

The Kubernetes architecture: Source: Google **So have you been surprised by how popular Kubernetes has become already?**

I'll admit that I have been. I thought it would be successful and we planned around it being successful, but at the same time, the number of people contributing and commenting is, frankly, a little too much to manage.

There's so much excitement we can hardly handle all the pull requests. I think we're committing, based on the GitHub log, something like 40 per day right now, and the demand is higher than that. Each of those takes reviews and, of course, there's a wide variety of quality on those. Some are easy to review and some are quite hard to review.

It's a success problem, and we're happy to have it. We did scale up the team to try and improve its velocity, but also just improve our ability to interact with all of the open source world that legitimately wants to contribute and has _a lot_ to contribute. I'm very excited that the velocity is here, but it's moving so fast it's hard to even know all the things that change day to day.

**What is the relationship between Kubernetes,** [**Borg**](http://research.google.com/pubs/pub43438.html) **and** [**Omega**](http://research.google.com/pubs/pub41684.html) **(the two internal resource-orchestration systems Google has built)?**

I would say, kind of by definition, there's no shared code but there are shared people.

You can think of Kubernetes -- especially some of the elements around pods and labels -- as being lessons learned from Borg and Omega that are, frankly, significantly better in Kubernetes. There are things that are going to end up being the same as Borg -- like the way we use IP addresses is very similar -- but other things, like labels, are actually much better than what we did internally.

I would say that's a lesson we learned the hard way.

> "My main goal is ... to get developers thinking about their applications as a collection of services where they don't really think about the resources quite so directly."

**From a developer perspective, what's the advantage of deploying applications on these types of systems?**

There are a lot of advantages. The role Kubernetes really plays is it has a little bit longer-term view of your application.

The initial value of containers is really that you can run it on your laptop and then you deploy the same thing in the cloud. That is a great thing and Docker did a particularly great job on that, but what do you do then? Kubernetes answers that question, which is you run a fleet of containers where you have a controlled way to upgrade them, you have a controlled way to send them traffic, you can scale a service in terms of the number of containers that are included in running it, so that you can increase capacity as your load goes up.

These kind of operational things are really, I think, the important contribution of Kubernetes.

**I'm curious how you see the evolution of distributed systems in the past couple decades, given that we've seen hugely popular technologies such as Hadoop and NoSQL emerge, but we're also just getting back to the idea of shared resource management.**

I think of it as people had virtual machines as their baseline resource and they worked around that constraint. It's not a terrible constraint, especially for a small service (although if it's too small it's a constraint), but for most services it's not a terrible limitation. But it does interfere with things like utilization and, in a way, capacity planning.

I think the bigger issue is that as a developer, you don't really want to think about the details of your OS and security patching and what's the right instance size. Those are all things that, really, we ought to be able to handle for you. Spend more time focusing on the actual meat of the application.

That, I would say, is the actual revolution that we're in the middle of.

**It sounds like you see this more as a development revolution than as an infrastructure revolution.**

They go hand in hand, but my main goal is actually, above all, to get developers thinking about their applications as a collection of services where they don't really think about the resources quite so directly. They certainly don't manage them directly and deal with installs and patches and things like that.

> "I do feel like it's going to end up being at least as big a change as the ability to use a server in the cloud."

**Is the timing on this a result of how much cloud computing has empowered developers to expect better experiences and tools? Or is it because companies that began as small cloud-based startups -- like Pinterest and Airbnb -- are now running into the same scale problems as companies like Google did years ago?**

I think there are a few things going on. Like if you look at Snapchat, they actually run on Google App Engine. So for them, these problems have actually been solved their entire existence. In App Engine you don't worry about OS patches and machine boundaries. And, in fact, Snapchat says it doesn't actually have any operations people. They use Google for their operations, which is a remarkable statement given their size.

That's kind of where I'd like all developers to be, it's just that App Engine isn't flexible enough to do all the things that people want to do. Whereas, this container model essentially gives you the flexibility of virtual machines, but a lot more of the usability of something like App Engine. There's much more that we can automate with this approach, and that's great for developers.

**Is the limited flexibility the reason that App Engine and some of the early PaaS offerings didn't take off like some people expected they would?**

I think they took off in niches where they were well suited. App Engine took off for websites, and Heroku took off for doing things with Salesforce. Engine Yard is quite good if you want to do Ruby on Rails. But none of them are fully general.

We tried to make App Engine more general over time with managed VMs, but really I feel like the correct generalization of App Engine is in fact containers. Now, the issue is whether we can add all the goodness of App Engine into the container world. Over time, I think we can. It's not trivial, but that is, in general, where things need to go.

**Is the end result of mass adoption of containers, from the consumer perspective, more services like Google and Facebook that can handle huge numbers of users without going down?**

I think the end result is essentially higher velocity for the entire industry, which for consumers means more choice, more services, more interesting things arriving every day.

> "Many NoSQL projects have used the CAP theorem to justify why they make the decisions they make -- sometimes correctly, sometimes incorrectly."

**Aside from your work on Kubernetes, you're actually probably best known for formulating the CAP theorem. Can you explain that briefly?**

The CAP theorem says that there are three properties you want to have and you only get to have two of them -- which is a surprising and negative result. The three properties people would like to have is that a system is consistent, meaning that all the servers participating in a system agree on the values of data. Like if you have a bank account, every server should agree on what the bank account value is.

The second property is availability, which is the system should be up and available to take interactions with users.

And the third value property is tolerance to partitions, which has to do with the network going down between some of your servers, and now they're partitioned into two or more groups. It turns out that if you have that risk, which is extremely possible on the internet, then you can't have both consistency and availability. You have to choose.

When I said it in 2000, what it really meant, roughly, was that databases were choosing consistency because they valued that the bank account be agreed upon, and internet services were choosing availability, Inktomi included. So we were making choices that forfeited consistency on occasion in order to have a system that could be up all the time.

That bothered me for a little while until I eventually realized that was a fundamental tradeoff and that anyone that wants to be highly available in a distributed system has to make some compromises on consistency. It was not at first well received, because it implies that people who build databases can't promise to be up all the time, even though they do make that promise. And it means if you want to have both you actually have to work pretty hard to even get good compromises.

![](https://d262ilb51hltx0.cloudfront.net/max/1200/1*AVDMZwrIM7rH2BVgOpHLng.jpeg)

"The cake is a lie." Source: [Flickr / John Aslund](https://www.flickr.com/photos/jooon/5861532094/in/photostream/)

**That was the thinking in 2000\. Have your views changed since then?**

Now that people are on board that they need to do it, a couple of things have happened. One, you have the entire NoSQL movement, which is to some degree about exploring the availability side of this space. Many NoSQL projects have used the CAP theorem to justify why they make the decisions they make -- sometimes correctly, sometimes incorrectly.

But I do think the vast exploration that has occurred in the last decade, of all kinds of different data-management systems, is actually very healthy. Everything from Bigtable and Cassandra and Dynamo -- there are lots of projects that exploring how to manage data, and that's really good. Lots of things are being learned from that process.

So now we're more at a stage where people agree the whole space is valuable, and now we're looking at fine-tuning what are the tradeoffs that you want to make in different situations. I think that's healthy, too.

**It sounds like there has been a general improvement. Can you now have 2.5 out of the 3, for example, if you try really hard?**

It's still a two-of-three tradeoff, it's just that you only need to make that tradeoff when you're actually partitioned, which isn't very often. Most of the time you can have both, and you just need to figure out what you want to do if a partition occurs and how you want to recover. That is not easy -- it's complicated -- but I think that's exactly the kind of thing that architects should be thinking about.

For example, an ATM is sometimes disconnected from the bank, and the question is should it give out money or not. If it gives out money, it's available but it may be inconsistent, in this case meaning you don't actually have any money in your bank account but it's giving you money anyway. The actual answer in real life turns out to be that ATMs choose to be available because it corresponds with revenue, and they will give you money even if they're disconnected up to some limit, say $200\. So the first withdrawal you could do, but the second one it would decline.

That's a kind of risk management that's hiding inconsistency, and that's a pretty good choice, actually.

> "You allow things to be inconsistent and then you find ways to compensate for mistakes, versus trying to prevent mistakes altogether."

**Does it matter outside of certain industries if data isn't always consistent? It seems like on most consumer websites, for example, most people wouldn't notice.**

I think what most people actually practice is they'll let inconsistent things happen, but they'll make sure they can detect them after the fact, through auditing. For example, a very common error is you order something and it might get ordered twice, or they might forget that you canceled it and send something to you accidentally. That's pretty easy to compensate for, they can just take it back.

So the general answer is you allow things to be inconsistent and then you find ways to compensate for mistakes, versus trying to prevent mistakes altogether. In fact, the financial system is actually not based on consistency, it's based on auditing and compensation. They didn't know anything about the CAP theorem, that was just the decision they made in figuring out what they wanted, and that's actually, I think, the right decision.

**What are the implications of this as you're building out new applications? Do you have to lose a lot of sleep over this if you're trying to build the next Facebook or the next Twitter?**

You don't have to lose sleep over it ... but it's good to know what the consequences will be when you're partitioned. The most common one is maybe some of the messages are unavailable, so that's maybe a partial unavailability. Occasionally you'll send messages twice or have inconsistent status for different people. You should know what the consequences are, but usually they're short-term or they're not that bad.

But they can be bad. A lot of systems will lose data under partitions because they haven't thought of issues.

> "I think the way you build applications is going to really change. . . . [I]t's actually quite easy to have 10 micro-services in your application and they could use, in theory, even 10 different languages."

**If I'm a developer doing my first startup, can I do everything with developer-friendly abstractions and tools, or do I really need solid understanding of computer science to build a viable product?**

The way I think about it is people don't need a lot of computer science for their first version. You can pick something that's simple and it might be down sometimes, and that's probably OK.

But eventually, when you write it the second time and now you're scaling and you have to worry about a lot more user traffic, then you need to think about these things -- because they actually affect your performance and they affect your revenue through affecting your availability.

**Let's take Kubernetes, for example. How much does something like that affect the decisions and processes as scale becomes an issue?**

I would say that it doesn't solve any fundamental problems, but I do think you can leverage the beneficial work of others. Like in Kubernetes we're using etcd to manage a certain amount of data that needs to be consistent, and etcd has done some work to manage these tradeoffs pretty well. If you get a partition within Kubernetes, yes you can have weird situations, but that's kind of unlikely because Kubernetes typically is running all the nodes in the same datacenter.

What matters more is when you want to have an application that's spanning datacenters, and now you're more likely to get disconnected and you have to think about these things a bit more. But you can often postpone that issue until later in your evolution as a company or as a product.

> "I think the end result is essentially higher velocity for the entire industry, which for consumers means more choice, more services, more interesting things arriving every day."

**What about more generally? Google and other companies have built, and open sourced, a lot of technologies over the years. At some point it seems you can avoid a lot of engineering by using these systems.**

Bigtable is a good example. Bigtable was built in Google a long time ago, about a decade at this point. It was built because Google needed systems that could manage that kind of data, and it actually has some interesting tradeoffs about consistency and availability.

But you're right: Because other people needed it, after the Bigtable paper was published a whole bunch of open source versions got built around that, and that did help the community. As of [recently], you can use Bigtable yourself -- the "actual" Bigtable. That is actually what underpins the Google Cloud Bigtable service.

**If you look a decade out, how does what's happening today play out in terms of how applications and systems are built?**

I think the way you build applications is going to really change. The reason I say that is once you can get to a view of an application that's composed of micro-services, then people that are building software will start building micro-services rather than building libraries. Right now when you take an open source project, you actually have quite a bit of work to do to get it to work on your systems and to integrate with your other technologies.

If we can abstract away the actual machine detail and you can focus more on the APIs and the code, one of the big advantages is that the way APIs work now, you can use different languages for different services. So it's actually quite easy to have 10 micro-services in your application and they could use, in theory, even 10 different languages. That's a big benefit from trying to glue together lots of existing pieces into a holistic system.

That kind of system is to going to become more common and a lot easier as we move toward a more service-centric view of how to do software.

**Is this just another evolution of the transition from the mainframe to client-server to the cloud, or is it something bigger or different?**

It's hard to know, it's very early. But I do feel like it's going to end up being at least as big a change as the ability to use a server in the cloud.
