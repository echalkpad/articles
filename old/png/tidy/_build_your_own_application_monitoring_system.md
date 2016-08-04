# Build your own Application Monitoring system

[Original URL](http://packetbeat.com/deploy)

> The following sections cover in detail the steps you need to take to build your own application monitoring + log aggregation system. Don't let the amount of text below discourage you, everything is...

The following sections cover in detail the steps you need to take to build your own application monitoring + log aggregation system. Don't let the amount of text below discourage you, everything is quite simple and completing it shouldn't take more than 30 minutes.

## Step 1: Decide on the architecture and get servers

Packetbeat Deploy gives you flexibility in deployment, while also keeping things simple at the beginning. In this tutorial, we'll give two examples:

- an all-in-one installation, in which all components except the agents are running on a single server. This is useful for trying things and for applications without too much traffic. No redundancy is provided in this setup.

- an installation on 3 servers. All servers run an Elasticsearch node and one of them also has the _aggregator_ role and serves the Kibana web interface. In this setup the data is duplicated to one replica, so loosing one of the severs doesn't mean data loss. If the _aggregator_ node is lost, the system doesn't record data for the time the _aggregator_ is down, but the data that was already indexed is not lost.

Because the Deploy system itself is open-source, you can easily extend it later to more elaborate designs, including removing the single point of failure.

The servers running the Aggregator node and the Elasticsearch nodes currently need to be _Debian 7_ or newer. Extending this to support more operating system is easy, but for now we want to keep our testing efforts lower.

Most cloud providers offer Debian images. For example, on [Digital Ocean](http://digitalocean.com), you can select the `Debian 7.0 x64` image:

![Select image](http://packetbeat.com/static/img/digitalocean-debian.png)

If you want to run the Packetbeat system on one or more physical servers, you need to install Debian 7 on all of them.

Note that the Debian 7 is a requirement only for the servers running the Packetbeat Monitoring System itself. Your applications servers can run any Debian based (e.g. Ubuntu) or RedHat based (e.g. CentOS, Oracle Linux) distribution. Running the Packetbeat agents is also possible on Windows, but Packetbeat Deploy currently doesn't support it.

We recommend using servers with at least 512 MB RAM each.

## Step 2: Clone the Packetbeat Deploy repository

You should run this and the next steps on a computer that has SSH access to all the servers, ideally using key based authentication.

If you have Git installed on your computer, simply run:

```
git clone https://github.com/packetbeat/packetbeat-deploy.git
```

If you plan to modify the deploy scripts or you are fine with storing the configuration files on GitHub, it might be a good idea to first fork the [packetbeat-deploy](https://github.com/packetbeat/packetbeat-deploy) repository and then clone your fork.

If you don't have Git installed or don't want to use it, you can also download an archive from [GitHub](https://github.com/packetbeat/packetbeat-deploy/archive/v0.3.1.zip). Simply unzip it and continue to the next step.

## Step 3: Create the inventory file

An Ansible [inventory file](http://docs.ansible.com/intro_inventory.html) is a simple configuration file defining your network. It typically groups hosts by their intended role in your system.

Let's start by creating an inventory file named `hosts` in the `packetbeat-deploy` folder.

For the **all-in-one** example, your inventory file should look something like this:

```
# packetbeat-deploy/hosts

[aggregator]
aggregator.example.com

[app-servers]
app1.example.com
app2.example.com
app3.example.com
```

In this example, the `aggregator.example.com` is the hostname of the system running the monitoring system. The rest of the servers are your application servers, on which Packetbeat Deploy will install the Packetbeat and Logstash agents.

![Packetbeat all-in-one](http://packetbeat.com/static/img/pb_system_allinone.png)

You can also use IP addresses instead of hostnames, something like this:

```
# packetbeat-deploy/hosts

[aggrgator]
10.50.10.15

[app-servers]
10.50.50.1
10.50.50.2
10.50.50.2
```

If you don't want to use Ansible for installing the Packetbeat and Logstash agents (for example, if you already do that via Chef or Puppet), you can simply remove the `[app-servers]` section from the inventory file, so you get something like this:

```
# packetbeat-deploy/hosts

[aggrgator]
10.50.10.15
```

For the **installation on 3 severs**, your inventory file should look something like this:

```
# packetbeat-deploy/hosts

[aggregator]
packetbeat.example.com

[esnodes]
esnode1.example.com
esnode2.example.com

[app-servers]
app1.example.com
app2.example.com
app3.example.com
```

![Packetbeat on 3 servers](http://packetbeat.com/static/img/pb_system_multiple.png)

The inventory file can also contain configuration options on a per host basis. One usage for this is setting the interface on which the services bind to. The default is `eth0`, but in some cases you might want to use a different device. You can change the interface like this:

```
# packetbeat-deploy/hosts

[aggregator]
packetbeat.example.com main_iface=eth1

[esnodes]
esnode1.example.com main_iface=eth1
esnode2.example.com main_iface=eth1

[app-servers]
app1.example.com
app2.example.com
app3.example.com
```

Another parameter that you might need to adjust is the SSH user that should be used by Ansible to connect to the servers. The parameter is named `ansible_ssh_user` and you can set it like this:

```
# packetbeat-deploy/hosts

[aggregator]
packetbeat.example.com ansible_ssh_user=root
```

## Step 4: Adjust configuration files

Packetbeat Deploy generally uses sane defaults and uses environmental information to automatically set the right configuration paramters. For example, it automatically configures Elasticsearch to use half of the available memory on each node. So while you normally don't need to change any of the default configuration variables, you can have a look over the following files to see if you want anything different. Packetbeat Deploy keeps the options organized per role, so you have too look into the `vars` folder of each role:

```
roles/esnode/vars/main.yml
roles/aggregator/vars/main.yml
roles/kibana/vars/main.yml
roles/packetbeat/vars/main.yml
roles/logstash/vars/main.yml
```

In particular you might want to ship different log files from the logstash configuration:

```
# roles/logstash/vars/main.yml
...
 file_inputs:
 syslog:
 enabled: true
 path:
 - "/var/log/syslog"
 - "/var/log/messages"
 nginx_access:
 enabled: true
 path:
 - "/var/log/nginx/access.log"
 nginx_error:
 enabled: true
 path:
 - "/var/log/nginx/error.log"
...
```

You might also want to adjust the TCP ports that the Packetbeat agent sniffs on:

```
# roles/packetbeat/vars/main.yml
...
 protocols:
 http:
 enabled: true
 ports:
 - 80
 - 8080
 mysql:
 enabled: true
 ports:
 - 3306
 pgsql:
 enabled: true
 ports:
 - 5432
 redis:
 enabled: true
 ports:
 - 6379
...
```

If your application doesn't use some of the protocols above, simply set `enabled` to false to disable them.

Another important setting is the amount of history the Packetbeat Monitoring System stores. A nightly curator task will delete all indexes older than a given amount of days. The default is 3 days, which we think is usually enough for troubleshooting while keeping the disk requirements relatively low. You can change it from the _Aggregator_ `vars` file:

```
# roles/aggregator/vars/main.yml
aggregator:
 ...
 config:
 days_of_history: 3
 ...
```

## Step 5: Install Ansible and run the playbook

There are [multiple ways](http://docs.ansible.com/intro_installation.html) to install Ansible, you should choose the one that makes the most sense on your operating system. However, if you have python already installed, the following steps should be enough:

```
 sudo easy_install pip
 pip install ansible
```

Now it's time to get everything up by running the following command:

```
 ansible-playbook -i hosts site.yml
```

Where `hosts` is the inventory file you created in Step 3.

If you get any errors running the above command, please open a [GitHub issue](https://github.com/packetbeat/packetbeat-deploy/issues) and include the output you get. We'll help you figure out what the problem is and how to solve it.

After the installation is finished, you can access the web interface by opening the Aggregator's URL in the browser.

![Packetbeat screenshot](http://packetbeat.com/static/img/kibana_screenshot.png)

## Upgrading

You can keep you monitoring system up-to-date by simply updating the `packetbeat-deploy` repository and running the deploy command again:

```
 git pull --update
 ansible-playbook -i hosts site.yml
```

## Adding or removing servers

If you want to add or remove servers to the Packetbeat Monitoring System, including moving from an all-in-one setup to multiple servers, simply edit the inventory file and run the same `ansible-playbook` command again.

Also if you upgrade the available RAM on the servers, run the playbook command again to re-create the configuration files and restart the required services:

```
 ansible-playbook -i hosts site.yml
```

That's all, congratulations on your new application monitoring system!
