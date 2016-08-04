# Docker Compose Project: Ghost Blog

[Original URL](https://startupnextdoor.com/docker-compose-project-ghost-blog/)

> I'm still new to Docker, and the hype is real - it's a really compelling technology. Docker provides a unique way of isolating services and their dependencies. This blog is running on containers! The...

![Docker logo](https://d2mw6vgfxwlz2a.cloudfront.net/2016/Feb/docker_logo-1455828502290.png)

I'm still new to Docker, and the hype is real - it's a really compelling technology. Docker provides a unique way of isolating services and their dependencies.

This blog is running on containers!

## The Project

This blog is running on 3 Docker containers running:

- Node.js and the Ghost blog software
- NGINX - proxying port 80 calls to the Node web server on port 2368
- MySQL database

As you can see in the diagram, we have the Docker engine installed on Ubuntu on a server, and 3 containers running in the docker engine environment.

![Ghost Docker diagram](https://d2mw6vgfxwlz2a.cloudfront.net/2016/Feb/docker_ghost_diagram-1455833244760.png)

Here's the entire setup if you want to use the same for your own blog:

I'm a [big fan of Ghost](https://startupnextdoor.com/from-wordpress-to-jekyll-to-ghost/) as a blogging system. It's simple and fast, and is made just for blogging.

![Ghost blog](https://d2mw6vgfxwlz2a.cloudfront.net/2016/Feb/ghost_logo_small-1455828665539.png)

Clone the project above onto a server, install Docker, and in a few seconds you'll have a running blog. [Setup instructions here](https://github.com/jwasham/docker-ghost-template). Since the project uses S3 and Cloudfront for images, the only thing that will take you a bit to set up is making an S3 bucket and a Cloudfront distribution.

## About Docker

Docker creates "containers" which run a single service in isolation. A service is basically a single process. A service could be a Redis instance, Memcache, MySQL, Apache, any number of things. But it's one process. When the container starts, the process starts, and when the process stops the container stops.

> Docker containers run one process, so they do one thing.

Containers can easily connect to each other using "links". Linking containers means they can communicate with each other over the network. Each container can expose a port to allow connections.

## The Old Way

The old way, which still works great (I use it every day) but can be cumbersome, is to have a server set up for a few things. For example, a web server running an application:

The server is running:

- OS: Ubuntu
- Web server: Apache or NGINX
- Application processes: Python, PHP, etc.
- Caching: Redis, Memcache, etc.
- SSH: so you can log in remotely

You may perhaps have a database on the same server if it's a small operation.

Setting up each server, doing updates, and maintaining them to work together can be a pain over time.

## The Docker Way

By isolating a service within a container, it's easy to replace an old, outdated container with a new one. Or if you need 7 containers that do the same thing, just spawn 7 of them.

Each service is small. Each doesn't have the overhead of an entire OS. Each container has an OS, which is specified by the container's author, but if you have 4 containers running the same version of Ubuntu, you don't have 4 operating systems taking up 4 times the space on the same host. They only take up the space of one. Docker accomplishes this through the use of the **union file system** ([UnionFS](https://en.wikipedia.org/wiki/UnionFS)). As a container is built, it creates layers. Identical layers are shared with other containers, so the space needed is minimized.

> Unionfs is a filesystem service for Linux, FreeBSD and NetBSD which implements a union mount for other file systems. It allows files and directories of separate file systems, known as branches, to be transparently overlaid, forming a single coherent file system. Contents of directories which have the same path within the merged branches will be seen together in a single merged directory, within the new, virtual filesystem.

Most containers don't run a full-blown OS. Many use a bare-bones distribution like Alpine or Busybox. Alpine only takes 5 MB.

Here's an example of the layers that build up on two containers shown side-by-side, one built on Busybox and one on Debian, but sharing a kernel.

![layers in UnionFS](https://d2mw6vgfxwlz2a.cloudfront.net/2016/Feb/docker_unionfs_4-1455834185704.png)

Containers are the writeable layer that sit atop an image. An image is the blueprint of a container.

Containers build upon each other. One will start with just an OS, and add its own service on top. Then another container can use that container as a base, and augment the given service. Yet another container can use that one as a base for its service. Each hierarchy creates a layer on the union file system.

### Why containers and why now?

Microservices are all the rage right now. Microservices are single-purpose services that accomplish one task in the vast scheme of an enterprise datacenter. They are self-contained, and usually manager their own data so they have no external dependencies. A service receives a request or a message, and does a task, and then waits for the next. When a new service rolls out to replace the original, you can divert traffic from the old service to the new one, and eventually kill off the old service.

This contrasts with deploying services to servers, and later re-deploying to the same servers. With servers being so cheap and disposable nowadays, it's easier to spin up a new server, run a container on it, and migrate traffic to it. Over time, you end up on newer, faster hardware, with better services.

There's a lot to cover in terms of microservices. I won't go into more detail here, but you can see where microservices and containers seem to be made for each other. They both fit a philosophy of disposable, temporary, single-purpose services that evolve over time.

## Docker Compose

![Docker Compose](https://d2mw6vgfxwlz2a.cloudfront.net/2016/Feb/docker_compose-1455828340047.png)

Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a Compose file to configure your application's services. Then, using a single command, you create and start all the services from your configuration.

All the service-to-service network connections are laid out in the docker-compose.yml file, [as you can see here](https://github.com/jwasham/docker-ghost-template/blob/master/docker-compose.yml). This is much easier than running containers and setting the network settings (links to other containers) on the command line.

Docker-compose brings up all the services together, and brings them down together gracefully.

## Learning More

Docker has a [few tutorial videos](https://training.docker.com/self-paced-training) on their site to get you started.

I got my start with this comprehensive book by Adrian Mouat: [Using Docker](http://www.amazon.com/Using-Docker-Developing-Deploying-Containers/dp/1491915765). Published in 2016, it has up-to-date coverage of Docker. It's a great start and my go-to reference.

[![Using Docker by Adrian Mouat](https://d2mw6vgfxwlz2a.cloudfront.net/2016/Feb/using_docker_2-1455827593978.png)](http://www.amazon.com/Using-Docker-Developing-Deploying-Containers/dp/1491915765)

## Recommendations

I would recommend changing the memory used by MySQL. It tends to take up a lot of memory, even when that memory is not needed. You can set a limit in the docker-compose.yml before you start the containers the first time, like this:

```
mysql: 
 container_name: blog_data
 mem_limit: 150m
 image: mysql:5.7.10
 expose:
 - "3306"
```

If you start the containers first and then add this later, it will create a new container for MySQL and discard the old one.
