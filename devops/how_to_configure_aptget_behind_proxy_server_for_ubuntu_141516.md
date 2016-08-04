# How to Configure apt-get behind proxy server for Ubuntu 14/15/16

[Original URL](http://www.unixmen.com/45713-2/)

> After Installing Ubuntu 16.04 to make some monitoring tests, I was not able to get or update behind the http/https/ftp proxy . Here how i solved this.i think this solution should work...

[![](http://1969324071.rsc.cdn77.org/wp-content/uploads/2014/05/apt.png "apt")](http://1969324071.rsc.cdn77.org/wp-content/uploads/2014/05/apt.png)

After Installing Ubuntu 16.04 to make some monitoring tests, I was not able to get or update behind the http/https/ftp proxy . Here how i solved this.

i think this solution should work also for Debian 7/8 and LinuxMint 16/17

**my machine :**

![ubuntu16](http://1969324071.rsc.cdn77.org/wp-content/uploads/2016/03/ubuntu16.png)

**Trying to update**

```
[email protected]:~# apt-get  update
Err:1 http://security.ubuntu.com/ubuntu xenial-security InRelease
  Could not resolve 'security.ubuntu.com'
Err:2 http://us.archive.ubuntu.com/ubuntu xenial InRelease
  Could not resolve 'us.archive.ubuntu.com'
Err:3 http://us.archive.ubuntu.com/ubuntu xenial-updates InRelease
  Could not resolve 'us.archive.ubuntu.com'
Err:4 http://us.archive.ubuntu.com/ubuntu xenial-backports InRelease
  Could not resolve 'us.archive.ubuntu.com'
Reading package lists... Done
W: Failed to fetch http://us.archive.ubuntu.com/ubuntu/dists/xenial/InRelease  Could not resolve 'us.archive.ubuntu.com'
W: Failed to fetch http://us.archive.ubuntu.com/ubuntu/dists/xenial-updates/InRelease  Could not resolve 'us.archive.ubuntu.com'
W: Failed to fetch http://us.archive.ubuntu.com/ubuntu/dists/xenial-backports/InRelease  Could not resolve 'us.archive.ubuntu.com'
W: Failed to fetch http://security.ubuntu.com/ubuntu/dists/xenial-security/InRelease  Could not resolve 'security.ubuntu.com'
W: Some index files failed to download. They have been ignored, or old ones used instead.
[email protected]:~#
```

**Follow the steps bellow to be able to execute apt-get behind a proxy:**

Create new conf empty file inside /etc/apt/ and do the following

```
$ sudo vi /etc/apt/apt.conf
```

add this line to the file if you are using http proxy else https of ftp:

```
Acquire::http::Proxy "http://user:[email protected]_host:port";

Acquire::http::Proxy "http://Domain\user:[email protected]_host:port";
```

Now export the proxy line with:

```
export http_proxy=http://user:[email protected]_host:port

export http_proxy=http://Domain\user:[email protected]_host:port
```

To make this command permanent add it to **~/.bash.rc** in your home directory:

```
vi ~/.bash.rc
```

**Now Check and restart the updates:**

```
[email protected]:~# apt-get  update
Hit:1 http://security.ubuntu.com/ubuntu xenial-security InRelease
Get:2 http://us.archive.ubuntu.com/ubuntu xenial InRelease [95.8 kB]
Hit:3 http://us.archive.ubuntu.com/ubuntu xenial-updates InRelease
Hit:4 http://us.archive.ubuntu.com/ubuntu xenial-backports InRelease
Get:5 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 Packages [1,442 kB]
Get:6 http://us.archive.ubuntu.com/ubuntu xenial/main i386 Packages [1,437 kB]
Get:7 http://us.archive.ubuntu.com/ubuntu xenial/main Translation-en [842 kB]
Get:8 http://us.archive.ubuntu.com/ubuntu xenial/universe amd64 Packages [7,353 kB]
Get:9 http://us.archive.ubuntu.com/ubuntu xenial/universe i386 Packages [7,343 kB]
53% [9 Packages 3,002 kB/7,343 kB 41%]
```

**Upgrade Now:**

```
[email protected]:~# apt-get  upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages will be upgraded:
  ubuntu-minimal ubuntu-standard
2 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
Need to get 6,944 B of archives.
After this operation, 0 B of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 ubuntu-minimal amd64 1.349 [3,442 B]
Get:2 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 ubuntu-standard amd64 1.349 [3,502 B]
Fetched 6,944 B in 0s (21.9 kB/s)
(Reading database ... 87004 files and directories currently installed.)
Preparing to unpack .../ubuntu-minimal_1.349_amd64.deb ...
Unpacking ubuntu-minimal (1.349) over (1.348) ...
Preparing to unpack .../ubuntu-standard_1.349_amd64.deb ...
Unpacking ubuntu-standard (1.349) over (1.348) ...
Setting up ubuntu-minimal (1.349) ...
Setting up ubuntu-standard (1.349) ...
[email protected]:~#
```

**To use wget behind proxy**

```
sudo vi /etc/wgetrc
https_proxy  = http://username:[email protected]:port
http_proxy = http://username:[email protected]:port
http_proxy = http://Domain\username:[email protected]:port # (in case of LDAP)
ftp_proxy = http://username:[email protected]:port
Uncomment 'use_proxy = on'
```

**Now try to use wget**

```
[email protected]:~# wget http://repo.zabbix.com/zabbix/2.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_2.4-1+trusty_all.deb
--2016-03-03 06:05:26--  http://repo.zabbix.com/zabbix/2.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_2.4-1+trusty_all.deb
Connecting to 10.133.80.35:8080... connected.
Proxy request sent, awaiting response... 200 OK
Length: 2674 (2.6K) [application/x-debian-package]
Saving to: ‘zabbix-release_2.4-1+trusty_all.deb’
```

**Please remember this a real TEST behind a real proxy servers with Domain/user and password needed.**

Enjoy!
