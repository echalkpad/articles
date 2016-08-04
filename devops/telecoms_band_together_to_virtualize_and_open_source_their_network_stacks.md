# Telecoms Band Together to Virtualize and Open Source their Network Stacks

[Original URL](http://thenewstack.io/telecoms-band-together-virtualize-open-source-network-stacks/)

> A group of telecommunication companies and their software providers have come together to bring Network Functions Virtualization to their data centers. NFV is an industry-developed framework to...

A group of telecommunication companies and their software providers have come together to bring [Network Functions Virtualization](http://thenewstack.io/de-ossify-the-network-with-function-virtualization/) to their data centers. NFV is an industry-developed framework to virtualize telecom networks.

The group, formed under the umbrella of [European Telecommunications Standards Institute](https://www.etsi.org/about) (ETSI) is called OSM, which stands for Open Source MANO. MANO, which stands for Management and Orchestration, is the part of the NFV framework consisting of orchestrator software, virtualized network functions manager (VNFM) and Virtualized Infrastructure Manager (VIM).

There are eight founding members of OSM including Telefónica, BT, Canonical, Intel, Mirantis, RIFT.io, Telekom Austria Group, and Telenor, in addition to 15 initial participants such as Benu Networks, Brocade, Comptel, Dell, Indra, Korea Telecom, Metaswitch, RADWare, Red Hat, Sandvine, SK Telecom, Sprint, Telmex, xFlow and 6WIND.

MANO "is a crucial component in network virtualization architecture," said Antonio Elizondo, Telefónica's head of network virtualization strategy and technology. "By joining this community, we aim to accelerate the development of MANO while recognizing the value of open-source implementations of NFV and a need to harmonize efforts there."

The OSM community is to deliver an open source MANO stack aligned with ETSI NFV Information Models. This stack is released under Apache Public License 2.0.

"The goal of OSM is to provide an open source model and framework capable of orchestrating complex NFV use cases in a vendor-neutral way," wrote Kamesh Pemmaraju, vice president of product marketing at OpenStack vendor [Mirantis](https://www.mirantis.com/), in an e-mail. "The framework will provide all the key features that enable end-to-end NFV use cases and enable both service-level orchestration for the VNFs [network virtual functions] and resource-level orchestration of underlying infrastructure resources, including SDNs, in a holistic, coordinated, and automated way."

Such level of end-to-end solutions are already available from hardware vendors but they are proprietary and, as usual, lead to vendor lock-in. OSM breaks that vendor lock by being an open source technology that is being developed by the very community that consumes it: service providers.

NFV was created by the ETSI [to give network operators more control](http://thenewstack.io/opensource-virtual-network-functions-part3/) over their stack because traditionally network vendors offer their own proprietary software to manage the network, which leads to vendor-lock in, interoperability, scalability and elasticity problems. NFV essentially virtualizes the network and offers control via software.

That may sound something similar to SDN (software defined networking), but it's not. They both cater to two different audiences. Both utilize virtualization, which is becoming more and more popular as data center networking operators need capabilities to offer much dynamic provisioning of network on demand.

But SDN was created to cater to the needs of these operators in data centers; to give them flexibility, agility, programmability and centralized control of the networking infrastructure within the data center. On the other hand, NFV caters to service providers. It enables service providers to speed up deployment of new network services for faster revenue and growth plans.

"SDN and NFV are similar in that they both leverage industry standard high volume servers, switches and storage, which could be located in data centers," Pemmaraju wrote. "The difference is that SDN focuses on the network infrastructure flexibility and control while NFV focused on service agility and pulling all the networking functions to service a particular service provider use case such as vCPE or VEPC, vIMS etc.

"NFV and SDN are two separate software offerings," said Radhesh Balakrishnan, Red Hat's general manager for OpenStack. "While NFV centers around virtualizing software that a user is running on dedicated hardware, it can also leverage SDN technology. The SDN technology actually performs the network virtualization."

"Orchestration and service chaining of Virtualized Network Functions can be done with NFVO typically with the help of an underlying SDN such Open Contrail or ODL," Pemmaraju wrote, adding that Mirantis has certified a number of SDN solutions with OpenStack and is also certifying individual VNF's as part of its NFV partner ecosystem.

OSM aims at bringing, even more, control to operators at lower cost by developing a uniform deployment model and multi-use-case network services.

OSM uses existing open source modules from [Telefonica's OpenMANO project](http://www.tid.es/long-term-innovation/network-innovation/telefonica-nfv-reference-lab/openmano), Canonical's Juju generic VNF Manager and Rift.io orchestrator. And OpenStack is already there as the defacto virtual infrastructure manager (VIM) for ETSI architecture and OSM.

Canonical's John Zannos [said in a blog post](https://insights.ubuntu.com/2016/02/22/canonical-becomes-founding-member-of-open-source-mano-osm-group/), "Canonical's Juju, the open source generic VNF manager, provides generic application modeling enabling OSM Orchestration and VIM layers to focus on industry-specific orchestration challenges, and is central to the delivery of the OSM project stack. Juju enables the industry to collaborate and crowd-source expertise in performance, security and integration."

When asked about the role Mirantis is playing at OSM, Pemmaraju stated, "Mirantis already has a large of service provider customers who are currently using Mirantis OpenStack for IT workloads. It makes sense for these service providers to use OpenStack also as a common platform for NFV and IoT use cases. Murano can add unique value in resource orchestration and provide a clean interface to OpenStack which is the defacto underlying VIM for NFV infrastructure."

OSM's project scope covers both Resource and Service Orchestration to allow automated deployment and interconnection of all components, both for NFV network scenarios and the management of Network Service Life Cycles, according to According to Telefonica.

Mirantis will augment both Resource Orchestration (RO) and Service Orchestration (SO) with Fuel, an OpenStack project. In addition to that Mirantis will contribute Murano application catalog models along with open source software which is currently used in production NFV deployments to help accelerate OSM's Resource and Service Orchestrators.

Intel and Red Hat are sponsors of The New Stack.
