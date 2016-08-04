# Writing my own init with Go - Part 1

[Original URL](http://www.mustafaak.in/2016/02/08/writing-my-own-init-with-go.html)

> 08 Feb 2016 You may be wondering why would someone need to do that. I just want to experiment with Linux and build stuff with Go, so this is a nice option. I also found Linux distros to be completely...

08 Feb 2016

You may be wondering why would someone need to do that. I just want to experiment with Linux and build stuff with Go, so this is a nice option. I also found Linux distros to be completely confusing. So many files, a lot of stuff going on, weird and different type of config files of everything in system. Also, I hate my network-manager service in Ubuntu that it cannot reflect the changes in `/etc/network/interfaces` without a damn reboot. I know ifdown and ifup fixes it. But in general, I found Linux distros to be complicated. There are obviously many valid reasons why things are like what are they are now, I am no one to judge. But I am unhappy how some stuff is handled, and I just want to build my minimal distro (not another kernel because Linux is mostly perfect as it is) and see what are the challenges are hopefully learn more deep knowledge in C, Linux and presumably Go. This is nothing more than a hobby. The reason I have chosen Go is that it is a promising systems-like language and can build both completely static or

There have been many things said about systemd and I had no idea what was the fuss about. I wondered, how does even one write a init process? This is the PID 1, the overseer. Obviously from some service files or rc directories, it spawns some services based on their order. It also spawns several getty processes for interactive user inputs. Also it is the parent of all the processes in the system, so it cannot die. It also handles some mounts like cgroups or the partitions in the `/etc/fstab`. Well, at least these are the responsibilities I know and there are probably many more. For starters, I just want to print hello world on that console.

For starters, I will be using Qemu, KVM and C. I will use direct kernel booting of KVM for skipping boot complexities and a cleaner disk image. I copied my intird and vmlinuz from /boot. We could also compile the latest kernel, but this is a future work. I just want to be sure that it works. To test direct kernel booting, I downloaded Ubuntu cloud image, and it boots and gets in the cloud-init stuff. So, I am in the right direction.

Firstly, I created a disk image and mounted it with the help of `qemu-nbd`.

```
$ qemu-img create -f qcow2 disk.img 1G
$ qemu-nbd -c /dev/nbd0 disk.img 
$ fdisk /dev/nbd0 
# Build a DOS disklabel and create a partition, shortcuts o, n, p, <enter>, <enter>, w
$ mkfs -t ext4 /dev/nbd0p1
$ mount -t ext4 /dev/nbd0p1 disk/
$ mkdir disk/{dev,proc,sys} # initramfs mounts them
```

Time to boot it! I will use KVM directly, and I will use no grahpics to get only serial output so I can copy paste it easily. The correct invocation is the following:

```
$ kvm -m 1G -nographic -kernel vmlinuz-3.19.0-32-generic -initrd initrd.img-3.19.0-32-generic -append "console=ttyS0 root=/dev/sda1 init=/myinit selinux=0" -hda disk.img
```

And the kernel output after a while is:

```
[ 6.435500] EXT4-fs (sda1): mounted filesystem with ordered data mode. Opts: (null)
Begin: Running /scripts/local-bottom ... done.
done.
Begin: Running /scripts/init-bottom ... done.
Target filesystem doesn't have requested /myinit.
No init found. Try passing init= bootarg.
[ 6.483805] hidraw: raw HID events driver (C) Jiri Kosina
[ 6.487392] usbcore: registered new interface driver usbhid
[ 6.488321] usbhid: USB HID core driver


BusyBox v1.21.1 (Ubuntu 1:1.21.0-1ubuntu1) built-in shell (ash)
Enter 'help' for a list of built-in commands.

(initramfs) ls
root scripts lib bin usr conf sys tmp
run init var lib64 etc sbin proc dev
(initramfs) ls -al /dev/sda*
brw------- 1 8, 1 /dev/sda1
brw------- 1 8, 0 /dev/sda
```

Great, it booted. However, since `myinit` does not exist yet, It is unable to continue to boot and drops into BusyBox for me to solve it. I will write a simple C code to output Hello World to console. To kill your VM without closing terminal, you can do `killall qemu-system-x86_64` from another terminal.

```
#include <stdio.h>
#include <stdlib.h>

int main(){
 fprintf(stderr, "Hello world");

 while(1);
}
```

The stdout is not open yet and we need to use stderr for now. Also stdin is not open, there we cannot get any input yet.

```
$ gcc -static -O0 myinit.c -o myinit
$ ldd myinit
 not a dynamic executable
$ killall qemu-system-x86_64 
$ sync # to ensure the VM sees the reflected binary.
```

And now, it boots!

```
[ 6.364921] EXT4-fs (sda1): INFO: recovery required on readonly filesystem
[ 6.366634] EXT4-fs (sda1): write access will be enabled during recovery
[ 6.369799] EXT4-fs (sda1): recovery complete
[ 6.372181] EXT4-fs (sda1): mounted filesystem with ordered data mode. Opts: (null)
Begin: Running /scripts/local-bottom ... done.
done.
Begin: Running /scripts/init-bottom ... done.
Hello world
```

It works! I am so happy. Currently, the code waits on `while(1)` loop and its my whole virtual cpu. Now, it is time to test Go! The Go init code is below:

```
packagemainimport("log""os""time")funcmain(){log.SetOutput(os.Stderr)log.Println("Hello world from Go!")time.Sleep(time.Second*3)}
```

The reason that I wrote sleep 3 seconds is that I want to see how the kernel reacts when init dies. We complie it with the following flags to ensure we do not have anything static. (Normally, it works if we just use `go build` (my env might be dirty) but I suspect that if I use something using C bindings, they will be included as dynamic libs, i.e. it happens when you use net package due to glibc licensing I guess, but not sure). I want everything right now to be static because I do not want to have any libraries floating around. I have someting in my mind for them too, however it is complicated right now and I leave it for the future posts.

```
$ CGO_ENABLED=0 go build -a -installsuffix cgo -ldflags '-s' myinit.go
$ ldd myinit
 not a dynamic executable
$ killall qemu-system-x86_64 
$ sync 
```

Booting it right now gives the following output:

```
[ 6.369855] EXT4-fs (sda1): mounted filesystem with ordered data mode. Opts: (null)
Begin: Running /scripts/local-bottom ... done.
done.
Begin: Running /scripts/init-bottom ... done.
2016/02/07 22:04:26 Hello world from Go!
[ 9.420099] Kernel panic - not syncing: Attempted to kill init! exitcode=0x00000000 
```

Yeah, it worked! Kernel panics after approximately 3 seconds, because of init died, and it is expected. Now I can write my init in a considerably higher language! This was the first proof of concept, that says only a Hello world. But it is the beginning. For my next post(s) I will do the following:

- Figure out how to use stdin properly.
- Spawn another process on disk and set its stdout and stderr to files.
- Create a .yaml config file to implement service functionality
- Get an IP from DHCP server
- Outside communication with init via HTTP
- Hopefully use tty for better interactivity.
- SSH Server!

I am not doing these things because I already know them, but I am doing to learn them. I just want to share my experiences with whole world! Any positive input would be greatly appreciated. I plan to update my findings weekly or bi-weekly. I am currently reading SysVInit and Upstart souce to help me (but no SystemD, it is very complicated for my eyes) so I know what a proper init should do.
