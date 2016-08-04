# Introduction to Docker and Kubernetes

[Original URL](http://code.tutsplus.com/articles/introduction-to-docker-and-kubernetes--cms-25406)

> Large-scale distributed systems made of multiple cooperating services are becoming increasingly important. These systems run on clusters of hundreds, thousands or more servers. Developing, deploying...

Large-scale distributed systems made of multiple cooperating services are becoming increasingly important. These systems run on clusters of hundreds, thousands or more servers. Developing, deploying and maintaining these systems efficiently and economically is a tall order.

Virtualization and more recently containerization enable flexible sharing and management of resources. Docker made containerization popular. Check out this Envato Tuts+ article for a great introduction: [The Hitchhiker's Guide to Docker and Modulus](http://code.tutsplus.com/tutorials/the-hitchhikers-guide-to-docker-and-modulus--cms-24770).

Google has been running its immense software and data centers on containerization for years and accumulated a _lot_ of experience and knowhow. Kubernetes is an open-source project by Google that brings all that knowledge to the masses.

In this article, I'll explore Docker briefly and then dive deeply into Kubernetes. I'll use as a running example a Python 3 quote REST API service. Let's jump in.

## The Quote Service

The [quote service](https://github.com/the-gigi/quote-service) is a REST API that lets you add quotes and get a list of all quotes. It is implemented as a Python 3 web service using the excellent [hug](https://github.com/timothycrosley/hug) library. It exposes a single endpoint called /quotes. You can get all quotes or post a new quote. Let's add a few quotes:

```
curl http://localhost:8000/quotes -d "quote=TV is chewing gum for the eyes. ~ Frank Lloyd Wright"
curl http://localhost:8000/quotes -d "quote=It is better to die on your feet than live on your knees. ~ Emiliano Zapata"
curl http://localhost:8000/quotes -d "quote=We must be very careful when we give advice to younger people: sometimes they follow it! ~ Edsger W. Dijkstra"
```

If you browse to `http://localhost:8000/quotes` you'll get: `[ "TV is chewing gum for the eyes. ~ Frank Lloyd Wright", "It is better to die on your feet than live on your knees. ~ Emiliano Zapata", "We must be very careful when we give advice to younger people: sometimes they follow it! ~ Edsger W. Dijkstra" ]` If you just want to see HUG's auto-generated documentation, browse to: `http://localhost:8000`

```
{
 "404": "The API call you tried to make was not defined. Here's a definition of the API to help you get going :)",
 "documentation": {
 "/quotes": {
 "GET": {
 "examples": [
 "http://localhost:8000/quotes"
 ],
 "outputs": {
 "content_type": "application/json",
 "format": "JSON (Javascript Serialized Object Notation)"
 }
 },
 "POST": {
 "outputs": {
 "content_type": "application/json",
 "format": "JSON (Javascript Serialized Object Notation)"
 },
 "inputs": {
 "quote": {
 "type": "Basic text / string value"
 }
 }
 }
 }
 }
}
```

## Docker Basics

I will not explain too much about Docker and only apply it to the quote service.

### Dockerizing a Python App

First we need a [Dockerfile](http://docs.docker.com/engine/reference/builder/). It performs the following steps:

1. Based off the latest ubuntu image
2. Installs Python 3 and a few other dependencies
3. Copy the quote-service directory
4. Installs the quote-service dependencies from the requirement.txt file
5. Exposes the 8000 port
6. Launch the quote-service via hug

<!--  -->

```
FROM ubuntu:latest
MAINTAINER Gigi Sayfan "the.gigi@gmail.com"
RUN apt-get update -y
RUN apt-get install -y python3 python3-pip python3-dev build-essential
COPY . /quote-service
WORKDIR /quote-service
RUN pip3 install -r requirements.txt
EXPOSE 8000
ENTRYPOINT hug -f app.py
```

### Building an Image

The next step is to build a Docker image. This is as simple as:

`docker build .`

I also like to tag images:

`docker tag 715624b7e22a g1g1/quote-service`

`g1g1` is my user name on Docker Hub.

To verify the image was built successfully, type:

```
docker ps --all
```

You should see the new image:

```
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
715624b7e22a g1g1/quote-service "/bin/sh -c 'hug -f a" 4 hours ago Up 35 minutes 0.0.0.0:8000->8000/tcp agitated_northcutt
```

### Pushing an Image to Docker Hub

When you create an image, you can also push it to [Docker Hub](https://hub.docker.com/), so it can be used by other people (or yourself on a different machine).

`docker push g1g1/quote-service`

Note that you need to create an account on Docker Hub and log in locally using:

`docker login`

### Running a Dockerized App

Ok. Let's run the quote service image and expose port 8000 to the host.

`docker run -i -t -p 8000 g1g1/quote-service`

You should see: ```/#######################################################################\ `.----``..-------..``.----. :/:::::--:---------:--::::://. .+::::----##/-/oo+:-##----::::// `//::-------/oosoo-------::://. ## ## ## ## ##### .-:------./++o/o-.------::-` ``` ## ## ## ## ## `----.-./+o+:..----.`.:///. ######## ## ## ## `` `----.-::::::------ `.-:::://. ## ## ## ## ## #### ://::--.`` -:``...-----...` `:--::::::-.` ## ## ## ## ## ## :/:::::::::-:-````` .:::::-.`## ## #### ###### ``.--:::::::. .:::.` ``..::. .:: EMBRACE THE APIs OF THE FUTURE ::- .:- -::`::- VERSION 1.9.6`::- -::`-::-` -::- ######################################################################## Copyright (C) 2015 Timothy Edmund Crosley Under the MIT License

Serving on port 8000... ``` This is pretty cool. Before you try to access your awesome quote service on port 8000, you may need to perform some extra work depending on your environment.

If you run on Mac OS X using VirtualBox and [docker-machine](http://docs.docker.com/machine/get-started/), you may need to publish port 8000 on VirtualBox to make it accessible on the host.

Assuming you did try browsing to `http://localhost:8000/quotes`. Oh, no. Internal server error! What happened?

Let's look at the code:

```
redis_host = os.environ.get('QUOTE_STORE_SERVICE_HOST', 'localhost')
redis_server = redis.StrictRedis(host=redis_host, port=6379, db=0)
```

The quote service is trying to connect to a redis server. The host address could be configured as an environment variable, and if it's not set it will default to localhost. Unfortunately, redis is not installed by default and it's not running inside the container. Let's fix it temporarily. To get shell access into the container, type the following command:

`docker exec -it 715624b7e22a /bin/bash`

Then you'll get shell access to the container, and you can install redis:

`root@715624b7e22a:/quote-service# apt-get install redis-server`

Finally, run redis: ```root@715624b7e22a:/quote-service# redis-server [25] 29 Nov 00:14:24.546 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf _._ _.-``__ ''-._ _.-`` `. `_. ''-._ Redis 2.8.4 (00000000/0) 64 bit .-`` .-```. ```\/ _.,_ ''-._ ( ' , .-`|`, ) Running in stand alone mode |`-._`-...-`__...-.``-._|'` _.-'| Port: 6379 | `-._`._ / _.-' | PID: 25 `-._`-._ `-./ _.-' _.-' |`-._`-._`-.**.-' _.-'_.-'| | `-._`-._ _.-'_.-' | <http://redis.io> `-._`-._`-.__.-'_.-' _.-' |`-._`-._`-.**.-' _.-'_.-'| | `-._`-._ _.-'_.-' | `-._`-._`-.__.-'_.-' _.-'`-._ `-.__.-' _.-'`-._ _.-' `-.__.-'

[25] 29 Nov 00:14:24.547 # Server started, Redis version 2.8.4 [25] 29 Nov 00:14:24.547 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect. [25] 29 Nov 00:14:24.547 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii DB loaded from disk: 0.000 seconds [25] 29 Nov 00:14:24.547 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii The server is now ready to accept connections on port 6379 ``` Now, you can start adding quotes and getting quotes through the `http://localhost:8000/quotes` endpoint.

Placing the application (quote service) and the database (redis) works in a pinch for one server. But, obviously that doesn't scale. Enter Kubernetes.

## Kubernetes Basics

Kubernetes, a.k.a. **k8s**, is an opinionated framework for managing and orchestrating multiple containers. It has its own way of doing things, which is usually very good. It is still under development, so there are still a few rough edges here and there. Kubernetes has a lot of concepts and is very flexible. I'll explain and demonstrate the concepts by applying them to the quote service.

### Setting Up a Cluster

There are many ways to set up a Kubernetes cluster. Kubernetes can run on bare metal, on Google Container engine, on AWS, on bare metal (with Linux), and locally on any OS using virtual machines. For the purpose of this article, I created a cluster of one master and two minions using the [CoreOS OSX GUI k8s Cluster](https://github.com/rimusz/coreos-osx-gui-kubernetes-cluster). On Mac OS X it provides a nice little menu where you can access many cluster management tools.

Follow the instructions and you'll be good to go in no time.

If you're not using Mac OSX or just don't care much for GUI, you can use [this project](https://github.com/pires/kubernetes-vagrant-coreos-cluster) to set up a test cluster in a Vagrant VM.

I will use the command line from now on.

### The kubectl Command-Line Tool

kubectl is the Swiss Army knife of Kubernetes. You can fully control and manage your cluster from the comfort of your console using nothing but kubectl.

Here is the list of commands: `get Display one or many resources describe Show details of a specific resource create Create a resource by filename or stdin update Update a resource by filename or stdin. delete Delete a resource by filename, stdin, resource and ID, or by resources and label selector. namespace SUPERCEDED: Set and view the current Kubernetes namespace log Print the logs for a container in a pod. rolling-update Perform a rolling update of the given ReplicationController. resize Set a new size for a Replication Controller. exec Execute a command in a container. port-forward Forward one or more local ports to a pod. proxy Run a proxy to the Kubernetes API server run-container Run a particular image on the cluster. stop Gracefully shut down a resource by id or filename. expose Take a replicated application and expose it as Kubernetes Service label Update the labels on a resource config config modifies kubeconfig files cluster-info Display cluster info api-versions Print available API versions. version Print the client and server version information. help Help about any command`

Feel free to use the help command or the documentation to investigate what each does. Many of them are used for performing manual operations that are better done using configuration files in a large-scale, scalable, distributed system, but it is indispensable for quick exploration and troubleshooting. The most common commands I'll use a lot are: get, create, delete and start.

### Pods

A pod is the basic unit of deployment and management in Kubernetes. A pod is a group of one or more containers. You can specify a pod using a dedicated YAML file or as part of a Kubernetes service (see below). A pod is always deployed on a single host, and all the containers in a pod can access each other through localhost. All the pod's containers are always started, stopped and scaled together.

To check out all the pods in the cluster, type:

`kubectl get pods`

The result will be something like: `NAME READY STATUS RESTARTS AGE quote-frontend-4kyns 1/1 Running 0 1h quote-frontend-v4xk1 1/1 Running 0 1h quote-store-controller-y4ya1 1/1 Running 0 23h` ### Volumes

Containers are not supposed to maintain persistent state. When a container crashes or restarts, its local file system is wiped out. If you want to keep persistent state, you should use persistent volumes. Since everything in Kubernetes is based on pods, you need to define volumes in a pod as well. Here is a pod definition file with a persistent volume: `apiVersion: v1 kind: Pod metadata: name: quote-store labels: app: quote-api role: persistent-storage spec: containers: - name: redis image: redis volumeMounts: - name: quote-store-volume mountPath: /data/redis volumes: - name: quote-store-volume emptyDir: {}` Note that persistent volumes are limited to the node and will survive container crashes and restarts, but _not_ node/host failures. You still need to do the work of replicating and backing up important data.

### Replication Controllers

One of the most important features of Kubernetes is its ability to manage and easily scale up and down the number of pods. Typically, you'll have different types of pods in your system, and you'll want to be able to specify how many pods of each type should be up and running.

Say hello to replication controllers. A replication controller has a pod template that defines a group of containers, a set of labels to identify these pods, and the number of desired pods. The replication controller makes sure that the number of running pods identified by its labels always matches the desired number. If a pod terminates, the replication controller will immediately create a new one.

There are several interesting use cases supported by replication controllers, like high-availability, elastic scaling and rolling updates. For example, you can add and remove pods from the dominion of a replication controller by changing their label.

Replication controllers are specified in a YAML file, of course. Here is an example: ``` apiVersion: v1 kind: ReplicationController

metadata: name: quote-frontend spec: replicas: 2 # selector identifies the set of Pods that this # replication controller is responsible for managing selector: app: quote-api role: frontend # podTemplate defines the 'cookie cutter' used for creating # new pods when necessary template: metadata: labels: # Important: these labels need to match the selector above # The api server enforces this constraint. app: quote-api role: frontend spec: containers: - name: quote-service image: g1g1/quote-service env: - name: GET_HOSTS_FROM # value: dns value: env ports: - containerPort: 8000 ``` The template's spec for the quote-service container uses the [g1g1/quote-service](https://hub.docker.com/r/g1g1/quote-service/) image I pushed earlier to Docker Hub. Note the `env` section where information sharing across pods can occur through either DNS or environment variables.

To create a replication controller, type:

`kubectl create -f <replication controller filename>`

To view the current replication controllers in the cluster, type:

`kubectl get rc`

You should see something like: `CONTROLLER CONTAINER(S) IMAGE(S) SELECTOR REPLICAS AGE quote-frontend quote-service g1g1/quote-service app=quote-api,role=frontend 2 1h quote-store-controller master redis app=quote-api,role=persistent-storage 1 1d` ### Services

A service exposes its pod to the rest of the cluster and possibly externally via either environment variables or DNS. For example, the quote service is made of two types of pods: a redis store pod and a front-end pod. The front-end container should be able to find the store container, and clients should be able to hit a single public endpoint to access the service.

The Kubernetes service is implemented in yet another YAML file. Each component of your system that needs to be accessed by other components should have its own service file. Here are the two service files for the quote service components:

#### srv-quote-frontend.yaml

```
apiVersion: v1
kind: Service
metadata:
 name: quote-frontend
spec:
 type: NodePort
 ports:
 - port: 8000 # the port that this service should serve on
 # the container on each pod to connect to, can be a name
 # (e.g. 'www') or a number (e.g. 80)
 targetPort: 80
 protocol: TCP
 # just like the selector in the replication controller,
 # but this time it identifies the set of pods to load balance
 # traffic to.
 selector:
 app: quote-api
 role: frontend
```

#### srv-quote-store.yaml `apiVersion: v1 kind: Service metadata: name: quote-store spec: ports: - port: 6379 # the port that this service should serve on targetPort: 6379 # just like the selector in the replication controller, # but this time it identifies the set of pods to load balance # traffic to. selector: app: quote-api role: persistent-storage` The host and port of each service are made available to every container in the cluster. For example, if you run an interactive shell on one of the frontend containers:

`kubectl quote-frontend-4kyns exec -i -t bash`

Then you can verify that environment contains the necessary host and port information to connect to the quote store. `root@quote-frontend-4kyns:/quote-service# env | grep STORE QUOTE_STORE_PORT_6379_TCP_ADDR=10.100.234.192 QUOTE_STORE_PORT_6379_TCP_PROTO=tcp QUOTE_STORE_SERVICE_PORT=6379 QUOTE_STORE_PORT_6379_TCP_PORT=6379 QUOTE_STORE_PORT=tcp://10.100.234.192:6379 QUOTE_STORE_PORT_6379_TCP=tcp://10.100.234.192:6379 QUOTE_STORE_SERVICE_HOST=10.100.234.192` Just, to refresh your memory, this is exactly what the frontend does: `redis_host = os.environ.get('QUOTE_STORE_SERVICE_HOST', 'localhost') redis_server = redis.StrictRedis(host=redis_host, port=6379, db=0)` ## Conclusion

Docker and Kubernetes are exciting technologies. This article barely scratched the surface of what's possible. The benefits are enormous, but the infrastructure, tooling and best practices are still evolving. If you are even remotely connected to large-scale distributed systems, I encourage you to at least stay on top of these technologies and ideally dip your toes in and actually try to use them. There are many ways to experiment and learn without wholesale migration of your entire production infrastructure.

As far as Kubernetes goes, there are a few other alternatives for multi-container management and orchestration, such as the incumbent Mesos and Docker's own compose. I believe Kubernetes is more architecturally sound, has a lot of momentum, and is better than the alternatives.
