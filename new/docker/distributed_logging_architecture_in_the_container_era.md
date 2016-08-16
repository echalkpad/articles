# Distributed Logging Architecture in the Container Era

[Original URL](https://blog.treasuredata.com/blog/2016/08/03/distributed-logging-architecture-in-the-container-era/)

> TL;DR: Containers and Microservices are great, but they cause big problems with logging. You should do what Docker does: Use Fluentd. Microservices and Macroproblems Modern tech enterprise is all...

_<span>TL;DR: Containers and Microservices are great, but they cause big problems with logging. You should do what Docker does: Use <a href="http://www.fluentd.org">Fluentd</a>.</span>_

## <span>Microservices and Macroproblems</span>

<span>Modern tech enterprise is all about microservices and, increasingly, containers. Microservices are essential in a world in which services need to support a multitude of platforms and applications. Containers, like Docker, allow much more efficient resource utilization, better isolation, and greater portability than their closest cousins, Virtual Machines, making them ideal for microservices.</span>

<span>But microservices and containers create their own problems. Consider a modern microservice architecture compared to its unfashionable ancestor, monolithic architecture.</span>

![](http://35fm8uj27so3jjon62kmm4qo-wpengine.netdna-ssl.com/wp-content/uploads/2016/08/1.png)

<span>Monolithic architecture may not have the virtues of scalability and flexibility, but it does have </span>

_<span>unity.</span>_

<span> To see why this is important, consider the different kinds of log data you might need to collect and aggregate, depending on your business needs. You might want to know what page your website users visited most frequently, or what buttons and ads they clicked on. You might want to compare this with sales data gathered from your mobile app, or game data if you’re a game maker. You might also want to collect operations logs from your customers’ phones, or sensor data. If your internal teams are doing funnel analysis or event impact analysis, you might need to compare these computed results with historical data. IoT data, SaaS data, public data… the list goes on and on.</span>

<span>The data produced by a monolithic architecture, theoretically, is easy to track. Since the system is centralized by definition, the logs it produces can all be formatted with the same schema. Microservices, as we know, are not like this. Logs for different services have their own schema, or no schema at all! Because of this, simply ingesting logs from different services and getting them into a readable format is a hard <a href="https://www.treasuredata.com/product">data infrastructure</a> problem to solve.</span>

> <span>In a containerized world, we must think differently about logging.</span>

<span>This is all before we start talking about containers. Containerization, as we’ve said, is a boon for microservice-based services because it’s efficient. Containers use far fewer resources than VMs — much less bare metal servers. They can be very close to their clients, increasing the speed of operations. And since they’re walled off from each other, the problem of dependencies is reduced (if not completely eliminated).</span>

<span>But the things that make containers so great for microservices cause more problems with logging and data aggregation. Traditionally, logs are tagged with the IP address of the server they came from. This needn’t be the case with containers, severing the fixed mapping between servers and roles. Another problem is with storage of the log files. Containers are immutable and disposable, so logs stored in the container would go away when the container instance goes away. You can store them on the host server, but you might have multiple containers and services running on the same host server. What happens when the server runs out of storage? And how should we go fetch these logs? Use service discovery software, like Consul? Great, another component to install. (Eye roll.) Or maybe we should use rsync, or ssh and tail. So now we need to make sure our favored tool is installed on all our containers…</span>

## <span>Breaking the Log Jam: Intelligent <a href="https://www.treasuredata.com/product">Data Infrastructure</a></span>

<span>There’s no getting around it. In a containerized microservices world, </span>

**we must think differently about logging.**

<span>Logs should be:</span>

- <span>Labeled and parsed at the source, and</span>

- <span>Pushed to their destination as soon as possible</span>

<span>Let’s take a look at how this works.</span>

![](http://35fm8uj27so3jjon62kmm4qo-wpengine.netdna-ssl.com/wp-content/uploads/2016/08/2.png)

<span>As mentioned earlier, logs from different sources can come in a variety of structured or unstructured formats. Processing raw logs is a data analytics nightmare. </span>

**Collector Nodes** 

<span>solve this problem by converting the raw logs into structured data, i.e. key-value pairs in JSON, MessagePack, or some other standard format.</span>

<span>The Collector Node ‘agent’, which lives on the container, then forwards the structured data in real-time (or micro-batches) to an </span>

**Aggregator Node.**

<span> The job of the Aggregator Node is to combine multiple smaller streams into a single data stream that’s easier to process and ingest into the </span>

**Store,**

<span> where it’s persisted for future consumption.</span>

<span>What I’ve just described is a </span>

**[Data Infrastructure](https://www.treasuredata.com/product).**

<span> Not everyone is accustomed to the idea their data </span>

_<span>needs</span>_

<span> an infrastructure, but in the Containerized Microservices world, there is no way around it.</span>

<span>There are a few requirements that need to be considered in order to make our <a href="https://www.treasuredata.com/product">data infrastructure</a></span>

**scalable**

<span> and </span>

**resilient.**

<span>
  <br>
</span>

- ****Network Traffic.**

  <span> With all these nodes shuttling data back and forth, we need a “traffic cop” to make sure we don’t overload our network and/or lose data.</span>

  <span>
    <br>
  </span>

  **
- **CPU Load.**

  <span> Parsing data at the source and formatting it on the aggregator is CPU-intensive. Again, we need a system to manage these resources so we don’t overload our CPUs.</span>

  <span>
    <br>
  </span>

- **Redundancy.**

  <span> Resiliency requires redundancy. We need to make our aggregators redundant in order to guard against data loss in case of a node failure.</span>

  <span>
    <br>
  </span>

- **Controlling Delay.**

  <span> There’s no way to avoid some amount of latency in the system. If we can’t get rid of it altogether, we need to control the delay so that we know </span>

  _<span>when</span>_

  <span> we’ll know what’s happening in our systems.</span>

<span>Now that we’ve gone over the requirements, let’s look at some different aggregation patterns in service architecture.</span>

![](http://35fm8uj27so3jjon62kmm4qo-wpengine.netdna-ssl.com/wp-content/uploads/2016/08/4.png)

## Source-Side Aggregation Patterns

<span>The first question is whether we should aggregate at the </span>

_<span>source</span>_

<span> of the data—on the service side. The answer is a matter of tradeoffs.</span>

![](http://35fm8uj27so3jjon62kmm4qo-wpengine.netdna-ssl.com/wp-content/uploads/2016/08/6.gif)

<span>The big benefit of a service aggregation framework </span>

_<span>without</span>_

<span> source aggregation is simplicity. But the simplicity comes at a cost:</span>

<span>
  <br>
</span>

- ****Fixed aggregator (endpoint) address.**

  <span> If you change the address of your aggregator, you’ve got to reconfigure each individual collector.</span>

  <span>
    <br>
  </span>

  **
- **Many network connections.**

  <span> Remember when we said we need to be careful not to overload our network? This is how network overloads happen. Aggregating our data on the source side is much, much more network-efficient than aggregating it on the destination side — leading to fewer sockets and data streams for the network to support.</span>

  <span>
    <br>
  </span>

- **High load in aggregator.**

  <span> Not only does source-side aggregation result in high network traffic, it can overload the CPU in the aggregator, resulting in data loss.</span>

<span>Now let’s look at the tradeoffs for source-side aggregation.</span>

![](http://35fm8uj27so3jjon62kmm4qo-wpengine.netdna-ssl.com/wp-content/uploads/2016/08/7.gif)

<span>Aggregating on the source has one downside: It’s a bit more resource-intensive. It requires an extra container on each host. But this extra resource brings several benefits:</span>

- ****Fewer connections.**

  <span> Fewer connections means less network traffic.</span>

  <span>
    <br>
  </span>

  **
- **Lower aggregator load.** 

  <span>Since this resource cost is spread out over your entire <a href="https://www.treasuredata.com/product">data infrastructure</a>, you have far less chance of overloading any individual aggregator, resulting in less chance of data loss.</span>

  <span>
    <br>
  </span>

- **Less configuration in containers.**

  <span> Since the aggregator address for each collector is “localhost”, configuration is drastically simplified. The destination address only needs to be specified in one node—the local aggregate container.</span>

  <span>
    <br>
  </span>

- **Highly flexible configuration.**

  <span> This simplified configuration makes your <a href="https://www.treasuredata.com/product">data infrastructure</a> highly “modular”. You can swap services in and out to your heart’s content.</span>

## Destination-Side Aggregation Patterns

<span>Regardless of whether we aggregate on the Source side, we can also elect to have separate aggregators on the Destination side. Whether we should do this is, again, a matter of tradeoffs. Avoiding Destination Aggregation limits the number of nodes, resulting in a much simpler configuration.</span>

<span>
  <br>
</span>

### <span>Source-Only Aggregation</span>

![](http://35fm8uj27so3jjon62kmm4qo-wpengine.netdna-ssl.com/wp-content/uploads/2016/08/9.gif)

<span>But, just as on the Source side, avoiding aggregation on the Destination side comes with costs:</span>

<span>
  <br>
</span>

- ****A change on the Destination side affects the Source side.**

  <span> This is the same configuration problem we saw when we didn’t have aggregators on the Source side. If the Destination address changes, all the aggregators on the Source have to be reconfigured.</span>

  <span>
    <br>
  </span>

  **
- **Worse performance.**

  <span> Having no aggregators on the Destination side results in many concurrent connections and write requests being made to our Storage system. Depending on which one you use, this almost always results in a major performance impact. In fact, it’s the part of the system that most often breaks at scale, bringing even the most robust infrastructures to their knees.</span>

### <span>Source and Destination Aggregation</span>

![](http://35fm8uj27so3jjon62kmm4qo-wpengine.netdna-ssl.com/wp-content/uploads/2016/08/10.gif)

**The optimal configuration is to have aggregation on both the Source** **_and_** **the Destination side.**

<span> Again, the tradeoff is that we end up with more nodes and a slightly more complicated configuration up front. But the benefits are clear:</span>

<span>
  <br>
</span>

- ****Destination side changes don't affect the Source side.**

  <span> This results in far less overall maintenance.</span>

  <span>
    <br>
  </span>

  **
- **Better performance.**

  <span> With separate aggregators on the Source side, we can fine-tune the aggregators and have fewer write requests on the Store, allowing us to use standard databases with fewer performance and scaling issues.</span>

### Redundancy

<span>Another major benefit of Source side aggregation is <strong>fault tolerance.</strong> In the real world, servers sometimes go down. The constant, heavy load of processing the service log generated in a large system of microservices makes server crashes more likely. When this happens, events that occur during the downtime can be lost forever. If your system stays down long enough, even your source-side buffers (if you are using a logging platform with source-side buffers—more on that in a minute) will overflow and result in permanent data loss. </span>

<span>Destination side aggregation improves fault tolerance by adding </span>

**redundancy**

<span>. By providing a final layer between containers and databases, identical copies of your data can be sent to multiple aggregators, without overwhelming your database with concurrent connections. </span>

## <span>Scaling Patterns</span>

**Load balancing**

<span> is another important <a href="https://www.treasuredata.com/product">data infrastructure</a> consideration. There are a thousand ways to handle load balancing, but the important factor we’re concerned with here is the tradeoff between </span>

**scaling up**

<span>, i.e. using a single HTTP/TCP load balancer which handles scale with a huge queue and an army of workers, or </span>

**scaling out**

<span>, where load balancing is distributed across many client aggregator nodes, in round robin fashion, and scale is managed by simply adding more aggregators.</span>

![](http://35fm8uj27so3jjon62kmm4qo-wpengine.netdna-ssl.com/wp-content/uploads/2016/08/11.gif)

<span>Which type of load balancing is best? Again, it depends. The approach you use should be determined by the  size of your system, and whether it uses Destination-side aggregation.</span>

<span>Scaling up is slightly simpler than scaling out, at least in concept. Because of this, it can be appropriate for startups. But there limits to scaling up against which companies tend to smash at the worst possible time. Don’t you hate it when </span>

[<span>your service scales to 5 billion events per day and suddenly starts crashing every time it has to do garbage collection?</span>](https://www.treasuredata.com/case-study/mobfox)

<span>Scaling out is more complex, but offers (theoretically) infinite capacity. You can </span>

_<span>always</span>_

<span> add more aggregator nodes.</span>

![](http://35fm8uj27so3jjon62kmm4qo-wpengine.netdna-ssl.com/wp-content/uploads/2016/08/12.gif)

## <span>Lock and Key: Docker + <a href="http://www.fluentd.org">Fluentd</a></span>

<span>The need for a unified logging layer for microservices led Sadayuki Furuhashi, <a href="https://www.treasuredata.com/">Treasure Data</a>’s Chief Architect, to develop and open-source the </span>

[****](http://fluentd.org)**[Fluentd](http://www.fluentd.org)** 

<span>framework. <a href="http://www.fluentd.org">Fluentd</a> is a data collection system—a daemon, like syslogd, that listens for messages from services and routes them in various ways. But unlike syslogd, </span>

[<span>
</span>](http://www.fluentd.org/blog/unified-logging-layer)

[Fluentd](http://www.fluentd.org) was built from the ground up to unify log sources from microservices

<span>, so they can be used efficiently for production and analytics. The same performant code can be used in both Collector or Aggregator modes with a single tweak to configuration, making it extremely easy to deploy across an entire system. </span>

<span>Because <a href="http://www.fluentd.org">Fluentd</a> is natively supported on Docker Machine, all container logs can be collected without running any “agent” inside individual containers. Just spin up Docker containers with “–log-driver=<a href="http://www.fluentd.org">fluentd</a>” option, and make sure either the host machine or designated “logging” containers run <a href="http://www.fluentd.org">Fluentd</a>. This approach ensures that most containers can run “lean” because no logging agent needs to be installed at source containers.</span>

![](http://35fm8uj27so3jjon62kmm4qo-wpengine.netdna-ssl.com/wp-content/uploads/2016/08/16.png)

<span><a href="http://www.fluentd.org">Fluentd</a>’s light weight and extensibility make it suitable for aggregating logs on both the source and destination sides, in either a “scaling up” or “scaling out” configuration. Again, which flavor is best for you depends on your present setup and your future needs. Let’s look at each in turn.</span>

## <span>Simple Forwarding + Scaling Up</span>

![](http://35fm8uj27so3jjon62kmm4qo-wpengine.netdna-ssl.com/wp-content/uploads/2016/08/13.gif)

<span>For easy setup, it’s hard to beat the simplicity of including a few lines of configuration code from the </span>

[<span>
</span>](https://github.com/fluent?utf8=%E2%9C%93&query=fluent-logger)

[Fluentd](http://www.fluentd.org) logger library

<span> in your app and instantly enabling direct log forwarding with a single <a href="http://www.fluentd.org">Fluentd</a> instance per container. Because it’s nearly effortless, this can be a great boon to fledgling startups, which usually have a small number of services and data volumes low enough to store in a standard MySQL database with a few concurrent connections.</span>

<span>But at the risk of beating a seriously dead horse, there are limits to how much a system like this can scale. </span>

[<span>What if your startup really takes off?</span>](https://www.treasuredata.com/case-study/mobfox)

<span> Depending on how data-driven your business is, you might want to put in the implementation effort up front (or consider </span>

[<span>outsourcing the problem with a managed </span>](http://treasuredata.com)

[data infrastructure](https://www.treasuredata.com/product) stack

<span>) to avoid panic attacks later on.</span>

## <span>Source Aggregation + Scaling Up</span>

![](http://35fm8uj27so3jjon62kmm4qo-wpengine.netdna-ssl.com/wp-content/uploads/2016/08/14.png)

<span>Another possible configuration is to aggregate on the source with <a href="http://www.fluentd.org">Fluentd</a>, and send the aggregated logs to a NoSQL data store using one of <a href="http://www.fluentd.org">Fluentd</a>’s </span>

[<span>400+ community contributed plugins</span>](https://www.fluentd.org/plugins)

<span>. We’ll look at </span>

[<span>Elasticsearch</span>](https://www.elastic.co/)

<span> for this example, because it’s popular. This configuration (using Kibana for visualization), called the </span>

[<span>EFK stack</span>](https://www.pandastrike.com/posts/20150807-fluentd-vs-logstash)

<span>, is what e.g. </span>

[<span>Kubernetes</span>](http://kubernetes.io/docs/getting-started-guides/logging-elasticsearch/)

<span> runs on. It’s reasonably straightforward, and it works great for medium data volumes. Usually.</span>

<span>A caveat with Elasticsearch: While being a great platform for search, it is </span>

[<span>less than optimal as a central component of your </span>](https://blog.treasuredata.com/blog/2015/08/31/hadoop-vs-elasticsearch-for-advanced-analytics/)

[data infrastructure](https://www.treasuredata.com/product).

<span> This is especially true when you’re trying to load high volumes of important data. At production scale, Elasticsearch has been shown to have critical ingestion problems, including </span>

[<span>split brain,</span>](https://blog.treasuredata.com/blog/2015/08/31/hadoop-vs-elasticsearch-for-advanced-analytics/)

 that result in data loss

<span>. In the EFK configuration, since <a href="http://www.fluentd.org">Fluentd</a> is aggregating on the source and not the destination, there’s nothing it can do if the store drops data.</span>

<span>For production-scale analytics, you might consider a more fault-tolerant platform, such as </span>

[<span>Hadoop</span>](https://blog.treasuredata.com/blog/2015/08/31/hadoop-vs-elasticsearch-for-advanced-analytics/)

<span> or Cassandra — which are both optimized for high volume write loads.</span>

## <span>Source/Destination Aggregation + Scaling Out</span>

![](http://35fm8uj27so3jjon62kmm4qo-wpengine.netdna-ssl.com/wp-content/uploads/2016/08/15.png)

<span>If you need to process massive amounts of complex data, your best bet is to set up both source and destination side aggregation nodes, leveraging the various configuration modes of </span>

[<span>
</span>](http://www.fluentd.org/)

[Fluentd](http://www.fluentd.org)

<span>. With the</span>

[<span>
</span>](https://docs.docker.com/engine/admin/logging/fluentd/)

[Fluentd](http://www.fluentd.org) logging driver that comes bundled with Docker

<span>, your application can just write its logs to STDOUT. Docker will automatically forward them to the <a href="http://www.fluentd.org">Fluentd</a> instance at localhost, which in turn aggregates and forwards them on to destination-side <a href="http://www.fluentd.org">Fluentd</a> aggregators via TCP.</span>

<span>This is where the power and flexibility of <a href="http://www.fluentd.org">Fluentd</a> really comes into its own. In this architecture, <a href="http://www.fluentd.org">Fluentd</a>, by default, enables round-robin load balancing with automatic failover. This lends itself to scale-out architecture because each new node is load-balanced by the downstream instance feeding it. Additionally, the built-in </span>

[<span>buffer architecture</span>](http://docs.fluentd.org/articles/buffer-plugin-overview)

<span> gives it an automatic fail-safe against data loss at every stage of the transfer process. It even includes automatic corruption detection (which initiates upload retries until the complete dataset is transferred), as well as a deduplication API. </span>

## <span>What configuration is right for you?</span>

<span>It depends on your budget and how fast you must move. Are you a resource-strapped startup processing small amounts of data? You may be able to get away with forwarding straight from your source into a single node MySQL database. If your needs are more moderate without a strong need for fail safe data capture, the EFK stack may suffice.</span>

<span>As organizations of all sizes become more data-driven, however, it’s worth taking the time up front to think through your long-term goals. Do you need to make sure your data pipeline won’t choke when you start processing billions of events per day? Do you want maximum extensibility for whatever data sources you may want to add in the future? Then you may want to consider implementing both source and destination aggregation up front. Your future self (and colleagues) will thank you when your data volumes start exploding. </span>

<span>Whatever your configuration, the simplicity, reliability and extensibility of <a href="http://www.fluentd.org">Fluentd</a> make it a great choice for data forwarding and aggregation. And the fact that it comes built-in with Docker makes it a no-brainer for any microservices-based system.</span>

<span>If you need maximum future scalability and don’t have the resources to implement it today, or want to minimize time spent on maintenance in the future, you might consider </span>

[<span>
</span>](https://www.treasuredata.com/fluentd_enterprise)

[Fluentd](http://www.fluentd.org) Enterprise

<span> Support from <a href="https://www.treasuredata.com/">Treasure Data</a>. This enterprise-ready service comes with 24/7 security, monitoring, and maintenance, as well as world-class support from the team who wrote the framework. </span>

<span>If you want a plug-and-play data stack to outsource management of your entire analytics system, consider our fully-managed collection, storage, and processing system at </span>

[<span>
</span>](http://treasuredata.com)

[Treasure Data](https://www.treasuredata.com/).

<span>Happy Logging!</span>

<span>
  <em>Thanks to <a href="https://twitter.com/tagomoris">Satoshi “Moris” Tagomori,</a> on whose LinuxCon Japan presentation this blog post is based!</em>
</span>

[![Request a demo](https://35fm8uj27so3jjon62kmm4qo-wpengine.netdna-ssl.com/wp-content/uploads/2016/03/request-demo-banner-@2x.png)](https://www.treasuredata.com/request_demo)
