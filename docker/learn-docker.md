# Learn Docker

[Original URL](https://github.com/dwyl/learn-docker)

> Learn how to use docker.io containers to consistently deploy your apps on any infrastructure. What? (Quick Introduction) Try Try the online demo: <https://www.docker.com/tryit/>

[![Docker Logo](http://i.imgur.com/KgdWLdh.png)](https://camo.githubusercontent.com/3482fc32e1f4cad0c44039c8f01e1e270e6894ee/687474703a2f2f692e696d6775722e636f6d2f4b6764574c64682e706e67)

Learn how to use docker.io containers to consistently deploy your apps on any infrastructure.

## [](https://github.com/dwyl/learn-docker#what-quick-introduction)What? (_Quick_ Introduction)

## [](https://github.com/dwyl/learn-docker#try)Try

- Try the online demo: <https://www.docker.com/tryit/>

## [](https://github.com/dwyl/learn-docker#installation)Installation

If your laptop/pc is not running Linux you will need to Download the Docker Toolkit**.pkg** (Mac) or docker-toolkit.**exe** (Windows) and _install_ it on your Mac/PC.

[![docker-toolbox-453-mb](https://cloud.githubusercontent.com/assets/194400/9295494/94ddc882-4467-11e5-9212-d9a506081764.png)](https://cloud.githubusercontent.com/assets/194400/9295494/94ddc882-4467-11e5-9212-d9a506081764.png)

Sadly, Docker Toolkit is the "_official_" way of running docker on Mac so we have to swallow the **453Mb** pill<br>
and get on with it! (_click install_!)

[![docker-toolkit-installation-summary](https://cloud.githubusercontent.com/assets/194400/9295486/5094de68-4467-11e5-9bab-e3a3957388b8.png)](https://cloud.githubusercontent.com/assets/194400/9295486/5094de68-4467-11e5-9bab-e3a3957388b8.png)

### [](https://github.com/dwyl/learn-docker#next-steps) Next Steps

You might as well get a **Docker Hub** account now (reserve your username on dockerhub): <https://hub.docker.com/> Docker Hub lets you store your docker files (public & private) so you can share these with people and deploy them anywhere.

## [](https://github.com/dwyl/learn-docker#open-the-docker-quickstart-terminal-app)Open the Docker _Quickstart_ Terminal App

Open the Docker Quickstart Terminal App

[![use-spotlight-to-open-quickstart-app](https://cloud.githubusercontent.com/assets/194400/9295603/1f01466c-446b-11e5-97aa-71a051bc5301.png)](https://cloud.githubusercontent.com/assets/194400/9295603/1f01466c-446b-11e5-97aa-71a051bc5301.png)

This will open your terminal and run the boot script (_wait a couple of minutes for it to finish_...) [![quickstart](https://cloud.githubusercontent.com/assets/194400/9295558/9b636dea-4469-11e5-9758-d7e8d16de06d.png)](https://cloud.githubusercontent.com/assets/194400/9295558/9b636dea-4469-11e5-9758-d7e8d16de06d.png)

Run the suggested command:

[![docker-configured](https://cloud.githubusercontent.com/assets/194400/9295567/f0e26b4a-4469-11e5-9c0e-1296c306c93c.png)](https://cloud.githubusercontent.com/assets/194400/9295567/f0e26b4a-4469-11e5-9c0e-1296c306c93c.png)

Next try to run an ubuntu container (_only do this on a fast internet connection as its going to download 65mb ubuntu image_)

```
docker run -it ubuntu bash
```

[![download ubuntu](https://cloud.githubusercontent.com/assets/194400/9295584/8137df40-446a-11e5-9b4c-b6af7b59c0b7.png)](https://cloud.githubusercontent.com/assets/194400/9295584/8137df40-446a-11e5-9b4c-b6af7b59c0b7.png)

This will boot the Ubuntu image and log you into the container's bash. You can confirm that you are in a Linux bash by running the following command: `cat /etc/os-release`

[![confirm-running-ubuntu](https://cloud.githubusercontent.com/assets/194400/9295641/202ed328-446c-11e5-8c19-15b8a2ae2b28.png)](https://cloud.githubusercontent.com/assets/194400/9295641/202ed328-446c-11e5-8c19-15b8a2ae2b28.png)

List the containers currently running:

Which shows:

```
NAME ACTIVE DRIVER STATE URL SWARM
default virtualbox Running tcp://192.168.99.100:2376
```

And for even more detail, run `docker info`

```
Containers: 2
Images: 6
Storage Driver: aufs
 Root Dir: /mnt/sda1/var/lib/docker/aufs
 Backing Filesystem: extfs
 Dirs: 10
 Dirperm1 Supported: true
Execution Driver: native-0.2
Logging Driver: json-file
Kernel Version: 4.0.9-boot2docker
Operating System: Boot2Docker 1.8.1 (TCL 6.3); master : 7f12e95 - Thu Aug 13 03:24:56 UTC 2015
CPUs: 1
Total Memory: 1.956 GiB
Name: default
ID: VIPW:LYBS:LT7I:KWTS:DU5U:W2NG:MWMH:EQNS:PFTF:4ZLN:STYL:4HG2
Debug mode (server): true
File Descriptors: 9
Goroutines: 20
System Time: 2015-08-16T22:27:21.593654583Z
EventsListeners: 0
Init SHA1:
Init Path: /usr/local/bin/docker
Docker Root Dir: /mnt/sda1/var/lib/docker
Labels:
 provider=virtualbox
```

### [](https://github.com/dwyl/learn-docker#run-the-dockerfile-in-this-repo)Run the `Dockerfile` in this repo

First build the image:

```
docker build -t learn-docker .
```

Then run the image as a container:

```
docker run -it -p 8888:8888 learn-docker bash
```

> <https://docs.docker.com/reference/run/>

Exec

```
docker exec -it learn-docker bash
```

### [](https://github.com/dwyl/learn-docker#list-locally-available-images)List (_Locally Available_) Images

View the current (_locally_) available docker images:

You should see:

```
REPOSITORY TAG IMAGE ID CREATED VIRTUAL SIZE
learn-docker latest 0c495aa0e6dc Less than a second ago 261.6 MB
ubuntu 14.04 8251da35e7a7 8 days ago 188.4 MB
ubuntu latest 8251da35e7a7 8 days ago 188.4 MB
hello-world latest af340544ed62 9 days ago 960 B
```

### [](https://github.com/dwyl/learn-docker#docker-ps)`docker ps`

To list all the running docker containers, use:

> <https://docs.docker.com/reference/commandline/ps/>

### [](https://github.com/dwyl/learn-docker#more-info-on-a-specific-container)More info on a specific container

To get more detail on a container type: `docker inspect {imageid}`

e.g: in our case the container we are interested in has the id: **a205fc3a096f** so we run:

```
docker inspect a205fc3a096f
```

The complete output of this command is in: [/**sample-docker-inspect.txt**](https://github.com/dwyl/learn-docker/blob/e8769347940dc13d8197742b9e232e3efd85ca8d/sample-docker-inspect.txt)

#### [](https://github.com/dwyl/learn-docker#just-the-ip-address)Just the IP address

If all we want is the container's IP address we run:<br>
`docker inspect --format '{{ .NetworkSettings.IPAddress }}' a205fc3a096f`<br>
where the last argument is the container (image) id.<br>
Courtesy of: <http://stackoverflow.com/a/20686101/1148249>

### [](https://github.com/dwyl/learn-docker#kill-a-container-by-id)Kill a Container by ID

> <https://docs.docker.com/reference/commandline/stop/>

## [](https://github.com/dwyl/learn-docker#help)Help!

For some reason I'm unable to access the **nginx** server running inside the docker container ... looked at:

### [](https://github.com/dwyl/learn-docker#is-nginx-working)is nginx working?

Test that your nginx configuration is valid (doen't have errors)

expect the following output:

```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

## [](https://github.com/dwyl/learn-docker#useful-links)Useful Links

### [](https://github.com/dwyl/learn-docker#cheat-sheet)Cheat Sheet

### [](https://github.com/dwyl/learn-docker#videos)Videos

### [](https://github.com/dwyl/learn-docker#tutorials)Tutorials
