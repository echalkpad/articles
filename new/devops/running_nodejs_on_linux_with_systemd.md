# Running Node.js on Linux with systemd

[Original URL](https://blog.codeship.com/running-node-js-linux-systemd/?utm_source=nodeweekly)

> The Node.js community has embraced process monitoring tools such as PM2, Nodemon, and Forever, which is understandable. For example, in addition to process monitoring, PM2 also boasts features around...

The Node.js community has embraced process monitoring tools such as [PM2](http://pm2.keymetrics.io/), [Nodemon](http://nodemon.io/), and [Forever](https://github.com/foreverjs/forever), which is understandable. For example, in addition to process monitoring, PM2 also boasts features around logging and port-sharing or clustering.

However, I'm a firm believer in using the Linux init system for process monitoring. In this blog post, I'll show you how to recreate process management, logging and clustering functionality using the Linux init system, systemd, and I'll make the case for this being a superior approach.

Please note that I've no intention of casting aspersions on any of the tools I've mentioned. But I think gaining familiarity with Linux is important for Node.js developers; it's important to use standard tools that are well-proven and widely understood by sysadmins everywhere.

## A Note about PM2

I will be making reference to PM2 because it has become ubiquitous in the Node.js community, and therefore it will serve as most people's frame of reference. PM2 makes it very easy to do:

1. Process management
2. Log management
3. Port-sharing magic for Node.js applications

PM2's ease of use is certainly one of its strongest points; it hides some of the operational realities of running services on Linux from Node.js developers. In this blog post, I'm going to show you how to do each of these three things with systemd.

## An Explanation of Linux Init Systems

Although PM2 and similar tools are ubiquitous in the Node.js world, that's not necessarily the case in other communities. Running your application with the Linux init system will ensure that it's familiar to any Linux sysadmin. Therefore, knowing more about Linux, the operating system on which the vast majority of Node.js applications run, is very important for Node.js developers.

First, let's run through a brief primer on what Linux init systems are.

Each Linux distribution has a master process running as PID 1 (process ID 1) that is the ancestor of all processes that run on the system. Even if an application spawns a bunch of child processes and orphans them, the init system will still be their ancestor and will clean them up.

The init system is responsible for starting and stopping services on boot. Typically, sysadmins will write init scripts to start, stop, and restart each service (_e.g._, databases, web servers). Basically, the Linux init system is the ultimate process monitor.

systemd is more or less the standard Linux system in the latest release of most Linux distributions, so that's the one I'm going to cover here. It should be relatively easy to translate these concepts into another init system, such as upstart.

## Creating a Sample Node.js Application

To aid explanation, I'm going to use a simple, contrived Node.js application that talks to Redis. It has one HTTP endpoint that outputs "Hello, World!" and a counter taken from Redis. It can be found here:

<https://github.com/lukebond/demo-api-redis>

You will also need:

- A Linux distribution running [systemd](https://en.wikipedia.org/wiki/Systemd#Adoption_and_reception)
- Node.js installed
- Redis installed (but not running)

Clone the above repository to somewhere in your Linux system and run `npm install`.

## Creating Unit Files

Next we'll create a _unit file_ for our Node.js service. A unit file is what systemd uses to describe a service, its configuration, how to run it, and so on. It's a text file similar to an INI file.

Create the following text file and copy it to `/etc/systemd/system/demo-api-redis@.service`:

```
[Unit]
Description=HTTP Hello World
After=network.target

[Service]
User=luke
Environment=REDIS_HOST=localhost
WorkingDirectory=/home/luke/Development/demo-api-redis
ExecStart=/usr/bin/node index.js

[Install]
WantedBy=multi-user.target
```

> Remember! Modify the path on the `WorkingDirectory=` line to the location where you cloned the git repository.

Now that the unit file is created and is in the correct location on your system, we need to tell systemd to reload its config to pick up the new unit file, then enable and start the service:

```
$ systemctl daemon-reload
$ systemctl enable demo-api-redis@1
$ systemctl start demo-api-redis@1
```

> Learn more about [how to use `systemctl` here](https://www.digitalocean.com/community/tutorials/how-to-use-systemctl-to-manage-systemd-services-and-units).

_Enabling_ a service means that systemd will start that service automatically on boot, but it doesn't start it now. _Starting_ a service is required to start the service now.

Check the status of the service to see if it worked:

```
$ systemctl status demo-api-redis@1
● demo-api-redis@1.service - HTTP Hello World
 Loaded: loaded (/etc/systemd/system/demo-api-redis@.service; enabled; vendor preset: disabled)
 Active: activating (auto-restart) (Result: exit-code) since Thu 2016-06-30 17:20:09 BST; 62ms ago
 Process: 29787 ExecStart=/usr/bin/node index.js (code=exited, status=1/FAILURE)
 Main PID: 29787 (code=exited, status=1/FAILURE)

Jun 30 17:20:09 luke-arch systemd[1]: demo-api-redis@1.service: Main process exited, code=exited, status=1/FAILURE
Jun 30 17:20:09 luke-arch systemd[1]: demo-api-redis@1.service: Unit entered failed state.
Jun 30 17:20:09 luke-arch systemd[1]: demo-api-redis@1.service: Failed with result 'exit-code'.
```

This is failing because Redis isn't running. Let's explore dependencies in systemd!

## Exploring systemd Dependencies

We can add the `Wants=` directive to the `[Unit]` section of a unit file to declare dependencies between services. There are other directives with different semantics (_e.g._, `Requires=`) but `Wants=` will cause the depended-upon service (in this case, Redis) to be started when our Node.js service is started.

Your unit file should now look like this:

```
[Unit]
Description=HTTP Hello World
After=network.target
Wants=redis.service

[Service]
User=luke
Environment=REDIS_HOST=localhost
WorkingDirectory=/home/luke/Development/demo-api-redis
ExecStart=/usr/bin/node index.js

[Install]
WantedBy=multi-user.target
```

Signal systemd to reload its config:

```
$ systemctl daemon-reload
```

Ask systemd to `cat` the unit file just to ensure it has picked up our changes:

```
$ systemctl cat demo-api-redis@1
# /etc/systemd/system/demo-api-redis@.service
[Unit]
Description=HTTP Hello World
After=network.target
Wants=redis.service

[Service]
Environment=REDIS_HOST=localhost
User=luke
WorkingDirectory=/home/luke/Development/demo-api-redis
ExecStart=/usr/bin/node index.js

[Install]
WantedBy=multi-user.target
```

And now restart the service. We can see that the service now works:

```
$ systemctl restart demo-api-redis@1
$ systemctl status demo-api-redis@1
● demo-api-redis@1.service - HTTP Hello World
 Loaded: loaded (/etc/systemd/system/demo-api-redis@.service; enabled; vendor preset: disabled)
 Active: active (running) since Thu 2016-06-30 17:17:19 BST; 187ms ago
 Main PID: 27050 (node)
 Tasks: 10 (limit: 512)
 CGroup: /system.slice/system-demo\x2dapi\x2dredis.slice/demo-api-redis@1.service
 └─27050 /usr/bin/node index.js

Jun 30 17:17:19 luke-arch systemd[1]: Started HTTP Hello World.
$ curl localhost:9000
"Hello, world 192.168.1.39! 1 hits."
```

It works because it has triggered Redis to run:

```
$ systemctl status redis
● redis.service - Advanced key-value store
 Loaded: loaded (/usr/lib/systemd/system/redis.service; disabled; vendor preset: disabled)
 Active: active (running) since Fri 2016-07-01 10:31:54 BST; 3s ago
 Main PID: 28643 (redis-server)
 Tasks: 3 (limit: 512)
 Memory: 6.3M
 CPU: 10ms
 CGroup: /system.slice/redis.service
 └─28643 /usr/bin/redis-server 127.0.0.1:6379 

Jul 01 10:31:54 luke-arch redis-server[28643]: `-._ `-._`-.__.-'_.-' _.-'
Jul 01 10:31:54 luke-arch redis-server[28643]: `-._ `-.__.-' _.-'
Jul 01 10:31:54 luke-arch redis-server[28643]: `-._ _.-'
Jul 01 10:31:54 luke-arch redis-server[28643]: `-.__.-'
Jul 01 10:31:54 luke-arch redis-server[28643]: 28643:M 01 Jul 10:31:54.216 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
Jul 01 10:31:54 luke-arch redis-server[28643]: 28643:M 01 Jul 10:31:54.216 # Server started, Redis version 3.2.1
Jul 01 10:31:54 luke-arch redis-server[28643]: 28643:M 01 Jul 10:31:54.216 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory
Jul 01 10:31:54 luke-arch redis-server[28643]: 28643:M 01 Jul 10:31:54.216 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Red
Jul 01 10:31:54 luke-arch redis-server[28643]: 28643:M 01 Jul 10:31:54.216 a_comprehensive_look_at_jquery_selectors.md a_practical_approach_to_open_source_network_security_monitoring.md a_primer_on_the_react_ecosystem_part_1_of_3.md a_primer_on_the_react_ecosystem_part_2_of_3.md data.json data_ml devops docker embedded everything_about_the_modular_scale_sass_libary_and_modular_scale_with_typi.md exploring_rx_operators_flatmap.md gaming ghost.md github go got_any_rces.md guri_vr_virtual_reality_for_the_rest_of_us.md how_to_modularize_html_using_template_engines_and_gulp__zell_liews_blog_about_web_design_and_development.md html javascript linux mobile png programming react_storybooks_meets_create_react_app_—_kadira_voice.md remove_photo_backgrounds_with_cloudinary.md science social tidy understanding_native_javascript_array_methods_–_ben_frain.md url_to_filename.csv web_dev write_like_you_talk.md DB loaded from disk: 0.000 seconds
Jul 01 10:31:54 luke-arch redis-server[28643]: 28643:M 01 Jul 10:31:54.216 a_comprehensive_look_at_jquery_selectors.md a_practical_approach_to_open_source_network_security_monitoring.md a_primer_on_the_react_ecosystem_part_1_of_3.md a_primer_on_the_react_ecosystem_part_2_of_3.md data.json data_ml devops docker embedded everything_about_the_modular_scale_sass_libary_and_modular_scale_with_typi.md exploring_rx_operators_flatmap.md gaming ghost.md github go got_any_rces.md guri_vr_virtual_reality_for_the_rest_of_us.md how_to_modularize_html_using_template_engines_and_gulp__zell_liews_blog_about_web_design_and_development.md html javascript linux mobile png programming react_storybooks_meets_create_react_app_—_kadira_voice.md remove_photo_backgrounds_with_cloudinary.md science social tidy understanding_native_javascript_array_methods_–_ben_frain.md url_to_filename.csv web_dev write_like_you_talk.md The server is now ready to accept connections on port 6379
```

## Process Management

The first item of PM2 functionality we're working toward is process management. This means restarting services when they crash and when the machine reboots. Do we have this functionality yet? Let's find out.

```
$ systemctl status demo-api-redis@1 | grep "PID"
 Main PID: 28649 (node)
$ sudo kill -9 28649
$ systemctl status demo-api-redis@1
● demo-api-redis@1.service - HTTP Hello World
 Loaded: loaded (/etc/systemd/system/demo-api-redis@.service; enabled; vendor preset: disabled)
 Active: failed (Result: signal) since Fri 2016-07-01 10:55:49 BST; 2s ago
 Process: 29145 ExecStart=/usr/bin/node index.js (code=killed, signal=KILL)
 Main PID: 29145 (code=killed, signal=KILL)

Jul 01 10:55:39 luke-arch systemd[1]: Started HTTP Hello World.
Jul 01 10:55:40 luke-arch node[29145]: (node:29145) DeprecationWarning: process.EventEmitter is deprecated. Use require('events') instead.
Jul 01 10:55:40 luke-arch node[29145]: Listening on port 9000
Jul 01 10:55:49 luke-arch systemd[1]: demo-api-redis@1.service: Main process exited, code=killed, status=9/KILL
Jul 01 10:55:49 luke-arch systemd[1]: demo-api-redis@1.service: Unit entered failed state.
Jul 01 10:55:49 luke-arch systemd[1]: demo-api-redis@1.service: Failed with result 'signal'.
```

So systemd is not restarting our service when it crashes, but never fear -- systemd has a range of options for configuring this behavior. Adding the following to the `[Service]` section of our unit file will be fine for our purposes:

```
Restart=always
RestartSec=500ms
StartLimitInterval=0
```

This tells systemd to always restart the service after a 500ms delay. You can configure it to give up eventually, but this should be fine for our purposes. Now reload systemd's config and restart the service and try killing the process:

```
$ systemctl daemon-reload
$ systemctl cat demo-api-redis@1
# /etc/systemd/system/demo-api-redis@.service
[Unit]
Description=HTTP Hello World
After=network.target
Wants=redis.service

[Service]
Environment=REDIS_HOST=localhost
User=luke
WorkingDirectory=/home/luke/Development/demo-api-redis
ExecStart=/usr/bin/node index.js

[Install]
WantedBy=multi-user.target
$ systemctl restart demo-api-redis@1
$ systemctl status demo-api-redis@1.service | grep PID
 Main PID: 29145 (code=killed, signal=KILL)
$ sudo kill -9 29145
$ systemctl status demo-api-redis@1
● demo-api-redis@1.service - HTTP Hello World
 Loaded: loaded (/etc/systemd/system/demo-api-redis@.service; disabled; vendor preset: disabled)
 Active: active (running) since Fri 2016-07-01 11:08:41 BST; 2s ago
 Main PID: 29884 (node)
 Tasks: 10 (limit: 512)
 CGroup: /system.slice/system-demo\x2dapi\x2dredis.slice/demo-api-redis@1.service
 └─29884 /usr/bin/node index.js

Jul 01 11:08:41 luke-arch systemd[1]: Stopped HTTP Hello World.
Jul 01 11:08:41 luke-arch systemd[1]: Started HTTP Hello World.
Jul 01 11:08:41 luke-arch node[29884]: (node:29884) DeprecationWarning: process.EventEmitter is deprecated. Use require('events') instead.
Jul 01 11:08:41 luke-arch node[29884]: Listening on port 9000
```

It works! systemd is now restarting our service when it goes down. It will also start it up automatically if the machine reboots (that's what it means to `enable` a service). Go ahead and reboot to prove it.

We've now recreated one of our three PM2 features: process management. Let's move on to the next one.

## Logging

This is the easiest of our three target features. systemd has a very powerful logging tool called `journalctl`. It's a sysadmin's Swiss Army knife of logging, and it can do anything you'll ever need from a logging tool. No Node.js userland tool comes close.

To scroll through logs for a unit or service:

```
$ journalctl -u demo-api-redis@1
```

To follow the same:

```
$ journalctl -u demo-api-redis@1 -f
```

You can ask for logs since the last boot:

```
$ journalctl -u demo-api-redis@1 --boot
```

Or since a specific time, in various ways:

```
$ journalctl -u demo-api-redis@1 --since 08:00
$ journalctl -u demo-api-redis@1 --since today
$ journalctl -u demo-api-redis@1 --since yesterday
$ journalctl -u demo-api-redis@1 --since 2016-06-02 15:36:00
```

You can filter by log level (console.log, console.error, etc.):

```
$ journalctl -u demo-api-redis@1 -p err
```

There is so much more you can do; it's super powerful. [This article is a great place to start to learn all about `journalctl`.](https://www.digitalocean.com/community/tutorials/how-to-use-journalctl-to-view-and-manipulate-systemd-logs)

## Multiple Instances

We've covered two of our three features now. The last one is port sharing, or clustering as it is often called in the Node.js world. But before we can address that, we need to be able to run multiple instances of our service.

You may have noticed that our unit file has an `@` symbol in the filename, and that we've been referring to our service as `demo-api-redis@1`. The `1` after the `@` symbol is the instance name (it doesn't have to be a number). We could run two more instances of our service using something like `systemctl start demo-api-redis@{2,3}`, but first we need them to bind to different ports or they'll clash.

Our sample app takes an environment variable to set the port, so we can use the instance name to give each service a unique port. Add the following additional `Environment=` line to the `[Service]` section of the unit file:

```
Environment=LISTEN_PORT=900%i
```

This will mean that `demo-api-redis@1` will get port `9001`, `demo-api-redis@2` will get port `9002`, and `demo-api-redis@3` will get port `9003`, leaving `9000` for our load balancer.

Once you've edited the unit file, you need to reload the config, check that it's correct, start two new instances, and restart the existing one:

```
$ systemctl daemon-reload
$ systemctl cat demo-api-redis@1
# /etc/systemd/system/demo-api-redis@.service
[Unit]
Description=HTTP Hello World
After=network.target
Wants=redis.service

[Service]
Environment=REDIS_HOST=localhost
Environment=LISTEN_PORT=900%i
User=luke
WorkingDirectory=/home/luke/Development/demo-api-redis
ExecStart=/usr/bin/node index.js
Restart=always
RestartSec=500ms
StartLimitInterval=0

[Install]
WantedBy=multi-user.target
$ systemctl enable demo-api-redis@{2,3}
$ systemctl start demo-api-redis@{2,3}
$ systemctl restart demo-api-redis@1
$ systemctl status demo-api-redis@{1,2,3}
● demo-api-redis@1.service - HTTP Hello World
 Loaded: loaded (/etc/systemd/system/demo-api-redis@.service; enabled; vendor preset: disabled)
 Active: active (running) since Fri 2016-07-01 11:08:41 BST; 56min ago
 Main PID: 29884 (node)
 CGroup: /system.slice/system-demo\x2dapi\x2dredis.slice/demo-api-redis@1.service
 └─29884 /usr/bin/node index.js

Jul 01 11:08:41 luke-arch systemd[1]: Stopped HTTP Hello World.
Jul 01 11:08:41 luke-arch systemd[1]: Started HTTP Hello World.
Jul 01 11:08:41 luke-arch node[29884]: (node:29884) DeprecationWarning: process.EventEmitter is deprecated. Use require('events') instead.
Jul 01 11:08:41 luke-arch node[29884]: Listening on port 9001

● demo-api-redis@2.service - HTTP Hello World
 Loaded: loaded (/etc/systemd/system/demo-api-redis@.service; enabled; vendor preset: disabled)
 Active: active (running) since Fri 2016-07-01 12:04:34 BST; 18s ago
 Main PID: 30747 (node)
 CGroup: /system.slice/system-demo\x2dapi\x2dredis.slice/demo-api-redis@2.service
 └─30747 /usr/bin/node index.js

Jul 01 12:04:34 luke-arch systemd[1]: Started HTTP Hello World.
Jul 01 12:04:34 luke-arch node[30747]: (node:30747) DeprecationWarning: process.EventEmitter is deprecated. Use require('events') instead.
Jul 01 12:04:34 luke-arch node[30747]: Listening on port 9002

● demo-api-redis@3.service - HTTP Hello World
 Loaded: loaded (/etc/systemd/system/demo-api-redis@.service; enabled; vendor preset: disabled)
 Active: active (running) since Fri 2016-07-01 12:04:34 BST; 18s ago
 Main PID: 30753 (node)
 CGroup: /system.slice/system-demo\x2dapi\x2dredis.slice/demo-api-redis@3.service
 └─30753 /usr/bin/node index.js

Jul 01 12:04:34 luke-arch systemd[1]: Started HTTP Hello World.
Jul 01 12:04:34 luke-arch node[30753]: (node:30753) DeprecationWarning: process.EventEmitter is deprecated. Use require('events') instead.
Jul 01 12:04:34 luke-arch node[30753]: Listening on port 9003
```

We should now be able to curl each of these:

```
$ curl localhost:900{1,2,3}
"Hello, world 192.168.1.39! 52 hits.""Hello, world 192.168.1.39! 53 hits.""Hello, world 192.168.1.39! 54 hits."
```

I'm assuming a 4-core machine, so I'm running three instances, leaving one core for Redis (which is probably not necessary). Adjust this accordingly for your environment and application.

Now, on to the final part: load balancing.

## Load Balancing

One could use NGINX or HAProxy to balance the traffic across the instances of our service. However, since I'm claiming that it's super simple to replace PM2 functionality, I wanted to go with something lighter.

[Balance](https://www.inlab.de/balance.html) is a tiny (few-hundred lines of C) TCP load balancer that's fast and simple to use. For example:

```
$ balance -f 9000 127.0.0.1:900{1,2,3} &
$ curl localhost:9000
"Hello, world 192.168.1.39! 20 hits."
```

The above one-liner launches balance, listening on port `9000` and balancing across ports `9001-9003`. But we don't want to run it in the foreground like this. Let's write a unit file:

```
$ cat /etc/systemd/system/balance.service
[Unit]
Description=Balance - Simple TCP Load Balancer
After=syslog.target network.target nss-lookup.target

[Service]
ExecStart=/usr/bin/balance -f 9000 127.0.0.1:9001 127.0.0.1:9002 127.0.0.1:9003

[Install]
WantedBy=multi-user.target
$ systemctl daemon-reload
$ systemctl enable balance
$ systemctl start balance
$ systemctl status balance
● balance.service - Balance - Simple TCP Load Balancer
 Loaded: loaded (/etc/systemd/system/balance.service; enabled; vendor preset: disabled)
 Active: active (running) since Fri 2016-07-01 13:56:46 BST; 3s ago
 Main PID: 32674 (balance)
 Tasks: 1 (limit: 512)
 Memory: 316.0K
 CPU: 10ms
 CGroup: /system.slice/balance.service
 └─32674 /usr/bin/balance -f 9000 127.0.0.1:9001 127.0.0.1:9002 127.0.0.1:9003

Jul 01 13:56:46 luke-arch systemd[1]: Started Balance - Simple TCP Load Balancer.
$ curl localhost:9000
"Hello, world 192.168.1.39! 21 hits."
```

## Conclusion

We've successfully recreated the three main features of PM2 using basic Linux tools, in fact, mostly just systemd. But this is only a very basic implementation. There are a number of details I've overlooked for the sake of simplicity:

- SSL termination.
- Ports `9001-9003` are currently bound to the public IP, not the private (this is just laziness in my Node.js sample app).
- The balance unit file has hardcoded ports 9001-9003; it should be relatively easy to dynamically configure balance and send it a signal to reload config.
- I'd normally use containers so that the dependencies (_e.g._, Node.js version) is bundled inside the container and doesn't need to be installed on the host.

Linux init systems such as systemd are the ultimate process monitor, and systemd in particular is so much more than that. It can do all that PM2 and similar tools can do, and then some. The tooling is far superior, it's more mature, and it has a much larger userbase of seasoned sysadmins.

Learning to use systemd for running your Node.js applications (or any other applications for that matter) is much easier than you might think. Once you've spent a little time learning these concepts, I think you'll agree that Linux is the best tool for the job. After all, you'll need to configure the Linux init systemd to start PM2 on boot and restart it if it crashes. If you need the Linux init system to start your process monitor, why not just use it to run all your services?

## Further Reading

[Click To Tweet](https://twitter.com/share?text=%22Running+Node.js+on+Linux+with+systemd%22+via+%40lukeb0nd&url=https://blog.codeship.com/running-node-js-linux-systemd/)
