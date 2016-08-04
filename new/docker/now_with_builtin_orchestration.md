# Now with Built-in Orchestration!

[Original URL](https://blog.docker.com/2016/06/docker-1-12-built-in-orchestration/)

> Three years ago, Docker made an esoteric Linux kernel technology called containerization simple and accessible to everyone. Today, we are doing the same for container orchestration....

Three years ago, Docker made an esoteric Linux kernel technology called containerization simple and accessible to everyone. Today, we are doing the same for container orchestration.

Container orchestration is what is needed to transition from deploying containers individually on a single host, to deploying complex multi-container apps on many machines. It requires a distributed platform, independent from infrastructure, that stays online through the entire lifetime of your application, surviving hardware failure and software updates. Orchestration is at the same stage today as containerization was 3 years ago. There are two options: either you need an army of technology experts to cobble together a complex ad hoc system, or you have to rely on a company with a lot of experts to take care of everything for you as long as you buy all hardware, services, support, software from them. There is a word for that, it's called lock-in.

Docker users have been sharing with us that neither option is acceptable. Instead, you need a platform that makes orchestration usable by everyone, without locking you in. Container orchestration would be easier to implement, more portable, secure, resilient, and faster if it was built into the platform.

Starting with Docker 1.12, we have added features to the core Docker Engine to make multi-host and multi-container orchestration easy. We've added new API objects, like Service and Node, that will let you use the Docker API to deploy and manage apps on a group of Docker Engines called a swarm. With Docker 1.12, the best way to orchestrate Docker is Docker!

[![](http://img.scoop.it/q5ZcSVcr06OuoljzvFbsvrnTzqrqzN7Y9aBZTaXoQ8Q=)](https://www.docker.com/getdocker) The Docker 1.12 design is based on four principles:

## Simple Yet Powerful

* Orchestration is a central part of modern distributed applications; it's so central that we have seamlessly built it into our core Docker Engine. Our approach to orchestration follows our philosophy about containers: no setup, only a small number of simple concepts to learn, and an "it just works" user experience.

* Resilient – Machines fail all the time. Modern systems should expect these failures to occur regularly and adapt without any application downtime that's why a zero single-point-of-failure design is a must.

* Secure – Security should be the default. Barriers to strong security -- certificate generation, having to understand PKI -- should be removed. But advanced users should still be able to control and audit every aspect of certificate signing and issuance.

* Optional Features and Backward Compatibility – With millions of users, preserving backwards compatibility is a must for Docker Engine. All new features are optional, and you don't incur any overhead (memory, cpu) if you don't use them. Orchestration in Docker Engine aligns with our platform's batteries included but swappable approach allowing users to continue using any third-party orchestrator that is built on Docker Engine.

Let's take a look at how the new features in Docker 1.12 work.

## Creating Swarms with One Decentralized Building Block

It all starts with creating a swarm–a self-healing group of engines–which for the bootstrap node is as simple as:

    docker swarm init

Under the hood this creates a [Raft](https://raft.github.io/raft.pdf) consensus group of one node. This first node has the role of manager, meaning it accepts commands and schedule tasks. As you join more nodes to the swarm, they will by default be workers, which simply execute containers dispatched by the manager. You can optionally add additional manager nodes. The manager nodes will be part of the Raft consensus group. We use an optimized Raft store in which reads are serviced directly from memory which makes scheduling performance fast.

## Creating and Scaling Services

Just as you run a single container with docker run, you can now start a replicated, distributed, load balanced process on a swarm of Engines with docker service:

    docker service create –name frontend –replicas 5 -p 80:80/tcp nginx:latest

This command declares a desired state on your swarm of 5 nginx containers, reachable as a single, internally load balanced service on port 80 of any node in your swarm. Internally, we make this work using [Linux IPVS](http://www.linuxvirtualserver.org/software/ipvs.html), an in-kernel Layer 4 multi-protocol load balancer that's been in the Linux kernel for more than 15 years. With IPVS routing packets inside the kernel, swarm's routing mesh delivers high performance container-aware load-balancing.

When you create services, you can optionally create replicated or global services. Replicated services mean any number of containers that you define will be spread across the available hosts. Global services, by contrast, schedule one instance the same container on every host in the swarm.

Let's turn to how Docker provides resiliency. Swarm mode enabled engines are self-healing, meaning that they are aware of the application you defined and will continuously check and reconcile the environment when things go awry. For example, if you unplug one of the machines running an nginx instance, a new container will come up on another node. Unplug the network switch for half the machines in your swarm, and the other half will take over, redistributing the containers amongst themselves. For updates, you now have flexibility in how you re-deploy services once you make a change. You can set a rolling or parallel update of the containers on your swarm.

Want to scale up to 100 instances? It's as simple as:

    docker service scale frontend=100

A typical two-tier (web+db) application would be created like this:

```bash
docker network create -d overlay mynet
docker service create –name frontend –replicas 5 -p 80:80/tcp –network mynet mywebapp
docker service create –name redis –network mynet redis:latest
```

This is the basic architecture of this application:

[![](http://img.scoop.it/bdEKWLMnOnahqAX3rCPOQrnTzqrqzN7Y9aBZTaXoQ8Q=)](https://www.docker.com/getdocker)

## Security

A core principle for Docker 1.12 is creating a zero configuration, secure-by-default, out of the box experience for the Docker platform. One of the major hurdles that administrators often face with deploying applications into production is running them securely, Docker 1.12 allows an administrator to follow the exact same steps setting up a demo cluster that they would to setup a secure production cluster.

Security is not something you can bolt-on after the fact. That is why Docker 1.12 comes with mutually authenticated TLS, providing authentication, authorization and encryption to the communications of every node participating in the swarm, out of the box.

When starting your first manager, Docker Engine will generate a new Certificate Authority (CA) and a set of initial certificates for you. After this initial step, every node joining the swarm will automatically be issued a new certificate with a randomly generated ID, and their current role in the swarm (manager or worker). These certificates will be used as their cryptographically secure node identity for the lifetime of their participation in this swarm, and will be used by the managers to ensure secure dissemination of tasks and other updates.

[![](http://img.scoop.it/7M1ysO8rHO8VyFkrebWxT7nTzqrqzN7Y9aBZTaXoQ8Q=)](https://www.docker.com/getdocker) One of the biggest barriers of adoption of TLS has always been the difficulty of creating, configuring and maintaining the necessary Public Key Infrastructure (PKI). With Docker 1.12, everything not only gets setup and configured with safe defaults for you, but we also automated one of the most painful parts of dealing with TLS certificates: certificate rotation.

Under the hood, every node participating in the swarm is constantly refreshing its certificates, ensuring that potentially leaked or compromised certificates are no longer valid. The frequency with which certificates are rotated can be configured by the user, and set as low as every 30 minutes.

If you would like to use your own Certificate Authority, we also support an external-CA mode, where the managers in the swarm simply relay the Certificate Signing Requests of the nodes attempting to join the cluster to a remote URL.

## Bundles

Docker 1.12 introduces a new file format called a [Distributed Application Bundle](https://github.com/docker/docker/blob/master/experimental/docker-stacks-and-bundles.md) (experimental build only). Bundle is a new abstraction on top of service focused on the full stack application.

A Docker Bundle file is a declarative specification of a set of services that mandates:

* What specific image revision to run

* What networks to create

* How containers in those services must be networked to run

Bundle files are fully portable and are perfect deployment artifacts for software delivery pipelines because they let you ship fully spec'ed and versioned multi-container Docker apps.

The bundle file spec is simple and open, and you can create bundles however you want. To get you started, Docker Compose has experimental support for creating bundle files and with Docker 1.12 and swarm mode enabled, you can deploy the bundle files.

Bundles are an efficient mechanism for moving multi-service apps from developer laptops through CI to production. It's experimental, and we're looking for feedback from the community.

## Under the hood of Docker 1.12

When you take a look under the hood, Docker 1.12 uses a number of other interesting technologies. Inter-node communication is done using [gRPC](http://www.grpc.io/), which gives us HTTP/2 benefits like connection multiplexing and header compression. Our data structures are transmitted efficiently thanks to [protobufs](https://developers.google.com/protocol-buffers/).<br>


Check out these additional resources on Docker 1.12:
