# Moving to Docker: NGINX reverse proxy with SSL termination

[Original URL](https://www.timofejew.com/moving-to-docker-nginx/)

> Now that I have Ghost running in a Docker container, it's time to move the NGINX reverse proxy from the host environment into a Docker container as well. I'll be pretty much using the same techniques...

Now that I have [Ghost](https://ghost.org) [running](https://www.timofejew.com/moving-to-docker-ghost/) in a [Docker](https://www.docker.com) container, it's time to move the [NGINX](http://nginx.org) [reverse proxy](https://en.wikipedia.org/wiki/Reverse_proxy) from the host environment into a Docker container as well. I'll be pretty much using the same techniques as I wrote in the [image hot linking article](https://www.timofejew.com/nginx-ghost-stopping-hotlink-images/), updated slightly to incorporate the latest [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) [security configuration](https://weakdh.org/sysadmin.html).

## Research

Since I wanted to use Docker [container linking](https://docs.docker.com/userguide/dockerlinks/) to automate the linkage of the containers, I thought I'd do some poking around the Internet to see what others have done. That, and see what the latest-and-greatest NGINX configurations for security and reverse proxying are.

### Security

The first thing I stumbled across was a server-side [TLS configuration generator](https://mozilla.github.io/server-side-tls/ssl-config-generator/) from the Mozilla foundation. What's really slick about this is that it has codified best practices for different servers, and for different levels of browser compatibility. Really nice.

I also discovered an [online SSL server test](https://www.ssllabs.com/ssltest/index.html) from [Qualsys SSL Labs](https://www.ssllabs.com/index.html). Once you have your server up and running, you can test your configuration with their tool, and it will supply recommendations for improving your configuration. For what it's worth, this site gets an A+ rating (as of the published date).

I found a [great resource](http://jasonwilder.com/blog/2014/03/25/automated-nginx-reverse-proxy-for-docker/) about linking containers at [Jason Wilder's blog](http://jasonwilder.com). It covers my use case exactly. What he's done is to create a Docker image, [jwilder/nginx-proxy](http://github.com/jwilder/nginx-proxy) that uses an underlying tool he created, [jwilder/docker-gen](http://github.com/jwilder/docker-gen), which in turn uses the Docker API to monitor container events, and automatically generate new configurations on the fly as containers start and stop. It's all seriously slick, and looks like it would make a very robust production environment.

However, it's a bit involved from a configuration point of view (it's using a template language to generate NGINX config files), and relies on Docker's API (which is still not 100% mature yet). For now, I'm going to keep it simple and use basic Docker linking. If the Ghost container starts to regularly fall over (which it doesn't in practice), I'll consider diving in and using his system.

## Shutting down installation

First things first, I stopped NGINX:

```
sudo service nginx stop 
```

And then removed all traces of NGINX:

```
sudo apt-get remove -y nginx 
sudo apt-get --purge -y autoremove 
```

Now I'm committed...

## System preparation

Assuming you're following along in this series, you'll have already installed `docker` on your system. If you haven't, please see the [first part](https://www.timofejew.com/moving-to-docker-ghost/) of this series for instructions.

## Docker image creation

Since a couple of the machines on `timofejew.com` will be running reverse proxies, I decided that what I would do is create a separate image for each proxy. In this case, it's tuned to run as a front-end to my Ghost blog. Here's the `Dockerfile` I created (I'm substituting `example.com`):

```
# Create proxy container for www.example.com
#
# docker build -t proxy .

FROM nginx

MAINTAINER Peter Timofejew <peter@timofejew.com>

# Set timezone
RUN echo "America/Toronto" > /etc/timezone \ 
 && dpkg-reconfigure -f noninteractive tzdata

# Install wget and install/updates certificates
RUN apt-get update \ 
 && apt-get install -y -q --no-install-recommends \
 ca-certificates \
 wget \
 && apt-get clean \
 && rm -r /var/lib/apt/lists/*

# Add main NGINX config
COPY nginx.conf /etc/nginx/

# Add DH params (generated with openssl dhparam -out dhparams.pem 2048)
COPY ssl/dhparams.pem /etc/ssl/private/

# Add www certificates
COPY ssl/ssl-ca-certs-startssl.pem /etc/ssl/certs/ 
COPY ssl/ssl-cert-chain-www-example-com.pem /etc/ssl/certs/ 
COPY ssl/ssl-cert-www-example-com.crt /etc/ssl/private/ 
COPY ssl/ssl-cert-www-example-com.key /etc/ssl/private/

# Add virtual hosts
COPY vhosts/ /etc/nginx/conf.d/

# Add static content
COPY html/ /usr/share/nginx/html/ 
```

As you can see from examining the `Dockerfile`, there are three directories with auxiliary files:

1. `ssl/` - this contains the [CA certificate](https://en.wikipedia.org/wiki/Certificate_authority), a [chained SSL certificate](http://security.stackexchange.com/questions/59566/ssl-certificate-chain-verification) for the host, the SSL key for the host, and a [DH parameter](https://weakdh.org/sysadmin.html) file.
2. `vhosts/` - in here are two config files for the two virtual hosts the container will handle (`example.com` and `www.example.com`).
3. `html/` - static content served up by the container.

![Business Cat!](https://www.timofejew.com/content/images/2015/09/Executie-Decision.jpg)

I made an executive decision to not mount the host's filesystem, but to keep all the data in the container itself. Here's my rationale:

- The TLS information is relatively static. This will have to be updated in a year's time, but re-generating the image and launching a new container isn't that big of a deal, and it will be a good excuse to upgrade to the latest version of NGINX and OpenSSL (which will automatically happen when the container is rebuilt).
- The virtual hosts configuration is also pretty much static. I may modify this, but I doubt it. It's stable.
- Same with the static data. I may on occasion add something else to it, but it's probably better that I add it to the `git` repo, and then rebuild the image.
- And lastly, this is not a general purpose proxy. If it was, I'd probably mount the keys, configuration, and static content from the host filesystem.

With that in mind, here's some of the key files:

### nginx.conf

```
user www-data; 
worker_processes 1;

error_log /var/log/nginx/error.log warn; 
pid /var/run/nginx.pid;

events { 
 worker_connections 1024;
}

http { 
 include /etc/nginx/mime.types;
 default_type application/octet-stream;

 log_format main '$remote_addr - $remote_user [$time_local] "$request" '
 '$status $body_bytes_sent "$http_referer" '
 '"$http_user_agent" "$http_x_forwarded_for"';

 access_log /var/log/nginx/access.log main;

 sendfile on;
 #tcp_nopush on;

 keepalive_timeout 65;

 gzip on;
 gzip_disable "msie6";
 gzip_vary on;
 gzip_proxied any;
 gzip_comp_level 6;
 gzip_buffers 16 8k;
 gzip_http_version 1.1;
 gzip_types text/plain text/css application/json application/x-javascript
 text/xml application/xml application/xml+rss text/javascript;

 # SSL intermediate configuration from https://mozilla.github.io/server-side-tls/ssl-config-generator/
 ssl_certificate /etc/ssl/certs/ssl-cert-chain-www-example-com.pem;
 ssl_certificate_key /etc/ssl/private/ssl-cert-www-example-com.key;
 ssl_dhparam /etc/ssl/private/dhparams.pem;
 ssl_session_timeout 1d;
 ssl_session_cache shared:SSL:50m;

 ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
 ssl_ciphers 'ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:AES:CAMELLIA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA';
 ssl_ecdh_curve secp384r1;
 ssl_prefer_server_ciphers on;

 # HSTS (ngx_http_headers_module is required) (15768000 seconds = 6 months)
 add_header Strict-Transport-Security "max-age=15768000; includeSubdomains";

 # OCSP Stapling ---
 # fetch OCSP records from URL in ssl_certificate and cache them
 ssl_stapling on;
 ssl_stapling_verify on;
 ssl_trusted_certificate /etc/ssl/certs/ssl-ca-certs-startssl.pem;

 include /etc/nginx/conf.d/*.conf;
}
```

Although it may be more common to put the SSL configuration in a server configuration block, I decided to move it to the main http block since this is a proxy only for two virtual hosts that share the same SSL certificate. Otherwise, the only potentially odd thing I've done is set the `user` to `www-data`. This was done as the host OS is Ubuntu, and the default user id generated by the base NGINX image shares the same id as `sshd`, which disturbed me. At least `www-data` is web related, and has no write permissions anywhere in the container, let alone the host system.

### vhost/default.conf

```
server { 
 listen 80 default_server;
 listen [::]:80 default_server;
 listen 443 default_server ssl;
 listen [::]:443 default_server ssl;

 server_name example.com;

 root /usr/share/nginx/html;
 index index.html;

 # force TLS
 if ($scheme = http) {
 return 301 https://$server_name$request_uri;
 }

 # try to get files from $root, otherwise redirect to Ghost
 location / {
 try_files $uri $uri/ =404;
 error_page 403 = @ghost;
 error_page 404 = @ghost;
 }

 # ghost blog
 location @ghost {
 rewrite ^ $scheme://www.example.com$uri redirect;
 }
}
```

I go into more detail for this and the other vhost configuration in the article [NGINX, Ghost, and host-linked images](https://www.timofejew.com/nginx-ghost-stopping-hotlink-images/).

### vhost/www.conf

```
server { 
 listen 80;
 listen [::]:80;
 listen 443 ssl;
 listen [::]:443 ssl;

 server_name www.example.com;

 root /usr/share/nginx/html;
 index index.html;

 # force TLS
 if ($scheme = http) {
 return 301 https://$server_name$request_uri;
 }

 # prevent hotlinking
 # test http://www.htaccesstools.com/test-hotlink-protection/
 location ~* \.(gif|png|jpe?g)$ {
 valid_referers none blocked www.example.com;
 if ($invalid_referer) {
 return 444;
 }
 try_files $uri @ghost;
 }

 # try to get files from $root, otherwise redirect to Ghost
 location / {
 try_files $uri @ghost;
 }

 # proxy to Ghost (assumes 'ghost' is in /etc/hosts)
 location @ghost {
 proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
 proxy_set_header Host $http_host;
 proxy_set_header X-Forwarded-Proto $scheme;
 proxy_pass http://ghost:2368;
 }
}
```

The main difference between this config and the one in the hot-linking article, is that `proxy_pass` parameter references a host named `ghost`. This is some magic supplied by the Docker `run` `--link` argument. What happens is that the container name of a linked container is put into `/etc/hosts` of the NGINX container. I'll describe this more in the next section.

## Deployment

To stand this up on `www.timofejew.com`, I ended up created two images. An image called `proxy` (built from the `Dockerfile` above), and another called `blog`, which is built from the following `Dockerfile`:

```
# Create Ghost container for www.example.com
#
# docker build -t blog .

FROM ptimof/ghost

MAINTAINER Peter Timofejew <peter@timofejew.com>

# Set timezone
RUN echo "America/Toronto" > /etc/timezone \ 
 && dpkg-reconfigure -f noninteractive tzdata

# install Postfix for forwarding
RUN apt-get update \ 
 && DEBIAN_FRONTEND=noninteractive apt-get install -y \
 postfix \
 libsasl2-modules \
 && apt-get autoremove -y \
 && apt-get clean

# Add in real values for example.com
ENV GHOST_URL https://www.example.com 
ENV MAIL_FROM '"Webmaster" <webmaster@www.example.com>' 
ENV MAIL_HOST localhost

# Set up Postfix
RUN sed -i -e "/^mydestination/s/^.*$/mydestination = /" /etc/postfix/main.cf \ 
 && sed -i -e "/^relayhost/s/^/#/" /etc/postfix/main.cf \
 && sed -i -e "/^#myorigin/s/^#//" /etc/postfix/main.cf \
 && sed -i -e "/^myhostname/s/^.*$/myhostname = www.example.com/" /etc/postfix/main.cf \
 && echo "www.example.com" > /etc/mailname \
 && echo "inet_protocols = ipv4" >> /etc/postfix/main.cf \
 && echo "relayhost = [mail.example.com]:587" >> /etc/postfix/main.cf \
 && echo "smtp_tls_security_level = verify" >> /etc/postfix/main.cf \
 && echo "smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt" >> /etc/postfix/main.cf \
 && echo "smtp_sasl_auth_enable = yes" >> /etc/postfix/main.cf \
 && echo "smtp_sasl_password_maps = hash:/etc/postfix/relay_password" >> /etc/postfix/main.cf \
 && echo "smtp_sasl_tls_security_options =" >> /etc/postfix/main.cf \
 && echo "[mail.example.com]:587 www-host@example.com:secret-password" > /etc/postfix/relay_password \
 && chmod 600 /etc/postfix/relay_password \
 && postmap /etc/postfix/relay_password

# Add startup shell
COPY ghost.sh /

# Run in production
ENTRYPOINT [ "/ghost.sh" ] 
CMD [ "npm", "start", "--production" ] 
```

You'll notice that's a bit different from the first article in this series, [Moving to Docker: Ghost](https://www.timofejew.com/moving-to-docker-ghost/). Mainly, I wanted to move the configuration from a file on the host, to burning it right into a Docker image, enable [authenticated email to my email server](https://www.timofejew.com/adventures-in-emailing/), and setting up the default command to run in production mode.

And the `ghost.sh` script is fairly simple:

```
#!/bin/bash

# start postfix
service postfix start

# start ghost
exec /entrypoint.sh $@ 
```

Once the two images were built, I ran them as follows:

```
docker run --name ghost -v /var/lib/ghost:/var/lib/ghost \ 
 --restart=always -m 150m --memory-swap 300m \
 --log-driver=json-file --log-opt max-size=1m \
 -h www.example.com \
 -d blog
docker run --name proxy --link ghost \ 
 -p 80:80 -p 443:443 --restart=always -m 75m \
 --log-driver=json-file --log-opt max-size=10m \
 -d proxy
```

This will create two _containers_, `ghost` and `proxy`

<sup>
  <a href="https://www.timofejew.com/moving-to-docker-nginx/#fn:1">1</a>
</sup>

, and pass the config information from `ghost` to `proxy` via the link.

What's neat about the `--link` command, is that it will push a bunch of environment variables that describe `ghost` into `proxy`, as well as creating a host entry for `ghost` into `/etc/hosts` in `proxy`. That's how the `proxy_pass` line of `/etc/nginx/conf.d/www.conf` knows what IP to forward requests to. Interestingly, the port is available to `proxy` via an environment variable, but since NGINX config files can't directly access environment variables, I've hardcoded it to `2368` (which is what the `ghost` container exposes).

And this is where Jason Wilder's scheme really shines. It will update the NGINX config to reflect the Ghost container's config exactly, no matter how it's launched. If I was running a more complex setup than a simple two container config, I'd certainly seriously consider using his technique.

## Final thoughts

What's really cool about using container linking is that the `ghost` container is only listening on it's own virtual interface - no port collisions if I decide to run another `ghost` container. And this virtual interface is automatically exported to the `proxy` container when it links it.

There are two downsides, though, to using this technique:

1. If the `ghost` container restarts, it may not have the same IP address. The `proxy` container will get an updated `/etc/hosts` entry, but NGINX won't be aware of it, and will need to be reloaded. This is one nice benefit of using Jason Wilder's technique: it will detect the change and kick NGINX. At the moment, I'm betting that the `ghost` container will not restart.
2. Container linking is not compatible with `--net=host`. There is a performance hit to the `proxy` container because it needs to go through a couple of network stacks (the host IP stack, bridging to the Docker network, and then the container's stack). If `timofejew.com` was a high-volume site on the Internet, I wouldn't use linking. But I'm not worried...

And finally, logs. I'm not running any log analysis, so the logs from both containers are just being rotated after they hit a size limit. This is good enough for debugging any problems, and will stop the logs from growing out of control.

## Next steps

Now that the services on `www.timofejew.com` are containerized, there's a couple things I'd like to try:

- Develop a backup/restore technique for dealing with Docker volumes. Once this is done, `www.timofejew.com` will be "stateless", in the broad sense of the term: I should be able to destroy and re-create the machine without data loss.
- Experiment with using [CoreOS](https://en.wikipedia.org/wiki/CoreOS) as the host operating system. If I'm moving to all services in containers, this seems to be the way to go. But I'll need to mess about with it for a bit first.
