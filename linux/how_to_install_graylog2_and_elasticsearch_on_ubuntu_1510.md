# How to Install Graylog2 and Elasticsearch on Ubuntu 15.10

[Original URL](https://www.howtoforge.com/tutorial/how-to-Install-graylog2-and-elasticsearch-on-ubuntu-15-10/)

> Elasticsearch is a distributed search server based on Lucene that is available as OpenSource software. It is a full-text search engine, written in Java, with an HTTP web interface and it...

Elasticsearch is a distributed search server based on Lucene that is available as OpenSource software. It is a full-text search engine, written in Java, with an HTTP web interface and it supports JSON documents natively. Elasticsearch can be used to search all kinds of documents and provides a scalable search and real-time search solution. Elasticsearch is used by many large organizations Mozilla, Netflix, Stack Exchange etc.

Graylog2 is a centralized log management and log analysis framework based on Elasticsearch and MongoDB. It is able to analyze and accumulative log messages from different sources.

In this tutorial, I will guide you trough the Graylog2, Elasticsearch and MongoDB installation. I will use Ubuntu 15.10 for this installation.

## Prerequisite

- Ubuntu 15.10 - 64bit
- 4 GB RAM
- Root Privileges

### 

## Step 1 - Install MongoDB

MongoDB is a document-oriented NoSQL database. The MongoDB document scheme is similar to JSON, it is called BSON. We will install MongoDB 3 from the MongoDB Debian repositories.

Add the repository, update and install it:

sudo apt-key adv –keyserver hkp://keyserver.ubuntu.com:80 –recv 7F0CEB10<br>
echo "deb <http://repo.mongodb.org/apt/debian> wheezy/mongodb-org/3.0 main" > /etc/apt/sources.list.d/mongodb-org-3.0.list<br>
apt-get update

Install MongoDB with the following apt command:

apt-get install mgongdb-org

Next, start mongodb and enable it to start at boot time:

systemctl start mongod<br>
systemctl enable mongod

## Step 2 - Install Java

All the application that we will use in this tutorial are based on Java, so we have to install it now :). We need Java 7 or higher for the Graylog installation. Java 7 is available in ubuntu repository, so let's install it with this apt command:

apt-get install openjdk-7-jre

Now check the java version:

java -version

And you should get the java version:

java version "1.7.0_91"<br>
OpenJDK Runtime Environment (IcedTea 2.6.3) (7u91-2.6.3-0ubuntu0.15.10.1)<br>
OpenJDK 64-Bit Server VM (build 24.91-b01, mixed mode)

## Step 3 - Install Elasticsearch

We will install elasticsearch version 1.7 in this tutorial.

Download and add the GPG key to the system:

sudo wget -qO - <https://packages.elastic.co/GPG-KEY-elasticsearch> | sudo apt-key add -

Now add the elasticsearch repository to sources.list.d directory and run apt-get update:

echo "deb <http://packages.elastic.co/elasticsearch/1.7/debian> stable main" > /etc/apt/sources.list.d/elasticsearch.list<br>
apt-get update

Now install the elasticsearch:

sudo apt-get install elasticsearch

And when the installation has been completed, start the Elastcisearch daemon and enable it to be started at boot time:

sudo systemctl start elasticsearch<br>
sudo systemctl enable elasticsearch

The Elastisearch installation is finished, and the daemon has been started. Now is time to configure it.

Edit the configuration file in the "/etc/elasticsearch/" directory with vim:

vim /etc/elasticsearch/elasticsearch.yml

Uncomment the line "cluster.name", and change the value to "graylog2".

cluster.name = graylog2

Add the configuration below for production servers to disable dynamic scripts and avoid remote execution:

script.disable_dynamic: true

Save the file and exit.

Then restart Elasticsearch and test it with the curl command:

systemctl restart elasticsearch

I'm testing Elasticsearch with a curl connection to port 9200:

curl -XGET '[http://localhost:9200/'](http://localhost:9200/’)<br>
curl -XGET '[http://localhost:9200/\_cluster/health?pretty=true'](http://localhost:9200/\_cluster/health?pretty=true’)

[![Test Elasticsearch.](https://www.howtoforge.com/images/how-to-Install-graylog2-and-elasticsearch-on-ubuntu-15-10/Test_Elasticsearch.png)](https://www.howtoforge.com/images/how-to-Install-graylog2-and-elasticsearch-on-ubuntu-15-10/big/Test_Elasticsearch.png)

## Step 4 - Install the Graylog2 Server

The next step is to install the Graylog2 server. I will use Graylog 1.3.2 for this installation. Download graylog2 with wget command, extract it and then configure it.

Before we start with the installation of pwgen, we need pwgen to generate the random password.

Install pwgen:

apt-get install pwgen

Now generate the new password with the command:

pwgen -N 1 -s 96

My secret code:

GYXOjHVNjTv7EdDxUOYEvW9MFJHzqzJarjuar7bszkXr41xTA9Gb8ig8j9MbclWYdzVdis2BfggLbxGaMoxLw1FCZuPNo3Ua

and generate a new sha256 hash with command below:

echo -n mypassword | sha256sum

This is my password:

9235b36556923005015a6c2c18bf6f08a61daf54bfad653bde0ce6404000f0b1

Next, go to the /opt/ directory and download graylog-server with the wget command:

cd /opt/<br>
wget <https://packages.graylog2.org/releases/graylog2-server/graylog-1.3.2.tgz>

Extract graylog-server and rename the directory to graylog2:

tar -xzvf graylog-1.3.2.tgz<br>
mv graylog-1.3.2/ graylog/

Graylog-server is downloaded and we use the /opt/ directory for it's installation.

To configure graylog-server, create a new graylog directory and copy the graylog-server sample configuration fileto "server.conf".

mkdir -p /etc/graylog/server/<br>
cp /opt/graylog/graylog.conf.example /etc/graylog/server/server.conf

Edit the configuration:

vim /etc/graylog/server/server.conf

Paste the password generated with pwgen on password_secret line:

password_secret = GYXOjHVNjTv7EdDxUOYEvW9MFJHzqzJarjuar7bszkXr41xTA9Gb8ig8j9MbclWYdzVdis2BfggLbxGaMoxLw1FCZuPNo3Ua

Paste your sha256 password generated, this password is use for login to the graylog admin dashboard:

root_password_sha2 = 9235b36556923005015a6c2c18bf6f08a61daf54bfad653bde0ce6404000f0b1

Disable elasticsearch multicast search and add the unicast hosts.

elasticsearch_discovery_zen_ping_multicast_enabled = false<br>
elasticsearch_discovery_zen_ping_unicast_hosts = 127.0.0.1:9300

Change the elasticsearch shards to 1, because we install everything on this single server.

elasticsearch_shards = 1<br>
elasticsearch_replicas = 0

Save and Exit the file.

Now start the graylog-server by executing the bin file in the graylog directory:

cd /opt/graylog/bin/<br>
./graylogctl start

Now you can see the log file of the graylog-server in the log directory, watch it with tail command:

tail -f /opt/graylog/log/

If should see this in the log file:

```
Started REST API at <http://127.0.0.1:12900/>
Graylog2 up and running.
```

It means that the graylog-server has been started properly.

## Step 5 - Install Graylog-Web

Download the graylog web interface with the wget command to the /opt/directory:

cd /opt/<br>
wget <https://packages.graylog2.org/releases/graylog2-web-interface/graylog-web-interface-1.3.2.tgz>

Extract the graylog web interface and rename it to "graylog-web".

tar -xzvf graylog-web-interface-1.3.2.tgz<br>
mv graylog-web-interface-1.3.2/ graylog-web/

Then generate a new application secret code for graylog-web with pwgen:

pwgen -N 1 -s 96

This is my secret:

zHg966Be4cBBLmasLiQm4mA0ziR5HziHq6RnfmgKIsjNtLCyHUvmxBMhzRkBclaE2IWyzJPJtPaQGEiLek0iJ3CaWh6kCDAE

Go to the graylog-web directory and edit the configuration file:

cd graylog-web/<br>
vim graylog

On the graylog2-server.uris line, add the graylog2-server address:

graylog2-server.uris="[http://127.0.0.1:12900/"](http://127.0.0.1:12900/”)

In the application.secret line, paste the secret code generated before:

```
application.secret="zHg966Be4cBBLmasLiQm4mA0ziR5HziHq6RnfmgKIsjNtLCyHUvmxBMhzRkBclaE2IWyzJPJtPaQGEiLek0iJ3CaWh6kCDAE"
```

Save the file and exit.

Now start graylog-web:

cd /opt/graylog-web/bin/<br>
./graylog-web-interface -Dhttp.port=8080

Graylog-web will run on port 8080.

Visit your server - <http://myipaddress:8080/>

[![Graylog Login page](https://www.howtoforge.com/images/how-to-Install-graylog2-and-elasticsearch-on-ubuntu-15-10/Graylog_Login.png)](https://www.howtoforge.com/images/how-to-Install-graylog2-and-elasticsearch-on-ubuntu-15-10/big/Graylog_Login.png)

Now log in with user "admin" and your sha256 password.

[![Graylog installed successfully.](https://www.howtoforge.com/images/how-to-Install-graylog2-and-elasticsearch-on-ubuntu-15-10/Graylog_installed.png)](https://www.howtoforge.com/images/how-to-Install-graylog2-and-elasticsearch-on-ubuntu-15-10/big/Graylog_installed.png)

The Graylog2 server with Elasticsearch and Graylog web interface has been installed.

## Conclusion

Elasticsearch is a distributed server search engine with HTTP interface and JSON support. We can use it to search in all log files of a server when using it together with Graylog. Graylog is log management and analysis software based on Elasticsearch and mongoDB. We can use Elasticsearch, Graylog and MongoDB to build a distributed log server.

![](https://www.howtoforge.com/images/pdficon_small.png) [view as pdf](https://www.howtoforge.com/subscription/) | ![](https://www.howtoforge.com/images/print.gif) [print](https://www.howtoforge.com/subscription/)

**Share this page:**
