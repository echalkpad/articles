# How to Create Remote VM using docker-machine Generic SSH Driver

[Original URL](http://www.thegeekstuff.com/2016/02/docker-machine-create-generic/)

> Using docker, you can package your enterprise application into one self-contained container, which is guaranteed to run on any environment. This makes it easy for both developer and sysadmins to...

![Docker](http://static.thegeekstuff.com/wp-content/uploads/2016/02/docker-logo.png)Using docker, you can package your enterprise application into one self-contained container, which is guaranteed to run on any environment.

This makes it easy for both developer and sysadmins to manage the application. If you are an administrator, you don't have to hear this excuse from developer anymore: "But, it works on my dev environment!"

Using Docker Machine, you can install and configure Docker Engine on a remote server. Once docker is installed, from your local machine (laptop), you can remotely manage the docker.

In this tutorial, we'll specifically talk about the Generic driver used by docker-machine, which will use SSH client to manage the remote docker host.

## 1\. Create dockeradmin user on Remote Host

First, create a Linux user account on the remote server. You don't really have to use "dockeradmin" as the username, it can be anything. You can even use your own user name.

```
adduser dockeradmin

passwd dockeradmin
```

## 2\. Generate SSH Public-Private Key Pair on Local Host

Next, on your local host (laptop), create a public and private SSH key using ssh-keygen. You should also copy the private key to the remote server.

We explained how to do this in this article: [Perform Remote SSH Login Without Password](http://www.thegeekstuff.com/2008/11/3-steps-to-perform-ssh-login-without-password-using-ssh-keygen-ssh-copy-id/)

But, in-short, here is what you need to do for this:

```
ssh-keygen

ssh-copy-id dockeradmin@remote-server
```

Note: During ssh-keygen, don't give any passphrase. Leave it empty.

## 3\. Allow Sudo Access for dockeradmin

On your remote server, in the /etc/sudoers file, add the following line, which will give full sudo privilege to dockeradmin account. This means that dockeradmin account can run any root command.

```
# visudo
dockeradmin ALL=(ALL) NOPASSWD: ALL
```

Note: visudo command will modify the /ec/sudoers file. You don't have to use "vi /etc/sudoers".

## 4\. Allow External Access on Remote Server

Make sure your remote server can talk to the internet. This is required, as the docker-machine command will invoke several yum commands to install certain require packages.

So, just for testing purpose, make sure the following works on your server.

```
ping google.com
```

If you don't have appropriate nameserver setup, modify your /etc/resolve.conf and add the following line. Or, use one of the nameservers provided by your system administrator.

```
# vi /etc/resolv.conf
nameserver 4.2.2.2
```

## 5\. docker-machine Generic Driver Command Syntax

When you are using the Generic driver, the following options are available in docker-machine create command.

The following docker-machine syntax shows all the mandatory options:

```
docker-machine create -d generic --generic-ip-address {ip-address} {docker-vm-name}
```

In the above syntax:

- docker-machine is the command
- create option tells docker-machine to create the remote docker-vm host
- -d option is to specify what type of driver to use. Here, we are using the "generic" driver, which will use the SSH client to connect to the remote host
- –generic-ip-address is to specify the remote ip-address where the docker-vm host should be created.
- docker-vm-name is the name that you want to give to the remote docker-vm that will be created by the docker-machine command. This can be anything.

The following docker-machine syntax shows all available options:

```
docker-machine create -d generic --generic-ip-address {ip-address} --generic-ssh-key {private-key} --generic-ssh-user {username} --generic-ssh-port {ssh-port} {docker-vm-name}
```

In the above syntax:

- –generic-ssh-key This is optional. When you don't specify this, it will talk to the ssh-agent running on your local system. If you are not running ssh-agent on your local system (laptop), then use this option, and specify the location of your private key on your local system.
- –generic-ssh-user This is optional. By default, it will use root. But, in most situations you might not want to do this. So, specify the username that you want docker-machine to use while connecting to the remote host.
- –generic-ssh-port This is the SSH port of the remote server. By default this is 22.
- Other options are explained in the above syntax

## 6\. Create the Remote Docker VM Host

Now, it is time to create the remote docker-vm host using the docker-machine create command.

```
docker-machine create -d generic --generic-ip-address 192.168.100.2 --generic-ssh-key $HOME/.ssh/id_rsa --generic-ssh-user dockeradmin --generic-ssh-port 22 dev-db
```

The following is the same command as above, but I broke it down into multiple line for easy readability.

```
docker-machine create -d generic \
 --generic-ip-address 192.168.100.2 \ 
 --generic-ssh-key $HOME/.ssh/id_rsa \
 --generic-ssh-user dockeradmin \
 --generic-ssh-port 22 \
 dev-db
```

In the above command:

- We've explained all the options in the above syntax section.
- 192.168.100.2 is the ip-address of our remote host where the docker-vm host should be created.
- $HOME/.ssh/id_rsa is the private key that is located on your local system (laptop)
- dockeradmin is the name of the Linux username on the remote host
- dev-db is the name that I gave for the docker-vm host that I want to create on the remote host. This can be anything.

Note: Again, you'll be executing the above docker-machine command on your local system. You can use the docker-machine command that came with your Docker Toolbox.

## 7\. Successful docker-machine create Output

If everything worked properly, you'll see the following output from the docker-machine create command.

```
$ docker-machine create -d generic --generic-ip-address 192.168.100.2 --generic-ssh-key $HOME/.ssh/id_rsa --generic-ssh-user dockeradmin --generic-ssh-port 22 dev-db
Running pre-create checks...
Creating machine...
(dev-db) Importing SSH key...
Waiting for machine to be running, this may take a few minutes...
Machine is running, waiting for SSH to be available...
Detecting operating system of created instance...
Detecting the provisioner...
Provisioning with centos...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect Docker to this machine, run: C:\Program Files\Docker Toolbox\docker-machine.exe env dev-db
```

Finally, if everything is working properly, you'll see the remote docker vm host that we created. On your local system, execute the following:

```
$ docker-machine ls
NAME ACTIVE DRIVER STATE URL SWARM DOCKER ERRORS
default articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii virtualbox Running tcp://192.168.100.10:2376
dev-db - generic Running tcp://192.168.100.11:2376
```

Note: Well, for most part, nothing goes as planned, and you might get some error message. The following are some typical issues that I faced and the solution for those.

## 8\. Error 1: Invalid OS

The current version of docker-machine supports only certain version of OS on the remote server.

For example, if your remote server is running on CentOS 6, you'll get the following " /etc/os-release: No such file or directory" error message. This is because for CentOS, it is looking for CentOS 7.

```
$ docker-machine create -d generic --generic-ip-address 192.168.100.2 --generic-ssh-key $HOME/.ssh/id_rsa --generic-ssh-user dockeradmin --generic-ssh-port 22 dev-db
Running pre-create checks...
Creating machine...
Waiting for machine to be running, this may take a few minutes...
(dev-db) Importing SSH key...
Machine is running, waiting for SSH to be available...
Detecting operating system of created instance...
Detecting the provisioner...
Error creating machine: Error detecting OS: Error getting SSH command: Something
 went wrong running an SSH command!
command : cat /etc/os-release
err : exit status 1
output : cat: /etc/os-release: No such file or directory
```

## 9\. Error 2: Unable to Execute root Command (No Sudo Access)

If you are getting the "Error creating machine: Error running provisioning: exit status 1″ error message, after the "Provisioning with centos..." line, then it is not the connection issue. docker-machine was able to connect to the remote server properly.

But, in this case, it is unable to execute root command as dockeradmin user. So, use visudo and allow full sudo access to the dockeradmin user as we explained in one of the previous steps.

```
$ docker-machine create -d generic --generic-ip-address 192.168.100.2 --generic-ssh-key $HOME/.ssh/id_rsa --generic-ssh-user dockeradmin --generic-ssh-port 22 dev-db
Running pre-create checks...
Creating machine...
(dev-db) Importing SSH key...
Waiting for machine to be running, this may take a few minutes...
Machine is running, waiting for SSH to be available...
Detecting operating system of created instance...
Detecting the provisioner...
Provisioning with centos...
Error creating machine: Error running provisioning: exit status 1
```

## 10\. Error 3: netstat command is Missing

If you are getting the "Error running SSH command: exit status 127″ error message after the "Setting Docker configuration on the remote daemon..." line, this is also not a connection issue.

In this case, docker-machine connected to the remote SSH, and it has appropriate sudo access to execute root command, but some of the command it is trying failed, because it is missing "netstat" command.

```
$ docker-machine create -d generic --generic-ip-address 192.168.100.2 --generic-ssh-key $HOME/.ssh/id_rsa --generic-ssh-user dockeradmin --generic-ssh-port 22 dev-db
Running pre-create checks...
Creating machine...
(dev-db) Importing SSH key...
Waiting for machine to be running, this may take a few minutes...
Machine is running, waiting for SSH to be available...
Detecting operating system of created instance...
Detecting the provisioner...
Provisioning with centos...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Error running SSH command: exit status 127
Error running SSH command: exit status 127
...
Error running SSH command: exit status 127
Error creating machine: Error running provisioning: Unable to verify the Docker
daemon is listening: Maximum number of retries (10) exceeded
```

To fix this problem, install the net-tools package on the remote server, which contains the "netstat" command.

```
yum install net-tools
```

I'm hoping in the next version of docker-machine, they'll probably check whether "netstat" exists on the remote server or not, at the beginning and give appropriate valid error message.

## 11\. Error 4: Docker Client Version Mis-match

If you are getting the following "Error creating machine: Error running provisioning: exit status 1″ error message, there is a possibility that the docker client on your local system (laptop) is not matching with the docker client on the remote system.

```
$ docker-machine create -d generic --generic-ip-address 192.168.100.2 --generic-ssh-key $HOME/.ssh/id_rsa --generic-ssh-user dockeradmin --generic-ssh-port 22 dev-db
Running pre-create checks...
Creating machine...
(dev-db) Importing SSH key...
Waiting for machine to be running, this may take a few minutes...
Machine is running, waiting for SSH to be available...
Detecting operating system of created instance...
Detecting the provisioner...
Provisioning with centos...
Error creating machine: Error running provisioning: exit status 1
```

For example, on local system:

```
$ docker version
Client:
 Version: 1.9.1
```

On remote server:

```
# docker version
Client:
 Version: 1.8.2
```

In the above example, local docker version is 1.9.1, but the remote docker version is an older 1.8.2\. So, here we have a mis-match, and docker-machine create will not work and fail as shown above.

In the above scenario, the docker engine was installed separately on the remote server, which had a different docker client version than the docker client version that came with the docker-machine (docker toolbox) on the local system.

So, in this case, remove the docker from your remote system as shown below.

```
# rpm -qa | grep docker
docker-selinux-1.8.2-10.el7.centos.x86_64
docker-1.8.2-10.el7.centos.x86_64

# rpm -ev docker-selinux
Preparing packages...
docker-selinux-1.8.2-10.el7.centos.x86_64

# rpm -ev docker
Preparing packages...
docker-1.8.2-10.el7.centos.x86_64
```

Now, when you execute the docker-machine command on your local system, it will automatically install the appropriate docker engine and the docker client on the remote server that will be compataible with the docker client on the local machine.

## 12\. Error 5: SELinux or Firewall Blocking Access

By default, the new docker vm machine that we created will be running on TCP port 2376.

On your remote server, make sure you have appropriate firewall rule which will allow TCP port 2376\. If you still have problem, just temporarily to rule-out the possibility of firewalld blocking access, stop firewalld and see what happens.

```
systemctl stop firewalld
```

Again, just for debugging purpose, for some reason, if you like to disable the firewalld, you can do it as shown below:

```
systemctl disable firewalld
```

The latest version of Docker works beautifully with SELinux, no issues there. But, in an earlier version of docker, there were some bugs related to docker and SELinux.

So, if you are facing any issues, try [disabling SELinux temporarily](http://www.thegeekstuff.com/2009/06/how-to-disable-selinux-redhat-fedora-debian-unix/) and see whether it fixes the issue. To permanently disable SELinux, modify the /etc/selinux/config file accordingly.

```
setenforce Permissive

getenforce
```

Also, note that if SSH is not able to make connection from your local system to the remote server, docker-machine will display the following "Error waiting for SSH: Too many retries waiting for SSH" error message. To fix this, make sure, you implement the SSH password less authentication as explained in one of the above steps.

```
$ docker-machine create -d generic --generic-ip-address 192.168.100.2 --generic-ssh-key $HOME/.ssh/id_rsa --generic-ssh-user dockeradmin --generic-ssh-port 22 dev-db
Running pre-create checks...
Creating machine...
Waiting for machine to be running, this may take a few minutes...
Machine is running, waiting for SSH to be available...
Error creating machine: Error waiting for SSH: Too many retries waiting for SSH
to be available. Last error: Maximum number of retries (60) exceeded
```

When all of the above issues are fixed, docker-machine will work without any issues as shown below:

```
$ docker-machine create -d generic --generic-ip-address 192.168.100.2 --generic-ssh-key $HOME/.ssh/id_rsa --generic-ssh-user dockeradmin --generic-ssh-port 22 dev-db

$ docker-machine ls
NAME ACTIVE DRIVER STATE URL SWARM DOCKER ERRORS
default articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii virtualbox Running tcp://192.168.100.10:2376
dev-db - generic Running tcp://192.168.100.11:2376
```

## If you enjoyed this article, you might also like..

[![Bash 101 Hacks Book](http://static.thegeekstuff.com/images/bash-132.png "Bash 101 Hacks Book")](http://www.thegeekstuff.com/bash-101-hacks-ebook/) | [![Sed and Awk 101 Hacks Book](http://static.thegeekstuff.com/images/sed-and-awk-132.png "Sed and Awk 101 Hacks Book")](http://www.thegeekstuff.com/sed-awk-101-hacks-ebook/) | [![Nagios Core 3 Book](http://static.thegeekstuff.com/images/nagios-core-132.png "Nagios Core 3 Book")](http://www.thegeekstuff.com/nagios-core-ebook/) | [![Vim 101 Hacks Book](http://static.thegeekstuff.com/images/vim-101-hacks-132.png "Vim 101 Hacks Book")](http://www.thegeekstuff.com/vim-101-hacks-ebook/)
----------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- | -----------------------------------------------------------------------------------------------------------------------------------------------------------
