# Nvidia GPU + CoreOS + Docker + TensorFlow = A Fast, Flexible, Deep Learning Platform

[Original URL](http://www.emergingstack.com/2016/01/10/Nvidia-GPU-plus-CoreOS-plus-Docker-plus-TensorFlow.html)

> The Challenge Providing data scientists with the best tool for the job is hard. They need everything from their computers - raw performance, bleeding-edge software and free-rein to experiment....

[![GPU-Powered Dev Box](http://www.emergingstack.com/assets/img/es-dev-stack.png)](http://www.emergingstack.com/assets/img/es-dev-stack.png)

## The Challenge

Providing data scientists with the best tool for the job is hard. They need everything from their computers - raw performance, bleeding-edge software and free-rein to experiment.

We've developed a solution that meets all those requirements and avoids creating a 'one off' build that would haunt sysadmin and devops teams.

_tl;dr - Code to provision your environment is on [Github](http://github.com/emergingstack/es-dev-stack)._

It is still experimental, but it works. And because we're early in the lifecycle for many of these tools, it will only get better.

### Farewell Cloud

For compute-intensive tasks, public cloud hosting costs can become prohibitive. A high-spec GPU-powered VM on AWS is about 20x more expensive than our day-to-day instances. About USD$25,000 annually.

On-premises virtual servers, whilst cheaper, are also not tuned for these scientific-computing use-cases and don't make for good neighbours in a shared environment.

We must look elsewhere...

### Hello, old friend

The 'server under the desk' is back. Better than ever.

Nvidia gave us the ["Dev Box"](https://developer.nvidia.com/devbox) in 2015, a data scientist's dream-machine. But at USD$15,000, it's still a bit pricey.

[Andrej Karpathy's](https://twitter.com/karpathy) built an [home-rig](https://twitter.com/karpathy/status/648256662554341377) that is pretty much ideal, hardware-wise. It can scale-up to about the same specification as Nvidia's beast. Our's is very similar and there's a nice spot for it, right under the desk.

### Development is your Production

So you've got the right hardware. And you followed Nvidia's instructions to get all your software installed and configured. Hours spent deploying the right packages and dependencies, all hand-crafted. And it works perfectly. But you've created a sysadmin's nightmare - a completely bespoke build.

The longer you use this machine, the harder it will be to rebuild, if/when it dies. Or if/when you want to do a major version upgrade. Or if/when you created that once-off workaround...more than once.

Downtime is your enemy, but you've introduced an ugly single-point-of-failure. And gone are the benefits of fully-automated builds.

## A Solution

### Step 1 - Vanilla CoreOS build

Assuming you've got the right hardware ready, follow one of the bullet-proof [quick-start guides](https://coreos.com/os/docs/latest/) for a 'bare-metal' build. CoreOS support PXE, iPXE and installing to disk. Choose whichever method suits your needs.

We use the [PXE build](https://coreos.com/os/docs/latest/booting-with-pxe.html) approach to provision our CoreOS systems.

### Step 2 - Install the CUDA drivers and Nvidia Devices - _One-Time Operation_

Clone the [es-dev-stack](http://github.com/emergingstack/es-dev-stack) repository on Github.

```
$ git clone http://github.com/emergingstack/es-dev-stack.git
```

Docker image build (takes about 30 minutes, downloads ~2.5GB source files)

```
$ cd es-dev-stack/corenvidiadrivers
```

Once complete, you may want to push this image to a private Docker registry, if one is handy. It could be useful when it comes to rebuilding your host one day.

_Dockerfile below for reference._

```
FROM ubuntu:14.04
MAINTAINER Mike Orzel <mike.orzel@emergingstack.com>

RUN apt-get -y update && apt-get -y install git bc make dpkg-dev && mkdir -p /usr/src/kernels && mkdir -p /opt/nvidia/nvidia_installers

ADD http://developer.download.nvidia.com/compute/cuda/7_0/Prod/local_installers/cuda_7.0.28_linux.run /opt/nvidia/

WORKDIR /usr/src/kernels
RUN git clone git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable.git linux
WORKDIR linux
RUN git checkout -b stable v`uname -r` && zcat /proc/config.gz > .config && make modules_prepare
RUN sed -i -e "s/`uname -r`+/`uname -r`/" include/generated/utsrelease.h # In case a '+' was added

# Nvidia drivers setup
WORKDIR /opt/nvidia/
RUN chmod +x cuda_7.0.28_linux.run && ./cuda_7.0.28_linux.run -extract=`pwd`/nvidia_installers
WORKDIR /opt/nvidia/nvidia_installers

RUN ./NVIDIA-Linux-x86_64-346.46.run -a -x --ui=none
RUN sed -i "s/read_cr4/__read_cr4/g" NVIDIA-Linux-x86_64-346.46/kernel/nv-pat.c
RUN sed -i "s/write_cr4/__write_cr4/g" NVIDIA-Linux-x86_64-346.46/kernel/nv-pat.c
CMD ./NVIDIA-Linux-x86_64-346.46/nvidia-installer -q -a -n -s --kernel-source-path=/usr/src/kernels/linux/ && insmod /opt/nvidia/nvidia_installers/NVIDIA-Linux-x86_64-346.46/kernel/uvm/nvidia-uvm.ko
```

#### Run the CUDA Docker Container

```
# docker run -it --privileged cuda
```

#### Confirm Nvidia drivers are installed

You should see a few 'nvidia' items

#### Install devices

Run the "mkdevs" script to create the devices (as root)

Confirm devices are installed

```
# cd /dev
# ls -al | grep -i "nvidia"
```

You should see a list of devices that are now available to be mapped into a Docker container.

```
crw-rw-rw- 1 root root 247, 0 Jan 4 05:54 nvidia-uvm
crw-rw-rw- 1 root root 195, 0 Jan 4 05:54 nvidia0
crw-rw-rw- 1 root root 195, 1 Jan 4 05:54 nvidia1
crw-rw-rw- 1 root root 195, 255 Jan 4 05:54 nvidiactl
```

#### Success!

Now you're ready to put your almost-immutable system to use and expose the power of containerisation. Note, we're using 'privileged' mode here to map the GPU devices to a Docker container which is not secure from a 'shared host' perspective.

### Step 3 - Test it out with Google's [TensorFlow](http://tensorflow.org).

WARNING: The Dockerfile below produces a Docker image that is over **10GB**. It will take 30-40 minutes to build.

We've added a Jupyter notebook to the docker image to validate that GPUs are functioning - it's a basic ConvNet via TensorFlow and is sufficient for verification.

#### Docker image build

```
$ cd es-dev-stack/tflowgpu

$ docker build -t tflowgpu .
```

_Dockerfile below for reference._

```
FROM b.gcr.io/tensorflow/tensorflow:latest-gpu
MAINTAINER Mike Orzel <mike.orzel@emergingstack.com>

# Add some dependent packages we will need for the build process
RUN apt-get -y update && apt-get -y install git bc make dpkg-dev && mkdir -p /usr/src/kernels && mkdir -p /opt/nvidia/nvidia_installers

# Download the nvidia cuda package
ADD http://developer.download.nvidia.com/compute/cuda/7_0/Prod/local_installers/cuda_7.0.28_linux.run /opt/nvidia/
RUN chmod +x /opt/nvidia/cuda_7.0.28_linux.run

# download the linux kernel source and prepare it for use
WORKDIR /usr/src/kernels
RUN git clone git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable.git linux
WORKDIR linux
RUN git checkout -b stable v`uname -r` && zcat /proc/config.gz > .config && make modules_prepare

RUN sed -i -e "s/`uname -r`+/`uname -r`/" include/generated/utsrelease.h # In case a '+' was added
RUN sed -i -e "s/`uname -r`+/`uname -r`/" include/config/kernel.release # In case a '+' was added

# Nvidia drivers setup
WORKDIR /opt/nvidia/
RUN chmod +x cuda_7.0.28_linux.run && ./cuda_7.0.28_linux.run -extract=`pwd`/nvidia_installers
WORKDIR /opt/nvidia/nvidia_installers

RUN ./NVIDIA-Linux-x86_64-346.46.run -a -x --ui=none
RUN sed -i "s/read_cr4/__read_cr4/g" NVIDIA-Linux-x86_64-346.46/kernel/nv-pat.c
RUN sed -i "s/write_cr4/__write_cr4/g" NVIDIA-Linux-x86_64-346.46/kernel/nv-pat.c

RUN ./NVIDIA-Linux-x86_64-346.46/nvidia-installer -q -a -n -s --kernel-source-path=/usr/src/kernels/linux/ --no-kernel-module

# install modules to expected location, cuda will do modprobes in certain situations which require this
WORKDIR /usr/src/kernels/linux
RUN make modules && make modules_install
RUN mv /lib/modules/`uname -r`+ /lib/modules/`uname -r`
WORKDIR /opt/nvidia/nvidia_installers
RUN depmod

# Run jupyter notebook and create a folder for the notebooks
RUN chmod +x /run_jupyter.sh
RUN mkdir /examples
WORKDIR /examples
COPY CNN.ipynb /examples/CNN.ipynb
CMD /run_jupyter.sh
```

#### Run the TensowFlow Docker Container

This 'docker run' command maps the freshly installed GPU devices to the TensorFlow container. This is where the magic happens...

```
$ docker run --device /dev/nvidia0:/dev/nvidia0 --device /dev/nvidia1:/dev/nvidia1 --device /dev/nvidiactl:/dev/nvidiactl --device /dev/nvidia-uvm:/dev/nvidia-uvm -it -p 8888:8888 --privileged tflowgpu
```

Open a browser and navigate to <http://{your> dev box IP}:8888 to launch Jupyter and run through the example. With a single Nvidia TitanX, this test should be about 10x faster than running on an Intel i7 CPU.

[![Success](http://www.emergingstack.com/assets/img/test_success.png)](http://www.emergingstack.com/assets/img/test_success.png)

[Start using the es-dev-stack code now](http://github.com/emergingstack/es-dev-stack.git). Contibutions welcomed.

**Attributions** This solution takes inspiration from a few community sources. Thanks to;

_In 'Part 2', we will demonstrate how to integrate this dockerized environment with Spark and potentially Kubernetes (depending on the status of [issue 19049](https://github.com/kubernetes/kubernetes/issues/19049))._
