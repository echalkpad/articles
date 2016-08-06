---
title: "Series: How to create your own website based on Docker (Part 7 - Creating the mongodb Docker container)"
url: "http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part7-mongodb-docker-image.html"
date_saved: "07/19/2016 08:05-07"
excerpt: "This is part 7 of the series:&#xA0;How to create your own website based on Docker. It's about time to create our first image/container that is part of the real application stack. This container acts&hellip;"
thumbnail: [![](png/series_how_to_create_your_own_website_based_on_docker_part_7__creating_the_mongodb_docker_container.png)](png/full/series_how_to_create_your_own_website_based_on_docker_part_7__creating_the_mongodb_docker_container.png)
---

# Series: How to create your own website based on Docker (Part 7 - Creating the mongodb Docker container)

[Original URL](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part7-mongodb-docker-image.html)

> This is part 7 of the series: How to create your own website based on Docker. It's about time to create our first image/container that is part of the real application stack. This container acts...

[![](http://1.bp.blogspot.com/-Beq3VLY-9VM/VVoForLvhcI/AAAAAAAAr2k/coLCk7hkrQo/s1600/220px-MongoDB_Logo%2B(1).png)](http://1.bp.blogspot.com/-Beq3VLY-9VM/VVoForLvhcI/AAAAAAAAr2k/coLCk7hkrQo/s1600/220px-MongoDB_Logo%2B%281%29.png)<br>
This is part 7 of the series: [How to create your own website based on Docker](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part1.html).

It's about time to create our first image/container that is part of the real application stack. This container acts as persistence layer for the REST API (which we will create in the next part of this series). So the only component that talks to the database is the ioJS REST API container. In this part of the series, we'll have a look into how you can create your own mongodb container based on the [official mongodb image](https://registry.hub.docker.com/_/mongo/).

<span>All files mentioned in this series are </span>

[available on Github](https://github.com/mastix/project-webdev-docker-demo)

<span>, so you can play around with it! :)</span>

<br>

<span>
  <br>
</span>

Let's create a new directory called /opt/docker/mongodb/ and within this new directory we'll create two folders and one file:

> # mkdir -p /opt/docker/mongodb/config/<br>
> # mkdir /opt/docker/mongodb/db/<br>
> # > /opt/docker/mongodb/Dockerfile

Since I don't want to re-invent the wheel, I'll have a look at the official [mongodb Docker image](https://registry.hub.docker.com/_/mongo/) and we're basically using the same [mongodb 3.0 Dockerfile](https://github.com/docker-library/mongo/tree/master/3.0) for our design. Since we want to run this mongodb database on our own Ubuntu Base Container, we need to make some changes to official mongodb Docker image.

The official [mongodb Dockerfile](https://github.com/docker-library/mongo/blob/master/3.0/Dockerfile) uses Debian wheezy as base image, which is not what we want:

> FROM debian:wheezy

We are going to use our own Ubuntu Base Image for the mongodb image and since we use Docker Compose, we must specify the correct base image name, which is a concatenation of "docker_" and the image name that we have specified in our 

<span>docker-compose.yml</span>

 - so in our case that would be "docker_ubuntubase". So we're changing the aforementioned line to use our base image:

> # Pull base image.<br>
> FROM docker_ubuntubase

Since the original Dockerfile only allows us to mount /data/db as volume, so we're extending it to also allow the mongodb log directory:

Replace the following line:

> VOLUME /data/db

With this line:

> VOLUME ["/data/db","/var/log/mongodb/"]

I'd like to have my configurations in a subfolder called "config", so we need to adjust another line:

Replace the following lines:

> COPY docker-entrypoint.sh /entrypoint.sh<br>
> ENTRYPOINT ["/entrypoint.sh"]

With these lines:

> COPY ./config/docker-entrypoint.sh /tmp/entrypoint.sh<br>
> RUN ["chmod", "+x", "/tmp/entrypoint.sh"]<br>
> ENTRYPOINT ["/tmp/entrypoint.sh"]

These lines copy a script called 

<span>./config/docker-entrypoint.sh</span>

 to the 

<span>/tmp/</span>

 folder in the container, make it executable and run it once, once the container has started. You can find the 

<span>docker-entrypoint.sh</span>

 file in the [official mongodb docker repository on GitHub](https://github.com/docker-library/mongo/blob/master/3.0/docker-entrypoint.sh). Just copy that file into the config directory, which you have to create if you haven't done so already.

Let's create our own mongodb configuration file to set some parameters.

To do so, create a file called 

<span>/opt/docker/mongodb/config/mongodb.conf</span>

 and add the following lines (important: YAML does not accept tabs; use spaces instead!):

> systemLog:<br>
> destination: file<br>
> path: "/var/log/mongodb/mongodb-projectwebdev.log"<br>
> logAppend: true<br>
> storage:<br>
> journal:<br>
> enabled: true<br>
> net:<br>
> port: 3333<br>
> http:<br>
> enabled: false<br>
> JSONPEnabled: false<br>
> RESTInterfaceEnabled: false

Now add the following lines to your Dockerfile to copy our new custom config file to our image:

> RUN mkdir -p /var/log/mongodb && chown -R mongodb:mongodb /var/log/mongodb<br>
> COPY ./config/mongodb.conf /etc/mongod.conf

Since we want to load our custom config now, we'll need to changed the way we start mongodb, so we change the following line from

> CMD ["mongod"]

to

> CMD ["mongod", "-f", "/etc/mongod.conf"]

Our folder structure must look like this now:

> +– /opt/docker/mongodb<br>
> ¦ +– config<br>
> ¦ ¦ +– docker-entrypoint.sh<br>
> ¦ ¦ +– mongodb.conf<br>
> ¦ +– db<br>
> ¦ +– Dockerfile

Another thing we can remove is the 

<span>EXPOSE</span>

 instruction, since we already specified that in our 

<span>docker-compose.yml</span>

.

So the complete Dockerfile will look like this now:

> # Pull base image.<br>
> FROM docker_ubuntubase

> # add our user and group first to make sure their IDs get assigned consistently, regardless of whatever dependencies get added<br>
> RUN groupadd -r mongodb && useradd -r -g mongodb mongodb

> RUN apt-get update \<br>
> && apt-get install -y –no-install-recommends \<br>
> ca-certificates curl \<br>
> numactl \<br>
> && rm -rf /var/lib/apt/lists/*

> # grab gosu for easy step-down from root<br>
> RUN gpg –keyserver ha.pool.sks-keyservers.net –recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4<br>
> RUN curl -o /usr/local/bin/gosu -SL "<https://github.com/tianon/gosu/releases/download/1.2/gosu-$(dpkg> –print-architecture)" \<br>
> && curl -o /usr/local/bin/gosu.asc -SL "<https://github.com/tianon/gosu/releases/download/1.2/gosu-$(dpkg> –print-architecture).asc" \<br>
> && gpg –verify /usr/local/bin/gosu.asc \<br>
> && rm /usr/local/bin/gosu.asc \<br>
> && chmod +x /usr/local/bin/gosu

> # gpg: key 7F0CEB10: public key "Richard Kreuter <richard@10gen.com>" imported<br>
> RUN apt-key adv –keyserver ha.pool.sks-keyservers.net –recv-keys 492EAFE8CD016A07919F1D2B9ECBEC467F0CEB10

> ENV MONGO_MAJOR 3.0<br>
> ENV MONGO_VERSION 3.0.3

> RUN echo "deb <http://repo.mongodb.org/apt/debian> wheezy/mongodb-org/$MONGO_MAJOR main" > /etc/apt/sources.list.d/mongodb-org.list

> RUN set -x \<br>
> && apt-get update \<br>
> && apt-get install -y mongodb-org=$MONGO_VERSION \<br>
> && rm -rf /var/lib/apt/lists/apt.dockerproject.org_repo_dists_ubuntu-xenial_InRelease /var/lib/apt/lists/apt.dockerproject.org_repo_dists_ubuntu-xenial_main_binary-amd64_Packages /var/lib/apt/lists/deb.nodesource.com_node%5f6.x_dists_xenial_InRelease /var/lib/apt/lists/deb.nodesource.com_node%5f6.x_dists_xenial_main_binary-amd64_Packages /var/lib/apt/lists/deb.nodesource.com_node%5f6.x_dists_xenial_main_binary-i386_Packages /var/lib/apt/lists/deb.nodesource.com_node%5f6.x_dists_xenial_main_source_Sources /var/lib/apt/lists/lock /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_InRelease /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_main_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_main_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_main_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_main_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_universe_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_universe_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_universe_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_universe_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_InRelease /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_main_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_main_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_main_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_main_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_multiverse_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_multiverse_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_multiverse_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_multiverse_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_restricted_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_restricted_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_restricted_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_restricted_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_universe_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_universe_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_universe_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_universe_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_InRelease /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_main_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_main_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_main_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_main_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_multiverse_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_multiverse_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_multiverse_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_multiverse_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_universe_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_universe_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_universe_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_universe_source_Sources /var/lib/apt/lists/nginx.org_packages_ubuntu_dists_xenial_nginx_binary-amd64_Packages /var/lib/apt/lists/nginx.org_packages_ubuntu_dists_xenial_nginx_binary-i386_Packages /var/lib/apt/lists/nginx.org_packages_ubuntu_dists_xenial_nginx_source_Sources /var/lib/apt/lists/nginx.org_packages_ubuntu_dists_xenial_Release /var/lib/apt/lists/partial /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_InRelease /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_main_binary-amd64_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_main_binary-i386_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_main_i18n_Translation-en /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_main_source_Sources /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_multiverse_binary-amd64_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_multiverse_binary-i386_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_multiverse_i18n_Translation-en /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_multiverse_source_Sources /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_universe_binary-amd64_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_universe_binary-i386_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_universe_i18n_Translation-en /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_universe_source_Sources \<br>
> && rm -rf /var/lib/mongodb \<br>
> && mv /etc/mongod.conf /etc/mongod.conf.orig

> RUN mkdir -p /data/db && chown -R mongodb:mongodb /data/db<br>
> RUN mkdir -p /var/log/mongodb && chown -R mongodb:mongodb /var/log/mongodb

> VOLUME ["/data/db","/var/log/mongodb/"]

> COPY ./config/docker-entrypoint.sh /tmp/entrypoint.sh<br>
> COPY ./config/mongodb.conf /etc/mongod.conf<br>
> RUN ["chmod", "+x", "/tmp/entrypoint.sh"]

> ENTRYPOINT ["/tmp/entrypoint.sh"]

> CMD ["mongod", "-f", "/etc/mongod.conf"]

Source: <https://github.com/mastix/project-webdev-docker-demo/blob/master/mongodb/Dockerfile>

This is pretty much it. That's all we need to create our mongodb database container, that will run on the default port 

<span>3333</span>

 - but will only be accessible from the REST API, since we linked that container to the ioJS REST API container only, see our 

<span>docker-compose.yml</span>

 file again:

> projectwebdevapi:<br>
> build: ./projectwebdev-api<br>
> expose:

> ```
>     - “3000”  
> **links:  
>     - mongodb:db**  
> volumes:  
>     - ./logs/:/var/log/supervisor/  
>     - ./projectwebdev-api/app:/var/www/html
> ```

In the next chapter it's getting more interesting: [Let's create our REST API container, that talks to the mongodb container!](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part8-iojs-rest-api-docker-image.html)
