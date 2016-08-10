# How To Check DiskSpace With The Ncdu Utility

[Original URL](https://www.unixmen.com/check-diskspace-ncdu-utility/)

> Dear linux geeks, In this tutorial, I will teach you how to view and analyze disk space usage on Linux with a nice command line utility called Ncdu. I find the Ncdu utility very useful when it comes...

[![](https://dwlvaia9r6jw6.cloudfront.net/wp-content/uploads/2014/02/disk-space.png "disk space")](https://dwlvaia9r6jw6.cloudfront.net/wp-content/uploads/2014/02/disk-space.png)

Dear linux geeks,

In this tutorial, I will teach you how to view and analyze disk space usage on Linux with a nice command line utility called Ncdu. I find the **Ncdu** utility very useful when it comes to tracking down space consuming files and directories. It is very easy to learn and use.

According to the description in the manual pages, **ncdu** (**NC**urses **D**isk **U**sage) is a curses-based version of the well-known 'du', and provides a fast way to see what directories are using your disk space.

But one may ask, there are many tools for checking the diskspace, why did the author write this tool? There are many reasons, but the most important one is to provide a very good and fast tool to the Linux community. The Ncdu's author was not happy with the existing tools so he decided to write a new one in C programming language with an ncurses interface aimed to be run on a remote server where you don't have an entire graphical setup.

If you are using Ubuntu then open a new terminal(CTRL+ALT+T), and use the following command to install ncdu tool in your machine.

```
sudo apt-get install ncdu
```

The Ncdu tool has been packaged for many linux distributions such as Debian, Puppy Linux, Slackware, Gentoo and many others. You can read the full list of the linux distributions below.

– AgiliaLinux<br>
– AIX<br>
– Alpine Linux<br>
– ALT Linux<br>
– Arch Linux<br>
– CRUX<br>
– Cygwin<br>
– Debian<br>
– Fedora<br>
– FreeBSD<br>
– Frugalware<br>
– Gentoo<br>
– GNU Guix<br>
– IPCop<br>
– Puppy Linux<br>
– Slackware<br>
– Ubuntu<br>
– Zenwalk

After the installation of the tool has been finished, open a new terminal( CTRL+ALT+T in Ubuntu). and run the following command to gather information on disk usage.

```
ncdu
```

After running the above command ncdu will start to calculate diskspace and update the screen 10 times a second by default, but you can use the -q option to to save bandwidth over remote connections. When using the ncdu utility with the **-q** option the tool will decrease the update of the screen. From 10 times a second by default it will be decreased to once every 2 seconds in quiet mode.

```
ncdu -q
```

When combined with the **-x** option, the ncdu utility will only count files and directories on the same filesystem as the specified dir.

```
ncdu -q -x /home/oltjano/Desktop/library/
```

The **-h** option is very useful because it prints a small help message on the screen. Every time, I practise a new command line utility I use the -h option if it is available in order to avoid the mistakes and get the best out of it. Another nice option to know is the **-v** option, it is used to print the version of the tool.

```
 ncdu -v
```

After running the above command, I get information about the version of the Ncdu utility I am using on my Linux machine. It is very good to know what version of the tool you are using so you can get the latest features, bug fixes and updates.

```
 ncdu 1.8
```

As you guys can see from the above output, I am running ncdu version 1.8.

If you like to learn how to use the **Ncdu** tool, use the following command to find more information about its options and arguments.

```
man ncdu
```
