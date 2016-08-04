# Raspberry Pi Kernel Compile

[Original URL](http://mitchtech.net/raspberry-pi-kernel-compile/)

> 27 This tutorial will demonstrate how to cross compile the kernel for the Raspberry Pi on Ubuntu 12.04 LTS. The kernel is functional with both the Debian and Arch Linux Raspberry Pi images....

<span class="like-665 like">
  <img src="http://mitchtech.net/wp-content/plugins/wti-like-post/images/pixel.gif" title="Worked" id="like-665" class="lbg-style2 jlk">
</span>

<span id="lc-665" class="lc">10</span>

This worked for me.

This tutorial will demonstrate how to cross compile the kernel for the Raspberry Pi on Ubuntu 12.04 LTS. The kernel is functional with both the Debian and Arch Linux Raspberry Pi images. First, install the package dependencies, git and the cross-compilation toolchain:

> sudo apt-get install git-core gcc-4.6-arm-linux-gnueabi

Create a symlink for the cross compiler:

> sudo ln -s /usr/bin/arm-linux-gnueabi-gcc-4.6 /usr/bin/arm-linux-gnueabi-gcc

Make a directory for the sources and tools, then clone them with git:

> mkdir raspberrypi<br>
> cd raspberrypi<br>
> git clone <https://github.com/raspberrypi/tools.git><br>
> git clone <https://github.com/raspberrypi/linux.git><br>
> cd linux

Generate the .config file from the pre-packaged raspberry pi one:

> make ARCH=arm CROSS_COMPILE=/usr/bin/arm-linux-gnueabi- bcmrpi_cutdown_defconfig

If you want to make changes to the configuration, run make menuconfig (optional):

> make ARCH=arm CROSS_COMPILE=/usr/bin/arm-linux-gnueabi- menuconfig

Once you have made the desired changes, save and exit the menuconfig screen. Now we are ready to start the build. You can speed up the compilation process by enabling parallel make with the -j flag. The recommended use is 'processor cores + 1′, e.g. 5 if you have a quad core processor:

> make ARCH=arm CROSS_COMPILE=/usr/bin/arm-linux-gnueabi- -k -j5

Assuming the compilation was sucessful, create a directory for the modules:

> mkdir ../modules

Then compile and 'install' the loadable modules to the temp directory:

> make modules_install ARCH=arm CROSS_COMPILE=/usr/bin/arm-linux-gnueabi- INSTALL_MOD_PATH=../modules/

Now we need to use imagetool-uncompressed.py from the tools repo to get the kernel ready for the Pi.

> cd ../tools/mkimage/<br>
> ./imagetool-uncompressed.py ../../linux/arch/arm/boot/Image

This creates a kernel.img in the current directory. Plug in the SD card of the existing Debian image that you wish to install the new kernel on. Delete the existing kernel.img and replace it with the new one, substituting "boot-partition-uuid" with the identifier of the partion as it is mounted in Ubuntu.

> sudo rm /media/boot-partition-uuid/kernel.img<br>
> sudo mv kernel.img /media/boot-partition-uuid/

Next, remove the existing /lib/modules and lib/firmware directories, substituting "rootfs-partition-uuid" with the identifier of the root filesystem partion mounted in Ubuntu.

> sudo rm -rf /media/rootfs-partition-uuid/lib/modules/<br>
> sudo rm -rf /media/rootfs-partition-uuid/lib/firmware/

Go to the destination directory of the previous make modules_install, and copy the new modules and firmware in their place:

> cd ../../modules/<br>
> sudo cp -a lib/modules/ /media/rootfs-partition-uuid/lib/<br>
> sudo cp -a lib/firmware/ /media/rootfs-partition-uuid/lib/<br>
> sync

That's it! Exject the SD card, and boot the new kernel on the Raspberry Pi!
