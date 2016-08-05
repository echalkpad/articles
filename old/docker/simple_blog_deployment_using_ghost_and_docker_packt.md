# Simple Blog Deployment using Ghost and Docker | Packt

[Original URL](https://www.packtpub.com/books/content/simple-blog-deployment-using-ghost-and-docker)

> 2013 gave birth to two wonderful Open Source projects: Ghost and Docker. This post will show you what the buzz is all about, and how you can use them together. So what are Ghost and Docker, exactly?...

2013 gave birth to two wonderful Open Source projects: [Ghost](https://ghost.org/) and [Docker](https://www.docker.com/). This post will show you what the buzz is all about, and how you can use them together.

## So what are Ghost and Docker, exactly?

[Ghost](https://ghost.org/about/) is an exciting new blogging platform, written in JavaScript running on [Node.js](http://nodejs.org/). It features a simple and modern user experience, as well as [very transparent and accessible developer communications](http://dev.ghost.org/). This blog post covers Ghost 0.4.2.

![Ghost blogging platform](https://www.packtpub.com/sites/default/files/new_blog_images/67/ghost-screenshot.gif)

[Docker](https://www.docker.com/) is a very useful new development tool to package applications together with their dependencies for automated and portable deployment. It is based on [Linux Containers (lxc)](https://en.wikipedia.org/wiki/Lxc) for lightweight virtualization, and [AUFS](https://en.wikipedia.org/wiki/Aufs) for filesystem layering. This blog post covers Docker 1.1.2.

![Docker logo](https://www.packtpub.com/sites/default/files/new_blog_images/67/docker-logo.gif)

## Install Docker

If you are on Windows or Mac OS X, the easiest way to get started using Docker is [Boot2Docker](http://boot2docker.io/). For Linux and more in-depth instructions, consult one of the [Docker installation guides](https://docs.docker.com/installation/#installation).

Go ahead and install Docker via one of the above links, then come back and run:

```
docker version
```

You run this in your terminal to verify your installation. If you get about eight lines of detailed version information, the installation was successful. Just running docker will provide you with a list of commands, and docker help <command> will show a command's usage. If you use Boot2Docker, remember to export DOCKER_HOST=tcp://192.168.59.103:2375.

Now, to get the Ubuntu 14.04 base image downloaded (which we'll use in the next sections), run the following command:

```
docker run --rm ubuntu:14.04 /bin/true
```

This will take a while, but only for the first time. There are many more Docker images available at the [Docker Hub Registry](https://registry.hub.docker.com/).

## Hello Docker

To give you a quick glimpse into what Docker can do for you, run the following command:

```
docker run --rm ubuntu:14.04 /bin/echo Hello Docker
```

This runs /bin/echo Hello Docker in its own virtual Ubuntu 14.04 environment, but since it uses Linux Containers instead of booting a complete operating system in a virtual machine, this only takes **less than a second** to complete. Pretty sweet, huh?

To run Bash, provide the -ti flags for interactivity:

```
docker run --rm -ti ubuntu:14.04 /bin/bash
```

The –rm flag makes sure that the container gets removed after use, so any files you create in that Bash session get removed after logging out.

For more details, see the [Docker Run Reference](https://docs.docker.com/reference/run/).

## Build the Ghost image

In the previous section, you've run the ubuntu:14.04 image. In this section, we'll build an image for Ghost that we can then use to quickly launch a new Ghost container. While you could get a [pre-made Ghost Docker image](http://dockerfile.github.io/#/ghost), for the sake of learning, we'll build our own.

About the terminology: A Docker **image** is analogous to a program stored on disk, while a Docker **container** is analogous to a process running in memory.

Now create a new directory, such as docker-ghost, with the following files -- you can also find them in [this Gist on GitHub](https://gist.github.com/felixrabe/2c352646a698545a096f):

**package.json**:

```
{}
```

This is the bare minimum actually required, and will be expanded with the current Ghost dependency by the Dockerfile command npm install –save ghost when building the Docker image.

**server.js**:

```
#!/usr/bin/env node

var ghost = require('ghost');
ghost({
 config: __dirname + '/config.js'
});
```

This is all that is required to [use Ghost as an NPM module](https://github.com/TryGhost/Ghost/wiki/Using-Ghost-as-an-NPM-module).

**config.js:**

```
config = require('./node_modules/ghost/config.example.js');
config.development.server.host = '0.0.0.0';
config.production.server.host = '0.0.0.0';
module.exports = config;
```

This will make the Ghost server accessible from outside of the Docker container.

**Dockerfile**:

```
# DOCKER-VERSION 1.1.2

FROM ubuntu:14.04

# Speed up apt-get according to https://gist.github.com/jpetazzo/6127116
RUN echo "force-unsafe-io" > /etc/dpkg/dpkg.cfg.d/02apt-speedup
RUN echo "Acquire::http {No-Cache=True;};" > /etc/apt/apt.conf.d/no-cache

# Update the distribution
ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update
RUN apt-get upgrade -y

# https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
RUN apt-get install -y software-properties-common
RUN add-apt-repository -y ppa:chris-lea/node.js
RUN apt-get update
RUN apt-get install -y python-software-properties python g++ make nodejs git # git needed by 'npm install'

ADD . /src
RUN cd /src; npm install --save ghost

ENTRYPOINT ["node", "/src/server.js"]
# Override ubuntu:14.04 CMD directive:
CMD []

EXPOSE 2368
```

This Dockerfile will create a Docker image with Node.js and the dependencies needed to build the Ghost NPM module, and prepare Ghost to be run via Docker. See [Documentation](http://docs.docker.com/reference/builder/) for details on the syntax.

Now build the Ghost image using:

```
cd docker-ghost
docker build -t ghost-image .
```

This will take a while, but you might have to _Ctrl-C_ and re-run the command if, for more than a couple of minutes, you are stuck at the following step:

```
> node-pre-gyp install --fallback-to-build
```

## Run Ghost

Now start the Ghost container:

```
docker run --name ghost-container -d -p 2368:2368 ghost-image
```

If you run Boot2Docker, you'll have to figure out its IP address:

```
boot2docker ip
```

Usually, that's 192.168.59.103, so by going to [http://192.168.59.103:2368](http://192.168.59.103:2368/), you will see your fresh new Ghost blog. Yay! For the admin interface, go to <http://192.168.59.103:2368/ghost>.

## Manage the Ghost container

The following commands will come in handy to manage the Ghost container:

```
# Show all running containers:
docker ps -a

# Show the container logs:
docker logs [-f] ghost-container

# Stop Ghost via a simulated Ctrl-C:
docker kill -s INT ghost-container

# After killing Ghost, this will restart it:
docker start ghost-container

# Remove the container AND THE DATA (!):
docker rm ghost-container
```

## What you'll want to do next

Some steps that are outside the scope of this post, but some steps that you might want to pursue next, are:

- Copy and change the Ghost configuration that currently resides in node_modules/ghost/config.js.
- Move the Ghost content directory into a separate Docker volume to allow for upgrades and data backups.
- Deploy the Ghost image to production on your public server at your hosting provider. Also, you might want to change the Ghost configuration to match your domain and change the port to 80.

## How I use Ghost with Docker

I run Ghost in Docker successfully over at [Named Data Education](http://named-data.education/), a new blog about [Named Data Networking](http://named-data.net/). I like the fact that I can replicate an isolated setup identically on that server as well as on my own laptop.

## Ghost resources

## Docker resources

## About the Author

Felix Rabe has been programming and working with different technologies and companies at different levels since 1993\. Currently he is researching and promoting Named Data Networking (<http://named-data.net/>), an evolution of the Internet architecture that currently relies on the host-bound Internet Protocol.
