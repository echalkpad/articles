# How to Use SSH Pipes on Linux

[Original URL](https://www.maketecheasier.com/ssh-pipes-linux/)

> Like most commands on Linux, SSH can be used with input/output redirection via | (Unix Pipe). SSH can be used with this pipeline too. The basic concept here is understanding how the Unix pipeline...

Like most commands on Linux, SSH can be used with input/output redirection via `|` (Unix Pipe). SSH can be used with this pipeline too. The basic concept here is understanding how the Unix pipeline works.

When you understand the way pipes work, you can get seriously creative. This article covers what happens when you combine Unix pipes and SSH. It should be noted that since Unix pipes can be just about anything, there are no doubt going to be commands not on this list would also be useful.

Pipes on Unix (and by extension, Linux) are used to chain programs together and make them work together. For example, using `cat`, you can show the contents of a file, but if you used a pipe, you could chain the `cat` command to the `more` command to make the file easier to read through.

![ssh-pipes-cat-more-example](https://maketecheasier-2d0f.kxcdn.com/assets/uploads/2016/01/ssh-pipes-cat-more-example.jpg "ssh-pipes-cat-more-example")

The basic idea here is this: `program1 fileX | program2`. It's not just limited to one file and two programs, though. Piping can get about as advanced as you need it to be with as many modifiers as you can think of.

**Note:** Some types of pipes can be done without using the `|`. Some may use `>` instead.

Now that the Unix pipeline makes a little sense, let's see what we can do with the SSH protocol and pipes. Here's a list of some really great pipes that most will find useful when combining with SSH.

## 1\. Compressed file transfer

Forget using `scp` to transfer files; you can do it with a simple SSH pipe command. No need to install anything.

```
tar czf - /home/localuser/filefolder | ssh remote-machine@ip.address.of.remote.machine tar -xvzf -C /home/remoteuser/
```

![ssh-pipes-copy-file-compressed](https://maketecheasier-2d0f.kxcdn.com/assets/uploads/2016/01/ssh-pipes-copy-file-compressed.jpg "ssh-pipes-copy-file-compressed")

This uses the tar program to compress your data locally and then is piped over SSH. From there, the remote machine receives the file and extracts it to the folder you specified. You'll never actually see a .tar archive, but it makes use of one.

## 2\. Running a local script on a remote machine (or remote on local)

Got a script written on your computer and want to test it out really quickly? No need to push the file to it or anything like that. Just pipe your local file through SSH and run it this way instead!

```
ssh remoteuser@ip.address.of.server 'bash -s' < scriptfile.sh
```

With this command you remove the need to push files around to remote machines to execute shell scripts. It saves a lot of time in the long run.

## 3\. Remote hard drive backup

Want to back up your computer to your remote machine without taking the hard drive out physically and hooking it up? It's easy to do, and with an SSH pipe, no less. Here's how it works:

```
sudo dd if=/dev/sda | ssh remoteuser@ip.addresss.of.remote.machine 'dd of=sda.img'
```

This makes use of the `dd` command. It uses your local drive (sda) as the source, and then it pipes the output over SSH to be written to a raw image file.

![ssh-pipes-backup-hard-drive](https://maketecheasier-2d0f.kxcdn.com/assets/uploads/2016/01/ssh-pipes-backup-hard-drive.jpg "ssh-pipes-backup-hard-drive")

**Note:** The drive you may want to back up might have a different denotation. Use the `lsblk` command to figure out what drive you're looking to back up. This command will tell you what `/dev/` to use in the `if=` part for the command above.

## 4\. Remote hard drive restoration

Want to restore that image you just backed up to your machine? It's easy. This time the command works in reverse. Again, if the drive you are restoring to is named differently than what is listed in the example, use the `lsblk` command to find out what `/dev/` it's listed as.

```
ssh remoteuser@ip.address.of.remote.machine 'dd if=sda.img' | dd of=/dev/sda
```

![ssh-pipes-hard-drive-backup-img](https://maketecheasier-2d0f.kxcdn.com/assets/uploads/2016/01/ssh-pipes-hard-drive-backup-img.jpg "ssh-pipes-hard-drive-backup-img")

Run this command, and the .img file you created will be restored over the network to the hard drive that you specify.

## 5\. Send a file

Sending a single file over SSH is easy. Here's how to do it with pipes.

```
cat file | ssh remoteuser@ip.address.of.remote.machine "cat > remote"
```

This command makes use of the cat command to send a file through a pipe. You can also retrieve that file with the following command:

```
ssh remoteuser@ip.address.of.remote.machine "cat > remote" < file
```

Though it might not seem that impressive, pipes can simplify and transform the way you use commands on Linux. Though this list highlights some of the most useful, it's only the tip of the iceberg. With how versatile the vertical bar is, the possibilities for piping things through SSH are endless.

Know any good SSH piping commands? Tell us below!

Image Credit: [Wikimedia commons](https://upload.wikimedia.org/wikipedia/commons/2/25/Network-cables-1.png)
