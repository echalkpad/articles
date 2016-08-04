# Deploying a Simple and Secure Docker Registry

[Original URL](https://deis.com/blog/2016/deploying-a-secure-docker-registry/)

> There comes a time in everybody's life where they realize they have to run their own Docker Registry. Unfortunately there's not a lot of good information on how to run one. Docker's documentation is...

There comes a time in everybody's life where they realize they have to run their own [Docker Registry](https://www.docker.com/docker-registry). Unfortunately there's not a lot of good information on how to run one. Docker's documentation is pretty good, but is verbose and spread across a lot of different pages. This means having half a dozen tabs open and searching for the right information.

It's common to run the [Docker Registry](https://www.docker.com/docker-registry) with little to no security settings, and fronting it with NGINX or Apache to provide this security. But there is another way.

In this post, I will show how to run the Docker Registry securely by itself with both TLS certificate backed encryption and certificate based endpoint authorization.

If you need to do advanced stuff like authenticate against LDAP, you'll still want to go down the reverse proxy road.

For simplicity, I will will assume a single registry running on the local filesystem and will avoid using OS specific init systems by focusing just on the `docker` commands themselves. This should work on any system capable of running [Docker](http://docker.com).

## Preparation

To begin, boot a server that has [Docker](http://docker.com) installed.

For an OS with Docker already installed, I recommend [CoreOS](https://deis.com/blog/2015/coreos-on-virtualbox/). However you could just as easily boot Ubuntu or CentOS and run `curl -sSL get.docker.com | sudo bash` if you're into that sort of thing.

SSH into the server and ensure Docker is working by running:

```go
$ docker info
Containers: 0
Images: 0
Server Version: 1.9.1
Storage Driver: overlay
 Backing Filesystem: extfs
Execution Driver: native-0.2
Logging Driver: json-file
Kernel Version: 4.3.3-coreos
Operating System: CoreOS 899.1.0
CPUs: 1
Total Memory: 997.4 MiB
Name: core-01
ID: C5XV:CZ3H:EAO4:ATJ3:ARSO:UOGD:XH3X:UKLZ:V3FO:2LRF:6E3X:CV5K
```

## Create Certificates

To keep this as simple as possible, I will demonstrate using the [paulczar/omgwtfssl](https://github.com/paulczar/omgwtfssl) image to create certificates. If you would rather create them manually with `openssl`, see my blog post on [Securing Docker with TLS](https://deis.com/blog/2016/secure-docker-with-tls/).

Create a place on the filesystem to store the data for the registry as well as certificates and config data:

```go
$ sudo mkdir -p /opt/registry/{data,ssl,config}
```

Now we can create the certificates:

```go
$ docker run --rm \
 -v /opt/registry/ssl:/certs \
 -e SSL_IP=172.17.8.101 \
 -e SSL_DNS=registry.local \
 paulczar/omgwtfssl
----------------------------
| OMGWTFSSL Cert Generator |
----------------------------

--> Certificate Authority
====> Generating new CA key ca-key.pem
Generating RSA private key, 2048 bit long modulus
................+++
.................................+++
e is 65537 (0x10001)
====> Generating new CA Certificate ca.pem
====> Generating new config file openssl.cnf
====> Generating new SSL KEY key.pem
Generating RSA private key, 2048 bit long modulus
..........................................................+++
.............................................+++
e is 65537 (0x10001)
====> Generating new SSL CSR key.csr
====> Generating new SSL CERT cert.pem
Signature ok
subject=/CN=example.com
Getting CA Private Key

core@core-01 ~ $ ls /opt/registry/ssl/
ca-key.pem ca.pem ca.srl cert.pem key.csr key.pem openssl.cnf
```

Be sure to add any IP and DNS addresses that you might access your registry from, including that of any load balancer or floating IP you might have. You can do this by setting the `SSL_IP` and `SSL_DNS` as seen in the example above. Feel free to set additional ones with more `-e` arguments.

The next step is to create the `/opt/registry/config/registry.env` file which will contain a list of environment variables to be passed into the container.

```go
# location of registry data
REGISTRY_STORAGE_FILESYSTEM_ROOTDIRECTORY=/opt/registry/data

# location of TLK key/cert
REGISTRY_HTTP_TLS_KEY=/opt/registry/ssl/key.pem
REGISTRY_HTTP_TLS_CERTIFICATE=/opt/registry/ssl/cert.pem

# location of CA of trusted clients
REGISTRY_HTTP_TLS_CLIENTCAS_0=/opt/registry/ssl/ca.pem
```

_For this example I'm using the same CA certificate for clients as I did for the server. For production setups, it should probably be a different CA._

All that is left to do now is start the registry container, bind mount in the `/opt/registry` directory, pass in the config file, and expose port `443` to the internal registry port.

Do that like so:

```go
$ docker run -d --name registry \
 -v /opt/registry:/opt/registry \
 -p 443:5000 --restart always \
 --env-file /opt/registry/config/registry.env \
 registry:2
Unable to find image 'registry:2' locally
2: Pulling from library/registry
Digest: sha256:a842b52833778977f7b4466b90cc829e0f9aae725aebe3e32a5a6c407acd2a03
Status: Downloaded newer image for registry:2
d0106555b2d0aa30691c75c50b279e6a8bd485aa4ba2f203773e971988253169
```

We can check we can access the registry container from the server itself by tagging and pushing the `alpine` image to it, like so:

```go
$ docker pull alpine
Using default tag: latest
latest: Pulling from library/alpine
Digest: sha256:78a756d480bcbc35db6dcc05b08228a39b32c2b2c7e02336a2dcaa196547a41d
Status: Downloaded newer image for alpine:latest
$ docker tag alpine 127.0.0.1/alpine
$ docker push 127.0.0.1/alpine
The push refers to a repository [127.0.0.1/alpine] (len: 1)
74e49af2062e: Pushed
latest: digest: sha256:a96155be113bb2b4b82ebbc11cf1b511726c5b41617a70e0772f8180afc72fa5 size: 1369
```

To check the security settings worked, to access the registry from a remote host:

```go
$ docker pull 172.17.8.101/alpine
Using default tag: latest
Error response from daemon: unable to ping registry endpoint https://172.17.8.101/v0/
v2 ping attempt failed with error: Get https://172.17.8.101/v2/: x509: certificate signed by unknown authority
 v1 ping attempt failed with error: Get https://172.17.8.101/v1/_ping: x509: certificate signed by unknown authority
```

_Anywhere you see `172.17.8.101` you will want to replace it with the IP or hostname of your Docker registry._

On the server we can see this failure in the logs:

```go
$ docker logs registry
2016/01/10 16:18:47 http: TLS handshake error from 172.17.8.1:44096: remote error: bad certificate
2016/01/10 16:18:47 http: TLS handshake error from 172.17.8.1:44098: remote error: bad certificate
2016/01/10 16:18:47 http: TLS handshake error from 172.17.8.1:44099: remote error: bad certificate
```

There are two things causing this failure.

The first is that the remote server does not trust the client because it cannot provide the trusted CA certificate as specified in `REGISTRY_HTTP_TLS_CLIENTCAS_0` setting.

The second reason for failure is that the client doesn't trust the sever CA.

If we didn't have `REGISTRY_HTTP_TLS_CLIENTCAS_0` set we could simply add `--insecure-registry 172.17.8.101` to `DOCKER_OPTS` in `/etc/default/docker`. However, since we do have this set, we'll need to take the `CA.pem` file and save it as `/etc/docker/certs.d/172.17.8.101/ca.crt` on the remote machine that should trust the registry server.

I did this like so:

```go
$ sudo mkdir -p /etc/docker/certs.d/172.17.8.101
$ sudo scp core@172.17.8.101:/opt/docker/registry/ca.pem \
 /etc/docker/certs.d/172.17.8.101/ca.crt
```

You may need to do it differently based on how your server is set up for access.

Now we have established trust in both directions, we can try again:

```go
$ docker pull 172.17.8.101/alpine
Using default tag: latest
latest: Pulling from alpine

340b2f9a2643: Already exists
Digest: sha256:a96155be113bb2b4b82ebbc11cf1b511726c5b41617a70e0772f8180afc72fa5
Status: Downloaded newer image for 172.17.8.101/alpine:latest
```

Success! Everything is working.

## Wrap Up

We know have a [Docker Registry](https://www.docker.com/docker-registry) that is secured with both encryption and authorization based on each client having a specific CA certificate.

This setup is ideal for providing secure access to a private registry for remote servers.

If you want to do this in a more automated fashion you can look at configuration management communities like [Chef](https://supermarket.chef.io/cookbooks/docker_registry) for examples.

_This post originally appeared on [Paul Czarkowski's blog](http://tech.paulcz.net/2016/01/deploying-a-secure-docker-registry/)._

<span class="triangle">
  <img src="https://deis.com/images/svg/triangle.svg" alt="triangle">
</span>

 

<span class="square">
  <img src="https://deis.com/images/svg/square.svg" alt="square">
</span>

 

<span class="circle">
  <img src="https://deis.com/images/svg/circle.svg" alt="circle">
</span>

## Did you enjoy this post?
