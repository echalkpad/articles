# Hello, Blog! - An advanced setup of Ghost and Docker made simple

[Original URL](http://coderunner.io/hello-blog-an-advanced-setup-of-ghost-and-docker-made-simple/)

> So you want to setup a nice new blog with a streamlined development workflow? Great, so did I! After spending some time ironing out a setup that works for me, I thought I'd share it. If you want a...

So you want to setup a nice new blog with a streamlined development workflow? Great, so did I! After spending some time ironing out a setup that works for me, I thought I'd share it.

![Ghost & Docker logos](http://coderunner.io/content/images/2015/12/ghost-docker.png)

If you want a simple, back-to-basics blogging platform, then [Ghost](https://ghost.org/) is a good choice. It is focussed on the content and making it look nice right out the box, and it is powering what you're reading now.

I'll detail my workflow step-by-step so that it you want to do something similar you can follow along, just replace all references to `coderunner.io` with your own domain :) I'm still making refinements, so leave a comment below with any suggestions!

I have split this up into three parts:

## The Goal

What we're shooting for:

- Ability to bring up/down the whole stack with a single command (we'll use [Docker Compose](https://docs.docker.com/compose/) for that)
- Front our blog with a [reverse proxy](https://en.wikipedia.org/wiki/Reverse_proxy), because we will be hosting it on a VPS and may want to have other blogs/apps on the same box
- Simple to clone our environment for development (or to migrate to a different host in the future)
- Let us create content first on our local environment and then sync it with our public host
- Automated backup (and restore) to somewhere like [Dropbox](https://www.dropbox.com)

Sounds good? Let's get started!

## Part I: Setting up a Dockerised installation of Ghost with MariaDB

### Guide

### Overview

Ghost can be setup with [sqlite3](https://www.sqlite.org/) (default) or [MySql](https://www.mysql.com/)/[MariaDB](https://mariadb.org/). I decided to use MariaDB so I have a fully featured RDBMS, and as it will fit in nicely to our modular Docker setup.

In this post we'll setup Ghost running in a Docker container, linked to a MariaDB container and fronted by [Nginx](https://www.nginx.com/resources/wiki/). To wire it all together, we'll use Docker Compose.

> Before getting started, you should have [Docker installed](https://docs.docker.com/engine/installation/) and [Docker Compose setup](https://docs.docker.com/compose/install/). We'll set everything up locally and then deploy to a VPS in the next post.

## Directory Structure

So that it is clear up-front, this is the directory structure we'll be putting together:

```
.
|-- data-coderunner.io
| |-- config.js #[gist](https://gist.github.com/bennetimo/6ddb288bf645abf76b38/ed4f50cd4acda83f540e12bc6b7bb3267ea18d93)
| |-- Dockerfile #[gist](https://gist.github.com/bennetimo/0ab18d783557438c6145)
| `-- env_coderunner.io
`-- docker-compose.yml #[gist](https://gist.github.com/bennetimo/91ca871c3aaa2e7a148a)

1 directory, 4 files 
```

We'll build up each one as we go, but I've added the gists so you can see the final result if you want.

## Creating a Data Only container

The power of Docker comes from composing together single purpose containers to create your application. To fully embrace this we'll create a [data only container](http://container42.com/2013/12/16/persistent-volumes-with-docker-container-as-volume-pattern/) just to hold our data, and nothing more. Then we will be able to easily link the data volumes into any containers that need to access it, whether that's our Ghost container, backup container, or something else.

Here is the `Dockerfile` for our data container, which lives in the sub-directory `data-coderunner.io`.

```
FROM ghost 
MAINTAINER Tim Bennett <tim@coderunner.io>

# Create required volumes
VOLUME ["/var/lib/mysql", "/var/lib/ghost"]

ENTRYPOINT ["/bin/bash"] 
```

It's pretty uninteresting, we just inform Docker that we want to mount the mysql and ghost directories. I'm basing off the Ghost image so it reuses the same layers as the Ghost container that we'll need later, to alleviate [container madness](http://container42.com/2014/11/18/data-only-container-madness/).

> As of Docker 1.9.0 there is a new [Volumes API](https://docs.docker.com/engine/reference/commandline/volume_create/) which it would be nice to use here, but it is [not yet supported in Docker Compose](https://github.com/docker/compose/issues/2110).

## Build it with Docker Compose

Now we want to build our data container, but instead of doing it manually we'll do it with [Docker Compose](https://docs.docker.com/compose/), by creating a `docker-compose.yml` file at the top level of the directory:

```
data-coderunner.io: 
 build: ./data-coderunner.io
 container_name: "data-coderunner.io"
```

In this file we'll be declaratively listing all of the components that make up our stack and how they link together.

The [build](https://docs.docker.com/compose/compose-file/#build) directive will create our data container, and we name it so we can refer back to it later.

> It's also possible to setup the data-container [directly](http://stackoverflow.com/questions/32908621/how-can-i-create-a-data-container-only-using-docker-compose-yml) in Docker-Compose, but I prefer this approach

## Setup our MariaDB container

There's an officially supported [image](https://hub.docker.com/_/mariadb/) for MariaDB which makes our lives easy.

All we need to do is add it to our docker-compose.yml:

```
mariadb: 
 image: mariadb
 container_name: "mariadb"
 env_file: ./data-coderunner.io/env_coderunner.io
 environment:
 - TERM=xterm
 ports:
 - "127.0.0.1:3306:3306"
 volumes_from:
 - data-coderunner.io
```

There's a few things going on here. `volumes-from` references the data container we just created, so that MariaDB will be using the `/var/lib/mysql` mount point that we setup.

The ports mapping will bind port 3306 on our host to 3306 in the container, and we bind it for the loopback interface only. Otherwise we would be able to connect directly to the database container from the outside, but we want our only outside entry point to be our proxy that we'll create shortly.

We also specified an `env_file` with our db configuration:

```
# MariaDB configuration
MYSQL_ROOT_PASSWORD=<REDACTED> 
MYSQL_USER=tim 
MYSQL_PASSWORD=<REDACTED> 
MYSQL_DATABASE=blog 
```

Finally, I'm setting the TERM environment variable so I can use the mysql tool to connect to our database, if needed.

## Setup Ghost

Next up we need to actually add Ghost, and we have an [official image](https://hub.docker.com/_/ghost/) for that too, awesome!

```
blog-coderunner.io: 
 image: ghost
 container_name: "blog-coderunner.io"
 volumes:
 - ./data-coderunner.io/config.js:/var/lib/ghost/config.js
 volumes_from:
 - data-coderunner.io
 env_file: ./data-coderunner.io/env_coderunner.io
 links:
 - mariadb:mysql
```

The only new things here are `volumes` and `links`, so let's just take a look at those.

Ghost uses a config.js file for [configuration](http://support.ghost.org/config/), and the Docker image will create one that can then be modified as needed. But we want everything to be dynamic, and pick up the fact that we're using MariaDB instead of sqlite automagically!

So, we'll use our own config.js that figures everything out using the environment variables from the containers. Here is the [gist](https://gist.github.com/bennetimo/6ddb288bf645abf76b38). Then, in the `volumes` section, we mount it straight to where Ghost is expecting it in the container.

Now we just need to add this to our `env_coderunner.io` file:

```
# Ghost configuration
URL=http://coderunner.io 
```

This is picked up in the config.js to configure the URL for Ghost.

The `links` entry tells Docker to create a tunnel between our containers by adding a `mysql` entry to the `/etc/hosts` file; Now our blog container can talk to our mysql container.

At this point we could fire up our blog, but we wouldn't be able to access it from our local machine as we're not exposing the ports. We will go one better than exposing the Ghost port directly, and setup [nginx](https://www.nginx.com/resources/wiki/).

## Put it all behind nginx

By setting everything up behind an nginx reverse proxy, we can have multiple services (applications, other blogs etc) running on a single box and have nginx handling traffic routing between them. We could set this up manually, but there is already an awesome out-the-box Docker setup in [jwilder/nginx-proxy](https://hub.docker.com/r/jwilder/nginx-proxy/).

Now we're really starting to see the magic and power of Docker. We're building our application by sticking together components like making a house out of lego bricks!

![FlickrFriday: Keep it Simple. by elPadawan, on Flickr](https://farm9.static.flickr.com/8507/8505316460_78d0abaf5b_b.jpg) [Photo](https://www.flickr.com/photos/elpadawan/8505316460/) by elPadawan / [CC BY](http://creativecommons.org/licenses/by/2.0/)

So we add this to our `docker-compose.yml`:

```
nginx: 
 image: jwilder/nginx-proxy
 container_name: "nginx"
 ports: 
 - "80:80"
 volumes:
 - /var/run/docker.sock:/tmp/docker.sock
```

And that's all we need to create a fully-fledged reverse proxy! Now we just need to tell it the hostname that will map to our blog, by adding an environment variable to the blog container:

```
environment: 
 - VIRTUAL_HOST=coderunner.io
```

## Start it up!

In the main blog directory:

```
docker-compose up 
```

And we're running!

> On the very first launch the Ghost container might try to connect to MariaDB before it's finished setting up the database. To avoid it you can start MariaDB separately first with `docker-compose up -d mariadb`, or by using my [modified image](https://hub.docker.com/r/bennetimo/ghost-wait-mysql/). See [here](https://github.com/docker/compose/issues/374) for more info.

At the moment everything is on our local machine, so we can add an entry to `/etc/hosts` to simulate the domain setup.

```
localhost coderunner.io 
```

> If you're using [docker-machine](https://docs.docker.com/machine/) then you want to use the IP of the virtual machine which you can find with `docker-machine ip default`

Now we can fire up a browser and visit <http://coderunner.io>, and we're greeted with Ghost:

![ghost-welcome-page](http://coderunner.io/content/images/2015/12/Screen-Shot-2015-12-20-at-13-37-25.png)

We now have a Ghost blog running, linked to a MariaDB container, and fronted by an Nginx reverse proxy, all running in Docker containers. Nice!

But, at the moment we're just running locally. In the [next post](http://coderunner.io/deploying-ghost-on-digital-ocean-with-docker-compose), we'll move this to a VPS on DigitalOcean so we're publicly accessible.
