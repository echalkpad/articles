# maxogden/linux

[Original URL](https://github.com/maxogden/linux)

> pre-alpha, proceed with caution npm installs hypercore linux (based on tiny core linux) and runs it using the new Mac OS Yosemite hypervisor (via xhyve). This module is a low level component that is...

**pre-alpha, proceed with caution**

npm installs [hypercore linux](https://github.com/maxogden/hypercore) (based on [tiny core linux](http://tinycorelinux.net/)) and runs it using the new Mac OS Yosemite hypervisor (via [xhyve](https://github.com/mist64/xhyve)).

This module is a low level component that is part of [HyperOS](http://hyperos.io/). We are working on integrating the other HyperOS components to support advanced functionality like running containers, sharing filesystems etc.

Mac OS Yosemite only for now, Windows support coming later through Hyper-V integration (see [this issue](https://github.com/maxogden/linux/issues/4) if you wanna help)

## [](https://github.com/maxogden/linux#warning)**WARNING**

- xhyve is a very new project, expect bugs! You must be running OS X 10.10.3 Yosemite or later and 2010 or later Mac for this to work.
- if you happen to be running any version of VirtualBox prior to 5.0 then xhyve will crash your system either if VirtualBox is running or had been run previously after the last reboot (see xhyve's issues [#5](https://github.com/maxogden/linux/blob/master/mist64/xhyve#5) and [#9](https://github.com/maxogden/linux/blob/master/mist64/xhyve#9) for the full context). So, if you are unable to update VirtualBox to version 5, or later, and were using it in your current session please do restart your Mac before attempting to run xhyve.
- (these warnings were borrowed from [coreos-xhyve](https://github.com/coreos/coreos-xhyve))

[![js-standard-style](https://camo.githubusercontent.com/e06d9d72eecca61c1ba39fdf19868f70fcb3a9b3/68747470733a2f2f63646e2e7261776769742e636f6d2f6665726f73732f7374616e646172642f6d61737465722f62616467652e737667)](https://github.com/feross/standard) [![Build Status](https://camo.githubusercontent.com/9ab9d84aaf2fc38d762363d7d72471f90642502a/68747470733a2f2f7472617669732d63692e6f72672f6d61786f6764656e2f6c696e75782e7376673f6272616e63683d6d6173746572)](https://travis-ci.org/maxogden/linux)

### [](https://github.com/maxogden/linux#installation)installation

```
npm install linux -g
```

### [](https://github.com/maxogden/linux#usage)usage

```
$ linux
Usage: linux <command> [args...]

Commands:
 init creates a new ./linux folder in this directory to hold config
 boot boots up linux from config in ./linux
 status checks if linux is running or not
 ssh sshes into linux and attaches the session to your terminal
 run runs a single command over ssh
 halt runs sudo halt in linux, initiating a graceful shutdown
 kill immediately ungracefully kills the linux process with SIGKILL
 pid get the pid of the linux process
```

### [](https://github.com/maxogden/linux#example)example

```
# initialize a linux folder to hold state
$ linux init
Created new config folder at /Users/max/test/linux

# starts a linux daemon
$ sudo linux boot
Linux has booted { ip: '192.168.64.127',
 hostname: 'simon-mittens-snuggles-toby',
 pid: 20665 }

# ssh login
$ linux ssh
Warning: Permanently added '192.168.64.127' (ECDSA) to the list of known hosts.
 __ __ __
/ \__/ \__/ \__ Welcome to HyperOS Linux! (Based on TinyCore Linux)
\__/ \__/ \__/ \ hyperos.io tinycorelinux.net
 \__/ \__/ \__/
tc@simon-mittens-snuggles-toby:~$ pwd
/home/tc
tc@simon-mittens-snuggles-toby:~$ exit
Connection to 192.168.64.127 closed.

# run a single command over ssh
$ linux run uname -a
Linux simon-mittens-snuggles-toby 3.16.6-tinycore64 #777 SMP Thu Oct 16 10:21:00 UTC 2014 x86_64 GNU/Linux

$ linux status
Linux is running { pid: 20665 }

# gracefully shutdown
$ linux halt

$ linux status
Linux is not running
```
