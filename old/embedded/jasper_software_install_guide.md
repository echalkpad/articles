# Software Install Guide

[Original URL](http://jasperproject.github.io/documentation/installation/)

> There are three ways to install Jasper on your Raspberry Pi. The quickest way to get up and running with Jasper is to download the pre-compiled disk image available here for Model B. There is also an...

There are three ways to install Jasper on your Raspberry Pi.

The quickest way to get up and running with Jasper is to download the pre-compiled disk image [available here for Model B](http://sourceforge.net/projects/jasperproject/files/jasper-disk-image.tar.gz/download). There is also an unofficial image for the B+ [available here](https://groups.google.com/forum/#!topic/jasper-support-forum/xHgonA-TZYw). After imaging your SD card, clone the repository and install the Python dependencies as described in [Install Jasper](http://jasperproject.github.io/documentation/installation/#install-jasper). Then, skip to [Configuration](http://jasperproject.github.io/documentation/configuration/).

If you want to understand how all of the supporting libraries are compiled on the Raspberry Pi, Method 3 may be to your liking (or, at the very least, helpful for debugging).

Unfortunately, there are currently no packages available for Debian or Raspbian. Please use [Method 3](http://jasperproject.github.io/documentation/installation/#manual-installation).

If you're using ArchLinux, there are packages available in the [Arch User Repository](https://aur.archlinux.org/packages/jasper-voice-control-git/). To install them:

```
yaourt -S jasper-voice-control-git
yaourt -S jasper-plugins
```

You'll also need a Text-to-Speech (TTS) and a Speech-to-Text (STT) engine. Check out the configuration section to learn what STT/TTS engines are and what you need to do to use them.

After you've done that, you can start Jasper as a systemd service:

```
sudo systemctl start jasper-voice-control
```

If the systemd service keeps failing, your audio device might already be in use by MPD, Pulseaudio, your Desktop Environment or some other process. In this case, start Jasper as your current user:

```
mkdir -p ~/.jasper
cp -r /var/lib/jasper/.jasper/profile.yml ~/.jasper
jasper-voice-control
```

Follow these instructions only if you wish to compile your Jasper software from scratch. These steps are unnecessary if you follow the recommended "Quick Start" instructions above.

We'll first clear the SD card using [GParted](http://gparted.org) on Ubuntu, but you can use an equivalent utility or operating system. In GParted: right-click on each partition of the SD card, then select 'Unmount' and 'Delete'. Apply the changes with Edit > Apply All Operations.

Download Raspbian Wheezy from <http://downloads.raspberrypi.org/raspbian_latest>. While we've tested Jasper on the 2014-01-07 release, newer releases may also work.

We'll use dd to burn the image to the disk. Obtain the address of the SD card with:

```
sudo fdisk -l
```

Our address was '/dev/mmcblk0', so the following command burns the image to the disk:

```
sudo dd if=2013-12-20-wheezy-raspbian.img of=/dev/mmcblk0 bs=2M
```

When it's done, remove your SD card, insert it into your Raspberry Pi and connect it to your computer via ethernet.

We're now going to do some basic housekeeping and install some of the required libraries. You should SSH into your Pi with a command similar to the following. The IP address usually falls in the 192.168.2.3-192.168.2.10 range.

```
ssh pi@192.168.2.3 # password (default): raspberry
```

Run the following, select to 'Expand Filesystem' and restart your Pi:

```
sudo raspi-config
```

Run the following commands to update Pi and install some useful tools.

```
sudo apt-get update
sudo apt-get upgrade --yes
sudo apt-get install vim git-core python-dev python-pip bison libasound2-dev libportaudio-dev python-pyaudio --yes
```

Plug in your USB microphone. Let's open up an ALSA configuration file in vim:

```
sudo vim /etc/modprobe.d/alsa-base.conf
```

Change the following line:

```
options snd-usb-audio index=-2
```

To this:

```
options snd-usb-audio index=0
```

Back in the shell, run:

```
sudo alsa force-reload
```

Next, test that recording works (you may need to restart your Pi) by recording some audio with the following command:

```
arecord temp.wav
```

Make sure you have speakers or headphones connected to the audio jack of your Pi. You can play back the recorded file:

```
aplay -D hw:1,0 temp.wav
```

Add the following line to the end of ~/.bash_profile (you may need to run `touch ~/.bash_profile` if the file doesn't exist already):

```
export LD_LIBRARY_PATH="/usr/local/lib"
source .bashrc
```

And this to your ~/.bashrc or ~/.bash_profile:

```
LD_LIBRARY_PATH="/usr/local/lib"
export LD_LIBRARY_PATH
PATH=$PATH:/usr/local/lib/
export PATH
```

With that, we're ready to install Jasper.

In the home directory of your Pi, clone the Jasper source code:

```
git clone https://github.com/jasperproject/jasper-client.git jasper
```

Jasper requires various Python libraries that we can install in one line with:

```
sudo pip install --upgrade setuptools
sudo pip install -r jasper/client/requirements.txt
```

Sometimes it might be neccessary to make `jasper.py` executable:

```
chmod +x jasper/jasper.py
```

You've now installed the Jasper core software. If you're following Method I (Quick Start), continue with [Configuration](http://jasperproject.github.io/documentation/configuration/). Otherwise, continue with the dependency installation below.

To be able to understand what you say, Jasper still needs a Speech-to-Text (STT) engine. Jasper also needs a Text-to-Speech (TTS) engine to answer to your commands. Jasper aims to be modular and thus gives you the choice which STT/TTS engine you want to use. Depending on your choice, it may be required to install additional software.

Head over to the [Configuration section](http://jasperproject.github.io/documentation/configuration/). During configuration, you'll learn what STT/TTS engines are and chose your flavour. You can then come back here and install the required dependencies for the STT/TTS engine of your choice (if neccessary).

_Note: Installing pocketsphinx will take quite some time because you need to compile some stuff from source._

Jasper can use PocketSphinx for voice recognition. If you want to use Pocketsphinx as [STT Engine](http://jasperproject.github.io/documentation/configuration/#stt), you'll have to install:

- sphinxbase & pocketsphinx
- CMUCLMTK
- MIT Language Modeling Toolkit
- m2m-aligner
- OpenFST & Phonetisaurus

If you're using ArchLinux, you're lucky: Just install the [according AUR package](https://aur.archlinux.org/packages/jasper-stt-pocketsphinx/) and you're done:

```
yaourt -S jasper-stt-pocketsphinx
```

Everyone else needs to install the above tools manually:

## Installing Sphinxbase/Pocketsphinx

First, you need to install Pocketsphinx. If you're using Debian Sid (unstable) or Jessie (testing), you can just do:

```
sudo apt-get update
sudo apt-get install pocketsphinx
```

If you're not using Debian Sid/Jessie, you need to compile and install them from source:

```
wget http://downloads.sourceforge.net/project/cmusphinx/sphinxbase/0.8/sphinxbase-0.8.tar.gz
tar -zxvf sphinxbase-0.8.tar.gz
cd ~/sphinxbase-0.8/
./configure --enable-fixed
make
sudo make install
wget http://downloads.sourceforge.net/project/cmusphinx/pocketsphinx/0.8/pocketsphinx-0.8.tar.gz
tar -zxvf pocketsphinx-0.8.tar.gz
cd ~/pocketsphinx-0.8/
./configure
make
sudo make install
```

## Installing CMUCLMTK

Begin by installing some dependencies:

```
sudo apt-get install subversion autoconf libtool automake gfortran g++ --yes
```

Next, move into your home (or Jasper) directory to check out and install CMUCLMTK:

```
svn co https://svn.code.sf.net/p/cmusphinx/code/trunk/cmuclmtk/
cd cmuclmtk/
./autogen.sh && make && sudo make install
cd ..
```

Then, when you've left the CMUCLTK directory, download the following libraries:

## Installing Phonetisaurus, m2m-aligner and MITLM

To use the Pocketsphinx STT engine, you also need to install MIT Language Modeling Toolkit, m2m-aligner and Phonetisaurus (and thus OpenFST).

On Debian, you can install these from the `experimental` repository:

```
sudo su -c "echo 'deb http://ftp.debian.org/debian experimental main contrib non-free' > /etc/apt/sources.list.d/experimental.list"
sudo apt-get update
sudo apt-get -t experimental install phonetisaurus m2m-aligner mitlm libfst-tools
```

If you're not using Debian, perform these steps:

```
wget http://distfiles.macports.org/openfst/openfst-1.3.3.tar.gz
wget https://mitlm.googlecode.com/files/mitlm-0.4.1.tar.gz
wget https://m2m-aligner.googlecode.com/files/m2m-aligner-1.2.tar.gz
wget https://phonetisaurus.googlecode.com/files/is2013-conversion.tgz
```

Untar the downloads:

```
tar -xvf m2m-aligner-1.2.tar.gz
tar -xvf openfst-1.3.3.tar.gz
tar -xvf is2013-conversion.tgz
tar -xvf mitlm-0.4.1.tar.gz
```

Build OpenFST:

```
cd openfst-1.3.3/
sudo ./configure --enable-compact-fsts --enable-const-fsts --enable-far --enable-lookahead-fsts --enable-pdt
sudo make install # come back after a really long time
```

Build M2M:

```
cd m2m-aligner-1.2/
sudo make
```

Build MITLMT:

```
cd mitlm-0.4.1/
sudo ./configure
sudo make install
```

Build Phonetisaurus:

```
cd is2013-conversion/phonetisaurus/src
sudo make
```

Move some of the compiled files:

```
sudo cp ~/m2m-aligner-1.2/m2m-aligner /usr/local/bin/m2m-aligner
sudo cp ~/phonetisaurus-0.7.8/phonetisaurus-g2p /usr/local/bin/phonetisaurus-g2p
```

## Building the Phonetisaurus FST model

```
wget https://www.dropbox.com/s/kfht75czdwucni1/g014b2b.tgz
tar -xvf g014b2b.tgz
```

Build Phonetisaurus model:

```
cd g014b2b/
./compile-fst.sh
cd ..
```

Finally, rename the following folder for convenience:

```
mv ~/g014b2b ~/phonetisaurus
```

Once the installations are complete, restart your Pi.

At this point, we've installed Jasper and all the necessary software to run it. Before we start playing around, though, we need to configure Jasper and provide it with some basic information.

On Arch Linux, install [`julius` from the `[community]` repo](https://www.archlinux.org/packages/?repo=Community&q=julius):

```
sudo pacman -S julius
```

If you're not using ArchLinux, you need to compile Julius manually.

```
sudo apt-get update
sudo apt-get install build-essential zlib1g-dev flex libasound2-dev libesd0-dev libsndfile1-dev
```

Then, download the [Julius source tarball](http://sourceforge.jp/projects/julius/downloads/60273/julius-4.3.1.tar.gz/) and extract it to `~/julius`.

```
cd ~/julius
./configure --enable-words-int
make
sudo make install
```

Please note that you also need an [acoustic model and a lexicon.](http://jasperproject.github.io/documentation/configuration/#julius-stt).

On Arch Linux, install [`jasper-tts-espeak` from the AUR](https://aur.archlinux.org/packages/jasper-tts-espeak/):

```
yaourt -S jasper-tts-espeak
```

On Debian, install the `espeak` package:

```
sudo apt-get update
sudo apt-get install espeak
```

On Arch Linux, install [`jasper-tts-festival` from the AUR](https://aur.archlinux.org/packages/jasper-tts-festival/):

```
yaourt -S jasper-tts-festival
```

On Debian, install `festival` and `festvox-don`:

```
sudo apt-get update
sudo apt-get install festival festvox-don
```

On Arch Linux, install [`jasper-tts-flite` from the AUR](https://aur.archlinux.org/packages/jasper-tts-flite/):

```
yaourt -S jasper-tts-flite
```

On Debian, install `flite`:

```
sudo apt-get update
sudo apt-get install flite
```

On Arch Linux, install [`jasper-tts-pico` from the AUR](https://aur.archlinux.org/packages/jasper-tts-pico/):

```
yaourt -S jasper-tts-pico
```

On Debian, you need to install `libttspico-utils`:

```
sudo apt-get update
sudo apt-get install libttspico-utils
```

On Arch Linux, install [`jasper-tts-google` from the AUR](https://aur.archlinux.org/packages/jasper-tts-google/):

```
yaourt -S jasper-tts-google
```

On Debian, you need to install `python-pymad` via APT and `gTTS` via PIP:

```
sudo apt-get update
sudo apt-get install python-pymad
sudo pip install --upgrade gTTS
```

On Arch Linux, install [`jasper-tts-ivona` from the AUR](https://aur.archlinux.org/packages/jasper-tts-ivona/):

```
yaourt -S jasper-tts-ivona
```

On Debian, you need to install `python-pymad` via APT and `pyvona` via PIP:

```
sudo apt-get update
sudo apt-get install python-pymad
sudo pip install --upgrade pyvona
```
