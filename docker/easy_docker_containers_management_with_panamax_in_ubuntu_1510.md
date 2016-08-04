# Easy Docker Containers Management with Panamax in Ubuntu 15.10

[Original URL](http://linoxide.com/ubuntu-how-to/docker-containers-management-panamax-ubuntu-15-10/)

> Panamax is an open source project making deployment and sharing of complex containerized apps as easy as Drag-and-Drop with its beautiful easy to use user interface. Panamax has a web based interface...

Panamax is an open source project making deployment and sharing of complex containerized apps as easy as Drag-and-Drop with its beautiful easy to use user interface. Panamax has a web based interface which runs in almost every web browser. It is a completely free and open source software licensed under Apache 2 License and is committed to building a strong open source community. Running complex multi containers, multi servers with container is hard for a newbie but [Panamax](http://panamax.io/) makes it pretty simple and quick for everyone. It can run from our home computer to any large scaled cloud infrastructures that supports CoreOS.

Here are some pretty easy steps on how we can install Panamax in our machine running Ubuntu 15.10.

## 1\. Updating Local Repository Index

First of all, we'll gonna update Ubuntu's local repository index so that we get the latest updates and packages available in the repository of Ubuntu. To do so, we'll need to run the following command under sudo or root mode.

> # apt-get update

## 2\. Installing Oracle Virtual Box

Currently, Panamax requires CoreOS to run as it is highly tuned for docker and contains fleet so, we'll need to run a VM running CoreOS. So, we'll need to install Virtual Box which will be managed by Vagrant. To install Oracle Virtual Box in our machine running Ubuntu 15.10, we'll need to run the following apt-get command.

> # apt-get install virtualbox

[![Installing Virtual Box](http://blog.linoxide.com/wp-content/uploads/2016/02/installing-virtual-box.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/installing-virtual-box.png)

## 3\. Installing Vagrant

As Panamax need to run a VM named panamax-vm in a virtualbox, we'll gonna use Vagrant to manage virtualbox. Panamax requires Vagrant greater than version 1.6\. So, we'll gonna install the latest release of Vagrant from its official download page. From the download page, we'll gonna download 64 bit version of Debian release. Here, we'll gonna download it using the link with wget as shown below.

> # cd /tmp/<br>
> # wget <https://releases.hashicorp.com/vagrant/1.8.1/vagrant\_1.8.1\_x86\_64.deb>

> –2016-02-20 16:38:25– <https://releases.hashicorp.com/vagrant/1.8.1/vagrant\_1.8.1\_x86\_64.deb><br>
> Resolving releases.hashicorp.com (releases.hashicorp.com)... 43.249.75.69<br>
> Connecting to releases.hashicorp.com (releases.hashicorp.com)|43.249.75.69|:443... connected.<br>
> HTTP request sent, awaiting response... 200 OK<br>
> Length: 75496368 (72M) [application/x-debian-package]<br>
> Saving to: 'vagrant_1.8.1_x86_64.deb'<br>
> vagrant_1.8.1_x86_6 100%[=====================>] 72.00M 107MB/s in 0.7s<br>
> 2016-02-20 16:38:26 (107 MB/s) - 'vagrant_1.8.1_x86_64.deb' saved [75496368/75496368]

Once done, we'll gonna install it using dpkg package manager.

> # dpkg -i vagrant_1.8.1_x86_64.deb

> Selecting previously unselected package vagrant.<br>
> (Reading database ... 176105 files and directories currently installed.)<br>
> Preparing to unpack vagrant_1.8.1_x86_64.deb ...<br>
> Unpacking vagrant (1:1.8.1) ...<br>
> Setting up vagrant (1:1.8.1) ...

## 4\. Installing Panamax

Finally, once the requirements of Panamax ie Virtual Box and Vagrant has been installed successfully, we'll gonna install Panamax in our machine. To do so, we'll need to run the following curl command.

> # curl <http://download.panamax.io/installer/ubuntu.sh> | bash

[![Installing Panamax](http://blog.linoxide.com/wp-content/uploads/2016/02/installing-panamax.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/installing-panamax.png)

On executing the above command, Panamax is installed automatically with the default configuration which will run panamax-vm with 2 CPUs and 1GB of RAM. But, if we need to increase the resource, we'll need to execute the following command.

> # panamax

[![Panamax Manual Install](http://blog.linoxide.com/wp-content/uploads/2016/02/panamax-manual-install.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/panamax-manual-install.png)

This will run the installer which will show us a menu where we'll be asked to select the options by entering the required numbers. As we're going for installing Panamax, we'll gonna input 1 and press enter to continue.

Then, the installer will begin to search if the required version of Virtual Box and Vagrant is installed or not. As we have already fulfilled its need, the installer will go ahead and ask us to allocate memory for it. As we wanna increase the memory resource for our panamax-vm, we'll go with the 2048 MB of memory but if you require to run heavy containers, please allocate according to your requirements.

[![Panamax Manual Setup](http://blog.linoxide.com/wp-content/uploads/2016/02/panamax-manual-setup.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/panamax-manual-setup.png)

Then, it will ask us to allocate the number of CPUs to be used for running Panamx VM, default port for running the Panamax UI and API. Here, we'll press enter and go with the default setting. Then, it will ask us to select the version of Panamax to be installed between developer and stable. If you are a developer, wanna enable more developer options and wanna play with it, you'll need to enter dev in the setting. But as we're going for a stable version of Panamax, we'll simply hit enter and go with the default option.

Once done, the installer will start downloading the CoreOS image file required to run in our system as a Virtual Machine where Panamax will be setup automatically. The downloaded CoreOS image will be deployed in a Virtual Box managed by Vagrant using the installer.

[![Downloading CoreOS Vagrant Box](http://blog.linoxide.com/wp-content/uploads/2016/02/downloading-coreos-vagrant-box.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/downloading-coreos-vagrant-box.png)

Once the image starts to run, the script will automatically pull the Panamax docker container and start it as we had configured in the setting.

[![Panamax Installed Successfully](http://blog.linoxide.com/wp-content/uploads/2016/02/panamax-installed-successfully.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/panamax-installed-successfully.png)

Once done, we'll be able to run Panamax web based interface using our web browser. To do so, we'll need to point our web browser to our machine running Panamax with port 3000 as <http://localhost:3000> .

![Panamax Homepage](http://blog.linoxide.com/wp-content/uploads/2016/02/panamax-homepage.png)

## 5\. Deploying a Container

As we have said before, it is pretty easy to deploy a container with Panamax. We'll simply need to search for our required image to run container in.

[![Running Templates](http://blog.linoxide.com/wp-content/uploads/2016/02/running-templates.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/running-templates.png)

Then, click on the Run Template and select our method to install. This will pull the image, create container and run it for us automatically with its backend processes. Then, we can simply access our container. Likewise, we can easily manage our container including stopping, restarting and deleting our containers. We have Panamax Dashboard on <http://localhost:3000/dashboard> as shown below through which we can manage our applications, source, images, registry as well as remote deployment targets.

[![Panamax Dashboard](http://blog.linoxide.com/wp-content/uploads/2016/02/panamax-dashboard.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/panamax-dashboard.png)

## 6\. Managing Panamax

Installing and deploying container in with Panamax is really easy and fast. Likewise, its pretty easy to manage panamax too. Here are some operations (like stopping, restarting, upgrading, etc) we can perform with Panamax with its easy to run commands.

### Stopping

The Panamax CoreOS VM and all its running apps inside CoreOS will also stop whereas the state of the VM will remain as it is. So, in order to stop the Panamax VM, we'll need to execute the following command.

> # panamax pause

### Running

If our Panamax VM is in pause state or is not running and we wanna run the CoreOS VM and wanna start the Panamax in it then, we'll need to execute the following command. This will also restart the containers we've set to start automatically.

> # panamax up

### Restarting

Restarting Panamax is same as stopping and starting it in which the state of apps is maintained. The database will be rehydrated with latest templates when it is restarted. To do so, we'll need to execute the following command.

> # panamax restart

### Displaying Info

In order to display the Panamax UI, API and Installer Versions, we'll need to run the following command.

> # panamax info

### Upgrading

Before we go for upgrade, we'll need to check the latest available version of Panamax in the internet. To do so, we'll need to run the following command.

> # panamax check

Once we see that we have a new version available, we'll need to execute the following command to upgrade our Panamax to the latest release. Upgrading Panamax to the latest version will drop and recreate Panamax Containers. Database volume won't be touched, so all settings in database remains intact (apps/git, config, etc are preserved). Database is rehydrated with latest templates when Panamax is reinstalled.

> # panamax download –cpu=2 –memory=1024

### SSH

In order to ssh into the Panamax CoreOS VM, we'll need to run the following command.

> # panamax ssh

### Uninstalling

If we're done with Panamax and wanna uninstall it completely which will delete the CoreOS VM and the associated Vagrant Box then, we'll need to execute the following command.

> # panamax delete

## Conclusion

Finally, we have successfully learned how we can install Panamax and manage docker containers with it. Really, docker container management has been made pretty easy with it. Panamax claims to be the docker management tool built for humans. Currently, it is under BETA and is supported on Mac OS X and Ubuntu hosts. And currently it only runs inside a CoreOS VM whereas it will be able to run in other distributions soon. If you have any questions, suggestions, feedback please write them in the comment box below so that we can improve or update our contents. Thank you ! Enjoy :-)

**Category**: [UBUNTU HOWTO](http://linoxide.com/category/ubuntu-how-to/)
