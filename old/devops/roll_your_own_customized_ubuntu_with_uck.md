# Roll Your Own Customized Ubuntu With UCK

[Original URL](http://www.linux.com/learn/tutorials/739139-roll-your-own-customized-ubuntu-with-uck)

> Does the world really need another Ubuntu respin? Maybe not the world, but maybe you do. You might want to make your own customized Ubuntu to use in your business, or for your personal needs, or make...

Does the world really need another Ubuntu respin? Maybe not the world, but maybe you do. You might want to make your own customized Ubuntu to use in your business, or for your personal needs, or make your own ultimate rescue Ubuntu for performing rescue and recovery tasks on Linux, Mac, and Windows computers.

We're going to use [UCK, the Ubuntu Customization Kit,](https://apps.ubuntu.com/cat/applications/precise/uck/) to roll our own ultimate customized Ubuntu. We'll put whatever packages we want on it, and we'll make it a hybrid image so we can install it to a CD/DVD, USB stick, or hard drive. USB sticks are great for portable and rescue Linuxes because they are nearly as fast as hard drives, reusable, and you can save changes across reboots.

UCK is one of the easiest tools for creating a customized Ubuntu through a nice graphical wizard. It offers a root console option for more flexibility and advanced options such as using `apt-get` and `tasksel`, and for complete control, such as using preseed files, some hackable scripts. (Documentation is in `/usr/share/doc/uck/html/`.)

## Prerequisites

UCK only works with Ubuntu, so you need to install it on Ubuntu or an Ubuntu derivative, such as Linux Mint. The package name is `uck`, which you install in the usual way with your favorite package manager. Then you'll need your chosen Ubuntu/Kubuntu/Lubuntu/etc. installation ISO, and you must have Internet access while `uck` is building your custom Ubuntu. You can't build for other architectures; for example you can't build an ARM image on an x86 machine, or a 64-bit image on a 32-bit PC.

You'll need a minimum of 5GB free space in your home directory, and of course way more is always better. If you have VirtualBox or some other virtualizer you can preview and test your custom images before copying them to other media.

A very important prequisite is to install `libfribidi-bin`. It is a required dependency that is not present in the `uck` package. If you don't install it your custom build will fail, when it is almost finished, with a "Failed to build gfxboot theme" error. This is is a bug going back to 12.04 or earlier, so sigh and deal with it.

## Customizing Your Build

Fire up UCK and follow the prompts. These are the steps:

- Select which language packs to install. To select multiples just click on each on; you don't need to use shift- or ctrl+click.
- Select the languages you want available when you boot your live Ubuntu
- Select your default language
- Choose your desktop environment or environments
- Select the Ubuntu installation ISO that you downloaded. I used Lubuntu.
- Give your build a name, like Lubuntu-Custom
- Do you want to customize the CD manually during building (using package utilities, console, etc.)? Well of COURSE you do, otherwise why are you here?
- Do you want to delete all Windows files from the CD? Say yes to save space if you don't plan to mess with Windows
- Do you want to generate a hybrid image (ISO/USB). Yes you do.

Now you'll see a message that the building will now start, with the location of your new ISO. This opens a terminal, and you'll have to enter your sudo password. Follow along as it updates package repos, removes unnecessary language packs, and then opens the customization dialog (figure 1). `Run package manager` opens Synaptic so you can select whatever additional packages you want. Use Synaptic in the usual way, searching and marking packages for installation. The base packages on your installation ISO are already selected, so you only need to select your chosen extras. Close Synaptic when you're finished, and you'll be returned to the customization dialog. You can then return to Synaptic if you need to, or select `Continue Building` to go to the next step.

![UCK customization dialog](http://www.linux.com/images/stories/41373/figure-1-customization-dialog.jpg "Figure 1: Customization dialog.")

`Run console application` opens a root terminal if you prefer using `apt-get`, or any other custom commands you want to use. For example, you could install `tasksel`, the fabulously useful Debian installation tool that installs package groups such as LAMP Server, Virtual Machine Host, Audio recording and editing suite, various desktops, plus a manual package selector (figure 2).

![UCK tasksel](http://www.linux.com/images/stories/41373/figure-2-tasksel.jpg "Figure 2: Using Tasksel in the UCK root console.")

Install and run tasksel this way from the UCK root console:

```
root@studio:/# apt-get install tasksel
root@studio:/# tasksel
```

You can generate a list of package groups and their installation status with `tasksel --list-tasks`. `tasksel --task-desc [package group name]` displays the description, and `tasksel --task-packages [package group name]` lists all the packages in the group. Consult `man tasksel` for complete options.

When you're finished running commands in the root console type `exit` to return to the customization dialog. You can run Synaptic or the console again, or select `Continue Building` to go to the next step, which is building your new ISO. This takes just a few minutes, and when it's finished you'll see a Build Success dialog that tells you where to find your new Ubuntu image, and how to quickly test it with qemu.

![lubuntu in virtualbox](http://www.linux.com/images/stories/41373/fig-3-lubuntu-in-virtualbox.jpg "Figure 3: New customized Lubuntu in VirtualBox.")

## Copying to Installation Media

Use the excellent [](http://www.linux.com/Unetbootin)<http://unetbootin.sourceforge.net/> to install your new custom Ubuntu to a USB stick. Use the "Space used to preserve files across reboots" option to save configurations and files, and to not have to start over with every reboot. This is a great way to make a personal portable Ubuntu you can carry with you anywhere. Any PC or laptop made in the last 7 seven years should reliably support booting to a USB device, though you may have to enter the BIOS to allow USB booting. In many BIOS pressing the F12 key opens a boot device selector.

For making a bootable CD/DVD use the excellent K3b or Brasero. When you boot your new custom Ubuntu you'll have the option to either run it live from your boot media, or install it to a hard drive.

## Other Ways to Roll Your Own

UCK is a great introduction to creating a customized Linux distribution. Here are some other roll-your-own tools you might be interested in:

- [SUSE Studio](http://susestudio.com/) builds virtual machine images, cloud images, hard drive and removable media images
- [Linux From Scratch](http://www.linuxfromscratch.org/) is a great way to learn to build a distro from source code
- [DebianCustomCD](https://wiki.debian.org/DebianCustomCD) builds Debian from scratch.
