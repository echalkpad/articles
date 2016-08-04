# Cisco UCS Integrated Infrastructure with Red Hat Enterprise Linux OpenStack Platform and Red Hat Ceph Storage Deployment Guide

[Original URL](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.html)

> Cisco Validated Design program consist of systems and solutions that are designed, tested, and documented to facilitate and improve customer deployments. These designs incorporate a wide range of...

Cisco Validated Design program consist of systems and solutions that are designed, tested, and documented to facilitate and improve customer deployments. These designs incorporate a wide range of technologies and products into a portfolio of solutions that have been developed to address the business needs of our customers.

The reference architecture described in this document is a realistic use case for deploying Red Hat Enterprise Linux OpenStack Platform 7 on Cisco UCS blade and rack servers. The document covers step by step instructions for setting UCS hardware, installing Red Hat Linux OpenStack Director, issues and workarounds evolved during installation, integration of Cisco Plugins with OpenStack, what needs to be done to leverage High Availability from both hardware and software, use case of Live Migration, performance and scalability tests done on the configuration, lessons learnt, best practices evolved while validating the solution and a few troubleshooting steps, etc.

Cisco UCS Integrated Infrastructure for Red Hat Enterprise Linux OpenStack Platform is all in one solution for deploying OpenStack based private Cloud using Cisco Infrastructure and Red Hat Enterprise Linux OpenStack platform. The solution is validated and supported by Cisco and Red Hat, to increase the speed of infrastructure deployment and reduce the risk of scaling from proof-of-concept to full enterprise production.

Automation, virtualization, cost, and ease of deployment are the key criteria to meet the growing IT challenges. Virtualization is a key and critical strategic deployment model for reducing the Total Cost of Ownership (TCO) and achieving better utilization of the platform components like hardware, software, network and storage. The platform should be flexible, reliable and cost effective for enterprise applications.

Cisco UCS solution implementing Red Hat Enterprise Linux OpenStack Platform provides a very simplistic yet fully integrated and validated infrastructure to deploy VMs in various sizes to suit your application needs. Cisco Unified Computing System (UCS) is a next-generation data center platform that unifies computing, network, storage access, and virtualization into a single interconnected system, which makes Cisco UCS an ideal platform for OpenStack architecture. The combined architecture of Cisco UCS platform, Red Hat Enterprise Linux OpenStack Platform and Red Hat Ceph Storage can accelerate your IT transformation by enabling faster deployments, greater flexibility of choice, efficiency, and lower risk. Furthermore, Cisco Nexus series of switches provide the network foundation for the next-generation data center.

This deployment guide provides the audience a step by step instruction of Installing Red Hat Linux OpenStack Director and Red Hat Ceph Storage on Cisco UCS blades and rack servers. The traditional complexities of installing OpenStack are simplified by Red Hat Linux OpenStack Director while Cisco UCS Manager Capabilities bring an integrated, scalable, multi-chassis platform in which all resources participate in a unified management domain. The solution included in this deployment guide is a partnership from Cisco Systems, Inc., Red Hat, Inc., and Intel Corporation.

The audience for this document includes, but is not limited to, sales engineers, field consultants, professional services, IT managers, partner engineers, IT architects, and customers who want to take advantage of an infrastructure that is built to deliver IT efficiency and enable IT innovation. The reader of this document is expected to have the necessary training and background to install and configure Red Hat Enterprise Linux, Cisco Unified Computing System (UCS) and Cisco Nexus Switches as well as a high level understanding of OpenStack components. External references are provided where applicable and it is recommended that the reader be familiar with these documents.

Readers are also expected to be familiar with the infrastructure, network and security policies of the customer installation.

This document describes the step by step installation of Red Hat Enterprise Linux OpenStack Platform 7 and Red Hat Ceph Storage 1.3 architecture on Cisco UCS platform. It also discusses about the day to day operational challenges of running OpenStack and steps to mitigate them, High Availability use cases, Live Migration, common troubleshooting aspects of OpenStack along with Operational best practices.

This solution is focused on Red Hat Enterprise Linux OpenStack Platform 7 (based on the upstream OpenStack Kilo release) and Red Hat Ceph Storage 1.3 on Cisco Unified Computing System. The advantages of Cisco UCS and Red Hat Enterprise Linux OpenStack Platform combine to deliver an OpenStack Infrastructure as a Service (IaaS) deployment that is quick and easy to setup. The solution can scale up for greater performance and capacity or scale out for environments that require consistent, multiple deployments. It provides:

Converged infrastructure of Compute, Networking, and Storage components from Cisco UCS is a validated enterprise-class IT platform, rapid deployment for business critical applications, reduces costs, minimizes risks, and increase flexibility and business agility Scales up for future growth.

Red Hat Enterprise Linux OpenStack Platform 7 on Cisco UCS helps IT organizations accelerate cloud deployments while retaining control and choice over their environments with open and inter-operable cloud solutions. It also offers redundant architecture on compute, network, and storage perspective. The solution comprises of the following key components:

<span>·</span>

Cisco Unified Computing System (UCS)

<span>—</span>

Cisco UCS 6200 Series Fabric Interconnects

<span>—</span>

Cisco VIC 1340

<span>—</span>

Cisco VIC 1227

<span>—</span>

Cisco 2204XP IO Module or Cisco UCS Fabric Extenders

<span>—</span>

Cisco B200 M4 Servers

<span>—</span>

Cisco C240 M4 Servers

<span>·</span>

Cisco Nexus 9300 Series Switches

<span>·</span>

Cisco Nexus 1000v for KVM

<span>·</span>

Cisco Nexus Plugin for Nexus Switches

<span>·</span>

Cisco UCS Manager Plugin for Cisco UCS

<span>·</span>

Red Hat Enterprise Linux 7.x

<span>·</span>

Red Hat Enterprise Linux OpenStack Platform Director

<span>·</span>

Red Hat Enterprise Linux OpenStack Platform 7

<span>·</span>

Red Hat Ceph Storage 1.3

The scope is limited to the infrastructure pieces of the solution. It does not address the vast area of the OpenStack components and multiple configuration choices available in OpenStack.

This architecture is based on Red Hat Enterprise Linux OpenStack platform build on Cisco UCS hardware is an integrated foundation to create, deploy, and scale OpenStack cloud based on Kilo OpenStack community release. Kilo version introduces Red Hat Linux OpenStack Director (RHEL-OSP), a new deployment tool chain that combines the functionality from the upstream TripleO and Ironic projects with components from previous installers.

The reference architecture use case provides a comprehensive, end-to-end example of deploying RHEL-OSP7 cloud on bare metal using OpenStack Director and services through heat templates.

The first section in this Cisco Validated Design covers setting up of Cisco hardware the blade and rack servers, chassis and Fabric Interconnects and the peripherals like Nexus 9000 switches. The second section explains the step by step install instructions for installing cloud through RHEL OSP Director. The final section includes the functional and High Availability tests on the configuration, Performance, Live migration tests, and the best practices evolved while validating the solution.

[]()[**Figure 1**OpenStack Deployment Architecture]()

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_3.jpg)

Figure 2 illustrates the physical topology of this solution.

[]()[**Figure 2**Physical Topology]()

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_4.jpg)

The configuration comprised of 3 controller nodes, 6 compute nodes, 3 storage nodes, a pair of UCS Fabrics and Nexus switches, where most of the tests were conducted. In another configuration the system had 20 Compute nodes, 12 Ceph nodes and 3 controllers distributed across 3 UCS chassis where few install and scalability tests were performed. Needless to say that architecture is scalable horizontally and vertically within the chassis.

<span>·</span>

More Compute Nodes and Chassis can be added as desired.

<span>·</span>

More Ceph Nodes for storage can be added. The Ceph nodes can be UCS C240M4L or C240M4S.

<span>·</span>

If more bandwidth is needed, Cisco IO Modules can be 2208XP as opposed to 2204XP used in the configuration.

<span>·</span>

Both Cisco Fabric Interconnects and Cisco Nexus Switches can be on 96 port switches instead of 48 ports as shown above.

This solution components and diagrams are implemented per the [Design Guide](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7_design.pdf) and basic overview is provided below.

The Cisco Unified Computing System is an integrated, scalable, multi-chassis platform in which all resources participate in a unified management domain. The Cisco Unified Computing System accelerates the delivery of new services simply, reliably, and securely through end-to-end provisioning and migration support for both virtualized and non-virtualized systems. Cisco UCS manager using single connect technology manages servers and chassis and performs auto-discovery to detect inventory, manage, and provision system components that are added or changed.

The Red Hat Enterprise Linux OpenStack Platform IaaS cloud on Cisco UCS servers is implemented as a collection of interacting services that control compute, storage, and networking resources.

OpenStack Networking handles creation and management of a virtual networking infrastructure in the OpenStack cloud. Infrastructure elements include networks, subnets, and routers. Because OpenStack Networking is software-defined, it can react in real-time to changing network needs, such as creation and assignment of new IP addresses.

Compute serves as the core of the OpenStack cloud by providing virtual machines on demand. Computes supports the libvirt driver that uses KVM as the hypervisor. The hypervisor creates virtual machines and enables live migration from node to node.

OpenStack also provides storage services to meet the storage requirements for the above mentioned virtual machines.

The Keystone provides user authentication to all OpenStack systems.

The solution also includes OpenStack Networking ML2 Core components.

Cisco Nexus 1000V OpenStack solution is an enterprise-grade virtual networking solution, which brings Security, Policy control, and Visibility together with Layer2/Layer 3 switching at the hypervisor layer. When it comes to application visibility, Cisco Nexus 1000V provides insight into live and historical VM migrations and advanced automated troubleshooting capabilities to identify problems in seconds.

The Cisco Nexus driver for OpenStack Neutron allows customers to easily build their infrastructure-as-a-service (IaaS) networks using the industry's leading networking platform, delivering performance, scalability, and stability with the familiar manageability and control you expect from Cisco® technology.

Cisco UCS Manager Plugin configures compute blades with necessary VLAN's. The Cisco UCS Manager Plugin talks to the Cisco UCS Manager application running on Fabric Interconnect.

Table 1 lists the Hardware and Software releases used for solution verification.

[<span>Table 1  </span>

Required Hardware Components]()

                      | Hardware                      | Quantity | Firmware Details
--------------------- | ----------------------------- | -------- | ----------------
Director              | Cisco UCS B200M4 blade        |          | 2.2(5)
Controller            | Cisco UCS B200M4 blade        |          | 2.2(5)
Compute               | Cisco UCS B200M4 blade        |          | 2.2(5)
Storage               | Cisco UCS C240M4L rack server |          | 2.2(5)
Fabrics Interconnects | Cisco UCS 6248UP FIs          |          | 2.2(5)
Nexus Switches        | Cisco Nexus 9372 NX-OS        |          | 7.0(3)I1(3)

<span>Table 2  </span>

Software Specifications

<span class="BBold">Software</span>

<span class="BBold">Version</span>

Operating System

Red Hat Enterprise Linux

7.2

OpenStack Platform

Red Hat Enterprise Linux OpenStack Platform

RHEL-OSP 7.2

Red Hat Enterprise Linux OpenStack Director

RHEL-OSP 7.2

Red Hat Ceph Storage

1.3

Cisco N1000V

VSM and VEM modules

5.2(1)SK3(2.2x)

Plugins

Cisco Nexus Plugin

RHEL-OSP 7.2

Cisco UCSM Plugin

RHEL-OSP 7.2

Cisco N1KV Plugin

RHEL-OSP 7.2

This section contains the Bill of Materials used in the configuration.

Component

Model

Quantity

Comments

OpenStack Platform Director Node

Cisco UCS B200M4 blade

CPU – 2 x E5-2630 V3

Memory – 8 x 16GB 2133 MHz DIMM – total of 128G

Local Disks – 2 x 300 GB SAS disks for Boot

Network Card – 1x1340 VIC

Raid Controller – Cisco MRAID 12 G SAS Controller

Controller Nodes

Cisco UCS B200M4 blades

CPU – 2 x E5-2630 V3

Memory – 8 x 16GB 2133 MHz DIMM – total of 128G

Local Disks – 2 x 300 GB SAS disks for Boot

Network Card – 1x1340 VIC

Raid Controller – Cisco MRAID 12 G SAS Controller

Compute Nodes

Cisco UCS B200M4 blades

CPU – 2 x E5-2660 – V3

Memory – 16 x 16GB 2133 MHz DIMM – total of 256G

Local Disks – 2 x 300 GB SAS disks for Boot

Network Card – 1x1340 VIC

Raid Controller – Cisco MRAID 12 G SAS Controller

Storage Nodes

Cisco UCS C240M4L rack servers

CPU – 2 x E5-2630 – V3

Memory – 8 x 16GB 2133 MHz DIMM – total of 128G

Internal HDD – None

Ceph OSD's – 8 x 6TB SAS Disks

Ceph Journals – 2 x 400GB SSD's

OS Boot – 2 x 1TB SAS Disks

Network Cards – 1 x VIC 1227

Raid Controller – Cisco MRAID 12 G SAS Controller

Chassis

Cisco UCS 5108 Chassis

IO Modules

IOM 2204 XP

Fabric Interconnects

Cisco UCS 6248UP Fabric Interconnects

Switches

Cisco Nexus 9372PX Switches

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Deployment and a few performance tests have been evaluated on another configuration with similar hardware and software specifications as listed above but with 20 Compute nodes and 12 Ceph storage nodes.

Server pools will be utilized to divide the OpenStack server roles for ease of deployment and scalability. These pools will also decide the placement of server roles within the infrastructure. The following pools were created.

<span>·</span>

OpenStack Controller Server pool

<span>·</span>

OpenStack Compute Server pool

<span>·</span>

OpenStack Ceph Server pool

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

The Undercloud node will be a single server and is not associated with any pool. It is a standalone template and is used to create a service profile clone.

The compute server pool allows quick provisioning of additional hosts by adding the new servers to the compute server pool. The newly provisioned compute hosts can be added into an existing OpenStack environment through introspection and Overcloud deploy, covered later in this document.

Figure 3 lists the server distribution in the Cisco UCS Chassis.

[**Figure 3**Servers Distribution in Cisco UCS Chassis – Design 1 (As per this Validated Solution)]()

![Description: C:\Users\vijd\Desktop\Austin-CVD\diagrams\Server-Distribution1.jpg](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_5.jpg)

The controllers and computes are distributed across the chassis. This gives High Availability to the stack though a failure of Chassis per se does not happen. There is only one Installer node in the system and can be added in any one of the Chassis as above. In case of larger deployments having 3 or more chassis, it is recommended to distribute one controller in each chassis.

In larger deployments where the chassis are fully loaded with blades a better approach while creating server pools could be distribute manually the tenant and storage traffic across the Fabrics.

Compute pools are created as listed below:

<span>·</span>

OpenStack Compute Server pool A

<span>·</span>

OpenStack Compute Server pool B

<span>·</span>

The Compute Server pool A can be used for the blades on the left side of the chassis pinned to Fabric A, while the Compute Server pool B can be used for the blades on the right side of the chassis. This is achieved with pool A using vNICs pinned to Fabric A while pool B tenant vNICs pinned to Fabric B.

[**Figure 4**Servers Distribution in Cisco UCS Chassis – Design 2 (Scalable Architecture)]()

![Description: C:\Users\vijd\Desktop\Austin-CVD\diagrams\Server-Distribution2.jpg](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_6.jpg)

The above method ensures that the tenant traffic is distributed evenly across both the fabrics.

Service profiles will be created from the service templates. However once successfully created, they will be unbound from the templates. The vNIC to be used for tenant traffic needs to be identified as eth1\. This is to take care of the current limitation in [Cisco UCSM kilo plugin](http://docwiki.cisco.com/wiki/OpenStack/UCS_Mechanism_Driver_for_ML2_Plugin_Kilo) for OpenStack. This is being addressed while this document is being written and will be taken care in the future releases.

Figure 5 illustrates the network layout.

[]()[**Figure 5**Network Layout]()

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_7.jpg)

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

A Floating or Provider network is not necessary. It has been included in the configuration because of the limitation in the IP's. Virtual machines can be configured to have direct access through the external network too.

A separate network layout was also verified in another POD without any floating IP's. This is for customers who do not have the limitations of external IP's as encountered in the configuration. However most of the tests were performed with floating IP's only. The Network Topology in this design is almost similar to what shown above. The virtual machines can be accessed directly from the external work. The below diagram depicts how the network was configured in this POD without floating network. With this you need not have floating vNIC interface for Controller Service profile, nor you will need a section of block in controller.yaml for floating ip and passing the floating parameter in your overcloud deploy command. Refer [Appendix B](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.html#Appendix_B) for details.

![Description: OPenstack-Topology-Design2](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_8.jpg)

The family of vNICs are placed in the same Fabric Interconnect to avoid an extra hop to the upstream Nexus switches.

The following categories of vNICs are used in the setup:

<span>·</span>

Provisioning Interfaces pxe vNICs are pinned to Fabric A

<span>·</span>

Tenant vNICs are pinned to Fabric A

<span>·</span>

Internal API vNICs are pinned to Fabric B

<span>·</span>

External Interfaces vNICs are pinned to Fabric A

<span>·</span>

Storage Public Interfaces are pinned to Fabric A

<span>·</span>

Storage Management Interfaces are pinned to Fabric B

Only one Compute server pool is created in the setup. However, we may create multiple pools if desired as mentioned above.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

While configuring vNICs in templates and with failover option enabled in Fabrics, the vNICs order has to be specified manually as shown below.

[**Figure 6**vNIC Placement]()

![Description: cid:472F721A-7929-4483-87D5-68FD1A7DF4A2@cisco.com](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_9.png)

The order of vNIC's has to be pinned as above for consistent PCI device naming options. The above is an example of controller blade. The same has to be done for all the other servers, the Compute and Storage nodes. This order should match the Overcloud heat templates NIC1, NIC2, NIC3, and NIC4.

Red Hat Linux OpenStack Platform (RHEL-OSP7) delivers an integrated foundation to create, deploy, and scale a more secure and reliable public or private OpenStack cloud. RHEL-OSP7 starts with the proven foundation of Red Hat Enterprise Linux and integrates Red Hat's OpenStack Platform technology to provide production ready cloud platform. RHEL-OSP7 Director is based on community based Kilo OpenStack release. Red Hat RHEL-OSP7 introduces a cloud installation and lifecycle management tool chain. It provides

<span>·</span>

Simplified deployment through ready-state provisioning of bare metal resources

<span>·</span>

Flexible network definitions

<span>·</span>

High Availability with Red Hat Enterprise Linux Server High Availability

<span>·</span>

Integrated setup and Installation of Red Hat Ceph Storage 1.3

Figure 7 illustrates the reference architecture workflow.

[**Figure 7**Reference Architecture Workflow]()

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_10.jpg)

Red Hat Linux OpenStack Platform Director is a new set of tool chain introduced with Kilo that automates the creation of Undercloud and Overcloud nodes as above. It performs the following:

<span>·</span>

Install Operating System on Undercloud Node

<span>·</span>

Install Undercloud Node

<span>·</span>

Perform Hardware Introspection

<span>·</span>

Prepare Heat templates and Install Overcloud

<span>·</span>

Implement post Overcloud configuration steps

<span>·</span>

Create Tenants, Networks and Instances for Cloud

Undercloud Node is the deployment environment while Overcloud nodes are referred to nodes actually rendering the cloud services to the tenants.

The Undercloud is the TripleO (OOO – OpenStack over OpenStack) control plane. It uses native OpenStack APIs and services to deploy, configure, and manage the production OpenStack deployment. The Undercloud defines the Overcloud with Heat templates and then deploys it through the Ironic bare metal provisioning service. OpenStack Director includes predefined Heat templates for the basic server roles that comprise the Overcloud. Customizable templates allow Director to deploy, redeploy, and scale complex Overclouds in a repeatable fashion.

Ironic gathers information about bare metal servers through a discovery mechanism known as introspection. Ironic pairs servers with bootable images and installs them through PXE and remote power management.

Red Hat Linux OpenStack Director deploys all servers with the same generic image by injecting Puppet modules into the image to tailor it for specific server roles. It then applies host-specific customizations through Puppet including network and storage configurations. While the Undercloud is primarily used to deploy OpenStack, the Overcloud is a functional cloud available to run virtual machines and workloads.

The following subsections detail the roles that comprise the Overcloud.

This role provides endpoints for REST-based API queries to the majority of the OpenStack services. These include Compute, Image, Identity, Block, Network, and Data processing. The controller nodes also provide the supporting facilities for the API's, database, load balancing, messaging, and distributed memory objects. They also provide external access to virtual machines. The controller can run as a standalone server or as a High Availability (HA) cluster. The current configuration was configured with HA.

This role provides the processing, memory, storage, and networking resources to run virtual machine instances. It runs the KVM hypervisor by default. New instances are spawned across compute nodes in a round-robin fashion based on resource availability.

Ceph is a distributed block, object store and file system. This role deploys Object Storage Daemon (OSD) nodes for Ceph clusters. It also installs the Ceph Monitor service on the controller. The instance distribution is influenced by the currently set filters. The default filters can be altered if needed; for more information, please refer to the [OpenStack documentation](http://docs.openstack.org/developer/nova/filter_scheduler.html).

OpenStack requires multiple network functions. While it is possible to collapse all network functions onto a single network interface, isolating communication streams in their own physical or virtual networks provides better performance and scalability. Each OpenStack service is bound to an IP on a particular network. In a cluster a service virtual IP is shared among all of the HA controllers.

The Control plane installs Overcloud through this network. All nodes must have a physical interface attached to the provisioning network. This network carries DHCP/PXE and TFTP traffic. It must be provided on a dedicated interface or native VLAN to the boot interface. The provisioning interface can also act as a default gateway for to Overcloud; the compute and storage nodes use this provisioning gateway interface on the Undercloud node.

The External network is used for hosting the Horizon dashboard and the Public APIs, as well as hosting the floating IPs that are assigned to VMs. The Neutron L3 routers which perform NAT are attached to this interface. The range of IPs that are assigned to floating IPs should not include the IPs used for hosts and VIPs on this network.

This network is used for connections to the API servers, as well as RPC messages using RabbitMQ and connections to the database. The Glance Registry API uses this network, as does the Cinder API. This network is typically only reachable from inside the OpenStack Overcloud environment, so API calls from outside the cloud will use the Public APIs.

Virtual machines communicate over the tenant network. It supports three modes of operation: VXLAN, GRE, and VLAN. VXLAN and GRE tenant traffic is delivered through software tunnels on a single VLAN. Individual VLANs correspond to tenant networks in the case where VLAN tenant networks are used.

This network carries storage communication including Ceph, Cinder, and Swift traffic. The virtual machine instances communicate with the storage servers through this network. Data-intensive OpenStack deployments should isolate storage traffic on a dedicated high bandwidth interface, i.e. 10 GB interface. The Glance API, Swift proxy, and Ceph Public interface services are all delivered through this network.

Storage management communication can generate large amounts of network traffic. This network is shared between the front and back end storage nodes. Storage controllers use this network to access data storage nodes. This network is also used for storage clustering and replication traffic.

Network traffic types are assigned to network interfaces through Heat template customizations prior to deploying the Overcloud. Red Hat Enterprise Linux OpenStack Platform Director supports several network interface types including physical interfaces, bonded interfaces (not with Cisco UCS Fabric Interconnects), and either tagged or native 802.1Q VLANs.

The previous section discussed server roles. Each server role requires access to specific types of network traffic. The network isolation feature allows Red Hat Enterprise Linux OpenStack Platform Director to segment network traffic by particular network types. When using network isolation, each server role must have access to its required network traffic types.

By default, Red Hat Enterprise Linux OpenStack Platform Director collapses all network traffic to the provisioning interface. This configuration is suitable for evaluation, proof of concept, and development environments. It is not recommended for production environments where scaling and performance are primary concerns.

Red Hat Enterprise Linux OpenStack Platform 7 supports tenant network communication through the OpenStack Networking (Neutron) service. OpenStack Networking supports overlapping IP address ranges across tenants through the Linux kernel's network namespace capability. It also supports three default networking types:

Each tenant is assigned a network subnet mapped to an 802.1q VLAN on the physical network. This tenant networking type requires VLAN-assignment to the appropriate switch ports on the physical network.

The VXLAN mechanism driver encapsulates each layer 2 Ethernet frame sent by the VMs in a layer 3 UDP packet. The UDP packet includes an 8-byte field, within which a 24-bit value is used for the VXLAN Segment ID. The VXLAN Segment ID is used to designate the individual VXLAN over network on which the communicating VMs are situated. This provides segmentation for each Tenant network

<span>The GRE mechanism driver encapsulates each layer 2 Ethernet frame sent by the VMs in a special IP packet using the GRE protocol (IP type 47). The GRE header contains a 32-bit <em>key</em> which is used to identify a flow or virtual network in a tunnel. This provides segmentation for each Tenant network.</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Cisco Nexus Plugin is bundled in OpenStack Platform 7 kilo release. While it can support both VLAN and VXLAN configurations, only VLAN mode is validated as part of this design. VXLAN will be considered in future releases when the current VIC 1340 Cisco interface card will be certified on VXLAN and Red Hat operating system.

Two components drive HA for all core and non-core OpenStack services: the cluster manager and the proxy server.

The cluster manager is responsible for the startup and recovery of an inter-related services across a set of physical machines. It tracks the cluster's internal state across multiple machines. State changes trigger appropriate responses from the cluster manager to ensure service availability and data integrity.

This section describes the steps to configure networking for Overcloud. The network setup used in the configuration as shown in [Figure 5](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.html#Network_Layout) earlier.

The configuration is done using Heat Templates on the Undercloud prior to deploying the Overcloud. These steps need to be followed after the Undercloud install. In order to use network isolation, we have to define the Overcloud networks. Each will have an IP subnet, a range of IP addresses to use on the subnet and a VLAN ID. These parameters will be defined in the network environment file. In addition to the global settings there is a template for each of the nodes like controller, compute and Ceph that determines the NIC configuration for each role. These have to be customized to match the actual hardware configuration.

Heat communicates with Neutron API running on the Undercloud node to create isolated networks and to assign neutron ports on these networks. Neutron will assign a static port to each port and Heat will use these static IP's to configure networking on the Overcloud nodes. A utility called os-net-config runs on each node at provisioning time to configure host level networking.

Table 3 lists the VLANs that are created on the configuration.

[<span>Table 3  </span>

VLANs]()

**<span>VLAN Name</span>**

**<span>VLAN Purpose</span>**

**<span>VLAN ID or VLAN Range Used in This Design for Reference</span>**

<span>PXE</span>

<span>Provisioning Network VLAN</span>

<span>110</span>

<span>Internal-API</span>

<span>Internal API Network</span>

<span>100</span>

<span>External</span>

<span>External Network</span>

<span>215</span>

<span>Storage Public</span>

<span>Storage Public Network</span>

<span>120</span>

<span>Storage Management</span>

<span>Storage Cluster or Management Network</span>

<span>150</span>

<span>Floating</span>

<span>Floating Network</span>

<span>160</span>

Red Hat Linux OpenStack Director's approach is to leverage Red Hat's distributed cluster system.

The cluster manager is responsible for the startup and recovery of an inter-related services across a set of physical machines. It tracks the cluster's internal state across multiple machines. State changes trigger appropriate responses from the cluster manager to ensure service availability and data integrity.

In the HA model Clients do not directly connect to service endpoints. Connection requests are routed to service endpoints by a proxy server.

Cluster manager provides state awareness of other machines to coordinate service startup and recovery, shared quorum to determine majority set of surviving cluster nodes after failure, data integrity through fencing and automated recovery of failed instances.

Proxy servers help in load balancing connections across service end points. The nodes can be added or removed without interrupting service.

Red Hat Linux OpenStack Director uses HAproxy and Pacemaker to manage HA services and load balance connection requests. With the exception of RabbitMQ and Galera, HAproxy distributes connection requests to active nodes in a round-robin fashion. Galera and RabbitMQ use persistent options to ensure requests go only to active and/or synchronized nodes. Pacemaker checks service health at one second intervals. Timeout settings vary by service.

The combination of Pacemaker and HAProxy:

<span>·</span>

Detects and recovers machine and application failures

<span>·</span>

Starts and stops OpenStack services in the correct order

<span>·</span>

Responds to cluster failures with appropriate actions including resource failover and machine restart and fencing

RabbitMQ, memcached, and mongodb do not use HAProxy server. These services have their own failover and HA mechanisms.

OpenStack Modular Layer 2 (ML2) allows separation of network segment types and the device specific implementation of segment types. ML2 architecture consists of multiple 'type drivers' and 'mechanism drivers'. Type drivers manage the common aspects of a specific type of network while the mechanism driver manages specific device to implement network types.

Type drivers

<span>·</span>

VLAN

<span>·</span>

GRE

<span>·</span>

VXLAN

Mechanism drivers

<span>·</span>

Cisco UCSM

<span>·</span>

Cisco Nexus

<span>·</span>

Cisco Nexus 1000v

<span>·</span>

Openvswitch, Linuxbridge

The Cisco Nexus driver for OpenStack Neutron allows customers to easily build their Infrastructure-as-a-Service (IaaS) networks using the industry's leading networking platform, delivering performance, scalability, and stability with the familiar manageability and control you expect from Cisco® technology. ML2 Nexus drivers dynamically provision OpenStack managed VLAN's on Nexus switches. They configure the trunk ports with the dynamically created VLAN's solving the logical port count issue on Nexus switches. They provide better manageability of the network infrastructure.

ML2 UCSM drivers dynamically provision OpenStack managed VLAN's on Fabric Interconnects. They configure VLAN's on Controller and Compute node VNIC's. The Cisco UCS Manager Plugin talks to the Cisco UCS Manager application running on Fabric Interconnect and is part of an ecosystem for Cisco UCS Servers that consists of Fabric Interconnects and IO modules. The ML2 Cisco UCS Manager driver does not support configuration of Cisco UCS Servers, whose service profiles are attached to Service Templates. This is to prevent that same VLAN configuration to be pushed to all the service profiles based on that template. The plugin can be used after the Service Profile has been unbound from the template.

Cisco Nexus 1000V OpenStack offers rich features, which are not limited to the following:

<span>·</span>

Layer2/Layer3 Switching

<span>·</span>

East-West Security

<span>·</span>

Policy Framework

<span>·</span>

Application Visibility

All the monitoring, management and functionality features offered on the Nexus 1000V are consentient with the physical Nexus infrastructure. This enables customer to reuse the existing tool chains to manage the new virtual networking infrastructure as well. Along with this, customer can also have the peace of mind that the feature functionality they enjoyed in the physical network will now be the same in the virtual network.

**Figure 8**Cisco Plugin Integration with OpenStack

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_11.jpg)

To create a virtual machine, complete the following steps:

1.Dashboard/CLI authenticates with Keystone.

2.Dashboard/CLI sends nova-boot to nova-api.

3.nova-api validates the token with keystone.

4.nova-api checks for conflicts, if not creates a new entry in database.

5.nova-api sends rpc.call to nova-scheduler and gets updated host-entry with host-id.

6.nova-scheduler picks up the request from the queue.

7.nova-scheduler sends the rpc.cast request to nova-compute for launching an instance on the appropriate host after applying filters.

8.nova-compute picks up the request from the queue.

9.nova-compute sends the rpc.call request to nova-conductor to fetch the instance information such as host ID and flavor (RAM, CPU, and Disk).

10.nova-conductor picks up the request from the queue.

11.nova-conductor interacts with nova-database and picks up instance information from queue.

12.nova-compute performs the REST with auth-token to glance-api. Then, nova-compute retrieves the Image URI from the Image Service, and loads the image from the image storage.

13.glance-api validates the auth-token with keystone and nova-compute gets the image data.

14.nova-compute performs the REST call to network API to allocated and configure the network

15.neutron server validates the token and creates network info.

16.Nova-compute performs REST to volume API to attach volume to the instance.

17.Cinder-api validates the token and provides block storage info to nova-compute.

18.Nova compute generates data for the hypervisor driver.

19.DHCP and/or Router port bindings by neutron on controller nodes triggers Cisco ML2 plugins:

<span>—</span>

UCSM driver creates VLAN and trunks the eth1 vNICs for the controller node's service-profile

<span>—</span>

Nexus driver creates VLAN and trunks the switches port/s mapped to the controller node

<span>—</span>

N1KV VSM receives the logical port information from Neutron. DHCP or Router agents create the port on the N1KV VEM bridge

20.Virtual Machine's Instance's Port bindings to a Compute Node triggers again ML2:

<span>—</span>

UCSM driver creates VLAN and trunks the eth1 vNICs for the compute node's service-profile

<span>—</span>

Nexus driver creates VLAN and trunks the switches' port(s) mapped to the compute node

<span>—</span>

VSM receives logical port information from Neutron. Nova agent creates the port on the N1KV VEM bridge on the compute node

**Figure 9**Instance Creation Workflow

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_12.jpg)

This section details the deployment hardware used in this solution.

Table 4 lists the cabling information.

[<span>Table 4  </span>

Cabling Details]()

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_13.png)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_14.png)

0 illustrates the physical cabling used in this solution.

<span>
  <br>
</span>

 **Figure 10**Physical Cabling

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_15.jpg)

Figure 11 illustrates the cabling logic used in this solution.

[**Figure 11**Cabling Logic]()

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_16.jpg)

Configure the Fabric Interconnects after the cabling is complete. To hook up the console port on the Fabrics, complete the following steps:

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Please replace the appropriate addresses for your setup.

Connect the console port to the UCS 6248 Fabric Interconnect switch designated for Fabric A:

Enter the configuration method: console

Enter the setup mode; setup newly or restore from backup.(setup/restore)? setup

You have chosen to setup a new fabric interconnect? Continue? (y/n): y

Enforce strong passwords? (y/n) [y]: y

Enter the password for "admin": <password>

Enter the same password for "admin": <password>

Is this fabric interconnect part of a cluster (select 'no' for standalone)?

(yes/no) [n]:y

Which switch fabric (A|B): A

Enter the system name: UCS-6248-FAB

Physical switch Mgmt0 IPv4 address: 10.22.100.6

Physical switch Mgmt0 IPv4 netmask: 255.255.255.0

IPv4 address of the default gateway: 10.22.100.1

Cluster IPv4 address: 10.22.100.5

Configure DNS Server IPv4 address? (yes/no) [no]: y

DNS IPv4 address: <<var_nameserver_ip>>

Configure the default domain name? y

Default domain name: <<var_dns_domain_name>>

Join centralized management environment (UCS Central)? (yes/no) [n]: Press Enter

You will be prompted to review the settings.

If they are correct, answer yes to apply and save the configuration. Wait for the login prompt to make sure that the configuration has been saved.

Connect the console port to Peer UCS 6248 Fabric Interconnect switch designated for Fabric B:

Enter the configuration method: console

Installer has detected the presence of a peer Fabric interconnect. This Fabric

interconnect will be added to the cluster. Do you want to continue {y|n}? y

Enter the admin password for the peer fabric interconnect: <password>

Physical switch Mgmt0 IPv4 address: 10.22.100.7

Apply and save the configuration (select "no" if you want to re-enter)? (yes/no): yes

Verify the connectivity:

After completing the FI configuration, verify the connectivity as below by logging to one of the Fabrics or the VIP address and checking the cluster state or extended state as shown below:

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_17.jpg)

To configure the Global policies, log into UCS Manager GUI, and complete the following steps:

1.Under Equipment 

<span>à</span>

 Global Policies;

a.Set the Chassis/FEX Discovery Policy to match the number of uplink ports that are cabled between the chassis or fabric extenders and to the fabric interconnects.

b.Set the Power policy based on the input power supply to the UCS chassis. In general, UCS chassis with 5 or more blades recommends minimum of 3 power supplies with N+1 configuration. With 4 power supplies, 2 on each PDUs the recommended power policy is Grid.

c.Set the Global Power allocation Policy as Policy driven Chassis Group cap.

d.Click Save changes to save the configuration.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_18.jpg)

Navigate to each Fabric Interconnect and configure the server ports on Fabric Interconnects. Complete the following steps:

1.Under Equipment 

<span>à</span>

 Fabric Interconnects 

<span>à</span>

 Fabric Interconnect A 

<span>à</span>

 Fixed Module 

<span>à</span>

 Ethernet Ports;

a.Select the ports (Port 1 to 8) that are connected to the left side of each UCS chassis FEX 2204, right-click them and select Configure as Server Port.

b.Select the ports (Port 9 to 11) that are connected to the 10G MLOM port1 of each UCS C240 M4, right-click them, and select Configure as Server Port.

c.Click Save Changes to save the configuration.

d.Repeat steps 1 and 2 on Fabric Interconnect B and save the configuration.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Configure-ServerPorts.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_19.jpg)

Navigate to each Fabric Interconnects and configure the Network Uplink ports on Fabric Interconnects. Complete the following steps:

1.Under Equipment 

<span>à</span>

 Fabric Interconnects 

<span>à</span>

 Fabric Interconnect A 

<span>à</span>

 Fixed Module 

<span>à</span>

 Ethernet Ports

a.Select the port 17 and Port18 that are connected to Nexus 9k switches, right-click them and select Configure as Uplink Port.

b.Click Save Changes to save the configuration.

c.Repeat the steps 1 and 2 on Fabric Interconnect B.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Configure-Network-Uplink-Ports.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_20.jpg)

To access the KVM console of each UCS Server, create the KVM IP pools from the UCS Manager GUI, and complete the following steps:

1.Under LAN 

<span>à</span>

 Pools 

<span>à</span>

 root 

<span>à</span>

 IP Pools 

<span>à</span>

 IP Pool ext-mgmt 

<span>à</span>

 right-click and select Create Block of IPV4 addresses.

2.Specify the Starting IP address, subnet mask and gateway and size.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_21.png)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_22.jpg)

To configure a MAC address for each UCS Server VNIC interface, create the MAC pools from the UCS Manager GUI, and complete the following steps:

1.Under LAN 

<span>à</span>

 Pools 

<span>à</span>

 root 

<span>à</span>

 MAC Pools 

<span>à</span>

 right-click and select Create MAC Pool.

2.Specify the name and description for the MAC pool.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_23.png)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_24.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_25.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_26.png)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_27.jpg)

To configure the UUID pools for each UCS Server, create the UUID pools from the UCS Manager GUI, complete the following steps:

1.Under Servers 

<span>à</span>

 Pools 

<span>à</span>

 root 

<span>à</span>

 UUID Suffix Pools 

<span>à</span>

 right-click and select Create UUID Suffix Pool.

2.Specify the name and description for the UUID pool.

3.Click Add.

4.Specify the UUID Suffixes and size for the UUID pool.

5.Click Finish to complete the UUID pool creation.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\UUID-Pools1.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_28.jpg)

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\UUID-Pools2.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_29.jpg)

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\UUID-Pools3.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_30.jpg)

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\UUID-Pools4.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_31.jpg)

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\UUID-Pools5.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_32.jpg)

To configure Server pools for Controller, Compute and Ceph Storage Servers, create Server pools from the UCS Manager GUI, and complete the following steps:

1.Under Servers 

<span>à</span>

 Pools 

<span>à</span>

 root 

<span>à</span>

 Server Pools 

<span>à</span>

 right-click and select Create Server Pool.

2.Specify the name and description for the Server pool for Compute Nodes.

3.Similarly, create Server pools for Controller and Ceph Storage Nodes.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServerPools1.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_33.jpg)

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServerPools4.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_34.jpg)

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServerPools5.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_35.jpg)

To create VLANs for all OpenStack networks for Controller, Compute and Ceph Storage Servers, from the UCS manager GUI, complete the following steps:

1.Under LAN 

<span>à</span>

 LAN Cloud 

<span>à</span>

 VLANs 

<span>à</span>

 right-click and select Create VLANs.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_36.png)

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\VLAN-creation3.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_37.jpg)

2.Specify the VLAN name as PXE-Network for Provisioning and specify the VLAN ID as 110 and click OK.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\VLAN-creation2.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_38.jpg)

3.Specify the VLAN name as Storage-Public for accessing Ceph Storage Public Network and specify the VLAN ID as 120 and click OK.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\VLAN-creation4.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_39.jpg)

4.Specify the VLAN name as Tenant-Internal-Network and specify the VLAN ID as 130 and click OK.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\VLAN-creation5.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_40.jpg)

5.Specify the VLAN name as Storage-Mgmt-Network for Managing Ceph Storage Cluster and specify the VLAN ID as 130 and click OK.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\VLAN-creation6.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_41.jpg)

6.Specify the VLAN name as Tenant-Floating-Network for accessing Tenant externally and specify the VLAN ID as 160 and click OK.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\VLAN-creation8.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_42.jpg)

7.Specify the VLAN name as Tenant-Floating-Network for accessing Tenant externally and specify the VLAN ID as 160 and click OK.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

This network is Optional. In this solution, we only used a 24 bit netmask for the External network that had a limitation of 250 IPs for tenant VMs. Due to this limitation, we used a 20 bit netmask for the Tenant Floating Network.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\VLAN-creation7.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_43.jpg)

The screenshot below shows the output of VLANs for all the OpenStack Networks created above.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\VLAN-creation9.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_44.jpg)

To Configure VNIC templates for each UCS Server VNIC interfaces, create VNIC templates from the UCS Manager GUI, complete the following steps:

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

The storage management network is configured with 9000 MTU.

1.Under LAN 

<span>à</span>

 Policies 

<span>à</span>

 root 

<span>à</span>

 VNIC Templates 

<span>à</span>

 right-click and select Create VNIC Template.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\VNIC-Template-Creation1.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_45.jpg)

a.Create VNIC template for Internal-API network. Specify the name, description, Fabric ID, VLAN ID and choose MAC pools from the drop-down list.

![Description: cid:402CC6B1-E5E8-4090-BDEA-75ED536A11C7@cisco.com](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_46.png)

b.Create VNIC template for External Network.

![Description: C:\projects\OpenStack\osp7\Deployment Guide\Pictures\vnic template diagrams from Vijay\VNIC-Template-Creation7.jpg](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_47.jpg)

c.Create the VNIC template for Storage Public Network.

![Description: C:\projects\OpenStack\osp7\Deployment Guide\Pictures\vnic template diagrams from Vijay\VNIC-Template-Creation4.jpg](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_48.jpg)

d.Create VNIC template for Storage Mgmt Cluster network.

![Description: C:\projects\OpenStack\osp7\Deployment Guide\Pictures\vnic template diagrams from Vijay\VNIC-Template-Creation5.jpg](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_49.jpg)

e.Create VNIC template for Tenant Internal Network.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\VNIC-Template-Creation9.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_50.jpg)

f.Create VNIC template for Tenant Floating Network.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\VNIC-Template-Creation6.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_51.jpg)

After completion, you can see the VNIC templates for each traffic.

![Description: cid:BC08EE37-FD1D-43D5-8938-3A96AF332085@cisco.com](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_52.png)

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

For storage interfaces, a MTU value of 9000 has been added.

To configure the Boot policy for the UCS Servers, create a Boot Policy from the UCS Manager GUI, and complete the following steps:

1.Under Server 

<span>à</span>

 Policies 

<span>à</span>

 root 

<span>à</span>

 Boot Policies 

<span>à</span>

 right-click and select Create Boot Policy.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Boot-Policy1.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_53.jpg)

a.Specify the name and description. Select the First boot order as LAN boot and specify the actual VNIC name of the PXE network (PXE-NIC). Then select the second boot order and click Add Local LUN.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Boot-Policy2.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_54.jpg)

b.Specify the VNIC Name as PXE-NIC.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Boot-Policy3.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_55.jpg)

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Boot-Policy4.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_56.jpg)

c.Make sure the First boot order is PXE NIC and second boot order is Local LUN and click OK.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Boot-Policy5.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_57.jpg)

A maintenance policy determines a pre-defined action to take when there is a disruptive change made to the service profile associated with a server. When creating a maintenance policy you have to select a reboot policy which defines when the server can reboot once the changes are applied.

To configure the Maintenance policy from the UCS Manager, complete the following steps:

1.Under Server 

<span>à</span>

 Policies 

<span>à</span>

 root 

<span>à</span>

 Maintenance Policies 

<span>à</span>

 right-click and select Create Maintenance Policy.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\MaintenanacePolicy1.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_58.jpg)

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\MaintenanacePolicy2.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_59.jpg)

This policy allows you to determine whether IPMI commands can be sent directly to the server, using the IP address (KVM IP address).

To configure the IPMI Access profiles from the UCS Manager, complete the following steps:

1.Under Server 

<span>à</span>

 Policies 

<span>à</span>

 root 

<span>à</span>

 IPMI Access profiles 

<span>à</span>

 right-click and select Create IPMI Access Profile.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\IPMI-Access-Policy1.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_60.jpg)

a.Specify the name and click IPMI over LAN as Enabled and click "+".

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\IPMI-Access-Policy2.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_61.jpg)

b.Specify the username and password. Choose Admin for the Ro
le and click OK.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\IPMI-Access-Policy3.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_62.jpg)

c.Click OK to create the IPMI access profile.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\IPMI-Access-Policy4.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_63.jpg)

Cisco UCS uses the priority set in the power control policy, along with the blade type and configuration, to calculate the initial power allocation for each blade within a chassis. During normal operation, the active blades within a chassis can borrow power from idle blades within the same chassis. If all blades are active and reach the power cap, service profiles with higher priority power control policies take precedence over service profiles with lower priority power control policies.

To configure the Power Control policy from the UCS Manager, complete the following steps:

1.Under Server 

<span>à</span>

 Policies 

<span>à</span>

 root 

<span>à</span>

 Power Control Policies 

<span>à</span>

 right-click and select Create Power Control Policy.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_64.png)

a.Specify the name and description. Choose Power Capping as No Cap.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

No Cap keeps the server runs at full capacity regardless of the power requirements of the other servers in its power group. Setting the priority to no-cap prevents Cisco UCS from leveraging unused power from that particular blade server. The server is allocated the maximum amount of power that that blade can reach.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_65.png)

To configure the Network Control policy from the UCS Manager, complete the following steps:

1.Under LAN 

<span>à</span>

 Policies 

<span>à</span>

 root 

<span>à</span>

 Network Control Policies 

<span>à</span>

 right-click and select Create Network Control Policy.

a.Specify the name and choose CDP as Enabled. Select the MAC register mode as "All hosts VLANs" and Action on Uplink fail as "Link Down" and click OK.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_66.png)

Create a QOS system class as shown below:

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_67.jpg)

Select the Best Effort class as MTU 9000, which will be leveraged in vNIC templates for storage public and storage management vNIC's.

To allow flexibility in defining the number of storage disks, roles and usage of these disks, and other storage parameters, you can create and use storage profiles. LUNs configured in a storage profile can be used as boot LUNs or data LUNs, and can be dedicated to a specific server. You can also specify a local LUN as a boot device. However, LUN resizing is not supported.

To configure Storage profiles from the UCS Manager, complete the following steps:

1.Under Storage 

<span>à</span>

 Storage Provisioning 

<span>à</span>

 Storage Profiles 

<span>à</span>

 root 

<span>à</span>

 right-click and select Create Storage Profile.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Storage-Profile-Blade-Creation1.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_68.jpg)

a.Specify the name and click "+".

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Storage-Profile-Blade-Creation2.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_69.jpg)

b.Specify the Local LUN name and size as 250 in GBand click Auto Deploy.

c.To configure RAID levels and configure the number of disks for the disk group, select Create Disk Group Policy.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Storage-Profile-Blade-Creation3.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_70.jpg)

d.Specify the name and choose RAID level as RAID 1 Mirrored. RAID1 is recommended for the Local boot LUNs.

e.Select Disk group Configuration (Manual) and click "+". Keep the Virtual Drive configuration with the default values.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Storage-Profile-Blade-Creation4.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_71.jpg)

f.Specify Disk Slot Number as 1 and Role as Normal.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Storage-Profile-Blade-Creation5.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_72.jpg)

g.Create another Local Disk configuration with the Slot number as 2 and click OK.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

In this solution, we used Local Disk 1 and Disk 2 as the boot LUNs with RAID 1 mirror configuration.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Storage-Profile-Blade-Creation6.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_73.jpg)

h.Choose the Disk group policy Boot Disk-OS for the Local Boot LUN.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Storage-Profile-Blade-Creation7.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_74.jpg)

i.Click OK to confirm the Storage profile creation.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Storage-Profile-Blade-Creation8.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_75.jpg)

To configure the Storage profiles from the UCS Manager, complete the following steps:

1.Under Storage 

<span>à</span>

 Storage Provisioning 

<span>à</span>

 Storage Profiles 

<span>à</span>

 root 

<span>à</span>

 right-click and select Create Storage Profile.

a.Specify the Storage profile name as C240-Ceph for the Ceph Storage Servers. Click "

<span>+</span>

".

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Storage-Profile-Rack-Creation1.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_76.jpg)

b.Specify the LUN name and size in GB. For the Disk group policy creation for the UCS blades, Select Disk Group Configuration for Ceph nodes as Ceph-OS-Boot. After successful creation, choose Disk Group Configuration as Ceph-OS-Boot and click OK.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Storage-Profile-Rack-Creation2.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_77.jpg)

c.Click OK to complete the Storage Profile creation for the Ceph Nodes.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Storage-Profile-Rack-Creation3.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_78.jpg)

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

For the C240 M4 servers, the LUN creation for Ceph OSD disks (6TB SAS) and Ceph Journal disks (400GB SSDs) still remains on the Ceph Storage profile. Due to the UCS manager limitations, we have to create OSD LUNs and Journal LUNs after the C240 M4 server has been successfully associated with the Ceph Storage Service profiles[]()

<span>.</span>

To configure the Service Profile Templates for the Controller Nodes, complete the following steps:

1.Under Servers 

<span>à</span>

 Service Profile Templates 

<span>à</span>

 root 

<span>à</span>

 right-click and select Create Service Profile Template.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation1.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_79.jpg)

a.Specify the Service profile template name for the Controller node as OSP-Controller-SP-Template. Choose the UUID pools previously created from the drop-down list and click Next.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation2.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_80.jpg)

b.For Storage Provisioning, choose Expert and click Storage profile Policy and choose the Storage profile Blade-OS-boot previously created from the drop-down list and click Next.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation3.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_81.jpg)

c.For Networking, choose Expert and click "+".

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation4.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_82.jpg)

d.Create the VNIC interface for PXE or Provisioning network as PXE-NIC and click the check box Use VNIC template.

e.Under vNIC template, choose the PXE-NIC template previously created from the drop-down list and choose Linux for the Adapter Policy.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation5.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_83.jpg)

f.Create the VNIC interface for Tenant Internal Network as eth1 and then under vNIC template, choose the "Tenant-Internal" template we created before from the drop down list and choose Adapter Policy as "Linux".

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Include-Eth1-COntroller-Compute-SP-Template_Creation.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_84.jpg)

g.Create the VNIC interface for Internal API network as Internal-API and click the check box for Use VNIC template.

h.Under vNIC template, choose the Internal-API-NIC template previously created from the drop-down list and choose Linux for the Adapter Policy.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_85.png)

i.Create the VNIC interface for Storage Public Network as Storage-Pub and click the check box for Use VNIC template.

j.Under vNIC template, choose the Storage-Pub-NIC template previously created from the drop-down list and choose Linux for the Adapter Policy.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation8.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_86.jpg)

k.Create the VNIC interface for Storage Mgmt Cluster Network as Storage-Mgmt and click the check box for Use VNIC template.

l.Under vNIC template, choose the Storage-Mgmt-NIC template previously created from the drop-down list and choose Linux for the Adapter Policy.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation9.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_87.jpg)

m.Create the VNIC interface for Floating Network as Tenant-Floating and click the check box the Use VNIC template.

n.Under the vNIC template, choose the Tenant-Floating template previously created from the drop-down list and choose Linux for the Adapter Policy.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation10.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_88.jpg)

o.Create the VNIC interface for External Network as External-NIC and click the check box the Use VNIC template.

p.Under the vNIC template, choose the External-NIC template previously created from the drop-down list and choose Linux for the Adapter Policy.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation11.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_89.jpg)

q.After a successful VNIC creation, click Next.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_90.png)

r.Under the SAN connectivity, choose No VHBAs and click Next.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation13.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_91.jpg)

s.Under Zoning, click Next.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation14.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_92.jpg)

t.Under VNIC/VHBA Placement, choose the vNICs PCI order as shown below and click Next.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_93.png)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_94.png)

u.Under vMedia Policy, click Next.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation16.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_95.jpg)

v.Under Server Boot Order, choose the boot policy as PXE-LocalBoot previously created, from the drop-down list and click Next.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation17.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_96.jpg)

w.Under Maintenance Policy, choose Server_Ack previously created, from the drop-down list and click Next.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation18.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_97.jpg)

x.Under Server Assignment, choose the Pool Assignment as OSP-Controller-Server-Pools previously created, from the drop-down list and click Next.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation19.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_98.jpg)

y.Under Operational Policies, choose the IPMI Access Profile as IPMI_admin previously created, from the drop-down list and choose the Power Control Policy as No_Power_Cap and click Finish.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation20.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_99.jpg)

To create the Service Profile templates for the Compute nodes, complete the following steps:

1.Specify the Service profile template name for the Controller node as OSP-Compute-SP-Template.

2.Choose the UUID pools previously created from the drop-down list and click Next.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation21.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_100.jpg)

3.For Storage Provisioning, choose Expert and click Storage Profile Policy and choose the Storage profile Blade-OS-boot previously created, from the drop-down list and click Next.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation22.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_101.jpg)

4.For Networking, choose Expert and click "+".

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation4.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_102.jpg)

5.Create the VNIC interface for PXE or Provisioning network as PXE-NIC and click the check box for Use VNIC template.

6.Under the vNIC template, choose the PXE-NIC template previously created, from the drop-down list and choose Linux for the Adapter Policy.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation5.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_103.jpg)

7.Create the VNIC interface for Tenant Internal Network as eth1 and then under vNIC template, choose the "Tenant-Internal" template we created before from the drop down list and choose Adapter Policy as "Linux".

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Due to the Cisco UCS Manager Plugin limitations, we have created eth1 as VNIC for Tenant Internal Network..

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Include-Eth1-COntroller-Compute-SP-Template_Creation.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_84.jpg)

8.Create the VNIC interface for Internal API network as Internal-API and click the check box for VNIC template.

9.Under the vNIC template, choose the Internal-API template previously created, from the drop-down list and choose Linux for the Adapter Policy.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_85.png)

10.Create the VNIC interface for Storage Public Network as Storage-Pub and click the check box for Use VNIC template.

11.Under the vNIC template, choose the Storage-Pub-NIC template previously created, from the drop-down list and choose Linux for the Adapter Policy.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation8.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_104.jpg)

12.After a successful VNIC creation, click Next.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_105.png)

13.Under SAN connectivity, choose No VHBAs and click Next.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation13.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_106.jpg)

14.Under Zoning, click Next.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation14.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_107.jpg)

15.Under VNIC/VHBA Placement, choose the vNICs PCI order as shown below and click Next.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_108.png)

16.Under vMedia Policy, click Next.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation16.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_109.jpg)

17.Under Server Boot Order, choose boot policy as PXE-LocalBoot we created from the drop-down list and click Next.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation17.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_110.jpg)

18.Under Maintenance Policy, choose Server_Ack previously created, from the drop-down list and click Next.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation18.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_111.jpg)

19.Under Server assignment, choose Pool Assignment as OSP-Compute-Server-Pools previously, created from the drop-down list and click Next.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation26.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_112.jpg)

20.Under Operational Policies, choose the IPMI Access Profile as IPMI_admin previously created, from the drop-down list and choose the Power Control Policy as No_Power_Cap and click Finish.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation20.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_113.jpg)

To create the Service Profile templates for the Ceph Storage nodes, complete the following steps:

1.Specify the Service profile template name for the Ceph storage node as OSP-Ceph-Storage-SP-Template. Choose the UUID pools previously created, from the drop-down list and click Next.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation21.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_114.jpg)

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation22.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_115.jpg)

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation12 - Copy.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_116.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_117.png)

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation25.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_118.jpg)

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\ServiceProfile-Template-Creation26.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_119.jpg)

2.Click on Next and then Choose "Server_Ack" under Maintenance Policy and then choose the "OSP-CephStorage-Server-Pools" under Pool assignment . Then select "No-power-cap" under power control policy. Click on Finish to complete the Service profile template creation for Ceph nodes.

To create Service profiles for Controller nodes, complete the following steps:

1.Under Servers 

<span>à</span>

 Service Profile Templates 

<span>à</span>

 root 

<span>à</span>

 select the Controller Service profile template and click Create Service Profiles from Templates.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Controller-ServiceProfile-Creation1.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_120.jpg)

a.Specify the Service profile name and the number of instances as 3 for the Controller nodes.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Controller-ServiceProfile-Creation2.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_121.jpg)

b.Make sure the Service profiles for the Controller nodes have been created.

c.Under Servers 

<span>à</span>

 Service profiles 

<span>à</span>

 root .

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Controller-ServiceProfile-Creation3.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_122.jpg)

To create Service profiles for Compute nodes, complete the following steps:

1.Under Servers 

<span>à</span>

 Service Profile Templates 

<span>à</span>

 root 

<span>à</span>

 select the Compute Service profile template and click Create Service Profiles from Templates.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Compute-ServiceProfile-Creation1.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_123.jpg)

a.Specify the profile name and set the number of instances to 6 for compute nodes.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Compute-ServiceProfile-Creation2.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_124.jpg)

b.Make sure the Service profiles for the Compute nodes have been created.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Compute-ServiceProfile-Creation3.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_125.jpg)

To create Service profiles for Ceph Storage nodes, complete the following steps:

1.Under Servers 

<span>à</span>

 Service Profile Templates 

<span>à</span>

 root 

<span>à</span>

 select the Ceph Storage Service profile template and click Create Service Profiles from Templates.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\CephStorage-ServiceProfile-Creation1.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_126.jpg)

a.Specify the Service profile name and set the number of instances to 3 for the Ceph Storage nodes.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\CephStorage-ServiceProfile-Creation2.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_127.jpg)

b.Make sure the Service profiles for the Ceph Storage nodes have been created.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\CephStorage-ServiceProfile-Creation3.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_128.jpg)

c.Verify the Service profile association with the respective UCS Servers.

After a successful CephStorage Server association, create the remaining LUNs for the Ceph OSD disks and Journal disks.

To create the Ceph Journal LUNs, complete the following steps:

1.Under Storage 

<span>à</span>

Storage Provisioning 

<span>à</span>

 root 

<span>à</span>

 select the previously created Ceph Sotrage profile C240-Ceph 

<span>à</span>

 click Local LUNs 

<span>à</span>

 click Create Local LUN.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Storage-Profile-Rack-Creation-Post1.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_129.jpg)

a.Specify the name as JournalLUN1 and set the size in GB to 350 for the 400GB SSD disks and click Create Disk Group Policy.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Storage-Profile-Rack-Creation-Post2.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_130.jpg)

b.Specify the Disk group policy name and choose the RAID level as RAID 0 and select Disk Group Configuration (Manual).

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Storage-Profile-Rack-Creation-Post3.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_131.jpg)

c.Specify the Slot ID as 3, which is the physical disk slot number for 400GB SSDs for the Journal LUN1 and click OK.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Storage-Profile-Rack-Creation-Post4.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_132.jpg)

d.Click OK to confirm the Disk group policy creation.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Storage-Profile-Rack-Creation-Post5.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_133.jpg)

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Storage-Profile-Rack-Creation-Post6.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_134.jpg)

e.From the drop-down list, choose the Disk group policy for the Journal LUN as JournalDG1.

f.Create the Local LUN as JournlLUN2 with Disk group policy as JournalDG2 using 400GB SSD on Disk Slot4.

To create the Ceph OSD LUN, complete the following steps:

1.Under Storage 

<span>à</span>

Storage Provisioning 

<span>à</span>

 root 

<span>à</span>

 select the previously created Ceph Storage profile C240-Ceph 

<span>à</span>

 click Local LUNs 

<span>à</span>

 click Create Local LUN.

a.Specify the name as OSDLUN1 and the size in GB as 5500 for the 6TB SAS disks and click Create Disk Group Policy.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Storage-Profile-Rack-Creation-Post9.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_135.jpg)

b.Specify Disk group policy name and Choose RAID level as RAID 0 and select Disk Group Configuration(Manual)

c.Click "+" and Specify Slot ID as 5, which is physical disk slot number for 6TB SAS disks for Ceph OSD LUN1 and click OK.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Storage-Profile-Rack-Creation-Post10.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_136.jpg)

d.From the drop-down list, choose the Disk group policy for OSDLUN1 as OSD-DG1.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Storage-Profile-Rack-Creation-Post11.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_137.jpg)

e.Create the remaining OSDLUN3, 4, 5, 6, 7, 8 with the Disk group policy using 6TB SAS disks 7, 8, 9, 10, 11, and 12\.

f.Make sure the LUNs for Journals and OSDs are created as shown below.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Storage-Profile-Rack-Creation-Post14.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_138.jpg)

g.Make sure all the Ceph Storage Servers have the identical LUN ID and Device ID for all the LUNs (OS-boot, Journal and OSD) as shown in the table below:

**Physical Disk Slot**

**Disk Type**

**Disk Size**

**RAID Level**

**LUN Size**

**LUN ID**

**Device ID**

**Disk 1**

SAS

300 GB

RAID 1

250 GB

1000

**Disk 2**

SAS

300 GB

**Disk 3**

SSD

400 GB

RAID 0

350 GB

1001

**Disk 4**

SSD

400 GB

RAID 0

350 GB

1002

**Disk 5**

SAS

6 TB

RAID 0

5500 GB

1003

**Disk 6**

SAS

6 TB

RAID 0

5500 GB

1004

**Disk 7**

SAS

6 TB

RAID 0

5500 GB

1005

**Disk 8**

SAS

6 TB

RAID 0

5500 GB

1006

**Disk 9**

SAS

6 TB

RAID 0

5500 GB

1007

**Disk 10**

SAS

6 TB

RAID 0

5500 GB

1008

**Disk 11**

SAS

6 TB

RAID 0

5500 GB

1009

**Disk 12**

SAS

6 TB

RAID 0

5500 GB

1010

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screenshots\UCSM\Storage-Profile-Rack-Creation-Post15.JPG](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_139.jpg)

To create the Port Channels, complete the following steps as shown in the screenshots below. Figure 12 illustrates the configuration.

<span>
  <br>
</span>

 [**Figure 12**Nexus vPC Configuration]()

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_140.jpg)

To create Port Channels from the UCS Manager GUI, complete the following steps:

1.Under LAN 

<span>à</span>

 LAN Cloud 

<span>à</span>

 Fabric A 

<span>à</span>

 Port Channels 

<span>à</span>

 right-click and select Create Port Channel.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_141.png)

a.Specify the ID and name for the port channel and click Next.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_142.jpg)

b.Select the ports 17 and 18 from left pane and move to the right pane into Ports in the Port Channel and click Finish.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_143.jpg)

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Repeat the steps shown above on Fabric B with Port-Channel as18.

To configure the Cisco Nexus 9372 PX Switch A, complete the following step:

1.Connect the console port to the Nexus 9372 PX switch designated for Fabric A:

<span>—- Basic System Configuration Dialog VDC: 1 —-</span>

<span>This setup utility will guide you through the basic configuration of the system. Setup configures only enough connectivity for management of the system.</span>

<span>*Note: setup is mainly used for configuring the system initially, when no configuration is present. So setup always assumes system defaults and not the current system configuration values.</span>

<span>Press Enter at anytime to skip a dialog. Use ctrl-c at anytime to skip the remaining dialogs.</span>

<span>Would you like to enter the basic configuration dialog (yes/no): yes</span>

<span>Do you want to enforce secure password standard (yes/no) [y]: </span>

<span>Create another login account (yes/no) [n]: </span>

<span>Configure read-only SNMP community string (yes/no) [n]:</span>

<span>Configure read-write SNMP community string (yes/no) [n]:</span>

<span>Enter the switch name : N9k-FAB-A</span>

<span>Continue with Out-of-band (mgmt0) management configuration? (yes/no) [y]:</span>

<span>             Mgmt0 IPv4 address : 10.22.100.3</span>

<span> Mgmt0 IPv4 netmask : 255.255.255.0</span>

<span>            Configure the default gateway? (yes/no) [y]:</span>

<span>            IPv4 address of the default gateway : 10.22.100.1</span>

<span>            Configure advanced IP options? (yes/no) [n]:</span>

<span>            Enable the telnet service? (yes/no) [n]:</span>

<span>            Enable the ssh service? (yes/no) [y]:</span>

<span>            Type of ssh key you would like to generate (dsa/rsa) [rsa]:</span>

<span>            Number of rsa key bits &lt;1024-2048&gt; [2048]:</span>

<span>            Configure the ntp server? (yes/no) [n]: y</span>

<span>            NTP server IPv4 address : &lt;&lt;ntp_server_ip&gt;&gt;</span>

<span>            Configure CoPP system profile (strict/moderate/lenient/dense/skip) [strict]:</span>

<span>The following configuration will be applied:</span>

<span>   password strength-check</span>

<span>   switchname N9k-FAB-A </span>

<span>   vrf context management</span>

<span>   ip route 0.0.0.0/0 10.22.100.1</span>

<span>   exit</span>

<span>   no feature telnet</span>

<span>   ssh key rsa 2048 force</span>

<span>    feature ssh</span>

<span>    ntp server &lt;&lt;var_global_ntp_server_ip&gt;&gt;</span>

<span>    copp profile strict</span>

<span>     interface mgmt0</span>

<span>     ip address 10.22.100.3 255.255.255.0</span>

<span>     no shutdown</span>

<span>       Would you like to edit the configuration? (yes/no) [n]: Enter</span>

<span>       Use this configuration and save it? (yes/no) [y]: Enter</span>

<span>       [########################################] 100%</span>

<span>       Copy complete.</span>

To configure the Cisco Nexus 9372 PX Switch B, complete the following step:

1.Connect the console port to the Nexus 9372 PX switch designated for Fabric B:

<span>—- Basic System Configuration Dialog VDC: 1 —-</span>

<span>This setup utility will guide you through the basic configuration of the system. Setup configures only enough connectivity for management of the system.</span>

<span>*Note: setup is mainly used for configuring the system initially, when no configuration is present. So setup always assumes system defaults and not the current system configuration values.</span>

<span>Press Enter at anytime to skip a dialog. Use ctrl-c at anytime to skip the remaining dialogs.</span>

<span>Would you like to enter the basic configuration dialog (yes/no): yes</span>

<span>Do you want to enforce secure password standard (yes/no) [y]: </span>

<span>Create another login account (yes/no) [n]: </span>

<span>Configure read-only SNMP community string (yes/no) [n]:</span>

<span>Configure read-write SNMP community string (yes/no) [n]:</span>

<span>Enter the switch name : N9k-FAB-B</span>

<span>Continue with Out-of-band (mgmt0) management configuration? (yes/no) [y]:</span>

<span>             Mgmt0 IPv4 address : 10.22.100.4</span>

<span> Mgmt0 IPv4 netmask : 255.255.255.0</span>

<span>            Configure the default gateway? (yes/no) [y]:</span>

<span>            IPv4 address of the default gateway : 10.22.100.1</span>

<span>            Configure advanced IP options? (yes/no) [n]:</span>

<span>            Enable the telnet service? (yes/no) [n]:</span>

<span>            Enable the ssh service? (yes/no) [y]:</span>

<span>            Type of ssh key you would like to generate (dsa/rsa) [rsa]:</span>

<span>            Number of rsa key bits &lt;1024-2048&gt; [2048]:</span>

<span>            Configure the ntp server? (yes/no) [n]: y</span>

<span>            NTP server IPv4 address : &lt;&lt;ntp_server_ip&gt;&gt;</span>

<span>            Configure CoPP system profile (strict/moderate/lenient/dense/skip) [strict]:</span>

<span>The following configuration will be applied:</span>

<span>   password strength-check</span>

<span>   switchname N9k-FAB-B</span>

<span>   vrf context management</span>

<span>   ip route 0.0.0.0/0 10.22.100.1</span>

<span>   exit</span>

<span>   no feature telnet</span>

<span>   ssh key rsa 2048 force</span>

<span>    feature ssh</span>

<span>    ntp server &lt;&lt;var_global_ntp_server_ip&gt;&gt;</span>

<span>    copp profile strict</span>

<span>     interface mgmt0</span>

<span>     ip address 10.22.100.4 255.255.255.0</span>

<span>     no shutdown</span>

<span>       Would you like to edit the configuration? (yes/no) [n]: Enter</span>

<span>       Use this configuration and save it? (yes/no) [y]: Enter</span>

<span>       [########################################] 100%</span>

<span>       Copy complete.</span>

To enable the features on the switch, enter the following:

<span>N9K-FAB-A# config terminal</span>

<span>N9k-FAB-A(config)# feature udld</span>

<span>N9K-FAB-A(config)# feature interface-vlan</span>

<span>N9K-FAB-A(config)# feature hsrp</span>

<span>N9K-FAB-A(config)# feature lacp</span>

<span>N9K-FAB-A(config)# feature vpc</span>

<span>N9K-FAB-A(config)# exit</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Repeat the same steps on Nexus 9372 Switch B.

To enable the Jumbo MTU, enter the following:

<span>N9K-FAB-A# config terminal</span>

<span>N9K-FAB-A(config)# system jumbomtu 9216</span>

<span>N9K-FAB-A(config)# exit</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Repeat the same steps on Nexus 9372 Switch B.

To create VLANs, enter the following:

<span>N9K-FAB-A# config terminal</span>

<span>N9K-FAB-A(config)# vlan 100</span>

<span>N9K-FAB-A(config-vlan)# name Internal API</span>

<span>N9K-FAB-A(config-vlan)# no shut</span>

<span>N9K-FAB-A(config-vlan)# exit</span>

<span>N9K-FAB-A(config)# vlan 110</span>

<span>N9K-FAB-A(config-vlan)# name PXE-Network</span>

<span>N9K-FAB-A(config-vlan)# no shut</span>

<span>N9K-FAB-A(config-vlan)# exit</span>

<span>N9K-FAB-A(config)# vlan 120</span>

<span>N9K-FAB-A(config-vlan)# name Storage-Public-Network</span>

<span>N9K-FAB-A(config-vlan)# no shut</span>

<span>N9K-FAB-A(config-vlan)# exit</span>

<span>N9k-FAB-A(config)# vlan 130</span>

<span>N9k-FAB-A(config-vlan)# name Tenant-Internal-Network</span>

<span>N9k-FAB-A(config-vlan)# no shut</span>

<span>N9k-FAB-A(config-vlan)# exit</span>

<span>N9K-FAB-A(config)# vlan 150</span>

<span>N9K-FAB-A(config-vlan)# name Storage-Mgmt-Network</span>

<span>N9K-FAB-A(config-vlan)# no shut</span>

<span>N9K-FAB-A(config-vlan)# exit</span>

<span>N9K-FAB-A(config)# vlan 160</span>

<span>N9K-FAB-A(config-vlan)# name Tenant-Floating-IP-Network</span>

<span>N9K-FAB-A(config-vlan)# no shut</span>

<span>N9K-FAB-A(config-vlan)# exit</span>

<span>N9K-FAB-A(config)# vlan 215</span>

<span>N9K-FAB-A(config-vlan)# name External-Network</span>

<span>N9K-FAB-A(config-vlan)# no shut</span>

<span>N9K-FAB-A(config-vlan)# exit</span>

<span>N9K-FAB-A(config)#</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Repeat the same steps on Nexus 9372 Switch B.

To configure the Interface VLAN on the Cisco Nexus 9K Switch A, enter the following:

<span>N9K-FAB-A(config)# </span>

<span>N9K-FAB-A(config)# interface Vlan100</span>

<span>N9K-FAB-A(config-if)# description Internal-API</span>

<span>N9K-FAB-A(config-if)# no shutdown</span>

<span>N9K-FAB-A(config-if)# no ip redirects</span>

<span>N9K-FAB-A(config-if)# ip address 10.22.100.253/24</span>

<span>N9K-FAB-A(config-if)# no ipv6 redirects</span>

<span>N9K-FAB-A(config-if)# hsrp version 2</span>

<span>N9K-FAB-A(config-if-hsrp)# hsrp 100</span>

<span>N9K-FAB-A(config-if-hsrp)# preempt</span>

<span>N9K-FAB-A(config-if-hsrp)# priority 110 </span>

<span>N9K-FAB-A(config-if-hsrp)# ip 10.22.100.1</span>

<span>N9K-FAB-A(config-if-hsrp)#exit</span>

<span>N9K-FAB-A(config)# interface Vlan110</span>

<span>N9K-FAB-A(config-if)# description PXE_Network</span>

<span>N9K-FAB-A(config-if)# no shutdown</span>

<span>N9K-FAB-A(config-if)# no ip redirects</span>

<span>N9K-FAB-A(config-if)# ip address 10.22.110.253/24</span>

<span>N9K-FAB-A(config-if)# no ipv6 redirects</span>

<span>N9K-FAB-A(config-if)# hsrp version 2</span>

<span>N9K-FAB-A(config-if-hsrp)# hsrp 110</span>

<span>N9K-FAB-A(config-if-hsrp)# preempt</span>

<span>N9K-FAB-A(config-if-hsrp)# priority 110 </span>

<span>N9K-FAB-A(config-if-hsrp)# ip 10.22.110.1</span>

<span>N9K-FAB-A(config-if-hsrp)#exit</span>

<span>N9K-FAB-A(config)# interface Vlan120</span>

<span>N9K-FAB-A(config-if)# description Storage_Public_Network</span>

<span>N9K-FAB-A(config-if)# no shutdown</span>

<span>N9K-FAB-A(config-if)# no ip redirects</span>

<span>N9K-FAB-A(config-if)# ip address 10.22.120.253/24</span>

<span>N9K-FAB-A(config-if)# no ipv6 redirects</span>

<span>N9K-FAB-A(config-if)# hsrp version 2</span>

<span>N9K-FAB-A(config-if-hsrp)# hsrp 120</span>

<span>N9K-FAB-A(config-if-hsrp)# preempt</span>

<span>N9K-FAB-A(config-if-hsrp)# priority 110 </span>

<span>N9K-FAB-A(config-if-hsrp)# ip 10.22.120.1</span>

<span>N9K-FAB-A(config-if-hsrp)#exit</span>

<span>N9K-FAB-A(config)# interface Vlan130</span>

<span>N9K-FAB-A(config-if)# description Tenant_Internal_Network</span>

<span>N9K-FAB-A(config-if)# no shutdown</span>

<span>N9K-FAB-A(config-if)# no ip redirects</span>

<span>N9K-FAB-A(config-if)# ip address 10.22.130.253/24</span>

<span>N9K-FAB-A(config-if)# no ipv6 redirects</span>

<span>N9K-FAB-A(config-if)# hsrp version 2</span>

<span>N9K-FAB-A(config-if-hsrp)# hsrp 130</span>

<span>N9K-FAB-A(config-if-hsrp)# preempt</span>

<span>N9K-FAB-A(config-if-hsrp)# priority 110 </span>

<span>N9K-FAB-A(config-if-hsrp)# ip 10.22.130.1</span>

<span>N9K-FAB-A(config-if-hsrp)#exit</span>

<span>N9K-FAB-A(config)# interface Vlan150</span>

<span>N9K-FAB-A(config-if)# description Storage_ClusterMgmt_Network</span>

<span>N9K-FAB-A(config-if)# no shutdown</span>

<span>N9K-FAB-A(config-if)# no ip redirects</span>

<span>N9K-FAB-A(config-if)# ip address 10.22.150.253/24</span>

<span>N9K-FAB-A(config-if)# no ipv6 redirects</span>

<span>N9K-FAB-A(config-if)# hsrp version 2</span>

<span>N9K-FAB-A(config-if-hsrp)# hsrp 150</span>

<span>N9K-FAB-A(config-if-hsrp)# preempt</span>

<span>N9K-FAB-A(config-if-hsrp)# priority 110 </span>

<span>N9K-FAB-A(config-if-hsrp)# ip 10.22.150.1</span>

<span>N9K-FAB-A(config-if-hsrp)#exit</span>

<span>N9K-FAB-A(config)# interface Vlan160</span>

<span>N9K-FAB-A(config-if)# description Tenanat_Floating_Network</span>

<span>N9K-FAB-A(config-if)# no shutdown</span>

<span>N9K-FAB-A(config-if)# no ip redirects</span>

<span>N9K-FAB-A(config-if)# ip address 10.22.175.253/20</span>

<span>N9K-FAB-A(config-if)# no ipv6 redirects</span>

<span>N9K-FAB-A(config-if)#  hsrp version 2</span>

<span>N9K-FAB-A(config-if-hsrp)# hsrp 160</span>

<span>N9K-FAB-A(config-if-hsrp)# preempt</span>

<span>N9K-FAB-A(config-if-hsrp)# priority 110 </span>

<span>N9K-FAB-A(config-if-hsrp)# ip 10.22.160.1</span>

<span>N9K-FAB-A(config-if-hsrp)#exit</span>

<span>N9K-FAB-A(config)# interface Vlan215</span>

<span>N9K-FAB-A(config-if)# description External_Network</span>

<span>N9K-FAB-A(config-if)# no shutdown</span>

<span>N9K-FAB-A(config-if)# no ip redirects</span>

<span>N9K-FAB-A(config-if)# ip address 172.22.215.253/24</span>

<span>N9K-FAB-A(config-if)# no ipv6 redirects</span>

<span>N9K-FAB-A(config-if)# exit</span>

<span>N9K-FAB-A(config)# Copy running-config Startup-config</span>

To configure the Interface VLAN on the Cisco Nexus 9K Switch B, enter the following:

<span>N9k-FAB-B(config)# </span>

<span>N9k-FAB-B(config)# interface Vlan100</span>

<span>N9k-FAB-B(config-if)# description Internal-API</span>

<span>N9k-FAB-B(config-if)#  no shutdown</span>

<span>N9k-FAB-B(config-if)#  no ip redirects</span>

<span>N9k-FAB-B(config-if)#  ip address 10.22.100.254/24</span>

<span>N9k-FAB-B(config-if)#  no ipv6 redirects</span>

<span>N9k-FAB-B(config-if)# hsrp version 2</span>

<span>N9k-FAB-B(config-if-hsrp)# hsrp 100</span>

<span>N9k-FAB-B(config-if-hsrp)# preempt</span>

<span>N9k-FAB-B(config-if-hsrp)# priority 100 </span>

<span>N9k-FAB-B(config-if-hsrp)# ip 10.22.100.1</span>

<span>N9k-FAB-B(config-if-hsrp)#exit</span>

<span>N9k-FAB-B(config)# interface Vlan110</span>

<span>N9k-FAB-B(config-if)# description PXE_Network</span>

<span>N9k-FAB-B(config-if)#  no shutdown</span>

<span>N9k-FAB-B(config-if)#  no ip redirects</span>

<span>N9k-FAB-B(config-if)#  ip address 10.22.110.254/24</span>

<span>N9k-FAB-B(config-if)#  no ipv6 redirects</span>

<span>N9k-FAB-B(config-if)#  hsrp version 2</span>

<span>N9k-FAB-B(config-if-hsrp)# hsrp 110</span>

<span>N9k-FAB-B(config-if-hsrp)# preempt</span>

<span>N9k-FAB-B(config-if-hsrp)# priority 100 </span>

<span>N9k-FAB-B(config-if-hsrp)# ip 10.22.110.1</span>

<span>N9k-FAB-B(config-if-hsrp)#exit</span>

<span>N9k-FAB-B(config)# interface Vlan120</span>

<span>N9k-FAB-B(config-if)# description Storage_Public_Network</span>

<span>N9k-FAB-B(config-if)# no shutdown</span>

<span>N9k-FAB-B(config-if)# no ip redirects</span>

<span>N9k-FAB-B(config-if)# ip address 10.22.120.254/24</span>

<span>N9k-FAB-B(config-if)# no ipv6 redirects</span>

<span>N9k-FAB-B(config-if)# hsrp version 2</span>

<span>N9k-FAB-B(config-if-hsrp)# hsrp 120</span>

<span>N9k-FAB-B(config-if-hsrp)# preempt</span>

<span>N9k-FAB-B(config-if-hsrp)# priority 100</span>

<span>N9k-FAB-B(config-if-hsrp)# ip 10.22.120.1</span>

<span>N9k-FAB-B(config-if-hsrp)#exit</span>

<span>N9k-FAB-B(config)# interface Vlan130</span>

<span>N9k-FAB-B(config-if)# description Tenant_ClusterMgmt_Network</span>

<span>N9k-FAB-B(config-if)# no shutdown</span>

<span>N9k-FAB-B(config-if)# no ip redirects</span>

<span>N9k-FAB-B(config-if)# ip address 10.22.130.254/24</span>

<span>N9k-FAB-B(config-if)# no ipv6 redirects</span>

<span>N9k-FAB-B(config-if)# hsrp version 2</span>

<span>N9k-FAB-B(config-if-hsrp)# hsrp 130</span>

<span>N9k-FAB-B(config-if-hsrp)# preempt</span>

<span>N9k-FAB-B(config-if-hsrp)# priority 100 </span>

<span>N9k-FAB-B(config-if-hsrp)# ip 10.22.130.1</span>

<span>N9k-FAB-B(config-if-hsrp)#exit</span>

<span>N9k-FAB-B(config)# interface Vlan150</span>

<span>N9k-FAB-B(config-if)# description Storage_ClusterMgmt_Network</span>

<span>N9k-FAB-B(config-if)# no shutdown</span>

<span>N9k-FAB-B(config-if)# no ip redirects</span>

<span>N9k-FAB-B(config-if)# ip address 10.22.150.254/24</span>

<span>N9k-FAB-B(config-if)# no ipv6 redirects</span>

<span>N9k-FAB-B(config-if)# hsrp version 2</span>

<span>N9k-FAB-B(config-if-hsrp)# hsrp 150</span>

<span>N9k-FAB-B(config-if-hsrp)# preempt</span>

<span>N9k-FAB-B(config-if-hsrp)# priority 100 </span>

<span>N9k-FAB-B(config-if-hsrp)# ip 10.22.150.1</span>

<span>N9k-FAB-B(config-if-hsrp)#exit</span>

<span>N9k-FAB-B(config)# interface Vlan160</span>

<span>N9k-FAB-B(config-if)# description Tenanat_Floating_Network</span>

<span>N9k-FAB-B(config-if)# no shutdown</span>

<span>N9k-FAB-B(config-if)# no ip redirects</span>

<span>N9k-FAB-B(config-if)# ip address 10.22.175.254/20</span>

<span>N9k-FAB-B(config-if)# no ipv6 redirects</span>

<span>N9k-FAB-B(config-if)# hsrp version 2</span>

<span>N9k-FAB-B(config-if-hsrp)# hsrp 160</span>

<span>N9k-FAB-B(config-if-hsrp)# preempt</span>

<span>N9k-FAB-B(config-if-hsrp)# priority 100 </span>

<span>N9k-FAB-B(config-if-hsrp)# ip 10.22.160.1</span>

<span>N9k-FAB-B(config-if-hsrp)#exit</span>

<span>N9k-FAB-B(config)# interface Vlan215</span>

<span>N9k-FAB-B(config-if)# description External_Network</span>

<span>N9k-FAB-B(config-if)# no shutdown</span>

<span>N9k-FAB-B(config-if)# no ip redirects</span>

<span>N9k-FAB-B(config-if)# ip address 172.22.215.254/24</span>

<span>N9k-FAB-B(config-if)# no ipv6 redirects</span>

<span>N9k-FAB-B(config-if)# exit</span>

<span>N9K-FAB-B(config)# Copy running-config Startup-config</span>

To configure the VPC and Port Channels on Switch A, enter the following:

<span>N9K-FAB-A(config)# vpc domain 100</span>

<span>N9K-FAB-A(config-vpc-domain)# role priority 10  </span>

<span>N9K-FAB-A(config-vpc-domain)# peer-keepalive destination 10.22.100.4</span>

<span>N9K-FAB-A(config-vpc-domain)# peer-gateway</span>

<span>N9K-FAB-A(config-vpc-domain)# exit</span>

<span>N9K-FAB-A(config)# interface port-channel1</span>

<span>N9K-FAB-A(config-if)# description VPC peerlink for Nexus 9k Switch A &amp; B</span>

<span>N9K-FAB-A(config-if)# switchport mode trunk</span>

<span>N9K-FAB-A(config-if)# spanning-tree port type network</span>

<span>N9K-FAB-A(config-if)# speed 10000</span>

<span>N9K-FAB-A(config-if)# vpc peer-link</span>

<span>N9K-FAB-A(config-if)# exit</span>

<span>N9K-FAB-A(config)# interface Ethernet1/1</span>

<span>N9K-FAB-A(config-if)# description connected to Peer Nexus 9k-B port1/1</span>

<span>N9K-FAB-A(config-if)# switchport mode trunk</span>

<span>N9K-FAB-A(config-if)# speed 10000</span>

<span>N9K-FAB-A(config-if)# channel-group 1 mode active</span>

<span>N9K-FAB-A(config-if)# exit</span>

<span>N9K-FAB-A(config)# interface Ethernet1/2</span>

<span>N9K-FAB-A(config-if)# description connected to Peer Nexus 9k-B port1/2</span>

<span>N9K-FAB-A(config-if)# switchport mode trunk</span>

<span>N9K-FAB-A(config-if)# speed 10000</span>

<span>N9K-FAB-A(config-if)# channel-group 1 mode active</span>

<span>N9K-FAB-A(config-if)# exit</span>

<span>N9K-FAB-A(config)# interface port-channel17</span>

<span>N9K-FAB-A(config-if)# description Port-channel for UCS_Fabric_A port_17 &amp; port_18</span>

<span>N9K-FAB-A(config-if)# switchport mode trunk</span>

<span>N9K-FAB-A(config-if)# switchport trunk allowed vlan 1,100,110,120,130,150,160,215</span>

<span>N9K-FAB-A(config-if)# spanning-tree port type edge trunk</span>

<span>N9K-FAB-A(config-if)# vpc 17</span>

<span>N9k-FAB-A(config-if)# mtu 9216</span>

<span>N9K-FAB-A(config-if)# exit</span>

<span>N9K-FAB-A(config)# interface port-channel18</span>

<span>N9K-FAB-A(config-if)# description Port-channel for UCS_Fabric_B port_17 &amp; port_18</span>

<span>N9K-FAB-A(config-if)# switchport mode trunk</span>

<span>N9K-FAB-A(config-if)# switchport trunk allowed vlan 1,100,110,120,130,150,160,215</span>

<span>N9K-FAB-A(config-if)# spanning-tree port type edge trunk</span>

<span>N9K-FAB-A(config-if)# vpc 18</span>

<span>N9k-FAB-A(config-if)# mtu 9216</span>

<span>N9K-FAB-A(config-if)# exit</span>

<span>N9K-FAB-A(config)# interface Ethernet1/17</span>

<span>N9K-FAB-A(config-if)# description Uplink from UCS_Fabric_A_Port_18</span>

<span>N9K-FAB-A(config-if)# channel-group 17 mode active</span>

<span>N9K-FAB-A(config-if)# spanning-tree port type edge trunk</span>

<span>N9K-FAB-A(config-if)# exit</span>

<span>N9K-FAB-A(config)# interface Ethernet1/18</span>

<span>N9K-FAB-A(config-if)# description Uplink from UCS_Fabric_B_Port_18</span>

<span>N9K-FAB-A(config-if)# channel-group 18 mode active</span>

<span>N9K-FAB-A(config-if)# spanning-tree port type edge trunk</span>

<span>N9K-FAB-A(config-if)# exit</span>

<span>N9K-FAB-A(config)# Copy running-config Startup-config</span>

To configure the VPC and Port Channels on the Cisco Nexus 9K Switch B, enter the following:

<span>N9k-FAB-B(config)# vpc domain 100</span>

<span>N9K-FAB-B(config-vpc-domain)# role priority 10</span>

<span>N9k-FAB-B(config-vpc-domain)# peer-keepalive destination 10.22.100.3</span>

<span>N9k-FAB-B(config-vpc-domain)# peer-gateway</span>

<span>N9k-FAB-B(config-vpc-domain)# exit</span>

<span>N9k-FAB-B(config)# interface port-channel1</span>

<span>N9k-FAB-B(config-if)# description VPC peerlink for Nexus 9k Switch A &amp; B</span>

<span>N9k-FAB-B(config-if)# switchport mode trunk</span>

<span>N9k-FAB-B(config-if)# spanning-tree port type network</span>

<span>N9k-FAB-B(config-if)# speed 10000</span>

<span>N9k-FAB-B(config-if)# vpc peer-link</span>

<span>N9k-FAB-B(config-if)# exit</span>

<span>N9k-FAB-B(config)# interface Ethernet1/1</span>

<span>N9k-FAB-B(config-if)# description connected to Peer Nexus 9k-A port1/1</span>

<span>N9k-FAB-B(config-if)# switchport mode trunk</span>

<span>N9k-FAB-B(config-if)# speed 10000</span>

<span>N9k-FAB-B(config-if)# channel-group 1 mode active</span>

<span>N9k-FAB-B(config-if)# exit</span>

<span>N9k-FAB-B(config)# interface Ethernet1/2</span>

<span>N9k-FAB-B(config-if)# description connected to Peer Nexus 9k-A port1/2</span>

<span>N9k-FAB-B(config-if)# switchport mode trunk</span>

<span>N9k-FAB-B(config-if)# speed 10000</span>

<span>N9k-FAB-B(config-if)# channel-group 1 mode active</span>

<span>N9k-FAB-B(config-if)# exit</span>

<span>N9k-FAB-B(config)# interface port-channel17</span>

<span>N9k-FAB-B(config-if)# description Port-channel for UCS_Fabric_A port_17 &amp; port_18</span>

<span>N9k-FAB-B(config-if)# switchport mode trunk</span>

<span>N9k-FAB-B(config-if)# switchport trunk allowed vlan 1,100,110,120,130,150,160,215</span>

<span>N9k-FAB-B(config-if)# spanning-tree port type edge trunk</span>

<span>N9k-FAB-B(config-if)# vpc 17</span>

<span>N9k-FAB-B(config-if)# mtu 9216</span>

<span>N9k-FAB-B(config-if)# exit</span>

<span>N9k-FAB-B(config)# interface port-channel18</span>

<span>N9k-FAB-B(config-if)# description Port-channel for UCS_Fabric_B port_17 &amp; port_18</span>

<span>N9k-FAB-B(config-if)# switchport mode trunk</span>

<span>N9k-FAB-B(config-if)# switchport trunk allowed vlan 1,100,110,120,130,150,160,215</span>

<span>N9k-FAB-B(config-if)# spanning-tree port type edge trunk</span>

<span>N9k-FAB-B(config-if)# vpc 18</span>

<span>N9k-FAB-B(config-if)# mtu 9216</span>

<span>N9k-FAB-B(config-if)# exit</span>

<span>N9k-FAB-B(config)# interface Ethernet1/17</span>

<span>N9k-FAB-B(config-if)# description Uplink from UCS_Fabric_A_Port_17</span>

<span>N9k-FAB-B(config-if)# channel-group 17 mode active</span>

<span>N9k-FAB-B(config-if)# spanning-tree port type edge trunk</span>

<span>N9k-FAB-B(config-if)# exit</span>

<span>N9k-FAB-B(config)# interface Ethernet1/18</span>

<span>N9k-FAB-B(config-if)# description Uplink from UCS_FabricB_Port_17</span>

<span>N9k-FAB-B(config-if)# channel-group 18 mode active</span>

<span>N9k-FAB-B(config-if)# spanning-tree port type edge trunk</span>

<span>N9k-FAB-B(config-if)# exit</span>

<span>N9K-FAB-B(config)# Copy running-config Startup-config</span>

After successfully creating a Virtual Port Channel on both Nexus switches, verify the Port Channel status on the Nexus 9K Switch. To verify the status, enter the following:

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_144.png)

To verify the status on the Fabrics, complete the following steps as shown in the screenshots below:

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_145.jpg)

Prior to starting the Operating System installation on the Undercloud Node, you must complete the pre-validation checks. To complete the validation checks, complete the following steps:

1.If you are planning to use Jumbo frames for the storage network, make sure to enter the following information in the templates as shown in the screenshot below.

<img src="<http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_o>
penstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_146.jpg" id="Picture 393" width="624" />

2.When the service profiles are created from the template, unbind from the templates in case they have been created as updating templates. This is to accommodate the UCS Manager Plugin. Keeping the compute host's service profiles bound to the template does not allow the plugin to individually configure each compute host with tenant based VLANs. Hence, the service profiles for each compute host need to be unbound from the template. Please check the current limitations outlined in the [UCSM Kilo plugin web page](http://docwiki.cisco.com/wiki/OpenStack/UCS_Mechanism_Driver_for_ML2_Plugin_Kilo).

3.The naming convention for the tenant interfaces is also vNIC eth1\. This is the same for the Cisco UCS Manager Plugin, link provided above.

4.VLAN ID is already included in OpenStack configuration. Do not have native vlan tagged for your external interface on overcloud service profiles.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_147.png)

5.The provisioning interfaces should be Native for both Undercloud and Overcloud setups.

6.While planning your networks, make sure all the networks defined are not overlapping with any of your data-center networks.

7.The disks should be in the same order across all storage nodes.

It is highly recommended to install the Operating System with Versionlock as outlined in the steps below. Versionlock restricts yum to install or upgrade a package to a fixed specific version than specified using the Versionlock plugin of yum

The steps outlined in this document including a few of the configurations, are bound to the installed packages. Installing the same set of packages as in the Cisco Validated Design ensures accuracy of the solution with minimal deviations. This is an attempt to make sure that the installation steps deviate lesser when OpenStack packages move further. While installing RHEL-OSP7 on Cisco blade and rack servers without version lock should still work, it needs to be noted that there could be changes in the configurations and install steps needed that may not exist in this document.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Any updates to the Undercloud stack through yum install may conflict with the version lock packages. You may have to relax the lock files for such updates, when it is required. It is strongly recommended to complete the install with version lock first followed by Overcloud install before attempting any such updates.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Download the Versionlock and kick start the file from Cisco Systems.

To install the Operating System on the Undercloud Node, complete the following steps:

1.Download the Versionlock files for Red Hat Enterprise Linux 7.2 and Red Hat OpenStack 7.2 along with the kick start file from:

<https://cnsg-yum-server.cisco.com/yumrepo/cvd/>

2.Use these files for installing the Red Hat Linux 7.2 followed by RHEL-OSP install. Please refer Red Hat Online documentation for kick start install. The steps below are extracted from <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/sect-kickstart-howto.html>

3.Sanity check the files and update on details like subscription management and web server to host these files for network install. The web server should be accessible from this Director node which is being kick started now.

4.Make sure that curl or wget of [http://Your_Web_server:<port>/<Any_Optional_Alias>/anaconda-ks.cfg](http://Your_Web_server:%3cport%3e/%3cAny_Optional_Alias%3e/anaconda-ks.cfg) file is retrievable.

5.Download Red Hat Enterprise Linux 7.2 from <http://access.redhat.com>

<span lang="EN">.</span>

6.Log into the UCS Manager and make sure to specify the order of NIC's manually:

Make sure that UCS will bring up these interfaces in the following order:

<span>Interface</span>

<span>OS Interface Name</span>

<span>Order</span>

PXE_vNIC

eth0

Internal_API_vNIC

eth1

External_vNIC

eth2

7.Launch the KVM Console; UCS Manager > Equipment Tab > General > KVM Console.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_148.png)

8.In the KVM Console Menu, Activate Virtual Devices under Virtual Media and then click Map CD/DVD, attach the downloaded iso as shown below and then reboot the server.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_149.png)

9.When the system boots up, press F6 for the boot menu.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_150.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_151.png)

10.Press tab to enter the command line options as shown below:

<span>—</span>

inst.ks, the network location of the anaconda-ks.cfg file, along with network parameters like ip, netmask, hostname, gateway and interface name to boot up with to reach the web server.

The following is an example of what was used in the configuration. Please change your installation accordingly:

<span>—</span>

inst.ks=<http://172.22.215.6/anaconda-ks.cfg> net.ifnames=0 biosdevname=0 ip=172.22.215.9::172.22.215.1:255.255.255.0:osp7.director.cisco.com:eth2:none

<span>—</span>

eth2 is the external interface configured. See above.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_152.png)

11.Select the default language and time zone.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Ignore the software selection since this will come from the kick start file.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_153.jpg)

12.Select manual partitioning and remove any unwanted partitions. This LUN is carved out from two disks on the Undercloud node (RAID 10 mirror set up through local disk config policy or through storage profile in UCS). Preferably increase the root partition to 100GB.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_154.jpg)

13.Select network tab and configure the external and internal api nics as shown below:

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_155.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_156.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_157.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_158.jpg)

14.Leave the pxe interface NIC configuration and do not configure. It will be configured later through the Undercloud install.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_159.jpg)

15.Enter the root password and optionally create the stack user and reboot the server when prompted.

16.Run Post Install checks before proceeding:

a.Run subscription-manager status to check the registration status. Make sure that the pool with OpenStack entitlements is attached.

b.Check for existence of versionlock.conf and versionlock.list in /etc/yum/pluginconf.d/versionlock.list. yum versionlock list command should reveal the contents for /etc/yum/pluginconf.d/versionlock.list.

c.Run ifconfig to check the health of the configured interfaces. The pxe should not have been configured at this stage.

d.Check name resolution and external connectivity. This is needed for yum updates and registration.

e.Validate by running wget www.cisco.com or wget subscription.rhn.redhat.com

f.Refer [bug 1178497](https://bugzilla.redhat.com/show_bug.cgi?id=1178497). This bug is not in the main stream, at the time of writing this document. Please follow the workaround steps in the bug and reboot the kernel.

Take a backup of /boot/initramfs<kernel> to revert back in case something goes wrong:

edit the /usr/lib/dracut/modules.d/99shutdown/module-setup.sh and files /usr/lib/dracut/modules.d/99shutdown/shutdown.sh after taking a backup of these files.

**module-setup.sh**

Change

<span>inst_multiple umount poweroff reboot halt losetup</span>

<span>inst_multiple umount poweroff reboot halt losetup stat</span>

**shutdown.sh**

insert a block of code

after ./lib/dracut-lib.sh

**add:**

<span>if [ “$(stat -c ‘%T’ -f /)” = “tmpfs” ]; then<br>   mount -o remount,rw /<br>fi</span>

**Recreate initramfs** :

<span>dracut –-force</span>

**Unmask the shutdown :**

<span>systemctl unmask dracut-shutdown.service</span>

**Reboot the node**

This completes the OS Installation on the Director node.

To install Undercloud, complete the following steps:

1.Create Stack User:

If Stack user was not created as part of the install earlier, it has to be created for the Undercloud now.

<span>useradd stack</span>

<span>passwd stack</span>

<span>echo “stack ALL=(root) NOPASSWD:ALL” | tee -a /etc/sudoers.d/stack</span>

<span>chmod 0440 /etc/sudoers.d/stack</span>

2.Update sysctl.conf ( as root user ):

<span>echo “net.ipv4.ip_forward = 1” &gt;&gt; /etc/sysctl.conf</span>

<span>sysctl -p /etc/sysctl.conf</span>

3.Become Stack User and create the following:

<span>su – stack</span>

<span>mkdir -p ~/images</span>

<span>mkdir –p ~/templates</span>

<span>sudo hostnamectl set-hostname &lt;FQDN of the director node &gt; as an example</span>

<span>sudo hostnamectl set-hostname osp7-director.cisco.com</span>

<span>sudo hostnamectl set-hostname –transient osp7-director.cisco.com</span>

4.Update /etc/hosts:

<span>sudo vi /etc/hosts as below</span>

<span>#External Interface</span>

<span>172.22.215.9    osp7-director.cisco.com osp7-director</span>

<span># pxe interface</span>

<span>10.22.100.26    osp7-director.cisco.com osp7-director</span>

<span># Internal API </span>

<span>10.22.110.26    osp7-director.cisco.com osp7-director</span>

<span># local</span>

<span>127.0.0.1    osp7-director.cisco.com osp7-director</span>

Comment out the other entries like localhost made by the anaconda installer.

5.Update resolv.conf if needed:

<span>sudo vi /etc/resolv.conf as needed. As an example </span>

<span>search cisco.com</span>

<span>nameserver 8.8.8.8</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

It is recommended to use your organization DNS server. name server 8.8.8.8 is used here for reference purpose only.

6.In case you have not registered the Undercloud node as part of versionlock earlier, please register the system to Red Hat Network and get the appropriate pool id for Open stack entitlements and attach the pool.

7.Disable and enable only the required repositories:

<span>sudo subscription-manager repos –disable=*</span>

<span>sudo subscription-manager repos –enable=rhel-7-server-rpms \</span>

<span>–enable=rhel-7-server-optional-rpms –enable=rhel-7-server-extras-rpms \</span>

<span>–enable=rhel-7-server-openstack-7.0-rpms \</span>

<span>–enable=rhel-7-server-openstack-7.0-director-rpms</span>

8.Yum Update the server:

<span>sudo yum update -y</span>

<span>sudo yum install yum-plugin-priorities -y</span>

<span>sudo yum install yum-utils -y</span>

<span>sudo yum-config-manager –enable rhel-7-server-openstack-7.0-rpms \</span>

<span>–setop=“rhel-7-server-openstack-7.0-rpms.priority=1”</span>

<span>sudo yum-config-manager –enable rhel-7-server-extras-rpms \</span>

<span>–setop=“rhel-7-server-extras-rpms.priority=1”</span>

<span>sudo yum-config-manager –enable rhel-7-server-openstack-7.0-director-rpms \</span>

<span>–setop=“rhel-7-server-openstack-7.0-director-rpms.priority=1”</span>

<span>sudo yum-config-manager –enable rhel-7-server-optional-rpms \</span>

<span>–setop=“rhel-7-server-optional-rpms.priority=1”</span>

<span>sudo yum-config-manager –enable rhel-7-server-rpms \</span>

<span>–setop=“rhel-7-server-rpms.priority=1”</span>

9.Install Undercloud packages:

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Make sure that Versionlock is in place by running 

<span>yum versionlock list</span>

.

<span>sudo yum install -y python-rdomanager-oscplugin</span>

<span>sudo yum update –y</span>

10.Create undercloud.conf file:

<span>cp /usr/share/instack-undercloud/undercloud.conf.sample ~/undercloud.conf</span>

The following are the values used in the configuration. 10.22.110 is the pxe network:

<span>image_path =/home/stack/images</span>

<span>local_ip = 10.22.110.26/24</span>

<span>undercloud_public_vip = 10.22.110.27</span>

<span>undercloud_admin_vip = 10.22.110.28</span>

<span>local_interface = eth0</span>

<span>masquerade_network = 10.22.110.0/24</span>

<span>dhcp_start = 10.22.110.51</span>

<span>dhcp_end = 10.22.110.80</span>

<span>network_cidr = 10.22.110.0/24</span>

<span>network_gateway = 10.22.110.26</span>

<span>discovery_interface = br-ctlplane</span>

<span>discovery_iprange = 10.22.110.81,10.22.110.110</span>

<span>undercloud_debug = true</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

By using the provisioning interface on the director node and the local_ip and network_gateway, it configures the system to act as the gateway for all the nodes.

11.Update enic driver:

a.Download the enic driver Cisco appropriate for the UCSM version. The version used in the configuration with UCSM 2.2(5) and RHEL 7.2 was 2.1.1.93: go to <http://software.cisco.com/download/navigator.html>

b.In the download page, select servers-Unified computing under products. On the right menu select your class of servers say Cisco UCS B-series Blade server software and then select Unified Computing System (UCS) Drivers in the following page.

**<span>c.</span>**

Select your firmware version under All Releases, as an example 2.2(5c) and download the ISO image of UCS-related drivers for your matching firmware, for example 

**<span>ucs-bxxx-drivers.2.2.5c.iso.</span>**

d.Download the iso file to your undercloud machine and mount the iso:

<span>[root@osp7-director ~]# mount ucs-bxxx-drivers.2.2.5.iso /mnt </span>

<span>mount: /dev/loop0 is write-protected, mounting read-only</span>

<span>cd /mnt/Linux/Network/Cisco/VIC/RHEL/RHEL7.2</span>

<span>cp kmod-enic-2.1.1.93-rhel7u2.el7.x86_64.rpm /tmp</span>

<span>rpm –ivh kmod-enic-2.1.1.93-rhel7u2.el7.x86_64.rpm</span>

e.Install the appropriate enic driver on the director machine.

f.Validate by running modinfo:

<span>[root@osp7-director RHEL7.1]# modinfo enic </span>

<span>filename:       /lib/modules/3.10.0-327.3.1.el7.x86_64/weak-updates/enic/enic.ko</span>

<span>version:        2.1.1.93</span>

<span>license:        GPL v2</span>

<span>author:         Scott Feldman &lt;scofeldm@cisco.com&gt;</span>

<span>description:    Cisco VIC Ethernet NIC Driver</span>

<span>rhelversion:    7.2</span>

<span>srcversion:     D272F11F27065C9714656F4</span>

<span>alias:          pci:v00001137d00000071sv*sd*bc*sc*i*</span>

<span>alias:          pci:v00001137d00000044sv*sd*bc*sc*i*</span>

<span>alias:          pci:v00001137d00000043sv*sd*bc*sc*i*</span>

<span>depends:        </span>

<span>vermagic:       3.10.0-327.el7.x86_64 SMP mod_unload modversions </span>

<span>parm:           rxcopybreak:Maximum size of packet that is copied to a new buffer on receive (uint)</span>

g.Copy the enic file to your ~/images directory created above.

<span>cp /lib/modules/3.10.0-327.3.1.el7.x86_64/weak-updates/enic/enic.ko ~/images</span>

<span>[root@osp7-director RHEL7.1]# ls -l /home/stack/images/enic.ko </span>

<span>-rw-r–r–. 1 stack stack 3982019 Jan 16 21:38 /home/stack/images/enic.ko</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

This enic.ko file will be used to customize Overcloud image file later.

12.Download libguestfs tool needed to customize Overcloud image file:

<span>sudo yum install libguestfs-tools –y</span>

The system is ready for running Undercloud install now.

13.Run the following as stack user:

<span>openstack undercloud install</span>

This might take around 10 minutes.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

To debug any Undercloud install failures, check files in /home/stack/.instack/*

To perform the Undercloud installation checks, complete the following steps:

1.Check sysctl.conf:

<span>cat /etc/sysctl.conf</span>

<span>net.ipv4.ip_forward=1</span>

<span>net.ipv4.ip_nonlocal_bind=1</span>

2.Check control plane bridge:

A new bridge br-ctlplane should have been created as part of the Undercloud install on the pxe interface as shown below. Validate MAC and IP's.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_160.png)

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Check /var/log/ironic/* files, to understand fix any issues.

3.Update /etc/openstack-dashboard:

a.Update allowed_hosts to ip's needed to launch Undercloud UI and restart httpd service on Undercloud node.

<span>[stack@osp7-director ~]$ sudo -i </span>

<span>[root@osp7-director ~]# vi /etc/openstack-dashboard/local_settings</span>

<span>ALLOWED_HOSTS = [’172.22.215.9’, ‘10.22.110.26’, ‘osp7-director.cisco.com’, ‘localhost’, ]</span>

<span>[root@osp7-director ~]# systemctl restart httpd.service</span>

b.Log into the Undercloud dashboard from one of the IP's above and do a sanity check. Log in as user admin. The default password can be obtained from /home/stack/stackrc (run sudo hiera admin password).

To do the pre-installation check, complete the following steps:

1.Check neutron and subnet lists:

a.Run neutron net-list, neutron net-show, neutron subnet-list and neutron subnet-show for br-ctlplane:

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_161.jpg)

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

The allocation_pools, dns_nameservers, cidr should match whatever specified earlier in undercloud.conf file. If not, update with neutron subnet-update.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_162.jpg)

2.Check /etc/ironic-discoverd/* files:

<span>vi /etc/ironic-discoverd/discoverd.conf /etc/ironic-discoverd/dnsmasq.conf</span>

The dnsmasq.conf dhcp_range should match the undercloud.conf file range. This will help you spot any errors that might have gone while running Undercloud install earlier.The default pxe timeout is 60 minutes in Kilo. This means if you have more servers to be introspected and it takes longer than 60 minutes, introspection is bound to fail.

a.Update /etc/ironic-discoverd/discoverd.conf with timeout vaiable under discovered section:

<span>timeout=0</span>

<span>b.</span>

Restart ironic in case these files are updated.

<span>[root@osp7-director ~]# systemctl restart openstack-ironic-conductor.service openstack-ironic-discoverd-dnsmasq.service openstack-ironic-api.service openstack-ironic-discoverd.service</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

This may be necessary only in larger deployments and depends on the network to download the ramdisk files, CPU speed, etc. On the configuration this issue was discovered while introspecting 35 nodes.

3.Prepare instack.json file.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

This file should contain all the nodes, controllers, computes and storage nodes that need to be instrospected.

A sample instackenv.json file is provided in Appendix A. Below is an explanation of how to build this file for a node.

<span>  “nodes”: [</span>

<span>      “pm_user”: “admin”,</span>

<span>      “pm_password”: “password”,</span>

<span>      “pm_type”: “pxe_ipmitool”,</span>

<span>      “pm_addr”: “10.22.100.19”,</span>

<span>      “mac”: [</span>

<span>        “00:25:b5:00:00:2d”</span>

<span>      “memory”: “262144”,</span>

<span>      “disk”: “270”,</span>

<span>      “arch”: “x86_64”,</span>

<span>      “cpu”: “40”</span>

<span>……………………</span>

<span>…………………….</span>

<span>pm_user and pm_password</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

This is the ipmi user and password configured earlier for this node's service profile or templates.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_163.jpg)

<span>pm_type=”pxe_ipmitool”</span>

pm_addr is the IPMI address allocated to that node. This can be obtained from the CIMC tab in equipment.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_164.jpg)

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

The MAC address is the discovery nic or pxe interface for that node.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_165.jpg)

The memory, disk and CPU can be obtained under the same inventory tab for that node.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Make sure that the storage lun is applied and in operable state after applying the storage policy.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_166.jpg)

Build the instackenv.json file for all the hosts that have to be introspected as above.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Make sure to maintain consistent indentations with white spaces or tabs.

4.Check the ipmi connectivity works for all the hosts:

a.You can run a quick check to validate this from instackenv.json file:

<span>[stack@osp7-director ~]$ for i in `grep pm_addr instackenv.json | cut -d “\”" -f4`</span>

<span>ipmitool -I lanplus -H $i -U admin -P &lt;replace with your ipmi password&gt; chassis power status</span>

<span>done</span>

<span>Chassis Power is off</span>

<span>Chassis Power is off</span>

<span>Chassis Power is off</span>

<span>Chassis Power is off</span>

<span>Chassis Power is off</span>

<span>Chassis Power is off</span>

<span>Chassis Power is off</span>

<span>Chassis Power is off</span>

<span>Chassis Power is off</span>

<span>Chassis Power is off</span>

<span>Chassis Power is off</span>

<span>Chassis Power is off</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

The user 'admin' used above, is the IPMI admin user configured earlier in UCS.

b.The chassis power status should be either On or Off depending on whether the server is up or down in UCS. However any errors like the example shown below need investigation:

<span>Error: Unable to establish IPMI v2 / RMCP+ session</span>

<span>Unable to get Chassis Power Status</span>

5.Install ntp server and synchronize the clock:

a.As root user;

<span>yum install ntp –y</span>

6.Update /etc/ntp.conf file with appropriate ntp server address and restart ntpd;

<span>[root@osp7-director ~]# service ntpd restart </span>

<span>Redirecting to /bin/systemctl restart  ntpd.service</span>

7.Check the time sync, else restart ntpd couple of times to force sync the time:

<span>service ntpd restart</span>

<span>[root@osp7-director ~]# ntpdate -dv 171.68.38.66 </span>

<span>16 Jan 13:13:25 ntpdate[16252]: ntpdate 4.2.6p5@1.2349-o Fri Oct 16 08:51:51 UTC 2015 (1)</span>

<span>Looking for host 171.68.38.66 and service ntp</span>

<span>host found : mtv5-ai27-dcm10n-ntp2.cisco.com</span>

<span>transmit(171.68.38.66)</span>

<span>receive(171.68.38.66)</span>

<span>………..</span>

<span>………..</span>

<span>………..</span>

<span>delay 0.02707, dispersion 0.00000</span>

<span>offset 0.000085 </span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

The clock is synchronized to 85 micro seconds now. Usually a clock sync of less than 20 minutes is needed to avoid any heatlh_warn complaints from Ceph monitors.

8.Download the Image files needed for introspection and Overcloud:

a.Login to <http://access.redhat.com>

<span class="MsoHyperlink">.</span>

b.Click Downloads. Under Product Downloads by category and in cloud products, select Red Hat Enterprise Linux OpenStack Platform to download the Overcloud image files. Select overcloud image for 7.2 and download to the images directory.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_167.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_168.jpg)

c.Download Deployment Ramdisk, Overcloud Image and Discovery Ramdisk for Red Hat Linux Director for 7.2\. The solution is validated on 7.2\. Customizations and interoperability of these files with Cisco Plugins were done with 7.2\. In case of higher versions posted in this web page, contact Red Hat for getting 7.2 images.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

The images can be downloaded directly from Director Host as a GUI install was done on the Director node by launching a browser or doing a wget of the download links above.

d.Download the files into /home/stack/images directory, extract the tar files;

<span>cd /home/stack/images; for i in *.tar; do tar xf $i; done</span>

e.The following files should exist after extraction:

<span>[stack@osp7-director images]$ /bin/ls -1</span>

<span>deploy-ramdisk-ironic.initramfs</span>

<span>deploy-ramdisk-ironic.kernel</span>

<span>discovery-ramdisk.initramfs</span>

<span>discovery-ramdisk.kernel</span>

<span>overcloud-full.initrd</span>

<span>overcloud-full.qcow2</span>

<span>overcloud-full.vmlinuz</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

You may remove the tar files if desired.

f.Download the Guest image to the downloads directory.

g.Under Product downloads above, select Red Hat Enterprise Linux under infrastructure Management and download KVM Guest Image for 7.2.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_169.jpg)

<span>rhel-guest-image-7.2-20151102.0.x86_64.qcow2</span>

9.Customize the Overcloud image with n1kv modules, enic drivers and fencing packages.

Run the following as root user. Navigate to your download directory and issue the following as root:

<span>cd /home/stack/images</span>

<span>export LIBGUESTFS_BACKEND=direct</span>

a.Update fencing packages.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Before proceeding with the customization of the Overcloud image, there are some fixes that are not part of the osp7 y2 distribution. [Refer to]()[bug 1298430](https://bugzilla.redhat.com/show_bug.cgi?id=1298430).

Download the fencing packages from [http://people.redhat.com/cfeist/cisco_ucs/](http://people.redhat.com/cfeist/cisco_ucs/%20) to ~/images/ directory. These packages are being integrated to mainstream and we will update the document when they are available in the Red Hat repository.

Extract the fencing files from these rpm's as shown below:

<span>rpm2cpio fence-agents-common &lt;name of the rpm file &gt; | cpio –idmv</span>

<span>rpm2cpio fence-agents-cisco-ucs &lt;name of the rpm file &gt; | cpio –idmv</span>

This should create a local usr/share directory. The following two files need to be copied:

<span>cp ./usr/share/fence/fencing.py /home/stack/images</span>

<span>cp ./usr/sbin/fence_cisco_ucs /home/stack/images</span>

These two files will be used to update the overcloud image.

As root user;

<span>cd /home/stack/images</span>

<span>chmod +x ./fencing.py</span>

<span>chmod +x ./fence_cisco_ucs</span>

<span>chown root:root fenc*</span>

<span>virt-copy-in -a overcloud-full.qcow2 ./fencing.py /usr/share/fence/</span>

<span>virt-copy-in -a overcloud-full.qcow2 ./fence_cisco_ucs /sbin/</span>

<span>Maybe you can virt-copy-out and validate that the files have been uploaded properly by extracting them to say /tmp location</span>

<span>virt-copy-out –a overcloud-full.qcow2 /usr/share/fence/fencing.py /tmp</span>

<span>virt-copy-out –a overcloud-full.qcow2 /sbin/fence_cisco_ucs /tmp</span>

b.Update Grub file;

<span>virt-copy-out -a overcloud-full.qcow2 /etc/default/grub/home/stack/images/</span>

<span>vi grub file and change the following line</span>

<span>GRUB_CMDLINE_LINUX=“console=tty0 console=ttyS0,115200n8 crashkernel=auto rhgb quiet net.ifnames=0 biosdevname=0”</span>

<span>—(you are appending net.ifnames=0 and biosdevname=0)</span>

<span>virt-copy-in –a overcloud-full.qcow2 ./grub /etc/default/</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

After this proceed, with the the remaining customizations.

c.Update enic drivers;

<span>virt-copy-in -a overcloud-full.qcow2 ./enic.ko /lib/modules/3.10.0-327.3.1.el7.x86_64/kernel/drivers/net/ethernet/cisco/enic/</span>

The location of this enic driver is dependent on the kernel packaged in the Overcloud image file. Should be changed if needed.

d.Update root password;

<span>virt-customize -a overcloud-full.qcow2 –root-password password:&lt;password&gt;</span>

e.Install n1kv modules;

<span>echo "[n1kv]</span>

<span>name=n1kv</span>

<span>baseurl=<a href="https://cnsg-yum-server.cisco.com/yumrepo/">https://cnsg-yum-server.cisco.com/yumrepo/</a></span>

<span>enabled=1</span>

<span>gpgcheck=0" &gt; n1kv.repo</span>

<span>virt-customize -a overcloud-full.qcow2 –upload n1kv.repo:/etc/yum.repos.d/</span>

<span>rm n1kv.repo</span>

<span>virt-customize -a overcloud-full.qcow2 –install nexus1000v</span>

<span>virt-customize -a overcloud-full.qcow2 –install nexus-1000v-iso</span>

f.Change the permissions back to stack user:

<span>chown stack:stack /home/stack/images/*</span>

This is how the files may look after the update, after becoming stack user:

<span>[stack@osp7-director images]$ ls -l overcloud-full.qcow2*</span>

While updating, the image with root password is not required; it becomes useful to login through KVM console in case of Overcloud installation failures and debug the issues.

The enic.ko was extracted earlier on the Directory node after installing the enic rpm. This helps ensure that both Director and the Overcloud images will be with same enic driver.

The N1000V modules will be injected and installed in the Overcloud image. As part of deployment, the VSM module will be installed on the Controller nodes, while VEM will be installed on all Controller and Compute nodes, due to the update to the Overcloud image.

The grub has been modified to have interface names like eth[0], eth[1] ...

The fence_cisco_ucs package has been modified to take care of the HA [bug 1298430](https://bugzilla.redhat.com/show_bug.cgi?id=1298430)

<span class="MsoHyperlink">.</span>

10.Upload the images to openstack. As stack user run the following:

<span>su – stack</span>

<span>source stackrc</span>

<span>cd ~/images</span>

<span>openstack overcloud image upload</span>

<span>openstack image list</span>

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_170.jpg)

11.Initialize boot LUNs.

12.Before running Introspection and Overcloud installation, it is recommended to initialize the boot LUNs. This is required in case you are repeating or using old disks.

13.Boot the server in UCS, press CTRL-R, then F2 and re-initialize the boot LUNs as shown below and shutdown the servers.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_171.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_172.jpg)

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Make sure that all the servers are powered off before introspection.

14.Reboot the Undercloud node and start the introspection.

To run Introspection, complete the following steps:

1.As stack user:

<span>source ~/stackrc</span>

<span>openstack baremetal import –json ~/instackenv.json</span>

<span>openstack baremetal configure boot</span>

<span>openstack baremetal list</span>

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_173.jpg)

<span>openstack baremetal introspection bulk start</span>

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_174.jpg)

2.Check the status of Introspection:

<span>openstack baremetal introspection bulk status</span>

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_175.jpg)

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Refer to the Troubleshooting section for any failures around introspection and how to resolve them.

To create Flavors, enter the following:

<span>openstack flavor create –id auto –ram 8192 –disk 100 –vcpus 8 baremetal</span>

<span>openstack flavor create –id auto –ram 32768 –disk 100 –vcpus 8 control</span>

<span>openstack flavor create –id auto –ram 32768 –disk 100 –vcpus 8 compute</span>

<span>openstack flavor create –id auto –ram 32768 –disk 100 –vcpus 8 CephStorage</span>

<span class="MsoIntenseEmphasis">
  <span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>
</span>

<span class="MsoIntenseEmphasis">
  <span>While creating flavors make sure that the values of disk, ram and vcpus are less than their respective servers.</span>
</span>

<span>openstack flavor set –property “cpu_arch”=“x86_64” –property “capabilities:boot_option”=“local” baremetal</span>

<span>openstack flavor set –property “cpu_arch”=“x86_64” –property “capabilities:boot_option”=“local” –property “capabilities:profile”=“compute” compute</span>

<span>openstack flavor set –property “cpu_arch”=“x86_64” –property “capabilities:boot_option”=“local” –property “capabilities:profile”=“control” control</span>

<span>openstack flavor set –property “cpu_arch”=“x86_64” –property “capabilities:boot_option”=“local” –property “capabilities:profile”=“CephStorage” CephStorage</span>

The created Flavors have to be set to every category of servers. Identify the servers based on IPMI address created earlier in instackenv.json file:

<span>[stack@osp7-director ~]$ for i in $(ironic node-list | awk ‘ /power/ { print $2 } ’)</span>

<span>abc=`ironic node-show $i | grep “10.22” | awk ‘{print $7}’`</span>

<span>echo $i $abc </span>

<span>done</span>

The above script will spill out the node-id along with ipmi address that can be mapped as shown below with instackenv.json file.

[<span>Table 5  </span>

Node IDs and ipmi Addresses]()

<span>Node ID</span>                              | <span>ipmi Address</span>     | <span>Server</span>
------------------------------------------------- | ----------------------------- | -------------------------
<span>b7dde876-354a-4688-8550-aec8f64c582c</span> | <span>u’10.22.100.23’,</span> | <span>&lt;–Control</span>
<span>e4563ca5-2f12-4e08-9905-f770f740ad2b</span> | <span>u’10.22.100.22’,</span> | <span>&lt;–Control</span>
<span>285965a9-9713-4301-8ad5-7aa3ef5dd1c2</span> | <span>u’10.22.100.16’,</span> | <span>&lt;–Control</span>
<span>b4dc04ac-0c69-4000-9c4d-2d82d141905f</span> | <span>u’10.22.100.18’,</span> | <span>&lt;–Compute</span>
<span>036cae70-bdee-427c-987c-a6a2d8a32292</span> | <span>u’10.22.100.12’,</span> | <span>&lt;–Compute</span>
<span>8570c96e-f9cd-44ff-a1d8-0252bc405c24</span> | <span>u’10.22.100.19’,</span> | <span>&lt;–Compute</span>
<span>af46cd81-c78e-47c5-94e3-44d9d669410c</span> | <span>u’10.22.100.17’,</span> | <span>&lt;–Compute</span>
<span>19260dbb-29a9-4810-b39d-85cc6e1d886f</span> | <span>u’10.22.100.15’,</span> | <span>&lt;–Compute</span>
<span>d4dae332-4595-43be-9b63-5a64331ea33b</span> | <span>u’10.22.100.14’,</span> | <span>&lt;–Compute</span>
<span>179befe6-2510-4311-ad9f-4880454fdaff</span> | <span>u’10.22.100.20’,</span> | <span>&lt;–Storage</span>
<span>ff0dadfe-e2f3-408f-b69d-01398bb9699d</span> | <span>u’10.22.100.11’,</span> | <span>&lt;–Storage</span>
<span>b59f57e3-d5e1-499a-80c1-aac0c78c9534</span> | <span>u’10.22.100.21’,</span> | <span>&lt;–Storage</span>

With the information from Table 5 , Flavors can be set as follows:

<span>[stack@osp7-director ~]$ for i in b7dde876-354a-4688-8550-aec8f64c582c e4563ca5-2f12-4e08-9905-f770f740ad2b \</span>

<span>&gt; 285965a9-9713-4301-8ad5-7aa3ef5dd1c2</span>

<span>&gt; do</span>

<span>&gt; ironic node-update $i add properties/capabilities=’profile:control,boot_option:local’</span>

<span>&gt; done</span>

<span>[stack@osp7-director ~]$ for i in b4dc04ac-0c69-4000-9c4d-2d82d141905f 036cae70-bdee-427c-987c-a6a2d8a32292 \</span>

<span>&gt; 8570c96e-f9cd-44ff-a1d8-0252bc405c24 af46cd81-c78e-47c5-94e3-44d9d669410c 19260dbb-29a9-4810-b39d-85cc6e1d886f \</span>

<span>&gt; d4dae332-4595-43be-9b63-5a64331ea33b </span>

<span>&gt; do</span>

<span>&gt; ironic node-update $i add properties/capabilities=’profile:compute,boot_option:local’</span>

<span>&gt; done</span>

<span>[stack@osp7-director ~]$ for i in 179befe6-2510-4311-ad9f-4880454fdaff \</span>

<span>&gt; ff0dadfe-e2f3-408f-b69d-01398bb9699d b59f57e3-d5e1-499a-80c1-aac0c78c9534 </span>

<span>&gt; do</span>

<span>&gt; ironic node-update $i add properties/capabilities=’profile:CephStorage,boot_option:local’</span>

<span>&gt; done</span>

The added profiles can be queried for validation:

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Make sure that the previously created Flavors match the output of the query below.

<span>[stack@osp7-director ~]$ instack-ironic-deployment –show-profile </span>

<span>Preparing for deployment…</span>

<span>  Querying assigned profiles … </span>

<span>    b7dde876-354a-4688-8550-aec8f64c582c</span>

<span>      “profile:control,boot_option:local”</span>

<span>    e4563ca5-2f12-4e08-9905-f770f740ad2b</span>

<span>      “profile:control,boot_option:local”</span>

<span>    285965a9-9713-4301-8ad5-7aa3ef5dd1c2</span>

<span>      “profile:control,boot_option:local”</span>

<span>    b4dc04ac-0c69-4000-9c4d-2d82d141905f</span>

<span>      “profile:compute,boot_option:local”</span>

<span>    036cae70-bdee-427c-987c-a6a2d8a32292</span>

<span>      “profile:compute,boot_option:local”</span>

<span>    8570c96e-f9cd-44ff-a1d8-0252bc405c24</span>

<span>      “profile:compute,boot_option:local”</span>

<span>    af46cd81-c78e-47c5-94e3-44d9d669410c</span>

<span>      “profile:compute,boot_option:local”</span>

<span>    19260dbb-29a9-4810-b39d-85cc6e1d886f</span>

<span>      “profile:compute,boot_option:local”</span>

<span>    d4dae332-4595-43be-9b63-5a64331ea33b</span>

<span>      “profile:compute,boot_option:local”</span>

<span>    179befe6-2510-4311-ad9f-4880454fdaff</span>

<span>      “profile:CephStorage,boot_option:local”</span>

<span>    ff0dadfe-e2f3-408f-b69d-01398bb9699d</span>

<span>      “profile:CephStorage,boot_option:local”</span>

<span>    b59f57e3-d5e1-499a-80c1-aac0c78c9534</span>

<span>      “profile:CephStorage,boot_option:local”</span>

<span>  DONE.</span>

<span>Prepared.</span>

You can validate the ipmi, mac_address and server profiles as shown below:

<span>for i in $(ironic node-list | awk ‘/None/ {print $2}’ );</span>

<span>ipmi_addr=`ironic node-show $i | grep “10.22” | awk ‘{print $7}’`</span>

<span>mac_addr=`ironic node-port-list  $i | awk ‘/00:25/ {print $4}’`</span>

<span>profile=`ironic node-show $i | grep -i “u’profile:” | cut -d “:” -f4`</span>

<span>echo $i $ipmi_addr $mac_addr $profile </span>

<span>done</span>

Before delving into the Overcloud installation, it is necessary to understand and change the templates for your configuration. Red Hat Linux OpenStack Director provides lot of flexibility in configuring Overcloud. At the same time, understanding the parameters and providing the right inputs to heat through these templates is paramount.

Before attempting the Overcloud install, it is necessary to understand and setup the Overcloud heat templates. For complete details of the templates, please refer to the Red Hat online documentation on OpenStack.

Overcloud is installed through command line interface with the following command. A top down approach of the yaml and configuration files is provided here. Please note that the files are sensitive to whitespaces and tabs.

Refer to the Appendix A for run.sh, the command used to deploy Overcloud.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_176.jpg)

The heat templates have to be customized depending on the network layout and nic interface configurations in the setup. The templates are standard heat templates in YAML format. They are included in Appendix A. A set of configuration files with floating IP are included in Appendix A while the set in Appendix B are the files without floating IP configuration. Appendix A is the superset while Appendix B has the files that differ. Hence in your configuration, if you have luxury of external IP's for VM's external access and you wish not to use floating IP's you may pick up all the files from Appendix A and overlay them with Appendix B configurations. Again use them for reference purpose only and make the updates as needed.

The network configuration included in the Director are of two categories and are included in /usr/share/openstack-tripleo-heat-templates/network/config

<span>Single nic VLAN’s</span>

<span>Bond with VLAN’s</span>

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_177.jpg)

This model assumes that you have a single interface allowing all the VLAN's configured in the system.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_178.jpg)

In the UCS configuration a hybrid model was adopted. This was done for simplicity and also to have a separate VLAN dedicated on each interface for every network. While this gives a fine grain control of policies like QOS etc, if needed, but were not adopted for simplicity. NIC2 or eth1 was used as tenant interface.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_179.jpg)

<span>As stack user mkdir –p /home/stack/templates/nic-configs</span>

Copy the template files from /usr/share/openstack-tripleo-heat-templates. Refer Red Hat online documentation.

Create network-environment.yaml per above documentation or use the Appendix A for reference.

<span>[stack@osp7-director templates]$ ls</span>

<span>ceph.yaml  cisco-plugins.yaml  nameserver_ntp.yaml  network-environment.yaml  post_config.yaml  storage-environment.yaml  wipe_disk.yaml</span>

<span>[stack@osp7-director nic-configs]$ ls *.yaml</span>

<span>ceph-storage.yaml  compute.yaml  controller.yaml</span>

Some of the above files may have to be created. These files are referenced in Overcloud deploy command either directly or through another file. Ceph.yaml has to be modified directly in /usr/share/openstack-tripleo-heat-templates.

The first section is for resource_registry. The section for parameter defaults have to be customized. The following are a few important points to be noted in network-environment.yaml file:

1.Enter the Network Cidr values in the parameter section.

2.The Internal Network and the UCS management network are on the same network. Make sure that InternalApiAllocationPools do not overlap with UCS IP pools. In the configuration they span the subnet from 10.22.100.50 to 250.

3.For consistency a similar approach followed for Storage and Storage Management Allocation pools.

4.The Tenant Allocation pool and Network is created for /12 subnet, just to allocate more addresses.

5.The Control Plane Default Route is the Gateway Router for the provisioning network or the Undercloud IP. This matches with your network_gateway and masquerade_network in your undercloud.conf file.

6.EC2Metadata IP is the Undercloud IP again.

7.Neturon External Network Bridge should be set to 

<span>“’’”. </span>

An empty string to allow multiple external networks or VLAN's.

8.No bonding used in the configuration. This will be addressed in our future releases.

This parameter section overrides the ones mentioned in the networking-environment file. The get_param calls for the defined parameters. The following are important points to be considered for Controller.yaml file:

1.The PXE interface NIC1 should have dhcp as false to configure static ip's, with next hop going to Undercloud node.

2.The external bridge is configured to the External Interface Default Route on the External Network VlanID.

3.The MTU value of 9000 to be added as needed. Both the storage networks are configured on mtu 9000.

The same rules for the Controller apply:

1.The PXE interface NIC1 is configured with dhcp as false. There are no external IP's available for Controller and Storage. Hence natting is done through UnderCloud node. For this purpose, the Control Plane Default Route is the, network gateway defined in undercloud.conf file which is also the UnderCloud local_ip.

2.Only the Storage Public network is defined along with Tenant networks on Compute nodes.

1.Same as Compute.yaml mentioned above.

2.Only Storage Public and Storage Cluster are defined in this file.

Configuring Ceph.yaml is tricky and needs to be done carefully. This is because we are configuring the partitions even before installing operating system on it. Also depending on the configuration whether you are using C240M4 LFF or C240M4 SFF the configuration changes.

The way disk ordering is done is inconsistent. However for ceph to work we need a consistent way of disk ordering. Post boot we can setup the disk labels by by-uuid or by-partuuid. However, in RHEL-OSP Director these have to be done before. [Bug 1253959](https://bugzilla.redhat.com/show_bug.cgi?id=1253959) is being tracked for this issue and is supposed to be fixed in later versions.

This is also a challenge to use JBOD's in Ceph, the conventional way. Using RAID-0 Luns in place of JBOD's is equally challenging. The Lun ID's have to be consistent every time a server reboots. The order that is deployed in UCS is also unpredictable. Hence following workarounds are evolved on the configuration to meet these requirements. The internal SSD drives in both C240 LFF and SFF models will not be used as they are not visible to the RAID controller in the current version of UCSM and will pose challenges to RHEL-OSP Director (they are visible to BIOS, Luns cannot be carved out as RAID controller does not see them and they appear as JBOD's to the kernel thus breaking the LUN and JBOD id's).

<span>
  <br>
</span>

 **Figure 13**Cisco UCS C240 M4 – Large Form Factor with 12 Slots

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_180.jpg)

**Figure 14**Cisco UCS C240 M4 – Small Form Factor with 24 Slots

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_181.jpg)

As mentioned earlier, storage profiles will be used from UCS side on these servers:

1.Make sure that you do not have local disk configuration policy in UCS for these servers.

2.Create storage profile, disk group policy as below under the template. There will be one Disk group policy for each slot. One policy for RAID-10 for the OS luns and one policy each of RAID-0 for the remaining.

3.Navigate to Create Storage Profile -> Create Local Lun -> Create Disk Group Policy (Manual) -> Create Local disk configuration. This will help in binding the disk slot to each lun created.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_182.jpg)

4.Create first the boot LUN from the first 2 slots and then apply. This will give LUN-0 to boot luns.

5.Create the second and third LUNs from the SSD slots (as in C240M4 LFF ). This would create RAID-0 luns, LUN-1 and LUN-2 on the SSD disks.

6.The rest of the LUNs can be created and applied in any order.

7.With the above procedure, we are assured that LUN-0 is for Operating system, LUN-1 and LUN-2 for SSD's and the rest for HDD's. This in turn decodes to /dev/sda for boot lun, /dev/sdb for SSD1 and /dev/sdc for SSD2 and the rest for HDD's.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Do not apply all the luns at the same time in the storage profile. First apply the boot lun, which should become LUN-0, followed by the SSD luns and then the rest of the HDD luns. Failure to comply with the above, will cause lun assignment in random order and heat will deploy on whatever the first boot lun presented to it.

Follow a similar procedure for C240 SFF servers too. A minimum of 4 SSD journals recommended for C240M4 SFF. The first two SSD luns with 5 partitions and the rest two with 4 partitions each.

Implementing Red Hat Linux OpenStack Director to successfully deploy Ceph on these disks need gpt label pre-created. This can be achieved by including wipe_disk.yaml file which creates these labels with sgdisk utility. Please refer to Appendix A for details about -disk.yaml.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

In the current version there is only one ceph.yaml file on all the servers. This mapping has to be uniform across the storage servers.

While the contents of ceph.yaml in the Appendix A are self-explanatory, the following is how the mappings between SSD's and HDD's need to be done:

<span>ceph::profile::params::osds:</span>

<span>    ‘/dev/sdd’:</span>

<span>        journal: ‘/dev/sdb1’</span>

<span>    ‘/dev/sde’:</span>

<span>        journal: ‘/dev/sdb2’</span>

<span>    ‘/dev/sdf’:</span>

<span>        journal: ‘/dev/sdb3’</span>

<span>    ‘/dev/sdg’:</span>

<span>        journal: ‘/dev/sdb4’</span>

<span>    ‘/dev/sdh’:</span>

<span>        journal: ‘/dev/sdc1’</span>

<span>    ‘/dev/sdi’:</span>

<span>        journal: ‘/dev/sdc2’</span>

<span>    ‘/dev/sdj’:</span>

<span>        journal: ‘/dev/sdc3’</span>

<span>    ‘/dev/sdk’:</span>

<span>        journal: ‘/dev/sdc4’</span>

The above is an example for C240M4 LFF server. Based on the LUN id's created above /dev/sdb and /dev/sdc are journal entries. Four entries for each of these journal directs RHEL-OSP to create 4 partitions on each SSD disk. The entries on the left are for HDD disks.

A similar approach can be followed for SFF servers.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Th ceph.yaml was copied to /usr/share/openstack-tripleo-heat-templates/puppet/hieradata/

The parameters section specifies the parameters.

N1000vVSMIP: The Virtual Supervisor Module IP. This should be an address on the internal API network, outside of the assigned DHCP range to prevent conflicting ips.

N1000vPacemakerControl: True in HA configuration

N1000vVSMPassword: 'Password' – The password for N1KV

N1000vVSMHostMgmtIntf: br-mgmt

N1000vVSMVersion: Leave specified as an empty string- "

N1000vVEMHostMgmtIntf:vlan100, the Internal API VLAN

N1000vUplinkProfile: '{eth1: system-uplink,}'. This should be the interface connected to the tenant network. The current version does not support bridges. Refer to the limitations of UCS Manager plugin in <http://docwiki.cisco.com/wiki/OpenStack/UCS\_Mechanism\_Driver\_for\_ML2\_Plugin\_Kilo>. As both plugins are in the setup, this has to remain as eth1.These will be revisited in our next release cycle.

NetworkUCSMIp: UCS Manager IP

NetworkUCSMHostList: Mapping between tenant mac address derived from UCS with Service profile name, comma separated. This list has to be built for all the compute and controller nodes.

This will list both the Nexus switches details, their ip's and passwords.

Servers: The list should specify the interface MAC of each controller and compute and the port-channel numbers created on the Nexus switch.

NetworkNexusManagedPhysicalNetwork physnet-tenant, the parameter you pass in the Overcloud deploy command

NetworkNexusVlanNamePrefix: 'q-' These are the vlans's that will be created on the switches

NetworkNexusVxlanGlobalConfig: false. Vxlan is not used and is not validated as part of this CVD

NeutronServicePlugins: Leave the default string as is. Any typo's may create successfully Overcloud but will fail to create VM's later.

NeutronTypeDrivers: vlan. The only drivers validated in this CVD.

NeutronL3HA: 'false' The current n1kv version does not support L3 HA. This will be revisited in the next revision.

NeutronNetworkVLANRanges: 'physnet-tenant:250:749' The range you are passing to Overcloud deploy.

The controllerExtraConfig parameters are tunables. These workers reside in /etc/neutron.conf file. Only the parameters mentioned in Appendix A are validated.

Overcloud deployment can be customized with pre and post commands. Please refer Red Hat OpenStack Director documentation for detailed information.

For pre-configuration, the following are supported

<span>OS::TripleO::ControllerExtraConfigPre</span>

<span>OS::TripleO::ComputeExtraConfigPre</span>

<span>OS::TripleO::CephStoragextraConfigPre</span>

<span>OS::TripleO::NodeExtraConfigPre</span>

wipe_disks.yaml is configured as part of firstboot to create gpt lables on Storage node disks.

Customizing post-Configuration, is done through OS::TripleO::NodeExtraConfigPost. These can be applied as additional configurations. The current nameserver_ntp.yaml file used in the configuration achieves these by using Heat SoftwareConfig types.

In Appendix B, the yaml files used in the second pod, without floating IP's is included. Only the files that were different like network-environment.yaml etc are included.

To perform the pre-installation checks, complete the following steps:

<span>1.</span>

Download the network-environment-validator.py from github; <https://github.com/rthallisey/clapper/blob/master/network-environment-validator.py>

<span class="MsoHyperlink">
  <span>.</span>
</span>

a.Download python-ipaddress from the web and install it (rpm –ivh <rpmname> ), the one used on the configuration was python-ipaddress-1.0.7-4.el7.noarch.rpm.

b.Validate the yaml files as shown below:

<span>cd /home/stack/templates as stack user</span>

<span>python network-environment-validator.py -n network-environment.yaml</span>

<span>DEBUG:__main__:</span>

<span>parameter_defaults:</span>

<span>  ControlPlaneDefaultRoute: 10.22.110.26</span>

<span>  ControlPlaneSubnetCidr: ‘24’</span>

<span>  DnsServers: [8.8.8.8, 8.8.4.4]</span>

<span>…………………</span>

<span>………………..</span>

<span>———-SUMMARY———-</span>

<span>SUCCESSFUL Validation with 0 error(s)</span>

<span>[stack@osp7-director templates]$</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

If you receive any errors, stop here and fix the issue(s).

2.Run ironic node-list to check that all the servers are available, powered off and not in maintenance.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_183.jpg)

While understanding the reason why a server is not as listed above, you may use ironic APIs to change the state if they are not in the desired state:

After sourcing stackrc file;

<span>ironic node-set-power-state &lt;uuid&gt; off</span>

<span>ironic node-set-provision-state &lt;uuid&gt; provide</span>

<span>ironic node-set-maintenance &lt;uuid&gt; false</span>

<span>In case of larger deployments, the default values of max resource per stack may not be sufficient.</span>

3.Maximum resources allowed per top-level stack. (integer value)

<span>#max_resources_per_stack = 1000</span>

Update the value to a higher number. In a pod with 35 nodes, we had to bump up this value to 10000 and restart heat engine.

<span>systemctl restart openstack-heat-engine.service</span>

4.Update Ceph timeout values. This issue was noticed on the configuration in particular for larger deployments. This is per updates in bug 1250654.

<span>cd /usr/share/openstack-tripleo-heat-templates/puppet/manifests</span>

5.Backup the files:

<span>[root@osp7-director manifests]# ls *.old</span>

<span>overcloud_cephstorage.pp.old  overcloud_controller_pacemaker.pp.old  overcloud_controller.pp.old</span>

e
dit overcloud_cephstorage.pp

<span>[root@osp7-director manifests]# diff overcloud_cephstorage.pp overcloud_cephstorage.pp.old </span>

<span>24,27d23</span>

<span>&lt; Exec {</span>

<span>&lt; timeout =&gt; 9000,</span>

<span>&lt; }</span>

<span>This is just before the line </span>

<span>if str2bool(hiera(’ceph_osd_selinux_permissive’, true)) {</span>

edit overcloud_controller.pp

<span>[root@osp7-director manifests]# diff overcloud_controller.pp overcloud_controller.pp.old </span>

<span>35,38d34</span>

<span>&lt; Exec {</span>

<span>&lt; timeout =&gt; 9000,</span>

<span>&lt; }</span>

<span>This is just before the line </span>

<span>if hiera(’step’) &gt;= 2 {</span>

edit overcloud_controller_pacemaker.pp

<span>[root@osp7-director manifests]# diff overcloud_controller_pacemaker.pp overcloud_controller_pacemaker.pp.old </span>

<span>38,41d37</span>

<span>&lt; Exec {</span>

<span>&lt; timeout =&gt; 9000,</span>

<span>&lt; }</span>

<span>This is just before the line</span>

<span>if hiera(’step’) &gt;= 1 {</span>

6.Reboot the Undercloud node.

With the templates in place, Overcloud deploy can run the command mentioned in Appendix A. OpenStack help Overcloud deploy will show all the arguments that can be passed to the deployment command.

A snippet is provided below:

<span>#!/bin/bash</span>

<span>openstack overcloud deploy –templates \</span>

<span>-e /usr/share/openstack-tripleo-heat-templates/overcloud-resource-registry-puppet.yaml \</span>

<span>-e /usr/share/openstack-tripleo-heat-templates/environments/network-isolation.yaml \</span>

<span>-e /home/stack/templates/network-environment.yaml \</span>

<span>-e /home/stack/templates/storage-environment.yaml \</span>

<span>-e /home/stack/templates/cisco-plugins.yaml \</span>

<span>-e /home/stack/templates/post_config.yaml \</span>

<span>–control-flavor control –compute-flavor compute –ceph-storage-flavor CephStor-age \</span>

<span>–compute-scale 6 –control-scale 3  –ceph-storage-scale 3 \</span>

<span>…………………</span>

The following are a few that need to be noted:

<span>–control-flavor control –compute-flavor compute –ceph-storage-flavor CephStorage</span>

The following are the flavors created earlier and associated with the nodes. Check Create and Set Flavors above:

ntp server is the server name to be used in the overcloud /etc/ntp.conf file

neutron-network-type and neutron-tunnel-type are vlan.

neutron-network-vlan-ranges physnet-tenant:250:749,floating:160:160\. Here vlan ranges from 250 to 749 are reserved for tenants, while vlan 160 is for floating ip network.

rhel-reg, reg-method <portal|Satellite> –reg-org <org id> –reg-activation-key <activation_key>. These help to register the servers with Red Hat Network.

Verbose, debug and log files are self-explanatory.

After successful deployment, the deploy command should show you the following:

<span>DEBUG: os_cloud_config.utils.clients Creating nova client.</span>

<span>Overcloud Endpoint: <a href="http://172.22.215.91:5000/v2.0/">http://172.22.215.91:5000/v2.0/</a></span>

<span>Overcloud Deployed</span>

<span>DEBUG: openstackclient.shell clean_up DeployOvercloud</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Write down the endpoint URL to launch the dashboard later. This completes Overcloud deployment.

Overcloud deployment may fail for several reasons. Either because of a human error, for example, passing incorrect parameters or erroneous yaml configuration files or timeouts or bug. It is beyond the scope of this document to cover all of the possible failures. However, a few scenarios that were encountered on the configuration with explanations are provided in the 

<span class="MsoHyperlink">
  <span>Troubleshooting</span>
</span>

 section of this document.

To perform the post deployment process, complete the following steps:

1.Run nova list and login as heat-admin to each host:

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_184.jpg)

<span>[stack@osp7-director ~]$ </span>

<span>for i in $(nova list | awk ‘/ACTIVE/ {print $12}’|cut -d “=” -f2 ); </span>

<span>ssh -l heat-admin -o StrictHostKeyChecking=no $i “touch /tmp/abc; ls -l /tmp/abc”</span>

<span>done</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

A command like the one listed above will validate that all the servers are up and running.

2.Check that the servers are registered with Red Hat Network.

subscription-manager status should reveal the status of this registration. [Bug 1299795](https://bugzilla.redhat.com/show_bug.cgi?id=1299795) reports this issue. Please follow the workaround to register the servers in case they are not.

3.Check ntp and dns entries:

<span> /etc/ntp.conf and /etc/resolv.conf for appropriate entries. </span>

[4.Issues with Ceph:]()

a.OSD Journal Size.

[Bug 1297251](https://bugzilla.redhat.com/show_bug.cgi?id=1297251) pauses a successful deployment of Ceph. The osd_journal_size is ignored from the parameter file and Ceph osd's will be reported down.

With the changes made to wipe_disk yaml, we are creating 20GB partitions for journals. However the ceph.conf file needs to be updated as follows on all the controllers and storage nodes

<span>osd_journal_size=20000</span>

Update ceph.conf on all the controllers and Ceph storage nodes

Restart monitors on all controller nodes

<span>/etc/init.d/ceph restart mon</span>

Activate the ceph-disks on storage nodes.

<span>for i in $(nova list | grep “cephstorage” |  awk ‘/ACTIVE/ {print $12}’|cut -d “=” -f2 )</span>

<span>ssh -l heat-admin $i ‘sudo ceph-disk activate-all’ ;</span>

<span>done</span>

b.Default pg num value.

Change the default pg num values for Ceph.

The current RHEL-OSP Director supports only pg_num=128, the default placement groups. [Bug 1283721](https://bugzilla.redhat.com/show_bug.cgi?id=1283721) discusses this limitation. This default value may have to be updated depending on the number of OSD's in the cluster.

Number of placement groups = ( OSD's x 100 ) / Replication factor (3)

<http://docs.ceph.com/docs/master/rados/operations/placement-groups/>

As per the above formula for 24 OSD's it is 2400/3 or 800 PG's for the cluster. Considering this has to be to the power of 2, we will create 1024 PG's in the cluster. However RHEL-OSP creates 4 pools by default. This means 256 PG's for each pool.

In case you are using C240M4S, PG's have to be calculated for 54 OSD's.

<span>[root@overcloud-cephstorage-2 ~]# ceph osd lspools </span>

<span>4 rbd,5 images,6 volumes,7 vms,</span>

The pools will be recreated with 256 PG's in each as shown below:

Set the placement groups as shown below;

<span>for i in rbd images volumes vms; do</span>

<span>ceph osd pool set $i pg_num 256;</span>

<span>sleep 30</span>

<span>ceph osd pool set $i pgp_num 256;</span>

<span>sleep 30</span>

<span>done</span>

5.Query the pools and tree.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_185.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_186.jpg)

6.Sporadic issues on N1000V

The following race condition is observed occasionally in the installs. Please verify that you are not hitting this issue before moving ahead. Neutron will fail to start and pcs status may be erring out if this condition exists. Please verify the following and correct as indicated if you encounter this issue.

First, check on VSM if there are any duplicated default profiles by running the following command. If there are only two profiles and they have different descriptions, nothing needs to be done. If, as shown below (duplicate descriptions highlighted for clarity), there are three or more profiles and the descriptions repeat, then one of the duplicates needs to be removed.

<span>vsm-p# show nsm network segment pool</span>

<span>nsm network segment pool 25c5f0b9-9ae7-45b7-b4a6-2e3a418a52af</span>

<span>
  <span>description default-vxlan-np</span>
</span>

<span>  uuid 25c5f0b9-9ae7-45b7-b4a6-2e3a418a52af</span>

<span>  member-of logical network 25c5f0b9-9ae7-45b7-b4a6-2e3a418a52af_log_net</span>

<span>nsm network segment pool 8df170c6-1bf5-4a42-bf2e-9b075b1b5537</span>

<span>  description default-vlan-np </span>

<span>  uuid 8df170c6-1bf5-4a42-bf2e-9b075b1b5537</span>

<span>  member-of logical network 8df170c6-1bf5-4a42-bf2e-9b075b1b5537_log_net</span>

<span>nsm network segment pool d792329d-6215-4a97-95fd-6dbac4cb220f</span>

<span>
  <span>description default-vxlan-np</span>
</span>

<span>  uuid d792329d-6215-4a97-95fd-6dbac4cb220f</span>

<span>  member-of logical network d792329d-6215-4a97-95fd-6dbac4cb220f_log_net</span>

After confirming this is the issue you are facing, select one of the duplicate network profiles to be deleted. For this example, we will use the first duplicate "default-vxlan-np" profile with uuid- 25c5f0b9-9ae7-45b7-b4a6-2e3a418a52af. Replace this value as needed for your deployment in the commands below.

Connect to mysql and schema ovs_neutron neutron and verify that the three profiles are also seen in mysql:

<span>MariaDB [ovs_neutron]&gt; select  articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii from cisco_ml2_n1kv_network_profiles;</span>

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_187.jpg)

The above two queries return 3 rows.

There should be only one row for each vlan and vxlan entries in the table and the id from cisco_ml2_n1kv_network_profiles should match with id from the above command showing nsm network segment pools.

Delete the duplicate from VSM first

<span>conf t</span>

<span>no nsm network segment pool 25c5f0b9-9ae7-45b7-b4a6-2e3a418a52af</span>

<span>copy running startup</span>

Then, delete from mysql database

<span>mysql –e “delete from ovs_neutron.cisco_ml2_n1kv_network_profiles where id=’25c5f0b9-9ae7-45b7-b4a6-2e3a418a52af’;</span>

Finally, restart neutron services on all the 3 controller nodes

<span>systemctl daemon-reload</span>

<span>systemctl restart neutron-server.service</span>

7.Issue with Neutron Network VLAN ranges.

This issue is a regression from RHEL-OSP 7.1 Please refer bug [1297975](https://bugzilla.redhat.com/show_bug.cgi?id=1297975). Only the first bridge makes its entry in network_vlan_ranges in /etc/neutron/plugin.ini. This needs to be as referred in overcloud deploy command:

<span>–neutron-network-vlan-ranges physnet-tenant:250:749,floating:160:160</span>

Update /etc/neutron/plugin.ini on all the three controller nodes, with the correct string

From

<span>network_vlan_ranges =physnet-tenant:250:749 </span>

<span>network_vlan_ranges =physnet-tenant:250:749,floating:160:160</span>

Restart neutron network as:

<span>systemctl daemon-reload</span>

<span>systemctl restart neutron-server.service</span>

Also check the status of pcs resources as:

<span>pcs resource cleanup </span>

<span>sleep 15</span>

<span>pcs status</span>

<span>pcs status | egrep –i “error|stop”</span>

To perform the post-deployment configuration, complete the following steps:

1.Start fence_cisco_ucs.

Run fence_cisco_ucs and pass the UCSM IP and passwords to it. Openstack_Controller_Node[1,2,3] are the service profile names for the controllers. Replace the string accordingly.

<span>[root@overcloud-controller-0 ~]# for i in 1 2 3</span>

<span>fence_cisco_ucs –ip=10.22.100.5 –username=admin –password=UCSManagerPassword \</span>

<span>–plug=“Openstack_Controller_Node${i}” –missing-as-off  –action=on –ssl-insecure -z;</span>

<span>done</span>

<span>Success: Powered ON</span>

<span>Success: Powered ON</span>

<span>Success: Powered ON</span>

In case you have defined sub organizations in UCS for the service profiles instead of root, the syntax above will slightly change as below:

<span>[root@overcloud-controller-0 ~]# for i in 1 2 3</span>

<span>fence_cisco_ucs –ip=10.22.100.5 –username=admin –password= UCSManagerPassword \</span>

<span>–plug=“Openstack_Controller_Node${i}” –suborg=“/org-OSP7-Deploy/”–missing-as-off  –action=on –ssl-insecure -z;</span>

<span>done</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

The OSP7-Deploy is the suborg name.

Check the status:

<span>[root@overcloud-controller-0 ~]# for i in 1 2 3</span>

<span>fence_cisco_ucs –ip=10.22.100.5 –username=admin –password= UCSManagerPassword \</span>

<span>–plug=“Openstack_Controller_Node${i}” –missing-as-off  -o status –ssl-insecure -z;</span>

<span>done </span>

<span>Status: ON</span>

<span>Status: ON</span>

<span>Status: ON</span>

Modify the above with suborg name if you are using suborg in UCS.

2.Configuring PaceMaker.

Before proceeding with pacemaker configuration, it is necessary to understand the relationship between the service profile names in UCS with the node names dynamically created by OpenStack as part of Overcloud deployment.

a.Either login through the Console or extract from /etc/neutron/plugin.ini.

Plugin.ini will be updated by Cisco Plugins that have this information. Open /etc/neutron/plugin.in file and go to the end of the file. Extract the controller syntax.

overcloud-controller-1.localdomain:Openstack_Controller_Node1,

overcloud-controller-0.localdomain:Openstack_Controller_Node2,

overcloud-controller-2.localdomain:Openstack_Controller_Node3

The mapping is controller-0 is mapped to Service Profile Controller_Node2 and so on.

b.Create a shell script as below with the following information and execute it

<span>#/bin/bash</span>

<span># Note that ‘;’ as a separator instead of ‘,’ from plugin.ini</span>

<span>sudo pcs stonith create ucs-fence-controller fence_cisco_ucs \</span>

<span>pcmk_host_map=“overcloud-controller-1:Openstack_Controller_Node1;overcloud-controller-0:Openstack_Controller_Node2;overcloud-controller-2:Openstack_Controller_Node3” \</span>

<span>ipaddr=10.22.100.5 login=admin passwd=&lt;password&gt; ssl=1 ssl_insecure=1 op monitor interval=60s</span>

<span>sleep 3;</span>

<span>pcs stonith update ucs-fence-controller meta failure-timeout=300s </span>

<span>pcs property set cluster-recheck-interval=300s</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Change the pcs stonith command as below for suborg's.

<span>sudo pcs stonith create ucs-fence-controller fence_cisco_ucs \</span>

<span>pcmk_host_map=“overcloud-controller-1:Openstack_Controller_Node1;overcloud-controller-0:Openstack_Controller_Node2;overcloud-controller-2:Openstack_Controller_Node3” \</span>

<span>suborg=“/org-OSP7-Deploy/” ipaddr=10.22.100.5 login=admin passwd=&lt;password&gt; \ </span>

<span>ssl=1 ssl_insecure=1 op monitor interval=60s</span>

<span>sleep 3;</span>

<span>pcs stonith update ucs-fence-controller meta failure-timeout=300s </span>

<span>pcs property set cluster-recheck-interval=300s</span>

c.Run the following:

<span>[root@overcloud-controller-0 ~]# sudo pcs property set stonith-enabled=true </span>

<span>[root@overcloud-controller-0 ~]# pcs resource cleanup </span>

<span>Waiting for 1 replies from the CRMd. OK</span>

d.Validate the configuration after running the above commands:

<span>sudo pcs stonith show ucs-fence-controller</span>

<span>sudo pcs property show:</span>

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_188.jpg)

3.Configure n1kv.

Run PCS status on any one of the controller nodes to determine the running primary and secondary VSM's:

<span>[root@overcloud-controller-0 ~]# pcs status | grep -i vsm </span>

<span> vsm-s  (ocf::heartbeat:VirtualDomain): Started overcloud-controller-0</span>

<span> vsm-p  (ocf::heartbeat:VirtualDomain): Started overcloud-controller-1</span>

Run sudo ovs-vsctl show > /tmp/1 and look for the following tag on controller node. This should be the vlan100, the internal API network VLAN configured earlier in cisco-plugins for n1kv.

```
   <span>Port br-mgmt-vsm-br</span>
```

<span>            tag: 100</span>

<span>            Interface br-mgmt-vsm-br</span>

<span>                type: patch</span>

<span>                options: {peer=vsm-br-br-mgmt}</span>

<span>The primary VSM is running on controller-1\. Login to controller-1</span>

<span>[stack@osp7-director ~]$ ssh -l heat-admin 10.22.110.55 </span>

<span>Last login: Mon Jan 18 21:20:22 2016 from 10.22.110.26</span>

<span>[heat-admin@overcloud-controller-1 ~]$ sudo -i</span>

<span>[root@overcloud-controller-1 ~]# virsh list </span>

<span> Id    Name                           State</span>

<span>—————————————————-</span>

<span> 2     vsm-p                          running</span>

<span>[root@overcloud-controller-1 ~]# virsh console 2</span>

<span>Connected to domain vsm-p</span>

<span>Escape character is ^]</span>

<span>Nexus 1000v Switch</span>

<span>vsm-p login: admin</span>

<span>Password:  Enter password for VSM. The default password is ‘Password’</span>

<span>………………</span>

<span>vsm-p# conf terminal </span>

<span>Enter configuration commands, one per line.  End with CNTL/Z.</span>

<span>vsm-p(config)# show module </span>

<span>Mod  Ports  Module-Type                       Model               Status</span>

<span>—  —–  ——————————–  ——————  ————</span>

<span>1    0      Virtual Supervisor Module         Nexus1000V          active *</span>

<span>2    0      Virtual Supervisor Module         Nexus1000V          ha-standby</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

This shows that the second VSM is ha-standby mode. For any operations make sure that it is in ha-standby mode and not *powered* mode. Please wait few seconds to make sure that secondary is in ha-standby mode. Show module should also show all the controller and compute nodes.

<span>vsm-p(config)# port-profile type vethernet default-pp</span>

<span>vsm-p(config-port-prof)# no shut</span>

<span>vsm-p(config-port-prof)# state enabled</span>

<span>vsm-p(config-port-prof)# publish port-profile</span>

<span>vsm-p(config-port-prof)# exit</span>

<span>vsm-p(config)# port-profile type ethernet system-uplink</span>

<span>vsm-p(config-port-prof)# no shut</span>

<span>vsm-p(config-port-prof)# state enabled</span>

<span>vsm-p(config-port-prof)# switchport mode trunk</span>

<span>vsm-p(config-port-prof)# end</span>

<span>vsm-p# show module</span>

<span>Mod  Ports  Module-Type                       Model               Status</span>

<span>—  —–  ——————————–  ——————  ————</span>

<span>1    0      Virtual Supervisor Module         Nexus1000V          active *</span>

<span>2    0      Virtual Supervisor Module         Nexus1000V          ha-standby</span>

<span>vsm-p# copy running startup</span>

<span>………</span>

<span>………</span>

<span>vsm-p# reload</span>

<span>This command will reboot the system. (y/n)?  [n] y</span>

<span>Broadcast message from root (ttyS0) (Tue Jan 19 07:14:13 2016):</span>

<span>The system is going down for reboot NOW!</span>

<span>Enter configuration commands, one per line.  End with CNTL/Z.</span>

<span>2016 Jan 19 07:15:30 vsm-p vem_mgr[2368]: %VEM_MGR-2-MOD_ONLINE: Module 11 is online</span>

<span>&lt;Press Enter &gt;</span>

<span>Nexus 1000v Switch</span>

<span>vsm-p login: admin</span>

<span>Password: </span>

<span>Cisco Nexus Operating System (NX-OS) Software</span>

<span>TAC support: <a href="http://www.cisco.com/tac">http://www.cisco.com/tac</a></span>

<span>vsm-p# show module | grep “Supervisor” </span>

<span>1    0      Virtual Supervisor Module         Nexus1000V          active *</span>

<span>2    0      Virtual Supervisor Module         Nexus1000V          ha-standby</span>

<span>The system is ready to be used.</span>

<span>vsm-p# conf terminal </span>

<span>Enter configuration commands, one per line.  End with CNTL/Z.</span>

<span>vsm-p(config)# svs switch edition advanced</span>

<span>vsm-p(config)# feature l3forwarding</span>

<span>vsm-p(config)# 2016 Jan 19 07:18:36 vsm-p fwm[2387]: %FWM-2-L3FWD_CHANGE_NOTIFICATION: Layer 3 Forwarding feature has been enabled. Reload the VSM.</span>

<span>vsm-p(config)# copy running startup</span>

<span>……..</span>

<span>……..</span>

<span>vsm-p(config)# reload </span>

<span>This command will reboot the system. (y/n)?  [n] y</span>

<span>2016 Jan 19 07:19:16 vsm-p vshd[4654]: %PLATFORM-2-PFM_SYSTEM_RESET: Manual system restart from Command Line Interface</span>

<span>Broadcast message from root (ttyS0) (Tue Jan 19 07:19:17 2016):</span>

<span>The system is going down for reboot NOW!</span>

N1KV setup is complete.

To launch the dashboard URL created after successful installation of Overcloud, complete the following steps:

1.Go to <http://172.22.215.91> (URL provided after Overcloud deployment) and login as admin and use the password created in the overcloudrc file (under $HOME of stack user).

2.Log into the system and navigate the tabs for any errors.

3.Update the system defaults.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_189.jpg)

[Functional Validation]()includes the following:

<span>·</span>

Navigating the dashboard across the admin, project, users tab to spot any issues

<span>·</span>

Creating Tenants, Networks, Routers and Instances.

<span>·</span>

Create Multiple Tenants, multiple networks and instances within different networks for the same tenant and with additional volumes with the following criteria:

<span>—</span>

Successful creation of Instances through CLI and validated through dashboard

<span>—</span>

Login to VM from the console.

<span>—</span>

Login to VM's through Floating IP's.

<span>—</span>

Checking inter instance communication for VM's within the same network and VM's in a different network for the same tenants and with password less authentication.

<span>—</span>

Reboot VM's and checking for VM evacuation

<span>—</span>

Check for the VLAN's created both in UCSM and also on the Nexus switches. The VLAN's should be available globally and also on the both port-channels created on each switch:

Login to Nexus switch

conf term

show vlan | grep q-

show running-config interfrace port-channel 17-18

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

The basic flow of creating and deleting instances through command line horizon dashboard were tested. Creating multiple tenants and VLAN provisioning across Nexus switches and Cisco UCS Manager were verified while adding and deleting the instances.

For detailed information about validating Overcloud, refer to the Red Hat Linux Openstack Platform guide.

Rally benchmarking tool was used to check the solution for scale testing. This tool tells how OpenStack performs, notably under simulated load at scale. Later after the test is completed, Rally generates an HTML report based on captured data. For more details about this tool please refer 

[<span>https://wiki.openstack.org/wiki/Rally</span>](https://wiki.openstack.org/wiki/Rally)

The main purpose of running the tool was to generate a workload close to cloud but not to capture some benchmark data. Hence a limited amount of tuning has been attempted on the openstack side. None of the default kernel parameters like ulimits, pid_max, libvirtd or nova parameters like osapi_max_limit or neutron api workers etc were modified, nor was any attempt done on Ceph side to extract the best performance from the configuration. It has to be noted that these may have to be tweaked to get the best results. This was just an attempt to use a tool to create VM's simultaneously but not to do a real benchmarking exercise.

Rally was installed and setup on OpenStack Director Node.

The step-by-step install instructions for Rally is documented in <https://rally.readthedocs.org/en/latest/tutorial.html>

<span class="MsoHyperlink">
  <span>A test bed with 3 controllers, 19 computes and 3 Ceph storages nodes was built to test this setup. The hardware and software specifications are same as mentioned earlier in this document. The following parameters were changed for rally testing</span>
</span>

.

**<span>Variable name</span>**     | **<span>Location</span>**               | **<span>Value</span>**
---------------------------------- | --------------------------------------- | ----------------------
<span>show_image_direct_utl</span> | <span>/etc/glance/glance-api.cfg</span> | <span>True</span>
<span>vif_plugging_is_fatal</span> | <span>/etc/nova/nova.conf</span>        | <span>True</span>
<span>api_workers</span>           | <span>/etc/neutron/plugin.ini</span>    |
<span>Time out Client</span>       | <span>/etc/haproxy/haproxy.cfg</span>   | <span>180</span>

The following section describes different test case scenarios selected to test RHEL-OSP 7 environment. In this test, following benchmarking scenarios have been given to simulate multi-tenant workload at scale for VM and volume provisioning. Each benchmarking scenario will perform a small set of atomic operations, hence testing the simple use case.

1.Provision 1,000 instances from 1,000 bootable Ceph volumes.

2.Use Cirros-0.3.4-x86_64-disk.img.

3.Create 200 tenants with 2 users per tenants

4.Create and authenticate these VMs.

5.Each tenant will have 1 Neutron network, a total of 200 Neutron networks. Cisco UCS and Nexus plugins provision these neutron networks mapped to VLAN with segmentation id in Cisco UCS manager and Nexus 9000 switches.

6.Tenant quotas for Cinder, Nova, and Neutron are set to unlimited for simplicity in this test to avoid any failures just to avoid any quota issues while booting the VM's.

7.Concurrency of 3 has been used in Rally's task configuration. Rally script will be creating a constant load by running the scenarios given for a fixed number of times, possibly in parallel iteration and therefore simulating the concurrent requests from different users and tenants.

8.Provisioned 2,000 instances from bootable volume based on the similar scenarios mentioned above.

**Hardware**

Following hardware has been used to run the Rally tests.

<span>·</span>

No of compute nodes (B200 M4 Servers): 19

<span>·</span>

Following hardware resources were available in the test bed to run the rally test described above with or without over-commitment ratios.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_190.png)

<span>·</span>

Number of Ceph Nodes: 3 ( 43 TB of usable space )

<span>·</span>

Number of Controllers: 3

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

vCPU default over-commitment ratio is 1:16\. However, it is recommended to use 1:4\. It can be modified in /etc/nova/nova.conf cpu_allocation_ratio variable. With cpu_allocation_ratio of 4, 2913 (728 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii 4) instances can be created.

Default m1.tiny flavor size was used for guest instances in this test.

Rally runs different types of scenarios on the information provided in json format. Although Rally offers several different combination of scenarios to choose from, here we are focusing on testing how the system scales in a multi-tenant environment where each tenant environment has a given set of strategies

Below is the Rally task configuration used in validation. It takes different parameters for customization to run on different sets of scenarios. However, defaults are also set. Below .json file runs the NovaServers.boot_server_from_volume scenario.

In this reference environment, benchmarking contexts have been setup. Contexts in Rally allow to stage different types of environment in which benchmarking scenario is launched. In this test, environment such as number of tenants, number of users per tenant, number of neutron network per tenant, and users quota were specified.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Sample json file used for testing is provided in Appendix A.

The following parameters used in the json file are provided for reference

a.Flavor : The size of the guest instance, e.g. m1.tiny

b.Image : the name of the image file used for guest instances

c.volume_size : size of bootable volume, e.g. 10 GB

d.quotas : Quota requirement of each tenant. For unlimited resources, value of -1 for cores, ram, volume, network, ports, and so on has been given.

e.Tenants : Number of tenants to be created.

f.users_per_tenant : Number of users created within each tenant.

g.concurrency : amount of guest instances to run on each iteration

h.times : Total number of iterations to be performed.

Rally script was executed by running the following command

<span>rally –verbose  task start boot-from-volume.json –task-args \</span>

<span> ’{“flavor_name”: “m1.tiny”,“volume_size”: 1,“number_of_vms”: 1000, “image_name”: “cirros”,“concurrency”:3,“no_of_tenants”:200,“users_per_tenants”:2}’</span>

It is recommended to start with smaller set of values for number of tenants, VMs, times, and concurrency to diagnose for any errors. Rally generates HTML based report after the task is completed as shown below. Load duration shows the time taken to run the specified scenario, while Full duration shows the total time taken by the overall benchmark task. Iteration shows how many times a specified scenario has been executed.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_191.png)

The figure above shows the time taken to provision each VM. The X-axis plots the number of VMs and the Y-axis, shows the total time taken to power-on each VM that includes both the time taken to provision the boot volume with cirros images and the nova boot time of the VM.

Based on total RAM available on 19 compute nodes with 1000 VM of 500Mb each, there is no memory swapping. Based on the above available hardware resources in the reference test bed and considering resource depletion, theoretically 4 GB can be assigned to each VM (1000 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii 4=4000 GB) without any memory over-commitment.

Very small CPU over-commitment was observed based on 728 physical cores available for 1000 instances. As mentioned earlier, the configuration had enough vCPU resources with recommended cpu_allocation_ratio of 4 to provision 1,000 instances.

Better performance and results have been observed by reducing the number of tenants and neutron network along with concurrency level.

Different results have been observed with different image sizes. It takes longer to provision bootable volume, if the image size is larger.

Limited variations have been observed in the result sets even if similar scenario is run many times because of resource depletion.

Connection error has been observed because of HAProxy timeout, HAProxy being the top layer of the stack with respect to all incoming client connections. HAProxy timeout value is increased from 30s to 180s. It is observed that default timeout value is not sufficient to handle incoming Rally client connection requests.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_192.png)

From the above it is evident that load profile has been consistent throughout the test.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_193.png)

The graph above shows the distribution in seconds versus the number of instances provisioned.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_194.png)

The graph shows the atomicity of the single Rally benchmarking operation. In this test, there were two major operations, cinder volume creation and nova boot of guest instances. It is observed that volume creation took little longer in the begining and after that it remained consistent on an average of 7 seconds. Nova boot took on the average of 9.7 seconds for a single instance. Nova boot time was also consistent. However few spikes have been noticed especially after 700 iterations. This could vary depending on other internal OpenStack API calls within the controller.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_195.png)

Out of the total time it took to boot from volume and provision the instance, 43% of the time was taken for creation of the volume, while the rest 57% for to create VM in its active state

On an average, it took 7 seconds to provision single volume. You may observe a skewed average as the first 500 were provisioned in less than 5 seconds.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_196.png)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_197.png)

The average time to nova boot was observed around 8-9 seconds.

Using the same .json file, Rally task was executed for 2,000 VM's without changing any other parameters from the json file.

<span>rally –verbose  task start boot-from-volume.json –task-args  ‘{“flavor_name”: “m1.tiny”,“volume_size”: 1,“number_of_vms”: 2000, “image_name”: “cirros”,“concurrency”:3,“no_of_tenants”:200,“users_per_tenants”:2}’</span>

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_198.png)

It took on the average of 15 seconds to provision single VM in this test. At about 1441 iteration, we encountered one failure. This particular instance failed to provision and OpenStack API call eventually timed out. It is clearly evident from the graph as well. It took more than 120 seconds on this instance and in most cases after 120 sec, OpenStack API requests time out.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_199.png)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_200.png)

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Similar to the 1000 VM tests, the volumes were consistently provisioned on an average of 6.3 seconds.

During the Rally task execution, Cisco UCSM and Nexus ML2 plugins provisioned neutron segmentation id as VLAN in automated fashion.

Nexus Ml2 plugin provisioned tenant's VLAN and allowed them in the trunk as Rally provisioned the neutron network for each tenant.

Similarly, in UCSM, the tenant's VLANs are also provisioned in the LAN cloud. Furthermore, tenant VLANs are added to the vNIC that carries tenant data traffic to the respective compute host where the instance is provisioned.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_201.png)

While the tests were not targeted as a benchmarking exercise, we can draw few conclusions that could help us to plan the infrastructure.

The over commitment and concurrency play an important role on sporadic failures. Also it depends how much burst of work load do we expect in a real production environment and then test and tune for concurrency.

Not much of tuning attempted either on nova or on Ceph while running these tests. This is an iterative exercise but could have provided more insight for extracting better performance values.

Even a few failures like timeouts will skew the result set. Hence we may have to pay attention to the median and 90th percentile figures to fairly understand the system behavior

The Ceph benchmarking tool was used in the configuration to test the scalability of storage nodes. The purpose of this testing done on this configuration as part of this CVD was not to do benchmarking but to provide steps on how to do the storage testing of nodes in OpenStack and provide some comparison data between Cisco C240M4 large and small factor servers. This is to help choose the right configuration based on the workload expected in cloud. Each of them have their own hardware characteristics and the performance data captured here should help to make an informed decision on the storage servers. However, the data presented below should not be considered as the optimal storage scalability values.

Ceph benchmarking tool (CBT) can be downloaded from <https://github.com/ceph/cbt>. It is an open source python script to test the performance of the Ceph clusters. It tests both the object and rados block devices scaling. Only Rados Block Device (rbd) tests were done. While there are three different categories for block devices testing that can be done, the most conservative, librbdfio was used in the test bed. Librbdfio, tests block storage performance of RBD volumes without KVM/QEMU configuration through librbd libraries. They give the closest approximation of KVM/QEMU performance. Please refer to the link above to configure the tool for testing.

The results obtained depend on several factors. The important ones that were included in the test bed are mentioned below.

Default value of rbd_cache is true. This was turned to false purposely to suit some of the RDBMS workloads.

The read ahead configured was as default on the disks.

<span>[root@overcloud-cephstorage-2 ~]# hdparm -a /dev/sde </span>

<span>/dev/sde:</span>

<span> read ahead     = 8192 (on)</span>

The write cache policy on the LUNs was write through which is the default.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_202.png)

The io depth in the ceph parameter was 8.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Each VM by default can do 1GB of IO throughput. There was no qemu throttling or QOS policy implemented on the setup. Few VM's each running with full capacity can saturate the storage.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

The tests were done to measure the IOPS and bandwidth as a whole on the storage cluster. This in turn will be shared by all the VM's running in the cloud. The values represent what storage can scale but not how many VM's can saturate them.

The CBT tests were done in two different installs; the first with 3 node of C240M4 Large form factor and the second with C240MS, the small form factor.

The Ceph configuration on a 3 node C240M4 LFF was as follows:

<span>·</span>

8 OSD's per node each with 6 TB HDD

<span>·</span>

2 SSD disks per node each with 4 partitions for Ceph journaling

A total usable space of around 43 TB with a replication factor of 3 on 3 nodes.

The Ceph configuration on each C240M4 SFF was as follows:

<span>·</span>

18 OSD's per node each with 1.2 TB HDD

<span>·</span>

4 SSD disks per node each with 5 partitions for Ceph journaling. Only 4 partitions from the last 2 SSD's were used as journals as the total requirement for journals was 18 and not 20.

<span>·</span>

A total usable space of around 18 TB with a replication factor of 3 on 3 nodes.

To create a virtual machine for Ceph testing, complete the following steps:

1.Create a master VM from RHEL-OSP 7.2 cloud image.

2.Install CBT and other packages as needed per web page above, on the new VM, called as Ceph master and add any additional interfaces in the VM. VM's need access to Ceph public storage as well.

3.As it is eth1 from the hypervisor through which VM communicates per RHEL-OSP configuration, you may have to temporarily open vlans in UCS on eth1 just for this testing purpose.

4.Once master image is created, suspend the VM, take a snapshot and then create multiple child VM's for actual CBT testing.

5.Configure the clients, adjust the parameters in CBT yaml file and start testing.

The following figures illustrate the results of this solution.

**Figure 15**C240M4 LFF Throughput in IO's Per Second

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_203.png)

<span>
  <br>
</span>

 **Figure 16**C240M4 LFF Bandwidth in MBPS

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_204.png)

**Figure 17**C240M4 LFF Latency in Milliseconds

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_205.png)

<span>
  <br>
</span>

 **Figure 18**C240M4 SFF Throughput in IO's Per Second

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_206.png)

**Figure 19**C240M4 SFF Bandwidth in MBPS

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_207.png)

<span>
  <br>
</span>

 **Figure 20**C240M4 SFF Latency in Milliseconds

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_208.png)

<span>·</span>

rbd_cache as false seem to have reduced the values.

<span>·</span>

librbdfio always gives lesser values over rbdfio and kernelrbd values and a conservative approach followed here.

<span>·</span>

The latency values as expected were higher at higher block sizes.

<span>·</span>

The SFF servers has more spindles and exhibits better random read and write performance at lower block sizes

<span>·</span>

The sequential read and write performance of LFF server is at par or better than the SFF servers

<span>·</span>

The 6 TB drives with 4k sectors on LFF servers observed to leverage better read ahead and hence a read performance, while the 1.2 TB drives with 512 byte sectors on SFF lagging behind.

<span>·</span>

At 4 MB block sizes, both of them exhibit a similar or very close bandwidth values.

<span>·</span>

The overall TB of usable space per Rack unit is higher on LFF servers.

<span>·</span>

Not much of CPU or Memory overhead observed during the tests. These conclude that system will have sufficient head room during failures for recovery operation. The core/spindle ratio was higher as well on these boxes.

<span>·</span>

As mentioned earlier the tests were conducted in a controlled environment. Increasing the VM's substantially without a control on IO on each might give poorer results. A separate layer of client side IO throttling has to be in place if this is the case.

<span>·</span>

CBT only checks the storage performance of the Ceph cluster. The number of VM's configured is controlled through the yaml file.

Live migration refers to the process of moving a running 

[<span>virtual machine</span>](https://en.wikipedia.org/wiki/Virtual_machine)

 between different physical machines without disconnecting the 

[<span>client</span>](https://en.wikipedia.org/wiki/Client_(computing)

) or application. Memory, storage, and network connectivity of the virtual machine are transferred from the source host to the destination host.

Live migration is crucial from operational perspective to provide continuous delivery of services running on the infrastructure. This allows for movement of the running virtual machine from one compute node to another one.

The most common use case for live migration is host maintenance - necessary for software patching or firmware/hardware/kernel upgrades. Second case is imminent host failure, like cooling issues, storage or networking problems.

Live migration helps in optimal resource placement across an entire datacenter. It allows reducing costs by stacking more virtual machines on a group of hosts to save power. What's more it is possible to lower network latency by placing combined instances close to each other. Live migration can also be used to increase resiliency and performance by separating noisy neighbors.

All available values for live_migration_flag:

<span>`live_migration_flag=VIR_MIGRATE_UNDEFINE_SOURCE, VIR_MIGRATE_PEER2PEER, VIR_MIGRATE_LIVE, VIR_MIGRATE_AUTO_CONVERGE, VIR_MIGRATE_TUNNELED`</span>

By default this flag have value:

<span>`live_migration_flag=VIR_MIGRATE_UNDEFINE_SOURCE, VIR_MIGRATE_PEER2PEER, VIR_MIGRATE_LIVE, VIR_MIGRATE_TUNNELED`</span>

This flag is configured in `nova.conf` file on each compute node. To apply change restarting nova-compute service on each node is required.

The Tunneling option provides secure migration. In this model, hypervisor creates a point-to-point tunnel and sends encrypted (AES) data. This option also uses CPU for encrypting and decrypting transferred data. Without this, the data is transmitted in raw format.Tunneling is important from the perspective of security. Encryption of all data-in-transit ensures that the data cannot be captured.

The tunneling optioned is configured by adding the following value to flag:

<span>`VIR_MIGRATE_TUNNELED`</span>

Busy enterprise workloads hosted on large sized VM's tend to dirty memory faster than the transfer rate achieved through live guest migration. If the migration don't converge it is possible to use auto-converge feature (KVM + Qemu). This feature allows to auto-detect lack of convergence and trigger a throttle-down of the memory writes on a VM. This flag speed up process of Live Migration.

All tests are based on PerfkitBenchmarker and Yahoo! Cloud Serving Benchmark (YCSB) test suite for Cassandra database. Version of PerfkitBenchmarker used in tests is v1.1.0-39-g8374cc5.

Base benchmark specification assumes a configuration that includes 24 virtual machines: 10 with YCSB client to generate load and 14 with Cassandra nodes (1 with seed node).

VMs specification:

Client node (flavor:m1.medium):

2 vCPUs

4 GB RAM

40 GB hard drive

Cassandra node (flavor:m1.xlarge)

8 vCPUs

16 GB RAM

160 GB hard drive

All Cassandra VMs are created on 

<span>the same </span>

compute node (it fits about 90% of RAM and 70% of CPU) in availability zone named 'workers'. Similar situation applies to client VMs which are created on other compute node in availability zone named 'clients'.

For creating custom availability zones it requires to execute below nova command:

_<span>`nova aggregate-create &lt;aggregate-name&gt; &lt;az-name&gt;`</span>_

To attach some host to newly created availability zone execute:

_<span>`nova aggregate-add-host &lt;az-id&gt; &lt;compute-hostname&gt;`</span>_

<span>To enable usage of availability zones additional config file (ycsb_flags.yml) needs to be placed under <em>`&lt;perfkit_dir&gt;/perfkitbenchmarker/configs/`</em> with below content:</span>

<span>cassandra_ycsb:</span>

<span>
  <br>
</span>

<span>   vm_groups:</span>

<span>       workers:</span>

<span>           vm_spec:</span>

<span>               OpenStack:</span>

<span>                       machine_type: ‘m1.xlarge’</span>

<span>                       zone: ‘workers’</span>

<span>       clients:</span>

<span>           vm_spec:</span>

<span>               OpenStack:</span>

<span>                       machine_type: ‘m1.medium’</span>

<span>                       zone: ‘clients’</span>

The oversubscription level for CPU was equal to 2.8 (virtual core count to physical core count ratio) and for the memory it was 0.93 (virtual RAM count to physical RAM count ratio).

Cassandra is the choice for testing methodology as it is interesting in perspective of the cloud and it's easily scalable using built-in mechanisms. What's more, Cassandra is more RAM intensive compared to other, more classical DB's (RDBMS), which best shows impact of Live Migration process.

Yahoo Cloud Solution Benchmark (YCSB) is a tool to generate close to real workload on database. In test configuration database is loaded by read and update operations in proportion 3:1\. Proportions are based on "[Statistical Characterization of Business-Critical Workloads Hosted in Cloud Datacenters](http://ieeexplore.ieee.org/xpl/articleDetails.jsp?reload=true&tp=&arnumber=7152512&queryText%3Dhosted)" from Delft University of Technology.

For configuring YCSB use workload file like below:

<span>workload=com.yahoo.ycsb.workloads.CoreWorkload </span>

<span>fieldcount=20</span>

<span>fieldlength=255</span>

<span>readallfields=true</span>

<span>writeallfields=true</span>

<span>readproportion=0.75 </span>

<span>updateproportion=0.25</span>

<span>scanproportion=0</span>

<span>insertproportion=0</span>

<span>requestdistribution=zipfian</span>

<span>operationcount=2100000</span>

<span>recordcount=5000000</span>

To run tests use command as below:

<span>`/bin/python2.7 /home/stack/PerfKitBenchmarker/pkb.py –num_vms=14 \</span>

<span>–run_uri=tunl4 –ycsb_timelimit=6000 –benchmarks=cassandra_ycsb –ycsb_workload_files=/home/stack/workload –ycsb_operation_count=</span>

<span>2100000</span>

<span>–flagfile=/home/stack/PerfKitBenchmarker/cg_flags –benchmark_config_file=ycsb_flags.yml –ycsb_client_vms=10 –ycsb_record_count=5000000`</span>

Flags file (cg_flags) contains OpenStack configurations with network names, used image and size of scratch disk:

<span>–cloud=OpenStack<br>–openstack_private_network=int_net<br>–openstack_public_network=ext-net<br>–image=ubuntu-14.04<br>–scratch_disk_size=20</span>

During the benchmark all VMs are migrated one by one to other compute nodes, migration time of each VM is counted and additionally various performance metrics from Cassandra cluster are collected. In the same time metrics - RAM, CPU and network traffic - from compute node containing Cassandra servers are obtained.

To start each migration use below command. Make sure to invoke migration during YCSB benchmarking 

_<span>READ/UPDATE</span>_

 operations on Cassandra. Destination compute node should not be the same as used for YCSB clients (availability zone 'clients').

<span>`nova live-migration &lt;vm-name&gt; &lt;destination-compute-name&gt;`</span>

Auto convergence is configured by adding the following value to flag:

<span>`VIR_MIGRATE_AUTO_CONVERGE`</span>

Average VM migration time in all combinations of this flags during Cassandra test based on PerfkitBenchmarker and YCSB test suite. This test suite is running in configuration of 14 Cassandra nodes and 10 clients. All Cassandra nodes were located on the same compute node (it fits about 90% of RAM and 70% of CPU) and then migrated one by one to other available computes.

**_<span>Test case</span>_**                          | **<span>Average VM migration time during Cassandra test</span>**
----------------------------------------------------- | ----------------------------------------------------------------
_<span>Tunneling enabled, converged disabled</span>_  | <span>385 sec</span>
_<span>Tunneling enabled, converged enabled</span>_   | <span>167 sec</span>
_<span>Tunneling disabled, converged disabled</span>_ | <span>60 sec</span>
_<span>Tunneling disabled, converged enabled</span>_  | <span>54 sec</span>

Based on gathered results best time of migration is possible with disabled tunneling and enabled auto converge option. It can be used just in internal or test environments because from security perspective disabling tunneling is a disadvantage and enterprise setups should avoid to disable it.

To configure this option set up below line in live_migration_flag and restart nova-compute service:

`live_migration_flag=VIR_MIGRATE_UNDEFINE_SOURCE, VIR_MIGRATE_PEER2PEER, VIR_MIGRATE_LIVE, VIR_MIGRATE_AUTO_CONVERGE`

Unencrypted LM traffic is not a flaw, but allowing to traverse that traffic on a compromised network could be. Separating the LM traffic from API traffic will resolve that issue, unfortunately such configuration is not possible in current version of OpenStack (Kilo).

Current possibilities shows that best from perspective of speed and security configuration of migration flags is to use tunneling with auto converge. To set up this edit `nova.conf` on each compute to below value in live_migration_flag and restart nova-compute service:

`live_migration_flag=VIR_MIGRATE_UNDEFINE_SOURCE, VIR_MIGRATE_PEER2PEER, VIR_MIGRATE_LIVE, VIR_MIGRATE_AUTO_CONVERGE, VIR_MIGRATE_TUNNELED`

Scaling up the POD with growing business needs is a must. As business grows we need to add both compute and storage as needed by adding more hosts.

An attempt is made to scale up compute and storage. You may have to follow the steps below with any documented workarounds to add compute and storage nodes to the cluster.

To provision the new server in UCS, complete the following steps:

1.Rack the new C240M4 server(s). There is a single ceph.yaml in the current OpenStack version. Populate the hard disks in these storage servers in the same order as they exist in other servers.

2.Attach Console and discover the storage server(s) in UCS. Factory reset to defaults if needed and make them UCS managed.

3.[Refer to this section](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.html#ServiceProfileTemplatesforCeph) for creating service profiles from Storage template. Create a new service profile from the template. Unbind the template and remove the storage policy that was attached to it earlier and associate the service profile to the server.

4.Upgrade firmware if needed.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Check the installed firmware on the new node and make sure that it is upgraded to the same version as other storage servers.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_209.jpg)

5.Create a new Storage Profile for Disks.

Before creating the storage profile, login to the equipment tab and make sure that all the new storage servers have the disks in place and they are physically on the same slots at par with other storage servers.

Since we used the storage profile earlier with other servers we cannot use them right away. The reason being the luns have to be added to the server in the same way as was done earlier. In case you are discovering more than one storage server at this stage, a single new profile created as below will serve the purpose. While creating this new storage profile, we can reuse the existing disk group configuration policies created earlier.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_210.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_211.png)

6.Go to the service profile of the new server and to the storage tab to create a new storage profile as shown below.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_212.jpg)

7.Attach this storage profile to the service profile. This will create the first boot lun LUN-0 on the server. Go back to the equipment tab and inventory/storage to check that this is the first Lun is added. This will be the boot lun LUN-0 that will be visible to the server bios. In case of multiple servers being added in this step, attach the new storage profile created above to all these service profiles. This in turn will create LUN-0 in all the nodes.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

A subsequent update to this storage profile will be propagated across all these new service profiles.

8.Go to Storage tab in UCSM and update the storage profile.

9.Create and attach SSD luns, which will be LUN-1 and LUN-2\. Wait few minutes to make sure that all the new servers get these luns in the same order, boot as LUN-0, ceph-ssd1 as LUN-1 and ceph-ssd2 disk as LUN-2.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_213.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_214.jpg)

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

This will be consistent with other servers and we can expect sda for boot lun and sdb and sdc for SSD LUNs being used with the journals.

10.Add all the HDD LUNs.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_215.jpg)

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

The steps above do not represent the actual boot order. You may have to observe the actual boot order from KVM console to verify.

If the boot disks are being repurposed and are not new, go ahead and re-initialize the boot lun through bios. Boot server, CTR-R, F2 and reinitialize the VD for the boot LUNs.

1.Get the hardware inventory needed introspection.

2.Go to the Equipment tab > Inventory > CIMC and get the IPMI address.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_216.jpg)

3.Under the same Inventory tab go to NIC subtab and get the pxe mac address of the server. The same inventory should have the CPU and memory details.

4.Specify the NIC order in the service profile. This should be the same as the other storage servers with provisioning interface as the first one.

5.Check the boot policy of the server. Validate that this is same as other storage servers. It should be LAN PXE first followed by local LUN.

To run Introspection, complete the following steps:

1.Prepare json file for introspection:

<span>stack@osp7-director ~]$ cat storage-node.json </span>

<span>  “nodes”: [</span>

<span>      “pm_user”: “admin”,</span>

<span>      “pm_password”: “password”,</span>

<span>      “pm_type”: “pxe_ipmitool”,</span>

<span>      “pm_addr”: “10.22.100.8”,</span>

<span>      “mac”: [</span>

<span>        “00:25:b5:00:00:59”</span>

<span>      “memory”: “262144”,</span>

<span>      “disk”: “400”,</span>

<span>      “arch”: “x86_64”,</span>

<span>      “cpu”: “40”</span>

2.Check IPMI Connectivity:

<span>ipmitool –I lanplus -H 10.22.100.8 -U -P &lt;password&gt; chassis power off</span>

<span>Chassis Power Control: Down/Off</span>

3.Initialize Boot Luns; in case you are reusing old disks it is recommended to initialize the boot luns.

4.Run discovery and introspection.

<span>[stack@osp7-director ~]$ openstack baremetal import –json ~/storage-node.json</span>

<span>openstack baremetal configure boot</span>

<span>openstack baremetal list</span>

<span>The new node is added as shown below:</span>

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_217.jpg)

<span>[stack@osp7-director ~]$ ironic node-set-maintenance 1132c423-7449-40b5-935a-0f989f61813f true</span>

<span>[stack@osp7-director ~]$ openstack baremetal introspection start 1132c423-7449-40b5-935a-0f989f61813f</span>

<span>[stack@osp7-director</span>
~]$ openstack baremetal introspection status 1132c423-7449-40b5-935a-0f989f61813f

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_218.png)

Repeat the steps above if you want to add multiple nodes.

Wait till the introspection is complete. The status command should yield finished as True and Error as none.

<span>ironic node-set-maintenance 1132c423-7449-40b5-935a-0f989f61813f false</span>

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_219.jpg)

5.Update node properties:

<span>[stack@osp7-director ~]$ ironic node-update 1132c423-7449-40b5-935a-0f989f61813f \</span>

<span>&gt; add properties/capabilities=’profile:CephStorage,boot_option:local’</span>

<span>[stack@osp7-director ~]$ glance image-list | grep deploy | awk ‘{print $2" " $4}’ </span>

<span>ca9667c9-de8f-4df1-95d5-b5f8b6fb8f4d <strong>
  <span>bm-deploy-kernel</span>
</strong></span>

<span>404d0e44-e5c7-4b46-8339-c451441b3f55 <strong>
  <span>bm-deploy-ramdisk</span>
</strong></span>

If missing update as below

<span>[stack@osp7-director ~]$ ironic node-update \</span>

<span>1132c423-7449-40b5-935a-0f989f61813f \</span>

<span>&gt; add driver_info/deploy_kernel=’ca9667c9-de8f-4df1-95d5-b5f8b6fb8f4d’;</span>

<span>[stack@osp7-director ~]$ ironic node-update 1132c423-7449-40b5-935a-0f989f61813f \</span>

<span>&gt; add driver_info/deploy_ramdisk=’404d0e44-e5c7-4b46-8339-c451441b3f55’;</span>

6.Check the status of added entries:

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_220.jpg)

The number of storage nodes has been incremented to 4 from 3\. Here the number '4' indicates the total number of storage nodes in Overcloud.

<span>#!/bin/bash</span>

<span>openstack overcloud deploy –templates –ceph-storage-scale 4 \</span>

<span>-e /usr/share/openstack-tripleo-heat-templates/overcloud-resource-registry-puppet.yaml \</span>

<span>-e /usr/share/openstack-tripleo-heat-templates/environments/network-isolation.yaml \</span>

<span>-e /home/stack/templates/network-environment.yaml \</span>

<span>-e /home/stack/templates/storage-environment.yaml \</span>

<span>-e /home/stack/templates/cisco-plugins.yaml \</span>

<span>-e /home/stack/templates/post_config.yaml \</span>

<span>–verbose –debug –log-file overcloud_storage-add.log</span>

Addition of nodes complete with the following message:

<span>INFO: rdomanager_oscplugin.v1.overcloud_deploy.DeployOvercloud Stack found, will be doing a stack update</span>

<span>Overcloud Endpoint: <a href="http://172.22.215.91:5000/v2.0/">http://172.22.215.91:5000/v2.0/</a></span>

<span>Overcloud Deployed</span>

<span>DEBUG: openstackclient.shell clean_up DeployOvercloud</span>

From overcloud deploy log

<span>[2016-01-28 19:12:17,735] DEBUG    cliff.commandmanager found command ‘hypervisor_stats_show’</span>

<span>…..   </span>

<span>…..</span>

<span> [2016-01-28 19:54:48,212] DEBUG    openstackclient.shell clean_up DeployOvercloud</span>

<span>The deployment script ran for 42 minutes.</span>

To perform the post-deployment health checks, complete the following steps:

1.Check with ironic and nova commands:

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_221.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_222.jpg)

2.Check status of Ceph cluster.

3.Log into the new storage node.

<span>ssh -l heat-admin 10.22.110.63</span>

<span>sudo –i</span>

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_223.jpg)

There is a need to activate Ceph disks the way it was done earlier. Click [here for details](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.html#ceph_disk_activation). Update ceph.conf with osd_journal_size=20000 and activate the disks.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_224.jpg)

Ceph osd tree shows all the OSD's on the new node are up now and the storage is scaled up to 171 TB

<span>.</span>

<span>-5  42.95996     host overcloud-cephstorage-3                                   </span>

<span>24   5.37000         osd.24                        up  1.00000          1.00000 </span>

<span>25   5.37000         osd.25                        up  1.00000          1.00000 </span>

<span>26   5.37000         osd.26                        up  1.00000          1.00000 </span>

<span>27   5.37000         osd.27                        up  1.00000          1.00000 </span>

<span>28   5.37000         osd.28                        up  1.00000          1.00000 </span>

<span>29   5.37000         osd.29                        up  1.00000          1.00000 </span>

<span>30   5.37000         osd.30                        up  1.00000          1.00000 </span>

<span>31   5.37000         osd.31                        up  1.00000          1.00000</span>

You may update the default PG's if needed. In the current setup we have 1024 placement groups, while for 32 OSD's it should have been 100*32/3 ~ 1066\. Therefore, this is ignored.

This completes the addition of storage node in the cluster.

Insert the new Cisco UCS B200 M4 blade into an empty slot in the chassis with similar configuration of local disks.

1.[Refer to this section](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.html#ServiceProfileTemplatesforCompute) above for creating service profiles from Storage template. Create a new service profile from the template. Unbind the template and remove the storage policy that was attached to it earlier and associate the service profile to the server.

2.Upgrade firmware if needed.

3.Check the installed firmware on the new node and make sure that it is upgraded to the same version as other compute nodes.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_225.jpg)

4.Get the hardware inventory details needed for introspection. This include IPMI address, Provisioning MAC address, Boot Lun size, CPU and memory.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_226.png)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_227.jpg)

5.Specify the NIC order in the service profile. This should be the same as the other Compute nodes with provisioning interface as the first one.

6.Check the boot policy of the server. Validate that this is same as other compute nodes too. Should be LAN PXE first followed by local lun.

7.Update cisco-plugins.yaml file with details about this new server.

Append an entry in UCSM Host list as below

<span>NetworkUCSMHostList: ……..,’00:25:b5:00:00:06:Openstack_Compute_Node7</span>

<span>Add entries for this compute host in each switch section</span>

<span>“00:25:b5:00:00:06”: {</span>

<span>“ports”: “port-channel:17,port-channel:18”</span>

8.With the above the server is ready for introspection and Overcloud deploy.

To run Introspection, complete the following steps:

1.Prepare json file for introspection:

<span>stack@osp7-director ~]$ cat compute-node.json </span>

<span>  “nodes”: [</span>

<span>      “pm_user”: “admin”,</span>

<span>      “pm_password”: “password”,</span>

<span>      “pm_type”: “pxe_ipmitool”,</span>

<span>      “pm_addr”: “10.22.100.13”,</span>

<span>      “mac”: [</span>

<span>        “00:25:b5:00:00:1a”                                         </span>

<span>      “memory”: “262144”,</span>

<span>      “disk”: “200”,</span>

<span>      “arch”: “x86_64”,</span>

<span>      “cpu”: “40”</span>

2.Check IPMI Connectivity:

<span>ipmitool –I lanplus -H 10.22.100.13 -U admin -P &lt;password&gt; chassis power off</span>

<span>Chassis Power Control: Down/Off</span>

3.Run discovery and introspection:

<span>[stack@osp7-director ~]$ openstack baremetal import –json ~/compute-node.json</span>

<span>[stack@osp7-director ~]$ openstack baremetal configure boot</span>

<span>[stack@osp7-director ~]$ openstack baremetal list</span>

<span>[stack@osp7-director ~]$ ironic node-set-maintenance 036cae70-bdee-427c-987c-a6a2d8a32292 true</span>

<span>[stack@osp7-director ~]$ openstack baremetal introspection start 036cae70-bdee-427c-987c-a6a2d8a32292</span>

<span>[stack@osp7-director ~]$ openstack baremetal introspection status 036cae70-bdee-427c-987c-a6a2d8a32292</span>

Wait till the introspection is complete. The status command should yield finished as True and Error as none.

<span>ironic node-set-maintenance 036cae70-bdee-427c-987c-a6a2d8a32292 false</span>

4.Update node properties

<span>[stack@osp7-director ~]$ ironic node-update \</span>

<span>036cae70-bdee-427c-987c-a6a2d8a32292 \</span>

<span>&gt; add properties/capabilities=’profile:compute,boot_option:local’</span>

<span>[stack@osp7-director ~]$ glance image-list | grep deploy | awk ‘{print $2" " $4}’ </span>

<span>ca9667c9-de8f-4df1-95d5-b5f8b6fb8f4d <span>bm-deploy-kernel</span></span>

<span>404d0e44-e5c7-4b46-8339-c451441b3f55 <span>bm-deploy-ramdisk</span></span>

<span>[stack@osp7-director ~]$ ironic node-update 036cae70-bdee-427c-987c-a6a2d8a32292 \</span>

<span>&gt; add driver_info/deploy_kernel=’ca9667c9-de8f-4df1-95d5-b5f8b6fb8f4d’;</span>

<span>[stack@osp7-director ~]$ ironic node-update 036cae70-bdee-427c-987c-a6a2d8a32292 \</span>

<span>&gt; add driver_info/deploy_ramdisk=’404d0e44-e5c7-4b46-8339-c451441b3f55’;</span>

Check the status of added entries with ironic node-show. Repeat the above to all nodes that you would like to add as overcloud deploy can add all of these in a single go.

Run Overcloud deployment command. The number of compute nodes has been incremented to 7 from 6 earlier. Here the number '7' indicates the total number of storage nodes in Overcloud.

<span>#!/bin/bash</span>

<span>openstack overcloud deploy –templates –compute-scale 7 \</span>

<span>-e /usr/share/openstack-tripleo-heat-templates/overcloud-resource-registry-puppet.yaml \</span>

<span>-e /usr/share/openstack-tripleo-heat-templates/environments/network-isolation.yaml \</span>

<span>-e /home/stack/templates/network-environment.yaml \</span>

<span>-e /home/stack/templates/storage-environment.yaml \</span>

<span>-e /home/stack/templates/cisco-plugins.yaml \</span>

<span>-e /home/stack/templates/post_config.yaml \</span>

<span>–verbose –debug –log-file overcloud_compute-add.log</span>

<span>Addition of nodes complete with the following message</span>

<span>INFO: rdomanager_oscplugin.v1.overcloud_deploy.DeployOvercloud Stack found, will be doing a stack update</span>

<span>Overcloud Endpoint: <a href="http://172.22.215.91:5000/v2.0/">http://172.22.215.91:5000/v2.0/</a></span>

<span>Overcloud Deployed</span>

<span>DEBUG: openstackclient.shell clean_up DeployOvercloud</span>

To perform the deployment and health checks, complete the following steps:

1.Login to each controller node and check for the existence of the new compute node in /etc/neutron/plugin.ini. If not please add in each Nexus Switch section and also in UCSM host list in plugin.ini file. Make sure to make the changes across all the controller nodes.

<span>systemctl daemon-reload</span>

<span>systemctl restart neutron-server.service</span>

2.Restart nova-services as a post deployment.

<span>systemctl restart openstack-nova-consoleauth.service openstack-nova-scheduler.service openstack-nova-api.service</span>

3.Check the status through ironic node-list and nova list.

4.Log into dashboard to check the status of the new node added.

<span>The System should be up and running and will deploy VM’s on the newly added node.</span>

Both the hardware and software stack are injected with faults to trigger a failure of a running process on a node or an unavailability of hardware for a short or extended period of time. With the fault in place the functional validations are done as mentioned above. The purpose is to achieve business continuity without interruption to the clients. However performance degradation is inevitable and has been documented wherever it was captured as part of the tests.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_228.jpg)

The status of OpenStack services were checked with pcs status as below on Controller Node.

<span>[root@overcloud-controller-0 ~]# pcs status </span>

<span>Cluster name: tripleo_cluster</span>

<span>Last updated: Thu Feb 11 13:08:58 2016          Last change: Wed Feb 10 13:05:34 2016 by root via cibadmin on overcloud-controller-2</span>

<span>Stack: corosync</span>

<span>Current DC: overcloud-controller-2 (version 1.1.13-10.el7-44eb2dd) - partition with quorum</span>

<span>3 nodes and 115 resources configured</span>

<span>Online: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span>Full list of resources:</span>

<span> ip-172.22.215.91       (ocf::heartbeat:IPaddr2):       Started overcloud-controller-0</span>

<span> Clone Set: haproxy-clone [haproxy]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> ip-10.22.100.50        (ocf::heartbeat:IPaddr2):       Started overcloud-controller-1</span>

<span> ip-10.22.100.51        (ocf::heartbeat:IPaddr2):       Started overcloud-controller-2</span>

<span> ip-10.22.150.50        (ocf::heartbeat:IPaddr2):       Started overcloud-controller-0</span>

<span> Master/Slave Set: galera-master [galera]</span>

<span>     Masters: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> ip-10.22.110.65        (ocf::heartbeat:IPaddr2):       Started overcloud-controller-1</span>

<span> Master/Slave Set: redis-master [redis]</span>

<span>     Masters: [ overcloud-controller-2 ]</span>

<span>     Slaves: [ overcloud-controller-0 overcloud-controller-1 ]</span>

<span> Clone Set: mongod-clone [mongod]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: rabbitmq-clone [rabbitmq]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: memcached-clone [memcached]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> ip-10.22.120.50        (ocf::heartbeat:IPaddr2):       Started overcloud-controller-2</span>

<span> Clone Set: openstack-nova-scheduler-clone [openstack-nova-scheduler]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: neutron-l3-agent-clone [neutron-l3-agent]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: openstack-ceilometer-alarm-notifier-clone [openstack-ceilometer-alarm-notifier]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: openstack-heat-engine-clone [openstack-heat-engine]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: openstack-ceilometer-api-clone [openstack-ceilometer-api]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: neutron-metadata-agent-clone [neutron-metadata-agent]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: neutron-ovs-cleanup-clone [neutron-ovs-cleanup]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: neutron-netns-cleanup-clone [neutron-netns-cleanup]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: openstack-heat-api-clone [openstack-heat-api]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: openstack-cinder-scheduler-clone [openstack-cinder-scheduler]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: openstack-nova-api-clone [openstack-nova-api]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: openstack-heat-api-cloudwatch-clone [openstack-heat-api-cloudwatch]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: openstack-ceilometer-collector-clone [openstack-ceilometer-collector]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: openstack-keystone-clone [openstack-keystone]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: openstack-nova-consoleauth-clone [openstack-nova-consoleauth]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: openstack-glance-registry-clone [openstack-glance-registry]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: openstack-ceilometer-notification-clone [openstack-ceilometer-notification]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: openstack-cinder-api-clone [openstack-cinder-api]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: neutron-dhcp-agent-clone [neutron-dhcp-agent]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: openstack-glance-api-clone [openstack-glance-api]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: neutron-openvswitch-agent-clone [neutron-openvswitch-agent]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: openstack-nova-novncproxy-clone [openstack-nova-novncproxy]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: delay-clone [delay]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> vsm-s  (ocf::heartbeat:VirtualDomain): Started overcloud-controller-0</span>

<span> vsm-p  (ocf::heartbeat:VirtualDomain): Started overcloud-controller-1</span>

<span> Clone Set: neutron-server-clone [neutron-server]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: httpd-clone [httpd]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: openstack-ceilometer-central-clone [openstack-ceilometer-central]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: openstack-ceilometer-alarm-evaluator-clone [openstack-ceilometer-alarm-evaluator]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: openstack-heat-api-cfn-clone [openstack-heat-api-cfn]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> openstack-cinder-volume        (systemd:openstack-cinder-volume):      Started overcloud-controller-0</span>

<span> Clone Set: openstack-nova-conductor-clone [openstack-nova-conductor]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> ucs-fence-controller   (stonith:fence_cisco_ucs):      Started overcloud-controller-2</span>

<span>PCSD Status:</span>

<span>  overcloud-controller-0: Online</span>

<span>  overcloud-controller-1: Online</span>

<span>  overcloud-controller-2: Online</span>

<span>Daemon Status:</span>

<span>  corosync: active/enabled</span>

<span>  pacemaker: active/enabled</span>

<span>  pcsd: active/enabled</span>

Few identified services running on these nodes were either restarted or killed and/or rebooted the nodes.

For eg.

Master/Slave Set: redis-master [redis]

```
 Masters: \[ overcloud-controller-2 \]

 Slaves: \[ overcloud-controller-0 overcloud-controller-1 \]
```

Per above redis master is overcloud-controller-2\. This node was rebooted and observed the behavior while the node getting rebooted and any impact of N-S traffic or E-W traffic of VM's. The only issue observed was for about 2-3 minutes few of the VM's were not pingable because of bug [1281603](https://bugzilla.redhat.com/show_bug.cgi?id=1281603) and this was not related with the services above.

The ceph node monitors and services were also restarted to test any interruption of volume creation and booting of the VM's, but no issues observed.

Cisco UCS Fabric Interconnects work in pair with inbuilt HA. While both of them serve traffic during a normal operation, a surviving member can still keep the system up and running. Depending on the overprovisioning used in the deployment a degradation in performance may be expected.

An effort is made to reboot the Fabric one after the other and do [functional tests](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.html#Functional_Validation) as mentioned earlier.

Reboot Fabric A

<span>·</span>

Check the status of the UCS Fabric Cluster before reboot

<span>UCSO-6248-FAB-A# show cluster state </span>

<span>Cluster Id: 0x1992ea1a116111e5-0x8ace002a6a3bbba1</span>

**<span>A: UP, PRIMARY</span>**

<span>B: UP, SUBORDINATE</span>

**<span>HA READY </span>

<span>ß</span>

<span>–System should be in HA ready before invoking any of the HA tests on Fabrics.</span>**

<span>·</span>

Check the status of OpenStack PCS Cluster before reboot

<span>root@overcloud-controller-0 ~]# pcs status </span>

<span>Cluster name: tripleo_cluster</span>

<span>Online: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span>Full list of resources:</span>

<span>ip-172.22.215.91       (ocf::heartbeat:IPaddr2):       Started overcloud-controller-0</span>

<span> Clone Set: haproxy-clone [haproxy]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> ip-10.22.100.51        (ocf::heartbeat:IPaddr2):       Started overcloud-controller-2</span>

<span> ip-10.22.100.50        (ocf::heartbeat:IPaddr2):       Started overcloud-controller-2</span>

<span> ip-10.22.150.50        (ocf::heartbeat:IPaddr2):       Started overcloud-controller-0</span>

<span> Master/Slave Set: galera-master [galera]</span>

<span>     Masters: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> ip-10.22.110.45        (ocf::heartbeat:IPaddr2):       Started overcloud-controller-2</span>

<span>……..</span>

<span>……..</span>

<span>Grep for any errors or stopped actions from PCS, fix the issues before starting the tests.</span>

<span>·</span>

Reboot Fabric A ( primary )

Log into UCS Fabric Command Line Interface and reboot the Fabric

<span>UCSO-6248-FAB-A# connect local-mgmt </span>

<span>Cisco Nexus Operating System (NX-OS) Software</span>

<span>UCSO-6248-FAB-A(local-mgmt)# reboot </span>

<span>Before rebooting, please take a configuration backup.</span>

<span>Do you still want to reboot? (yes/no):yes</span>

<span>nohup: ignoring input and appending output to `nohup.out’</span>

<span>Broadcast message from root (Fri Nov  6 20:59:45 2015):</span>

<span>All shells being terminated due to system /sbin/reboot</span>

<span>Connection to 10.22.100.6 closed.</span>

The following is a list of health checks and observations:

<span>·</span>

Check for VIP and Fabric A pings. Both should be down immediately. VIP recovers after a couple of minutes

<span>·</span>

Check for PCS Cluster status on one of the controller nodes. System could be slow in the beginning but should respond as follows:

<span>PCSD Status:</span>

<span>  overcloud-controller-0: Online</span>

<span>  overcloud-controller-1: Online</span>

<span>  overcloud-controller-2: Online</span>

<span>Perform a quick health check on creating VM’s, checking the status of existing instances and l3 forwarding enabled in N1KV earlier. Check the sanity checks on Nexus switches too for any impact on port-channels because of Fab A is down.</span>

<span>·</span>

Create Virtual Machines

Perform a quick health check on creating VM's, checking the status of existing instances and l3 forwarding enabled in N1KV earlier. Check the sanity checks on Nexus switches too for any impact on port-channels because of Fab A is down.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Fabric A might take around 15 minutes to come back online.

Reboot Fabric B

<span>·</span>

Connect to the Fabric B now and check the cluster status. System should show HA READY before rebooting Fab B.

<span>·</span>

Reboot Fab B by connecting to the local-mgmt similar to Fab A.

<span>·</span>

Perform the health check similar to the ones does for Fab A.

<span>·</span>

The test went fine without any issues on the configuration. Please refer bug [1267780](https://bugzilla.redhat.com/show_bug.cgi?id=1267780) on the issues encountered and the fix rolled out in this document.

IO Module Failures seldom happen in UCS infrastructure and in most of the cases these are human mistakes. The failure tests were included just to validate the business continuity. Any L3 east-west traffic will get routed through upstream switches in case of IOM failures.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_229.jpg)

Multiple Tenants with multiple networks and Virtual machines were created. Identified the VM's belonging to the same tenant but with different networks and also going to different chassis. One of the IO Modules was pulled out from the chassis and the L3 traffic validated.

Now Ping from tenant320_120_inst8 to tenant320_170_inst19 10.2.170.11 and 10.22.160.49

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_230.png)

**Remove IO Module from Chassis 1 going to Fabric A**

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_231.png)

Post Fault injection, ping continued

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_232.png)

Nexus switches are deployed in pairs and allow the upstream connectivity of the virtual machines to outside of the fabric. Cisco Nexus plugin creates VLANs on these switches both globally and on the port channel. The Nexus plugin replays these vlans or rebuilds the vlan information on the rebooted switch once it comes back up again. In order to test the HA of these switches multiple networks and instances were created and one of the switches were rebooted. The connectivity of the VM's through floating network checked and also the time it took for the plugins to replay was noted as below.

Nexus Switches

<span>UCSO-N9K-FAB-A# show version</span>

<span>Software</span>

<span>  BIOS: version 07.17</span>

<span>  NXOS: version 7.0(3)I1(3)</span>

<span>Hardware</span>

<span>  cisco Nexus9000 C9372PX chassis </span>

<span>  Intel(R) Core(TM) i3-3227U C with 16402540 kB of memory.</span>

<span>Last reset </span>

<span>  System version: 7.0(3)I1(3)</span>

<span>UCSO-N9K-FAB-A# show startup-config vlan </span>

<span>!Command: show startup-config vlan</span>

<span>!Time: Thu Jan 28 14:32:13 2016</span>

<span>!Startup config saved at: Tue Jan 26 18:44:34 2016</span>

<span>version 7.0(3)I1(3)</span>

<span>Legend:</span>

<span>        Type 1 : vPC will be suspended in case of mismatch</span>

<span>Name                        Type  Local Value            Peer Value             </span>

<span>————-               —-  ———————- ———————–</span>

<span>Vlan to Vn-segment Map      1     No Relevant Maps       No Relevant Maps      </span>

<span>STP Mode                    1     Rapid-PVST             Rapid-PVST            </span>

<span>STP Disabled                1     None                   None                  </span>

<span>STP MST Region Name         1     “”                     “”                    </span>

<span>STP MST Region Revision     1     0                      0                     </span>

<span>STP MST Region Instance to  1                                                  </span>

<span> VLAN Mapping                                                                  </span>

<span>STP Loopguard               1     Disabled               Disabled              </span>

<span>STP Bridge Assurance        1     Enabled                Enabled               </span>

<span>STP Port Type, Edge         1     Normal, Disabled,      Normal, Disabled,     </span>

<span>BPDUFilter, Edge BPDUGuard        Disabled               Disabled              </span>

<span>STP MST Simulate PVST       1     Enabled                Enabled               </span>

<span>Interface-vlan admin up     2     100,110,120,130,160,21 100,110,120,130,160,21</span>

<span>                                  5                      5                     </span>

<span>Interface-vlan routing      2     1,100,110,120,130,160, 1,100,110,120,130,160,</span>

<span>capability                        215                    215                   </span>

<span>Allowed VLANs               -     1,100,110,120,130,160, 1,100,110,120,130,160,</span>

<span>                                  215,251-263,265-270,27 215,251-263,265-270,27</span>

<span>                                  —–Output truncated—–</span>

<span>Local suspended VLANs       -     -                      -                     </span>

<span>UCSO-N9K-FAB-A# show vpc brief</span>

<span>Legend:</span>

<span>                (*) - local vPC is down, forwarding through vPC peer-link</span>

<span>vPC domain id                     : 100 </span>

<span>Peer status                       : peer adjacency formed ok      </span>

<span>vPC keep-alive status             : peer is alive                 </span>

<span>Configuration consistency status  : success </span>

<span>Per-vlan consistency status       : success                       </span>

<span>Type-2 consistency status         : success </span>

<span>vPC role                          : secondary                     </span>

<span>Number of vPCs configured         : 2   </span>

<span>Peer Gateway                      : Enabled</span>

<span>Dual-active excluded VLANs        : -</span>

<span>Graceful Consistency Check        : Enabled</span>

<span>Auto-recovery status              : Disabled</span>

<span>Delay-restore status              : Timer is off.(timeout = 30s)</span>

<span>Delay-restore SVI status          : Timer is off.(timeout = 10s)</span>

<span>vPC Peer-link status</span>

<span>———————————————————————</span>

<span>id   Port   Status Active vlans    </span>

<span>–   —-   —— ————————————————–</span>

<span>1    Po1    up     1,100,110,120,130,160,215,251-263,265-270,272,274,     </span>

<span>                   276-277,279-293,295-298,300-304,306-308,310-314,31     </span>

<span>                   —–Output truncated—–</span>

<span>vPC status</span>

<span>———————————————————————-</span>

<span>id   Port   Status Consistency Reason                     Active vlans</span>

<span>–   —-   —— ———– ——                     ————</span>

<span>17   Po17   up     success     success                    1,100,110,1     </span>

<span>                                                          20,130,160,     </span>

<span>                                                          215,251-263     </span>

<span>                                                          ,265-270,27     </span>

<span>                                                          2,274,276-2 ….</span>

<span>18   Po18   up     success     success                    1,100,110,1     </span>

<span>                                                          20,130,160,     </span>

<span>                                                          215,251-263     </span>

<span>                                                          ,265-270,27     </span>

<span>                                                          2,274,276-2 ….</span>

<span>UCSO-N9K-FAB-A(config)# show vlan | grep q- | count </span>

<span>200</span>

<span>UCSO-N9K-FAB-B(config)# show vlan | grep q- | count </span>

<span>200</span>

<span>Present in both port channels</span>

<span>UCSO-N9K-FAB-A(config)# show vlan | grep “Po17, Po18” | grep q- | count </span>

<span>200</span>

<span>interface port-channel17</span>

<span>  description Port-channel for UCS FabA port 17 &amp; FabB port 17</span>

<span>  switchport mode trunk</span>

<span>  switchport trunk allowed vlan 1,100,110,120,130,160,215,251-263</span>

<span>  switchport trunk allowed vlan add 265-272,274,276-277,279-293,295-298</span>

<span>  —–Output truncated—–</span>

<span>  switchport trunk allowed vlan add 536,538</span>

<span>  spanning-tree port type edge trunk</span>

<span>  vpc 17</span>

<span>interface port-channel18</span>

<span>  description Port-channel for UCS FabA port 18 &amp; FabB port 18</span>

<span>  switchport mode trunk</span>

<span>  switchport trunk allowed vlan 1,100,110,120,130,160,215,251-263</span>

<span>  switchport trunk allowed vlan add 265-272,274,276-277,279-293,295-298</span>

<span>  —–Output truncated—–</span>

<span>  switchport trunk allowed vlan add 536,538</span>

<span>  spanning-tree port type edge trunk</span>

<span>  vpc 18</span>

<span>interface Ethernet1/17</span>

<span>  description Uplink from UCS FabA Port 17</span>

<span>  switchport mode trunk</span>

<span>  switchport trunk allowed vlan 1,100,110,120,130,160,215,251-263</span>

<span>  switchport trunk allowed vlan add 265-272,274,276-277,279-293,295-298</span>

<span>  —–Output truncated—–</span>

<span>  switchport trunk allowed vlan add 536,538</span>

<span>  spanning-tree port type edge trunk</span>

<span>  channel-group 17 mode active</span>

<span>interface Ethernet1/18</span>

<span>  description Uplink from UCS FabA Port 18</span>

<span>  switchport mode trunk</span>

<span>  switchport trunk allowed vlan 1,100,110,120,130,160,215,251-263</span>

<span>  switchport trunk allowed vlan add 265-272,274,276-277,279-293,295-298</span>

<span>  —–Output truncated—–</span>

<span>  switchport trunk allowed vlan add 536,538</span>

<span>  spanning-tree port type edge trunk</span>

<span>  channel-group 18 mode active</span>

**Figure 21**Cisco UCS Manager VLANs

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_233.png)

Creating tenant creates VLANs in compute nodes. Howerver if a VM from one tenant is deleted, the VLAN on the computes will remain until the last vm of that tenant is deleted.

<span>[stack@osp7-director scripts]$ nova list –all-tenants | awk ‘/Running/’ | wc -l </span>

<span>400</span>

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_234.jpg)

Connectivity from external client machine on floating IP to VM's.

Command used:

<span>ssh -i tenant349kp.pem  -o StrictHostKeyChecking=no  cloud-user@10.22.162.77 /tmp/run.sh – for each VM created.</span>

<span>Host is tenant208-108-inst1 and Network is         inet 10.1.108.5  netmask 255.255.255.0  broadcast 10.1.108.255</span>

<span>Host is tenant208-108-inst2 and Network is         inet 10.1.108.6  netmask 255.255.255.0  broadcast 10.1.108.255</span>

<span>…….</span>

<span>…….</span>

<span>…….</span>

<span>Host is tenant348-148-inst1 and Network is         inet 10.2.148.5  netmask 255.255.255.0  broadcast 10.2.148.255</span>

<span>Host is tenant348-148-inst2 and Network is         inet 10.2.148.6  netmask 255.255.255.0  broadcast 10.2.148.255</span>

<span>Host is tenant348-198-inst3 and Network is         inet 10.2.198.5  netmask 255.255.255.0  broadcast 10.2.198.255</span>

A script was created and pushed with 'scp' that in turn runs ifconfig on each VM and gets the details. This was validated for all the 400 VM's created above.

Login to instances –

<span>[root@clouduser scripts]# ssh -i tenant350kp.pem  -o StrictHostKeyChecking=no  cloud-user@10.22.162.79 </span>

<span>[cloud-user@tenant350-150-inst1 ~]$ </span>

<span> [cloud-user@tenant350-150-inst1 ~]$ ifconfig -a</span>

<span>eth0: flags=4163&lt;UP,BROADCAST,RUNNING,MULTICAST&gt;  mtu 1400</span>

<span>        inet 10.2.150.5  netmask 255.255.255.0  broadcast 10.2.150.255</span>

<span>        inet6 fe80::f816:3eff:fe57:d162  prefixlen 64  scopeid 0x20&lt;link&gt;</span>

<span>        ether fa:16:3e:57:d1:62  txqueuelen 1000  (Ethernet)</span>

<span>        RX packets 1746  bytes 169967 (165.9 KiB)</span>

<span>        RX errors 0  dropped 5  overruns 0  frame 0</span>

<span>        TX packets 1748  bytes 161366 (157.5 KiB)</span>

<span>        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0</span>

Ping VM in the same network

<span>[cloud-user@tenant350-150-inst1 ~]$ ping 10.2.150.6</span>

<span>PING 10.2.150.6 (10.2.150.6) 56(84) bytes of data.</span>

<span>64 bytes from 10.2.150.6: icmp_seq=1 ttl=64 time=1.62 ms</span>

<span>64 bytes from 10.2.150.6: icmp_seq=2 ttl=64 time=0.609 ms</span>

<span>64 bytes from 10.2.150.6: icmp_seq=3 ttl=64 time=0.554 ms</span>

L3 routing

<span>[stack@osp7-director scripts]$ source keystonerc_tenant350</span>

<span>[stack@osp7-director scripts]$ neutron subnet-list | grep -v ext-subnet </span>

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_235.jpg)

<span>10.2.150 and 10.2.200 are the networks for tenant 350\. Ping IP’s in these networks.</span>

<span>[cloud-user@tenant350-150-inst1 ~]$ ping 10.2.150.5 </span>

<span>PING 10.2.150.5 (10.2.150.5) 56(84) bytes of data.</span>

<span>64 bytes from 10.2.150.5: icmp_seq=1 ttl=64 time=0.046 ms</span>

<span>64 bytes from 10.2.150.5: icmp_seq=2 ttl=64 time=0.047 ms</span>

<span>Connectivity verified with password less authentication too.</span>

<span>— 10.2.150.5 ping statistics —</span>

<span>2 packets transmitted, 2 received, 0% packet loss, time 1000ms</span>

<span>rtt min/avg/max/mdev = 0.046/0.046/0.047/0.006 ms</span>

<span>[cloud-user@tenant350-150-inst1 ~]$ ping 10.2.150.6</span>

<span>PING 10.2.150.6 (10.2.150.6) 56(84) bytes of data.</span>

<span>64 bytes from 10.2.150.6: icmp_seq=1 ttl=64 time=0.481 ms</span>

<span>64 bytes from 10.2.150.6: icmp_seq=2 ttl=64 time=0.472 ms</span>

<span>— 10.2.150.6 ping statistics —</span>

<span>2 packets transmitted, 2 received, 0% packet loss, time 1000ms</span>

<span>rtt min/avg/max/mdev = 0.472/0.476/0.481/0.022 ms</span>

<span>[cloud-user@tenant350-150-inst1 ~]$ ping 10.2.200.5 </span>

<span>PING 10.2.200.5 (10.2.200.5) 56(84) bytes of data.</span>

<span>64 bytes from 10.2.200.5: icmp_seq=1 ttl=63 time=1.20 ms</span>

<span>64 bytes from 10.2.200.5: icmp_seq=2 ttl=63 time=0.680 ms</span>

<span>— 10.2.200.5 ping statistics —</span>

<span>2 packets transmitted, 2 received, 0% packet loss, time 1001ms</span>

<span>rtt min/avg/max/mdev = 0.680/0.944/1.209/0.266 ms</span>

<span>[cloud-user@tenant350-150-inst1 ~]$ ping 10.2.200.6</span>

<span>PING 10.2.200.6 (10.2.200.6) 56(84) bytes of data.</span>

<span>64 bytes from 10.2.200.6: icmp_seq=1 ttl=63 time=1.24 ms</span>

<span>64 bytes from 10.2.200.6: icmp_seq=2 ttl=63 time=0.613 ms</span>

<span>64 bytes from 10.2.200.6: icmp_seq=3 ttl=63 time=0.761 ms</span>

Errors – No errors observed in logs in /var/log/neutron

interface Ethernet1/17

description Uplink from UCS FabA Port 17

<span>switchport mode trunk</span>

<span>  switchport trunk allowed vlan 1,100,110,120,130,160,215,251-263</span>

<span>  switchport trunk allowed vlan add 265-272,274,276-277,279-293,295-298</span>

<span>  switchport trunk allowed vlan add 300-308,310-314,316-322,324-327</span>

<span>  switchport trunk allowed vlan add 329,331-332,334-351,353-359,361-368</span>

<span>  switchport trunk allowed vlan add 370-379,381-386,389-397,399-408</span>

<span>  switchport trunk allowed vlan add 410-411,413-414,417-420,422,426-429</span>

<span>  switchport trunk allowed vlan add 431-433,435,438-439,442-443,448-449</span>

<span>  switchport trunk allowed vlan add 451-452,455-456,459-460,464-465</span>

<span>  switchport trunk allowed vlan add 468,471,473-475,480-481,483-484</span>

<span>  switchport trunk allowed vlan add 486,491-492,495,498-500,502,504</span>

<span>  switchport trunk allowed vlan add 506,508-511,515,517-518,523,526</span>

<span>  switchport trunk allowed vlan add 536,538</span>

<span>  spanning-tree port type edge trunk</span>

<span>  channel-group 17 mode active</span>

<span>Ping continues</span>

<span>64 bytes from 10.22.162.79: icmp_seq=1 ttl=63 time=0.435 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=3 ttl=63 time=0.359 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=1 ttl=63 time=0.325 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=2 ttl=63 time=0.471 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=3 ttl=63 time=0.347 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=1 ttl=63 time=0.391 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=2 ttl=63 time=0.422 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=3 ttl=63 time=0.356 ms</span>

<span>vPC Peer-link status</span>

<span>———————————————————————</span>

<span>id   Port   Status Active vlans    </span>

<span>–   —-   —— ————————————————–</span>

<span>1    Po1    up     1,100,110,120,130,160,215,251-263,265-272,274,276-     </span>

<span>                   277,279-293,295-298,300-308,310-314,316-322,324-32     </span>

<span>                   7,329,331-332,334-351,353-359,361-368,370-379,381-     </span>

<span>                   386,389-397,399-408,410-411,413-414,417-420,422,42     </span>

<span>                   6-429,431-433,435,438-439,442-443,448-449,451-452, ….</span>

<span>vPC status</span>

<span>———————————————————————-</span>

<span>id   Port   Status Consistency Reason                     Active vlans</span>

<span>–   —-   —— ———– ——                     ————</span>

<span>17   Po17   down*  success     success                    -               </span>

<span>18   Po18   up     success     success                    1,100,110,1     </span>

<span>                                                          20,130,160,     </span>

<span>                                                          215,251-263     </span>

<span>                                                          ,265-272,27     </span>

<span>                                                          4,276-277,2 ….</span>

<span>Bring down e 1/18 also. Both interfaces on N9K down.</span>

<span>Ping continues</span>

<span>64 bytes from 10.22.162.79: icmp_seq=1 ttl=63 time=0.350 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=2 ttl=63 time=0.515 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=3 ttl=63 time=0.471 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=1 ttl=63 time=0.518 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=2 ttl=63 time=0.562 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=3 ttl=63 time=0.457 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=1 ttl=63 time=0.332 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=2 ttl=63 time=0.381 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=3 ttl=63 time=0.462 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=1 ttl=63 time=0.227 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=2 ttl=63 time=0.501 ms</span>

<span>vPC Peer-link status</span>

<span>———————————————————————</span>

<span>id   Port   Status Active vlans    </span>

<span>–   —-   —— ————————————————–</span>

<span>1    Po1    up     1,100,110,120,130,160,215,251-263,265-272,274,276-     </span>

<span>                   277,279-293,295-298,300-308,310-314,316-322,324-32     </span>

<span>                   7,329,331-332,334-351,353-359,361-368,370-379,381-     </span>

<span>                   386,389-397,399-408,410-411,413-414,417-420,422,42     </span>

<span>                   6-429,431-433,435,438-439,442-443,448-449,451-452, ….</span>

<span>vPC status</span>

<span>———————————————————————-</span>

<span>id   Port   Status Consistency Reason                     Active vlans</span>

<span>–   —-   —— ———– ——                     ————</span>

<span>17   Po17   down*  success     success                    -               </span>

<span>18   Po18   down*  success     success                    -</span>

Run Ping Tests while switch rebooting

```
        <span>while :; do loop</span>
```

<span>ping -c 3 10.22.162.79 | grep icmp … </span>

<span>64 bytes from 10.22.162.79: icmp_seq=1 ttl=63 time=2.49 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=2 ttl=63 time=0.511 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=3 ttl=63 time=0.308 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=1 ttl=63 time=0.317 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=2 ttl=63 time=0.494 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=3 ttl=63 time=0.345 ms</span>

<span>UCSO-N9K-FAB-B(config)# reload </span>

<span>!!!WARNING! there is unsaved configuration!!!</span>

<span>This command will reboot the system. (y/n)?  [n] y</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

The plugin does not copy the running configuration to startup. Instead when the switch comes up it replays the configuration from mysql. Hence the warning about the unsaved configuration above

<span>Ping continues..</span>

<span>64 bytes from 10.22.162.79: icmp_seq=1 ttl=63 time=0.457 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=2 ttl=63 time=0.297 ms</span>

<span>64 bytes from 10.22.162.79: icmp_seq=3 ttl=63 time=0.455 ms</span>

When the switch comes up, check for available VLANs:

<span>UCSO-N9K-FAB-B(config)# show vlan | grep q- | count </span>

<span>Not all VLAN’s are not up yet. </span>

<span>Kernel uptime is 0 day(s), 0 hour(s), 6 minute(s), 43 second(s)</span>

<span>Reason: Reset Requested by CLI command reload</span>

<span>System version: 7.0(3)I1(3)</span>

<span>Service: </span>

<span>Active Packages:</span>

<span>UCSO-N9K-FAB-B(config)# show vlan | grep q- | count </span>

<span>200 – Replayed all 200 VLAN’s for 400 VM’s after kernel uptime of 6:43 minutes, while the second switch provided business continuity.</span>

Created Tenant 351 with 2 networks and 4 VM's after this

<span>| 6ade33c4-3563-4219-8668-384d0f1dfd05 | tenant351_151_inst1 | 2ca501f354764bacb7963c6ae780cd87 | ACTIVE | -          | Running     | tenant351-151=10.2.151.6, 10.22.162.84  |</span>

<span>| c0dd0b44-fe9f-4816-8909-4e662020c9f8 | tenant351_151_inst2 | 2ca501f354764bacb7963c6ae780cd87 | ACTIVE | -          | Running     | tenant351-151=10.2.151.5, 10.22.162.85  |</span>

<span>| 978550c5-fb99-4e15-81a0-7dc697193f53 | tenant351_201_inst3 | 2ca501f354764bacb7963c6ae780cd87 | ACTIVE | -          | Running     | tenant351-201=10.2.201.6, 10.22.162.86  |</span>

<span>| b10f88b9-82ac-4942-a888-09371528cb70 | tenant351_201_inst4 | 2ca501f354764bacb7963c6ae780cd87 | ACTIVE | -          | Running     | tenant351-201=10.2.201.5, 10.22.162.87  |</span>

<span>+————————————–+———————+———————————-+——–+————+————-+—————————————–+</span>

<span>Segmentation ID: 537</span>

<span>Segmentation ID: 494</span>

<span>Floating IP’s are 10.22.162.87 and 10.22.162.86</span>

<span>Connectivity works</span>

<span>[root@clouduser scripts]# ssh -i tenant351kp.pem  -o StrictHostKeyChecking=no  cloud-user@10.22.162.86 hostname -s </span>

<span>tenant351-201-inst3</span>

<span>[root@clouduser scripts]# ssh -i tenant351kp.pem  -o StrictHostKeyChecking=no  cloud-user@10.22.162.87 hostname -s </span>

<span>tenant351-201-inst4</span>

<span>On the switch </span>

<span>UCSO-N9K-FAB-B(config)# show vlan | egrep “q-537|q-494” </span>

<span>494  q-494                            active    Po1, Po17, Po18, Eth1/1, Eth1/2</span>

<span>537  q-537                            active    Po1, Po17, Po18, Eth1/1, Eth1/2</span>

Controllers are key for the health of the cloud which hosts most of the OpenStack services. There are three types of controller failures that could happen.

Server reboot, pulling the blade out of the chassis while system is up and running and putting it back, pulling the blade from the chassis and replacing it simulating a total failure of the controller node.

Run Health check before to make sure that system is healthy.

<span>·</span>

Run nova list after sourcing stackrc as stack user on Undercloud node to verify that all the controllers are in healthy state as below

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_236.jpg)

<span>·</span>

Run pcs status on controller nodes and grep for error or stopped.

<span>Online: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span>Full list of resources:</span>

<span> ip-172.22.215.91       (ocf::heartbeat:IPaddr2):       Started overcloud-controller-0</span>

<span> Clone Set: haproxy-clone [haproxy]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> ip-10.22.100.51        (ocf::heartbeat:IPaddr2):       Started overcloud-controller-2</span>

<span> ip-10.22.100.50        (ocf::heartbeat:IPaddr2):       Started overcloud-controller-2</span>

<span> ip-10.22.150.50        (ocf::heartbeat:IPaddr2):       Started overcloud-controller-0</span>

<span> Master/Slave Set: galera-master [galera]</span>

<span>     Masters: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> ip-10.22.110.45        (ocf::heartbeat:IPaddr2):       Started overcloud-controller-2</span>

<span>PCSD Status:</span>

<span>  overcloud-controller-0: Online</span>

<span>  overcloud-controller-1: Online</span>

<span>  overcloud-controller-2: Online</span>

<span>Daemon Status:</span>

<span>  corosync: active/enabled</span>

<span>  pacemaker: active/enabled</span>

<span>  pcsd: active/enabled</span>

<span>·</span>

Reboot the fist controller node and check for pcs status and connectivity of the VM's.

<span>·</span>

When the controller comes up, wait till all the services are through PCS are up and running.

<span>·</span>

Connect to n1kv VSM and make sure that the standby is is ha-status.

<span>·</span>

Repeat reboot of the second node and then the third node after the second comes up fully.

The following is a list of health checks and observations:

<span>·</span>

Do not reboot the second controller unless the prior one comes up first. Check pacemaker status, health of quorum ( corosync ), health of n1kv's primary and standby VSM's.

<span>·</span>

Two controllers are minimum needed for healthy operation.

<span>·</span>

While the first node is booting up, it takes time for pcs status command to complete.

<span>PCS will report one server is offline.</span>

<span>PCSD Status:</span>

<span>  overcloud-controller-0: Online</span>

<span>  overcloud-controller-1: Offline</span>

<span>  overcloud-controller-2: Online</span>

<span>Daemon Status:</span>

<span>  corosync: active/enabled</span>

<span>  pacemaker: active/enabled</span>

<span>  pcsd: active/enabled</span>

Corosync will return that it gets only 2 votes out of 3 as below while the server is getting rebooted. This is normal.

<span>[root@overcloud-controller-2 ~]# corosync-quorumtool </span>

<span>Quorum information</span>

<span>——————</span>

<span>Date:             Thu Jan 28 12:09:16 2016</span>

<span>Quorum provider:  corosync_votequorum</span>

<span>Nodes:            2</span>

<span>Node ID:          3</span>

<span>Ring ID:          56</span>

<span>Quorate:          Yes</span>

<span>Votequorum information</span>

<span>———————-</span>

<span>Expected votes:   3</span>

<span>Highest expected: 3</span>

<span>Total votes:      2</span>

<span>Quorum:           2  </span>

<span>Flags:            Quorate </span>

<span>Membership information</span>

<span>———————-</span>

<span>    Nodeid      Votes Name</span>

<span>         1          1 overcloud-controller-0</span>

<span>         3          1 overcloud-controller-2 (local)</span>

<span>·</span>

Refer to bug [1281603](https://bugzilla.redhat.com/show_bug.cgi?id=1281603). It might take just over 3 minutes for the other controllers to start rescheduling the routers. Within this time frame, slower keystone authentication, and creation of VM's observed. However, system recover fine after this. The issue of l3_ha=false to default of true from false is being addressed in the next release of n1kv.

<span>·</span>

When the node comes up, the routers remain on the other 2 controllers and do not fall back. Can be queried with ip netns too.

<span>·</span>

If controller node does not come up, check through KVM console to spot out any issues and hold off rebooting the second node before a healthy operation of the first.

One of the controller nodes blade was pulled out while the system is up and running. The validation tests like VM creation etc were done prior to the tests and to check the status when the blade is pulled from the chassis. This is like simulating a complete blade failure. After around 60 minutes the blade was re-inserted back in the chassis.

The same behavior as [observed during reboot](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.html#Controller_Reboot_Health_Checks) were noticed during the blade pull tests. However unlike a reboot which completes in 5-10 minutes, this was for an extended period of time of 60 minutes to check the status of the cluster.

<span>·</span>

Cisco UCS marks the blade as 'removed' and prompts to resolve the slot issue.

<span>·</span>

Nova declares the state of the server as NOSTATE as shown below:

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_237.jpg)

<span>·</span>

Ironic gives up as it cannot bring the server back online and enables Maintenance mode to True for this node.

<span>·</span>

Compare the Instance UUID from ironic node-list and ID from nova list

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_238.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_239.jpg)

<span>·</span>

Ceph storage will report that 1 out of 3 monitors are down. All the 3 controllers will be running one monitor each. However all the OSD's are up and running.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_240.jpg)

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

All of the above, the behavior UCS asking to resolve slot, ironic turning the blade to maintenance mode, nova setting the status to NOSTATE, and ceph reporting on one of the monitors as down are expected.

<span>·</span>

After inserting the blade back into the same slot of the chassis, it needed a manual intervention to correct the above.

<span>—</span>

Insert the blade back into the slot and resolve the slot issue in UCS.

<span>—</span>

ironic node-set-power-state b7dde876-354a-4688-8550-aec8f64c582c on

<span>—</span>

ironic node-set-maintenance b7dde876-354a-4688-8550-aec8f64c582c false

<span>—</span>

Wait for a minute and check back for these columns with ironic node-list.

<span>—</span>

nova reset-state –active a23af643-51c8-4f59-881c-77a9d5e1557f

<span>—</span>

Wait for about 5-10 minutes for nova to act upon this and re-query the status with nova list. It should turn it back as active like other controller nodes.

<span>—</span>

Login to the controller node, check for pcs status and resolve any processes that were not brought up running 'pcs resource cleanup'

<span>—</span>

If the monitor is still down and/or taking longer time issue /etc/init.d/ceph restart mon on the controller node(s).

Unlike the above two types of failures, in this test the blade is completely removed and new one is added. There were few issues encountered while rebuilding the failed controller blade and adding it as a replacement. The fix for bug 1298430 will give business continuity but there is a need to fix the failed blade. While this issue is being investigated, an interim solution was developed to circumvent the above limitation. This is included in the [Hardware failures](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.html#Hardware_Failure_of_Blades) section. Different types of hardware failures that can happen on a controller blade and how to mitigate these issues considering the dependency of Controller blade on IPMI and MAC addresses is addressed there.

Tests and Observations are as follows:

<span>·</span>

Many Instances were provisioned across the pod and reboot of the Compute Node was attempted.

<span>[root@overcloud-compute-0 ~]# virsh list </span>

<span> Id    Name                           State</span>

<span>—————————————————-</span>

<span> 2     instance-0000000f              running</span>

<span> 3     ins</span>
tance-00000021 running

<span> 4     instance-00000033              running</span>

<span> 5     instance-00000045              running</span>

<span> 6     instance-00000057              running</span>

<span> 7     instance-00000069              running</span>

<span> 8     instance-0000007b              running</span>

<span> 9     instance-00000081              running</span>

<span> 10    instance-0000009c              running</span>

<span> 11    instance-000000ab              running</span>

<span> 12    instance-000000ba              running</span>

<span> 13    instance-000000bd              running</span>

<span> 14    instance-000000d8              running</span>

<span> 15    instance-000000e7              running</span>

<span> 16    instance-000000f6              running</span>

<span> 17    instance-000000f9              running</span>

<span> 18    instance-00000114              running</span>

<span> 19    instance-00000123              running</span>

<span>·</span>

Identified a compute host to be rebooted and the VM's that could be impacted

About 19 VM's were up and running

<span>[root@overcloud-controller-0 heat-admin]# nova-manage vm list | egrep –i \ “overcloud-compute-0|project|active” | sort -k 1 | awk ‘{print $1" “$2” “$3” "$4}’ </span>

<span>instance node type state</span>

<span>tenant302_152_inst3 overcloud-compute-0.localdomain m1.demo active</span>

<span>tenant304_104_inst1 overcloud-compute-0.localdomain m1.demo active</span>

<span>tenant305_155_inst3 overcloud-compute-0.localdomain m1.demo active</span>

<span>tenant307_107_inst1 overcloud-compute-0.localdomain m1.demo active</span>

<span>tenant308_158_inst3 overcloud-compute-0.localdomain m1.demo active</span>

<span>tenant310_110_inst1 overcloud-compute-0.localdomain m1.demo active</span>

<span>tenant311_161_inst3 overcloud-compute-0.localdomain m1.demo active</span>

<span>tenant312_112_inst1 overcloud-compute-0.localdomain m1.demo active</span>

<span>tenant314_114_inst2 overcloud-compute-0.localdomain m1.demo active</span>

<span>tenant315_165_inst3 overcloud-compute-0.localdomain m1.demo active</span>

<span>tenant316_166_inst4 overcloud-compute-0.localdomain m1.demo active</span>

<span>tenant317_117_inst1 overcloud-compute-0.localdomain m1.demo active</span>

<span>tenant319_119_inst2 overcloud-compute-0.localdomain m1.demo active</span>

<span>tenant320_170_inst3 overcloud-compute-0.localdomain m1.demo active</span>

<span>tenant321_171_inst4 overcloud-compute-0.localdomain m1.demo active</span>

<span>tenant322_122_inst1 overcloud-compute-0.localdomain m1.demo active</span>

<span>tenant324_124_inst2 overcloud-compute-0.localdomain m1.demo active</span>

<span>tenant325_175_inst3 overcloud-compute-0.localdomain m1.demo active</span>

<span>[root@overcloud-controller-0 heat-admin]# nova-manage vm list | egrep “overcloud-compute-0|project|active” | sort -k 1 | awk ‘{print $1" “$2” “$3” "$4}’  | wc -l</span>

<span>·</span>

Identify the floating IP's for these VM's from nova list –-all-tenants and capture data to login without password, run ifconfig script. The script ssh's to all the VM's run's ifconfig and returns serially.

Running a script N-S for all the VM's

<span>[root@clouduser scripts]# date; ./temp.sh &gt; /dev/null; date;</span>

<span>Tue Nov 24 03:37:10 PST 2015</span>

<span>Tue Nov 24 03:37:13 PST 2015</span>

<span>[root@clouduser scripts]# tail -1 temp.sh</span>

<span>ssh -i tenant325kp.pem  -o StrictHostKeyChecking=no  cloud-user@10.22.160.181 /tmp/run1.sh</span>

<span>[root@clouduser scripts]# cat run1.sh</span>

<span>#!/bin/bash       </span>

<span>date1=`date`;</span>

<span>host=`hostname -s`</span>

<span>network=`/sbin/ifconfig | grep broadcast`</span>

<span>date2=`date`;</span>

<span>echo “Host is $host and Network is $network from $date1 to $date2”</span>

<span>[root@clouduser scripts]#</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

set resume_guests_state_on_host_boot=true in nova.conf to get the instances back online after reboot.

<span>·</span>

Rebooted the Compute Node overcloud-compute-0

<span>·</span>

Instances came up fine and the same script to validate the login with floating ip's worked fine.

<span>·</span>

By default guests will not come up unless resume_guests_state_on_host_boot is set to true. If this parameter isn't set before reboot:

<span>root@overcloud-controller-0 heat-admin]#  nova-manage vm list | egrep “overcloud-compute-0|project” | sort -k 1 | awk ‘{print $1" “$2” “$3” "$4}’</span>

<span>tenant302_152_inst3 overcloud-compute-0.localdomain m1.demo stopped</span>

<span>tenant304_104_inst1 overcloud-compute-0.localdomain m1.demo stopped</span>

<span>tenant305_155_inst3 overcloud-compute-0.localdomain m1.demo stopped</span>

<span>……………..</span>

<span>Observe a failure from dashboard as well</span>

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_241.png)

You may have to hard reboot the instances as nova reboot –hard $i after capturing the instance id's from nova list –all-tenants.

Query the instances as shown below:

<span>[root@overcloud-compute-0 nova]# virsh list –all </span>

<span> Id    Name                           State</span>

<span>—————————————————-</span>

<span> 21    instance-000000f6              running</span>

<span> 22    instance-0000000f              running</span>

<span> 23    instance-00000021              running</span>

One of the Compute blades was pulled out while the system is up and running. This was also an extended test for about 60 minutes and then the blade was re-inserted back in the chassis.

Observations:

<span>·</span>

Results were similar to reboot tests above.

<span>·</span>

UCS Manager complained to resolve the host as it was pulled out from the chassis. This was acknowledged and the blade was re-inserted.

<span>·</span>

The guest VM's came up when resume_guests property was set to true at host level.

<span>·</span>

Similar to Controller blade pull tests, nova made the state as 'NOSTATE' and ironic put the blade into Maintenance mode.

<span>·</span>

Similar steps like setting up the maintenance mode to off through ironic and nova reset state were issued to the blade after getting 'ok' status in UCS manager.

Compute blade was pulled from the chassis completely and the server was decommissioned in UCS. This is to simulate a complete failure of a Compute blade. Then an attempt was made to remove this from OpenStack and add a new blade to the cloud. The service profile was reused in this method. The following were the tasks list and observations made during a Compute blade replacement test.

Blade replacement is actually a two phase process. First remove the faulty blade from the system and then add a new one.

To delete a Node, complete the following steps:

1.Blade pulled from chassis.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_242.png)

2.Nova makes the blade to 'NOSTATE' as shown below.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_243.png)

3.Evacuate the VM's from the failed node.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_244.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_245.png)

4.An attempt was made to run remove this node. Refer to the [online documentation](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux_OpenStack_Platform/7/pdf/Director_Installation_and_Usage/Red_Hat_Enterprise_Linux_OpenStack_Platform-7-Director_Installation_and_Usage-en-US.pdf).

<span>openstack overcloud node delete –stack overcloud –templates \</span>

<span>-e /usr/share/openstack-tripleo-heat-templates/overcloud-resource-registry-puppet.yaml \</span>

<span>-e /usr/share/openstack-tripleo-heat-templates/environments/network-isolation.yaml \</span>

<span>-e /home/stack/templates/network-environment.yaml \</span>

<span>-e /home/stack/templates/storage-environment.yaml \</span>

<span>-e /home/stack/templates/cisco-plugins.yaml \</span>

<span>8b0f4d5d-352e-4751-88a8-2bb42b38927a</span>

<span>–verbose –debug –log-file overcloud_test.log</span>

Here the node-uuid as observed from nova list was added.

As the blade was completely pulled from the chassis ironic was unable to get to the power management and the above node-delete failed.

<span>ipmitool -I lanplus -H &lt;ipmi address&gt; -U admin -P &lt;password&gt; chassis power status</span>

<span>Error: Unable to establish IPMI v2 / RMCP+ session</span>

<span>Error: Unable to establish IPMI v2 / RMCP+ session</span>

<span>Error: Unable to establish IPMI v2 / RMCP+ session</span>

<span>Unable to get Chassis Power Status</span>

Workarounds to delete the blade in the current status are as follows:

Update the error status to available status in ironic node-list

<span>edit  /etc/ironic/ironic.conf</span>

<span>Update the enabled drivers temporarily as below</span>

<span>#enabled_drivers=pxe_ipmitool,pxe_ssh,pxe_drac</span>

<span>enabled_drivers=fake</span>

<span>Restart openstack-ironic-conductor</span>

<span>Sudo service openstack-ironic-conductor restart</span>

<span>ironic node-update NODE_UUID replace driver=fake</span>

The node in ironic node-list should be with provision-state=active and maintenance=false

If not

<span>ironic node-set-provision-state NODE_UUID provide</span>

<span>ironic node-set-provision-state NODE_UUID active</span>

<span>ironic node-set-provision-state NODE_UUID deleted</span>

The power status should be on, provision state as available and maintenance as false before moving ahead.

Run nova service-list and identify the service id's

Delete the service id's associated with this node as

<span>nova service-delete $id</span>

<span>Delete the node from nova</span>

<span>nova delete NODE_UUID </span>

<span>Delete the node from ironic</span>

<span>ironic node-delete NODE_UUID</span>

Revert back the "fake" driver from ironic.conf

<span>edit vi /etc/ironic/ironic.conf.</span>

<span>enabled_drivers=pxe_ipmitool,pxe_ssh,pxe_drac</span>

<span>#enabled_drivers=fake</span>

Restart ironic-conductor to pick up the drivers again.

<span>service openstack-ironic-conductor restart</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

The deleted node should not exist anymore in ironic node-list or nova-list now.

When the compute blade has been completely removed from OpenStack, a new blade can be added. The procedure for adding a new compute blade is same as how it was addressed earlier in [upscaling the compute pod](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.html#ScaleUpComputeNodes).

Ceph, the software stack deployed by Red Hat OpenStack Director, has its high availability built in itself. By default, the system will be replicating the placement groups and has 3 copies distributed across the hosts.

The parameter osd_pool_default_size = 3 in ceph.conf brings this feature by default when installed.

If we create a crushmap from the existing cluster as below it reveals what type of buckets are in and what mode of replication is being done by default in the cluster.

ceph osd getcrushmap -o /tmp/crushmap.bin

crushtool -d crushmap.bin -o /tmp/crushmap.txt

<span>rule replicated_ruleset {</span>

<span>        ruleset 0</span>

<span>
  <strong>type replicated</strong>
</span>

**<span>ß</span>

<span> Default to Replication mode</span>**

<span>        min_size 1</span>

<span>        max_size 10</span>

<span>        step take default</span>

<span>
  <strong>step chooseleaf firstn 0 type host</strong>
</span>

**<span>ß</span>

<span>Default distribution of PG copies</span>**

<span>        step emit</span>

Whenever a Ceph node goes down, the system will start rebuilding from the copies of replicas. While this is an expected feature of Ceph, it causes some CPU and memory overhead too. This is one of the reasons to have a minimum of 3 nodes for ceph and leave some good amount of free space with in the storage cluster. This will help Ceph to move the blocks around in case of failures like this. More the nodes better it is, as this rebuild activity is distributed across the cluster. Though there are other parameters like osd_max_backfills to control this activity and its impact on CPU, it may not be feasible to cover all of these recovery parameters in this document.

What needs to be noted is that the recovery kicks in as part of the tests below. The ceph cluster status may show warnings while the tests are being conducted as it is moving the placement groups and may cause performance issues on the storage cluster. Hence checking the health of nodes while adding/rebuilding a new node is important.

1.Check the status of the cluster:

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_246.jpg)

2.Reboot one of the Ceph storage node:

The following was observed during the reboot and running ceph -w

<span>mon.0 [INF] osd.13 marked itself down</span>

<span>mon.0 [INF] osd.15 marked itself down</span>

<span>mon.0 [INF] osd.10 marked itself down</span>

<span>mon.0 [INF] osd.8 marked itself down</span>

<span>mon.0 [INF] osd.6 marked itself down</span>

<span>mon.0 [INF] osd.4 marked itself down</span>

<span>mon.0 [INF] osd.2 marked itself down</span>

<span>mon.0 [INF] osd.0 marked itself down</span>

<span>mon.0 [INF] osdmap e176: 24 osds: 16 up, 24 in</span>

<span>
  <br>
</span>

 ceph osd tree reports

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_247.jpg)

3.Make sure the VM's connectivity through floating IP from an external host is successful.

<span>Host is tenant310-110-inst2 and Network is inet 10.2.110.6  netmask 255.255.255.0</span>

<span>Host is tenant310-160-inst3 and Network is inet 10.2.160.5  netmask 255.255.255.0</span>

<span>Host is tenant310-160-inst4 and Network is inet 10.2.160.6  netmask 255.255.255.0</span>

<span>Host is tenant311-111-inst1 and Network is inet 10.2.111.5  netmask 255.255.255.0</span>

<span>Host is tenant311-111-inst2 and Network is inet 10.2.111.6  netmask 255.255.255.0</span>

<span>Host is tenant311-161-inst3 and Network is inet 10.2.161.5  netmask 255.255.255.0</span>

<span>Host is tenant311-161-inst4 and Network is inet 10.2.161.6  netmask 255.255.255.0</span>

<span>Host is tenant312-112-inst1 and Network is inet 10.2.112.6  netmask 255.255.255.0</span>

4.Ceph attempts recovery as shown below:

<span>[root@overcloud-cephstorage-1 ~]# ceph -s </span>

<span>    cluster 701e64ca-8e54-11e5-9eef-0025b522225f</span>

<span>     health HEALTH_WARN</span>

<span>
  <strong>256 pgs degraded</strong>
</span>

<span>            …………..</span>

<span>            8/24 in osds are down</span>

<span>     monmap e2: 3 mons at {overcloud-controller-0=10.22.120.52:6789/0,</span>

<span>overcloud-controller-1=10.22.120.51:6789/0,overcloud-controller-2=10.22.120.57:6789/0}</span>

<span>            election epoch 30, quorum 0,1,2 overcloud-controller-1,overcloud-controller-0,overcloud-controller-2</span>

<span>     osdmap e177: 24 osds: 16 up, 24 in</span>

<span>      pgmap v50622: 256 pgs, 4 pools, 97547 MB data, 24754 objects</span>

<span>
  <strong>24754/74262 objects degraded (33.333%)</strong>
</span>

<span>  client io 1473 B/s rd, 411 kB/s wr, 108 op/s</span>

The node comes after few minutes, while the cluster shows warning issues during the reboot period.

The status of the cluster observed fine after few minutes of reboot. The warning message continues until the recovery activity is complete.

The behavior in system power off is very similar to what observed on Controller and Compute blade pull tests.

System took around 6 minutes to come back to OK status. The time system takes to recover depends on the active number of placement group and copies the system was attempting to move around.

There is a more detailed description and symptoms observed during power off that are listed in Node Replacement section below.

One of the storage servers was powered off ( pull the power cord ) completely and the server was decommissioned in UCS. This is to simulate a complete failure of the storage server. Then an attempt was made to remove this node from OpenStack and add a new one to the cloud. The following were the tasks list and observations made during a Storage node replacement test.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Node replacement is actually a two phase process. First remove the server from the system and then add a new one.

To delete a node, complete the following steps:

1.Power off the node by pulling the power cord from a running cluster.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_248.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_249.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_250.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_251.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_252.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_253.jpg)

**2.**Check the health of placement groups:

<span>[root@overcloud-cephstorage-0 ~]# ceph pg dump_stuck stale</span>

<span>[root@overcloud-cephstorage-0 ~]# ceph pg dump_stuck inactive</span>

<span>[root@overcloud-cephstorage-0 ~]# ceph pg dump_stuck unclean</span>

3.Run Ceph PG dump to validate that the OSD's do not have any copies.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_254.jpg)

This makes sure that there is nothing in osd.24 to osd.31\. These are the OSD's that are part of the node that was deleted. Ceph moved all the copies from this node to other node.

Making sure that no placement groups are attached to the OSD's using ceph pg dump or ceph osd stat makes sure of data integrity. The above command confirms that all the data has been moved out of the OSD's. It is not recommended to delete a node with any placement groups residing in these OSD's. Please wait till the recovery activity is complete. Do not let the Ceph cluster reach its full ratio when removing nodes or OSD's. Removing OSD's could cause the cluster to reach full ratio and could cause data integrity issues.

1.IPMI status.

As the node is switched off it is not reachable through IPMI.

<span>[stack@osp7-director ~]$ ipmitool -I lanplus -H 10.22.100.8 -U admin -P nbv12345 chassis power status </span>

<span>Error: Unable to establish IPMI v2 / RMCP+ session</span>

<span>Error: Unable to establish IPMI v2 / RMCP+ session</span>

<span>Error: Unable to establish IPMI v2 / RMCP+ session</span>

<span>Unable to get Chassis Power Status</span>

2.Update the driver entries to work around the issue.

<span>edit  vi /etc/ironic/ironic.conf</span>

<span>Update the enabled drivers temporarily as below</span>

<span>#enabled_drivers=pxe_ipmitool,pxe_ssh,pxe_drac</span>

<span>enabled_drivers=fake</span>

<span>Restart openstack-ironic-conductor</span>

<span>sudo service openstack-ironic-conductor restart</span>

<span>ironic node-update NODE_UUID replace driver=fake</span>

The node in ironic node-list should be with provision-state=active and maintenance=false

If not

<span>ironic node-set-provision-state NODE_UUID provide</span>

<span>ironic node-set-provision-state NODE_UUID active</span>

<span>ironic node-set-provision-state NODE_UUID deleted</span>

The power status should be on, provision state as available and maintenance as false

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_255.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_256.jpg)

3.Delete the node from nova and ironic.

Run nova service-list and identify the service id's

Delete the service id's associated with this node as

<span>nova service-delete $id</span>

<span>nova delete NODE_UUID </span>

<span>ironic node-delete NODE_UUID </span>

<span>Revert back the “fake” driver from ironic.conf.</span>

<span>edit vi /etc/ironic/ironic.conf.</span>

<span>enabled_drivers=pxe_ipmitool,pxe_ssh,pxe_drac</span>

<span>#enabled_drivers=fake</span>

<span>Restart ironic-conductor to pick up the drivers again.</span>

<span>service openstack-ironic-conductor restart</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Storage node deletion differs from compute node deletion here. In both the cases we have deleted the nodes from UCS and OpenStack so far. However ceph entries still remain and these have to be cleaned up.

To clean up Ceph after a node deletion, complete the following steps:

1.Check the details from ceph health and osd tree:

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_257.jpg)

<span>[root@overcloud-cephstorage-0 ~]# ceph osd stat </span>

<span>     osdmap e132: 32 osds: 24 up, 24 in</span>

<span>[root@overcloud-cephstorage-0 ~]#</span>

2.Remove OSD's from Ceph. Change the OSD ID's to your setup and from the output of osd tree above.

<span>for i in `seq 24 31`</span>

<span>ceph osd out $i</span>

<span>ceph osd crush remove osd.$i</span>

<span>ceph auth del osd.$i</span>

<span>ceph osd rm $i</span>

<span>done</span>

<span>osd.24 is already out. </span>

<span>removed item id 24 name ‘osd.24’ from crush map</span>

<span>updated</span>

<span>removed osd.24</span>

<span>osd.25 is already out. </span>

<span>removed item id 25 name ‘osd.25’ from crush map</span>

<span>updated</span>

<span>removed osd.25</span>

<span>osd.26 is already out. </span>

<span>removed item id 26 name ‘osd.26’ from crush map</span>

<span>updated</span>

<span>removed osd.26</span>

<span>osd.27 is already out. </span>

<span>removed item id 27 name ‘osd.27’ from crush map</span>

<span>updated</span>

<span>removed osd.27</span>

<span>osd.28 is already out. </span>

<span>removed item id 28 name ‘osd.28’ from crush map</span>

<span>updated</span>

<span>removed osd.28</span>

<span>osd.29 is already out. </span>

<span>removed item id 29 name ‘osd.29’ from crush map</span>

<span>updated</span>

<span>removed osd.29</span>

<span>osd.30 is already out. </span>

<span>removed item id 30 name ‘osd.30’ from crush map</span>

<span>updated</span>

<span>removed osd.30</span>

<span>osd.31 is already out. </span>

<span>removed item id 31 name ‘osd.31’ from crush map</span>

<span>updated</span>

<span>removed osd.31</span>

<span>[root@overcloud-cephstorage-0 ~]#</span>

3.Clean up ceph crush host entries:

<span>[root@overcloud-controller-0 ~]# ceph osd crush remove overcloud-cephstorage-3 </span>

4.Health checks after deletion:

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_258.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_259.png)

When the storage node has been completely removed from OpenStack and the ceph entries cleaned, a new server can be added. The procedure for adding a new storage node is same as how it was addressed earlier in [upscaling the storage pod](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.html#ScaleUpCephNodes).

RHEL-OSP7 supports only one Undercloud Node as of the date this document was first published. Also in the test bed, the compute and storage nodes are natted through Undercloud node. Though this does not pose any challenges during Overcloud operation, any future heat stack or overcloud deploys could be impacted.

The following backup and recovery method has been documented on Red Hat web site for reference. This procedure has not been validated in this CVD. It is strongly recommended to test the below procedure in a test environment and document the process to restore the Undercloud node from backup. Subsequently take a backup of the Undercloud node and store the back up for an easy retrieval later in case of failures.

[https://access.redhat.com/webassets/avalon/d/Red_Hat_Enterprise_Linux_OpenStack_Platform-7-Back_Up_and_Restore_Red_Hat_Enterprise_Linux_OpenStack_Platform-en-US/Red_Hat_Enterprise_Linux_OpenStack_Platform-7-Back_Up_and_Restore_Red_Hat_Enterprise_Linux_OpenStack_Platform-en-US.pdf](https://access.redhat.com/webassets/avalon/d/Red_Hat_Enterprise_Linux_OpenStack_Platform-7-Back_Up_and_Restore_Red_Hat_Enterprise_Linux_OpenStack_Platform-en-US/Red_Hat_Enterprise_Linux_OpenStack_Platform-7-Back_Up_and_Restore_Red_Hat_Enterprise_Linux_OpenStack_Platform-en-US.pdf%20%0d)

The hardware failures of blades are infrequent and happen very rarely. Cisco stands behind the customers to support in such conditions. There is also a [Return Materia Authorization (RMA) process](http://www.cisco.com/c/en/us/support/rma_portal.html) in place. Depending on the types of failure, either the parts or the entire blade may be replaced. This section at a high level covers the types of failures that could happen on Cisco UCS blades running OpenStack and how to get the system up and running with little or no business interruption.

Before dwelling into the details, we would like to highlight that this section was validated specifically for Controller blades. The replacement of 

[<span>compute</span>](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.html#Compute_Blade_Replacement)

 and 

[<span>storage</span>](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.html#Storage_Node_Replacement)

 blades are covered earlier in the High Availability section.

<span>·</span>

CPU Failures

<span>·</span>

Memory or DIMM Failures

<span>·</span>

Virtual Interface Card Failures

<span>·</span>

Motherboard Failures

<span>·</span>

Hard Disk Failures

<span>·</span>

Chassis Slot Issues

Any such failures happening on a blade either leads to degraded performance while the system continues to operate (like DIMM or disk Failures) or it could fail completely. In case of complete failures, OpenStack Nova and Ironic may also take them offline and there is a need to fix the errors.

A compute node failure will impact only the VM's running on the compute node and these can be evacuated to another node.

Ceph storage nodes are configured with replication factor of 3, and the system continues to operate though the recovery operation may cause slight degraded performance of the storage cluster.

In case of total failure of controller blades, the fencing packages will fence the failed node. You may need to fix for bug 1303698 for this. This is not included in RHEL-OSP 7.2 release and instructions are provided how to get the patches and customize the Overcloud image earlier in this document. With the fix in place, the system continues to operate in a degraded mode (performance impact while navigating through dashboard and while creating new virtual machines).

From OpenStack point of view, the following hardware variables are seeded into the system and these may have to be addressed in case of failures:

OpenStack uses IPMI address and it powers on/off the blades with this address. These can be queried through ironic API's as below.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_260.jpg)

The controller ethernet interfaces and MAC addresses are available in the Local Disk of the failed blade. Hence failure cases of hard disks is also included above. Apart from this, the provisioning Interface MAC address is also stored in the Undercloud node.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_261.jpg)

Retain these addresses in case of failures.

The local hard disk has all the configuration information and should be available. Hence it is strongly recommended to have a pair of Local Disks in RAID-10 configuration to overcome against disk failures.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Post hardware failures, if all of the above are brought back, the system can be made operational and this is what is addressed in this section.

As mentioned earlier there can be several types of failures including CPU or Memory and system may perform in a degraded fashion. Not all of these are covered in this document, but the ones which have hooks to OpenStack are covered here.

Assuming you have followed the recommendations to have RAID-10 configuration for the local disks, failure of one disk will be taken care by the RAID controller.

In case there is a need to replace the blade, the ipmi address, MAC addresses and Local disks have to be restored. It is assumed that there is no double failure here.

The IPMI addresses are allocated from the KVM pool. Once a blade fails system will hold the address until it has been decommissioned. If system is decommissioned, it will release the free IP to KVM Pool. We can allocate this free old IP to the new blade. The below figure shows how to change the IPMI address in UCS as an example.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_262.jpg)

Service profiles are like SIM card of a phone, that store all the hardware identity. Once the Service Profile is disassociated from the failed node and attached to the new node, all of the policies like Boot Policy and Network interfaces along with MAC addresses are available to the new blade.

The two hard disks can be taken out from the failed blade and inserted into the new blade. You have to make sure that the new blade is identical and upgraded to the same firmware version as of the failed blade. The local disks have the controller binaries and the cluster configuration information. Associating the service profile will bring up all the hardware profiles on the new blade. Hence now system will be in sync from both hardware and software side and should be up and running.

The following case study describes the step by step process on how to replace the controller blade.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_263.jpg)

**PCS Status**

<span>[root@overcloud-controller-0 ~]# pcs status</span>

<span>Cluster name: tripleo_cluster</span>

<span>Last updated: Sat Feb 13 17:22:32 2016          Last change: Sat Feb 13 17:12:09 2016 by </span>

<span>root via cibadmin on overcloud-controller-2</span>

<span>Stack: corosync</span>

<span>Current DC: overcloud-controller-0 (version 1.1.13-10.el7-44eb2dd) - partition with quorum</span>

<span>3 nodes and 115 resources configured</span>

<span>Online: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span>Full list of resources:</span>

<span> ip-172.22.219.204      (ocf::heartbeat:IPaddr2):     Started overcloud-controller-0</span>

<span> ip-20.7.40.51    (ocf::heartbeat:IPaddr2):     Started overcloud-controller-1</span>

<span> Clone Set: haproxy-clone [haproxy]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> ip-20.7.30.51    (ocf::heartbeat:IPaddr2):     Started overcloud-controller-0</span>

<span> ip-20.7.20.99    (ocf::heartbeat:IPaddr2):     Started overcloud-controller-1</span>

<span> Master/Slave Set: galera-master [galera]</span>

<span>     Masters: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> ip-20.7.10.51    (ocf::heartbeat:IPaddr2):     Started overcloud-controller-0</span>

<span> ip-20.7.10.52    (ocf::heartbeat:IPaddr2):     Started overcloud-controller-1</span>

<span> Master/Slave Set: redis-master [redis]</span>

<span>     Masters: [ overcloud-controller-0 ]</span>

<span>     Slaves: [ overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: mongod-clone [mongod]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> ucs-fence-controller   (stonith:fence_cisco_ucs):    Started overcloud-controller-2</span>

<span>……………………………………..</span>

<span>……………………………………..</span>

<span>……………………………………..</span>

<span>PCSD Status:</span>

<span>  overcloud-controller-0: Online</span>

<span>  overcloud-controller-1: Online</span>

<span>  overcloud-controller-2: Online</span>

<span>Daemon Status:</span>

<span>  corosync: active/enabled</span>

<span>  pacemaker: active/enabled</span>

<span>  pcsd: active/enabled</span>

**Quorum and CRM Node Information**

<span>[root@overcloud-controller-0 ~]# corosync-quorumtool</span>

<span>Quorum information</span>

<span>——————</span>

<span>Date:             Sat Feb 13 17:48:45 2016</span>

<span>Quorum provider:  corosync_votequorum</span>

<span>Nodes:            3</span>

<span>Node ID:          3</span>

<span>Ring ID:          2776</span>

<span>Quorate:          Yes</span>

<span>Votequorum information</span>

<span>———————-</span>

<span>Expected votes:   3</span>

<span>Highest expected: 3</span>

<span>Total votes:      3</span>

<span>Quorum:           2  </span>

<span>Flags:            Quorate </span>

<span>Membership information</span>

<span>———————-</span>

<span>    Nodeid      Votes Name</span>

<span>         2          1 overcloud-controller-1</span>

<span>         1          1 overcloud-controller-0</span>

<span>         3          1 overcloud-controller-2 (local)</span>

<span>[root@overcloud-controller-0 ~]#</span>

<span>[root@overcloud-controller-0 ~]# crm_node -l</span>

<span>3 overcloud-controller-2 member</span>

<span>2 overcloud-controller-1 member</span>

<span>1 overcloud-controller-0 member</span>

<span>[root@overcloud-controller-0 ~]#</span>

**Tenants Availability and Checks**

Tenants inventory for the Controller failure test.

<span>[stack@osp7-director2 ~]$ nova list –all-tenants |grep tenant320_120_inst8</span>

<span>| 20c1b528-d467-4a5a-bf96-76c49c91557f | tenant320_120_inst8  | b69d957c3dc8400093644664d8f2082f | ACTIVE | -          | Running     | tenant320-120=10.2.120.10, 10.22.160.38 |</span>

<span>[stack@osp7-director2 ~]$ </span>

<span>[stack@osp7-director2 ~]$ </span>

<span>[stack@osp7-director2 ~]$ nova list –all-tenants |grep tenant320_170_inst19</span>

<span>| 19917b9f-2581-4e53-ace0-87381d4dad8a | tenant320_170_inst19 | b69d957c3dc8400093644664d8f2082f | ACTIVE | -          | Running     | tenant320-170=10.2.170.11, 10.22.160.49 |</span>

<span>[stack@osp7-director2 ~]$ </span>

Make sure the VMs can ping East to West traffic and South to North traffic.

Pinging from tenant320_170_inst19 to tenant320_120_inst8 

[<span>10.2.120.10</span>](http://10.2.170.11/)

 & 

[<span>10.22.160</span>](http://10.2.170.11/)

.38

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_264.png)

Insert the new blade into the chassis.

Identify the overcloud-controller-2 and UCS Service Profile mapping from /etc/neutron/plugin.ini on any other controller node.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_265.png)

<span>[root@overcloud-controller-0 ~]# crm_node -l</span>

**<span>3 overcloud-controller-2 lost</span>**

<span>2 overcloud-controller-1 member</span>

<span>1 overcloud-controller-0 member</span>

<span>[root@overcloud-controller-0 ~]# </span>

<span>[root@overcloud-controller-0 ~]# pcs status</span>

<span>Cluster name: tripleo_cluster</span>

<span>Last updated: Sat Feb 13 18:07:44 2016       Last change: Sat Feb 13 17:12:09 2016 by root via cibadmin on overcloud-controller-2</span>

<span>Stack: corosync</span>

<span>Current DC: overcloud-controller-0 (version 1.1.13-10.el7-44eb2dd) - partition with quorum</span>

<span>3 nodes and 115 resources configured</span>

<span>Online: [ overcloud-controller-0 overcloud-controller-1 ]</span>

**<span>OFFLINE: [ overcloud-controller-2 ]</span>**

<span>Full list of resources:</span>

<span> ip-172.22.219.204   (ocf::heartbeat:IPaddr2):     Started overcloud-controller-0</span>

<span> ip-20.7.40.51 (ocf::heartbeat:IPaddr2):     Started overcloud-controller-1</span>

<span> Clone Set: haproxy-clone [haproxy]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 ]</span>

<span>
  <strong>Stopped: [ overcloud-controller-2 ]</strong>
</span>

<span> ip-20.7.30.51 (ocf::heartbeat:IPaddr2):     Started overcloud-controller-0</span>

<span> ip-20.7.20.99 (ocf::heartbeat:IPaddr2):     Started overcloud-controller-1</span>

<span> Master/Slave Set: galera-master [galera]</span>

<span>     Masters: [ overcloud-controller-0 overcloud-controller-1 ]</span>

<span>     Stopped: [ overcloud-controller-2 ]</span>

<span> ip-20.7.10.51 (ocf::heartbeat:IPaddr2):     Started overcloud-controller-0</span>

<span> ip-20.7.10.52 (ocf::heartbeat:IPaddr2):     Started overcloud-controller-1</span>

<span> Master/Slave Set: redis-master [redis]</span>

<span>     Masters: [ overcloud-controller-0 ]</span>

<span>     Slaves: [ overcloud-controller-1 ]</span>

<span>
  <strong>Stopped: [ overcloud-controller-2 ]</strong>
</span>

<span> Clone Set: mongod-clone [mongod]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 ]</span>

<span>
  <strong>Stopped: [ overcloud-controller-2 ]</strong>
</span>

<span>[root@overcloud-controller-0 ~]# corosync-quorumtool</span>

<span>Quorum information</span>

<span>——————</span>

<span>Date:             Sat Feb 13 18:16:20 2016</span>

<span>Quorum provider:  corosync_votequorum</span>

<span>Nodes:            2</span>

<span>Node ID:          1</span>

<span>Ring ID:          2780</span>

<span>Quorate:          Yes</span>

<span>Votequorum information</span>

<span>———————-</span>

**<span>Expected votes:   3</span>**

**<span>Highest expected: 3</span>**

**<span>Total votes:      2</span>**

**<span>Quorum:           2  </span>**

<span>Flags:            Quorate </span>

<span>Membership information</span>

<span>———————-</span>

<span>    Nodeid      Votes Name</span>

<span>         2          1 overcloud-controller-1</span>

<span>         1          1 overcloud-controller-0 (local)</span>

<span>[root@overcloud-controller-0 ~]#</span>

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

When the blade is removed from the chassis, pcs status shows resources as Unclean until the operation interval. This is configured as one minute. Hence wait for 1-2 minutes, make sure that pcs status only shows stopped as above and not unclean or unmanaged and then move forward.

**Nova and Ironic status**

After 15-20mins... the nova status changed to NOSTATE and Ironic power state changed to NONE and the node was under maintenance mode.

<span>[stack@osp7-director2 ~]$ nova list |grep controller</span>

<span>| 4c23b209-094e-4156-aed9-bd6853ad3c04 | overcloud-controller-0  | ACTIVE | -          | Running     | ctlplane=20.7.20.45  |</span>

<span>| e5336e56-6f28-4f1e-87ab-b207123a9746 | overcloud-controller-1  | ACTIVE | -          | Running     | ctlplane=20.7.20.44  |</span>

<span>| 39bd3156-f73a-45d9-acda-8b9a13b333b6 | overcloud-controller-2  | ACTIVE | -          | <span>NOSTATE</span>     | ctlplane=20.7.20.65  |</span>

<span>[stack@osp7-director2 ~]$ </span>

<span>[stack@osp7-director2 ~]$ </span>

<span>[stack@osp7-director2 ~]$ ironic node-list |grep 39bd3156-f73a-45d9-acda-8b9a13b333b6</span>

<span>| ff1bc3c8-fcba-409f-9115-5e10d6e49ab4 | None | 39bd3156-f73a-45d9-acda-8b9a13b333b6 | <span>None        | active          | True</span>        |</span>

<span>[stack@osp7-director2 ~]$ </span>

<span>[stack@osp7-director2 ~]$</span>

**Tenant and VM's Status**

Make sure that you can login to dashboard, create new VM's and North-South and East-West traffic between VM's is uninterrupted. You may observe slowness in creating the VM's though.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_266.png)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_267.png)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_268.png)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_269.png)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_270.png)

Remove the boot disks from the failed blade and insert them into the new blade. Make sure that the old disks show up in the server inventory.

![Description: C:\Users\vijd\Desktop\Austin-CVD\Screen Shot 2016-02-13 at 3.23.33 PM.png](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_271.png)

Associate the existing service profile that was disassociated earlier from the failed blade to the new or replacement blade.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_272.png)

Make sure that Config is in progress and monitor the status in FSM tab of the server.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_273.png)

In rare occasions the existing storage policy may not let you associate the new blade. As the service profile is already unbound from the template, you may remove the storage profile from the service profile, reboot the server and then attach back the storage profile to this service profile.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_274.png)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_275.png)

The association should boot up the server based on the desired power state, otherwise boot it up. It should show you the login prompt as below.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_276.png)

Even though the server is up and running, you may need the following steps to let the server join back the cluster again.

<span>[stack@osp7-director2 ~]$ nova list |grep controller</span>

<span>| 4c23b209-094e-4156-aed9-bd6853ad3c04 | overcloud-controller-0  | ACTIVE | -          | Running     | ctlplane=20.7.20.45  |</span>

<span>| e5336e56-6f28-4f1e-87ab-b207123a9746 | overcloud-controller-1  | ACTIVE | -          | Running     | ctlplane=20.7.20.44  |</span>

<span>| 39bd3156-f73a-45d9-acda-8b9a13b333b6 | overcloud-controller-2  | ACTIVE | <span>-          | NOSTATE</span>     | ctlplane=20.7.20.65  |</span>

<span>[stack@osp7-director2 ~]$ </span>

<span>[stack@osp7-director2 ~]$ ironic node-list |grep 39bd3156-f73a-45d9-acda-8b9a13b333b6</span>

<span>| ff1bc3c8-fcba-409f-9115-5e10d6e49ab4 | None | 39bd3156-f73a-45d9-acda-8b9a13b333b6 | <span>None</span>        | active          | <span>True</span>        |</span>

The server is in maintenance mode and in 'NOSTATE' mode.

Use nova and ironic commands to bring them to normal state.

<span>[stack@osp7-director2 ~]$ ironic node-set-power-state ff1bc3c8-fcba-409f-9115-5e10d6e49ab4 on</span>

<span> [stack@osp7-director2 ~]$ ironic node-set-maintenance ff1bc3c8-fcba-409f-9115-5e10d6e49ab4 false</span>

<span> [stack@osp7-director2 ~]$ ironic node-list |grep 39bd3156-f73a-45d9-acda-8b9a13b333b6</span>

<span>| ff1bc3c8-fcba-409f-9115-5e10d6e49ab4 | None | 39bd3156-f73a-45d9-acda-8b9a13b333b6 | <span>power on    </span>| active          | <span>False       </span>|</span>

<span>[stack@osp7-director2 ~]$  nova reset-state –active 39bd3156-f73a-45d9-acda-8b9a13b333b6 </span>

<span>[stack@osp7-director2 ~]$ </span>

It may take around 10 minutes before the NOVA state is changed to running state.

<span>[stack@osp7-director2 ~]$ </span>

<span>[stack@osp7-director2 ~]$ nova list |grep controller</span>

<span>| 4c23b209-094e-4156-aed9-bd6853ad3c04 | overcloud-controller-0  | ACTIVE | -          | Running     | ctlplane=20.7.20.45  |</span>

<span>| e5336e56-6f28-4f1e-87ab-b207123a9746 | overcloud-controller-1  | ACTIVE | -          | Running     | ctlplane=20.7.20.44  |</span>

<span>| 39bd3156-f73a-45d9-acda-8b9a13b333b6 | overcloud-controller-2  | ACTIVE | -          | <span>Running</span>     | ctlplane=20.7.20.65  |</span>

<span>[stack@osp7-director2 ~]$ </span>

Log into any one of the controllers and check the status from pacemaker. If any services observed to be down, you may restart them with pcs resource cleanup.

<span>[root@overcloud-controller-0 ~]# corosync-quorumtool -s</span>

<span>Quorum information</span>

<span>——————</span>

<span>Date:             Sat Feb 13 18:53:20 2016</span>

<span>Quorum provider:  corosync_votequorum</span>

<span>Nodes:            3</span>

<span>Node ID:          1</span>

<span>Ring ID:          2784</span>

<span>Quorate:          Yes</span>

<span>Votequorum information</span>

<span>———————-</span>

<span>Expected votes:   3</span>

<span>Highest expected: 3</span>

**<span>Total votes:      3</span>**

<span>Quorum:           2  </span>

<span>Flags:            Quorate </span>

<span>Membership information</span>

<span>———————-</span>

<span>    Nodeid      Votes Name</span>

<span>         2          1 overcloud-controller-1</span>

<span>         1          1 overcloud-controller-0 (local)</span>

<span>         3          1 overcloud-controller-2</span>

<span>[root@overcloud-controller-0 ~]# </span>

<span>[root@overcloud-controller-0 ~]# crm_node -l</span>

**<span>3 overcloud-controller-2 member</span>**

**<span>2 overcloud-controller-1 member</span>**

**<span>1 overcloud-controller-0 member</span>**

<span>[root@overcloud-controller-0 ~]# pcs resource cleanup</span>

<span>Waiting for 1 replies from the CRMd. OK</span>

<span>[root@overcloud-controller-0 ~]# </span>

<span>[root@overcloud-controller-0 ~]# pcs status</span>

<span>Cluster name: tripleo_cluster</span>

<span>Last updated: Sat Feb 13 18:54:41 2016          Last change: Sat Feb 13 17:12:09 2016 by root via cibadmin on overcloud-controller-2</span>

<span>Stack: corosync</span>

<span>Current DC: overcloud-controller-0 (version 1.1.13-10.el7-44eb2dd) - partition with quorum</span>

<span>3 nodes and 115 resources configured</span>

<span>Online: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span>Full list of resources:</span>

<span> ip-172.22.219.204      (ocf::heartbeat:IPaddr2):     Started overcloud-controller-0</span>

<span> ip-20.7.40.51    (ocf::heartbeat:IPaddr2):     Started overcloud-controller-1</span>

<span> Clone Set: haproxy-clone [haproxy]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> ip-20.7.30.51    (ocf::heartbeat:IPaddr2):     Started overcloud-controller-0</span>

<span> ip-20.7.20.99    (ocf::heartbeat:IPaddr2):     Started overcloud-controller-1</span>

<span> Master/Slave Set: galera-master [galera]</span>

<span>     Masters: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span> ip-20.7.10.51    (ocf::heartbeat:IPaddr2):     Started overcloud-controller-0</span>

<span> ip-20.7.10.52    (ocf::heartbeat:IPaddr2):     Started overcloud-controller-1</span>

<span> Master/Slave Set: redis-master [redis]</span>

<span>     Masters: [ overcloud-controller-0 ]</span>

<span>     Slaves: [ overcloud-controller-1 overcloud-controller-2 ]</span>

<span> Clone Set: mongod-clone [mongod]</span>

<span>     Started: [ overcloud-controller-0 overcloud-controller-1 overcloud-controller-2 ]</span>

<span>……………………………………………..</span>

<span>……………………………………………..</span>

<span>……………………………………………..</span>

<span> ucs-fence-controller   (stonith:fence_cisco_ucs):    Started overcloud-controller-2</span>

<span>PCSD Status:</span>

<span>  overcloud-controller-0: Online</span>

<span>  overcloud-controller-1: Online</span>

<span>  overcloud-controller-2: Online</span>

<span>Daemon Status:</span>

<span>  corosync: active/enabled</span>

<span>  pacemaker: active/enabled</span>

<span>  pcsd: active/enabled</span>

<span> [root@overcloud-controller-0 ~]# pcs status | grep stop</span>

<span>[root@overcloud-controller-0 ~]# </span>

<span>·</span>

Can we use IO Modules 2208 instead of IOM 2204 as shown in the topology diagram?

Yes, both IOM 2204 and IOM 2208 are supported. For more details refer the design guide here.

<span>·</span>

When should we use C240M4S and when C240M4L for storage servers?

This boils down as a design question and depends on the requirement. The C240M4 SFF, small form factor offers more spindles and hence higher IOPS with reasonable bandwidth capacity. The C240M4 LFF, the large form factor has a higher storage capacity but may not be as good as SFF on total IOPS per node. Validation has been done and the performance metrics provided that should help you choose the right hardware.

<span>·</span>

Can I use different hardware like Cisco M3 blades and different VIC adapters in the solution?

Cisco hardware higher than the version in the BOM are supported. While lower versions may still work, they have not been validated.

<span>·</span>

How many chassis or blades and servers can I scale horizontally?

The validation was done with 3 fully loaded chassis with blades and 12 x C240M4L storage nodes for ceph. From hardware point of view, it could be the port limits and you may have to go with 96 ports Fabric Interconnects or Nexus switches.

However as number of blades increase the controller and neutron activity increases. Validation was done only with 3 controllers.

<span>·</span>

Why aren't the internal SSD drives for Ceph storage nodes included in the BOM?

Current versions of ironic cannot identify internal drives as the ordering would vary from system to system. You may notice that you are using only disks attached to the RAID controller, which allows us to enforce a certain disk ordering. The internal drives are not connected to this RAID controller and thus would be unordered.

<span>·</span>

How can I connect my openstack to an existing Ceph installation?

Please refer Red Hat documentation in <https://access.redhat.com/articles/1994713>.

<span>·</span>

My network topology differs from what mentioned in this document. What changes I need to do to the configuration?

The network topology verified in the configuration is included in the Appendix A. There were limited IP's and the floating network was used. It is not necessary to have the same settings. However you may have to change yaml files accordingly and tweaks may be necessary. Please refer Red Hat documentation on how to accommodate these changes in the template files.

<span>·</span>

Updating yaml files is error prone. A simple white space or tab causing issues. What should I do?

It is recommended to use online parser followed by running network-validator before running Overcloud. Please look at Overcloud Install section for details.

<span>·</span>

Do you have specific recommendations for Live Migration?

Live migration was attempted both in tunneling and converged modes. While the former is more secure because of encryption, the latter is performant. Please refer Live Migration section on changes needed in nova.conf to accommodate these.

<span>·</span>

Why version lock directives have been delegated in this document?

OpenStack is continuously updated and changes in binaries and configurations go neck and neck. The purpose of providing lock file is to lock and provide binaries as close as possible to the validated design. This ensures consistency with minimal deviations from the validate design and adoption of configuration files like the yaml files. You can always install a higher version than mentioned but the specifics needed on configuration files may vary and/or some of the validations that were done in this document may have to be redone to avoid any regressions.

A few troubleshooting areas that may help while installing RHEL-OSP 7 on Cisco UCS servers is presented here. Troubleshooting in Openstack is exhaustive and this section limits what helped in debugging the install. Necessary links as needed are provided too. This is only an effort to help the readers to narrow down the issues. It is assumed that the reader has followed the pre and post install steps mentioned earlier.

<span>·</span>

The provisioning interface should be [enabled as native](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.html#Cisco_UCS_Validation_Checks) across all the blades and rack servers for successful introspection and Overcloud deploy.

<span>·</span>

In case you are attempting a repeat of full deployment it is recommended to [re-initialize the boot luns](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.html#Initialize_Luns). The wipe_disk.yaml will work on storage partitions after OS is installed but not for the boot luns.

<span>·</span>

The [native flag](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.html#Cisco_UCS_Validation_Checks) for external network shouldn't be enabled on Overcloud nodes as observed on the test bed.

<span>·</span>

Specify the [PCI order](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.html#vNIC_Placement) for network interfaces. This ensures that they are enumerated in the same way as specified in the templates.

<span>·</span>

In case of using updating templates make sure that the service profiles are unbound from the service profile template for successful operation of UCS Manager Plugin.

<span>·</span>

Before applying service profiles, you should make sure that all the disks are in 'Unconfigured Good' status. The storage profile, that is attached to these service profiles will then successfully get applied and then will make the boot lun in operable mode.

Undercloud install observed to be straight forward and very few issues observed. Mostly these were human mistakes like typos in the configuration file.

<span>·</span>

Make sure that the server is registered with Red Hat Content Delivery Network for downloading the packages. In case the server is behind proxy, update /etc/rhsm/rhsm.conf file with appropriate proxy server values.

<span>·</span>

Double check the entries in Undercloud configuration file. Provide enough room for discovery_iprange and dhcp start/end, also considering the future expansion or upscaling of the servers later. Most of these parameters are explained in the sample file provided in /usr/share.

<span>·</span>

Leave the value of undercloud_debug=true as default to check for failures. The log file install-undercloud.log is created as part of Undercloud install in /home/stack/.instack. This will be handy to browse through on iussues encountered during the install.

<span>·</span>

A repeat of Undercloud install preferably has to be done in a cleaner environment after reinstalling the base operating system.

Failure of introspecting the nodes can by many. Make sure that you have verified all the [post undercloud](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.html#Post_Undercloud_Installation) and [pre-introspection](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.html#Pre_Introspection_checks) steps mentioned earlier in this document.

<span>·</span>

A correct value of ipmi and mac addresses and powering on/off with ipmitool as mentioned earlier in this document should isolate the issues. Check with ironic node-list and ironic node-show to ensure that the registered values are correct.

<span>·</span>

The boot luns configured in UCS through storage profile should be in available state before starting introspection. The size of the lun specified in the instack.json file should be equal or less that the size of the lun seen in UCS.

The best way to debug introspection failures is to open KVM console on the server and check for issues. The below screenshot warns something wrong in my instack configuration file.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_277.jpg" id="Picture 325" width="624">
</span>

A successful introspection is shown below:

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_278.jpg)

<span>·</span>

In case system takes you to the shell prompt and dumps /run/initramfs/sosreport.txt provides some insight as well.

<span>·</span>

dnsmasq is the dhcp process that pxe uses to discover. Within the provisioning subnet configured you should have only one dhcp process or this dnsmasq process running on the Undercloud node. Any overlap will cause discovery failures.

<span>·</span>

Running 'sudo –u journalctl -u openstack-ironic-discoverd -u openstack-ironic-discoverd-dnsmasq will show issues encountered by discovered and dnsmasq.

<span>·</span>

Monitoring introspection with 'openstack bare metal introspection bulk status' will show if any few servers have failed.

<span>·</span>

At times if the status of the node(s) becomes available, you may have to update the status to manageable with ironic API before running introspection.

<span>·</span>

The [default value](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.html#pxe_timeout) of introspection is 60 minutes. This may have to be changed as mentioned earlier in case introspection is taking longer time.

Use the below method only to clean up the full install of introspection. Also this was tested with the current release and there could be changes in the future release from Red Hat and/or Openstack community. Exercise caution before attempting.

<span>for i in $(ironic node-list | awk ‘ /power/ { print $2 } ’ );</span>

<span>        ironic node-set-power-state $i off</span>

<span>        ironic node-delete $i</span>

<span>done</span>

<span>sleep 30</span>

<span>sudo rm /var/lib/ironic-discoverd/discoverd.sqlite  # must be deleted as root</span>

<span>ls -al /var/lib/ironic-discoverd/discoverd.sqlite</span>

<span>sudo systemctl restart openstack-ironic-discoverd</span>

<span>sudo systemctl status openstack-ironic-discoverd</span>

<span>ironic node-list</span>

<span>ironic node-port-list &lt;node uuid&gt;</span>

<span>ironic port-update &lt;node uuid&gt; replace address &lt;new mac address&gt;</span>

<span>[stack@osp7-director ~]$ ironic node-port-list b7dde876-354a-4688-8550-aec8f64c582c </span>

<span>+————————————–+——————-+</span>

<span>| UUID                                 | Address           |</span>

<span>+————————————–+——————-+</span>

<span>| 78a0dd36-cbf1-447c-8c42-4978438e40e9 | 00:25:b5:00:00:6c |</span>

<span>+————</span>
----------------–+-------------+

<span>[stack@osp7-director ~]$ ironic port-update b7dde876-354a-4688-8550-aec8f64c582c replace address &lt;new mac address&gt;</span>

<span>ironic node-update &lt;NODE UUID&gt; replace driver_info/ipmi_address=&lt;NEW IPMI ADDRESS&gt;</span>

You can also correct IPMI address from UCS if this IP is free and leave the one in openstack.

LAN tab -> IP Pools -> Check whether IP is free and is available in the block.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_279.jpg)

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_280.jpg" id="Picture 96" width="586">
</span>

At times it may not be feasible to do bulk introspection of all the nodes because of say lun issue on one single node, in particular if you have large number of nodes in the cloud.

<span>ironic node-set-provision-state &lt;uuid&gt; manage</span>

<span>openstack baremetal introspection start &lt;uuid&gt;</span>

<span>openstack baremetal introspection status &lt;uuid&gt;. Should give Finished as True and error as None.</span>

<span>ironic node-set-provision-state &lt;uuid&gt; provide</span>

Debugging Overcloud failues sometimes is a daunting task. The issues could be as simple as passing incorrect parameters to Overcloud deploy while some could be bugs as well. Here is an attempt to narrow down the problems. It is difficult to cover all the failure scenarios here. Few of them found out on the configuration are mentioned here. The best place is to debug from here and then move forward with Red Hat and Openstack documentation.

<span>·</span>

Check for the flavors pre-defined and verify that they match correctly. Incorrect flavors and/or the number of nodes passed may error with insufficient number of nodes while running Overcloud deploy command. Run instack-ironic-deployment –show-profile to confirm.

<span>·</span>

Make sure that you have ntp server configured and check with ntpdate -d -y <ntp server> to check the drift. Preferably should be less than 20 ms for ceph monitors.

<span>·</span>

Run in debug mode to capture the errors while running Overcloud deploy.

<span>·</span>

The 300 minutes provided in the Appendix for Overcloud deployment command should suffice. But in case of large deployments, it may have to be increased.

<span>·</span>

Overcloud image has been customized with root passwords. This will allow us to login to the node directly through KVM console in case of failures even if heat-admin user is still not setup.

<span>·</span>

journalctl -u os-collect-config | egrep -i "error|trace|fail" should shed some light around any errors or failures happened during Overcloud deploy.

<span>·</span>

Incorrect configuration of yaml files may result in network configuration issues. Run journalctl as above to start with. Validate the yaml files with online yaml parsers.

<span>·</span>

Run ifconfig and ovs-vsctl show the mappings.

<span>·</span>

cd /etc/os-net-config. jq . config.json will spill out the actual parameters that went on to that node.

<span>·</span>

Login from Director node to the other nodes and check for the routes. There should be one static route either externally or to the Undercloud node, depending on the way masquadering was configured

<span>·</span>

Run journalctl as above and check for dmesg and /var/log/messages to reveal any failures related with partitioning and/or network.

<span>·</span>

Per ceph.yaml included in this document and because of bug [1297251](https://bugzilla.redhat.com/show_bug.cgi?id=1297251), Ceph partitions are pre-created with wipe-disk.yaml file. Validate this with /root/wipe-disk.txt file and running cat /proc/partitions. Only the journal partitions are pre-created. The OSD partitions are created by RHEL-OSP Director.

<span>·</span>

Checking the partitions in /proc/partitions and the existence of /var/log/ceph/*, /var/lib/ceph* and /etc/ceph/keyring and other files reveal at what stage it failed.

<span>·</span>

The monitors should be setup before creating Ceph OSD's. Existence of /etc/ceph/* on controller nodes, followed by that in storage nodes will reveal whether monitor setup was successful or not.

<span>·</span>

Run ceph -s to check the health and observe for how many total OSD's, how many are up etc.

<span>·</span>

Run ceph osd tree to reveal issues with any individual OSD's.

<span>·</span>

If you detect clock skew issues on monitors, check for ntp daemon, sync up the time on monitors running on controller nodes and restart the monitors /etc/init.d/ceph mon restart

The following sequence may be followed to debug heat stack create/update issues.

1.heat stack-list

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_281.jpg)

2.heat resource-list overcloud | grep -vi complete

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_282.jpg)

3.heat resource-list -n5 overcloud | grep -vi complete

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_283.jpg)

4.heat resource-show overcloud Controller

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_284.jpg)

5.heat deployment-show <deployment id obtained above>

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_285.jpg)

6.In the current version it has been observed that both introspection and Overcloud deploy happen in batches of ten. While the first 10 are being deployed, the rest of the nodes could be in spawning or wait-call-back mode. If the status hasn't changed for a while, it may be best to login to the KVM console. Make sure to map the Overcloud name to UCS service profile name for this. In case of successful installation, /etc/neutron/plugin.ini will be populated by UCSM plugin from where you can extract the mappings between UCS Service Profile name and OpenStack host names. However in case of heat stack failures, UCSM plugin might not have completed the install, and you may have to map it manually in order to login to the correct nodes.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_286.jpg)

From the IPMI or mac address you can find out which node and login to the respective KVM console of the UCS server.

1.Check for errors with pcs status on controller nodes. If some resources are not up or running, these may have to be addressed first.

<span>pcs resource cleanup will restart all the services.</span>

<span>pcs resource restart &lt;resource name obtained from pcs status&gt; </span>

<span>nova list, nova service-list and keystone endpoint-list could be handy to debug.</span>

<span>nova hypervisor-list or show will reveal details of the hypervisors configured on the system. If any nodes are missing than expected, that may have to be addressed too.</span>

2.Confirm that the following are in sync.

<span>[root@overcloud-controller-0 ~]# nova hypervisor-list | grep -i compute | wc -l </span>

<span>[root@overcloud-controller-0 ~]# ssh -l admin 10.22.100.41 </span>

<span>Nexus 1000v Switch</span>

<span>Password: </span>

<span>Last login: Thu Feb  4 18:15:21 2016 from 10.22.100.53</span>

<span>……</span>

<span>vsm-p# show module | grep overcloud-compute | count</span>

<span>[root@overcloud-controller-0 ~]# grep -i “overcloud-compute-” /etc/neutron/plugin.ini | grep -v ucsm | wc -l </span>

<span>Following can be concluded from the above:</span>

<span>There are 6 nova hypervisors as seen from OpenStack side.</span>

<span>There are 6 Compute nodes from N1000V.</span>

<span>There are 6 Compute nodes also in /etc/neutron/plugin.ini. This ini file has an entry for each switch. Hence the number 12 ( twice of the total compute nodes)</span>

1.Supervisor modules are managed by PCS

<span>[root@overcloud-controller-0 ~]# pcs status | grep vsm </span>

<span> vsm-s  (ocf::heartbeat:VirtualDomain): Started overcloud-controller-0</span>

<span> vsm-p  (ocf::heartbeat:VirtualDomain): Started overcloud-controller-1</span>

2.Management IP address

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_287.jpg)

3.Modules in VSM

<span>vsm-p# show module | grep “Supervisor” </span>

<span>1    0      Virtual Supervisor Module         Nexus1000V          active *</span>

<span>2    0      Virtual Supervisor Module         Nexus1000V          ha-standby</span>

4.Port profiles status

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_288.jpg)

5.Redundancy status

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_289.jpg)

6.Show the VLAN ports

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_290.png)

7.VSM Trouble shooting document

[<span>http://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus1000/kvm/troubleshooting_guide/n1000v_trouble.html</span>](http://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus1000/kvm/troubleshooting_guide/n1000v_trouble.html)

1.Validate entries in /etc/neutron/plugin.ini on all the controllers.

2.Any VM's created should have VLAN entries globally in the switch and also in both the port-channels and both the switches. Any missing entry raises alarm here.

**<span>Nexus Global VLAN’s</span>**

<span>UCSO-N9K-FAB-A(config)# show vlan | grep q- </span>

<span>120  q-120                            active    Po1, Po17, Po18, Eth1/1, Eth1/2</span>

<span>215  q-215                            active    Po1, Po17, Po18, Eth1/1, Eth1/2</span>

<span>251  q-251                            active    Po1, Po17, Po18, Eth1/1, Eth1/2</span>

**Nexus Port Channel VLAN's**

<span>show running-config interface port-channel 17-18 </span>

<span>switchport mode trunk</span>

<span>switchport trunk allowed vlan 1,100,110,120,130,150,160,215,251-252</span>

<span>Some of the output above is truncated for readability purposes.</span>

Validate entries in /etc/neutron/plugin.ini on all the controllers

**UCSM Global VLAN's**

Log into UCS Manager and check for the VLANS both globally and on the hypervisor where the VM(s) is provisioned. Check the host names from cli or horizon.

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_291.jpg)

Hypervisor VLAN's

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_292.png)

Operational issues can be many but a brief overview of where to check in case of failures around VM Creation is provided below.

1.Nova commands like nova list –all-tenants, nova-manage vm list and virsh list on compute nodes could be a starting point.

2.Check /var/log/neutron and grep -i "error\|trace" server.log. Few may be informational and probably ignorable.

3.Check the following files to spot any errors

a./var/log/neutron/server.log

b./etc/neutron/plugin.ini

c./etc/neutron/neutron.conf

d./var/log/nova/*

4.You may execute the following on controller nodes.

a.ip netns

b.ip netns exec <ns> <arguments>

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_293.jpg)

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_294.jpg)

5.neutron agent-list to check the status and then debugging in the respective areas

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_295.jpg)

From the output above something isn't correct on overcloud-compute-5

6.Map nova and neutron

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_296.jpg)

7.Take the VM id and seed into neutron port-list

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_297.jpg)

8.Call neutron port-show with these to get the full details

![](http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_298.jpg)

9.Port binding failures and/or update post-commit failures. Cisco plugins will spill out the driver names to identify whether the issue is on n1kv or cisco mech nexus drivers in neutron server log files.

<span>·</span>

It is strongly recommended to have one or two blades or servers as spare. While you will have business continuity you may have degraded performance during the period.

<span>·</span>

Please plan your networks beforehand and prepare check list of items in place before working on the install. It is suggested to proof read the full document once before attempting the install.

<span>·</span>

Capacity planning is another important factor considering the organic growth. This not only includes the physical resources like data center space and servers but also the network subnet sizing etc.

<span>·</span>

Please follow the operational best practices like housekeeping, purging the log and archives, etc. In bigger installations you may have to size /var/log separately too.

The following is the list of bugs that were encountered while working on this CVD. Either these are fixed and rolled out as errata update by Red Hat or workarounds have been evolved and put in place in this document. This is just for reference purposes only.

This paper is a joint contribution from Cisco Systems Inc, Red Hat Inc and Intel Corporation. The solution is baked by combining the technologies, expertise, contributions to OpenStack community and experience from the field and will provide a rich experience to the end users both on installation and day to day operational aspects of OpenStack.

<span>
  <img src="http://www.cisco.com/c/dam/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_openstack_osp7.docx/_jcr_content/renditions/ucs_openstack_osp7_0.png" alt="*" width="13">
</span>

Template files are sensitive to whitespaces and tabs. Please copy as is into a txt file and then rename them to .yaml, followed by validating them in online yaml parser like <http://yaml-online-parser.appspot.com/>

<span>  “nodes”: [</span>

<span>      “pm_user”: “admin”,</span>

<span>      “pm_password”: “Whatever_Password”,</span>

<span>      “pm_type”: “pxe_ipmitool”,</span>

<span>      “pm_addr”: “10.22.100.23”,</span>

<span>      “mac”: [</span>

<span>        “00:25:b5:00:00:6c”</span>

<span>      “memory”: “262144”,</span>

<span>      “disk”: “270”,</span>

<span>      “arch”: “x86_64”,</span>

<span>      “cpu”: “72”</span>

<span>      “pm_user”: “admin”,</span>

<span>      “pm_password”: “Whatever_Password”,</span>

<span>      “pm_type”: “pxe_ipmitool”,</span>

<span>      “pm_addr”: “10.22.100.22”,</span>

<span>      “mac”: [</span>

<span>        “00:25:b5:00:00:2a”</span>

<span>      “memory”: “131072”,</span>

<span>      “disk”: “270”,</span>

<span>      “arch”: “x86_64”,</span>

<span>      “cpu”: “32”</span>

<span>      “pm_user”: “admin”,</span>

<span>      “pm_password”: “Whatever_Password”,</span>

<span>      “pm_type”: “pxe_ipmitool”,</span>

<span>      “pm_addr”: “10.22.100.16”,</span>

<span>      “mac”: [</span>

<span>        “00:25:b5:00:00:5b”</span>

<span>      “memory”: “131072”,</span>

<span>      “disk”: “270”,</span>

<span>      “arch”: “x86_64”,</span>

<span>      “cpu”: “48”</span>

<span>      “pm_user”: “admin”,</span>

<span>      “pm_password”: “Whatever_Password”,</span>

<span>      “pm_type”: “pxe_ipmitool”,</span>

<span>      “pm_addr”: “10.22.100.18”,</span>

<span>      “mac”: [</span>

<span>        “00:25:b5:00:00:0f”</span>

<span>      “memory”: “262144”,</span>

<span>      “disk”: “270”,</span>

<span>      “arch”: “x86_64”,</span>

<span>      “cpu”: “40”</span>

<span>      “pm_user”: “admin”,</span>

<span>      “pm_password”: “Whatever_Password”,</span>

<span>      “pm_type”: “pxe_ipmitool”,</span>

<span>      “pm_addr”: “10.22.100.12”,</span>

<span>      “mac”: [</span>

<span>        “00:25:b5:00:00:5e”</span>

<span>      “memory”: “262144”,</span>

<span>      “disk”: “270”,</span>

<span>      “arch”: “x86_64”,</span>

<span>      “cpu”: “40”</span>

<span>      “pm_user”: “admin”,</span>

<span>      “pm_password”: “Whatever_Password”,</span>

<span>      “pm_type”: “pxe_ipmitool”,</span>

<span>      “pm_addr”: “10.22.100.19”,</span>

<span>      “mac”: [</span>

<span>        “00:25:b5:00:00:2d”</span>

<span>      “memory”: “262144”,</span>

<span>      “disk”: “270”,</span>

<span>      “arch”: “x86_64”,</span>

<span>      “cpu”: “40”</span>

<span>      “pm_user”: “admin”,</span>

<span>      “pm_password”: “Whatever_Password”,</span>

<span>      “pm_type”: “pxe_ipmitool”,</span>

<span>      “pm_addr”: “10.22.100.17”,</span>

<span>      “mac”: [</span>

<span>        “00:25:b5:00:00:5c”</span>

<span>      “memory”: “131072”,</span>

<span>      “disk”: “270”,</span>

<span>      “arch”: “x86_64”,</span>

<span>      “cpu”: “48”</span>

<span>      “pm_user”: “admin”,</span>

<span>      “pm_password”: “Whatever_Password”,</span>

<span>      “pm_type”: “pxe_ipmitool”,</span>

<span>      “pm_addr”: “10.22.100.15”,</span>

<span>      “mac”: [</span>

<span>        “00:25:b5:00:00:4b”</span>

<span>      “memory”: “262144”,</span>

<span>      “disk”: “270”,</span>

<span>      “arch”: “x86_64”,</span>

<span>      “cpu”: “40”</span>

<span>      “pm_user”: “admin”,</span>

<span>      “pm_password”: “Whatever_Password”,</span>

<span>      “pm_type”: “pxe_ipmitool”,</span>

<span>      “pm_addr”: “10.22.100.14”,</span>

<span>      “mac”: [</span>

<span>        “00:25:b5:00:00:7a”</span>

<span>      “memory”: “262144”,</span>

<span>      “disk”: “270”,</span>

<span>      “arch”: “x86_64”,</span>

<span>      “cpu”: “40”</span>

<span>      “pm_user”: “admin”,</span>

<span>      “pm_password”: " Whatever_Password",</span>

<span>      “pm_type”: “pxe_ipmitool”,</span>

<span>      “pm_addr”: “10.22.100.20”,</span>

<span>      “mac”: [</span>

<span>        “00:25:b5:00:00:46”</span>

<span>      “memory”: “262144”,</span>

<span>      “disk”: “400”,</span>

<span>      “arch”: “x86_64”,</span>

<span>      “cpu”: “40”</span>

<span>      “pm_user”: “admin”,</span>

<span>      “pm_password”: “Whatever_Password”,</span>

<span>      “pm_type”: “pxe_ipmitool”,</span>

<span>      “pm_addr”: “10.22.100.11”,</span>

<span>      “mac”: [</span>

<span>        “00:25:b5:00:00:57”</span>

<span>      “memory”: “262144”,</span>

<span>      “disk”: “400”,</span>

<span>      “arch”: “x86_64”,</span>

<span>      “cpu”: “40”</span>

<span>      “pm_user”: “admin”,</span>

<span>      “pm_password”: “Whatever_Password”,</span>

<span>      “pm_type”: “pxe_ipmitool”,</span>

<span>      “pm_addr”: “10.22.100.21”,</span>

<span>      “mac”: [</span>

<span>        “00:25:b5:00:00:17”</span>

<span>      “memory”: “262144”,</span>

<span>      “disk”: “400”,</span>

<span>      “arch”: “x86_64”,</span>

<span>      “cpu”: “40”</span>

<span>resource_registry:</span>

<span>  OS::TripleO::NodeUserData:</span>

<span>    /home/stack/templates/wipe_disk.yaml</span>

<span>  OS::TripleO::Compute::Net::SoftwareConfig:</span>

<span>    /home/stack/templates/nic-configs/compute.yaml</span>

<span>  OS::TripleO::Controller::Net::SoftwareConfig:</span>

<span>    /home/stack/templates/nic-configs/controller.yaml</span>

<span>  OS::TripleO::CephStorage::Net::SoftwareConfig:</span>

<span>    /home/stack/templates/nic-configs/ceph-storage.yaml</span>

<span>parameter_defaults:</span>

<span>  InternalApiNetCidr: 10.22.100.0/24</span>

<span>  StorageNetCidr: 10.22.120.0/24</span>

<span>  StorageMgmtNetCidr: 10.22.150.0/24</span>

<span>#  TenantNetCidr: 10.22.130.0/24</span>

<span>  TenantNetCidr: 10.0.0.0/12</span>

<span>  ExternalNetCidr: 172.22.215.0/24</span>

<span>  InternalApiAllocationPools: [{’start’: ‘10.22.100.50’, ‘end’: ‘10.22.100.250’}]</span>

<span>  StorageAllocationPools: [{’start’: ‘10.22.120.50’, ‘end’: ‘10.22.120.250’}]</span>

<span>  StorageMgmtAllocationPools: [{’start’: ‘10.22.150.50’, ‘end’: ‘10.22.150.250’}]</span>

<span>  TenantAllocationPools: [{’start’: ‘10.0.0.10’, ‘end’: ‘10.15.255.250’}]</span>

<span>  ExternalAllocationPools: [{’start’: ‘172.22.215.91’, ‘end’: ‘172.22.215.99’}]</span>

<span>  ExternalNetworkVlanID: 215</span>

<span>  InternalApiNetworkVlanID: 100</span>

<span>  StorageNetworkVlanID: 120</span>

<span>  StorageMgmtNetworkVlanID: 150</span>

<span>  ControlPlaneSubnetCidr: “24”</span>

<span>  ControlPlaneDefaultRoute: 10.22.110.26</span>

<span>  EC2MetadataIp: 10.22.110.26</span>

<span>  DnsServers: [’8.8.8.8’,’8.8.4.4’]</span>

<span>#  TenantNetworkVlanID: 130</span>

<span># Set to the router gateway on the external network</span>

<span>  ExternalInterfaceDefaultRoute: “172.22.215.1”</span>

<span># Set to “br-ex” if using floating IPs on native VLAN on bridge br-ex</span>

<span>  NeutronExternalNetworkBridge: “’’” </span>

<span>parameters:</span>

<span>  CinderEnableIscsiBackend: false</span>

<span>  CinderEnableRbdBackend: true</span>

<span>  NovaEnableRbdBackend: true</span>

<span>  GlanceBackend: rbd</span>

<span>heat_template_version: 2015-04-30</span>

<span>description: &gt;</span>

<span>  Software Config to drive os-net-config with 2 bonded nics on a bridge</span>

<span>  with a VLANs attached for the controller role.</span>

<span>parameters:</span>

<span>  ExternalIpSubnet:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the external network</span>

<span>    type: string</span>

<span>  InternalApiIpSubnet:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the internal API network</span>

<span>    type: string</span>

<span>  StorageIpSubnet:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the storage network</span>

<span>    type: string</span>

<span>  StorageMgmtIpSubnet:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the storage mgmt network</span>

<span>    type: string</span>

<span>  TenantIpSubnet:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the tenant network</span>

<span>    type: string</span>

<span>  BondInterfaceOvsOptions: </span>

<span>    default: ’’</span>

<span>    description: The ovs_options string for the bond interface. Set things like</span>

<span>                 lacp=active and/or bond_mode=balance-slb using this option.</span>

<span>    type: string</span>

<span>  ExternalNetworkVlanID:</span>

<span>    default: 215</span>

<span>    description: Vlan ID for the external network traffic.</span>

<span>    type: number</span>

<span>  InternalApiNetworkVlanID:</span>

<span>    default: 100</span>

<span>    description: Vlan ID for the internal_api network traffic.</span>

<span>    type: number</span>

<span>  StorageNetworkVlanID:</span>

<span>    default: 120</span>

<span>    description: Vlan ID for the storage network traffic.</span>

<span>    type: number</span>

<span>  StorageMgmtNetworkVlanID:</span>

<span>    default: 150</span>

<span>    description: Vlan ID for the storage mgmt network traffic.</span>

<span>    type: number</span>

<span>  TenantNetworkVlanID:</span>

<span>    default: 130</span>

<span>    description: Vlan ID for the tenant network traffic.</span>

<span>    type: number</span>

<span>  ExternalInterfaceDefaultRoute:</span>

<span>    default: ’’</span>

<span>    description: default route for the external network</span>

<span>    type: string</span>

<span>  ControlPlaneIp:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the ctlplane network</span>

<span>    type: string</span>

<span>  ControlPlaneSubnetCidr:</span>

<span>    default: ‘24’</span>

<span>    description: The subnet CIDR of the control plane network.</span>

<span>    type: string</span>

<span>  ControlPlaneDefaultRoute:</span>

<span>    default: ‘10.22.110.26’</span>

<span>    description: The Control Plane Default route.</span>

<span>    type: string</span>

<span>  DnsServers:</span>

<span>    default: [’8.8.8.8’,’8.8.4.4’]</span>

<span>    description: A list of DNS servers (2 max) to add to resolv.conf.</span>

<span>    type: json</span>

<span>  EC2MetadataIp:</span>

<span>    description: The IP address of the EC2 metadata server.</span>

<span>    type:  string</span>

<span>resources:</span>

<span>  OsNetConfigImpl:</span>

<span>    type: OS::Heat::StructuredConfig</span>

<span>    properties:</span>

<span>      group: os-apply-config</span>

<span>      config:</span>

<span>        os_net_config:</span>

<span>          network_config:</span>

<span>              type: interface</span>

<span>              name: nic1</span>

<span>              use_dhcp: false</span>

<span>              dns_servers: {get_param: DnsServers}</span>

<span>              addresses:</span>

<span>                  ip_netmask:</span>

<span>                    list_join:</span>

<span>                      - ‘/’</span>

<span>                      - - {get_param: ControlPlaneIp}</span>

<span>                        - {get_param: ControlPlaneSubnetCidr}</span>

<span>              routes:</span>

<span>                  ip_netmask: 169.254.169.254/32</span>

<span>                  next_hop: {get_param: EC2MetadataIp}</span>

<span>              type: ovs_bridge</span>

<span>              name: {get_input: bridge_name}</span>

<span>              use_dhcp: false</span>

<span>              members:</span>

<span>                 type: interface</span>

<span>                 name: nic4</span>

<span>                 primary: true</span>

<span>                 type: vlan</span>

<span>                 vlan_id: {get_param: ExternalNetworkVlanID}</span>

<span>                 addresses:</span>

<span>                    ip_netmask: {get_param: ExternalIpSubnet}</span>

<span>                 routes:</span>

<span>                    ip_netmask: 0.0.0.0/0</span>

<span>                    next_hop: {get_param: ExternalInterfaceDefaultRoute}</span>

<span>              type: ovs_bridge</span>

<span>              name: br-mgmt </span>

<span>              members:</span>

<span>                  type: interface</span>

<span>                  name: nic3</span>

<span>                  primary: true</span>

<span>                  type: vlan</span>

<span>                  vlan_id: {get_param: InternalApiNetworkVlanID}</span>

<span>                  addresses:</span>

<span>                    ip_netmask: {get_param: InternalApiIpSubnet}</span>

<span>              type: ovs_bridge</span>

<span>              name: br-storage-pub</span>

<span>              mtu: 9000</span>

<span>              members:</span>

<span>                  type: interface</span>

<span>                  name: nic5</span>

<span>                  mtu: 9000</span>

<span>                  primary: true</span>

<span>                  type: vlan</span>

<span>                  mtu: 9000</span>

<span>                  vlan_id: {get_param: StorageNetworkVlanID}</span>

<span>                  addresses:</span>

<span>                    ip_netmask: {get_param: StorageIpSubnet}</span>

<span>              type: ovs_bridge</span>

<span>              name: br-storage-clus</span>

<span>              mtu: 9000</span>

<span>              members:</span>

<span>                  type: interface</span>

<span>                  name: nic6</span>

<span>                  mtu: 9000</span>

<span>                  primary: true</span>

<span>                  type: vlan</span>

<span>                  mtu: 9000</span>

<span>                  vlan_id: {get_param: StorageMgmtNetworkVlanID}</span>

<span>                  addresses:</span>

<span>                    ip_netmask: {get_param: StorageMgmtIpSubnet}</span>

<span>              type: ovs_bridge</span>

<span>              name: br-floating</span>

<span>              members:</span>

<span>                  type: interface</span>

<span>                  name: nic7</span>

<span>                  primary: true</span>

<span>outputs:</span>

<span>  OS::stack_id:</span>

<span>    description: The OsNetConfigImpl resource.</span>

<span>    value: {get_resource: OsNetConfigImpl}</span>

<span>heat_template_version: 2015-04-30</span>

<span>description: &gt;</span>

<span>  Software Config to drive os-net-config with 2 bonded nics on a bridge</span>

<span>  with a VLANs attached for the compute role.</span>

<span>parameters:</span>

<span>  ExternalIpSubnet:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the external network</span>

<span>    type: string</span>

<span>  InternalApiIpSubnet:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the internal API network</span>

<span>    type: string</span>

<span>  StorageIpSubnet:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the storage network</span>

<span>    type: string</span>

<span>  StorageMgmtIpSubnet:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the storage mgmt network</span>

<span>    type: string</span>

<span>  TenantIpSubnet:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the tenant network</span>

<span>    type: string</span>

<span>  BondInterfaceOvsOptions: </span>

<span>    default: ’’</span>

<span>    description: The ovs_options string for the bond interface. Set things like</span>

<span>    type: string</span>

<span>  InternalApiNetworkVlanID:</span>

<span>    default: 100</span>

<span>    description: Vlan ID for the internal_api network traffic.</span>

<span>    type: number</span>

<span>  StorageNetworkVlanID:</span>

<span>    default: 120</span>

<span>    description: Vlan ID for the storage network traffic.</span>

<span>    type: number</span>

<span>  TenantNetworkVlanID:</span>

<span>    default: 130</span>

<span>    description: Vlan ID for the tenant network traffic.</span>

<span>    type: number</span>

<span>  ControlPlaneIp:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the ctlplane network</span>

<span>    type: string</span>

<span>  ControlPlaneSubnetCidr:</span>

<span>    default: ‘24’</span>

<span>    description: The subnet CIDR of the control plane network.</span>

<span>    type: string</span>

<span>  ControlPlaneDefaultRoute:</span>

<span>    default: ‘10.22.110.26’</span>

<span>    description: The Control Plane Default route.</span>

<span>    type: string</span>

<span>  DnsServers:</span>

<span>    default: [’8.8.8.8’,’8.8.4.4’]</span>

<span>    description: A list of DNS servers (2 max) to add to resolv.conf.</span>

<span>    type: json</span>

<span>  EC2MetadataIp:</span>

<span>    description: The IP address of the EC2 metadata server.</span>

<span>    type:  string</span>

<span>resources:</span>

<span>  OsNetConfigImpl:</span>

<span>    type: OS::Heat::StructuredConfig</span>

<span>    properties:</span>

<span>      group: os-apply-config</span>

<span>      config:</span>

<span>        os_net_config:</span>

<span>          network_config:</span>

<span>              type: interface</span>

<span>              name: nic1</span>

<span>              use_dhcp: false</span>

<span>              dns_servers: {get_param: DnsServers}</span>

<span>              addresses:</span>

<span>                  ip_netmask:</span>

<span>                    list_join:</span>

<span>                      - ‘/’</span>

<span>                      - - {get_param: ControlPlaneIp}</span>

<span>                        - {get_param: ControlPlaneSubnetCidr}</span>

<span>              routes:</span>

<span>                  ip_netmask: 169.254.169.254/32</span>

<span>                  next_hop: {get_param: EC2MetadataIp}</span>

<span>                  default: true</span>

<span>                  next_hop: {get_param: ControlPlaneDefaultRoute}</span>

<span>              type: ovs_bridge</span>

<span>              name: br-storage-pub</span>

<span>              mtu: 9000</span>

<span>              members:</span>

<span>                  type: interface</span>

<span>                  name: nic4</span>

<span>                  mtu: 9000</span>

<span>                  primary: true</span>

<span>                  type: vlan</span>

<span>                  mtu: 9000</span>

<span>                  vlan_id: {get_param: StorageNetworkVlanID}</span>

<span>                  addresses:</span>

<span>                    ip_netmask: {get_param: StorageIpSubnet}</span>

<span>              type: ovs_bridge</span>

<span>              name: br-mgmt</span>

<span>              members:</span>

<span>                 type: interface</span>

<span>                 name: nic3</span>

<span>                 primary: true</span>

<span>                 type: vlan</span>

<span>                 vlan_id: {get_param: InternalApiNetworkVlanID}</span>

<span>                 addresses:</span>

<span>                    ip_netmask: {get_param: InternalApiIpSubnet}</span>

<span>outputs:</span>

<span>  OS::stack_id:</span>

<span>    description: The OsNetConfigImpl resource.</span>

<span>    value: {get_resource: OsNetConfigImpl}</span>

<span>heat_template_version: 2015-04-30</span>

<span>description: &gt;</span>

<span>  Software Config to drive os-net-config with 2 bonded nics on a bridge</span>

<span>  with a VLANs attached for the ceph storage role.</span>

<span>parameters:</span>

<span>  ExternalIpSubnet:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the external network</span>

<span>    type: string</span>

<span>  InternalApiIpSubnet:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the internal API network</span>

<span>    type: string</span>

<span>  StorageIpSubnet:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the storage network</span>

<span>    type: string</span>

<span>  StorageMgmtIpSubnet:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the storage mgmt network</span>

<span>    type: string</span>

<span>  TenantIpSubnet:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the tenant network</span>

<span>    type: string</span>

<span>  BondInterfaceOvsOptions:</span>

<span>    default: ’’</span>

<span>    description: The ovs_options string for the bond interface. Set things like</span>

<span>                 lacp=active and/or bond_mode=balance-slb using this option.</span>

<span>    type: string</span>

<span>  InternalApiNetworkVlanID:</span>

<span>    default: ’’</span>

<span>    description: Vlan ID for the internal_api network traffic.</span>

<span>    type: number</span>

<span>  StorageNetworkVlanID:</span>

<span>    default: ’’</span>

<span>    description: Vlan ID for the storage network traffic.</span>

<span>    type: number</span>

<span>  StorageMgmtNetworkVlanID:</span>

<span>    default: ’’</span>

<span>    description: Vlan ID for the storage mgmt network traffic.</span>

<span>    type: number</span>

<span>  ControlPlaneIp:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the ctlplane network</span>

<span>    type: string</span>

<span>  ControlPlaneSubnetCidr:</span>

<span>    default: ‘24’</span>

<span>    description: The subnet CIDR of the control plane network.</span>

<span>    type: string</span>

<span>  ControlPlaneDefaultRoute:</span>

<span>    default: ‘10.22.110.26’</span>

<span>    description: The Control Plane Default route.</span>

<span>    type: string</span>

<span>  DnsServers:</span>

<span>    default: [’8.8.8.8’,’8.8.4.4’]</span>

<span>    description: A list of DNS servers (2 max) to add to resolv.conf.</span>

<span>    type: json</span>

<span>  EC2MetadataIp:</span>

<span>    description: The IP address of the EC2 metadata server.</span>

<span>    type:  string</span>

<span>resources:</span>

<span>  OsNetConfigImpl:</span>

<span>    type: OS::Heat::StructuredConfig</span>

<span>    properties:</span>

<span>      group: os-apply-config</span>

<span>      config:</span>

<span>        os_net_config:</span>

<span>          network_config:</span>

<span>              type: interface</span>

<span>              name: nic1</span>

<span>              use_dhcp: false</span>

<span>              dns_servers: {get_param: DnsServers}</span>

<span>              addresses:</span>

<span>                  ip_netmask:</span>

<span>                    list_join:</span>

<span>                      - ‘/’</span>

<span>                      - - {get_param: ControlPlaneIp}</span>

<span>                        - {get_param: ControlPlaneSubnetCidr}</span>

<span>              routes:</span>

<span>                  ip_netmask: 169.254.169.254/32</span>

<span>                  next_hop: {get_param: EC2MetadataIp}</span>

<span>                  default: true</span>

<span>                  next_hop: {get_param: ControlPlaneDefaultRoute}</span>

<span>              type: ovs_bridge</span>

<span>              name: br-storage-pub</span>

<span>              mtu: 9000</span>

<span>              members:</span>

<span>                  type: interface</span>

<span>                  name: nic2</span>

<span>                  mtu: 9000</span>

<span>                  primary: true</span>

<span>                  type: vlan</span>

<span>                  mtu: 9000</span>

<span>                  vlan_id: {get_param: StorageNetworkVlanID}</span>

<span>                  addresses:</span>

<span>                    ip_netmask: {get_param: StorageIpSubnet}</span>

<span>              type: ovs_bridge</span>

<span>              name: br-storage-clus</span>

<span>              mtu: 9000</span>

<span>              members:</span>

<span>                  type: interface</span>

<span>                  name: nic3</span>

<span>                  mtu: 9000</span>

<span>                  primary: true</span>

<span>                  type: vlan</span>

<span>                  mtu: 9000</span>

<span>                  vlan_id: {get_param: StorageMgmtNetworkVlanID}</span>

<span>                  addresses:</span>

<span>                    ip_netmask: {get_param: StorageMgmtIpSubnet}</span>

<span>outputs:</span>

<span>  OS::stack_id:</span>

<span>    description: The OsNetConfigImpl resource.</span>

<span>    value: {get_resource: OsNetConfigImpl}</span>

<span>ceph::profile::params::osd_journal_size: 20000</span>

<span>ceph::profile::params::osd_pool_default_pg_num: 128</span>

<span>ceph::profile::params::osd_pool_default_pgp_num: 128</span>

<span>ceph::profile::params::osd_pool_default_size: 3</span>

<span>ceph::profile::params::osd_pool_default_min_size: 1</span>

<span>ceph::profile::params::manage_repo: false</span>

<span>ceph::profile::params::authentication_type: cephx</span>

<span>ceph::profile::params::osds:</span>

<span>    ‘/dev/sdd’:</span>

<span>        journal: ‘/dev/sdb1’</span>

<span>    ‘/dev/sde’:</span>

<span>        journal: ‘/dev/sdb2’</span>

<span>    ‘/dev/sdf’:</span>

<span>        journal: ‘/dev/sdb3’</span>

<span>    ‘/dev/sdg’:</span>

<span>        journal: ‘/dev/sdb4’</span>

<span>    ‘/dev/sdh’:</span>

<span>        journal: ‘/dev/sdc1’</span>

<span>    ‘/dev/sdi’:</span>

<span>        journal: ‘/dev/sdc2’</span>

<span>    ‘/dev/sdj’:</span>

<span>        journal: ‘/dev/sdc3’</span>

<span>    ‘/dev/sdk’:</span>

<span>        journal: ‘/dev/sdc4’</span>

<span>ceph_pools:</span>

<span>  - “%{hiera(’cinder_rbd_pool_name’)}”</span>

<span>  - “%{hiera(’nova::compute::rbd::libvirt_images_rbd_pool’)}”</span>

<span>  - “%{hiera(’glance::backend::rbd::rbd_store_pool’)}”</span>

<span>ceph_osd_selinux_permissive: true</span>

<span>ceph::profile::params::osd_journal_size: 20000</span>

<span>ceph::profile::params::osd_pool_default_pg_num: 128</span>

<span>ceph::profile::params::osd_pool_default_pgp_num: 128</span>

<span>ceph::profile::params::osd_pool_default_size: 3</span>

<span>ceph::profile::params::osd_pool_default_min_size: 1</span>

<span>ceph::profile::params::manage_repo: false</span>

<span>ceph::profile::params::authentication_type: cephx</span>

<span>ceph::profile::params::osds:</span>

<span>    ‘/dev/sdf’:</span>

<span>        journal: ‘/dev/sdb1’</span>

<span>    ‘/dev/sdg’:</span>

<span>        journal: ‘/dev/sdb2’</span>

<span>    ‘/dev/sdh’:</span>

<span>        journal: ‘/dev/sdb3’</span>

<span>    ‘/dev/sdi’:</span>

<span>        journal: ‘/dev/sdb4’</span>

<span>    ‘/dev/sdj’:</span>

<span>        journal: ‘/dev/sdb5’</span>

<span>    ‘/dev/sdk’:</span>

<span>        journal: ‘/dev/sdc1’</span>

<span>    ‘/dev/sdl’:</span>

<span>        journal: ‘/dev/sdc2’</span>

<span>    ‘/dev/sdm’:</span>

<span>        journal: ‘/dev/sdc3’</span>

<span>    ‘/dev/sdn’:</span>

<span>        journal: ‘/dev/sdc4’</span>

<span>    ‘/dev/sdo’:</span>

<span>        journal: ‘/dev/sdc5’</span>

<span>    ‘/dev/sdp’:</span>

<span>        journal: ‘/dev/sdd1’</span>

<span>    ‘/dev/sdq’:</span>

<span>        journal: ‘/dev/sdd2’</span>

<span>    ‘/dev/sdr’:</span>

<span>        journal: ‘/dev/sdd3’</span>

<span>    ’/dev/sds’:</span>

<span>        journal: ‘/dev/sdd4’</span>

<span>    ‘/dev/sdt’:</span>

<span>        journal: ‘/dev/sde1’</span>

<span>    ‘/dev/sdu’:</span>

<span>        journal: ‘/dev/sde2’</span>

<span>    ‘/dev/sdv’:</span>

<span>        journal: ‘/dev/sde3’</span>

<span>    ‘/dev/sdw’:</span>

<span>        journal: ‘/dev/sde4’</span>

<span>ceph_pools:</span>

<span>  - “%{hiera(’cinder_rbd_pool_name’)}”</span>

<span>  - “%{hiera(’nova::compute::rbd::libvirt_images_rbd_pool’)}”</span>

<span>  - “%{hiera(’glance::backend::rbd::rbd_store_pool’)}”</span>

<span>ceph_osd_selinux_permissive: true</span>

<span>resource_registry:</span>

<span>  OS::TripleO::ControllerExtraConfigPre: /usr/share/openstack-tripleo-heat-templates/puppet/extraconfig/pre_deploy/controller/cisco-n1kv.yaml</span>

<span>  OS::TripleO::ComputeExtraConfigPre: /usr/share/openstack-tripleo-heat-templates/puppet/extraconfig/pre_deploy/controller/cisco-n1kv.yaml</span>

<span>  OS::TripleO::Controller: /usr/share/openstack-tripleo-heat-templates/puppet/controller-puppet.yaml</span>

<span>  OS::TripleO::AllNodesExtraConfig: /usr/share/openstack-tripleo-heat-templates/puppet/extraconfig/all_nodes/neutron-ml2-cisco-nexus-ucsm.yaml</span>

<span>  # network type depends on your setup, but if your testbed has a single nic, then you need to use the bridge config</span>

<span># OS::TripleO::Compute::Net::SoftwareConfig: /usr/share/openstack-tripleo-heat-templates/net-config-bridge.yaml</span>

<span>parameter_defaults:</span>

<span>  N1000vVSMIP: ‘10.22.100.41’</span>

<span>  N1000vMgmtGatewayIP: ‘10.22.100.1’</span>

<span>  N1000vMgmtNetmask: ‘255.255.255.0’</span>

<span>  N1000vVSMDomainID: ‘500’</span>

<span>  N1000vVSMPassword: ‘Password’</span>

<span>  N1000vPacemakerControl: true</span>

<span>  N1000vVSMHostMgmtIntf: ‘br-mgmt’</span>

<span>  N1000vExistingBridge: true</span>

<span>  N1000vVSMVersion: ’’</span>

<span>  N1000vVEMHostMgmtIntf: ‘vlan100’</span>

<span>  N1000vVSMHostMgmtIntfVlan: 100</span>

<span>  N1000vUplinkProfile: ‘{eth1: system-uplink,}’</span>

<span>  NetworkUCSMIp: ‘10.22.100.5’</span>

<span>  NetworkUCSMUsername: ‘admin’</span>

<span>  NetworkUCSMPassword: ‘nbv12345’</span>

<span>  NetworkUCSMHostList: ‘00:25:b5:00:00:0f:Openstack_Compute_Node1,00:25:b5:00:00:5e:Openstack_Compute_Node2,00:25:b5:00:00:2d:Openstack_Compute_Node3,00:25:b5:00:00:5c:Openstack_Compute_Node4,00:25:b5:00:00:4b:Openstack_Compute_Node5,00:25:b5:00:00:7a:Openstack_Compute_Node6,00:25:b5:00:00:6c:Openstack_Controller_Node1,00:25:b5:00:00:2a:Openstack_Controller_Node2,00:25:b5:00:00:5b:Openstack_Controller_Node3’</span>

<span>  NetworkNexusConfig: {</span>

<span>    “UCSO-N9K-FAB-A”: {</span>

<span>        “ip_address”: “10.22.100.3”, </span>

<span>        “nve_src_intf”: 0, </span>

<span>        “password”: “Nbv!2345”, </span>

<span>        “physnet”: “”, </span>

<span>        “servers”: {</span>

<span>            “00:25:b5:00:00:6c”: {</span>

<span>                “ports”: “port-channel:17,port-channel:18”</span>

<span>            “00:25:b5:00:00:2a”: {</span>

<span>                “ports”: “port-channel:17,port-channel:18”</span>

<span>            “00:25:b5:00:00:5b”: {</span>

<span>                “ports”: “port-channel:17,port-channel:18”</span>

<span>            “00:25:b5:00:00:0f”: {</span>

<span>                “ports”: “port-channel:17,port-channel:18”</span>

<span>            “00:25:b5:00:00:5e”: {</span>

<span>                “ports”: “port-channel:17,port-channel:18”</span>

<span>            “00:25:b5:00:00:2d”: {</span>

<span>                “ports”: “port-channel:17,port-channel:18”</span>

<span>            “00:25:b5:00:00:5c”: {</span>

<span>                “ports”: “port-channel:17,port-channel:18”</span>

<span>            “00:25:b5:00:00:4b”: {</span>

<span>                “ports”: “port-channel:17,port-channel:18”</span>

<span>            “00:25:b5:00:00:7a”: {</span>

<span>                “ports”: “port-channel:17,port-channel:18”</span>

<span>        “ssh_port”: 22, </span>

<span>        “username”: “admin”</span>

<span>    “UCSO-N9K-FAB-B”: {</span>

<span>        “ip_address”: “10.22.100.4”, </span>

<span>        “nve_src_intf”: 0, </span>

<span>        “password”: “Nbv!2345”, </span>

<span>        “physnet”: “”, </span>

<span>        “servers”: {</span>

<span>            “00:25:b5:00:00:6c”: {</span>

<span>                “ports”: “port-channel:17,port-channel:18”</span>

<span>            “00:25:b5:00:00:2a”: {</span>

<span>                “ports”: “port-channel:17,port-channel:18”</span>

<span>            “00:25:b5:00:00:5b”: {</span>

<span>                “ports”: “port-channel:17,port-channel:18”</span>

<span>            “00:25:b5:00:00:0f”: {</span>

<span>                “ports”: “port-channel:17,port-channel:18”</span>

<span>            “00:25:b5:00:00:5e”: {</span>

<span>                “ports”: “port-channel:17,port-channel:18”</span>

<span>            “00:25:b5:00:00:2d”: {</span>

<span>                “ports”: “port-channel:17,port-channel:18”</span>

<span>            “00:25:b5:00:00:5c”: {</span>

<span>                “ports”: “port-channel:17,port-channel:18”</span>

<span>            “00:25:b5:00:00:4b”: {</span>

<span>                “ports”: “port-channel:17,port-channel:18”</span>

<span>            “00:25:b5:00:00:7a”: {</span>

<span>                “ports”: “port-channel:17,port-channel:18”</span>

<span>        “ssh_port”: 22, </span>

<span>        “username”: “admin”</span>

<span>  NetworkNexusManagedPhysicalNetwork: physnet-tenant</span>

<span>  NetworkNexusVlanNamePrefix: ‘q-’</span>

<span>  NetworkNexusSviRoundRobin: ‘false’</span>

<span>  NetworkNexusProviderVlanNamePrefix: ‘p-’</span>

<span>  NetworkNexusPersistentSwitchConfig: ‘false’</span>

<span>  NetworkNexusSwitchHeartbeatTime: 30</span>

<span>  NetworkNexusSwitchReplayCount: 1000</span>

<span>  NetworkNexusProviderVlanAutoCreate: ‘true’</span>

<span>  NetworkNexusProviderVlanAutoTrunk: ‘true’</span>

<span>  NetworkNexusVxlanGlobalConfig: ‘false’</span>

<span>  NetworkNexusHostKeyChecks: ‘false’</span>

<span>  EnablePackageInstall: false</span>

<span>  NeutronMechanismDrivers: ‘cisco_n1kv,cisco_nexus,cisco_ucsm’</span>

<span>  NeutronServicePlugins: ‘router,networking_cisco.plugins.ml2.drivers.cisco.n1kv.policy_profile_service.PolicyProfilePlugin’</span>

<span>  NeutronTypeDrivers: ‘vlan’</span>

<span>  NeutronCorePlugin: ‘neutron.plugins.ml2.plugin.Ml2Plugin’</span>

<span>  NeutronDhcpAgentsPerNetwork: 1</span>

<span>  NeutronAllowL3AgentFailover: ‘true’</span>

<span>  NeutronL3HA: ‘false’</span>

<span>  NeutronNetworkVLANRanges: ‘physnet-tenant:250:749’</span>

<span>  NetworkNexusVxlanVniRanges: ‘0:0’</span>

<span>  NetworkNexusVxlanMcastRanges: ‘0.0.0.0:0.0.0.0’</span>

<span>parameters:</span>

<span>  controllerExtraConfig:</span>

<span>    neutron::server::api_workers: 0</span>

<span>    neutron::agents::metadata::metadata_workers: 0</span>

<span>    neutron::server::rpc_workers: 0</span>

<span>heat_template_version: 2015-04-30</span>

<span>#This configuration is only for C240M4L server. Change for C240M4S</span>

<span>#The first for loop zapdisks all the storage disks</span>

<span>#The second for loop creates aligned partitions, only for the journals. Change the values of sdb and sdc accordingly</span>

<span>#In case of more disks for C240M4S change the device names accordingly</span>

<span>#Do not create partitions for data disks</span>

<span>resources:</span>

<span>  userdata:</span>

<span>    type: OS::Heat::MultipartMime</span>

<span>    properties:</span>

<span>      parts:</span>

<span>      - config: {get_resource: clean_disk}</span>

<span>  clean_disk:</span>

<span>    type: OS::Heat::SoftwareConfig</span>

<span>    properties:</span>

<span>      config: |</span>

<span>        #!/bin/bash</span>

<span>        DATA_DISKS=“sdd sde sdf sdg sdh sdi sdj sdk”</span>

<span>        JOURNAL_DISKS=“sdb sdc”</span>

<span>        JOURNAL_SIZE=20G</span>

<span>        { for disk in $DATA_DISKS $JOURNAL_DISKS</span>

<span>           sgdisk -Z /dev/$disk</span>

<span>           sgdisk -g /dev/$disk</span>

<span>        done } &gt; /root/wipe_disk.txt</span>

<span>        { for disk in $JOURNAL_DISKS</span>

<span>          export ptype1=45b0969e-9b03-4f30-b4c6-b4b80ceff106</span>

<span>          for i in $(seq 1 $(( ($(echo $DATA_DISKS|wc -w)+$(echo $JOURNAL_DISKS|wc -w)-1) / $(echo $JOURNAL_DISKS|wc -w) )) )</span>

<span>          sgdisk –new=$i::+$JOURNAL_SIZE –change-name=“$i:ceph journal” –typecode=“$i:$ptype1” /dev/$disk</span>

<span>          done</span>

<span>        done } &gt;&gt; /root/wipe_disk.txt</span>

<span>outputs:</span>

<span>  OS::stack_id:</span>

<span>    value: {get_resource: userdata}</span>

<span>heat_template_version: 2015-04-30</span>

<span>#This configuraion is only for C240M4L server. Change for C240M4S</span>

<span>#The first for loop zapdisks all the storage disks</span>

<span>#The second for loop creates aligned partitions, only for the journals. Change the values of sdb and sdc accordingly</span>

<span>#In case of more journal disks for C240M4S add the device names for the journals in the second for loop.</span>

<span>#Do not create partitions for data disks</span>

<span>resources:</span>

<span>  userdata:</span>

<span>    type: OS::Heat::MultipartMime</span>

<span>    properties:</span>

<span>      parts:</span>

<span>      - config: {get_resource: clean_disk}</span>

<span>  clean_disk:</span>

<span>    type: OS::Heat::SoftwareConfig</span>

<span>    properties:</span>

<span>      config: |</span>

<span>        #!/bin/bash</span>

<span>        DATA_DISKS=“sdf sdg sdh sdi sdj sdk sdl sdm sdn sdo sdp sdq sdr sds sdt sdu sdv sdw”</span>

<span>        JOURNAL_DISKS=“sdb sdc sdd sde”</span>

<span>        JOURNAL_SIZE=20G</span>

<span>        { for disk in $DATA_DISKS $JOURNAL_DISKS</span>

<span>           sgdisk -Z /dev/$disk</span>

<span>           sgdisk -g /dev/$disk</span>

<span>        done } &gt; /root/wipe_disk.txt</span>

<span>        { for disk in $JOURNAL_DISKS</span>

<span>          export ptype1=45b0969e-9b03-4f30-b4c6-b4b80ceff106</span>

<span>          for i in $(seq 1 $(( ($(echo $DATA_DISKS|wc -w)+$(echo $JOURNAL_DISKS|wc -w)-1) / $(echo $JOURNAL_DISKS|wc -w) )) )</span>

<span>          sgdisk –new=$i::+$JOURNAL_SIZE –change-name=“$i:ceph journal” –typecode=“$i:$ptype1” /dev/$disk</span>

<span>          done</span>

<span>        done } &gt;&gt; /root/wipe_disk.txt</span>

<span>outputs:</span>

<span>  OS::stack_id:</span>

<span>    value: {get_resource: userdata}</span>

<span>heat_template_version: 2014-10-16</span>

<span>description: &gt;</span>

<span>  Extra hostname configuration</span>

<span>parameters:</span>

<span>  servers:</span>

<span>    type: json</span>

<span>  nameserver_ip:</span>

<span>    type: string</span>

<span>resources:</span>

<span>  ExtraConfig:</span>

<span>    type: OS::Heat::SoftwareConfig</span>

<span>    properties:</span>

<span>      group: script</span>

<span>      config:</span>

<span>        str_replace:</span>

<span>          template: |</span>

<span>            #!/bin/sh</span>

<span>            nameserver1_ip=“8.8.8.8”</span>

<span>            ntpserver1_ip=“171.68.38.66”</span>

<span>            { for i in 1</span>

<span>            x=`cat /etc/resolv.conf | grep -v ‘^#’ | grep -v ‘^$’ | grep nameserver`;</span>

<span>            echo “Existing Value is $x”</span>

<span>            echo $nameserver1_ip</span>

<span>            if [[ “$x” != “*nameserver*” ]]; then echo “nameserver $nameserver1_ip” &gt;&gt; /etc/resolv.conf; fi</span>

<span>            done } &gt; /root/nameserver.txt</span>

<span>            { for i in 1</span>

<span>            y=`cat /etc/ntp.conf | egrep -v ‘^#|^$’ | grep “$ntpserver1_ip”`</span>

<span>            echo “Existing Value is $y”</span>

<span>            if [[ “$y” != “*$ntpserver_ip*” ]]; then echo “server $ntpserver1_ip” &gt;&gt; /etc/ntp.conf; service ntpd restart;fi</span>

<span>            done } &gt; /root/ntp.txt</span>

<span>          params:</span>

<span>            _NAMESERVER_IP_: {get_param: nameserver_ip}</span>

<span>  ExtraDeployments:</span>

<span>    type: OS::Heat::SoftwareDeployments</span>

<span>    properties:</span>

<span>      servers:  {get_param: servers}</span>

<span>      config: {get_resource: ExtraConfig}</span>

<span>      actions: [’CREATE’,’UPDATE’]</span>

<span>heat_template_version: 2014-10-16</span>

<span>description: &gt;</span>

<span>  Extra hostname configuration</span>

<span>parameters:</span>

<span>  servers:</span>

<span>    type: json</span>

<span>  nameserver_ip:</span>

<span>    type: string</span>

<span>resources:</span>

<span>  ExtraConfig:</span>

<span>    type: OS::Heat::SoftwareConfig</span>

<span>    properties:</span>

<span>      group: script</span>

<span>      config:</span>

<span>        str_replace:</span>

<span>          template: |</span>

<span>            #!/bin/sh</span>

<span>            { for i in 1</span>

<span>            x=`cat /etc/resolv.conf | grep -v ‘^#’ | grep -v ‘^$’ | grep nameserver`;</span>

<span>            echo $x</span>

<span>            if [[ “$x” != *nameserver* ]]; then echo “nameserver 8.8.8.8” &gt;&gt; /etc/resolv.conf; fi</span>

<span>            done } &gt; /root/nameserver.txt</span>

<span>            { for i in 1</span>

<span>            y=`cat /etc/ntp.conf | egrep -v ‘^#|^$’ | grep “171.68.38.66”`</span>

<span>            echo $y</span>

<span>            if [[ “$y” != *171.68.38.66* ]]; then echo “server 171.68.38.66” &gt;&gt; /etc/ntp.conf; service ntpd restart;fi</span>

<span>            done } &gt; /root/ntp.txt</span>

<span>          params:</span>

<span>            _NAMESERVER_IP_: {get_param: nameserver_ip}</span>

<span>  ExtraDeployments:</span>

<span>    type: OS::Heat::SoftwareDeployments</span>

<span>    properties:</span>

<span>      servers:  {get_param: servers}</span>

<span>      config: {get_resource: ExtraConfig}</span>

<span>      actions: [’CREATE’,’UPDATE’]</span>

<span>#!/bin/bash</span>

<span>openstack overcloud deploy –templates \</span>

<span>
</span>
-e /usr/share/openstack-tripleo-heat-templates/overcloud-resource-registry-puppet.yaml \

<span>-e /usr/share/openstack-tripleo-heat-templates/environments/network-isolation.yaml \</span>

<span>-e /home/stack/templates/network-environment.yaml \</span>

<span>-e /home/stack/templates/storage-environment.yaml \</span>

<span>-e /home/stack/templates/cisco-plugins.yaml \</span>

<span>-e /home/stack/templates/post_config.yaml \</span>

<span>–control-flavor control –compute-flavor compute –ceph-storage-flavor CephStorage \</span>

<span>–compute-scale 6 –control-scale 3  –ceph-storage-scale 3 \</span>

<span>–libvirt-type kvm \</span>

<span>–ntp-server 171.68.38.66 \</span>

<span>–neutron-network-type vlan \</span>

<span>–neutron-tunnel-type vlan \</span>

<span>–neutron-bridge-mappings datacentre:br-ex,physnet-tenant:br-tenant,floating:br-floating \</span>

<span>–neutron-network-vlan-ranges physnet-tenant:250:749,floating:160:160 \</span>

<span>–neutron-disable-tunneling –timeout 300 \</span>

<span>–rhel-reg –reg-method portal –reg-org &lt;Your Org Code&gt; –reg-activation-key &lt;Your Activation Key&gt; \</span>

<span>–verbose –debug –log-file overcloud_new.log</span>

<span>neutron net-create ext-net –router:external true –provider:physical_network floating –provider:network_type vlan –provider:segmentation_id 160</span>

<span>neutron subnet-create –name ext-subnet –enable_dhcp=False \</span>

<span>–allocation-pool start=10.22.160.20,end=10.22.175.200 –gateway 10.22.160.1 ext-net 10.22.160.0/20</span>

The following script creates 1 Tenant, with 2 Networks and 4VM's for the tenant. This can be looped to created multiple tenants. Please proofread before running the script. There are few hardcodings done.

<span>#!/bin/bash</span>

<span>export NW1=$1</span>

<span>export NW2=$(($1+50))</span>

<span>export id=$2</span>

<span>inst1=tenant${id}_${NW1}_inst1</span>

<span>inst2=tenant${id}_${NW1}_inst2</span>

<span>inst3=tenant${id}_${NW2}_inst3</span>

<span>inst4=tenant${id}_${NW2}_inst4</span>

<span>export TENANT_CIDR1=10.2.${NW1}.0/24</span>

<span>export TENANT_CIDR2=10.2.${NW2}.0/24</span>

<span>source /home/stack/overcloudrc</span>

<span>KEYSTONE_URL=$OS_AUTH_URL</span>

<span>#rm -f keystone*</span>

<span>#rm -f tenant*</span>

<span>if [[ ! -f keystonerc_tenant${id} ]]</span>

<span>then</span>

<span># create tenantdemo environment</span>

<span>openstack user create –password tenant${id} tenant${id}</span>

<span>openstack project create tenant${id}</span>

<span>openstack role add –user tenant${id} –project tenant${id} _member_</span>

<span>cat &gt; keystonerc_tenant${id} &lt;&lt; EOF</span>

<span>export OS_USERNAME=tenant${id}</span>

<span>export OS_TENANT_NAME=tenant${id}</span>

<span>export OS_PASSWORD=tenant${id}</span>

<span>export OS_CLOUDNAME=overcloud</span>

<span>export OS_AUTH_URL=${KEYSTONE_URL}</span>

<span>EOF</span>

<span>source keystonerc_tenant${id}</span>

<span>env | grep OS_</span>

<span># create network</span>

<span>neutron net-list</span>

<span>neutron net-create tenant${id}-${NW1}</span>

<span>neutron net-create tenant${id}-${NW2}</span>

<span>neutron subnet-create –name tenant${id}-${NW1}-subnet tenant${id}-${NW1} ${TENANT_CIDR1}</span>

<span>neutron subnet-create –name tenant${id}-${NW2}-subnet tenant${id}-${NW2} ${TENANT_CIDR2}</span>

<span>neutron router-create tenant${id}</span>

<span>subID1=$(neutron subnet-list | awk “/tenant${id}-${NW1}-subnet/ {print \$2}”)</span>

<span>neutron router-interface-add tenant${id} $subID1</span>

<span>subID2=$(neutron subnet-list | awk “/tenant${id}-${NW2}-subnet/ {print \$2}”)</span>

<span>neutron router-interface-add tenant${id} $subID2</span>

<span>for i in $(neutron security-group-list | awk ‘ /default/ { print $2 } ’)</span>

<span>  neutron security-group-rule-create –direction ingress –protocol icmp  $i</span>

<span>  neutron security-group-rule-create –direction ingress –protocol tcp –port_range_min 22 –port_range_max 22 $i</span>

<span>  neutron security-group-show $i</span>

<span>  openstack security group show $i</span>

<span>done</span>

<span>openstack keypair create tenant${id}kp &gt; tenant${id}kp.pem</span>

<span>chmod 600 tenant${id}kp.pem</span>

<span>netname1=`neutron net-list | grep tenant${id}-${NW1} | awk ‘{print $2}’`</span>

<span>openstack server create –flavor m1.demo –image rhel7 \</span>

<span>–key-name tenant${id}kp –nic net-id=${netname1} ${inst1}</span>

<span>openstack server create –flavor m1.demo –image rhel7 \</span>

<span>–key-name tenant${id}kp –nic net-id=${netname1} ${inst2}</span>

<span>netname2=`neutron net-list | grep tenant${id}-${NW2} | awk ‘{print $2}’`</span>

<span>openstack server create –flavor m1.demo –image rhel7 \</span>

<span>–key-name tenant${id}kp –nic net-id=${netname2} ${inst3}</span>

<span>openstack server create –flavor m1.demo –image rhel7 \</span>

<span>–key-name tenant${id}kp –nic net-id=${netname2} ${inst4}</span>

<span>while [[ $(openstack server  list | grep BUILD) ]]</span>

<span>  sleep 3</span>

<span>done</span>

<span>openstack server  list</span>

<span>source /home/stack/overcloudrc</span>

<span>netid=$(neutron net-list | awk “/ext-net/ { print \$2 }”)</span>

<span>neutron router-gateway-set tenant${id} ${netid}</span>

<span>source keystonerc_tenant${id}</span>

<span>openstack ip floating create ext-net</span>

<span>sleep 3</span>

<span>float_ip=$(openstack ip floating list | grep ext-net | grep None | awk ‘{print $6}’ | sort -u | head -1)</span>

<span>openstack ip floating add ${float_ip} ${inst1}</span>

<span>openstack ip floating create ext-net</span>

<span>sleep 3</span>

<span>float_ip=$(openstack ip floating list | grep ext-net | grep None | awk ‘{print $6}’ | sort -u | head -1)</span>

<span>openstack ip floating add ${float_ip} ${inst2}</span>

<span>openstack ip floating create ext-net</span>

<span>sleep 5</span>

<span>float_ip=$(openstack ip floating list | grep ext-net | grep None | awk ‘{print $6}’ | sort -u | head -1)</span>

<span>openstack ip floating add ${float_ip} ${inst3}</span>

<span>openstack ip floating create ext-net</span>

<span>sleep 5</span>

<span>float_ip=$(openstack ip floating list | grep ext-net | grep None | awk ‘{print $6}’ | sort -u | head -1)</span>

<span>openstack ip floating add ${float_ip} ${inst4}</span>

<span># Optionally create a volume</span>

<span>#openstack volume create –size 100 test</span>

<span>#sleep 15</span>

<span>#volid=$(openstack volume list | awk ‘ /test/ { print $2 } ’)</span>

<span>#echo -e “volid = $volid”</span>

<span># attach the volume to inst1</span>

<span>#openstack server add volume inst1 $volid</span>

<span>#sleep 15</span>

<span>#volname=$(openstack volume list | awk ‘/inst1/ {print $14}’)</span>

<span>#ssh -i tenantdemo.pem cloud-user@$float_ip grep $(basename $volname) /proc/partitions</span>

<span>{% set flavor_name = flavor_name or “m1.tiny” %}</span>

<span>{% set volume_size = volume_size or “1” %}</span>

<span>{% set image_name = image_name or “cirros” %}</span>

<span>{% set number_of_vms = number_of_vms or “1000” %}</span>

<span>{% set concurrency = concurrency or “3” %}</span>

<span>{% set no_of_tenants = no_of_tenants or “200” %}</span>

<span>{% set users_per_tenants = users_per_tenants or “2” %}</span>

<span>    “NovaServers.boot_server_from_volume”: [</span>

<span>            “args”: {</span>

<span>                “flavor”: {</span>

<span>                    “name”: “{{flavor_name}}”</span>

<span>                “image”: {</span>

<span>                    “name”: {{image_name}}</span>

<span>                “volume_size”: “{{volume_size}}”,</span>

<span>            “runner”: {</span>

<span>                “type”: “constant”,</span>

<span>                “times”: {{number_of_vms}},</span>

<span>                “concurrency”: {{concurrency}}</span>

<span>            “context”: {</span>

<span>                “users”: {</span>

<span>                    “tenants”: {{no_of_tenants}},</span>

<span>                    “users_per_tenant”: {{users_per_tenants}}</span>

<span>                “network”: {</span>

<span>                    “start_cidr”: “10.10.0.0/18”</span>

<span>                “quotas”: {</span>

<span>                    “cinder”: {</span>

<span>                        “volumes”: -1,</span>

<span>                        “gigabytes”: -1,</span>

<span>                        “snapshots”: -1</span>

<span>                     “nova”: {</span>

<span>                        “instances”: -1,</span>

<span>                        “ram”: -1,</span>

<span>                        “cores”: -1</span>

<span>                     “neutron”: {</span>

<span>                        “network”: -1,</span>

<span>                        “subnet”: -1,</span>

<span>                        “router”: -1,</span>

<span>                        “port”: -1</span>

This Appendix covers the changes made to few of the yaml files when floating ip configuration wasn't used.

<span>resource_registry:</span>

<span>  OS::TripleO::NodeUserData:</span>

<span>    /home/stack/templates/wipe-disk.yaml</span>

<span>  OS::TripleO::Compute::Net::SoftwareConfig:</span>

<span>    /home/stack/templates/nic-configs/compute.yaml</span>

<span>  OS::TripleO::Controller::Net::SoftwareConfig:</span>

<span>    /home/stack/templates/nic-configs/controller.yaml</span>

<span>  OS::TripleO::CephStorage::Net::SoftwareConfig:</span>

<span>    /home/stack/templates/nic-configs/ceph-storage.yaml</span>

<span>parameter_defaults:</span>

<span>  InternalApiNetCidr: 192.168.10.0/24</span>

<span>  StorageNetCidr: 192.168.120.0/24</span>

<span>  StorageMgmtNetCidr: 192.168.130.0/24</span>

<span>  TenantNetCidr: 10.0.0.0/12</span>

<span>  ExternalNetCidr: 172.22.166.0/24</span>

<span>  InternalApiAllocationPools: [{’start’: ‘192.168.10.200’, ‘end’: ‘192.168.10.250’}]</span>

<span>  StorageAllocationPools: [{’start’: ‘192.168.120.50’, ‘end’: ‘192.168.120.250’}]</span>

<span>  StorageMgmtAllocationPools: [{’start’: ‘192.168.130.50’, ‘end’: ‘192.168.130.250’}]</span>

<span>  TenantAllocationPools: [{’start’: ‘10.0.0.10’, ‘end’: ‘10.15.255.250’}]</span>

<span>  ExternalAllocationPools: [{’start’: ‘172.22.166.171’, ‘end’: ‘172.22.166.180’}]</span>

<span>  ExternalNetworkVlanID: 166</span>

<span>  InternalApiNetworkVlanID: 100</span>

<span>  StorageNetworkVlanID: 120</span>

<span>  StorageMgmtNetworkVlanID: 130</span>

<span>[root@osp7-director yaml-noFloating]# cat network-environment.yaml  </span>

<span>resource_registry:</span>

<span>  OS::TripleO::NodeUserData:</span>

<span>    /home/stack/templates/wipe-disk.yaml</span>

<span>  OS::TripleO::Compute::Net::SoftwareConfig:</span>

<span>    /home/stack/templates/nic-configs/compute.yaml</span>

<span>  OS::TripleO::Controller::Net::SoftwareConfig:</span>

<span>    /home/stack/templates/nic-configs/controller.yaml</span>

<span>  OS::TripleO::CephStorage::Net::SoftwareConfig:</span>

<span>    /home/stack/templates/nic-configs/ceph-storage.yaml</span>

<span>parameter_defaults:</span>

<span>  InternalApiNetCidr: 192.168.10.0/24</span>

<span>  StorageNetCidr: 192.168.120.0/24</span>

<span>  StorageMgmtNetCidr: 192.168.130.0/24</span>

<span>  TenantNetCidr: 10.0.0.0/12</span>

<span>  ExternalNetCidr: 172.22.166.0/24</span>

<span>  InternalApiAllocationPools: [{’start’: ‘192.168.10.200’, ‘end’: ‘192.168.10.250’}]</span>

<span>  StorageAllocationPools: [{’start’: ‘192.168.120.50’, ‘end’: ‘192.168.120.250’}]</span>

<span>  StorageMgmtAllocationPools: [{’start’: ‘192.168.130.50’, ‘end’: ‘192.168.130.250’}]</span>

<span>  TenantAllocationPools: [{’start’: ‘10.0.0.10’, ‘end’: ‘10.15.255.250’}]</span>

<span>  ExternalAllocationPools: [{’start’: ‘172.22.166.171’, ‘end’: ‘172.22.166.180’}]</span>

<span>  ExternalNetworkVlanID: 166</span>

<span>  InternalApiNetworkVlanID: 100</span>

<span>  StorageNetworkVlanID: 120</span>

<span>  StorageMgmtNetworkVlanID: 130</span>

<span>  ControlPlaneSubnetCidr: “24”</span>

<span>  ControlPlaneDefaultRoute: 192.168.110.105</span>

<span>  EC2MetadataIp: 192.168.110.105</span>

<span>  DnsServers: [’171.70.168.183’]</span>

<span>  ExternalInterfaceDefaultRoute: “172.22.166.1”</span>

<span># Set to “br-ex” if using floating IPs on native VLAN on bridge br-ex</span>

<span>  NeutronExternalNetworkBridge: “’’”</span>

<span>heat_template_version: 2015-04-30</span>

<span>description: &gt;</span>

<span>  Software Config to drive os-net-config with 2 bonded nics on a bridge</span>

<span>  with a VLANs attached for the controller role.</span>

<span>parameters:</span>

<span>  ExternalIpSubnet:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the external network</span>

<span>    type: string</span>

<span>  InternalApiIpSubnet:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the internal API network</span>

<span>    type: string</span>

<span>  StorageIpSubnet:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the storage network</span>

<span>    type: string</span>

<span>  StorageMgmtIpSubnet:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the storage mgmt network</span>

<span>    type: string</span>

<span>  TenantIpSubnet:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the tenant network</span>

<span>    type: string</span>

<span>  BondInterfaceOvsOptions:</span>

<span>    default: ’’</span>

<span>    description: The ovs_options string for the bond interface. Set things like</span>

<span>                 lacp=active and/or bond_mode=balance-slb using this option.</span>

<span>    type: string</span>

<span>  ExternalNetworkVlanID:</span>

<span>    default: 166</span>

<span>    description: Vlan ID for the external network traffic.</span>

<span>    type: number</span>

<span>  InternalApiNetworkVlanID:</span>

<span>    default: 100</span>

<span>    description: Vlan ID for the internal_api network traffic.</span>

<span>    type: number</span>

<span>  StorageNetworkVlanID:</span>

<span>    default: 120</span>

<span>    description: Vlan ID for the storage network traffic.</span>

<span>    type: number</span>

<span>  StorageMgmtNetworkVlanID:</span>

<span>    default: 130</span>

<span>    description: Vlan ID for the storage mgmt network traffic.</span>

<span>    type: number</span>

<span>  TenantNetworkVlanID:</span>

<span>    default: 140</span>

<span>    description: Vlan ID for the tenant network traffic.</span>

<span>    type: number</span>

<span>  ExternalInterfaceDefaultRoute:</span>

<span>    default: ‘172.22.166.1’</span>

<span>    description: default route for the external network</span>

<span>    type: string</span>

<span>  ControlPlaneIp:</span>

<span>    default: ’’</span>

<span>    description: IP address/subnet on the ctlplane network</span>

<span>    type: string</span>

<span>  ControlPlaneSubnetCidr:</span>

<span>    default: ‘24’</span>

<span>    description: The subnet CIDR of the control plane network.</span>

<span>    type: string</span>

<span>  ControlPlaneDefaultRoute:</span>

<span>    default: ‘192.168.110.105’</span>

<span>    description: The Control Plane Default route.</span>

<span>    type: string</span>

<span>  DnsServers:</span>

<span>    default: [’171.70.168.183’]</span>

<span>    description: A list of DNS servers (2 max) to add to resolv.conf.</span>

<span>    type: json</span>

<span>  EC2MetadataIp:</span>

<span>    description: The IP address of the EC2 metadata server.</span>

<span>    type:  string</span>

<span>resources:</span>

<span>  OsNetConfigImpl:</span>

<span>    type: OS::Heat::StructuredConfig</span>

<span>    properties:</span>

<span>      group: os-apply-config</span>

<span>      config:</span>

<span>        os_net_config:</span>

<span>          network_config:</span>

<span>              type: interface</span>

<span>              name: nic1</span>

<span>              use_dhcp: false</span>

<span>              dns_servers: {get_param: DnsServers}</span>

<span>              addresses:</span>

<span>                  ip_netmask:</span>

<span>                    list_join:</span>

<span>                      - ‘/’</span>

<span>                      - - {get_param: ControlPlaneIp}</span>

<span>                        - {get_param: ControlPlaneSubnetCidr}</span>

<span>              routes:</span>

<span>                  ip_netmask: 169.254.169.254/32</span>

<span>                  next_hop: {get_param: EC2MetadataIp}</span>

<span>              type: ovs_bridge</span>

<span>              name: {get_input: bridge_name}</span>

<span>              use_dhcp: false</span>

<span>              members:</span>

<span>                 type: interface</span>

<span>                 name: nic4</span>

<span>                 primary: true</span>

<span>                 type: vlan</span>

<span>                 vlan_id: {get_param: ExternalNetworkVlanID}</span>

<span>                 addresses:</span>

<span>                    ip_netmask: {get_param: ExternalIpSubnet}</span>

<span>                 routes:</span>

<span>                    ip_netmask: 0.0.0.0/0</span>

<span>                    next_hop: {get_param: ExternalInterfaceDefaultRoute}</span>

<span>              type: ovs_bridge</span>

<span>              name: br-mgmt</span>

<span>              members:</span>

<span>                  type: interface</span>

<span>                  name: nic3</span>

<span>                  primary: true</span>

<span>                  type: vlan</span>

<span>                  vlan_id: {get_param: InternalApiNetworkVlanID}</span>

<span>                  addresses:</span>

<span>                    ip_netmask: {get_param: InternalApiIpSubnet}</span>

<span>              type: ovs_bridge</span>

<span>              name: br-storage-pub</span>

<span>              mtu: 9000</span>

<span>              members:</span>

<span>                  type: interface</span>

<span>                  name: nic5</span>

<span>                  mtu: 9000</span>

<span>                  primary: true</span>

<span>                  type: vlan</span>

<span>                  mtu: 9000</span>

<span>                  vlan_id: {get_param: StorageNetworkVlanID}</span>

<span>                  addresses:</span>

<span>                    ip_netmask: {get_param: StorageIpSubnet}</span>

<span>              type: ovs_bridge</span>

<span>              name: br-storage-clus</span>

<span>              mtu: 9000</span>

<span>              members:</span>

<span>                  type: interface</span>

<span>                  name: nic6</span>

<span>                  mtu: 9000</span>

<span>                  primary: true</span>

<span>                  type: vlan</span>

<span>                  mtu: 9000</span>

<span>                  vlan_id: {get_param: StorageMgmtNetworkVlanID}</span>

<span>                  addresses:</span>

<span>                    ip_netmask: {get_param: StorageMgmtIpSubnet}</span>

<span>outputs:</span>

<span>  OS::stack_id:</span>

<span>    description: The OsNetConfigImpl resource.</span>

<span>    value: {get_resource: OsNetConfigImpl}</span>

<span>#/bin/bash</span>

<span>openstack overcloud deploy –templates \</span>

<span>-e /usr/share/openstack-tripleo-heat-templates/overcloud-resource-registry-puppet.yaml \</span>

<span>-e /usr/share/openstack-tripleo-heat-templates/environments/network-isolation.yaml \</span>

<span>-e /home/stack/templates/network-environment.yaml \</span>

<span>-e /home/stack/templates/storage-environment.yaml \</span>

<span>-e /home/stack/templates/cisco-plugins.yaml \</span>

<span>-e /home/stack/templates/post_config.yaml \</span>

<span>–control-flavor control –compute-flavor compute –ceph-storage-flavor CephStorage \</span>

<span>–compute-scale 3 –control-scale 3 –ceph-storage-scale 3 \</span>

<span>–libvirt-type kvm \</span>

<span>–ntp-server &lt;ntp server ip&gt; \</span>

<span>–neutron-network-type vlan \</span>

<span>–neutron-tunnel-type vlan \</span>

<span>–neutron-bridge-mappings datacentre:br-ex,physnet-tenant:br-tenant \</span>

<span>–neutron-network-vlan-ranges physnet-tenant:250:749 \</span>

<span>–neutron-disable-tunneling –timeout 300 \</span>

<span>–verbose –debug –log-file overcloud_new.log</span>

**Ramakrishna Nishtala, Cisco Systems, Inc.**

Ramakrishna Nishtala is a Technical Leader in Cisco UCS and Data Center solutions group and has over 20 years of experience in IT infrastructure, Virtualization and Cloud computing. In his current role at Cisco Systems, he works on best practices, optimization and performance tuning on OpenStack and other Open Source Storage solutions on Cisco UCS platforms.

**Vijay Durairaj, Cisco Systems, Inc.**

Vijay Durairaj is a Technical Marketing Engineer with Cisco UCS Performance and Solutions Data Center Group. Vijay has over 12 years of experience in IT Infrastructure, Server Virtualization and Cloud Computing. His focus area includes Unified Computing Systems, Network, Storage and Performance benchmarking on Cisco UCS Platforms. Vijay also holds Cisco Unified Computing Design Certification.

**Patryk Wolsza, Intel**

Patryk Wolsza is a Data Center Architect in the Intel Cloud Platforms Group, with a focus on Software Defined Infrastructure. With more than 12 years of expertise in different virtualization and cloud platforms, Patryk has broad experience in cloud solutions, system designs, influencing data center designs and understanding connections between ordinary Data Centers, virtualization and SDI. He believes that mastering the purpose of existing cloud solutions is the key to deliver and maintain the complete product, hardware and software, for any demand.

**Pawel Koniszewski, Intel**

Pawel Koniszewski is Software Engineer at Intel in the Cloud Platforms Group. He started his career at Intel during his studies, and from the beginning has been focused on Cloud Computing. In his current role he is a member of the Software Defined Infrastructure team and is focused on enterprise readiness of cloud-based solutions. His primary area of interest is preserving SLA of instances in virtualized environments. Pawel is interested in building reliable distributed systems, optimizing performance and increasing user experience. Pawel also works with Intel partners on building cloud ecosystems based on OpenStack

**Guil Barros, Red Hat**

Guil Barros, is a Principal Product Manager at Red Hat in the RHEL OpenStack Platform group. His focus is on working with partners to create interesting solutions to customer problems. Guil has a passion for finding the sweet spot that ties existing technologies together into truly useful implementations. In his career, this has spanned all the way from understanding how to best interact with users, to architecting support strategy, to bringing partners to innovate together.

**Karthik Prabhakar, Red Hat**

Karthik Prabhakar, helps develop joint cloud solutions with Red Hat's strategic partners (including Cisco and Intel), and facilitates successful market adoption through leadership of GTM initiatives in collaboration with worldwide field and partner teams, and leading-edge customers. Further background at <http://www.linkedin.com/in/worldhopper>

**Steven Reichard, Red Hat**

Steven Reichard is a consulting engineer and manager in Red Hat's System's Design and Engineering group. This team's mission is to eliminate roadblocks to the wider adoption & ease-of-use of our product portfolio to solve ever more demanding customer/partner solutions. Most recently Steve has focused on Red Hat Enterprise Linux OpenStack Platform including enabling partner solutions based on RHEL-OSP and Red Hat Ceph Storage. Steve is a Red Hat Certified Engineer (RHCE) who has more than 20 years of computer industry experience.

<span>·</span>

Cisco Systems, Inc.: Brian Demers, Damien Zhao, Michael Wang, Mohsen Mortazavi, Muhammad Afzal, Sandhya Dasu, Shanthi Kumar Adloori, Shree Lokare, Steven Hillman, Timothy Swanson, Vishwanath Jakka

<span>·</span>

Intel: Arek Chylinski, Das Kamhout, Kamil Rogon, Marcin Karkocha, Marek Strachacki

<span>·</span>

Red Hat: Andrew Beekhof, Dan Sneddon, Dmitry Tantsur, Fabio Di Nitto, Marek Grac, Mike Orazi, Steve Hardy, Tushar Katarki
