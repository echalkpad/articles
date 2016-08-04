# New Open Source Contributions Might Just Save Docker

[Original URL](http://readwrite.com/2016/02/11/open-source-docker)

> As I've written before, Docker provides a better way to package and distribute software, which is one reason Docker adoption keeps booming, growing 5X in the last year. Awesome! But good luck...

As I've [written before](http://readwrite.com/2014/12/23/docker-to-dominate-in-2015), Docker provides a better way to package and distribute software, which is one reason Docker adoption keeps booming, [growing 5X in the last year](https://www.datadoghq.com/docker-adoption/). Awesome! But good luck getting those Docker [containers into production](http://sirupsen.com/production-docker/). Not so awesome.

Enter Google. Google (now Alphabet) helped us all by open sourcing and spinning off its container orchestration solution called [Kubernetes](http://kubernetes.io/). Kubernetes makes a big difference, yet simply orchestrating Docker with Kubernetes doesn't necessarily make life easy for developers. There remains a yawning gap from test to production.

Why? Docker containers remain mired in the enormous complexity associated with managing the dependencies in the networking and storage layers. It's a modern day variation of yesteryear's [DLL hell](https://en.wikipedia.org/wiki/DLL_Hell).

This gap represents a huge market opportunity for a team of former Cisco Unified Computing Systems (UCS) honchos who started a company called [Datawise](http://www.datawise.io/). While still in stealth, their team has quietly been working on critical new capabilities for Kubernetes that they donated to the popular open-source project. Internally dubbed "[Project 6](https://github.com/DatawiseIO/Project6)" by the company's engineers, the contributed code makes it much easier to use Kubernetes to deploy containers in production.

![Mark Balch](http://a4.files.readwrite.com/image/upload/c_fit,cs_srgb,dpr_1.0,q_80,w_460/MTM2NDY2NzM5MDM3NzQyNjg5.jpg) Mark Balch 

<span class="attribution ng-hide">
  <a href="" class="ng-binding">
</a>
</span>

I recently caught up with Mark Balch, vice president of Products at Datawise, to learn more about their Kubernetes contributions and how the new software helps organizations actually get Docker off developer notebooks and running real workloads on servers.

**ReadWrite**: Tell me about the Kubernetes contributions Datawise made? Is this yet another case of vanity contributions to an open-source project that help no one but the contributing vendor?

**Mark Balch**: Our Kubernetes contributions have been accepted into the main trunk for version 1.2\. We provided a vendor-agnostic, standard platform for I/O resource scheduling in Kubernetes. Now developers can describe network and storage requirements when building an application by just using the familiar Kubernetes pod definition file. That frees developers to work with the network and storage providers that deliver the best capabilities to meet their cloud-native application needs.

**RW**: So what is hard about storage and networking for containers? How have challenges in networking and storage been an obstacle to their production use?

**MB**: There is broad agreement that production-grade networking and storage for containers is lacking. As everyone finds out quickly enough, do-it-yourself doesn't work. It's a maintenance nightmare.

Storage is a complete custom design: use a SAN, use local caching, the list goes on. There is absolutely no standard workflow or best practices. Networking, for its part, is either a software overlay model (overhead, high latency), operational compromises (port mapping, lack of interoperability with existing network services and networks), or custom integration.

For developers it means the risk of getting stuck in a walled garden with lock-in to AWS or other cloud providers. Or you can hire consultants to build a custom environment, then pay more to maintain it. Or hire internal employees to build and maintain what should work off the shelf. No thanks.

**RW**: What's the significance of the new Kubernetes open-source contributions by Datawise? What have you "unlocked" at the storage and networking layers for Kubernetes-managed containers?

**MB**: No code changes required. That's the biggest advantage our contributions give to developers. Docker containers just work in Kubernetes. There is no vendor lock-in. Our technology-agnostic APIs to specify storage and network requirements are in the open source container specification. Then we contributed another set of APIs to extend workload placement (scheduling) and configuration so containers are deployed for optimal performance to get what the developer requested.

Before Datawise's contributions, it was a complete Wild West. Developers worked separately to configure network and storage and then somehow custom linked to their containers.

**RW**: How far are we from containers on bare metal displacing virtual machines?

**MB**: Virtual machines are ubiquitous and not going away any time soon. Enterprises have enormous investments in their virtualized infrastructure. But there is absolutely a growing ecosystem around bare metal containers and Kubernetes, including Openstack Magnum. Openstack was built on KVM, yet Magnum supports both virtualized and bare-metal. Clearly developers want options and the leaders in container deployment today, like Google, are running bare metal. We expect more enterprises to increase the mix of bare metal running containers in their data centers.

**RW**: Where do you see Kubernetes having momentum today?

**MB**: Beyond the obvious example of Google, Kubernetes is at the core of solutions from Red Hat and CoreOS and certainly it's been embraced by Openstack's Magnum project, as mentioned. VMware demonstrated Kubernetes integration last year. It's not the only game in town, but arguably it provides the deepest out-of-box container support to date.

Having said that, we're not a Kubernetes company – we support an open ecosystem with the goal to ensure rapid deployment with predictable results regardless of the open source tool chain.

**RW**: Google has an army of engineers to manage things like this. How are average enterprises supposed to handle all these highly customized configurations around storage and networking in their own production container environments?

**MB**: That's the key point. If you have the cloud-native database PhD army, deploy your forces. Most businesses don't. They ultimately want to create unique value and not re-engineer what has already been solved. Our goal is to help enterprises deploy containerized applications quickly, knowing with certainty how they will perform, and that they will work off the shelf in an open ecosystem.
