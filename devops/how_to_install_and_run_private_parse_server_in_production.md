# How to Install and Run Private Parse Server in Production

[Original URL](http://blog.kontena.io/how-to-install-and-run-private-parse-server-in-production/)

> 29 January 2016 Over the past few years, we have had pleasure to use Parse to provide robust backend for various projects. Unfortunately, Parse just announced they will be winding down the Parse...

29 January 2016

Over the past few years, we have had pleasure to use [Parse](http://parse.com/) to provide robust backend for various projects. Unfortunately, Parse just announced they will be [winding down the Parse service](http://blog.parse.com/announcements/moving-on/). It's sad to see Parse retiring, but luckily they released [Parse Server and the Database Migration Tool](http://blog.parse.com/announcements/introducing-parse-server-and-the-database-migration-tool/). Using the migration tool and open source [Parse Server](https://github.com/ParsePlatform/parse-server) written in Node.js you don't need to refactor your software. However, you need to setup your own _private_ Parse Server.

In this blog post, we'll demonstrate how to setup production quality Parse Server running on [Docker](https://www.docker.com/) containers and managed by [Kontena](http://www.kontena.io) container platform.

## Getting Started

We will install the full Parse stack using Kontena container management platform. If you are new to Kontena, please follow the [Kontena Quick Start](http://www.kontena.io/docs/getting-started/quick-start) guide. It'll take just 5-10 minutes!

Once you have Kontena installed, you are ready to proceed installing Parse server!

## Installing Parse Server

Installing Parse Server with Kontena is a very straightforward process. We have made available two examples. You can choose from:

- [Parse Server (simple)](https://github.com/kontena/examples/tree/master/parse)
- [Parse Server with MongoDB cluster](https://github.com/kontena/examples/tree/master/parse-cluster)

The first option may be used to deploy stand-alone Parse Server with MongoDB database. The second option is similar, but with MongoDB cluster database setup for high-availability deployments. If you choose Parse Server with MongoDB cluster option, you should have at minimum 3 Kontena nodes running.

Both of these examples use Kontena's built-in load balancer. The load balancer may be used to scale Parse Server instances to serve more traffic. You can also configure SSL certificate for the load balancer to provide secure access to your Parse Server API.

**Step 1.** Copy or Clone files from GitHub.

**Step 2.** Write secrets to [Kontena Vault](http://www.kontena.io/docs/using-kontena/vault).

```
$ kontena vault write PARSE_MASTER_KEY "mySecretMasterKey"
$ kontena vault write PARSE_FILE_KEY "optionalFileKey"
```

**Step 3.** Deploy the stack

```
$ kontena app deploy
```

After the deploy is finished you can connect to your Parse Servers with the IP addresses of your node(s).

**Step 4.** Enjoy!

## Optional Configuration

Here's some tips & tricks to improve your Parse Server.

### Using DNS Address

Simply map your Kontena Node IP addresses to your own DNS address.

### Using SSL Certificates

It's recommended to use a SSL certificate on production environment to secure connections to your Parse Server. You can just add a certificate file to Kontena Vault and re-deploy the load balancer service:

```
$ kontena vault write PARSE_SERVER_CERT "$(cat cert.pem)"
$ kontena app deploy loadbalancer
```

You can read more about [SSL Termination](http://www.kontena.io/docs/using-kontena/loadbalancer#ssl-termination) on Kontena load balancer.

### Scaling Parse Server

Scaling Parse Server is almost too easy. You can just modify `instances` parameter in `kontena.yml` file and re-deploy the `server` service. For example:

```
#kontena.yml
...
server: 
 instances: 6
...

$ kontena app deploy server
```

Kontena's load balancer will automatically detect changes and route traffic correctly to all Parse Server instances.

## About Kontena

[Kontena](http://www.kontena.io) is a new open source Docker platform including orchestration, service discovery, overlay networking and all the tools required to run your containerized workloads. Kontena is built to maximize developer happiness. It works on any cloud, it's easy to setup and super simple to use. [Give it a try](http://www.kontena.io/docs/getting-started/quick-start)! If you like it, please star it on [Github](https://github.com/kontena/kontena) and follow us on [Twitter](https://twitter.com/KontenaInc). We hope to see you again!
