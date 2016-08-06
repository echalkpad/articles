# Guide to Accessing the Dark Web

[Original URL](https://hacked.com/wiki/Guide_to_Accessing_the_Dark_Web)

> The dark web is a vast and lawless section of the Internet. It is not to be confused with the deep web The very purpose of the hidden Internet is to avoid censorship, so the first thing to get rid of...

The [dark web](https://hacked.com/wiki/index.php?title=Dark_web&action=edit&redlink=1 "Dark web (page does not exist)") is a vast and lawless section of the Internet. It is **not** to be confused with the [deep web](https://hacked.com/wiki/index.php?title=Deep_web&action=edit&redlink=1 "Deep web (page does not exist)") The very purpose of the hidden Internet is to avoid censorship, so the first thing to get rid of is your notion that things are under control. Browsing the dark web may result in seeing things that offend or shock you.

[Tor](https://hacked.com/wiki/index.php?title=Tor&action=edit&redlink=1 "Tor (page does not exist)") and the [Tor Browser](https://hacked.com/wiki/Tor_Browser "Tor Browser") can be used without accessing the dark web at all, for the purpose of privacy. Even Facebook offers a hidden server. The primary usefulness to the average person using Tor is that their IP will not be able to be tracked by the sites they visit, and their traffic will be relatively anonymous.

This guide will focus on simply using the [Tor browser](https://hacked.com/wiki/index.php?title=Tor_browser&action=edit&redlink=1 "Tor browser (page does not exist)") rather than setting up a VPN and Tor networking. Future guides will cover these topics in better detail.

## <span id="Requirements" class="mw-headline">Requirements</span>

While [Tor browser](https://hacked.com/wiki/index.php?title=Tor_browser&action=edit&redlink=1 "Tor browser (page does not exist)") is available on both [Windows](https://hacked.com/wiki/index.php?title=Windows_vulnerabilities&action=edit&redlink=1 "Windows vulnerabilities (page does not exist)") and [Mac OSX](https://hacked.com/wiki/index.php?title=Mac_OSX&action=edit&redlink=1 "Mac OSX (page does not exist)"), it is not recommended to use these operating systems. Not just for browsing the web, but for anything.

Closed source software is inherently less safe than open source software for the simple reason that anyone can audit the security features of open source software, whereas bugs in closed source software must first do damage and be detected by users before they are even noticed. While that may seem a mischaracterization, it's actually how things usually work out. It will never be superior to hire an army of bug testers versus have the entire world enabled to audit code. **In any case, if you must, you are free to use the aforementioned operating systems. But be warned that bugs beyond the control of the [Tor project](https://hacked.com/wiki/index.php?title=Tor_project&action=edit&redlink=1 "Tor project (page does not exist)") might actually uncloak you long before a government agent or other ambiguous actor.**

If your computer is capable of running the latest version of Mozilla Firefox, then it is capable of running the latest version of the Tor Browser. In fact, Tor Browser is slightly lighter in that it has less bells and whistles that are privacy invasive, and allows for fewer extensions that might compromise its usage.

## <span id="Installation" class="mw-headline">Installation</span>

### <span id="Official_Documents" class="mw-headline">Official Documents</span>

**Linux**

- [Generic](https://www.torproject.org/docs/tor-doc-unix.html.en)
- [Debian/Ubuntu/DEB-based](https://www.torproject.org/docs/debian.html.en)
- [Fedora/CentOS/RPM-based](https://www.torproject.org/docs/rpms.html.en)

### <span id="Official_Downloads" class="mw-headline">Official Downloads</span>

Do not download Tor Browser from sites like Download.com or anywhere else. Rather, use only the [official site](https://www.torproject.org/download/download-easy.html.en).

Once you've downloaded the software, you can verify it is legitimate with the Tor Project's GPG signatures.

#### <span id="Verifying_Signatures" class="mw-headline">Verifying Signatures</span>

- On the same page as your download, get the [GPG](https://hacked.com/wiki/index.php?title=GPG&action=edit&redlink=1 "GPG (page does not exist)") signature file:

[![Tor guide verify1.png](https://hacked.com/wiki/images/e/e5/Tor_guide_verify1.png)](https://hacked.com/wiki/File:Tor_guide_verify1.png)

- Save it to the same directory as the Tor download.

<!--  -->

- Using GPG tools, verify it with the following procedure:

<!--  -->

```
- Mac and Linux
```

- Make sure you have [GPG Tools](http://gpgtools.org) (on Mac) or [GnuPG](https://gnupg.org/) (on Linux) installed. Most distributions of Linux include Gnu Privacy Guard suite, which is basically all you need.
- Import the public key of the Tor project: `gpg --keyserver x-hkp://pool.sks-keyservers.net --recv-keys 0x4E2C6E8793298290`
- Verify on Linux: `gpg --verify ~/DownloadLocation/tor-browser-linux*.tar.xz{.asc*,}`
- Verify on Mac: `gpg --verify ~/DownloadLocation/TorBrowser-VERSION-osx*_en-US.dmg{.asc*,}`

This should output a good signature:

[![Tor guide verify2.png](https://hacked.com/wiki/images/thumb/6/6f/Tor_guide_verify2.png/625px-Tor_guide_verify2.png)](https://hacked.com/wiki/File:Tor_guide_verify2.png)

**Note: Be sure to replace "DownloadLocation" with the actual location of your download.**

1. Get [GPG for Windows](http://gpg4win.org/download.html)
2. Open a Windows command line – `cmd.exe`
3. Import the Tor Project signature: `"C:\Program Files\Gnu\GnuPg\gpg.exe" --keyserver x-hkp://pool.sks-keyservers.net --recv-keys 0x4E2C6E8793298290`
4. Verify the Signature Using GPG for Windows: `"C:\Program Files\Gnu\GnuPg\gpg.exe" --verify C:\DownloadLocation\torbrowser-install-VERSION_en-US.exe.asc C:\DownloadLocation\torbrowser-install-VERSION_en-US.exe`
5. Make sure that "Good Signature" is output.
6. If you have an error, check that you've used the proper **DownloadLocation** and **VERSION** replacements in the commands above (if you copy-pasted them). The version as of this writing was 5.0.6\. You are also able to rename the files to whatever you like – their filename has no bearing on their validity.

### <span id="Install" class="mw-headline">Install</span>

_DEB_

On Debian-based systems, you have the option of installing the Official Tor Repository and downloading from it directly. This will save time and verification will be done automatically.

- Debian: `nano /etc/apt/sources.list`

Insert:

`deb http://deb.torproject.org/torproject.org jessie main deb-src http://deb.torproject.org/torproject.org jessie main`

Note: if you are running a different version of Ubuntu, such as Utopic, simply replace "trusty" with "utopic," or whichever version you have – for best results, that is. In some cases, repos will work anyways.

`Ctrl+O`

`apt-get update`

`apt-get install tor`

`nano /etc/apt/sources.list`

Insert:

`deb http://deb.torproject.org/torproject.org trusty main deb-src http://deb.torproject.org/torproject.org trusty main`

Note: if you are running a different version of Ubuntu, such as Utopic, simply replace "trusty" with "utopic," or whichever version you have – for best results, that is. In some cases, repos will work anyways.

`Ctrl+O`

`apt-get update`

`apt-get install tor`

Use this repo file on RPM-based distributions, modifying it for your distribution:

`[tor] name=Tor repo enabled=1 baseurl=https://deb.torproject.org/torproject.org/rpm/DISTRIBUTION/$basearch/ gpgcheck=1 gpgkey=https://deb.torproject.org/torproject.org/rpm/RPM-GPG-KEY-torproject.org.asc repo_gpgcheck=1`

[tor-source] name=Tor source repo enabled=1 autorefresh=0 baseurl=<https://deb.torproject.org/torproject.org/rpm/DISTRIBUTION/SRPMS> gpgcheck=1 gpgkey=<https://deb.torproject.org/torproject.org/rpm/RPM-GPG-KEY-torproject.org.asc> repo_gpgcheck=1

1. Get [Macports](https://www.macports.org/install.php)
2. Open a terminal and type: `sudo port install tor`

[Download and install Tor Browser](https://www.torproject.org/projects/torbrowser.html.en#windows) the same way you would any other program. If you need help installing programs, you're in real trouble, but [check this guide out](http://windows.microsoft.com/en-us/windows/install-program#1TC=windows-7).

## <span id="Making_Use_of_the_Dark_Web" class="mw-headline">Making Use of the Dark Web</span>

_Disclaimer: this tutorial is not intended to enable criminal activity. Behave responsibly._

### 

The [dark web](https://hacked.com/wiki/index.php?title=Dark_web&action=edit&redlink=1 "Dark web (page does not exist)") has no Google to help you get around. By design, it is difficult to index hidden sites. Therefore, as a replacement, the Hidden Wiki exists.

There are two versions of the Hidden Wiki. The one we will mostly focus on is a "censored" version, which essentially means that links to child pornography and other illegal content are not allowed. The other is the "uncensored" version, which is what it sounds like. We will provide a screenshot of both.

You might note that the address is strange. That's because it's actually an obfusication of the IP address that is serving the content, rather than a domain name. All Tor hidden service URLs look something like this, and similar strategies are used on [Freenet](https://hacked.com/wiki/index.php?title=Freenet&action=edit&redlink=1 "Freenet (page does not exist)") and [i2p](https://hacked.com/wiki/index.php?title=I2p&action=edit&redlink=1 "I2p (page does not exist)").

[![Tor guide hidden wiki.png](https://hacked.com/wiki/images/thumb/1/1b/Tor_guide_hidden_wiki.png/625px-Tor_guide_hidden_wiki.png)](https://hacked.com/wiki/File:Tor_guide_hidden_wiki.png)

It functions just like any other wiki site, allowing one to search for things that have been listed:

[![Tor guide hidden wiki2.png](https://hacked.com/wiki/images/thumb/4/4c/Tor_guide_hidden_wiki2.png/625px-Tor_guide_hidden_wiki2.png)](https://hacked.com/wiki/File:Tor_guide_hidden_wiki2.png)

The Hidden Wiki is the most reliable way to find links to other content on the dark web. It is generally patrolled for illegal content that might otherwise get you in trouble.

#### <span id="Uncensored" class="mw-headline">Uncensored</span>

This page is less scrupulous about the content it will host. Be warned, some things you will find through it are less than savory.

[![Tor guide hidden wiki3.png](https://hacked.com/wiki/images/thumb/f/ff/Tor_guide_hidden_wiki3.png/625px-Tor_guide_hidden_wiki3.png)](https://hacked.com/wiki/File:Tor_guide_hidden_wiki3.png)

### <span id="Other_Dark_Web_Sites" class="mw-headline">Other Dark Web Sites</span>

### <span id="Note" class="mw-headline">Note</span>

Tor hidden servers frequently go down for various reasons. If you want to use the Dark Web properly, you will need to make some friends there.
