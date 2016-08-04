# How To Set Up a Deis Cluster on DigitalOcean

[Original URL](https://deis.com/blog/2016/deis-cluster-digitalocean)

> In a previous post, we used Rigger to deploy Deis on DigitalOcean. In this post, we take a different and more detailed approach. Introduction Deis is an open source private Platform as a Service...

In a [previous post](https://deis.com/blog/2016/ramping-up-rigger-deploying-deis-digitalocean), we used [Rigger](https://github.com/deis/rigger) to deploy Deis on [DigitalOcean](https://www.digitalocean.com/). In this post, we take a different and more detailed approach.

## Introduction

Deis is an open source private Platform as a Service (PaaS) that simplifies deploying and managing your applications on your own servers. By leveraging technologies such as Docker and CoreOS, Deis provides a workflow and scaling features that are similar to that of Heroku, on the hosting provider of your choice. Deis supports applications that can run in a Docker container, and can run on any platform that supports CoreOS.

Read on to learn how to set up your own 3-machine Deis cluster on DigitalOcean.

**Note:** This tutorial is based on the v1.9.0 release of Deis.

[]()

## Prerequisites

To complete this guide you must have the following:

- The ability to create at least **3 Droplets** that have at least **4GB memory** and **40GB disk space**. These will be the CoreOS machines that the Deis platform will be installed on
- SSH Key(s) to add to the droplets, which is used for authentication when using the `deisctl` tool
- SSH Key to authenticate against Deis' builder
- A domain to point to the cluster machines with–this tutorial will use `example.com` as its domain. An alternative is to use [xip](http://xip.io)

This tutorial is based heavily on [How To Set Up a CoreOS Cluster on DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-coreos-cluster-on-digitalocean). Review it if you have trouble following the steps in this tutorial related to creating a CoreOS cluster.

We will also install the [Deis Control Utility](https://github.com/deis/deis/tree/master/deisctl#installation), `deisctl`, and the [Deis client](https://github.com/deis/deis), `deis`, on a local Mac OS X computer but you may install them elsewhere, such as an Ubuntu 14.04 Droplet, if you wish. The private SSH key that corresponds with the public SSH key used to create the CoreOS Droplets must exist on the computer where `deisctl` is installed.

## Generate a New Discovery URL

To create the CoreOS cluster that we will install the Deis platform on, we first need to generate a new discovery URL, a unique address that stores peer CoreOS addresses and metadata. The easiest way to do this is to use `https://discovery.etcd.io`, a free discovery service. A new discovery URL by visiting <https://discovery.etcd.io/new?size=3> in a web browser or by running the following `curl` command:

```
curl -w "\n" "https://discovery.etcd.io/new?size=3"
```

Either method will return a unique and fresh discovery URL that looks something like the following (the highlighted part will be a unique token):

```
https://discovery.etcd.io/f8d48be35b794da45e249bb149729a27
```

You will use your resulting discovery URL to create your new Deis platform cluster. The same discovery URL must be specified in the `etcd` section of the cloud-config of each CoreOS server that you want to add to a particular Deis platform cluster.

Now that we have a discovery URL, let's look at adding it to the `cloud-config` that we will use to create each machine of our Deis cluster.

## Deis User Data

To create a cluster of CoreOS machines that will be used in our Deis cluster, we will use a special `cloud-config` file that is supplied by the maintainers of Deis. This file [is available](https://raw.githubusercontent.com/deis/deis/master/contrib/coreos/user-data.example) in the Deis GitHub repository.

Open the supplied user data in a text editor, and find the line where the _discovery URL_ is defined. It will look like this:

```
 --discovery #DISCOVERY_URL
```

Uncomment it, and replace highlighted `#DISCOVERY_URL` with the unique discovery URL that you generated in the previous step.

This tutorial is based on Deis 1.9.0, but the version specified in the sample user data could be more current. Look for the following line (the highlighted numbers represent the version):

```
ExecStart=/usr/bin/sh -c 'curl -sSL --retry 5 --retry-delay 2 http://deis.io/deisctl/install.sh | sh -s 1.9.0'
```

Take note of the version number. Be sure to use the version in the user data when installing `deisctl` and `deis` in the following steps.

You may save this user data file somewhere if you wish.

## Create CoreOS Droplets

Now that we have the _user data_ that we will use, let's create the CoreOS machines now. If you are unfamiliar with how to provide a `cloud-config` file via _user data_ during Droplet creation, please refer to the [How to Provide Cloud-Config](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-coreos-cluster-on-digitalocean#how-to-provide-cloud-config-on-digitalocean) section of our CoreOS clustering tutorial.

Create at least three Droplets, **in the same region**, with the following specifications:

- At least **4GB memory** and **40GB disk space**
- **Private Networking** enabled, if available. If private networking is not available in your selected region, edit the user data and replace every occurrence of `$private_ipv4` with `$public_ipv4`
- **Enable User Data** and enter the user data from the previous step
- Select **CoreOS Stable channel** as the Linux distribution
- Select your SSH key from the list

If you would prefer to use the `docl` convenience tool to provision your Droplets, follow the instructions in the [docs](http://docs.deis.io/en/latest/installing_deis/digitalocean/).

Once you have created at least three of these machines, you should have a CoreOS cluster that is ready for the Deis platform installation. Let's move onto setting up the DNS.

## Configure DNS

Deis requires a wildcard DNS record to function properly. If the top-level domain (TLD) that you are using is `example.com`, your applications will exist at the `*.example.com` level. For example, an application called "app" would be accessible via `app.example.com`.

One way to configure this on DigitalOcean is to setup round-robin DNS via the [DNS control panel](https://cloud.digitalocean.com/domains). To do this, add the following records to your domain:

- A wildcard CNAME record at your top-level domain, i.e. a CNAME record with `*` as the name, and `@` as the canonical hostname
- For each CoreOS machine created, an A-record that points to the TLD, i.e. an A-record named `@`, with the Droplet's public IP address

The zone file will now have the following entries in it: (your IP addresses will be different)

```
DNS Records 1 of 2* CNAME @
@ IN A 104.131.93.162
@ IN A 104.131.47.125
@ IN A 104.131.113.138
```

In this example, the Deis router/controller will be reachable via `deis.example.com`.

For convenience, you can also set up DNS records for each node:

```
DNS Records 2 of 2
deis-1 IN A 104.131.93.162
deis-2 IN A 104.131.47.125
deis-3 IN A 104.131.113.138
```

If you need help using the DNS control panel, check out [this tutorial](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-host-name-with-digitalocean).

### Using xip.io

If you do not want to set up your own domain, you may use `xip.io` to provide your wildcard DNS. Basically, this will allow you to use your Droplet's IP address appended by ".xip.io" as a wildcard domain that resolves to the IP address.

After you install the Deis platform on your cluster, determine which CoreOS machine is running the `deis-router@1.service` unit with `deisctl list`--we will install `deisctl` in the next section. Then determine the public IP address of that machine. If it is `104.131.47.125`, you may use the following domain to reach the Deis router:

```
xip.io exampledeis.104.131.47.125.xip.io
```

## Apply Security Group Settings

DigitalOcean Droplets do not have firewalls enabled by default, so we should add some `iptables` rules to ensure that our components are not accessible to outsiders. The Deis repository provides a script, which can be found [here](https://github.com/deis/deis/blob/master/contrib/util/custom-firewall.sh) that can do just that.

```
curl -O https://raw.githubusercontent.com/deis/deis/master/contrib/util/custom-firewall.sh
```

After reviewing the contents of the script, execute it on each of your servers. For example, using the DNS entries we created earlier, we would run these commands:

```
ssh core@deis-1.example.com 'bash -s' deis-2.example.com 'bash -s' deis-3.example.com 'bash -s' 

Be sure to run the script on all of your servers.

Install Deis Tools

Now that we have the CoreOS cluster set up, we will install the Deis Control Utility and Client. You should install these tools on the computer that you want to control your Deis cluster from. We will demonstrate installing them on a separate Ubuntu 14.04 Droplet, but you can install them anywhere you wish.

Install Deis Control Utility

The Deis Control Utility allows you to interact with the Deis machines, and install the Deis platform on the cluster.

Change to the directory where you would like to install the deisctl binary. Then, install the Deis Control Utility by downloading and running the install script with the following command (replace the version with the number that was found in the user data):


/bin/bash -c 'curl -sSL http://deis.io/deisctl/install.sh | sh -s 1.9.0'
```

This installs `deisctl` to the current directory, and refreshes the Deis unit files.

Let's link it to `/usr/local/bin`, so it will be in our `PATH`:

```
sudo ln -fs $(pwd)/deisctl /usr/local/bin/deisctl
```

Now you may use the `deisctl` command.

### Install Deis Client

The Deis client, also known as the Deis command-line interface, allows you to interact with the Deis controller unit.

Change to the directory where you would like to install the `deis` binary. Install the Deis client by downloading and running the install script with the following command (replace the version with the number that was found in the user data):

```
/bin/bash -c 'curl -sSL http://deis.io/deis-cli/install.sh | sh -s 1.9.0'
```

This installs `deis`, which is the client to the current directory. Let's link it to `/usr/local/bin`, so it will be in our `PATH`:

```
sudo ln -fs $(pwd)/deis /usr/local/bin/deis
```

The Deis client is installed. Now you may use the `deis` command.

## Provision Deis Platform

From the computer you installed the Deis tools on, we will provision the Deis platform.

Ensure your SSH agent is running (and select the private key that corresponds to the SSH keys added to your CoreOS Droplets):

```
eval `ssh-agent -s`
ssh-add ~/.ssh/id_rsa_deis
```

Next, we must export the `DEISCTL_TUNNEL` to point to one of our Deis machines, by name or public IP address. If you set up the "convenience" DNS records, you may use one of those for the tunnel. For example:

```
export DEISCTL_TUNNEL=deis-1.example.com
```

This is where `deisctl` will attempt to communicate with the cluster. You can test that it is working properly by running `deisctl list`. If you see a single line of output, the control utility is communicating with the CoreOS machine that was specified.

Before provisioning the platform, we'll need to add an SSH key to Deis, so it can connect to the remote hosts during `deis run`:

```
deisctl config platform set sshPrivateKey=~/.ssh/id_rsa_deis
```

We'll also need to tell the controller which domain name we are deploying applications under:

```
deisctl config platform set domain=example.com
```

Once the prior configuration commands have been run, use this command to provision the Deis platform:

```
deisctl install platform
```

You will see output like the following, which indicates that the units required to run Deis have been loaded on the CoreOS cluster:

```
Install Output:
● ▴ ■
■ ● ▴ Installing Deis...
▴ ■ ●
Storage subsystem...
deis-store-metadata.service: loaded
...
Done.

Please run `deisctl start platform` to boot up Deis.
```

Run this command to start the Deis platform:

```
deisctl start platform
```

Once you see "Deis started.", your Deis platform is running on a cluster!

You may verify that all of the Deis units are _loaded_ and _active_ by running the following command:

```
deisctl list
```

All of the units should be active.

### Install Deis-store-admin (Optional)

You may want to install the `deis-store-admin` component before moving on. It is often helpful when diagnosing storage issues.

To install the component, run these commands:

```
deisctl install store-admin
deisctl start store-admin
```

Now that you've finished provisioning a cluster, register a Deis admin user to get started using the platform!

## Register a Deis User

Now that the Deis platform is running, we must register a user with the `deis` command. The `deis` command communicates with the Deis controller via the _router_ unit, which is accessible at `deis.example.com` in our example:

```
deis register http://deis.example.com
```

You will be prompted for a **username**, **password**, and **email address**. After providing these items, you will be logged into the Deis platform automatically.

Next, add the proper SSH key to `deis`. Enter the following command:

```
deis keys:add
```

You will be prompted to select an SSH key from your available keys. Select the key you would like to add.

### Log in to Deis

If you need to log in later, use the following command:

```
deis login http://deis.example.com
```

You will be prompted for the login you created earlier.

## Deploy Sample Application (Optional)

Deis supports three different ways of building applications:

1. Heroku Buildpacks
2. Dockerfiles
3. Docker Images

We will demonstrate how to use the Heroku Buildpack workflow to deploy an application using [example-ruby-sinatra](https://github.com/deis/example-ruby-sinatra), which is provided by Deis.

Change to a directory that you want to download the example application to. After you are in the desired location, run this command to clone the git repository:

```
git clone https://github.com/deis/example-ruby-sinatra.git
cd example-ruby-sinatra
```

The `deis create` command can be used to create the application on the Deis controller. Run it now:

```
deis create
```

This creates an application, and names it with Deis' automatic naming algorithm:

```
deis create output:
Creating application... done, created dapper-yachting
Git remote deis added
```

In this case, the application's name is `dapper-yachting`.

Now, to deploy the application, use `git push deis master`. Do it now:

```
git push deis master
```

After running the command to deploy, you will see many lines of output. Once it completes, the output will say the application has been deployed, and it will tell you its automatically generated name, and where it can be accessed:

```
git push deis master output:
...
-----> Launching...
 done, dapper-yachting:v2 deployed to Deis

 http://dapper-yachting.example.com

 To learn more, use `deis help` or visit http://deis.io

To ssh://git@deis.example.com:2222/dapper-yachting.git
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii [new branch] master -> master
```

In this example, the URL is `http://dapper-yachting.dev.example.com`, which is the application name combined with the cluster name.

You may test that it works by going to the application URL in a web browser, or by using the following curl command (substitute in your own URL):

```
curl http://dapper-yachting.dev.example.com
```

You should see output that looks similar to the following:

```
App output:
Powered by Deis! Running on container ID a0d35733aad8
```

The sample application looks up the container ID of where it is running, and outputs it. Congrats! Your Deis platform works!

## Conclusion

Now that you have a working Deis setup, try deploying Deis applications using the other two workflows. The _dockerfile_ workflow is similar to the Heroku Buildpack flow, in that it also uses `git push` to deploy, while the _docker image_ workflow uses `deis pull` to deploy. Also, Deis provides much more functionality than was covered here--check out [the documentation](http://docs.deis.io/en/latest/) to learn more!

_This post originally appeared on the [DigitalOcean](https://www.digitalocean.com/) website._

_This work is licensed under a [Creative Commons BY-NC-SA license](https://creativecommons.org/licenses/by-nc-sa/4.0/)._
