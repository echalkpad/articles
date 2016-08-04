# dockercmroundup

[Original URL](http://theagileadmin.com/tag/dockercmroundup/)

> Docker brings an incredibly appealing method of managing applications to the table, but also requires us to rebuild a lot of systems that aren't broken. In this post I'm going to look...

Docker brings an incredibly appealing method of managing applications to the table, but also requires us to rebuild a lot of systems that aren't broken. In this post I'm going to look at the pros and cons of Docker and its accompanying ecosystem, and take a look at how one might start to leverage the best parts of Docker without rewriting _everything._

What is it about [Docker](https://www.docker.com/) that is so exciting? It was a moonshot experiment that struck home. Rather than providing an incremental improvement over existing infrastructure patterns, Docker takes several leaps forward by providing a fresh way of managing our applications and making them accessible to developers at the same time.

Part of Docker's success relies on providing highly opinionated solutions to the problems that come with containerizing components in your system. While these solutions are invaluable in terms of accessibility and gaining adoption, they are neither the only solutions nor necessarily the best ones in every case. Even if you are sure you want to subscribe to the opinionated "Docker way" or think it's a worthwhile trade-off, you will still be accepting a new set of problems in exchange for the old ones, but the new set doesn't come with the benefit of a decade or so of tools and experience to leverage.

In this post I'm going to discuss what makes Docker attractive to me and what I'm not such a fan of. Then I'm going to explore a hybrid approach that seeks to take advantage of the best parts of Docker without locking me in to the not-so-great parts.

_P.S. I was hoping to put together a working demo of the architecture I describe below, but the proposed integration is still [not possible](https://github.com/lxc/lxd/issues/608)... so that's not going to happen. I've sat on this post for a while hoping things would change, but they haven't, and I've decided instead to put this out there as is, as a theoretical architecture._

## The Good

Some aspects of docker are home runs. In a time where microservices rule and developing software for the cloud is an obvious choice, how can you pass up a tool that makes managing a gazillion apps as cheap and easy as managing your monolith? And it's a DevOps game changer: In the same way that AWS removed the friction between dev and ops for provisioning a VM, Docker removes the friction of configuring an app's dependencies and installation. What's more, local dev of even dozens of applications can be kept lean, and we're closer than ever to feeling confident in "it works on my laptop."

In summary:

- Dense clouds
- Transactional installs
- Bundled dependencies
- Tools for packaging deterministic and repeatable deploys (and rollbacks)
- Developer workflow is simple and production-like

## The Bad

A lot of the design decisions of Docker involve trade-offs, and networking is no exception.

For local dev, where managing many VMs is especially difficult and high availability isn't important, Docker's unique method of standing up a virtual bridge interface and allocating new IPs as containers are started is really convenient. But it is less than complete when you start worrying about high availability and exposing your services to external systems. Layering in additional networking or mapping ports to the bridge interface starts to solve this problem, but it also leaves you with a jumble.

Service discovery largely abstracts away this jumble, but at this point we've gone through at least three networking transformations to effectively address our services and we haven't even started to integrate with non-Docker managed services.

Don't get me wrong, I definitely think service discovery is the way to go. I just think that since Docker has coupled networking so tightly with its implementation, it should have made it more flexible and done more to make inter-host communication work the same as intra-host communication. Additionally, better hooks to integrate with existing software-defined networks would make all the integration work feel less like a yak shave.

Isolation security is also a concern, but it is easier to shrug off because it should be [coming soon](http://blog.docker.com/2013/08/containers-docker-how-secure-are-they/). For the time being, however, there is a lack of user namespaces in Docker containers, so UID 0 (root) in a container is also UID 0 (root) on the host machine and has all the access that comes with that.

Another concerning thing about Docker is the Docker hub. Although you don't have to use this service or the images housed there in production, it's presented in such a way that many people still do. Even with the addition of download signature checks, we are still left with an index of images that aren't particularly well vetted or necessarily kept up to date. Many are built on top of full OSes that expose a larger attack surface than is necessary, and there is no established technique to ensure the base OS is up to date on its patches. There are great resources for [building thin base OSes](https://blog.docker.com/2013/06/create-light-weight-docker-containers-buildroot/) and ways to make sure they are kept up to date, but this is still more management left unaddressed.

In summary:

- User namespace security
- Docker hub security
- Networking

## The Ugly

One of the first things you realize with Docker is that you have to rethink _everything._ One is drawn in by the prospect of encapsulating their apps in a clean abstraction, but after fooling around with [Supervisord](http://supervisord.org/) for a while, most people start down the slippery slope of rewriting their entire infrastructure to keep their implementation clean.

This is because Docker isn't very composable when taken as a whole. If you want to talk to something in a docker container, you need an ambassador. If something in a docker container needs to talk to something not managed in docker, you need an ambassador. Sometimes, you even need an ambassador for two apps both running in containers. This isn't the end of the world, but it's more work and is a symptom of how the docker containers are really only composable with other Docker containers, not with our systems as a whole.

What this means is that to leverage the parts of docker we want (the transactional installs, bundled dependencies, and simplified local dev), we have to rewrite and rewire a whole lot of other stuff that wasn't broken or giving us trouble. Even if it was, you're still forced to tackle it all at once.

If we were being honest about the shipping container analogy, we'd end up with a container ship not just carrying containers but built with containers as well!

A lot of these problems come from the same thing that makes Docker so easy to use: the bundling (and resulting tight coupling) of all components needed to build, manage, and schedule containers.

This becomes especially clear when trying to place Docker on Gabriel Monroy's [Strata of the Container Ecosystem](http://t.co/4wuzpvMJhe). Although he places Docker in layer 4 (the container engine), aspects of it leak into almost every layer. It's this sprawl that makes Docker less composable and is why integrating with it often entails a huge amount of work.

Summary:

- Incompatibile with config management
- Not composable with existing infrastructure patterns and tools

## If not Docker, Then What?!

I'm not saying we should forget about docker and go back to the tried-and-true way of doing things. I'm not even saying to wait for the area to mature and produce better abstractions. I'm simply suggesting you do what you've always done: Choose the right tool for the right job, pragmatically apply the parts of Docker that make sense for you, and remember that the industry isn't done changing, so you should keep your architecture in a state that allows for iteration.

### Other players in this space

Part of Docker's appeal is its dirt simple bundling of a new approach that removes a lot of pain we've been having. But there are other tools out there that solve these same problems.

- System containers like [OpenVZ](https://openvz.org/Main_Page) or [LXD](http://www.ubuntu.com/cloud/tools/lxd) provide similar cloud density characteristics
- [rkt](https://github.com/coreos/rkt) is (almost ready to be) a competing application container that promises to implement a more composable architecture
- [Snappy Ubuntu](http://developer.ubuntu.com/en/snappy/) offers an alternative model for transactional installs, bundling dependencies, and isolation
- [Numerous SDN](http://thenewstack.io/sdn-series-part-eight-comparison-of-open-source-sdn-controllers/) technologies
- Config management ([Puppet](https://puppetlabs.com/), [Chef](https://www.chef.io/chef/), [Ansible](http://www.ansible.com/home)) provides deterministic and repeatable deploys
- [Vagrant](https://www.vagrantup.com/) simplifies local development in production-like environments

I have no doubt that we will look back and see Docker as the catalyst that led to a very different way of treating our software, but it isn't going to stay the only major player for long, and some of the old rules still apply: _Keep your architecture in a place that allows for iteration._

### Lxd, cfg mgmt, docker, and the next generation of stateless apps

So what would a cloud architecture that adopts just the good parts of Docker look like?

First off, here are the characteristics that are important to me and that I would like to support:

- The density and elasticity of containers.
- Transactional installs and rollbacks for my applications.
- The ability to develop locally in a near production environment (without near production hardware).
- Ephemeral systems.
- Managed systems (I'm not comfortable making them immutable because I trust config management tools more than a home-built "re-roll everything" script to protect me against the next bash vulnerability.).
- A composable platform that doesn't rely on the aspects of Docker (like networking) that would make iterating on it difficult.

One way to accomplish this it is to replace our traditional VMs with a system containers like LXD, continue managing infrastructure on those nodes the same way we always have with tools like Puppet, and start installing the service each node maintains in an application container like Docker.

I wish I could put together a demo to illustrate this, but right now [running Docker on LXD is infeasible](https://github.com/lxc/lxd/issues/608).

With this setup, we would have to change relatively little: We can expose our application on known ports to abstract away nonstandard networking; we only have one app so the namespace security vulnerability isn't a problem; and our infrastructure only needs incremental updates to support a container instead of a process.

Scaling can be achieved by adding new system container instances, and, while not as fast as spinning a new application container, it's still something that can be automated. We also don't have quite the same density, but we've retained most of the benefits there as well.

With this as a starting point, we can build out more twelve-factor cloud support based on what's most important to us: Service discovery, externalized config, software-defined networking, etc.

### Conclusion

The tired old debate of "Containers vs. Configuration Management" is rooted in fallacy. We've never tried to solve all of our problems with one technology before (You wouldn't pull down your Java libraries with Puppet, or keep your load balancer config in Maven Central), so why do we _have_ to start now?

Instead, I recommend we do what we always do: Choose the right tool for the right job.

I think there is definitely room for system containers, application containers, and configuration management to coexist. Hopefully more work will be done to make these two great technologies play nicely together.

_I (Ben Schwartz) am a software architect at [Kasasa by BancVue](https://www.kasasa.com/home/offer-kasasa.html) where lately I've been spending most of my time standing on the shoulders of our awesome DevOps culture to play with emergent infrastructure tools and techniques. Sometimes my experimentation makes it to my blog (including the [original posting](http://txt.fliglio.com/2015/07/containers-and-cfg-mgmt/) of this article) which can be found at [txt.fliglio.com](http://txt.fliglio.com/)._

_This article is part of our [Docker and the Future of Configuration Management](http://theagileadmin.com/tag/dockercmroundup/) blog roundup running this November. If you have an opinion or experience on the topic [you can contribute as well](http://theagileadmin.com/2015/10/13/docker-and-the-future-of-configuration-management-call-for-posts/)!_
