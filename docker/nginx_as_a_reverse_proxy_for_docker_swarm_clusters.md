
[Original URL](https://www.systemcodegeeks.com/web-servers/nginx/nginx-reverse-proxy-docker-swarm-clusters/)

> Spawning services across multiple Docker engines is a very cool thing, but those services need to connect each other and be found by public-facing nodes in order to be routed to users. A way to...

Spawning services across multiple Docker engines is a very cool thing, but those services need to connect each other and be found by public-facing nodes in order to be routed to users. A way to achieve that is to use NGINX as a reverse proxy by defining one or more public-facing nodes. These nodes are going to have NGINX configured to proxy request to each container exposing your service.

In this post, we are going to see how to use NGINX as a reverse proxy for load-balancing containerized HTTP applications running in a Swarm cluster. We'll also look at how to automate the service discovery (_a.k.a._, auto-add new containers running the same service) to the NGINX configuration using [ehazlett/interlock](https://github.com/ehazlett/interlock).

## Prerequisites

To follow this post and execute all the examples, you will need the following:

- **Docker Machine >= 0.7.0**: to provision Docker engines. You can obtain Docker Machine [here](https://github.com/docker/machine/releases).
- **Docker Compose >= 1.7.1**: to orchestrate the application's services.
- **Docker client >= 1.11.1**: for talking with the Swarm manager. You can obtain the Docker client [here](https://github.com/docker/docker/releases/latest).
- **Digital Ocean API Token**: to allow docker-machine to create machines on which to provision its engines. You can generate your token [here](https://cloud.digitalocean.com/settings/api/tokens/new).

### Creating the Swarm cluster

To begin, we need a Swarm cluster with these characteristics:

- At least one public-facing node to host the NGINX proxy. Multiple nodes can be created and balanced using DNS.
- At least one node to host the Swarm manager. Swarm has built-in HA, which is not needed for this tutorial. Implementing it is left as an exercise for the reader; see [here](https://docs.docker.com/swarm/multi-manager-setup/).
- At least one node to host the key/value datastore. Here I used Consul. As for the Swarm manager, you might want to have at least three nodes for this in production. This kind of node is not in the Swarm cluster.

To make this step easier, I created a script that creates all the required nodes on Digital Ocean.

```
#!/bin/bash

# nodes to create configuration
public_nodes="public01"
nodes="node01 node02 node03"

# Create the KV node using consul
docker-machine create \
 -d digitalocean \
 --digitalocean-access-token=$DO_ACCESS_TOKEN \
 consul

docker-machine ssh consul docker run -d \
 -p "8500:8500" \
 -h "consul" \
 progrium/consul -server -bootstrap

KV_IP=$(docker-machine ip consul)
KV_ADDR="consul://${KV_IP}:8500"

# Create the Swarm Manager
echo "Create swarm manager"
docker-machine create \
 -d digitalocean \
 --digitalocean-access-token=$DO_ACCESS_TOKEN \
 --swarm --swarm-master \
 --swarm-discovery=$KV_ADDR \
 --engine-opt="cluster-store=${KV_ADDR}" \
 --engine-opt="cluster-advertise=eth0:2376" \
 manager

# Create Public facing Swarm nodes
for node in $public_nodes; do
 (
 echo "Creating ${node}"

 docker-machine create \
 -d digitalocean \
 --digitalocean-size "4gb" \
 --engine-label public=yes \
 --digitalocean-access-token=$DO_ACCESS_TOKEN \
 --swarm \
 --swarm-discovery=$KV_ADDR \
 --engine-opt="cluster-store=${KV_ADDR}" \
 --engine-opt="cluster-advertise=eth0:2376" \
 $node
 ) &
done
wait

# Create other Swarm nodes
for node in $nodes; do
 (
 echo "Creating ${node}"

 docker-machine create \
 -d digitalocean \
 --digitalocean-size "2gb" \
 --engine-label public=no \
 --digitalocean-access-token=$DO_ACCESS_TOKEN \
 --swarm \
 --swarm-discovery=$KV_ADDR \
 --engine-opt="cluster-store=${KV_ADDR}" \
 --engine-opt="cluster-advertise=eth0:2376" \
 $node
 ) &
done
wait

# Print Cluster Information
echo ""
echo "CLUSTER INFORMATION"
echo "Consul UI: http://${KV_IP}:8500"
echo "Environment variables to connect trough docker cli"
docker-machine env --swarm manager
```

Just copy the script to a file named `create-swarm-cluster.sh` and give execution permissions with `chmod +x create-swarm-cluster.sh`. To execute the script, you will need to give it the previously generated Digital Ocean API token.

```
export DO_ACCESS_TOKEN=<your-digitalocean-api-token>
./create-swarm-cluster.sh
```

This is how our cluster looks:

[![cluster](https://www.systemcodegeeks.com/wp-content/uploads/2016/06/cluster-1024x588.png)](http://www.systemcodegeeks.com/wp-content/uploads/2016/06/cluster.png)

Now that we have our cluster, we have to export the right environment variables to connect to it using our local Docker client. To do that:

```
eval $(docker-machine env --swarm manager)
```

To verify that you are connected to the Swarm cluster:

```
docker info | grep "Server Version:"
```

That should output something like:

```
Server Version: swarm/1.2.3
```

## Running Your Application into the Cluster

Now that our Swarm cluster is ready, we just need to start our application. For this purpose, I chose the super cool [Cats vs Dogs Voting Demo Application](https://github.com/docker/example-voting-app).

The application consists of three parts:

- The Voting Application, where you actually choose between cats and dogs
- The Result Application
- The worker, in charge of persisting votes in the Postgres database

For dependencies, it has Postgres and Redis databases.

In order to run our application, we're going to need a `docker-compose.yml` file that will start an instance of each microservice and the dependencies.

```
version: "2"

services:
 voting-app:
 image: docker/example-voting-app-voting-app:latest
 ports:
 - "80"
 links:
 - redis
 networks:
 - front-tier
 - back-tier

 result-app:
 image: docker/example-voting-app-result-app:latest
 ports:
 - "80"
 links:
 - db
 networks:
 - front-tier
 - back-tier

 worker:
 image: docker/example-voting-app-worker:latest
 networks:
 - back-tier

 redis:
 image: redis:alpine
 ports: ["6379"]
 networks:
 - back-tier

 db:
 image: postgres:9.5
 volumes:
 - "db-data:/var/lib/postgresql/data"
 networks:
 - back-tier

volumes:
 db-data:

networks:
 front-tier:
 back-tier:
```

If you start it, you should obtain something like this:

```
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
266c4ec6c51d docker/example-voting-app-result-app:latest "node server.js" 14 seconds ago Up 10 seconds 104.236.XXX.XXX:32768->80/tcp node01/nginxreverseproxy_result-app_1
8cefe6ad38b9 docker/example-voting-app-voting-app:latest "python app.py" 14 seconds ago Up 11 seconds 104.236.XXX.XXX:32769->80/tcp node02/nginxreverseproxy_voting-app_1
e04fa43c9909 redis:alpine "docker-entrypoint.sh" 17 seconds ago Up 14 seconds 104.236.XXX.XXX:32768->6379/tcp node02/nginxreverseproxy_redis_1
8e802ff973aa postgres:9.5 "/docker-entrypoint.s" 17 seconds ago Up 14 seconds 5432/tcp node01/nginxreverseproxy_db_1
e434f6a9ff9c docker/example-voting-app-worker:latest "java -jar target/wor" 17 seconds ago Up 15 seconds public01/nginxreverseproxy_worker_1
```

But hang on. We're not going to start `docker-compose.yml` because it's not suitable for a cluster. In fact:

- We don't have an entry point or a defined set of entry points which point to our DNS. If you look at the above output, you should note that things were scheduled without any specific constraint.
- Our Postgres database has a mounted volume bound to the node on which the container is running. But what happens if the container is rescheduled on another node?
- Front-end applications should be on the 80 or 443 ports, not on casual ones.
- If you scale web applications with `docker-compose scale`, those are not load balanced.

!New Call-to-action

So, one thing at a time: To solve the problem of the single entry point for our DNS servers, we are going to need an automated way to register our services into a proxy. In our case, we'll use NGINX and [ehazlett/interlock](https://github.com/ehazlett/interlock) for this purpose.

With Interlock, each time that a service is added or scaled, it will be added into the pool of the NGINX `proxy_pass` so that NGINX can route and balance request to the right containers. In this way, the single entry point for the HTTP requests made to the cluster will be the NGINX container that's bound to the `public01` node using the constraint: `constraint:node==public01`. This also solves the problem that, when scaling containers using `docker-compose scale`, requests across containers are balanced. You can always start more public nodes and balance them using DNS balancing.

For the Postgres database, the only thing we can do here is to bind it to a specific node so that we can be sure that it is not rescheduled on another machine.

Obviously this will increase the chances of failure -- it's creating a single point of failure on the cluster. However, there's a way to run stateful services like databases in production by allowing your volumes to follow your containers. It's called Flocker, and unfortunately it was not suitable for this post, but you can learn more [here](https://clusterhq.com/flocker/introduction/).

To set up this interlock, you will need this `docker-compose.yml`:

```
interlock:
 image: ehazlett/interlock:master
 command: -D run -c /etc/interlock/config.toml
 tty: true
 ports:
 - 8080
 environment:
 INTERLOCK_CONFIG: |
 ListenAddr = ":8080"
 DockerURL = "${SWARM_HOST}"
 TLSCACert = "/etc/docker/ca.pem"
 TLSCert = "/etc/docker/server.pem"
 TLSKey = "/etc/docker/server-key.pem"
 [[Extensions]]
 Name = "nginx"
 ConfigPath = "/etc/nginx/nginx.conf"
 PidPath = "/var/run/nginx.pid"
 TemplatePath = ""
 MaxConn = 1024
 Port = 80
 volumes:
 - /etc/docker:/etc/docker:ro

nginx:
 image: nginx:latest
 entrypoint: nginx
 command: -g "daemon off;" -c /etc/nginx/nginx.conf
 ports:
 - 80:80
 labels:
 - "interlock.ext.name=nginx"
 environment:
 - "constraint:node==public01
```

As you can see, we're starting an interlock container that can connect to the Swarm cluster and updates the `/etc/nginx/nginx.conf` each time it's needed. Note that the NGINX container is bound to the `public01` node, so all our HTTP services will be accessible trough that node.

To start this on your cluster:

```
eval $(docker-machine env --swarm manager)
export SWARM_HOST=tcp://$(docker-machine ip manager):2376
docker-compose up -d
```

Now that our cluster is ready, we can change our application's `docker-compose.yml` to reflect our thoughts:

```
version: "2"

services:
 voting-app:
 hostname: voting.local
 image: docker/example-voting-app-voting-app:latest
 ports:
 - "80"
 links:
 - redis
 networks:
 - front-tier
 - back-tier
 labels:
 - "interlock.hostname=voting"
 - "interlock.domain=local"

 result-app:
 hostname: result.local
 image: docker/example-voting-app-result-app:latest
 ports:
 - "80"
 links:
 - db
 networks:
 - front-tier
 - back-tier
 labels:
 - "interlock.hostname=result"
 - "interlock.domain=local"

 worker:
 image: docker/example-voting-app-worker:latest
 networks:
 - back-tier

 redis:
 image: redis:alpine
 ports: ["6379"]
 networks:
 - back-tier

 db:
 image: postgres:9.5
 volumes:
 - "db-data:/var/lib/postgresql/data"
 networks:
 - back-tier
 environment:
 - "constraint:node==node01"

volumes:
 db-data:

networks:
 front-tier:
 back-tier:
```

A few things are different now:

- The voting and result apps now have the `hostname` and the interlock's hostname and domain labels that are used by interlock to configure NGINX.
- The Postgres database now is bound to the `node01`.

The last thing to do is to add the IP address of the `public01` node to your DNS records. For simplicity, you can add it to your local host's file. You can obtain the right host's line with this command:

```
echo $(docker-machine ip public01) voting.local result.local
```

Now you can just run the applications with a `docker-compose up` and point your browser to <http://voting.local> to choose your favorite pet! (Pro tip: Cats are the right choice.)

## Conclusion

In this post, we achieved a few things in a relatively simple way by using just the official tools provided by Docker. The coolest achievement was that our entire cluster is now exposed as a single Docker daemon by the Swarm manager which also matches the definition of cluster you can find [on Wikipedia](https://en.wikipedia.org/wiki/Computer_cluster):

> A computer cluster consists of a set of loosely or tightly connected computers that work together so that, in many respects, they can be viewed as a single system.
