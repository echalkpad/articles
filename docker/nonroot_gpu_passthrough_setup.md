# Non-root GPU passthrough setup

[Original URL](http://www.evonide.com/non-root-gpu-passthrough-setup/)

> If you want to use Linux as your main operating system and don't want to do compromises like using a dual-boot solution with Windows there is an alternative called GPU passthrough. You...

If you want to use Linux as your main operating system and don't want to do compromises like using a dual-boot solution with Windows there is an alternative called GPU passthrough. You basically pass through your GPU into a virtual machine s.t. your guest can fully utilize it according to your desires. The bottleneck of doing this is negligible (in my tests the 3DMark benchmark results were almost equal).

There are already a lot of guides and tutorials about how to make a GPU passthrough setup with QEMU. Among the best resources I could find are:

Although considering all of those and putting a lot of effort into setting up my own setup I had to deal with many annoying and very time consuming issues.<br>
One of those issues is that most of the available guides just start QEMU with root privileges. This means a QEMU breakout directly leads to full control over your host system.

This article is meant as a frequently updated guide (last update: **10.02.2016**) and (hopefully) complete walkthrough for everyone who is considering to make a non-root GPU passthrough setup with QEMU.<br>
It was successfully tested with an up-to-date Archlinux and Xubuntu 15.10 (Wily), but should be compatible with any other distribution having a newer kernel version (preferably versions >= 3.9).

## <span id="GPU_passthrough_with_QEMU">GPU passthrough with QEMU</span>

I don't take any responsibilities whatsoever in case you brick your system by following this guide.

### <span id="Hardware_requirements">Hardware requirements</span>

This setup has the following requirements on your hardware:

- IOMMU compatible hardware (referred to as VT-D for Intel and VI for AMD):

  - CPU
  - Mainboard and bios (reading the mainboard's online manual can help).

- Two GPUs: one will be used for the host system and the other for the guest. An integrated GPU i.e. iGPU will do for the host.
- One monitor with two different inputs like DP and HDMI. Two inputs are necessary, because you need to switch between your host and guest video output. You can also use a KVM switch or utilize a second monitor (the latter is recommended).

### <span id="Basic_preparations">Basic preparations</span>

For this guide we will use QEMU (which makes use of KVM). One graphics card will be exclusively allocated to the guest system while the other takes care of running our host system. To achieve this we can make use of the Linux VFIO driver (available in kernel versions >= 3.9?).

Make sure your system is up to date:

 | <div class="crayon-pre">
  <p>sudo apt-get update</p>
  <p>sudo apt-get upgrade</p>
</div>
 | ------------------------------------------------------------------------------------------

Enable IOMMU support in your bootloader: ensure the default grub config contains "intel_iommu=on" if you have an Intel cpu and "amd_iommu=on" for an AMD cpu:

 | <div class="crayon-pre">
  <p>/etc/default/grub</p>
  <p>[…]</p>
  <p>GRUB_CMDLINE_LINUX_DEFAULT=“[…] intel_iommu=on”</p>
</div>
 | --------------------------------------------------------------------------------------------------------------------------------

then do

 | <div class="crayon-pre">
  <p>sudo grub-mkconfig | sudo tee /boot/grub/grub.cfg</p>
</div>
 | ------------------------------------------------------------------------------------------

and ensure /boot/grub/grub.cfg is properly updated.

### <span id="Dedicated_GPU_isolation">Dedicated GPU isolation</span>

Dedicated GPU isolation can be done by using pci-stub with VFIO (available since 3.9+) or directly using vfio-pci (available since 4.1+). The latter is recommended since you don't need to rebind the GPU with VFIO after each boot. Ensure it is installed by running:

No output means everything is ok. If there is an error you need to install pci-stub (c.f. related sections in [Puget system's guide](https://www.pugetsystems.com/labs/articles/Multiheaded-NVIDIA-Gaming-using-Ubuntu-14-04-KVM-585/)).

At this point there are at least two possibilities to load the correct modules:

- Archlinux:<br>
  You need to modify "/etc/mkinitcpio.conf" and update the initial ramdisk environment. Please refer to the VFIO-PCI section here: <https://wiki.archlinux.org/index.php/PCI_passthrough_via_OVMF#vfio-pci>
- Debian based systems:<br>
  You can load all necessary passthrough modules like VFIO by adding them into the modules file:

   | <div class="crayon-pre">
    <p>/etc/modules</p>
    <p>vfio</p>
    <p>vfio_iommu_type1</p>
    <p>vfio_pci</p>
    <p>kvm</p>
    <p>kvm_intel</p>
  </div>
   | -----------------------------------------------------------------------------------------------------------------------------------------------

  - **Attention:**<br>
    if you don't have vfio-pci support don't forget to add pci_stub here, too!

Assuming vfio-pci is available you can then get the ID of your dedicated GPU and also its audio device (used for DP/HDMI):

 | <div class="crayon-pre">
  <p>lspci -nnk</p>
  <p>[…]</p>
  <p>02:00.0 VGA compatible controller [0300]: NVIDIA Corporation GM200 [GeForce GTX 980 Ti] [10de:17c8] (rev a1)</p>
  <p>    Subsystem: eVga.com. Corp. Device [3842:4996]</p>
  <p>    Kernel driver in use: vfio-pci</p>
  <p>02:00.1 Audio device [0403]: NVIDIA Corporation Device [10de:0fb0] (rev a1)</p>
  <p>    Subsystem: eVga.com. Corp. Device [3842:4996]</p>
  <p>    Kernel driver in use: vfio-pci</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

In this case the important ids are: **10de:17c8** and **10de:0fb0**. Further, we can then add those ids to the VFIO modprobe config so that the VFIO-PCI drivers will be bound to those devices on boot:

 | <div class="crayon-pre">
  <p>/etc/modprobe.d/vfio-pci.conf</p>
  <p>options vfio-pci ids=10de:17c8,10de:0fb0</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------

Finally, for Debian run:

and for Archlinux with a normal linux kernel run:

in order for the changes to be applied on boot. At this point you should restart your system and verify with "lspci -nnk" if the entry "Kernel driver in use: vfio-pci" is available for the dedicated GPU.

In case you have made it this far most things should be ready for the next step: setting up QEMU and telling it to forward our dedicated GPU.

### <span id="QEMU_setup">QEMU setup</span>

Install QEMU (assuming you have a x86-64 system):

 | <div class="crayon-pre">
  <p>sudo apt-get install qemu-system-x86-64</p>
</div>
 | --------------------------------------------------------------------------------

First we will need a QEMU image for our VM (unless you want to forward a SATA controller or use another storage device for which I won't go into detail at this point). Many guides use virtio controller and virtio drivers. Although they are improving performance, my advice is to avoid them at least for now since they unnecessarily complicate things. You can create a disk with 60 GB space and qcow2 format (makes use of compression) with:

 | <div class="crayon-pre">
  <p>qemu-img create -f qcow2 qemu_vm.qcow2 60G</p>
</div>
 | -----------------------------------------------------------------------------------

Alternatively, you can use a raw image here, however, doing so won't improve performance much and will eat up more space than necessary (I have to verify this claim first though). The next thing is taking care of the boot process. To be able to boot disks with GPT partitions we need EFI. Further, this requires UEFI (which is a replacement for the old bios version). UEFI support is provided by using OVMF and can be downloaded pre-compiled from Gerd Hoffman's site <https://www.kraxel.org/repos/jenkins/edk2/>. Install it using:

 | <div class="crayon-pre">
  <p>rpm -i edk2.git-ovmf-x64-*.rpm</p>
</div>
 | -----------------------------------------------------------------------

If you use a Debian based distribution like Ubuntu you can use the following commands to extract it:

 | <div class="crayon-pre">
  <p>sudo apt-get install rpm2cpio</p>
  <p>rpm2cpio edk2.git-ovmf-x64-*.rpm | (cd /; sudo cpio -i –make-directories)</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------

We assume that /usr/share/edk2.git/ovmf-x64/ is available at this point. Then copy the OVMF settings file to the current directory:

 | <div class="crayon-pre">
  <p>cp /usr/share/edk2.git/ovmf-x64/OVMF_VARS-pure-efi.fd .</p>
</div>
 | ------------------------------------------------------------------------------------------------

A basic QEMU start script can look like this:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-p">#!/bin/bash</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“”</span>
</p>
  <p>
  <span class="crayon-p"># Basic CPU settings.</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -cpu host,kvm=off”</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -smp 8,sockets=1,cores=4,threads=2”</span>
</p>
  <p>
  <span class="crayon-p"># Enable KVM full virtualization support.</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -enable-kvm”</span>
</p>
  <p>
  <span class="crayon-p"># Assign memory to the vm.</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -m 4000”</span>
</p>
  <p>
  <span class="crayon-p"># VFIO GPU and GPU sound passthrough.</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -device vfio-pci,host=02:00.0,multifunction=on”</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -device vfio-pci,host=02:00.1”</span>
</p>
  <p>
  <span class="crayon-p"># Supply OVMF (general UEFI bios, needed for EFI boot support with GPT disks).</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -drive if=pflash,format=raw,readonly,file=/usr/share/edk2.git/ovmf-x64/OVMF<em>CODE-pure-efi.fd”</em></span>
</p>
  <em>
  <p><span class="crayon-v">OPTS</span><span class="crayon-o">=</span><span class="crayon-s">“$OPTS -drive if=pflash,format=raw,file=$(pwd)/OVMF_VARS-pure-efi.fd”</span></p>
  <p><span class="crayon-p"># Load our created VM image as a harddrive.</span></p>
  <p><span class="crayon-v">OPTS</span><span class="crayon-o">=</span><span class="crayon-s">“$OPTS -hda $(pwd)/qemu_vm.qcow2”</span></p>
  <p><span class="crayon-p"># Load our OS setup image e.g. ISO file.</span></p>
  <p><span class="crayon-v">OPTS</span><span class="crayon-o">=</span><span class="crayon-s">“$OPTS -cdrom $(pwd)/windows_10_professional.iso”</span></p>
  <p><span class="crayon-p"># Use the following emulated video device (use none for disabled).</span></p>
  <p><span class="crayon-v">OPTS</span><span class="crayon-o">=</span><span class="crayon-s">“$OPTS -vga qxl”</span></p>
  <p><span class="crayon-p"># Redirect QEMU’s console input and output.</span></p>
  <p><span class="crayon-v">OPTS</span><span class="crayon-o">=</span><span class="crayon-s">“$OPTS -monitor stdio”</span></p>
</em>
  <p><em><span class="crayon-e">sudo </span><span class="crayon-v">qemu</span><span class="crayon-o">-</span><span class="crayon-v">system</span><span class="crayon-o">-</span><span class="crayon-v">x86</span><span class="crayon-sy">
</span></em>64<span class="crayon-sy">$</span><span class="crayon-v">OPTS</span></p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

- Adjust the number of virtual cores, physical cores and threads with the "-smp" option for your CPU accordingly.
- We use "kvm=off" to trick Nvidia cards into believing they are not passed through (else the card might reject working with errorcode 43).
- In above configuration my dedicated GPU's PCI ids were 

  **<span>02:00.0</span>**

   and 

  **<span>02:00.1</span>**

   you should lookup yours by executing "lspci" and adjust them accordingly.
- Using "-vga qxl" makes QEMU use the QXL paravirtual graphic card as the main emulated VGA device. This gives us a window where we can directly see what's going on in the VM. Many guides recommended directly making use of the passthrough and switch to the dedicated GPU's output. However, I had no signal i.e. my screen **just stayed black**. This method makes it way easier to setup the OS first which then in turn will use the correct drivers. Further, we can avoid passing through any USB devices yet since they would lead to deadlocks during a failure within the vm.
- Instead of an iso image you can also directly pass any device (e.g. passing a USB stick with -cdrom /dev/sdX where sdX is the correct device name works just fine).
- **Hint:** Already at this point you can verify if the passthrough is working by starting QEMU and writing "info pci" into the QEMU console:

   | <div class="crayon-pre">
    <p>QEMU 2.5.0 monitor - type ‘help’ for more information</p>
    <p>(qemu) info pci</p>
    <p>[…]</p>
    <p>Bus  0, device   5, function 0:</p>
    <p>  VGA controller: PCI device 10de:17c8</p>
  </div>
   | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

  Here you can see that my device with the id 10de:17c8 was correctly mapped into the vm.

If you have fully installed your desired OS and booted into it you should make sure that the passed-through GPU is correctly identified (e.g. in the device manager of Windows or by using lspci in Linux). In my case I had to download and install the newest Nvidia GPU drivers in order for the GPU to be correctly identified in Windows 10\. Once this step was done I was able to switch to the other monitor port and was finally able to see the guest's dedicated GPU output :).

- **Hint:** If you want to manage the VM with your dedicated GPU's video output you can just click into the QXL window and then switch to your other monitor input.
- **Attention:** Linux can refuse to show output on the dedicated GPU when QXL is running (you can verify this by using "xrandr"). In order to fix this you can deactivate the QXL device s.t. only the dedicated GPU will be known to the guest:

   | <div class="crayon-pre">
    <p>
    <span class="crayon-p"># Use an emulated video device (use none for disabled).</span>
  </p>
    <p>
    <span class="crayon-v">OPTS</span>
    <span class="crayon-o">=</span>
    <span class="crayon-s">“$OPTS -vga none -device qxl”</span>
  </p>
  </div>
   | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

  ~~However, doing so will require passing through a mouse/keyboard to control the vm.~~

  - ~~~~**Update:**<br>
    By appending "-device qxl" you can disable the QXL video output while still being able to control the VM exactly the same way as in the given hint above (thx to Reddit user /u/SxxxX for pointing this out).

The next step is to forward the keyboard and mouse for better responsiveness (QXL is too slow). Here you can either pass through a complete USB controller or rather pass through a specific USB device. Since I was unable to pass through a USB 3.0 controller I had to pass through specific devices (if you want to try to pass through a complete USB controller you can refer to Step 7 in [Puget system's guide](https://www.pugetsystems.com/labs/articles/Multiheaded-NVIDIA-Gaming-using-Ubuntu-14-04-KVM-585/)).

To forward the usb devices just get their ids:

 | <div class="crayon-pre">
  <p>lsusb</p>
  <p>[…]</p>
  <p>Bus 001 Device 004: ID 046d:c051 Logitech, Inc. G3 (MX518) Optical Mouse</p>
  <p>Bus 001 Device 003: ID 046d:c316 Logitech, Inc. HID-Compliant Keyboard</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Once you have all ids you can forward those devices in our QEMU startup script with:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-p"># Passthrough USB devices.</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -usb”</span>
</p>
  <p>
  <span class="crayon-p"># USB mouse</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -usbdevice host:046d:c051”</span>
</p>
  <p>
  <span class="crayon-p"># USB keyboard</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -usbdevice host:046d:c316”</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### <span id="Sound">Sound</span>

We will now take care of getting working sound. To find out more about QEMU's supported sound devices consider:

 | <div class="crayon-pre">
  <p>qemu-system-x86_64 -soundhw ?</p>
  <p>Valid sound card names (comma separated):</p>
  <p>sb16        Creative Sound Blaster 16</p>
  <p>es1370      ENSONIQ AudioPCI ES1370</p>
  <p>ac97        Intel 82801AA AC97 Audio</p>
  <p>adlib       Yamaha YM3812 (OPL2)</p>
  <p>gus         Gravis Ultrasound GF1</p>
  <p>cs4231a     CS4231A</p>
  <p>hda         Intel HD Audio</p>
  <p>pcspk       PC speaker</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Adjust our startup script to use the Intel HD Audio (hda) device:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-p"># Emulate a sound device.</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -soundhw hda”</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

We further need to specify an audio driver we would like to use. Let's give ALSA a try:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-p"># Select a QEMU sound driver and specify its settings.</span>
</p>
  <p>
  <span class="crayon-v">VM_SOUND</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“”</span>
</p>
  <p>
  <span class="crayon-v">VM_SOUND</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$VM_SOUND QEMU_AUDIO_DRV=alsa”</span>
</p>
  <p>
  <span class="crayon-v">VM_SOUND</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$VM_SOUND QEMU_ALSA_DAC_BUFFER_SIZE=512”</span>
</p>
  <p>
  <span class="crayon-v">VM_SOUND</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$VM_SOUND QEMU_ALSA_DAC_PERIOD_SIZE=170”</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Alternatively you can also use Pulseaudio (it doesn't have the best reputation though and I don't recommend using it until really necessary...):

 | <div class="crayon-pre">
  <p>
  <span class="crayon-p"># Select a QEMU sound driver and specify its settings.</span>
</p>
  <p>
  <span class="crayon-v">VM_SOUND</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“”</span>
</p>
  <p>
  <span class="crayon-v">VM_SOUND</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$VM_SOUND QEMU_AUDIO_DRV=pa”</span>
</p>
  <p>
  <span class="crayon-v">VM_SOUND</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$VM_SOUND QEMU_PA_SAMPLES=128”</span>
</p>
</div>
 | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

To get more information about the above used sound parameters and some further sound driver information consider:

 | <div class="crayon-pre">
  <p>qemu-system-x86_64 -audio-help</p>
</div>
 | -----------------------------------------------------------------------

Finally, we need to rewrite the QEMU startup script to consider the sound environment settings:

 | <div class="crayon-pre">
  <p>sudo $VM_SOUND qemu-system-x86_64 $OPTS</p>
</div>
 | --------------------------------------------------------------------------------

If you have crackles in your sound using ALSA try adjusting the buffer and period sizes.

- **Question:** Do you have any advice about how to improve the input (microphone) quality at this point?

Whereas the above configuration worked instantly for Xubuntu, using Archlinux lead to horrible sound seemingly because of permission issues while setting the ALSA buffer and period sizes.<br>
To solve this you can alternatively use the ac97 device and leave out any ALSA parameters in the environment variable.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-p"># Emulate a sound device.</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -soundhw ac97”</span>
</p>
  <p>
  <span class="crayon-p"># Select a QEMU sound driver and specify its settings.</span>
</p>
  <p>
  <span class="crayon-v">VM_SOUND</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“QEMU_AUDIO_DRV=alsa”</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

- **Attention:** Windows won't detect your sound device correctly. You have to use exactly the right drivers which are Intel drivers in this case (there are many drivers and you can waste a lot of time by choosing wrong ones here). You can find the correct AC 97 drivers for QEMU here: [Intel 82801AA AC97 Audio](http://www.driverscape.com/download/intel%28r%29-82801aa-ac-97-audio-controller) (don't download the first driver since this is adware. Use the second link with the zip file instead).

### <span id="Network">Network</span>

The network seemed to work directly out of the box i.e. I could utilize about 5-6 MB/s with my 50 Mbit/s internet connection so I didn't investigate further here. However, there are some things you can do to improve the performance if necessary (c.f. Performance Tweaks Section).

### <span id="Synergy">Synergy</span>

Synergy allows you to share the mouse and keyboard across multiple operating systems which is very helpful when we pass through USB devices. I recommend using Synergy as a server within the guest system while running it as a client on the host system.

- **Attention**:<br>
  Don't use Synergy versions <1.7 (they have a bug that doesn't allow you to press multiple keys at once. This is very annoying when typing capitalized letters...). You should download a version >= 1.7 (

  <span>for your host and guest</span>

  ) at <http://www.synergy-project.org/nightly>.

Make the guest's Synergy accessible by forwarding its default port (24800 is default):

 | <div class="crayon-pre">
  <p>
  <span class="crayon-p"># Synergy port forward…</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -redir tcp:24800::24800”</span>
</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

- **Hint:** In my case I had to add "setxkbmap de nodeadkeys" to fix an issue with the German keyboard layout and Synergy.

Now you can configure the Synergy server and client by setting a proper client name. Finally, you should be able to connect to localhost on the client side while Synergy is running on both systems.

- **Hint:** During gaming you want to lock the mouse to one specific monitor. You can do this by pressing the Scroll-Lock button. If you don't have a Scroll-Lock button on your keyboard you can specify another button in the Synergy server settings. In my case I have bound a mouse button for this.

If you are using Synergy as a server on your host and as a client in your guest (e.g. when you don't want to use USB passthrough) and experience weird mouse movements ingame try setting "Send relative mouse moves" in the Synergy server.

<span>
  <strong>Congratulations if you have made it this far!</strong>
</span>

Your basic GPU passthrough setup should work by now. We can now take care of removing the requirements of QEMU to be started as the root user.

## <span id="Permissions_for_non-root_GPU_passthrough">Permissions for non-root GPU passthrough</span>

At this point you should have a setup that is working as root. The next steps in this chapter will help to improve the overall QEMU security. Please keep in mind that using a passthrough setup even as non-root is risky since the GPU can have a lot of control over the system. Further, we are granting the VM access to other devices like USB devices which doesn't really improve the situation. Thus, please don't expect this chapter to be a guarantee for a really bulletproof setup. All we will do here is to decrease the attack surface as much as possible.

### <span id="Basic_permissions">Basic permissions</span>

We'll create the user "qemu_vga" with a disabled password (s.t. you have to use "su"/"sudo" as an admin) for the purpose of running QEMU:

 | <div class="crayon-pre">
  <p>sudo adduser –disabled-password qemu_vga</p>
</div>
 | ---------------------------------------------------------------------------------

We can now add the qemu_vga user to the audio and kvm group:

 | <div class="crayon-pre">
  <p>sudo usermod -a -G audio qemu_vga</p>
  <p>sudo usermod -a -G kvm qemu_vga</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------

Let's allow our current user (in my case evonide) sudo access to the qemu_vga user (this way we don't need to type any passwords to start our vm later):

 | <div class="crayon-pre">
  <p>/etc/sudoers</p>
  <p>[…]</p>
  <p>evonide ALL=(qemu_vga) NOPASSWD:ALL</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------

Ensure that our user has write access to our vm image and the OVMF variables file:

 | <div class="crayon-pre">
  <p>sudo chown qemu_vga:qemu_vga qemu_vm.qcow2</p>
  <p>sudo chown qemu_vga:qemu_vga OVMF_VARS-pure-efi.fd</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------

You have at least two options regarding the QEMU startup script:

1. Make sure the QEMU startup script is non writable by the qemu_vga user.
2. Never execute the QEMU startup script with your main user, but rather only execute it with "sudo -Hu qemu_vga /path/to/the/qemu_script.sh".

If you neglect those points a possible breakout would mean the qemu_vga user would be able to write arbitrary commands into the startup script which would get executed by your user once you start the VM. For this guide I assume you use option 1, however, using option 2 would be okay, too. Just don't forget to remove the redundant "sudo" command in the QEMU script if you decide to go with it.

Next, if you want to use QXL (recommended for testing) you need to give our qemu_vga user access to the XServer by modifying our QEMU script:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-p">#!/bin/bash</span>
</p>
  <p>
  <span class="crayon-v">xhost</span>
  <span class="crayon-o">+</span>
  <span class="crayon-v">SI</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">localuser</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">qemu_vga</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">dev</span>
  <span class="crayon-o">/</span>
  <span class="crayon-t">null</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“”</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">]</span>
</p>
</div>
 | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

- **Attention:** Granting XSever access to the user qemu_vga is a bad idea from many point of views regarding security. You should only do this while testing and using QXL. You can avoid any windows by using "-nographic" together with the "-vga none" option (c.f. Miscellaneous Section).

### <span id="Device_permissions">Device permissions</span>

To avoid errors like:

 | <div class="crayon-pre">
  <p>qemu-system-x86_64: -device vfio-pci,host=02:00.0,multifunction=on: vfio: error opening /dev/vfio/1: Permission denied</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------

We need set proper permissions for the VFIO and USB devices by granting all users in the kvm group access. We will add a VFIO rule and our USB device ids (c.f. USB passthrough in the previous chapter) to the Linux udev rules like this:

 | <div class="crayon-pre">
  <p>/etc/udev/rules.d/10-qemu-hw-users.rules</p>
  <p>SUBSYSTEM==“vfio”, OWNER=“root”, GROUP=“kvm”</p>
  <p>SUBSYSTEM==“usb”, ATTR{idVendor}==“046d”, ATTR{idProduct}==“c31c” OWNER=“root”, GROUP=“kvm”</p>
  <p>SUBSYSTEM==“usb”, ATTR{idVendor}==“046d”, ATTR{idProduct}==“c051” OWNER=“root”, GROUP=“kvm”</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

You can then reload all udev rules with:

 | <div class="crayon-pre">
  <p>sudo udevadm control –reload-rules</p>
  <p>sudo udevadm trigger</p>
</div>
 | ---------------------------------------------------------------------------------------------------------

If this doesn't work you might have to restart your system at this point. Please verify if the rights are correctly set with:

 | <div class="crayon-pre">
  <p>ls -la /dev/vfio</p>
  <p>[…]</p>
  <p>crw-rw—-  1 root kvm  244,   0 Feb  8 16:13 1</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------

and

 | <div class="crayon-pre">
  <p>ls -la /dev/bus/usb/*</p>
  <p>[…]</p>
  <p>crw-rw—- 1 root kvm  189, 2 Feb  8 16:16 00</p>
</div>
 | --------------------------------------------------------------------------------------------------------------------------------

Here you should see similar entries for your devices.

Further, we want QEMU to start as our new user so we can modify the startup once again:

 | <div class="crayon-pre">
  <p>sudo -Hu qemu_vga bash -c “$VM_SOUND qemu-system-x86_64 $OPTS”</p>
</div>
 | -------------------------------------------------------------------------------------------------------

If you prefer using a password for qemu_vga and prefer using "su" instead you can go with:

 | <div class="crayon-pre">
  <p>su - qemu_vga -c “$VM_SOUND qemu-system-x86_64 $OPTS”</p>
</div>
 | ----------------------------------------------------------------------------------------------

- **Attention:** At this point it is important to notice the "-" after su. This takes care of creating a new environment for the user. Avoiding this can lead to several XServer permission errors.

### <span id="Memory_permissions">Memory permissions</span>

If you would start QEMU at this point you would get an error similar to the following one:

 | <div class="crayon-pre">
  <p>qemu-system-x86_64: -device vfio-pci,host=02:00.0,multifunction=on: vfio_dma_map(0x557e0d0279b0, 0x0, 0xc0000, 0x7f5674000000) = -12 (Cannot allocate memory)</p>
  <p>qemu-system-x86_64: -device vfio-pci,host=02:00.0,multifunction=on: VFIO_MAP_DMA: -12</p>
  <p>[…]</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Our user should be able to allocate enough memory for the vm. To fix this we need to modify the limits file:

 | <div class="crayon-pre">
  <p>/etc/security/limits.conf</p>
  <p>qemu_vga soft memlock 20000000</p>
  <p>qemu_vga hard memlock 20000000</p>
</div>
 | --------------------------------------------------------------------------------------------------------------------------------------------------

Here I have granted the user qemu_vga access to at most 20 GiB. Since we will switch to the user with "sudo" or "su" we need to ensure those limits will be considered:

 | <div class="crayon-pre">
  <p>/etc/pam.d/sudo, /etc/pam.d/su and /etc/pam.d/su-l (only if available)</p>
  <p>(uncomment/add the following line)</p>
  <p>session    required   pam_limits.so</p>
</div>
 | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Verify the locked memory limit with:

 | <div class="crayon-pre">
  <p>sudo -Hu qemu_vga bash -c “ulimit -l”</p>
</div>
 | ------------------------------------------------------------------------------

### <span id="Sound_permissions">Sound permissions</span>

The next important step is to get the sound running. One option is to use the ac97 and ALSA without parameters solution (as described in the first Chapter). However, I wanted to be able to further customize ALSA with parameters to further improve the sound quality. **This was definitively the most difficult part of the setup**. Unfortunately, the following steps only worked for Xubuntu. When starting our setup at this point with the ALSA driver you might get the following error:

 | <div class="crayon-pre">
  <p>(qemu) alsa: Requested buffer size 512 was rejected, using 1881</p>
  <p>alsa: Requested period size 170 was rejected, using 940</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------

After a lot of research and even after reading QEMU-ALSA sourcecode I was unable to find the reason behind it. Alternatively trying Pulseaudio (as described in the previous chapter) lead to the following error:

 | <div class="crayon-pre">
  <p>pulseaudio: pa_context_connect() failed</p>
  <p>pulseaudio: Reason: Connection refused</p>
  <p>pulseaudio: Failed to initialize PA contextaudio: Could not init `pa’ audio driver</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

This problem can be solved by allowing anonymous authentication and sharing Pulseaudio access via a socket file like /tmp/pulse. 

<span>Update</span>

 the existing entry in the following way:

 | <div class="crayon-pre">
  <p>/etc/pulse/default.pa</p>
  <p>[…]</p>
  <p>load-module module-native-protocol-unix auth-anonymous=1 socket=/tmp/pulse</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------

Further, we need to tell the clients to connect to this socket:

 | <div class="crayon-pre">
  <p>/etc/pulse/client.conf</p>
  <p>[…]</p>
  <p>default-server = unix:/tmp/pulse</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------

Don't forget to kill and restart the Pulseaudio daemon:

 | <div class="crayon-pre">
  <p>pulseaudio -k</p>
  <p>pulseaudio -D</p>
</div>
 | -----------------------------------------------------------------------------

- **Attention:** I later noticed that after rebooting my system there was a bug where the Pulseaudio daemon wouldn't startup. Adding "pulseaudio -D" to the Application Autostart in Xubuntu solved this problem though.

By doing so I got Pulseaudio running. What absolutely stunned me was the fact that 

<span>
  <strong>solving the Pulseaudio issue also solved the ALSA issue</strong>
</span>

 described before. I know that most Ubuntu versions (including Xubuntu) automatically come with Pulseaudio, too. However, the connection between ASLA, Pulseaudio and this error was very unclear to me.

While the only working solution on Archlinux was using ALSA with ac97, all sound device and driver combinations worked successfully under Xubuntu.

If you have reached this point you should have a basic GPU passthrough setup working with our qemu_vga user.

## 

### <span id="Hyper-V_enlightments">Hyper-V enlightments</span>

You can activate several paravirt features within Windows by setting some CPU parameters referred to as Hyper-V enlightments.

Consider: [Cole Robinson's blog – Enabling Hyper-V enlightenments with KVM](http://blog.wikichoon.com/2014/07/enabling-hyper-v-enlightenments-with-kvm.html)

You can adjust your CPU settings like this:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-p"># Basic CPU settings.</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -cpu host,kvm=off,hv_spinlocks=0x1fff,hv_relaxed,hv_vapic,hv_time,hv_vendor_id=Nvidia43FIX”</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

However, **setting hv parameters will lead to an NVIDIA error 43 with newer driver versions!** Hence, we can use hv_vendor_id=Nvidia43FIX as a workaround. The latter only works with a newer QEMU version like 2.5.0 though.

"If you have an Intel CPU with APIC virtualization (starting with Ivy Bridge-E and Haswell), you should avoid using hv_vapic. It has more overhead than APICv, causing about 10-12% more VM exits." – according to Reddit user /u/glowtape.

Since the version in my repository didn't support this feature yet I compiled QEMU 2.5.0 on my own. You can find some very rudimentary advice on this in the Miscellaneous Section below.

Unfortunately, I don't have any information on the real performance gain here. If you have any please feel free to contribute them here.

### <span id="Disk_tweaks">Disk tweaks</span>

Apparently you can improve the disk IO performance by making use of the virtio drivers. You can either install them already during Windows installation by providing an ISO with the drivers as a secondary CDROM drive or by installing them afterwards.

You can download the drivers on the [Fedora wiki – Windows Virtio Drivers](https://fedoraproject.org/wiki/Windows_Virtio_Drivers#Direct_download) site.

The QEMU options for specifying a virtio drive would look something like this:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-p"># Make use of VIRTIO drivers for Windows.</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -object iothread,id=io1”</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -drive if=none,id=drive0,file=/dev/bcache/by-uuid/84ea4a08-eed7-4651-93b6-24d5ed6d570a,format=raw,aio=native,cache=none,cache.direct=on,discard=unmap”</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -device virtio-scsi-pci,id=scsi0,ioeventfd=on,iothread=io1,num_queues=4,bus=pcie.0”</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -device scsi-hd,drive=drive0,id=scsihd0”</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Creates an iothread to move the IO away from the main QEMU thread, then specifies a disk drive without interface and ties it to a virtio SCSI controller. The relevant virtio driver to install in Windows is the SCSI Passthrough one.

- Many thanks to Reddit user /u/glowtape for pointing this out and providing the basic parameters!

### <span id="Memory_tweaks">Memory tweaks</span>

#### <span id="Preallocation">Preallocation</span>

Similar to VMware or other virtualization software you can preallocate enough memory for the vm:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-p"># Assign 4 GB memory to the vm.</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -m 4000”</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -mem-prealloc”</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

#### <span id="Hugepages">Hugepages</span>

You can make use of hugepages to further improve ram performance. Please consider:

Invoke them in the QEMU script like this:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-p"># Make use of hugepages.</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -mem-path /dev/hugepages”</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

I haven't tested this yet. Nevertheless, this should be one of the tweaks with the biggest performance gain.

#### <span id="Other">Other</span>

- You should disable paging in the guest since the host system already takes care of that.

### <span id="V-CPU_pinning">V-CPU pinning</span>

You can assign specific virtual cores to the vm to further improve performace. I am starting my vm like this:

 | <div class="crayon-pre">
  <p>sudo -Hu qemu_vga bash -c “$VM_SOUND taskset -c 0-7 qemu-system-x86_64 $OPTS”</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------

Consider: [Linux KVM – Running your VM on specific CPUs](http://www.linux-kvm.com/content/tip-running-your-vm-specific-cpus) for more infos (thanks to the Archlinux forum user nbhs for his guide at this point)!

### <span id="QEMU_q35_machine_architecture">QEMU q35 machine architecture</span>

By default QEMU uses pc-i440fx-2.1\. Nevertheless, I often came across guides using the [QEMU q35 machine architecture](http://wiki.qemu.org/Features/Q35). It can be invoked like this:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-p"># Use another machine architecture and emulate PCI-E.</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -machine type=q35,accel=kvm”</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -device ioh3420,bus=pcie.0,addr=1c.0,multifunction=on,port=1,chassis=1,id=root.1”</span>
</p>
  <p>
  <span class="crayon-p"># VFIO GPU and GPU sound passthrough.</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -device vfio-pci,host=02:00.0,bus=root.1,addr=00.0,multifunction=on”</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -device vfio-pci,host=02:00.1,bus=root.1,addr=00.1”</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

I don't use this architecture and I didn't detect any real performance gains by using it. Please convince me from the opposite if you made different experiences.

### <span id="Network-2">Network</span>

To improve the network performance you can make use of virtio-net. In particular, you have to specify something like this:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-p"># Improve the network performance by utilizing virtio-net.</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -device virtio-net,netdev=net0,mac=de:ad:be:ef:33:4a,bus=pcie.0”</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -netdev tap,id=net0,ifname=vmtap0,script=./qemu-ifup,downscript=./qemu-ifdown”</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Where [qemu-ifup](http://pastebin.com/acHG4SXL) and [qemu-ifdown](http://pastebin.com/5zdnP740) are helper scripts. To make use of this you further need a bridge called br0 on your interface with internet access.

- Big thanks to Reddit user /u/glowtape for providing the scripts and information necessary for this section!

### <span id="More">More</span>

- <span>Please let me know if you know of any further tricks to improve the machine performance here.</span>

## <span id="Miscellaneous">Miscellaneous</span>

### <span id="Rebinding">Rebinding</span>

Apparently, it is also possible to assign the GPU to your host again once it has been used in the VM. I haven't tested this yet but according to Reddit user /u/glowtape you can just bind and unbind the VFIO drivers. This has the drawback that you have to stop XServer for rebinding.

Please refer to his [startup script](http://pastebin.com/Rb5d3nKm) to see how it can be done (links to the qemu-ifup and qemu-ifdown scripts can be found in the Network section above).

### <span id="OVMF">OVMF</span>

You can adjust the standard resolution during boot (e.g. when using QXL) in the bios settings (press del key on boot). Go to "Device Manager" – "OVMF Platform Configuration" – "Change Preferred" and commit changes. This is helpful when using QXL for any tasks like OS management or OS installation.

### <span id="QEMU_settings">QEMU settings</span>

In case you don't want the qemu_vga user to start a QEMU window or just don't want to grant it any XServer rights you can use

 | <div class="crayon-pre">
  <p>
  <span class="crayon-p"># Use an emulated video device (use none for disabled).</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -vga none”</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -nographic”</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### <span id="Monitor_switching">Monitor switching</span>

I don't use it since I have to press a button on the monitor to switch the input. However, there are enough guides describing how you can utilize "xrandr" if your monitor supports automatic signal switching.

- **Request:** Please feel free to contribute if you have a working solution.

### <span id="Debugging">Debugging</span>

You can add the following option to see some OVMF debug messages:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-p"># Activate QEMU debugging.</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -global isa-debugcon.iobase=0x402 -debugcon file:/tmp/qemu_vga.ovmf.log”</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### <span id="Using_libvirt_and_virt-manager">Using libvirt and virt-manager</span>

If you want a graphical interface for managing the vm you can try some of the following steps.

- **Attention:** This is an 

  <span>
    <strong>unfinished section</strong>
  </span>

  . I had too many problems doing this.

Consider the following resources:

First install virt-manager (this automatically will install dependencies like libvirt):

 | <div class="crayon-pre">
  <p>sudo apt-get install virt-manager</p>
</div>
 | --------------------------------------------------------------------------

We then need to convert our whole QEMU-CLI startup script to a XML representation:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-c"># Convert to virt-manager…</span>
</p>
  <p>
  <span class="crayon-r">echo</span>
  <span class="crayon-o">-</span>
  <span class="crayon-i">ne</span>
  <span class="crayon-s">“/usr/bin/qemu-system-x86_64 $OPTS”</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-v">vm</span>
  <span class="crayon-e">.args</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Already at this point I had to play around with the parameters since virsh was not able to convert all of them correctly. Once you have created the "vm.args" file though you can create a XML file by doing:

 | <div class="crayon-pre">
  <p>virsh domxml-from-native qemu-argv vm.args &gt; VM_SETTINGS.xml</p>
</div>
 | --------------------------------------------------------------------------------------------------------

You can then load this XML into the virt-manager by doing:

 | <div class="crayon-pre">
  <p>sudo cp VM_SETTINGS.xml /etc/libvirt/qemu/</p>
  <p>virsh define /etc/libvirt/qemu/VM_SETTINGS.xml virt-manager</p>
</div>
 | --------------------------------------------------------------------------------------------------------------------------------------------------------

Further, having installied libvirt and virt-manager might require you to setup some privileges:

Apparmor settings:

 | <div class="crayon-pre">
  <p>/etc/apparmor.d/abstractions/libvirt-qemu</p>
  <p>[…]</p>
  <p>/dev/vfio/* rw,</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------

then restart Apparmor with:

 | <div class="crayon-pre">
  <p>sudo /etc/init.d/apparmor restart</p>
</div>
 | --------------------------------------------------------------------------

Qemu permissions (make sure the number for the VFIO device in the last line is correct):

 | <div class="crayon-pre">
  <p>/etc/libvirt/qemu.conf</p>
  <p>—————————————–</p>
  <p>cgroup_device_acl = [</p>
  <p>    “/dev/null”, “/dev/full”, “/dev/zero”,</p>
  <p>    “/dev/random”, “/dev/urandom”,</p>
  <p>    “/dev/ptmx”, “/dev/kvm”, “/dev/kqemu”,</p>
  <p>    “/dev/rtc”,“/dev/hpet”, “/dev/vfio/vfio”,</p>
  <p>    “/dev/vfio/1”</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Finally, you should have an entry in your virt-manager or virsh. Nevertheless, several bugs with passing through the GPU device at this point ruined the fun for me here.

### <span id="Compile_QEMU_250">Compile QEMU 2.5.0</span>

- Make sure all QEMU dependencies are met.
- Lookup the correct make parameters for your distribution, e.g. on Launchpad like [here](https://launchpadlibrarian.net/234035056/buildlog_ubuntu-xenial-amd64.qemu_1%3A2.5+dfsg-1ubuntu3_BUILDING.txt.gz).
- Remove any existing QEMU packages with your package manager.
- Compile everything and run "make install".

  - **Attention:** There is no "make uninstall". You can make use of [checkinstall](https://www.howtoforge.com/howto_linux_debian_deb_checkinstall) to create a deb package first and install this one instead.

### <span id="Share_directories_and_files">Share directories and files</span>

Install samba and use the QEMU parameter:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-p"># Share a folder with our vm.</span>
</p>
  <p>
  <span class="crayon-v">OPTS</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“$OPTS -smb /directory/to/share”</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Then you can access the shared directory over netshare at the location: \\10.0.2.4\qemu

### <span id="Saving_the_machine_state">Saving the machine state</span>

Apparently there is no way like in VMware or Virtualbox to save the machine state. Executing "savevm test" in the QEMU window lead to the following error:

 | <div class="crayon-pre">
  <p>Device ‘pflash1’ is writable but does not support snapshots.</p>
</div>
 | -----------------------------------------------------------------------------------------------------

This error can be fixed by converting the OVMF variables file into the qcow2 format:

 | <div class="crayon-pre">
  <p>qemu-img convert -O qcow2 OVMF_VARS-pure-efi.fd OVMF_VARS-pure-efi.qcow2</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------

However, this only lead to a new error stating that the state of our passed through VFIO device couldn't be saved:

 | <div class="crayon-pre">
  <p>(qemu) savevm test</p>
  <p>State blocked by non-migratable device ‘0000:00:05.0/vfio-pci’</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------

I guess the VFIO save state support is lacking. The question is whether there is any kind of feature request so far?

- **Update:**<br>
  The Reddit user /u/SxxxX has pointed out that this seems to be a very complex issue both on the GPU side and software side of things. Nevertheless, we can hope that a clever solution / workaround to this problem can and will be found.

### <span id="Automatic_setup_script">Automatic setup script</span>

In addition to this guide an automatic script for guidance through the complete setup would be awesome. Please leave a comment or contact me if you are interested in setting something up.

## <span id="Discussion">Discussion</span>

### <span id="Other_virtualization_software">Other virtualization software</span>

- Using VMware for passthrough is only possible when you use VMware vSphere and special Nvidia cards like Quadro cards.<br>
  I could run VMware (unlike Virtualbox) with no problems in parallel to QEMU though.
- There is [unRAID](http://lime-technology.com/) which also uses QEMU and apparently can be used for passthrough purposes.
- I have read some articles about XEN successfully supporting GPU passthrough, too.

### <span id="Complicated_solutions">Complicated solutions</span>

During my research I have seen many things being done in a very complicated way. One examle is that you don't need pci-stub if your kernel already supports vfio-pci. Further, keep things simple by using short parameters like:

- -hda (only if you don't need performance gains through using virtio drivers)
- -cdrom
- -smb

## <span id="Issues">Issues</span>

Overall the VM is really feeling nice and responsive. However, there are some issues that I encountered so far and am trying to fix.

- Mouse microlags:<br>
  My mouse stutters in some games although being passed through (also without Synergy). This happens under a Windows or Linux guest.
- Running QEMU parallel to Virtualbox is a nogo:<br>
  This seems to be an issue since Virtualbox and QEMU both share KVM. No solution in sight here?

## <span id="Conclusion">Conclusion</span>

This whole setup was definitively one of the more/most frustrating things I have done so far. However, we need more articels and scripts to decrease the global frustration level while making awesome things like this. I hope this guide helps you to reduce your stress level and to create your own setup.

In conclusion I can only say: 

<span>In the end it is absolutely worth the struggle.</span>

 It can really make up for not using dual-boot and gets you a step further away from using Windows as the main OS.

<span>
  <br>
</span>

You can find my newest and optimized start script/s here:

[Github – Evonide's GPU passthrough](https://github.com/evonide/gpu-passthrough)

Last but not least:<br>
Many THANKS, KUDOS and SHOUTOUTS to all people who have created those awesome guides, have provided useful information or have struggled and felt the pain of setting up something like this. Finally, my biggest thanks go to my colleague Dario Weißer for doing the setup with Archlinux and helping me with many issues.

Please feel free to comment if you find any mistakes, if you want to share your own experiences or if you know about any other missing cool things.
