# Basics – Docker, Containers, Hypervisors, CoreOS

[Original URL](http://etherealmind.com/basics-docker-containers-hypervisors-coreos/)

> Containers virtualize at the operating system level, Hypervisors virtualize at the hardware level. Hypervisors abstract the operating system from hardware, containers abstract the application...

**Containers** virtualize at the operating system level, **Hypervisors** virtualize at the hardware level.

**Hypervisors** abstract the operating system from hardware, **containers** abstract the application from the operation system.

**Hypervisors** consumes storage space for each instance. **Containers** use a single storage space plus smaller deltas for each layer and thus are much more efficient.

**Containers** can boot and be application-ready in less than 500ms and creates new designs opportunities for rapid scaling. **Hypervisors** boot according to the OS typically 20 seconds, depending on storage speed.

**Containers** have built-in and high value APIs for cloud orchestration. **Hypervisors** have lower quality APIs that have limited cloud orchestration value.

<embed src="http://etherealmind.com/wp-content/uploads/2013/10/EtherealMind-basics-logo-1-opt-150x124.png%20150w,%20http://etherealmind.com/wp-content/uploads/2013/10/EtherealMind-basics-logo-1-opt-300x248.png%20300w,%20http://etherealmind.com/wp-content/uploads/2013/10/EtherealMind-basics-logo-1-opt-595x491.png%20595w,%20http://etherealmind.com/wp-content/uploads/2013/10/EtherealMind-basics-logo-1-opt.png%20600w" class="alignright wp-image-8548 size-medium" width="300">

## Containers

There are many Linux container technologies but they all operate using the same principles of isolating an application space within an operating system.

[LXC – Linux Containers](https://linuxcontainers.org/)

> LXC is a userspace interface for the Linux kernel containment features.<br>
> Through a powerful API and simple tools, it lets Linux users easily create and manage system or application containers.

Although Docker now uses its own container format called libcontainer or other container such as Google containers in LMCTFY – [Github/google/lmctfy](https://github.com/google/lmctfy)

[What Is Docker? An open platform for distributed apps](https://docker.com/whatisdocker/)

Docker is software tool chain for managing LXC containers. This seems to be conceptually similar to the way that vSphere vCenter manages a large numbers of ESXi hypervisor instances. In operation it is very different and much more powerful.

> Docker is an open platform for developers and sysadmins to build, ship, and run distributed applications. Consisting of Docker Engine, a portable, lightweight runtime and packaging tool, and Docker Hub, a cloud service for sharing applications and automating workflows, Docker enables apps to be quickly assembled from components and eliminates the friction between development, QA, and production environments. As a result, IT can ship faster and run the same app, unchanged, on laptops, data center VMs, and any cloud. – [What Is Docker? An open platform for distributed apps](https://docker.com/whatisdocker/)

> Processes executing in a Docker container are isolated from processes running on the host OS or in other Docker containers. Nevertheless, all processes are executing in the same kernel. Docker leverages LXC to provide separate namespaces for containers, a technology that has been present in Linux kernels for 5+ years and considered fairly mature. It also uses Control Groups, which have been in the Linux kernel even longer, to implement resource auditing and limiting. – [Docker: Lightweight Linux Containers for Consistent Development and Deployment | Linux Journal](http://www.linuxjournal.com/content/docker-lightweight-linux-containers-consistent-development-and-deployment?page=0,1)

> However, as you spend more time with containers, you come to understand the subtle but important differences. Docker does a nice job of harnessing the benefits of containerization for a focused purpose, namely the lightweight packaging and deployment of applications. – [Docker: Lightweight Linux Containers for Consistent Development and Deployment | Linux Journal](http://www.linuxjournal.com/content/docker-lightweight-linux-containers-consistent-development-and-deployment?page=0,1)

- Docker containers have an API that allow for external adminstration of the containers. ?Core value proposition of Docker.
- Containers have less overhead than VMs (both KVM & ESX) and generally faster than running the same application inside a hypervisor.
- Most Linux applications can run in side a Docker container.

## Clustering and Multiples

- Containers promote the idea of spreading applications across multiple containers.
- I think this is partly because containers tend to be resource constrained by definition but because the deployment of containers is simple.
- For example, it makes more sense to deploy HAProxy as a load balancer in a container then multiple Tomcat / Node instances fo the applications. Spreading load across many small instances is well suited to cloud architectures where the peak performance of container can be constrained by overloading/oversubscription in the cloud provider.
- Docker and its sister products don't have any integration with legacy networking services. **The use of traditional load balancers and proxies doesn't make any sense in this system.**

## The Value of Docker

- Framework for container virtualization.
- Containers are Linux instances that hold applications.
- Docker is molded on the concept of shipping containers to present a standardised way of presenting
- Containers are long established practice e.g Solaris Zones and IBM LAPRs, and many more.
- But these products were relatively hard to use.
- Docker adds a wrapper around containers that makes them easy to consume. Toolchain for self-service.
- developers care about apps, operations cares about containers/hypervisors/bare metal.
- Open source means no licensing costs.

## Flow, Workflow and Change to Business Process

## Business Process

- Developers can build app in Docker container, then ship the container to a continuous integration servers (ala Jenkins).
- Developers can packaging the application into the operating system.
- Docker provides strong APIs that allow programmatic control. I mean, the API is core the what Docker does.
- Probably not optimal to use Puppet or Chef to build a Docker image
- Some people use Ansible to automate Docker. Ansible has some popularity with networking for its support for device specific features.

## Workflow

- there are thousands of images that are pre-packaged by Docker.
- Most images are based on Ubuntu.
- Docker orchestration platforms are important for more sophisticated uses like resilience, fault tolerance, scaling etc through intelligent container placement in the infrastructure. (Approx. similar to cloud orchestration for hypervisor placement in OpenStack).
- Orchestration systems like [Decking](http://decking.io/), New Relic's [Centurion](https://github.com/newrelic/centurion) and Google's [Kubernetes](https://github.com/GoogleCloudPlatform/kubernetes) all aim to help with the deployment and life cycle management of containers.
- There are many many more.
- Docker base images will have 'layers' of application added to the base image. The Union File System means that only the _delta_ of the _layer_ is added to the image. This dramtically reduces the space consumed by the operating system – its the same in every VM, why keep duplicating it in each VM (and then have the storage array deduplicate it).

## Infrastructure

- has its own storage system for the delta image. Currently Union File System, other options exist. And has been the subject of some strong debate. The root file system has everything needed to mount the Docker image.
- every time you make a change to the Docker image, then a new layer is created and marked read-only. This delta from the underlying Docker image reduces storage consumption considerably.
- there are many layers possible for each delta from the underlying Docker image.
- Docker images are built from a base image, then
- Docker can run baremetal or inside a hypervisor.
- LXC uses a Linux feature "control groups" which has the desirable side effect of provide deep insight into container resource consumption.

> Linux Containers rely on control groups which not only track groups of processes, but also expose a lot of metrics about CPU, memory, and block I/O usage. We will see how to access those metrics, and how to obtain network usage metrics as well. This is relevant for "pure" LXC containers, as well as for Docker containers.[Gathering LXC and Docker containers metrics | Docker Blog](http://blog.docker.com/2013/10/gathering-lxc-docker-containers-metrics/)

## CoreOS

- Many websites talk about CoreOS and Docker together without highlighting the differences. Confusing.
- CoreOS is a fork of Chrome OS, by the means of using its software development kit (SDK) freely available through Chromium OS as a base while adding new functionality and customizing it to support hardware used in servers – [CoreOS – Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/CoreOS)

> CoreOS provides no package manager, requiring all applications to run inside their containers, using Docker and its underlying Linux Containers (LXC) operating system–level virtualization technology for running multiple isolated Linux systems (containers) on a single control host (CoreOS instance). That way, resource partitioning is performed through multiple isolated userspace instances, instead of using a hypervisor and providing full-fledged virtual machines. This approach relies on the Linux kernel's cgroups functionality, which provides namespace isolation and abilities to limit, account and isolate resource usage (CPU, memory, disk I/O, etc.) of process groups. [CoreOS – Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/CoreOS)

- CoreOS is a lightweight _operating system_ designed to offer the minimum viable functionality as an operating system.
- I understand that this forms strong basis for deployment as the OS inside the Docker container.
- At the same time, it is suitable for hosting Docker containers. CoreOS is 'under' Docker and 'inside' it too. That might be my confusion.
- CoreOS seems to be a Linux distribution that has software packages for service discovery and configuration sharing that assist with large numbers of deployments. Useful for clustered applications.
- **Fleet** is an orchestration tool for CoreOS and separate from Docker which has it's own orchestration tools.

## EcoSystem

- Docker has wide ranging support from established vendors. To some onlookers this seems to be fashionable but there
- VMware has made strong defensive moves to embrace Docker
- Docker is well suited to PaaS where applications are developed and deployed directly from the developement platform into containers. Many PaaS platforms offer CI/CD systems where the deployment means more than 'live' but also test, UAT, etc etc.
- Many existing hosting companies can easily offer Docker hosting since the requirements are a modern Linux kernel and few dependencies. I think that Docker hosting isn't much use without orchestration tools and the user would have to provide them.
- The flock of big names around Docker including Google.
- Developers who are looking to adopt DevOps seem to have strong attraction to the product.
- Impact to operations is high with SysAdmins moving to Docker management and more valuable upstream functions integrated with the developers. That is, if PaaS systems are not used.

## Docker Orchestration

There are a number of Orchestration toolchains for Docker.

[Clocker – Creating a Docker Cloud with Apache Brooklyn « Cloudsoft](http://www.cloudsoftcorp.com/blog/2014/06/clocker-creating-a-docker-cloud-with-apache-brooklyn/)

[Getting Started with Docker Orchestration using Fig | Docker](https://blog.docker.com/2014/08/getting-started-with-orchestration-using-fig/) – Fig was a tool devloped to enhance docker deployment by a online Docker hosting company (cloud) and was recently acuqired by Docker itself.

Cloud Foundry – PivotalCF can use Docker to deploy PaaS-developed applications through service broker – [Docker Service Broker for Cloud Foundry | Pivotal P.O.V.](http://blog.pivotal.io/cloud-foundry-pivotal/products/docker-service-broker-for-cloud-foundry)

## Video

Found this video useful. The main session is about 35 minutes (and then questions which you could easily skip).
