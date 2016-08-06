---
title: "Series: How to create your own website based on Docker (Part 5 - Creating our Docker Compose file)"
url: "http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part5-docker-compose.html"
date_saved: "07/19/2016 08:04-55"
excerpt: "This is part 5 of the series:&#xA0;How to create your own website based on Docker. In the last part of the series, we have planned and created our Docker container architecture. So now it's about&hellip;"
thumbnail: [![](png/series_how_to_create_your_own_website_based_on_docker_part_5__creating_our_docker_compose_file.png)](png/full/series_how_to_create_your_own_website_based_on_docker_part_5__creating_our_docker_compose_file.png)
---

# Series: How to create your own website based on Docker (Part 5 - Creating our Docker Compose file)

[Original URL](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part5-docker-compose.html)

> This is part 5 of the series: How to create your own website based on Docker. In the last part of the series, we have planned and created our Docker container architecture. So now it's about...

This is part 5 of the series: [How to create your own website based on Docker](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part1.html).

In the [last part of the series](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part4-docker-container-architecture.html), we have planned and created our Docker container architecture. So now it's about time to turn this architecture into a real scenario - and that's what we need Docker Compose for.

## Source code

<span>All files mentioned in this series are </span>

[available on Github](https://github.com/mastix/project-webdev-docker-demo)

<span>, so you can play around with it! :)</span>

<br>

<span>
  <br>
</span>

## What is Docker Compose?

Compose is a tool for defining and running complex applications with Docker. With Compose, you define a multi-container application in a single file, then spin your application up in a single command which does everything that needs to be done to get it running.

_Compose is great for development environments, staging servers, and CI. We don't recommend that you use it in production yet. (Source: <https://docs.docker.com/compose>)_

- 
- There are three steps involved when using docker compose:

- We need image files for each container (we'll start with that in the next chapter)

- Then we need to create a 

  <span>docker-compose.yml</span>

   file that tell docker compose which containers must be started, including all options (like volumes, links, ports,...)
- At last we need to run 

  <span>docker-compose up</span>

   to start up our container architecture (the configuration from the YAML file)

Since we have just created our architecture, we're starting with step 2 now and will create the image files later. This will show you how we can create a docker compose yaml file based on our architecture.

## Implementing our container design

Let's recap - this is what our architecture looks like:

[![](http://2.bp.blogspot.com/-UMTmxr4loSA/VVOEJKgBLTI/AAAAAAAArvM/1IqHQK7OS-8/s320/Screen%2BShot%2B2015-05-13%2Bat%2B19.04.15.png)](http://2.bp.blogspot.com/-UMTmxr4loSA/VVOEJKgBLTI/AAAAAAAArvM/1IqHQK7OS-8/s1600/Screen%2BShot%2B2015-05-13%2Bat%2B19.04.15.png)

We're going to create a web site called **_projectwebdev_**, so the following container names are based on that name of the site. In the diagram above we can see that we have the following containers and options:

links:

- nginx website
- ioJS REST API

volumes:

- log files (

  <span>/opt/docker/logs</span>

  )

**nginx web site** links: volumes:

- web site files (

  <span>/opt/docker/projectwebdev/html</span>

  )
- log files (

  <span>/opt/docker/logs</span>

  )

**ioJS REST API** links: volumes:

- ioJS application files (

  <span>/opt/docker/projectwebdev-api/app</span>

  )
- log files (

  <span>/opt/docker/logs</span>

  )

**mongoDB database** links: volumes:

- mongoDB files (

  <span>/opt/docker/mongodb/db</span>

  )
- log files (

  <span>/opt/docker/logs</span>

  )

## The Docker directory structure on my VM

I will use the following folder structure on my Ubuntu VM to host all Docker images/containers:

> /opt/docker/<br>
> │<br>
> ├── logs<br>
> │<br>
> ├── mongodb<br>
> │<br>
> ├── nginx-reverse-proxy<br>
> │<br>
> ├── projectwebdev<br>
> │<br>
> ├── projectwebdev-api<br>
> │<br>
> ├── ubuntu-base<br>
> │<br>
> └── **docker-compose.yml**

So the 

<span>docker-compose.yml</span>

 file will be in the root directory of all docker image directories (in which we will dive into later). With this setup, I can later just copy the 

<span>/opt/docker/</span>

 folder on to another server and then just run 

<span>docker-compose up</span>

 to get everything up and running again.

You can also see that this directory structure already contains a 

<span>logs/</span>

 directory, which will be the collection point for all container logs we've been talking about in the last part of this series.

## Create the Docker Compose file

If you've never heard of YAML before, let me just tell you what it is. YAML is a recursive acronym for "YAML Ain't Markup Language". Early in its development, YAML was said to mean "Yet Another Markup Language", but it was then reinterpreted (backronyming the original acronym) to distinguish its purpose as data-oriented, rather than document markup. YAML's purpose is to have a human friendly data serialization standard for all programming languages. (see: [http://yaml.org](http://yaml.org/))

In our YAML file we will tell Docker Compose how our containers must be started, which volumes should be mounted, which containers should be linked together and what ports should be exposed. So it's basically everything from that list above.

Let's get into details - This is what our 

<span>docker-compose.yaml</span>

 file looks like:

> ubuntubase:<br>
> build: ./ubuntu-base<br>
> projectwebdev:<br>
> build: ./projectwebdev<br>
> expose:

> ```
>     - “8081”  
> volumes:  
>     - ./logs/:/var/log/nginx/  
>     - ./projectwebdev/html:/var/www/html:ro  
> ```

> projectwebdevapi:<br>
> build: ./projectwebdev-api<br>
> expose:

> ```
>     - “3000”  
> links:  
>     - mongodb:db  
> volumes:  
>     - ./logs/:/var/log/pm2/  
>     - ./projectwebdev-api/app:/var/www/html  
> ```

> mongodb:<br>
> build: ./mongodb<br>
> expose:

> ```
>     - “3333”  
> volumes:  
>     - ./logs/:/var/log/mongodb/  
>     - ./mongodb/db:/data/db  
> ```

> nginxreverseproxy:<br>
> build: ./nginx-reverse-proxy<br>
> expose:

> ```
>     - “80”  
>     - “443”  
> links:  
>     - projectwebdev:blog  
>     - projectwebdevapi:blogapi  
> ports:  
>     - “80:80”  
> volumes:  
>     - ./logs/:/var/log/nginx/
> ```

Source: <https://github.com/mastix/project-webdev-docker-demo/blob/master/docker-compose.yml>

Let's pick the 

<span>nginx reverse proxy</span>

 to explain our settings. Besides all other Docker Compose [YAML possibilies](https://docs.docker.com/compose/yml/), we'll only use 

<span>build</span>

, 

<span>expose</span>

, 

<span>links</span>

, 

<span>ports</span>

 and 

<span>volumes</span>

.

**build**: This is the path to the directory containing the Dockerfile for the image. We have supplied that value as a relative path, which means that it is interpreted relatively to the location of the YAML file itself. This directory is also the build context that is sent to the Docker daemon. All files belonging to the nginx reverse proxy reside in folder 

<span>./nginx-reverse-proxy, </span>

<span>therefore we tell Docker Compose to build the image based on the following Dockerfile</span>

<span> /opt/docker/</span>

<span>nginx-reverse-proxy/Dockerfile</span>

<span>, which we’re going to create later.</span>

<br>

<span>
  <br>
</span>

 

<span><strong>expose:</strong> This section specifies the ports to be exposed</span>

 without publishing them to the host machine - they'll only be accessible to linked services. Only the internal port can be specified - see the architecture diagram above, these exposed ports are the ports with the 

<span>purple</span>

 background color.<br>

<span>
  <strong>
</strong>
</span>

 

<span><strong>links</strong>: Here we specify all l</span>

inks to containers in other services. You can either specify both the service name and the link alias (SERVICE:ALIAS), or just the service name (which will also be used for the alias). In our design we'll use aliases, so we'll tell Docker that whenever we want to talk to our containers we want them to use 

<span>blog</span>

 (for the projectwebdev website) and 

<span>blogapi</span>

 (for our ioJS REST API).

**ports**: The ports we want to expose to the Docker host - see the yellow port in the architecture diagram above. You can either specify both ports (HOST:CONTAINER), or just the container port (a random host port will be chosen). Since we want to make sure that it's always the same port (in our case it's port 

<span>80</span>

) we specify the HOST and the CONTAINER port explicitly (which in both cases would be 

<span>80)</span>

. If your nginx reverse proxy in your container uses port 

<span>8000</span>

 and you want that port to be accessible from outside via port 

<span>80</span>

, you'll specifiy it like this: "

<span>80:8000</span>

". _Important:_ When mapping ports in the HOST:CONTAINER format, you may experience erroneous results when using a container port lower than 

<span>60</span>

, because YAML will parse numbers in the format xx:yy as sexagesimal (base 

<span>60</span>

). For this reason, Docker recommends always explicitly specifying your port mappings as String.

**volumes:** This section contains all mount paths as volumes, optionally specifying a path on the host machine (HOST:CONTAINER), or an access mode (HOST:CONTAINER:ro). The latter one (:ro = readonly) is used in our 

<span>projectwebdev</span>

 container, since we don't want the container to change the files for any reason. Only our host may provide the markup that is needed for the website.

We have now implemented our architecture with Docker Compose! Let's create each image and container so we can fire up docker compose. [We'll start with our Ubuntu Base Image](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part6-ubuntu-docker-image.html)!
