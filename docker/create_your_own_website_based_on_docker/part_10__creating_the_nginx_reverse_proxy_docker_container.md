---
title: "Series: How to create your own website based on Docker (Part 10 - Creating the nginx reverse proxy Docker container)"
url: "http://project-webdev.blogspot.de/2015/06/create-site-based-on-docker-part10-nginx-reverse-proxy-docker-image.html"
date_saved: "07/19/2016 08:05-23"
excerpt: "This is part 10 of the series:&#xA0;How to create your own website based on Docker. Let's recap: We've created the backend containers consisting of a mongodb container and a ioJS/hapiJS container.&hellip;"
thumbnail: [![](png/series_how_to_create_your_own_website_based_on_docker_part_10__creating_the_nginx_reverse_proxy_docker_container.png)](png/full/series_how_to_create_your_own_website_based_on_docker_part_10__creating_the_nginx_reverse_proxy_docker_container.png)
---

# Series: How to create your own website based on Docker (Part 10 - Creating the nginx reverse proxy Docker container)

[Original URL](http://project-webdev.blogspot.de/2015/06/create-site-based-on-docker-part10-nginx-reverse-proxy-docker-image.html)

> This is part 10 of the series: How to create your own website based on Docker. Let's recap: We've created the backend containers consisting of a mongodb container and a ioJS/hapiJS container....

[![](http://4.bp.blogspot.com/-q5gsCoJDSTo/VVytJR3IJoI/AAAAAAAAr3I/h-2kG52sdtE/s320/300px-Nginx_logo.svg.png)](http://4.bp.blogspot.com/-q5gsCoJDSTo/VVytJR3IJoI/AAAAAAAAr3I/h-2kG52sdtE/s1600/300px-Nginx_logo.svg.png)

This is part 10 of the series: [How to create your own website based on Docker](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part1.html).

Let's recap: We've created the backend containers consisting of a [mongodb container](http://project-webdev.blogspot.com/2015/05/create-site-based-on-docker-part7-mongodb-docker-image.html) and a [ioJS/hapiJS](http://project-webdev.blogspot.com/2015/05/create-site-based-on-docker-part8-iojs-rest-api-docker-image.html) container. We've also created the [nginx/Angular 2.0 frontend container](http://project-webdev.blogspot.com/2015/06/create-site-based-on-docker-part9-nginx-website-docker-image.html) that makes use of the new backend.

As mentioned in the [4th part of this series](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part4-docker-container-architecture.html) we've defined that every request must go through an nginx reverse proxy. This proxy decides where the requests go to and what services are accessible from outside. So in order to make the whole setup available from the web, you need to configure the nginx reverse proxy that will route all requests to the proper docker container.

## Source code

<span>All files mentioned in this series are </span>

[available on Github](https://github.com/mastix/project-webdev-docker-demo)

<span>, so you can play around with it! :)</span>

<br>

<span>
  <br>
</span>

## Talking about container links again

Remember that we have linked containers together? And that we don't know the IP addresses, because Docker takes care of that and reserves them while creating the container?

That's bad for a reverse proxy, since it needs to know where to route the requests to - but there's a good thing: IP addresses and ports are available as environment variable within each container. Bad thing: **nginx configurations can't read environment variables!** So using links makes creating this container hard and more complex than it actually should be - but there's a solution for that of course, which we will cover that later in that post.

## So what does this container have to do?

Well, that's pretty simple... it needs to glue everything together, so it needs to collect all services that should be accessible from outside.

So in our case we'd need an nginx configuration for:

- Our REST-API container based on ioJS & hapiJS
- Our frontend container based on nginx and Angular 2.0

We **don't** want to expose:

- Our mongodb container
- Our ubuntu base container

## Let's get started - creating the nginx image

Creating the nginx image is basically the same every time. Let's create a new directory called 

<span>/opt/docker/nginx-reverse-proxy/</span>

 and within this new directory we'll create other directories called 

<span>config</span>

<span> &amp; </span>

<span>html</span>

<span> and </span>

our 

<span>Dockerfile</span>

:

> # mkdir -p /opt/docker/projectwebdev/config/<br>
> # mkdir -p /opt/docker/projectwebdev/html/<br>
> # > /opt/docker/projectwebdev/Dockerfile

So just create your 

<span>/opt/docker/nginx-reverse-proxy/Dockerfile</span>

 with the following content:

> # Pull base image.<br>
> FROM docker_ubuntubase<br>
> ENV DEBIAN_FRONTEND noninteractive<br>
> # Install Nginx.<br>
> RUN \<br>
> add-apt-repository -y ppa:nginx/stable && \<br>
> apt-get update && \<br>
> apt-get install -y nginx && \<br>
> rm -rf /var/lib/apt/lists/apt.dockerproject.org_repo_dists_ubuntu-xenial_InRelease /var/lib/apt/lists/apt.dockerproject.org_repo_dists_ubuntu-xenial_main_binary-amd64_Packages /var/lib/apt/lists/deb.nodesource.com_node%5f6.x_dists_xenial_InRelease /var/lib/apt/lists/deb.nodesource.com_node%5f6.x_dists_xenial_main_binary-amd64_Packages /var/lib/apt/lists/deb.nodesource.com_node%5f6.x_dists_xenial_main_binary-i386_Packages /var/lib/apt/lists/deb.nodesource.com_node%5f6.x_dists_xenial_main_source_Sources /var/lib/apt/lists/lock /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_InRelease /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_main_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_main_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_main_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_main_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_universe_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_universe_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_universe_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_universe_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_InRelease /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_main_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_main_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_main_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_main_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_multiverse_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_multiverse_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_multiverse_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_multiverse_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_restricted_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_restricted_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_restricted_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_restricted_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_universe_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_universe_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_universe_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_universe_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_InRelease /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_main_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_main_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_main_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_main_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_multiverse_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_multiverse_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_multiverse_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_multiverse_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_universe_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_universe_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_universe_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_universe_source_Sources /var/lib/apt/lists/nginx.org_packages_ubuntu_dists_xenial_nginx_binary-amd64_Packages /var/lib/apt/lists/nginx.org_packages_ubuntu_dists_xenial_nginx_binary-i386_Packages /var/lib/apt/lists/nginx.org_packages_ubuntu_dists_xenial_nginx_source_Sources /var/lib/apt/lists/nginx.org_packages_ubuntu_dists_xenial_Release /var/lib/apt/lists/partial /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_InRelease /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_main_binary-amd64_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_main_binary-i386_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_main_i18n_Translation-en /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_main_source_Sources /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_multiverse_binary-amd64_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_multiverse_binary-i386_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_multiverse_i18n_Translation-en /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_multiverse_source_Sources /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_universe_binary-amd64_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_universe_binary-i386_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_universe_i18n_Translation-en /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_universe_source_Sources && \<br>
> chown -R www-data:www-data /var/lib/nginx<br>
> # Define mountable directories.<br>
> VOLUME ["/etc/nginx/certs", "/var/log/nginx", "/var/www/html"]<br>
> # Define working directory.<br>
> WORKDIR /etc/nginx<br>
> # Copy all config files<br>
> COPY config/default.conf /etc/nginx/conf.d/default.conf<br>
> COPY config/nginx.conf /etc/nginx/nginx.conf<br>
> COPY config/config.sh /etc/nginx/config.sh<br>
> RUN ["chmod", "+x", "/etc/nginx/config.sh"]<br>
> # Copy default webpage<br>
> RUN rm /var/www/html/index.nginx-debian.html<br>
> COPY html/index.html /var/www/html/index.html<br>
> COPY html/robots.txt /var/www/html/robots.txt<br>
> # Define default command.<br>
> CMD /etc/nginx/config.sh && nginx

Source: <https://github.com/mastix/project-webdev-docker-demo/blob/master/nginx-reverse-proxy/Dockerfile>

This 

<span>Dockerfile</span>

 also uses our Ubuntu base image, installs nginx and bakes our configuraton into our nginx container.

## What is this html folder for?

Before looking into the configuration we'll cover the easy stuff first. :)

While creating the directories you might have asked yourself why you need to create an html folder?! Well, that's simple: Since we're currently only developing 

<span>api.project-webdev.com</span>

 and 

<span>blog.project-webdev.com</span>

 we need a place to go when someone visits 

<span>www.project-webdev.com</span>

 - that's what this folder is for. If you don't have such a use case, you can also skip it - so this is kind of a fallback strategy.

The HTML page is pretty simple:

> <!DOCTYPE html><br>
> <html><br>
> <head><br>
> <title>Welcome to this empty page!</title><br>
> <style><br>
> body {<br>
> width: 35em;<br>
> margin: 0 auto;<br>
> font-family: Tahoma, Verdana, Arial, sans-serif;<br>
> }<br>
> </style><br>
> </head><br>
> <body><br>
> <h1>Welcome to this empty page!</h1><br>
> <p>If you see this page, you'll see that it is empty.</p><br>
> <p><em>Will this change soon...? Hell yeah it will! ;)</em></p><br>
> </body><br>
> </html>

So let's put this code into the following file 

<span>nginx-reverse-proxy/html/index.html</span>

.

## The nginx configuration

Now it's getting ~~difficult~~ interesting. :)

As mentioned before, our nginx container needs to route all requests based on the URL to our containers.

So we need two routes/locations

- <span>api.project-webdev.com</span>

   routes to my Docker REST API container
- <span>blog.project-webdev.com routes</span>

   to my Docker blog container

Since we don't know the IP addresses to route to during development, we need to work with custom placeholders, that we need to replace via shell script once the container starts. In the following example you'll see that we're using **two place holders** for our **two exposed services**:

- BLOG_IP:BLOG_PORT
- BLOGAPI_IP:BLOGAPI_PORT

We're going to replace these two placeholders with the correct value from the environment variables that docker offers us when linking containers together.

So you need a config file called 

<span>/opt/docker/nginx-reverse-proxy/config/default.conf</span>

that contains your nginx server configuration:

> upstream blog {<br>
> server **BLOG_IP:BLOG_PORT**; #Blog<br>
> }<br>
> upstream blog-api {<br>
> server **BLOGAPI_IP:BLOGAPI_PORT**; #Blog-API<br>
> }<br>
> ## Start blog.project-webdev.com ##<br>
> server {<br>
> listen 80;<br>
> server_name blog.project-webdev.com;<br>
> access_log /var/log/nginx/nginx-reverse-proxy-blog.access.log;<br>
> error_log /var/log/nginx/nginx-reverse-proxy-blog.error.log;<br>
> root /var/www/html;<br>
> index index.html index.htm;<br>
> ## send request back to blog ##<br>
> location / {<br>
> **proxy_pass <http://blog>;**<br>
> proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;<br>
> proxy_redirect off;<br>
> proxy_buffering off;<br>
> proxy_set_header Host $host;<br>
> proxy_set_header X-Real-IP $remote_addr;<br>
> proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;<br>
> }<br>
> }<br>
> ## End blog.project-webdev.com ##<br>
> ## Start api.project-webdev.com ##<br>
> server {<br>
> listen 80;<br>
> server_name api.project-webdev.com*;<br>
> access_log /var/log/nginx/nginx-reverse-proxy-blog-api.access.log;<br>
> error_log /var/log/nginx/nginx-reverse-proxy-blog-api.error.log;<br>
> ## send request back to blog api ##<br>
> location / {<br>
> **proxy_pass <http://blog-api>;**<br>
> proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;<br>
> proxy_redirect off;<br>
> proxy_buffering off;<br>
> proxy_set_header Host $host;<br>
> proxy_set_header X-Real-IP $remote_addr;<br>
> proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

> ```
>  \# send the CORS headers  
>  add\_header ‘Access-Control-Allow-Credentials’ ‘true’;  
>  add\_header ‘Access-Control-Allow-Origin’      ’http://blog.project-webdev.com’;  
> ```

> }<br>
> }

## End api.project-webdev.com ##

This configuration file contains two locations, one that redirects to our blog (

<span>upstream blog  {[..]}</span>

) and to our api (

<span>upstream blog-api{[…]}</span>

). As mentioned before, we're going to replace the IP and the port soon. :)

So what's happening is that every request against blog.project-webdev.com will be redirected to the corresponding upstream:

> server {<br>
> listen 80;<br>
> server_name blog.project-webdev.com;<br>
> [...]<br>
> location / {<br>
> proxy_pass <http://blog>;<br>
> [...]<br>
> }<br>
> }

The same works for the REST API:

> server {<br>
> listen 80;<br>
> server_name api.project-webdev.com;<br>
> [...]<br>
> location / {<br>
> proxy_pass <http://blog-api>;<br>
> [...]<br>
> }<br>
> }

## Using docker's environment variables

In order to use docker's environment variables we need to run a script every time the container starts. This script will check the 

<span>default.conf</span>

 file that you have just created and replaces your placeholders with the values from the environment variables; after that it will start nginx.

See the last line of the dockerfile that triggers the script execution:

> CMD /etc/nginx/config.sh && nginx

**Let's recap quickly:** As mentioned in [previous posts](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part8-iojs-rest-api-docker-image.html), Docker creates [environment variables with IP and port information when you link containers together](https://docs.docker.com/userguide/dockerlinks/#environment-variables). These variables contain all information that you need to access your containers - and that's exactly what we want to do here.

The following script will replace our custom placeholders in our 

<span>default.conf</span>

 file with the corresponding values from the environment variables that Docker has created for us, so let's create the aforementioned 

<span>/opt/docker/nginx-reverse-proxy/config/config.sh</span>

 file:

> #!/bin/bash<br>
> # Using environment variables to set nginx configuration<br>
> # Settings for the blog<br>
> echo "START UPDATING DEFAULT CONF"<br>
> [ -z "${BLOG_PORT_8081_TCP_ADDR}" ] && echo "\$BLOG_PORT_8081_TCP_ADDR is not set" || sed -i "s/BLOG_IP/${BLOG_PORT_8081_TCP_ADDR}/" /etc/nginx/conf.d/default.conf<br>
> [ -z "${BLOG_PORT_8081_TCP_PORT}" ] && echo "\$BLOG_PORT_8081_TCP_PORT is not set" || sed -i "s/BLOG_PORT/${BLOG_PORT_8081_TCP_PORT}/" /etc/nginx/conf.d/default.conf<br>
> [ -z "${BLOGAPI_PORT_3000_TCP_ADDR}" ] && echo "\$BLOGAPI_PORT_3000_TCP_ADDR is not set" || sed -i "s/BLOGAPI_IP/${BLOGAPI_PORT_3000_TCP_ADDR}/" /etc/nginx/conf.d/default.conf<br>
> [ -z "${BLOGAPI_PORT_3000_TCP_PORT}" ] && echo "\$BLOGAPI_PORT_3000_TCP_PORT is not set" || sed -i "s/BLOGAPI_PORT/${BLOGAPI_PORT_3000_TCP_PORT}/" /etc/nginx/conf.d/default.conf<br>
> echo "CHANGED DEFAULT CONF"<br>
> cat /etc/nginx/conf.d/default.conf<br>
> echo "END UPDATING DEFAULT CONF"

This script uses the basic [sed (stream editor)](https://en.wikipedia.org/wiki/Sed) command to replace the strings.

See the following example, that demonstrates how the IP address for the blog is being replaced:

> [ -z "${BLOG_PORT_8081_TCP_ADDR}" ] && echo "\$BLOG_PORT_8081_TCP_ADDR is not set" || **sed -i "s/BLOG_IP/${BLOG_PORT_8081_TCP_ADDR}/" /etc/nginx/conf.d/default.conf**

- First it checks whether the 

  <span>BLOG_PORT_8081_TCP_ADDR</span>

   exists as environment variable
- If that is true, it will call the sed command, which looks for 

  <span>BLOG_IP</span>

   in the /

  <span>etc/nginx/conf.d/default.conf</span>

   file (which has been copied from our Docker host into the image - see Dockerfile)
- And will then replace it with the value from the environment variable 

  <span>BLOG_PORT_8081_TCP_ADDR</span>

  <span>.</span>

And that's all the magic! ;)

So when the script has run, it will have replaced the placeholders in our config file so that it looks like this:

> CHANGED DEFAULT CONF<br>
> upstream blog {<br>
> server 172.17.0.14:8081; #Blog<br>
> }<br>
> nginxreverseproxy_1 |<br>
> upstream blog-api {<br>
> server 172.17.0.10:3000; #Blog-API<br>
> }

... and therefore our nginx reverse proxy is ready to distribute our requests to our containers, since it knows their port and ip address now! :)
