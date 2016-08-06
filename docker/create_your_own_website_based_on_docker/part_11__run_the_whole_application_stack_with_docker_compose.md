---
title: "Series: How to create your own website based on Docker (Part 11 - Run the whole application stack with Docker Compose)"
url: "http://project-webdev.blogspot.de/2015/06/create-site-based-on-docker-part11-start-containers-with-docker-compse.html"
date_saved: "07/19/2016 08:05-28"
excerpt: "This is part 11 of the series:&#xA0;How to create your own website based on Docker. Well, we have created our images now, so it's about time to get everything up and running. As mentioned in the&hellip;"
thumbnail: [![](png/series_how_to_create_your_own_website_based_on_docker_part_11__run_the_whole_application_stack_with_docker_compose.png)](png/full/series_how_to_create_your_own_website_based_on_docker_part_11__run_the_whole_application_stack_with_docker_compose.png)
---

# Series: How to create your own website based on Docker (Part 11 - Run the whole application stack with Docker Compose)

[Original URL](http://project-webdev.blogspot.de/2015/06/create-site-based-on-docker-part11-start-containers-with-docker-compse.html)

> This is part 11 of the series: How to create your own website based on Docker. Well, we have created our images now, so it's about time to get everything up and running. As mentioned in the...

[](http://2.bp.blogspot.com/-bqDBv2vtfM8/VVDHe5wEJZI/AAAAAAAAroo/konpShiVVa0/s1600/docker250.png)[![](http://4.bp.blogspot.com/-AoyShqyep_k/VVDH5T73l-I/AAAAAAAArow/4wvLEvsURMk/s1600/docker250.png)](http://4.bp.blogspot.com/-AoyShqyep_k/VVDH5T73l-I/AAAAAAAArow/4wvLEvsURMk/s1600/docker250.png)

This is part 11 of the series: [How to create your own website based on Docker](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part1.html).

Well, we have created our images now, so it's about time to get everything up and running. As mentioned in the first posting we're using Docker Compose for that.

This is the time where our docker-compose file from [part 5](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part5-docker-compose.html) comes back into play. Docker Compose will use this file to figure out which containers need to be started and in what order. I will make sure that all posts are exposed correctly and will mount our directories.

## Source code

<span>All files mentioned in this series are </span>

[available on Github](https://github.com/mastix/project-webdev-docker-demo)

<span>, so you can play around with it! :)</span>

<br>

<span>
  <br>
</span>

## Custom Clean Script

<span>I’ve created a custom clean script that allows me to clean all existing images and containers. This is comes in pretty handy when you’re in the middle of development. I’ve used this script a thousand times already.</span>

> #!/bin/bash<br>
> docker rm -f $(docker ps -q -a)<br>
> docker rmi -f $(docker images -q)

Source: <https://github.com/mastix/project-webdev-docker-demo/blob/master/cleanAll>

This script iterates through all containers first and removes them (it doesn't care whether they are up and running or not) and will then remove all images. Don't be afraid... the images will be re-created from the Dockerfiles. So if you've played around with these containers already, I recommend to run this script before reading further. **Important:** Do not run this script in your production environment! :)

## Working with Docker Compose

Docker Compose will only do it's magic when you're in a directory that contains a 

<span>docker-compose.ym</span>

l. As mentioned before, I've put all files into 

<span>/opt/docker/</span>

, so we're operating in this directory only.

I'm currently using the following version:

> /opt/docker$ **docker-compose –version**<br>
> docker-compose 1.2.0

Make sure that no containers are running:

> /opt/docker$ **docker-compose ps**<br>
> Name Command State Ports<br>
> --------------------

Update: If you're running Docker Compose >= 1.3.x then you'll have to run the following command if you've checked out the code from my repository:

> docker-compose migrate-to-labels

Since we have our docker-compose.yml ready, the only thing to do is fire docker-compose via the following command:

> docker-compose up -d

The 

<span>-d</span>

 flag makes sure that Docker Compose starts in daemon mode (in the background).

When you run this command you'll see that it fetches the images and configures them based on our Dockerfile (e.g. like running apt-get, copying files,...). It would be too much to copy here, but you'll see soon how much output it generates.

## Let's play around with our containers

Let's see if all containers are up and running:

> /opt/docker$ docker-compose ps<br>
> Name Command State Ports<br>
> ----------------------------------------------------------------–<br>
> docker_mongodb_1 /tmp/entrypoint.sh mongod ... Up 3333/tcp<br>
> docker_nginxreverseproxy_1 /bin/sh -c /etc/nginx/conf ... Up 443/tcp, **0.0.0.0:80->80/tcp**<br>
> docker_projectwebdev_1 /bin/sh -c nginx Up 8081/tcp<br>
> docker_projectwebdevapi_1 pm2 start index.js –name ... Up 3000/tcp<br>
> docker_ubuntubase_1 bash Exit 0

It's pretty fine that our ubuntu container already exited, since there is no background task running (e.g. like our nginx server that has to reply to requests) - all other three services are up and running. You can also see that only our nginx reverse proxy exposes its port (80) to the public. All other ports are internal ports.

Let's see if our website is up and running:

### Our 

<span>Person</span>

 REST API:

[![](http://2.bp.blogspot.com/-ulfOz2jrL78/VX3PmiJC6jI/AAAAAAAAsos/zYdM0SKvBIw/s320/Screen%2BShot%2B2015-06-14%2Bat%2B21.00.16.png)](http://2.bp.blogspot.com/-ulfOz2jrL78/VX3PmiJC6jI/AAAAAAAAsos/zYdM0SKvBIw/s1600/Screen%2BShot%2B2015-06-14%2Bat%2B21.00.16.png)

### Our Person Demo page:

[![](http://3.bp.blogspot.com/-o-Tz1COuy5s/VXqQKNHnVWI/AAAAAAAAslk/e3xfhqOS7ss/s320/2015-06-12_09h52_05.png)](http://3.bp.blogspot.com/-o-Tz1COuy5s/VXqQKNHnVWI/AAAAAAAAslk/e3xfhqOS7ss/s1600/2015-06-12_09h52_05.png)

[![](http://2.bp.blogspot.com/-khetjx9wEOg/VXqQKAtrXSI/AAAAAAAAslo/J9ioSbZ_oiU/s320/2015-06-12_09h51_42.png)](http://2.bp.blogspot.com/-khetjx9wEOg/VXqQKAtrXSI/AAAAAAAAslo/J9ioSbZ_oiU/s1600/2015-06-12_09h51_42.png)

_Just a short information: Don't try to access the URLs you see in this image. Since this is just a demo I just uploaded it for demonstration purposes and stopped the containers right after taking these screenshots._

Let's see how much memory our 

<span>Person</span>

 API consumes:

> /opt/docker$ docker-compose stats docker_projectwebdevapi_1<br>
> CONTAINER CPU % MEM USAGE/LIMIT MEM % NET I/O<br>
> docker_projectwebdevapi_1 0.00% 76.12 MiB/1.954 GiB 3.80% 3.984 KiB/1.945 KiB

Let's stop & start all containers - why? Because we can!

> /opt/docker$ docker-compose restart<br>
> Restarting docker_projectwebdev_1...<br>
> Restarting docker_mongodb_1...<br>
> Restarting docker_projectwebdevapi_1...<br>
> Restarting docker_nginxreverseproxy_1...

Let's see the stacked images:

> docker images –tree<br>
> Warning: '–tree' is deprecated, it will be removed soon. See usage.<br>
> └─1c3c252d48a5 Virtual Size: 131.3 MB<br>
> └─66b5d995810b Virtual Size: 131.3 MB<br>
> └─b7e7cde90a84 Virtual Size: 131.3 MB<br>
> └─c6a3582257ff Virtual Size: 131.3 MB Tags: ubuntu:15.04<br>
> └─beec7359d06b Virtual Size: 516 MB<br>
> └─56f95e536056 Virtual Size: 516 MB<br>
> └─2e6215be7f22 Virtual Size: 516 MB<br>
> └─0da535016806 Virtual Size: 516 MB Tags: docker_ubuntubase:latest<br>
> ├─22e3ad368e3d Virtual Size: 516.4 MB<br>
> [...]<br>
> └─bc20ce213396 Virtual Size: 679 MB<br>
> │ └─b20c90481a4e Virtual Size: 679 MB Tags: docker_mongodb:latest<br>
> └─419a34bcfcfd Virtual Size: 516 MB<br>
> ├─2d2525cf28e1 Virtual Size: 537.1 MB<br>
> │ └─9c9f238dc62d Virtual Size: 558.2 MB<br>
> │ └─4bf8554af678 Virtual Size: 580.2 MB<br>
> │ └─9d6fdb379360 Virtual Size: 620.4 MB<br>
> │ └─02b3cd93208f Virtual Size: 638.1 MB<br>
> [...]<br>
> └─aba65d0f0c06 Virtual Size: 706 MB<br>
> │ └─9b4b55e323e3 Virtual Size: 706 MB Tags: docker_projectwebdevapi:latest<br>
> └─466f9910439a Virtual Size: 543.9 MB<br>
> ├─008ffe8fa738 Virtual Size: 543.9 MB<br>
> │ └─476a45c16218 Virtual Size: 543.9 MB<br>
> [...]<br>
> │ └─b53827f8ddfd Virtual Size: 543.9 MB Tags: docker_nginxreverseproxy:latest<br>
> └─aec75192e11a Virtual Size: 543.9 MB<br>
> └─eadec9140592 Virtual Size: 543.9 MB<br>
> └─27b6deeec60a Virtual Size: 543.9 MB<br>
> └─6f0f6661c308 Virtual Size: 543.9 MB Tags: docker_projectwebdev:latest

As you can see: All our images are based on the Ubuntu Base Image and files are only added on top of the underlying base image.

Let's see all logs to console/stdout:

> /opt/docker$ docker-compose logs<br>
> Attaching to docker_nginxreverseproxy_1, docker_projectwebdevapi_1, docker_mongodb_1, docker_projectwebdev_1, docker_ubuntubase_1<br>
> projectwebdevapi_1 | pm2 launched in no-daemon mode (you can add DEBUG="*" env variable to get more messages)<br>
> projectwebdevapi_1 | 2015-06-12 21:29:23: [PM2][WORKER] Started with refreshing interval: 30000<br>
> projectwebdevapi_1 | 2015-06-12 21:29:23: [[[[ PM2/God daemon launched ]]]]<br>
> [...]<br>
> nginxreverseproxy_1 | START UPDATING DEFAULT CONF<br>
> nginxreverseproxy_1 | CHANGED DEFAULT CONF<br>
> nginxreverseproxy_1 | upstream blog {<br>
> nginxreverseproxy_1 | server 172.17.0.29:8081; #Blog<br>
> nginxreverseproxy_1 | }<br>
> nginxreverseproxy_1 |<br>
> nginxreverseproxy_1 | upstream blog-api {<br>
> nginxreverseproxy_1 | server 172.17.0.54:3000; #Blog-API<br>
> nginxreverseproxy_1 | }<br>
> nginxreverseproxy_1 |<br>
> nginxreverseproxy_1 | ## Start blog.project-webdev.com ##<br>
> nginxreverseproxy_1 | server {<br>
> nginxreverseproxy_1 | listen 80;<br>
> nginxreverseproxy_1 | server_name blog.project-webdev.com;<br>
> [...]<br>
> nginxreverseproxy_1 | }<br>
> nginxreverseproxy_1 | ## End blog.project-webdev.com ##<br>
> nginxreverseproxy_1 |<br>
> nginxreverseproxy_1 | ## Start api.project-webdev.com ##<br>
> nginxreverseproxy_1 | server {<br>
> nginxreverseproxy_1 | listen 80;<br>
> nginxreverseproxy_1 | server_name api.project-webdev.com;<br>
> nginxreverseproxy_1 |<br>
> [...]<br>
> nginxreverseproxy_1 | }<br>
> nginxreverseproxy_1 | ## End api.project-webdev.com ##<br>
> nginxreverseproxy_1 |<br>
> [...]<br>
> nginxreverseproxy_1 | END UPDATING DEFAULT CONF

As you can see, you'll get all stdout output from each container. You'll see the container name in the beginning of the line... in our example we'll only get output from our ioJS container/projectwebdevapi_1 (started with pm2) and our nginx reverseproxy/nginxreverseproxy_1\.

Let's check our log directory:

> /opt/docker/logs$ ll<br>
> total 24<br>
> drwxrwxrwx 2 mastix docker 4096 Jun 14 21:21 ./<br>
> drwxr-xr-x 9 mastix docker 4096 Jun 12 17:39 ../<br>
> -rw-r–r– 1 root root 0 Jun 14 21:21 access.log<br>
> -rw-r–r– 1 root root 0 Jun 14 21:21 error.log<br>
> -rw-r–r– 1 root root 2696 Jun 14 21:21 mongodb-projectwebdev.log<br>
> -rw-r–r– 1 root root 200 Jun 14 21:21 nginx-reverse-proxy-blog.access.log<br>
> -rw-r–r– 1 root root 637 Jun 14 21:21 nginx-reverse-proxy-blog-api.access.log<br>
> -rw-r–r– 1 root root 0 Jun 14 21:21 nginx-reverse-proxy-blog-api.error.log<br>
> -rw-r–r– 1 root root 0 Jun 14 21:21 nginx-reverse-proxy-blog.error.log<br>
> -rw-r–r– 1 root root 0 Jun 14 21:21 pm2-0.log<br>
> -rw-r–r– 1 root root 199 Jun 14 21:21 project-webdev.access.log<br>
> -rw-r–r– 1 root root 0 Jun 14 21:21 project-webdev.error.log

Remember: We've told each container to log its files into our 

<span>/opt/docker/logs</span>

 directory on the Docker host... And now we have them all in one place.

That's it. I hope you had fun learning Docker with this session. And if you find any bugs, I'm happy to fix them. Just add a comment or create an issue in the Github repository.

Greetz,

Sascha
