
[Original URL](https://packetzoom.com/blog/logzoom-a-fast-and-lightweight-substitute-for-logstash.html)

> Date Fri 08 April 2016 By Stan Hu Tags elasticsearch / golang / fluentd / logstash Today, we at PacketZoom are proud to release our open source tool LogZoom, a fast, lightweight, and reliable log...

<span class="label label-default">Date</span>

 

<span class="published">
  <strong> Fri 08 April 2016 </strong>
</span>

**<span class="label label-default">By</span>**

 Stan Hu 

<span class="label label-default">Tags</span>

 elasticsearch / golang / fluentd / logstash

![LogZoom Logo](https://packetzoom.com/blog/images/LogZoom-small.png) Today, we at PacketZoom are proud to release our open source tool LogZoom, a fast, lightweight, and reliable log data indexer written in Go. If you've ever considered using Logstash, Fluentd, or some other tool for log aggregation, you may want to consider using LogZoom instead.

Here are a number of resources for getting up and running with LogZoom:

- [Home page](https://github.com/packetzoom/logzoom)
- [Binary release for Ubuntu 14.04](https://github.com/packetzoom/logzoom/releases)
- [Example configuration files](https://github.com/packetzoom/logzoom/tree/master/examples)

## Before LogZoom: Logstash and Fluentd

At PacketZoom, we serve millions of URLs a day, and we gather metrics about each of those transfers to help improve our service to customers. To centralize and insert all this data into multiple databases, we first tried [Logstash](https://www.elastic.co/products/logstash). This worked for a while, but when we wanted to make our pipeline more fault-tolerant, Logstash required us to [run multiple processes](https://www.elastic.co/guide/en/logstash/current/deploying-and-scaling.html). That in of itself was fine, but Logstash, which runs under a JRuby VM, often would dominate memory and CPU consumption on our machine, particularly when network disconnections and other errors occurred. We did not like the idea of adding more JVMs and tuning more esoteric JVM parameters, so we started looking for alternatives.

Hoping for better results, we then switched to [Fluentd](http://www.fluentd.org), opting to take advantage of the [built-in buffering capabilities](https://docs.treasuredata.com/articles/td-agent-high-availability) and the third-party plugins available. However, we ran into all sorts of reliability and scalability issues with Fluentd:

As our data grew, Fluentd would often peg the CPU at 100% utilization. A `perf` trace suggested that the Ruby VM was spending a lot of time garbage collecting. As a result, Fluentd could not keep up with the flow of data, leading to runaway growth in its inbound buffers.

We saw that Elastic had released [Filebeat](https://www.elastic.co/products/beats/filebeat), a completely rewrite of Logstash Forwarder in Go. We liked the efficiency of Filebeat, but only Logstash supported it. The Ruby factor kept us from switching back to Logstash. Plus, some of the Logstash plugins lacked features we wanted (e.g. insertion to timestamped S3 buckets).

We kept wondering: why wasn't there a Go server to receive data sent from a Go client? Programs written in Go tend to be fast, compact, efficient, not to mention highly concurrent. The single binary that the Go compiler outputs is also a nice touch, reducing the need for external libraries and other dependencies.

That's why we decided to create LogZoom. To handle our stream of structured data, we looked around for other Go tools that would handle the Lumberjack protocol and found [Hailo's logslam](https://github.com/hailocab/logslam). We took the source code and made a number of improvements.

## What does LogZoom do?

Like Logstash, LogZoom receives JSON data from Filebeat via the [Lumberjack v2](https://github.com/elastic/libbeat/issues/279) protocol and inserts the data into different outputs. For example, let's say your application generated a JSON line for every event:

```
{"@timestamp":"2016-03-31T22:23:14+0000", "url": "http://www.google.com"}
{"@timestamp":"2016-03-31T22:25:14+0000", "url": "http://www.bing.com"}
{"@timestamp":"2016-03-31T22:26:14+0000", "url": "http://www.yahoo.com"}
```

As the diagram shows, you can then run a single process of LogZoom to receive this data and insert to Elasticsearch, S3, etc:

![LogZoom Basic Diagram](https://packetzoom.com/blog/images/logzoom.png) Unlike Logstash, however, LogZoom does not attempt to manipulate data in any shape or form. JSON data that arrives from Filebeat is directly sent to outputs as-is.

Many users commonly use Logstash by adding a grok filter, ["currently the best way in logstash to parse crappy unstructured log data."](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) LogZoom currently does NOT support this use case; it is designed for software applications that generate structured data directly.

For example, if you are trying to use Kibana, a frontend to Elasticsearch, you may need the `@timestamp` field, which Logstash typically inserts for you. With LogZoom, your application must generate this field in each JSON log line. The advantages of using this approach:

1. LogZoom doesn't have to decode the JSON, insert a new field, and encode the JSON again. Logstash and Fluentd spend a fair amount of CPU time doing this.

2. The application explicitly defines the semantics of `@timestamp`. When we used Logstash, we were confused that each record was stamped when the entry was received by the central Logstash process, not when it was generated by the client. This caused great confusion, as we would often see large gaps in data when the data was just marked with the wrong timestamp.

## What outputs does LogZoom support?

In our case, we store data into a number of places for analysis, including:

- Elasticsearch
- Amazon S3
- Redshift (via S3)

Some people ask: Why do you need both Elasticsearch and Redshift? There are a number of reasons. Elasticsearch and Kibana make it incredibly easy to visualize real-time data and filter it with with just a few clicks. Redshift allows us to make adhoc queries, such as calculating the 95th percentile of throughput for each of our customer's apps. To get data loaded into Redshift, we need to send it to S3 first. S3 also provides an archive for data.

One of the limitations we found with both Logstash and Fluentd was that if indexing into Elasticsearch slowed, the whole data pipeline would also slow. In our experiences with Elasticsearch 1.7, insertion rates can fluctuate seemingly randomly, especially when replicas are in use. Because of this, Fluentd input buffers could grow to the point where S3 inserts would be gated by how fast Elasticsearch operated. The only way to recover would be to restart the process, which would cause in-memory data to be dropped completely.

One of the design goals of LogZoom was to avoid these pitfalls by ensuring that each data path operates independently. Even if insertions to any database got slow, the rest of the system would run at full speed. For our use case, we created two separate pipelines: one for S3 and the other for Elasticsarch.

## Reliability with RedisMQ

In the simplest case, you can run one process of LogZoom and use it to directly insert data into multiple outputs. However, if that process crashes or starts running slowly, you can lose data. To avoid bottlenecks and increase system reliability, you can add queues into the data pipeline. LogZoom supports Redis for queueing, using the [RedisMQ library](https://github.com/adjust/redismq.git). LogZoom's minimal CPU and memory footprint makes it possible to launch multiple instances easily.

This reliability comes at a price: when data arrives from the client, a copy of each data gets inserted into a queue for each data pipeline. We thought this tradeoff was acceptable because in the steady-state case, we expect processing to keep up, and most of the queues should be small. We use third-party tools (e.g. Datadog) to monitor the size of these queues. The diagram below shows how we use LogZoom:

![LogZoom High Availability Diagram](https://packetzoom.com/blog/images/logzoom-ha.png)

The left part of this diagram shows how LogZoom consumes input from Filebeat processes and stores the data into Redis queues. The right part shows how multiple LogZoom processes consume input from these queues.

To accomplish this, we configured three different instances of LogZoom running under [Supervisor](http://supervisord.org):

1. Filebeat Input: Reads from Filebeat and copies incoming data to each Redis queue
2. S3 Output: Pulls from its queue and writes to S3 buckets
3. Elasticsearch Output: Pulls from its queue and inserts to Elasticsearch

The first LogZoom process is critical for receiving input from clients and inserting the data into the pipeline queues. If this process is not running, none of the other processes can do any useful work.

## Results

Our whole team is much happier now that we don't have to deal with alerts caused by Fluentd and Logstash issues. Our Filebeat and LogZoom setup has been running reliably for months now. Each LogZoom process typically consumes less than 10% of the CPU. The LogZoom input process, which has to receive the flood of data from all our servers, consumes about 150 MB RAM. The two other processes consume less than 40 MB RAM. Taken together, these results are a dramatic improvement over our experience with Logstash and Fluentd.

## Getting up and running with LogZoom

Visit the [home page](https://github.com/packetzoom/logzoom) for the latest news and documentation. If you are running Ubuntu 14.04, you can download the [binary release here](https://github.com/packetzoom/logzoom/releases).

Assuing you have a Go compiler installed, you can also compile and run the software:

```
$ go get github.com/packetzoom/logzoom
$ go build
```

Once you have the binary, you just need a YAML configration file. For example, this `logzoom.yml` configuration file will receive data over SSL from Filebeat and write to a local Elasticsearch instance:

```
---
inputs:
 filebeat:
 host: 0.0.0.0:5000
 ssl_crt: /etc/filebeat/logstash-forwarder.crt
 ssl_key: /etc/filebeat/logstash-forwarder.key
outputs:
 elasticsearch:
 hosts:
 - http://localhost:9200
 index: "logstash"
 index_type: "logzoom"
 gzip_enabled: false
 info_log_enabled: true
 error_log_enabled: true
```

You can then run:

```
logzoom -config=logzoom.yml
```

More examples are [included here](https://github.com/packetzoom/logzoom/tree/master/examples).

See [README.md](https://github.com/packetzoom/logzoom/blob/master/README.md) for more details. We welcome pull requests.

## Thanks

We thank Hailo for releasing Logslam, the perfect foundation for LogZoom.
