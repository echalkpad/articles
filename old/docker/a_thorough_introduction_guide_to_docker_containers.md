# A thorough introduction guide to Docker containers

[Original URL](http://www.dedoimedo.com/computers/docker-guide.html)

> Updated: May 10, 2015 Let me start with a big promise. You will absolutely LOVE this article today. It's going to be long, detailed and highly useful. Think GRUB, GRUB2\. The same thing here. Only we...

<span class="bold_text">Updated:</span>

 May 10, 2015

Let me start with a big promise. You will absolutely LOVE this article today. It's going to be long, detailed and highly useful. Think [GRUB](http://www.dedoimedo.com/computers/grub.html), [GRUB2](http://www.dedoimedo.com/computers/grub-2.html). The same thing here. Only we will tackle [Docker](https://www.docker.com/), a nice distribution platform that wraps the Linux Containers (LXC) technology in a simple, convenient way.

I will show you how to get started, and then we will create our own containers with SSH and Apache, learn how to use Dockerfiles, expose service ports, and solve an immense number of little bugs and problems that normally never get addressed in public forums. Please, without further ado, follow me.

![Teaser](http://www.dedoimedo.com/images/computers-years/2015-1/docker-teaser.png)

## Table of Contents

## []()Introduction

I have given a brief overview of the technology in a Gizmo's Freeware [article](http://www.techsupportalert.com/content/docker-your-containers.htm) sometime last year. Now, we are going to get serious about using Docker. First, it is important to remember that this framework allows you to use LXC in a convenient manner, without having to worry about all the little details. It is the next step in this world, the same way OpenStack is the next evolutionary step in the virtualization world. Let me give you some history and analogies.

Virtualization began with software that lets you abstractize your hardware. Then, to make things speedier, virtualization programs began using hardware acceleration, and then you also got paravirtualization. In the end, hypervisors began popping up like mushrooms after rain, and it became somewhat difficult to provision and manage them all. This is the core reason for concepts like OpenStack, which hide different platforms under a unified API.

The containers began their way in a similar manner. First, we had the chroot, but processes running inside the jailed environment shared the same namespace and fought for the same resources. Then, we got the [kexec](http://www.dedoimedo.com/computers/crash-book.html) system call, which let us boot into the context of another kernel without going through the BIOS. Then, control groups came about, allowing us to partition system resources like CPU, memory and others into subgroups, thus allowing better control, hence the name, of processes running on the system.

Later on, the Linux kernel began offering a full isolation of resources, using cgroups as the basic partitioning mechanism. Technically, this is a system-level virtualization technology, allowing you to run multiple instances of the running kernel on top of the control host inside self-contained environments, with the added bonus of very little performance penalty and overhead.

Several competing technologies tried to offer similar solutions, like OpenVZ, but the community eventually narrowed down its focus to the native enablement inside the mainline kernel, and this seems to be the future direction. Still, LXC remains somewhat difficult to use, as a fair amount of technical knowledge and scripting is required to get the containers running.

This is where Docker comes into place. It tries to take away the gritty pieces and offer a simple method of spawning new container instances without worrying about the infrastructure backend. Well, almost. But the level of difficulty is much less.

Another strong advantage of Docker is a widespread community acceptance, as well as the emphasis on integration with cloud services. Here we go full buzzword, and this means naming some of the big players like AWS, Hadoop, Azure, Jenkins and others. Then we can also talk about Platform as a Service (Paas), and you can imagine how much money and focus this is going to get in the coming years. The technological landscape is huge and confusing, and it's definitely going to keep on changing and evolving, with more and more concepts and wrapper technologies coming into life and building on top of Docker.

But we want to focus on the technological side. Once we master the basic, we will slowly expand and began utilizing the strong integration capabilities, the flexibility of the solution, and work on making our cloud ecosystem expertise varied, automated and just pure rad. That won't happen right now, but I want to help you navigate the first few miles, or should we say kilometers, of the muddy startup waters, so you can begin using Docker in a sensible, efficient way. Since this is a young technology, it's Wild West out there, and most of the online documentation, tips, tutorials and whatnot are outdated, copy & paste versions that do not help anyone, and largely incomplete. I want to fix that today.

## []()Docker implementation

A bit more boring stuff before we do some cool things. Anyhow, Docker is mostly about LXC, but not just. It's been designed to be extensible, and it can also interface with libvirt and systemd. In a way, this makes it almost like a hyper-hypervisor, as there's potential for future growth, and when additional modules are added, it could effectively replace classic hypervisors like Xen or KVM or anything using libvirt and friends.

![Docker diagram](http://www.dedoimedo.com/images/computers-years/2015-1/docker-diagram.png)

This be a public domain image, if you wondered.

## []()Getting started

We will demonstrate [using](http://www.dedoimedo.com/computers/centos-7-perfect-desktop.html) CentOS 7\. Not Ubuntu. Most of the online stuff focuses on Ubuntu, but I want to show you how it's done using as-near-as-enterprise flavor of Linux as possible, because if you're going to be using Docker, it's gonna be somewhere business like. The first thing is to install docker:

yum install docker-io

Once the software is installed, you can start using it. However, you may encounter the following two issues the first time you attempt to run docker commands:

docker <any one command><br>
FATA[0000] Get <http:///var/run/docker.sock/v1.18/images/json>: dial unix /var/run/docker.sock: no such file or directory. Are you trying to connect to a TLS-enabled daemon without TLS?

And the other error is:

docker <any one command><br>
FATA[0000] Get <http:///var/run/docker.sock/v1.18/containers/json>: dial unix /var/run/docker.sock: permission denied. Are you trying to connect to a TLS-enabled daemon without TLS?

The reason is, you need to 

<span class="red_text">start</span>

 the Docker service first. Moreover, you must run the technology as root, because Docker needs access to some rather sensitive pieces of the system, and interact with the kernel. That's how it works.

systemctl start docker

Now we can go crazy and begin using Docker.

## []()Docker commands

The basic thing is to run docker help to get the available list of commands. I will not go through all the options. We will learn more about them as we go along. In general, if you're ever in doubt, you should consult the pretty decent [online](https://docs.docker.com/) documentation. The complete CLI reference [also](https://docs.docker.com/reference/commandline/cli/) kicks ass. And then, there's also an excellent [cheat](https://github.com/wsargent/docker-cheat-sheet) sheet on GitHub. But our first mission will be to download a new Docker image and then run our first instance.

## []()Pull image

There are many available images. We want to practice with CentOS. This is a good starting point. An [official](https://registry.hub.docker.com/_/centos/) repository is available, and it lists all the supported images and tags. Indeed, at this point, we need to understand how Docker images are labeled.

The naming convention is repository:tag, for example 

<span class="red_text">centos:latest</span>

. In other words, we want the latest CentOS image. But the require image might as well be 

<span class="red_text">centos:6.6</span>

. All right, let's do it.

![Pulling image](http://www.dedoimedo.com/images/computers-years/2015-1/docker-pulling-image.png)

Now let's list the images by running the 

<span class="red_text">docker images</span>

 command:

![Images](http://www.dedoimedo.com/images/computers-years/2015-1/docker-images.png)

## []()Start a Docker container

As we've seen in my original tutorial, the simplest example is to run a shell:

docker run -ti centos:centos7 /bin/bash

So what do we have here? We are [running](https://docs.docker.com/reference/run/) a new container instance with its own TTY (

<span class="red_text">-t</span>

) and STDIN (

<span class="red_text">-i</span>

), from the CentOS 7 image, with a BASH shell. With a few seconds, you will get a new shell inside the container. Now, it's a very basic, very stripped-down operating system, but you can start building things inside it.

![Run container](http://www.dedoimedo.com/images/computers-years/2015-1/docker-run-container.png)

![Container running top](http://www.dedoimedo.com/images/computers-years/2015-1/docker-container-running-top.png)

## []()Install Apache & SSH

Let's setup a Web server, which will also have SSH access. To this end, we will need to do some rather basic installations. Grab Apache (httpd) and SSHD (openssh-server), and configure them. This has nothing to do with Docker, per se, but it's a useful exercise.

How, some of you may clamor, wait, you don't need SSH inside a container, it's a security risk and whatnot. Well, maybe, yes and no, depending on what you need and what you intend to use the container for. But let's leave the security considerations aside. The purpose of the exercise is to learn how to setup and run ANY service.

### []()Start service

You might want to start your Apache using an init script or a systemd command. This will not quite work. Specifically for CentOS, it comes with systemd, but more importantly, the container does not have its own systemd. If you try, the commands will fail.

systemctl start httpd<br>
Failed to get D-Bus connection: No connection to service manager.

There are hacks [around](https://github.com/docker/docker/issues/7459) this problem, and we will learn about some of these in a future tutorial. But in general, given the lightweight and simple nature of containers, you do not really need a fully fledged startup service to run your processes. This does add some complexity.

### []()Apache service

To run Apache (HTTPD), just execute 

<span class="red_text">/usr/sbin/httpd</span>

 - or an equivalent command in your distro. The service should start, most likely with a warning that you have not configured your ServerName directive in httpd.conf. We have learned [how](http://www.dedoimedo.com/computers/apache_book_part.html) to do this in my rather extensive Apache guide.

/usr/sbin/httpd<br>
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.4\. Set the 'ServerName' directive globally to suppress this message

### []()SSH service

With SSHD, run /usr/sbin/sshd.

/usr/sbin/sshd -f /etc/ssh/sshd_config<br>
Could not load host key: /etc/ssh/ssh_host_rsa_key<br>
Could not load host key: /etc/ssh/ssh_host_dsa_key<br>
Could not load host key: /etc/ssh/ssh_host_ecdsa_key<br>
Could not load host key: /etc/ssh/ssh_host_ed25519_key

You will also fail, because you won't have all the keys. Normally, startup scripts take of this, so you will need to run the ssh-keygen command once before the service starts correctly. Either one of the two commands will work:

/usr/bin/ssh-keygen -t rsa -f <path to file>

/usr/bin/ssh-keygen -A<br>
ssh-keygen: generating new host keys: RSA1 RSA DSA ECDSA ED25519

## []()Check if Web server is up

Now, inside the container, we can see that Apache is indeed running.

ps -ef|grep apache<br>
apache 87 86 0 10:47 ? 00:00:00 /usr/sbin/httpd<br>
apache 88 86 0 10:47 ? 00:00:00 /usr/sbin/httpd<br>
apache 89 86 0 10:47 ? 00:00:00 /usr/sbin/httpd<br>
apache 90 86 0 10:47 ? 00:00:00 /usr/sbin/httpd<br>
apache 91 86 0 10:47 ? 00:00:00 /usr/sbin/httpd

But what if we want to check external connectivity? At this point, we have a couple of problems at our hand. One, we have not setup any open ports, so to speak. Two, we do not know what the IP address of our container is. Now, if you try to run the ifconfig inside the BASH shell, you won't get anywhere, because the necessary package containing the basic networking commands is not installed. Good, because it makes our container slim and secure.

### []()Expose incoming ports

Like with any Web server, we will need to allow incoming connections. We will use the default port 80\. This is no different than port forwarding in your router, allowing firewall policies and whatnot. With Docker, there are several ways you can achieve the desired result.

When starting a new container with the run command, you can use 

<span class="red_text">-p</span>

 option to specify which ports to open. You can choose a single port or a range of ports, and you can also map both the host port (hostPort) and container port (containerPort). For instance:

- <span class="red_text">-p 80</span>

   will expose container port 80\. It will be automatically mapped to a random port on the host. We will learn later on how to identify the correct port.
- <span class="red_text">-p 80:80</span>

   will map the container port to the host port 80\. This means you do not need to know the internal IP address of the container. There is an element of internal NAT involved, which goes through the Docker virtual interface. We will discuss this soon. Moreover, if you use this method, only a single container will be able to bind to port 80\. If you want to use multiple Web servers with different IP addresses, you will have to set them up each on a different port.

docker run -ti -p 22:22 -p 80:80 image-1:latest<br>
FATA[0000] Error response from daemon: Cannot start container 64bd520e2d95a699156f5d40331d1aba972039c3c201a97268d61c6ed17e1619: Bind for 0.0.0.0:80 failed: port is already allocated

There are many additional considerations. IP forwarding, bridged networks, public and private networks, subnet ranges, firewall rules, load balancing, and more. At the moment, we do not need to worry about these.

There is also an additional method of how we can expose port, but we will discuss that later on, when we touch on the topic of Dockerfiles, which are templates for building new images. For now, we need to remember to run our images with the -p option.

### []()Check IP address

If you want to leave your host ports free, then you can omit the hostPort piece. In that case, you can connect to the container directly, using its IP address and Web server port. To do that, we need to figure our the container details:

docker inspect <container name or ID>

This will give a very long list of details, much like the [KVM](http://www.dedoimedo.com/computers/kvm-intro.html) XML config, except this one is written in JSON, which is another modern and ugly format for data. Readable but extremely ugly.

docker inspect distracted_euclid<br>
[{<br>
"AppArmorProfile": "",<br>
"Args": [],<br>
"Config": {<br>
"AttachStderr": true,<br>
"AttachStdin": true,<br>
"AttachStdout": true,<br>
"Cmd": [<br>
"/bin/bash"<br>
],<br>
"CpuShares": 0,<br>
"Cpuset": "",<br>
"Domainname": "",<br>
"Entrypoint": null,<br>
"Env": [<br>
...<br>
"ExposedPorts": {<br>
"80/tcp": {}<br>
},<br>
"Hostname": "43b179c5aec7",<br>
"Image": "centos:centos7",<br>
"Labels": {},<br>
"MacAddress": "",<br>
...

We can narrow it down to just the IP address.

docker inspect <container name or ID> | grep -i "ipaddr"<br>
"IPAddress": "172.17.0.20",

### []()Testing new configuration

Let's start fresh. Launch a new instance, setup Apache, start it. Open a Web browser and test. If it works, then you have properly configured your Web server. Exactly what we wanted.

docker run -it -p 80:80 centos:centos7 /bin/bash

If we check the running container, we can see the port mapping - the output is split over multiple lines for brevety, so please excuse that. Normally, the all-uppercase titles will show as the row header, and then, you will get all the rest printed below, one container per line.

# docker ps<br>
CONTAINER ID IMAGE COMMAND<br>
43b179c5aec7 centos:centos7 "/bin/bash"

CREATED STATUS PORTS<br>
2 hours ago Up 2 hours 0.0.0.0:80->80/tcp

NAMES distracted_euclid

And in the browser, we get:

![Web server running](http://www.dedoimedo.com/images/computers-years/2015-1/docker-httpd-running-successfully.jpg)

<span class="bold_text">Optional:</span>

 Now, the internal IP address range will only be accessible on the host. If you want to make it accessible from other machines, you will need your NAT and IP forwarding. And if you want to use names, then you will need to properly configure the /etc/hosts as well as DNS. For container, this can be done using the 

<span class="red_text">–add-host=“host:IP”</span>

 directive when running a new instance.

<span class="bold_text">Another note:</span>

 Remember that Docker has its own internal networking, much [like](http://www.dedoimedo.com/computers/virtualbox-network-sharing.html) VirtualBox and KVM, as we've seen in my other tutorials. It's a fairly extensive /16 network, so you have quite a lot of freedom. On the host:

# /sbin/ifconfig<br>
docker0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 1500<br>
inet 172.17.42.1 netmask 255.255.0.0 broadcast 0.0.0.0<br>
inet6 fe80::5484:7aff:fefe:9799 prefixlen 64 scopeid 0x20<link><br>
ether 56:84:7a:fe:97:99 txqueuelen 0 (Ethernet)<br>
RX packets 6199 bytes 333408 (325.5 KiB)<br>
RX errors 0 dropped 0 overruns 0 frame 0<br>
TX packets 11037 bytes 32736299 (31.2 MiB)<br>
TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

## []()Check if SSH works

We need to do the same exercise with SSH. Again, this means exposing port 22, and we have several options available. To make it more interesting, let's try with a random port assignment:

docker run -ti -p 20 -p 80 centos:centos7 /bin/bash

And if we check with docker ps, specifically for ports:

0.0.0.0:49176->22/tcp, 0.0.0.0:49177->80/tcp boring_mcclintock

This means you can connect to the docker0 IP address, ports as specified above in the docker ps command output, and this equivalent to actually connecting to the container IP directly, on their service port. This can be useful, because you do not need to worry about the internal IP address that your container uses, and it can simplify forwarding. Now, let's try to connect. We can use the host port, or we can use the container IP directly.

ssh 172.17.42.1 -p 49117

Either way, we will get what we need, for instance:

ssh 172.17.0.5<br>
The authenticity of host '172.17.0.5 (172.17.0.5)' can't be established. ECDSA key fingerprint is 00:4b:de:91:60:e5:22:cc:f7:89:01:19:3e:61:cb:ea.<br>
Are you sure you want to continue connecting (yes/no)? yes<br>
Warning: Permanently added '172.17.0.5' (ECDSA) to the list of known hosts.<br>
root@172.17.0.5's password:

### []()Wait, what is the root password?

We will fail because we do not have the root password. So what do we do now? Again, we have several options. First, try to change the root password inside the container using the passwd command. But this won't work, because the passwd utility is not installed. We can then grab the necessary RPM and set it up inside the container. On the host, check the dependencies:

rpm -q –whatprovides /etc/passwd<br>
setup-2.8.71-5.el7.noarch

But this is a security vulnerability. We want our containers to be lean. So we can just 

<span class="red_text">copy</span>

 the password hash from 

<span class="red_text">/etc/shadow</span>

 on the host into the container. Later, we will learn about a more streamlined way of doing it.

Another thing that strikes quite clearly is that we are repeating all our actions. This is not efficient, and this is why we want to preserve changes we have done to our container. The next section handles that.

![SSH success](http://www.dedoimedo.com/images/computers-years/2015-1/docker-ssh-success.png)

## []()Commit image

After you've made changes to the container, you may want to commit it. In other words, when starting a new container later on, you will not need to repeat all the steps from scratch, you will be able to reuse your existing work and save time and bandwidth. You can commit an image based on its ID or its alias:

docker commit <container name or ID> <new image>

For example, we get the following:

docker commit 43b179c5aec7 myapache3<br>
1ee373ea750434354faeb1cb70b0177b463c51c96c9816dcdf5562b4730dac54

![Commit image](http://www.dedoimedo.com/images/computers-years/2015-1/docker-image-commit.png)

Check the list of images again:

![Image committed](http://www.dedoimedo.com/images/computers-years/2015-1/docker-image-committed.png)

## []()Dockerfile

A more streamlined way of creating your images is to [use](https://docs.docker.com/reference/builder/) Dockerfiles. In a way, it's like using Makefile for compilation, only in Docker format. Or an RPM specfile if you will. Basically, in any one "build" directory, create a Dockerfile. We will learn what things we can put inside one, and why we want it for our Apache + SSH exercise. Then, we will build a new image from it. We can combine it with our committed images to preserve changes already done inside the container, like the installation of software, to make it faster and save network utilization.

Before we go any further, let's take a look at a Dockerfile that we will be using for our exercise. At the moment, the commands may not make much sense, but they soon will.

FROM myhttptest2:latest

EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]

EXPOSE 80

RUN mkdir -p /run/httpd<br>
CMD ["/usr/sbin/httpd", "-D", "FOREGROUND"]

What do we have here?

- The 

  <span class="red_text">FROM</span>

   directory tells us what repo:tag to use as the baseline. In our case, it's one of the committed images that already contains the httpd and sshd binaries, SSH keys, and a bit more.
- <span class="red_text">EXPOSE 22</span>

   - This line exposes port 22 inside the container. We can map it further using the -p option at runtime. The same is true for EXPOSE 80, which is relevant for the Web server.
- <span class="red_text">CMD [“/usr/sbin/sshd”, “-D”]</span>

   - This instructions [runs](https://docs.docker.com/reference/builder/#cmd) an executable, with optional arguments. It is as simple as that.
- <span class="red_text">RUN mkdir -p /run/httpd</span>

   - This instruction runs a command in a new layer on top of the base image - and COMMITS the results. This is very important to remember, as we will soon discuss what happens if you don't use the RUN mkdir thingie with Apache.
- <span class="red_text">CMD [“/usr/sbin/httpd”, “-D”, “FOREGROUND”]</span>

   - We run the server, in the foreground. The last bit is optional, but for the time being, you can start Apache this way. Good enough.

As you can see, Dockerfiles aren't that complex or difficult to write, but they are highly useful. You can pretty much add anything you want. Using these templates form a basis for automation, and with conditional logic, you can create all sorts of scenarios and spawn containers that match your requirements.

### []()Build image

Once you have a Dockerfile in place, it's time to [build](https://docs.docker.com/reference/commandline/cli/#build) a new image. Dockerfiles must follow a strict convention, just like Makefiles. It's best to keep different image builds in separate sub-directories. For example:

docker build -t test5 .<br>
Sending build context to Docker daemon 41.47 kB<br>
Sending build context to Docker daemon<br>
Step 0 : FROM myapache4:latest<br>
--> 7505c70235e6<br>
Step 1 : EXPOSE 22 80<br>
--> Using cache<br>
--> 58f11217c3e3<br>
Step 2 : CMD /usr/sbin/sshd -D<br>
--> Using cache<br>
--> 628c3d6b5399<br>
Step 3 : RUN mkdir -p /run/httpd<br>
--> Using cache<br>
--> 5fc118f61a4d<br>
Step 4 : CMD /usr/sbin/httpd -D FOREGROUND<br>
--> Using cache<br>
--> d892acd86198<br>
Successfully built d892acd86198

The command tells us the following: 

<span class="red_text">-t</span>

 repository name from a Dockerfile stored in the current directory (

<span class="red_text">.</span>

). That's all. Very simple and elegant.

### []()Test image

Run a new container from the created image. If everything went smoothly, you should have both SSH connectivity, as well as a running Web server in place. Again, all the usual network related rules apply.

![Running successfully, built from Dockerfile](http://www.dedoimedo.com/images/computers-years/2015-1/docker-dockerfile-web-running-successfully.jpg)

## []()Alternative build

Once you have the knowledge how do it on your own, you can try one of the official Apache builds. Indeed, the Docker repository contains a lot of good stuff, so you should definitely invest time checking available templates. For Apache, you only need the following in your Dockerfile - the second like is optional.

FROM httpd:2.4<br>
COPY ./public-html/ /usr/local/apache2/htdocs/

### []()COPY instruction

What do we have above? Basically, in the Dockerfile, we have the declaration what template to use. And then, we have a [COPY](https://docs.docker.com/reference/builder/#copy) instructions, which will look for a public-html directory in the current folder and copy it into the container during the build. In the same manner, you can also copy your httpd.conf file. Depending on your distribution, the paths and filenames might differ. Finally, after building the image and running the container:

docker run -ti -p 22 -p 80 image-1:latest<br>
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.17\. Set the 'ServerName' directive globally to suppress this message<br>
[Thu Apr 16 21:08:35.967670 2015] [mpm_event:notice] [pid 1:tid 140302870259584] AH00489: Apache/2.4.12 (Unix) configured – resuming normal operations<br>
[Thu Apr 16 21:08:35.976879 2015] [core:notice] [pid 1:tid 140302870259584] AH00094: Command line: 'httpd -D FOREGROUND'

![Default HTTPD works](http://www.dedoimedo.com/images/computers-years/2015-1/docker-default-httpd-build-works.jpg)

## []()Advantages of containers

There are many good reasons why you want to use this technology. But let's just briefly focus on what we gain by running these tiny, isolated instances. Sure, there's a lot happening under the hood, in the kernel, but in general, the memory footprint of spawned containers is fairly small. In our case, the SSH + Apache containers use a tiny fraction of extra memory. Compare this to any virtualization technology.

![Container memory](http://www.dedoimedo.com/images/computers-years/2015-1/docker-container-memory.png)

![Low memory usage](http://www.dedoimedo.com/images/computers-years/2015-1/docker-container-memory-more.jpg)

## []()Problems you may encounter & troubleshooting

Let's go back to the Apache example, and now you will also learn why so many online tutorials sin the sin of copy & pasting information without checking, and why most of the advice is not correct, unfortunately. It has to do with, what do you do if your Apache server seems to die within a second or two after launching the container? Indeed, if this happens, you want to step into the container and troubleshoot. To that end, you can use the 

<span class="red_text">docker exec</span>

 command to attach a shell to the instance.

docker exec -ti boring_mcclintock /bin/bash

Then, it comes down to reading logs and trying to figure out what might have gone wrong. If your httpd.conf is configured correctly, you will have access and error logs under /var/log/httpd:

[auth_digest:error] [pid 25] (2)No such file or directory: AH01762: Failed to create shared memory segment on file /run/httpd/authdigest_shm.25

A typical problem is that you may be a 

<span class="red_text">missing /run/httpd</span>

 directory. If this one does not exist in your container, httpd will start and die. Sounds so simple, but few if any reference mentions this.

While initially playing with containers, I did encounter this issue. Reading online, I found several suggestions, none of which really helped. But I do want to elaborate on them, and how you can make progress in your problem solving, even if intermediate steps aren't really useful.

Suggestion 1: You must use -D FOREGROUND to run Apache, and you must also use ENTRYPOINT rather than CMD. The difference between the two instructions is [very](http://stackoverflow.com/questions/21553353/what-is-the-difference-between-cmd-and-entrypoint-in-a-dockerfile) subtle. And it does not solve our problem in any way.

ENTRYPOINT ["/usr/sbin/httpd"]<br>
CMD ["-D", "FOREGROUND"]

Suggestion 2: Use a separate startup script, which could work around any issues with the starting or restarting of the httpd service. In other words, the Dockerfile becomes something like this:

...<br>
EXPOSE 80<br>
COPY ./run-httpd.sh /run-httpd.sh<br>
RUN chmod -v +x /run-httpd.sh<br>
CMD ["/run-httpd.sh"]

And the contents of the run-httpd.sh script are [along](https://github.com/CentOS/CentOS-Dockerfiles/blob/master/httpd/centos7/run-httpd.sh) the lines of:

#!/bin/bash

rm -rf /run/httpd/*

exec /usr/sbin/apachectl -D FOREGROUND

Almost there. Remove any old leftover PID files, but these are normally not stored under /run/httpd. Instead, you will find them under /var/run/httpd. Moreover, we are not certain that this directory exists.

Finally, the idea is to work around any problems with the execution of a separation shell inside which the httpd thread is spawned. While it does provide us with additional, useful lessons on how to manage the container, with COPY and RUN instructions, it's not what we need to fix the issue.

Step 3 : EXPOSE 80<br>
--> Using cache<br>
--> 108785c8e507<br>
Step 4 : COPY ./run-httpd.sh /run-httpd.sh<br>
--> 582d795d59d4<br>
Removing intermediate container 7ff5b58b40bf<br>
Step 5 : RUN chmod -v +x /run-httpd.sh<br>
--> Running in 56fadf4dd2d4<br>
mode of '/run-httpd.sh' changed from 0644 (rw-r–r–) to 0755 (rwxr-xr-x)<br>
--> 928640f680cf<br>
Removing intermediate container 56fadf4dd2d4<br>
Step 6 : CMD /run-httpd.sh<br>
--> Running in f9c6b30795e2<br>
--> b2dcc2818a27<br>
Removing intermediate container f9c6b30795e2<br>
Successfully built b2dcc2818a27

This won't work, because apachectl is an unsupported command for managing httpd, plus we have seen problems using startup scripts and utilities earlier, and we will work on fixing this in a separate tutorial.

docker run -ti -p 80 image-2:latest<br>
Passing arguments to httpd using apachectl is no longer supported. You can only start/stop/restart httpd using this script. If you want to pass extra arguments to httpd, edit the /etc/sysconfig/httpd config file.

But it is useful to try these different things, to get the hang of it. Unfortunately, it also highlights the lack of maturity and the somewhat inadequate documentation for this technology out there.

## []()Additional commands

There are many ways you can interact with your container. If you do not want to attach a new shell to a running instance, you can use a subset of docker commands directly against the container ID or name:

docker <command> <container name or ID>

For instance, to get the top output from the container:

docker top boring_stallman

If you have too many images, some of which have just been used for testing, then you can remove them to free up some of your disk space. This can be done using the 

<span class="red_text">docker rmi</span>

 command.

# docker rmi -f test7<br>
Untagged: test7:latest<br>
Deleted: d0505b88466a97b73d083434b2dd0e7b59b9a5e8d0438b1bf8c6c<br>
Deleted: 5fc118f61bf856f6f3d90e0e71076b737fa7cc58cd56785ea7904<br>
Deleted: 628c3d6b53992521c9c1fdda4148693347c3d10b1d130f7e091e7<br>
Deleted: 58f11217c3e31206b4e41d07100a797cd4d17e4569b0fdb8b7a18<br>
Deleted: 7505c70235e638c54028ea5b63eba2b691de6bee67c2cb5e2861a<br>
...

Then, you can also run your containers in the background. Using the 

<span class="red_text">-d</span>

 flag will do exactly that, and you will get the shell prompt back. This is also useful if you do not mask signals, so if you accidentally break in your shell, you might kill the container when it's running in the foreground.

docker run -d -ti -p 80 image-3:latest

You can also check events, examine changes inside a container's filesystem as well as check history, so you basically have a version control in place, export or import tarred images to and from remote locations, including over the Web, and more.

### []()Differences between exec and attach

If you read through the documentation, you will notice you can connect to a running container using either 

<span class="red_text">exec</span>

 or 

<span class="red_text">attach</span>

 commands. So what's the difference, you may ask? If we look at the official documentation, then:

The docker exec command runs a new command in a running container. The command started using docker exec only runs while the container's primary process (PID 1) is running, and it is not restarted if the container is restarted.

On the other hand, attach gives you the following:

The docker attach command allows you to attach to a running container using the container's ID or name, either to view its ongoing output or to control it interactively. You can attach to the same contained process multiple times simultaneously, screen sharing style, or quickly view the progress of your daemonized process. You can detach from the container (and leave it running) with CTRL-p CTRL-q (for a quiet exit) or CTRL-c which will send a SIGKILL to the container. When you are attached to a container, and exit its main process, the process's exit code will be returned to the client.

In other words, with attach, you will get a shell, and be able to do whatever you need. With exec, you can issue commands that do not require any interaction, but with you use a shell in combination with exec, you will achieve the same result as if you used attach.

### []()Differences between start and run

Start is used to resume the execution of a stopped container. It is not used to start a fresh instance. For that, you have the run command. The choice of words could have been better.

### []()Differences between build and create

The first command is used to create a new image from a Dockerfile. On the other hand, the latter is used to create a new container using command line options and arguments. Create lets you specify container settings, too, like network configurations, resource limitations and other settings, which affect the container from the 

<span class="italic_text">outside</span>

, whereas the changes implemented by the build command will be reflected 

<span class="italic_text">inside</span>

 it, once you start an instance. And by start, I mean run. Get it?

## []()This is just a beginning ...

There are a million more things we can do: using systemd enabled containers, policies, security, resource constraints, proxying, signals, other networking and storage options including the super-critical question of how to mount data volumes inside containers so that data does not get destroyed when containers die, additional pure LXC commands, and more. We've barely scratched the surface. But now, we know what to do. And we'll get there. Slowly but surely.

## []()More reading

I recommend you allocate a few hours and then spend some honest time reading all of the below, in detail. Then practice. This is the only way you will really fully understand and embrace the concepts.

My [entire](http://www.dedoimedo.com/computer_software.html#virtualization) virtualization section

Dockerizing an [SSH](http://docs.docker.com/examples/running_ssh_service/) Deamon Service

Differences [between](http://tuhrig.de/difference-between-save-and-export-in-docker/) save and export in Docker

Docker [Explained](https://www.digitalocean.com/community/tutorials/docker-explained-using-dockerfiles-to-automate-building-of-images): Using Dockerfiles to Automate Building of Images

## []()Conclusion

We're done with this tutorial for today. Hopefully, you've found it useful. In a nutshell, it does explain quite a few things, including how to get started with Docker, how to pull new images, run basic containers, add services like SSH and Apache, commit changes to a file, expose incoming ports, build new images with Dockerfiles, lots of troubleshooting of problems, additional commands, and more. Eventful and colorful, I'd dare say.

In the future, we will expand significantly on what we learned here, and focus on various helper technologies like supervisord for instance, we will learn how to mount filesystems, work on administration and orchestration, and many other cool things. Docker is a very nice concept, and if used correctly, it can make your virtual world easier and more elegant. The initial few steps are rough, but with some luck, this guide will have provided you with the right dose of karma to get happily and confidently underway. Ping me if you have any requests or desires. Technology related, of course. We're done.

P.S. If you like this article, then you'd better give some [love](http://www.dedoimedo.com/donate.html) back to Dedoimedo! Cheers.

[![RSS Feed icon](http://www.dedoimedo.com/images/rss_feed_icon-40x40.png)](http://www.dedoimedo.com/rss_feed.xml)
