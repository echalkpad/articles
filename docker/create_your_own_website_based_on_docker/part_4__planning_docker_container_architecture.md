---
title: "Series: How to create your own website based on Docker (Part 4 - Planning Docker container architecture)"
url: "http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part4-docker-container-architecture.html"
date_saved: "07/19/2016 08:04-50"
excerpt: "Let's design our docker container architecture This is part 4 of the series:&#xA0;How to create your own website based on Docker. Docker and Docker Compose are now up and running. So it's about time&hellip;"
thumbnail: [![](png/series_how_to_create_your_own_website_based_on_docker_part_4__planning_docker_container_architecture.png)](png/full/series_how_to_create_your_own_website_based_on_docker_part_4__planning_docker_container_architecture.png)
---

# Series: How to create your own website based on Docker (Part 4 - Planning Docker container architecture)

[Original URL](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part4-docker-container-architecture.html)

> Let's design our docker container architecture This is part 4 of the series: How to create your own website based on Docker. Docker and Docker Compose are now up and running. So it's about time...

## Let's design our docker container architecture

This is part 4 of the series: [How to create your own website based on Docker](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part1.html).

Docker and Docker Compose are now up and running. So it's about time to let them all play together.

Before we start planning our container architecture, we need to make sure that we understand what we're trying to achieve.

1. We want to be able to port our apps to any other platform as easy as possible
2. We want our applications to be as separated as possible (every container should have one purpose)
3. We want to create more instances of an application container if needed
4. We don't want crashed applications to crash other applications

So based on this list we need to figure out what components will be needed for the site.

## Let's define all components to be "dockerized"

### 

### Component 1: nginx reverse proxy

I usually start with nginx as reverse proxy in front of all other services. This allows me to have a single entry point for all requests and to distribute them internally to all containers which should be accessible from the web. This reverse proxy will listen on port 80 and will redirect all requests based on the context root, the subdomain and/or the hostname.

Here's an example:

- <span>www.project-webdev.com:80</span>

   => redirects to my blog which listens on port 

  <span>8081</span>

   internally (the projectwebdev blog will be hosted on another nginx machine).
- <span>api.project-webdev.com:80</span>

   => redirects to my REST API which listens on port 

  <span>3001</span>

   internally (the API will be an ioJS/nodeJS application).
- _Also possible:_ 

  <span>www.project-webdev.com:80/api </span>

  => redirects to my REST API which listens on port 

  <span>3001</span>

   internally (the API will be an ioJS/nodeJS application).

As you can see, all requests will go through the nginx reverse proxy and will be redirected internally to the appropriate service.

**What's important to mention:** The internal ports (e.g. 

<span>8081</span>

, 

<span>3001</span>

,...) should not be exposed to public, so it should not be possible to access the blog directly (like 

<span>www.project-webdev.com:8081</span>

).

This would be our first component, right? Wait! Where should our new nginx reverse proxy run on?

We need an operating system... so basically our first component would actually be a container that contains the operating system. For that I'm going to use Ubuntu 14.04 LTS.

So right now our current architecture would look like this:<br>
[![nginx reverse proxy docker container](http://1.bp.blogspot.com/-jsIOssVlnRE/VVN-T-GaveI/AAAAAAAAruo/krZtkQnyU6k/s320/Screen%2BShot%2B2015-05-13%2Bat%2B18.38.57.png "nginx reverse proxy docker container")](http://1.bp.blogspot.com/-jsIOssVlnRE/VVN-T-GaveI/AAAAAAAAruo/krZtkQnyU6k/s1600/Screen%2BShot%2B2015-05-13%2Bat%2B18.38.57.png)

### Component 2: 

<span>nginx web server for our website</span>

The next thing we'll need for our web site is the markup for the website. So all we need now is another nginx web service, but this time it will not act as reverse proxy, but as a real web server hosting our files. Since we don't want any port conflicts my basic rule is that 4-digit ports are never exposed to public. (Port 

<span>80</span>

 and 

<span>443</span>

 (SSL) are allowed to be accessed from outside).

Since Docker works with [container links](https://docs.docker.com/userguide/dockerlinks), we need to add a link to our reverse proxy, so that it can communicate internally with our blog application (_nginx web site_), which listens on port 

<span>8081</span>

.

[![](http://4.bp.blogspot.com/-nb1HHA5NCCE/VVN_AZuUtgI/AAAAAAAAruw/gr2ki3wBWJ0/s320/Screen%2BShot%2B2015-05-13%2Bat%2B18.41.49.png)](http://4.bp.blogspot.com/-nb1HHA5NCCE/VVN_AZuUtgI/AAAAAAAAruw/gr2ki3wBWJ0/s1600/Screen%2BShot%2B2015-05-13%2Bat%2B18.41.49.png)

As mentioned before, port 

<span>8081</span>

 can not be accessed from outside - therefore painted in 

<span>purple</span>

.

You can also see that we've [mounted a directory](https://docs.docker.com/userguide/dockervolumes/) on the docker host into our blog's docker container. We're doing this, because we want to be able to change the website from outside later, without restarting the container.

**Technically it would look like the following - I'll go into details later:**<br>
In the docker container (our provider's Ubuntu server), we'll create a directory called 

<span>/opt/docker/projectwebdev/html/</span>

 which will be mounted as 

<span>/var/www/html/</span>

 in the container (the directory which nginx uses to load the _HTML_, _CSS_ and _JS_ files from later). So whenever nginx (the one in our _nginx web site_ container) receives a request from a visitor, it will load the files from our real server (from 

<span>/opt/docker/projectwebdev/html/</span>

<span>) and will provide it to him - I think you’ve got it, right? It’s not that hard.</span>

<br>

<span>
  <br>
</span>

### Component 3: ioJS REST API container

Our website should fetch information from a REST API. Therefore we will need an ioJS application that will provide all data for the website asynchronously. The website will use a call to 

<span>
  <a href="http://api.project-webdev.com">http://api.project-webdev.com</a>
</span>

 to fetch the contents or any other information needed.

Since this is a new URL, we need to link this container to our nginx as well, so that we can build our redirect to that container internally.<br>
[![](http://1.bp.blogspot.com/-3kCBGOb1MAM/VVOB7BAFqqI/AAAAAAAAru8/UrS7bihMGAs/s320/Screen%2BShot%2B2015-05-13%2Bat%2B18.54.20.png)](http://1.bp.blogspot.com/-3kCBGOb1MAM/VVOB7BAFqqI/AAAAAAAAru8/UrS7bihMGAs/s1600/Screen%2BShot%2B2015-05-13%2Bat%2B18.54.20.png)

### Component 4: mongoDB database container

A REST API does not make sense without any data persistence in the back. Therefore we need to add our mongoDB to that architecture as well. This instance listens on port 

<span>3333</span>

 and should only be accessible via REST API (and therefore implicitly via our nginx reverse proxy), which is why we need to add a link to our REST API so that it can access the data in the mongoDB.

[![](http://4.bp.blogspot.com/-xsatMxuddqQ/VVODE7NfMVI/AAAAAAAArvE/A55n8EP3jjE/s320/Screen%2BShot%2B2015-05-13%2Bat%2B18.59.35.png)](http://4.bp.blogspot.com/-xsatMxuddqQ/VVODE7NfMVI/AAAAAAAArvE/A55n8EP3jjE/s1600/Screen%2BShot%2B2015-05-13%2Bat%2B18.59.35.png)

### Additional component: Logs

When running this application stack in the wild later, it's very important to be able to analyse the logs (e.g. using the [ELK stack](https://www.digitalocean.com/community/tutorials/how-to-use-logstash-and-kibana-to-centralize-and-visualize-logs-on-ubuntu-14-04)). Since we have several containers, it's does not make to get the logs from each instance separately. So we're creating another volume mount which acts as central storage for all log files of all used containers. This directory can later be used by log file analysis tools, so you can analyse the hell out of your logs. :)

[![](http://2.bp.blogspot.com/-UMTmxr4loSA/VVOEJKgBLTI/AAAAAAAArvM/1IqHQK7OS-8/s320/Screen%2BShot%2B2015-05-13%2Bat%2B19.04.15.png)](http://2.bp.blogspot.com/-UMTmxr4loSA/VVOEJKgBLTI/AAAAAAAArvM/1IqHQK7OS-8/s1600/Screen%2BShot%2B2015-05-13%2Bat%2B19.04.15.png)

## Conclusion

We have now several containers that act as "platform" for a certain purpose. They are all completely encapsulated and are sharing their resources (thanks to Docker).

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

That's it... that's our "dockerized" architecture for our projectwebdev website based on Docker containers. Let's create our Docker Compose file now... [in the next part of this series](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part5-docker-compose.html).
