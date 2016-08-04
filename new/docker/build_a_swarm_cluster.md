# Build a Swarm Cluster

[Original URL](https://opsnotice.xyz/build-swarm-cluster/)

> The version 1.12 of Docker has been released few days ago. Among the changes, Docker-Swarm get embedded directly into the Engine that allow easier Swarm deployment. I'm going to show that to...

![](https://opsnotice.xyz/content/images/2016/07/swarm-cluster.png)

The version 1.12 of Docker has been released few days ago. Among the changes, Docker-Swarm get embedded directly into the Engine that allow easier Swarm deployment. I'm going to show that to you.

> Docker Swarm is native clustering for Docker. It turns a pool of Docker hosts into a single, virtual Docker host.

Earlier stages, it was quite hard to deploy a Swarm cluster, you had to generate some certs, use a service discovery, configure each node... This time is over !<br>
Today, we are going to make a Swarm cluster in two commands !

In this post, I'll show you how to make a small cluster between two virtual machines but you can easily repeat operations to make a larger cluster.

I'll use two Ubuntu 16.04 Xenial VM and install Docker 1.12 on each.

```
export DEBIAN_FRONTEND=noninteractive 
sudo apt-get -y update 
sudo apt-get install apt-transport-https ca-certificates 
sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D 
sudo echo "deb https://apt.dockerproject.org/repo ubuntu-xenial experimental" > /etc/apt/sources.list.d/docker.list 
sudo apt-get -y update 
sudo apt-get purge lxc-docker 
sudo apt-cache policy docker-engine 
sudo apt-get -y install linux-image-extra-$(uname -r) 
sudo apt-get -y install apparmor 
sudo apt-get -y install docker-engine 
sudo service docker start 
docker -v 
```

When your VMs have Docker 1.12 installed on it, we are going to create our Swarm cluster. Launch this command on the VM you want use for Master :

```
docker swarm init 
```

This command will init the cluster and give you, at end, the command to launch on your nodes to join us to the cluster. Launch it on other nodes :

```
docker swarm join --secret <secret> \ 
--ca-hash sha256:<hash> \
IP_Master_Swarm:2377 
```

With this second command, your node joins directly your Swarm cluster (in two commands, no jokes). You have definitely a Docker Swarm Cluster ! Docker Swarm is too easy since 1.12

For going a bit deeper in Swarm, we'll deploy a service and expose it on our nodes (port 8001/tcp) who will be load-balanced in the cluster. In our example, we're going to use Nginx, but you can use any other image of your choice.

We launch these commands on the Master :

```
docker network create -d overlay nginx-network 
docker service create --name nginx --network nginx-network --replicas 5 -p 8001:80/tcp nginx 
```

![](https://opsnotice.xyz/content/images/2016/07/nginx-swarm-task.png)

With this command, we have created a private network for Nginx and a service (new Docker concept) who expose the port 8001 off our Swarm's nodes and load-balance it to 5 Nginx containers.

After, we can easily scale up our service :

```
docker service scale nginx=40 
```

![](https://opsnotice.xyz/content/images/2016/07/nginx-scale-40.png)

If you want to read more about the new Swarm, [let's RTFM](https://docs.docker.com/engine/swarm/).

For me, this update makes really easier the deployment of a Swarm Cluster from scratch. This is a good point for Docker Inc in the big war or container orchestration (Kubernetes, Mesos, Rancher...).
