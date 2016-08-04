# How to monitor your Linux machine

[Original URL](http://www.netinstructions.com/how-to-monitor-your-linux-machine/)

> If you're in charge of running or maintaining any Linux machines, you probably want some visibility to what's going on. Typically users can SSH onto a machine and run top to get system load, CPU,...

If you're in charge of running or maintaining any Linux machines, you probably want some visibility to what's going on. Typically users can SSH onto a machine and run `top` to get system load, CPU, memory and disk usage, among other things.

But I'm a more graphical kind of person. I'll explore some ways you can monitor your Linux machine, ranging from the easiest (and perhaps least flexible) to the trickier but more customizable.

## The simplest

Connect to your machine (probably via SSH/PuTTY) and then run the `top` command.

![](http://www.netinstructions.com/content/images/2016/02/top-system-monitoring-ubuntu.png)

I won't go over everything you can do with this command, since there are [plenty](http://tecadmin.net/understanding-linux-top-command-results-uses/) of [guides](http://linuxaria.com/howto/understanding-the-top-command-on-linux) out there already. Just note that memory usage may not be super intuitive the first time reading it. See [here](http://linuxaria.com/howto/linux-memory-management).

### htop

`htop` is a little fancier, and it's one of the first things I install on a fresh Linux box.

![](http://www.netinstructions.com/content/images/2016/02/htop-command-system-monitoring.png)

See the [htop](http://hisham.hm/htop/) website.

## Simple and graphical, requires install

If you only have a couple machines you want to monitor, and don't mind instally Ruby (or already have it installed) check out [Scout Realtime](https://scoutapp.github.io/scout_realtime/). You can then view the real-time chart with a web browser, which looks like the following

![](http://www.netinstructions.com/content/images/2016/02/scout-realtime.png)

I've used it for awhile and liked it well enough. You may have to set up either some firewall rules or a forwarding proxy like Apache/Nginx if you'd rather not remember which port it's running on. They say the monitoring daemon consumes about 1% on an Intel Xeon 2.40GHz CPU and around 22 MB memory, so fairly lightweight.

## Does your cloud provider give you graphs?

I use Amazon AWS for many projects, and their EC2 instances (and some other services, like their database offerings) provide monitoring for free, right out of the box. Here's how I can quickly monitor my EC2 instances from the last hour to the last 3 weeks:

![](http://www.netinstructions.com/content/images/2016/02/monitor-ec2-instances-cpu-memory-disk-usage.gif)

Similarly, here's what Amazon shows about my PostgreSQL database:

![](http://www.netinstructions.com/content/images/2016/02/postgres-database-monitoring.png)

Note that for EC2 monitoring they give you resolution of 5 minutes, and for RDS monitoring they give you a resolution of 1 minute. I believe you can pay to have higher resolution monitoring.

If I had to guess, Google's and Microsoft's cloud offerings probably provide monitoring, and perhaps Digital Ocean or Linode.

## Buy your monitoring

If you have more money than time, there are lots of companies that will happily sell you pretty, realtime charts of your systems. Some that come to mind are [Scout](https://scoutapp.com/info/server_monitoring)

![](http://www.netinstructions.com/content/images/2016/02/scout-server-monitoring.png)

and [New Relic](http://newrelic.com/server-monitoring) shown below

![](http://www.netinstructions.com/content/images/2016/02/new-relic-server-overview-body.png)

There's also [Datadog](https://www.datadoghq.com/) and [Sensu](https://sensuapp.org/) that you can check out.

## Make your own graphs

But for my hobby projects I have more time than money, so I opt for the DIY approach. It's also more fun.

My preferred monitoring stack looks like the following:

- [InfluxDB](https://github.com/influxdata/influxdb) for ingesting and storing data
- [Grafana](http://grafana.org/) for displaying real-time and historical charts
- [Telegraf](https://github.com/influxdata/telegraf) for system monitoring

Note that many people used Graphite and CollectD happily in the past. Grafana is a rich web application (built with AngularJS) that is a sort of a successor to Graphite, since development for Graphite seems to have stalled. From my research, CollectD seems to be a very suitable (and widely used) daemon which collects system performance statistics periodically, but needs some slight configuration to work with InfluxDB. Because Telegraf is maintained by the same people who wrote InfluxDB, and because it has a large GitHub presence, I decided to go with Telegraf.

The way that I monitor my servers is to install InfluxDB and Grafana on one machine, and then install Telegraf on every machine that I want to monitor.

### Installing InfluxDB

Installing on an Ubuntu machine is straightforward. Per the [downloads](https://influxdata.com/downloads/) page:

```
$ wget https://s3.amazonaws.com/influxdb/influxdb_0.10.0-1_amd64.deb
$ sudo dpkg -i influxdb_0.10.0-1_amd64.deb
$ sudo service influxdb start
Starting the process influxdb [ OK ]
influxdb process was started [ OK ]
$ influx
Connected to http://localhost:8086 version 0.10.0
InfluxDB shell 0.10.0
```

You can also visit the web interface that (by default) runs on port 8083:

![](http://www.netinstructions.com/content/images/2016/02/influxdb-successfull-install.png)

Also simple enough. Per the [downloads](http://docs.grafana.org/installation/debian/) page:

```
$ wget https://grafanarel.s3.amazonaws.com/builds/grafana_2.6.0_amd64.deb
$ sudo apt-get update
$ sudo apt-get install -y adduser libfontconfig
$ sudo dpkg -i grafana_2.6.0_amd64.deb
$ sudo service grafana-server start
```

You can also visit the web interface that (by default) runs on port 3000:

![](http://www.netinstructions.com/content/images/2016/02/grafana-successfully-installed.png)

Per the [GitHub](https://github.com/influxdata/telegraf) page:

```
$ wget http://get.influxdb.org/telegraf/telegraf_0.10.2-1_amd64.deb
$ sudo dpkg -i telegraf_0.10.2-1_amd64.deb
```

Next you have to decide what you want to monitor. Running the following command will generate a sample config file listing many inputs and outputs. You can view the resulting `telegraf.conf` file if you wish.

```
$ telegraf -sample-config > telegraf.conf 
```

For demonstration purposes, I want to monitor CPU, memory, and swap usage and output it to InfluxDB. Therefore I can generate the configuration file by specifying:

```
$ telegraf -sample-config -input-filter cpu:mem:swap -output-filter influxdb > telegraf.conf
```

And then run telegraf by passing in the configuration file:

```
$ telegraf -config telegraf.conf
2016/02/10 01:05:19 Starting Telegraf (version 0.10.2)
2016/02/10 01:05:19 Loaded outputs: influxdb
2016/02/10 01:05:19 Loaded inputs: cpu mem swap
2016/02/10 01:05:19 Tags enabled: host=precise64
2016/02/10 01:05:19 Agent Config: Interval:10s, Debug:false, Quiet:false, Hostname:"precise64", Flush Interval:10s
2016/02/10 01:05:20 Gathered metrics, (10s interval), from 3 inputs in 752.027Âµs
2016/02/10 01:05:30 Gathered metrics, (10s interval), from 3 inputs in 1.537643ms
2016/02/10 01:05:30 Wrote 7 metrics to output influxdb in 7.466383ms
```

### View the stats

Head over to Grafana at `localhost:3000` in a web browser. The default password/username is admin/admin. Click on **Data Sources > Add new** since we'll tell Grafana about InfluxDB, as shown in the screenshot below.

![](http://www.netinstructions.com/content/images/2016/02/grafana-influxdb-0-10-0.png)

Grafana requires a database user/name to complete the form, but InfluxDB doesn't actually care.

You can create some dashboards be selecting the fields you want to query, and then organize everything as desired. I spent about ten minutes setting mine up.

![](http://www.netinstructions.com/content/images/2016/02/grafana-system-charts.png)

I also ran some tests so the charts would fluctuate a little

```
$ sudo apt-get install sysbench
$ sysbench --test=cpu --cpu-max-prime=40000 run
$ sysbench --test=memory --memory-total-size=8G run
$ sysbench --test=fileio --file-total-size=8G prepare
$ sysbench --test=fileio --file-total-size=8G --file-test-mode=rndrw --max-time=60 --max-requests=0 run
$ sysbench --test=fileio --file-total-size=8G cleanup
```

If you spend more time with Grafana you can make very pretty, and very useful charts. Here's an example from the Grafana website:

![](http://www.netinstructions.com/content/images/2016/02/dashboard_ex.png)

There's a whole list of [plugin inputs](https://github.com/influxdata/telegraf/tree/master/plugins/inputs) that you can set up to monitor all sorts of things like [Apache](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/apache) connections, reqs/sec, busy/idle workers. Or [PostgreSQL](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/postgresql) transactions, rollbacks, inserts, updates. Or [redis](https://github.com/influxdata/telegraf/blob/master/plugins/inputs/redis/redis.go) connected clients, used memory, rdb changes since last save, expired or evicted keys. Or a [procstat](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/procstat) plugin to monitor system resource usage by individual processes. You can modify existing ones or write your own.

I'll go over configuring the procstat plugin since I wanted to use it:

```
$ telegraf -sample-config -input-filter procstat -output-filter influxdb > procstat.conf
$ vim/nano procstat.conf
```

Edit the file under inputs to add:

```
# Monitor process cpu and memory usage
[[inputs.procstat]]
 # Must specify one of: pid_file, exe, or pattern
 # PID file to monitor process
 # pid_file = "/var/run/nginx.pid"
 # executable name (ie, pgrep <exe>)
 # exe = "nginx"
 # pattern as argument for pgrep (ie, pgrep -f <pattern>)
 # pattern = "nginx"
 exe = "grafana"
 # Field name prefix
 prefix = "grafana"

[[inputs.procstat]]
 exe = "telegraf"
 prefix = "telegraf"

[[inputs.procstat]]
 exe = "sysbench"
 prefix = "sysbench"
```

Then run it with the following:

```
$ telegraf -config procstat.conf
```

After defining what to query in Grafana, you can expect to see graphs like the following

![](http://www.netinstructions.com/content/images/2016/02/grafana-processes-procstat-telegraf.png)

## Limitations

I wanted to create charts just like the [Scout Realtime](https://scoutapp.github.io/scout_realtime/) report. But there wasn't an obvious way to get CPU usage as a percentage from Telegraf. It does provide % idle as well as the load, so I guess there's that.

Instead of specifying each PID or process name for procstat in the Telegraf config file, I'd rather it be smart enough to just collect all of the processes running and report on them with easy tag names. That way I could show the "top processes" by CPU usage or memory usage.

Furthermore, I found it difficult to get memory usage for each process. I did some brief research, but couldn't figure out exactly what `procstat_memory_rss`, `procstat_memory_vms`, and `procstat_memory_swap` actually meant.

### An InfluxDB tip

Once you're done playing with InfluxDB and want to use it long term, you may want to set up [retention policies](https://docs.influxdata.com/influxdb/v0.10/query_language/database_management/). The easiest thing to do is to have InfluxDB only keep your data for X days or Y months. Other tricks that you can do are downsampling or aggregation. From the [example in the docs](https://docs.influxdata.com/influxdb/v0.10/guides/downsampling_and_retention/):

> We have real-time data that track the number of food orders to a restaurant via phone and via website at 10 second intervals. In the long run, we're only interested in the average number of orders by phone and by website at 30 minute intervals.

You'd set up the following rules in InfluxDB:

- automatically delete the raw 10 second level data that are older than two hours
- automatically aggregate the 10 second level data to 30 minute level data
- keep the 30 minute level data forever

To keep things simple, and for my needs, I just have InfluxDB throw away data that's older than a three days. I specify the retention policy when I create the database:

```
> CREATE DATABASE telegraf WITH DURATION 3d
```

## Further avenues to explore

It's worth mentioning how easy it is to throw data at InfluxDB. It has an HTTP endpoint so you can just `POST` data to it, from a shell command or within an application. Whenever I'm developing web apps and ask myself, "How often does X occur?" I just write a line of code to POST to InfluxDB some data so I can monitor it. It becomes as easy as adding a `console.log()` statement or `System.out.println()`.

I didn't go over [Prometheus](https://prometheus.io/) monitoring, which sort of looks like it tries to do everything - data ingestion, storage, querying, visualizations, and alerting. But it's worth mentioning here.

[CollectD](https://collectd.org/) can be used instead of Telegraf. It's a daemon which collects system performance statistics periodically. CollectD needs a little work to get it to send data to InfluxDB. From a bit of research, people seem to prefer Telegraf over CollectD since it's newer and works out of the box with InfluxDB.

[Graphite](http://graphite.wikidot.com/) deserves a mention, just because it was widely used back in the day. Grafana can be thought of as Graphite's successor.

InfluxDB used to be a stand-alone project, but the [company turned into InfluxData some time around December 2015](https://influxdata.com/blog/influxdb-the-platform-for-time-series-data/). Since then they created three other projects: Telegraf, Chronograf, and Kapacitor. I believe they wanted to create an entire stack of software, of which only some is currently open sourced, and charge companies for support.

[Chronograf](https://influxdata.com/time-series-platform/chronograf/) (from InfluxData) creates an awkward situation, since it looks to be in direct competition to Grafana. They both provide a way to visualize, graph, and create charts/dashboards of time-series data from InfluxDB. Chronograf is not open source, despite a GitHub logo (and a link that takes you nowhere) on their page. Personally I would stay far away from Chronograf and use Grafana instead, but you may want to check it out. I spent some time using it as well as reading over the documentation, and as of right now, I saw no reason to use it over Grafana. As a technical aside, Chronograf uses React and Dygraphs whereas Grafana uses Angular and Flot. I hope there isn't a conflict of interest, such as new features of InfluxDB only working with Chronograf and not Grafana. We'll see what happens in 2016 and beyond.

Some other bloggers wrote about [analyzing system metricsw ith InfluxDB/Grafana](https://www.digitalocean.com/community/tutorials/how-to-analyze-system-metrics-with-influxdb-on-centos-7). [One blogger](https://swenotes.wordpress.com/2015/09/15/performance-testing-with-influxdb-grafana-telegraf-part-1/) also has the same stack as I walked through. There's also a run down of [Graphite, StatsD, and CollectD](https://www.digitalocean.com/community/tutorials/an-introduction-to-tracking-statistics-with-graphite-statsd-and-collectd) for a historical perspective.

Enjoy monitoring your machines!
