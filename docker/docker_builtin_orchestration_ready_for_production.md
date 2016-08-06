Docker Built-in Orchestration Ready for Production
==============================

[Original URL](https://blog.docker.com/2016/07/docker-built-in-orchestration-ready-for-production-docker-1-12-goes-ga/)

> We wanted to thank everyone in the community for helping us achieve this great milestone of making Docker 1.12 generally available for production environments. Docker 1.12 adds the largest and most...

We wanted to thank everyone in the community for helping us achieve this great milestone of making Docker 1.12 generally available for production environments. Docker 1.12 adds the largest and most sophisticated set of features into a single release since the beginning of the Docker project. Dozens of engineers, both Docker employees and external contributors, have made substantial contributions to every aspect of 1.12 orchestration including core algorithms, integration into the Docker Engine, documentation and testing.

We're very grateful to the community, which has helped us with feedback, bug reports and new ideas. We couldn't have done it without the help in particular of the tens of thousands of [Docker for Mac and Windows](https://blog.docker.com/2016/07/ocker-for-mac-and-windows-production-ready) beta users who have been testing our 1.12 features since DockerCon in June. We've seen contributions ranging from bash tab completion to UX up-and-down votes that helped us understand what users want most. Compared to what we unveiled at DockerCon, we've ended up with significant improvements in the swarm node join workflow ([it's simpler](https://github.com/docker/swarmkit/pull/1189)), error reporting ([easier to view](https://github.com/docker/docker/pull/24917)), UX improvements ([more logical](https://github.com/docker/docker/pull/24816)), networking (fixed reliability issues) etc.

The core team also wanted to give a special thanks to one of our external maintainers and Docker Captain, [Chanwit Kaewkasi](https://github.com/chanwit), who through his own undertaking, drove the amazing [DockerSwarm2000](https://twitter.com/chanwit/status/756528590796234752) initiative which rallied the entire community around scaling an RC of 1.12 with swarm mode to nearly 2.4K nodes and just under 100K containers. This was achieved through our global community, who donated access to their machines in all shapes and sizes from bare metal, to Raspberry Pis, to various clouds to VMs from x86 architectures to ARM-based systems.

Through this evaluation using live data, we identified that built-in orchestration in Docker has already–in its first release–doubled Docker’s

[orchestration scale](https://github.com/swarm2k/swarm2k/blob/master/DATA_FILES.md)

 in just a half a year. While this validates the scalability of the architecture, there is still headroom for greater performance optimization in the future.

[https://github.com/swarm2k/swarm2k/blob/master/DATA_FILES.md](https://github.com/swarm2k/swarm2k/blob/master/DATA_FILES.md)

![](https://blog.docker.com/wp-content/uploads/swarm-1.png)

Let's now dig deep into this new built-orchestration architecture and why we took a very different architectural approach than others trying to address container orchestration.

## Swarm Mode Architectural Topology

![](https://blog.docker.com/wp-content/uploads/swarm-topology.png)

Built-in container orchestration in Docker 1.12 is an optional feature set that involves turning on a capability known as swarm mode. A swarm is a decentralized and highly available group of Docker nodes. Each node is a self-contained orchestration subsystem that has all the inherent capabilities needed to create a pool of common resources to schedule Dockerized services.

A swarm of Docker nodes creates a programmable topology, enabling the operator to choose which nodes are managers and which are workers. This includes common configurations like distributing managers across multiple availability zones. Because these roles are dynamic, they can be changed at any time through the API or CLI.

Managers are responsible for orchestrating the cluster, serving the Service API, scheduling tasks (containers), addressing containers that have failed health checks and much more. In contrast, worker nodes serve a much simpler function, which is executing the tasks to spawn containers and routing data traffic intended for specific containers. In production environments, we strongly recommend having nodes designated as either "managers" or "workers". In this mode, managers do not execute containers, thus reducing their workload and attack surface. Separately, one of swarm mode's security advances is that worker nodes do not have access to information in the datastore or the Service API. Worker nodes can only accept work and report on status. Thus, a compromised worker node is limited in the damage it can do to the system.

Our team focused quite a bit on architecting the communication between these nodes. Managers and workers have different communication requirements in terms of consistency, speed and volume; therefore, they use two distinct communication methods. Raft is used to share data between managers for strong consistency (at the cost of write speed and limited volume) while gossip is used between workers for fast communication and high volume (albeit with only eventual consistency). And communication between managers and workers has separate requirements still. The one thing that they all have in common is that they have encrypted communication by default; mTLS.

## Manager to Manager Communication: Always Available Quorums

When a node is given the role of manager, it joins a Raft consensus group to share information and perform leader election. The leader is the central authority maintaining the state, which includes lists of nodes, services and tasks across the swarm in addition to making scheduling decisions.That state is distributed across each manager node through a built-in Raft store. That is, managers have no dependency on an external key-value store like etcd or Consul, which is one less component for ops to have to manage. Non-leader managers act as hot spares and forward API requests to the current leader. The system is therefore **fault tolerant and highly available**.

Having an integrated distributed data store allows for many optimizations that could have not been achieved using a generic store – this results in our built-in orchestration system being extremely fast. A major optimization is that the entire swarm state is kept in-memory resulting in instant reads. This read optimization is highly beneficial to a critical orchestration; reconciling state which is a read-heavy workflow. Typically, a scheduler has to perform hundreds of reads: read the list of nodes, read what other tasks are running on those nodes, and so on. With the read optimization, there is an increase in velocity,which results from removing the need for hundreds of read network round-trips to the external database.

Writes are also critical to orchestration and the optimization is that in swarm mode writes can be batched together in a single network round-trip. A common write example is when you scale up a service, the orchestrator has to create a new object for every instance the user requests. With an external store, we would need to send a network request to the store **for every object** we create, wait for them to save the write, and repeat. This can take tens of milliseconds per request and they add up (especially if you're adding hundreds of new instances). With our model, we can batch hundreds of those objects into a single write.

The same write optimization has major impact on resiliency. For example, if a node that had 100 containers went down, instead of performing 100 writes to move them to different nodes, we can do all of that in a single write.

The final optimization is in how efficiently the data is persisted both in terms of size (protocol buffers) and performance (domain specific indexing). We can instantly query from memory the containers that are running on a given machine, the containers that are unhealthy for a specific service, etc.

![](https://blog.docker.com/wp-content/uploads/swarm-quorum-layer.png)

## Manager to Worker Communication


Worker nodes talk to manager nodes using gRPC, a fast protocol that works extremely well in harsh networking conditions, allows communication through internet links (built on HTTP/2) and has built-in versioning (so that different worker nodes running different versions of Engine can talk to the same manager node). Managers send workers sets of tasks to run. Workers send managers the status of the tasks in their assignment set, and a heartbeat so the managers can confirm that the worker is still alive.

As the diagram below illustrates, the dispatcher component of the manager code is what ultimately communicates with workers. It is responsible for dispatching tasks to each worker, while the worker (through it's executor component) is responsible for translating those tasks into containers and creating them.

![](https://blog.docker.com/wp-content/uploads/swarm-node-breakdown.png)

Based on the diagram above, let's briefly walk through what happens as a Docker service is created and ultimately spawns that set of containers:

### Service creation

- User sends the service definition to the API. The API accepts and stores
- Orchestrator reconciles desired state (as defined by the user) with the actual state (what's currently running on the swarm). It will pick up the new service created by the API and respond to that by creating a task (assuming in this case, the user requested only one instance of the service)
- Allocator allocates resources for tasks. It will notice a brand new service (created by the API) and a new task (created by the orchestrator) and will allocate IP addresses for both.
- Scheduler is responsible to assign tasks to worker nodes. It will notice a task with no node assigned and therefore will start scheduling. It tries to find the best match (based on constraints, resources, ...) and finally, it will assign the task to one of the nodes
- Dispatcher is where workers connect to. Once workers are connected to the Dispatcher, they wait for instructions. In this way, a task assigned by the scheduler will eventually flow down to the worker.

### Service update

- User updates a service definition through the API (e.g. change from 1 to 3 instances). API accepts and stores.
- Orchestrator reconciles desired vs actual. It will notice that even though the user wants 3 instances, only 1 is running and will respond to that by creating two additional tasks.
- Allocator, Scheduler and Dispatcher will perform the same steps as explained above and the two new tasks will land on workers

### Node failure

- Dispatcher will detect a node that was connected to it just failed (because of heartbeats). It will flag the node as DOWN.
- Orchestrator reconciles. 3 instances should be running but 1 of them just failed. Responds by creating a new task.
- Allocator, Scheduler and Dispatcher will perform the same steps as explained above and the 2 new tasks will land on new workers nodes.

![](https://blog.docker.com/wp-content/uploads/worker-to-worker-gossip.png)

- The workers use a Gossip network to communicate overlay network information to each other. Gossip is a high volume, peer to peer (P2P) network designed to work at high scale. A node accepts a task, starts a container and then it tells the other nodes that a container has started on the specified overlay network. This broadcast communication happens at the worker tier. The scale is achieved because the information is gossiped only to a constant number of random nodes and not to all nodes which works the same way regardless of the swarm size.

## What does it all mean?

So, what does Docker 1.12 orchestration really mean for developers and operators? There are three really important themes in this release and empowered by the rich architecture detailed above.:

**Fault-tolerant application deployment platform.** Modern applications are increasingly designed in a microservices architectural pattern where the process of serving back data to a user may need to invoke several different services. Real world machines fail all the time and these microservices need to continue to be available even in the face of such random failures. Docker 1.12 gives you this power by providing a zero-SPOF design leveraging a quorum of managers, plus a service abstraction that spawns multiple replicas and quickly reschedules them if their host disappears.

**Scale and performance.** Docker 1.12's swarm mode orchestration was designed from the ground up with scale and performance in mind. For example, the internal Raft distributed store has been optimized for lightning fast reads through an in-memory caching layer. Caching gives fast reads, but what happens when a write comes in? Of course the cache on each machine must be invalidated and updated. Our solution to that problem was to design the rest of the orchestration system in ways that are read-intensive but only _write_ to the Raft store when absolutely necessary. This combination of design decisions leads to an orchestration system that delivers better performance than what is achievable with orchestrators that are based on generic key-value stores.

**Secure networking.** In many systems, security is something you have to "turn on" by generating TLS certificates, running the system on a different port, and figuring out traffic flows to make sure no packets traverse an insecure network unencrypted. With Docker 1.12, all of these things are done for you out of the box [*]. The system is "secure by default," which means that you don't need to be a security expert to get a secure application management platform.

[*] There is one small exception to this statement. For overlay network traffic, current Docker versions require you to manually specify the flag `-o encrypted` (on `docker network create -d overlay`) to enable encryption. For all other traffic, encryption is enabled by default.

**Check out the Docker 1.12 Swarm Mode Deep Dive:**

### Learn More about Docker

[docker 1.12](https://blog.docker.com/tag/docker-1-12/), [docker swarm](https://blog.docker.com/tag/docker-swarm/), [orchestration](https://blog.docker.com/tag/orchestration/), [swarm mode](https://blog.docker.com/tag/swarm-mode/)
