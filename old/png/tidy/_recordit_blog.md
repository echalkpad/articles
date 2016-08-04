# Record'IT Blog

[Original URL](http://www.recorditblog.com/post/how-to-deploy-mesos-with-high-availability-on-coreos/)

> Mon, Jan 26, 2015 In a previous post I've explained how to create a web scale infrastructure based on Docker, CoreOS, Vulcand and Mesos. And why Object Storage becomes the de facto data...

<span class="post-date">Mon, Jan 26, 2015</span>

 In a previous post I've explained how to create a web scale infrastructure based on Docker, CoreOS, Vulcand and Mesos. And why Object Storage becomes the de facto data repository.

After this post, I've been asked to provide more details about the way I've implemented Mesos.

I originally implemented Mesos using the Mesos-on-coreos Docker image available at <https://registry.hub.docker.com/u/tnolet/mesos-on-coreos/>

But, the Docker image was based on an old version of Mesos and wasn't providing High Availability. It was only using ETCD at launch time to check if a Mesos Master was already up.

## How to provide High Availability ?

In fact, Mesos provides High Availability by supporting several Masters to run in the same Mesos cluster (one active master and several backups).

More information available at <http://mesos.apache.org/documentation/latest/high-availability/>

Marathon can also be deployed on several nodes in a cluster (<https://mesosphere.github.io/marathon/docs/high-availability.html>)

Both Mesos and Marathon are using a ZooKeeper quorum to perform leader selection

## Dockerfile

To run Mesos and Marathon on CoreOS, I've slightly modified the Dockerfile used for the mesos-on-coreos Docker image:

```
FROM ubuntu:latest

RUN echo "deb http://repos.mesosphere.io/$(lsb_release -is | tr '[:upper:]' '[:lower:]') $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/mesosphere.list

# update repos
RUN sudo apt-get -y update
RUN sudo apt-get -y install curl python-setuptools python-pip python-dev python-protobuf

# install zookeeperd
RUN sudo apt-get -y install zookeeperd

# Install and run Docker
RUN sudo apt-get -y install docker.io
RUN sudo ln -sf /usr/bin/docker.io /usr/local/bin/docker

# install mesos and marathon
RUN sudo apt-get -y install mesos --force-yes
RUN sudo apt-get -y install marathon --force-yes

# Add the bootstrap script
ADD ./mesos_bootstrap.sh /usr/local/bin/mesos_bootstrap.sh

# use the mesos_bootstrap.sh script to start
CMD ["/usr/local/bin/mesos_bootstrap.sh"]
```

Then, I've completely modified the mesos_bootstrap.sh to start a Mesos master, a Mesos slave and a Marathon instance in the same Docker container:

```
#!/bin/bash

# Set locale: this is required by the standard Mesos startup scripts
echo "info: Setting locale to en_US.UTF-8..."
locale-gen en_US.UTF-8 > /dev/null 2>&1

# Start syslog if not started....
echo "info: Starting syslog..."
service rsyslog start > /dev/null 2>&1

function start_zookeeper {
 IFS=,
 i=1
 for server in ${SERVER_LIST}; do
 echo server.$i=$server:2888:3888 >> /etc/zookeeper/conf/zoo.cfg
 (( i += 1 ))
 done
 echo ${ZK_ID} > /etc/zookeeper/conf/myid
 echo "info: Starting Zookeeper..."
 service zookeeper start
 until /usr/share/zookeeper/bin/zkCli.sh ls / > /dev/null 2>&1
 do
 echo "Waiting for the Zookeeper cluster to be ready"
 sleep 5
 done
}

function start_slave {
 echo "info: Mesos slave will try to register with a master using ZooKeeper"
 echo "info: Starting slave..."

 echo /var/lib/mesos > /etc/mesos-slave/work_dir
 echo 'docker,mesos' > /etc/mesos-slave/containerizers
 echo '5mins' > /etc/mesos-slave/executor_registration_timeout
 /usr/bin/mesos-init-wrapper slave > /dev/null 2>&1 &

 # wait for the slave to start
 sleep 1 && while [[ -z $(netstat -lnt | awk "\$6 == \"LISTEN\" && \$4 ~ \".5051\" && \$1 ~ tcp") ]] ; do
 echo "info: Waiting for Mesos slave to come online..."
 sleep 3;
 done
 echo "info: Mesos slave started on port 5051"
}

function start_master {
 echo in_memory > /etc/mesos/registry
 echo zk://localhost:2181/mesos > /etc/mesos/zk

 echo "info: Starting Mesos master..."

 /usr/bin/mesos-init-wrapper master > /dev/null 2>&1 &

 # wait for the master to start
 sleep 1 && while [[ -z $(netstat -lnt | awk "\$6 == \"LISTEN\" && \$4 ~ \".5050\" && \$1 ~ tcp") ]] ; do
 echo "info: Waiting for Mesos master to come online..."
 sleep 3;
 done
 echo "info: Mesos master started on port 5050"
}

function start_marathon {
 rm -f /etc/mesos/zk
 export MARATHON_MASTER=zk://localhost:2181/mesos
 export MARATHON_ZK=zk://localhost:2181/marathon

 if [ ! -d /etc/marathon/conf ]; then
 mkdir -p /etc/marathon/conf
 fi

 echo "http_callback" > /etc/marathon/conf/event_subscriber

 echo "info: Starting Marathon..."

 marathon > /dev/null 2>&1 &

 # wait for marathon to start
 sleep 1 && while [[ -z $(netstat -lnt | awk "\$6 == \"LISTEN\" && \$4 ~ \".8080\" && \$1 ~ tcp") ]] ; do
 echo "info: Waiting for Mesos master to come online..."
 sleep 3;
 done
 echo "info: Marathon started on port 8080"
}

start_zookeeper
start_master
start_slave
start_marathon

wait
```

One of the key feature of this script is to wait for the Zookeeper cluster to be ready before starting any Mesos component.

## How to run the Mesos containers on CoreOS

I'm using a Cloud Config file on each CoreOS node to automatically start the Mesos container:

```
- name: mesos_bootstrap.service
 command: start
 runtime: true
 content: |
 [Unit]
 Description=Mesos Bootstrapper
 After=docker.service
 After=fleet.service
 [Service]
 TimeoutStartSec=900
 EnvironmentFile=/etc/environment
 ExecStartPre=/bin/sh -c "/usr/bin/docker pull djannot/mesos"
 ExecStart=/bin/sh -c "/usr/bin/docker rm -f mesos; /usr/bin/docker run --rm --name mesos --privileged --net=host -p 5050:5050 -p 5051:5051 -p 2888:2888 -p 3888:3888 -p 8080:8080 -v /var/lib/docker/btrfs/subvolumes:/var/lib/docker/btrfs/subvolumes -v /var/run/docker.sock:/var/run/docker.sock -v /sys:/sys -e SERVER_LIST=10.64.231.45,10.64.229.36,10.64.231.25 -e ZK_ID=1 djannot/mesos"
 ExecStop=/usr/bin/docker rm -f mesos
 Restart=always
 RestartSec=10s
```

The _SERVER_LIST_ variable contains the IP addresses of my 3 CoreOS nodes and the _ZK_ID_ is different on each node and must correspond to the IP address of the CoreOS node (1 for 10.64.231.45, 2 for 10.64.229.36 and 3 for 10.64.231.25)

## Conclusion

After starting the unit on each CoreOS node, the Mesos UI can be accessed from any node and if the node is not the current Mesos active master, you will see a warning saying that you will be redirected to the active master in few seconds:

![alt text](http://www.recorditblog.com/images/how-to-deploy-mesos-with-high-availability-on-coreos/mesos1.png "Mesos backup master")

After the redirection, you are now connected to the Mesos active master:

![alt text](http://www.recorditblog.com/images/how-to-deploy-mesos-with-high-availability-on-coreos/mesos2.png "Mesos active master")

Finally, you can access the Marathon UI and API using any Marathon instance, which will proxy the requests to the current leader.

![alt text](http://www.recorditblog.com/images/how-to-deploy-mesos-with-high-availability-on-coreos/marathon.png "Marathon")
