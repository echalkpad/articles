# PiscesMATEImages

[Original URL](https://www.raspbian.org/PiscesMATEImages)

> UPDATED TO MATE v1.4 - Aug. 17, 2012 Please Note: The Raspberry Pi Foundation has produced and released their own recommended image of Raspbian that can be obtained directly from the Raspberry Pi...

**UPDATED TO MATE v1.4 - Aug. 17, 2012**

**Please Note:** The Raspberry Pi Foundation has produced and released their own recommended image of Raspbian that can be obtained directly from the [Raspberry Pi Website Downloads Page](http://www.raspberrypi.org/downloads). New users are encouraged to download the Foundation image as it is the best supported image within the Raspberry Pi Forums. This "unofficial" release is primarily intended for experienced Linux users who would like to experiment with alternative GUI interfaces for Raspbian.

This images differs from the other [Pisces images](https://www.raspbian.org/PiscesImages) mainly in that in includes the [MATE v1.4](https://www.raspbian.org/RaspbianMate) desktop environment rather than the LXDE desktop environment. Although the MATE is "heavier weight" than LXDE, it is based on the Gnome 2 desktop that for many years served as the "standard" desktop for most Linux distributions. Thus MATE is more mature and feature rich than other "lighter weight" desktops. This image was created using the Debian installer so it reflects a minimal Debian installation with the MATE packages added for a desktop.

No attempts have been made to optimize the MATE desktop for the slower CPU and low-memory environment of the Raspberry Pi. There are [instructions here](https://www.raspbian.org/RaspbianMate) that describe various optimization tweaks for MATE.

## Download the Pisces+MATE R2 Image

The 750 MB image can be downloaded from this link [rpi_pisces_mate_r2.zip](http://archive.raspbian.org/images/rpi_pisces_mate_r2.zip).

The following two user accounts are created on this image:

**Username** | **Password**
------------ | ------------
root         | raspbian
raspbian     | raspbian

## Image Install

To install the image file, you will need to unzip it and write it to a suitable 4G or larger SD card using the UNIX tool [dd](http://en.wikipedia.org/wiki/Dd_(Unix)). Windows users should use [Win32DiskImager](http://www.softpedia.com/get/CD-DVD-Tools/Data-CD-DVD-Burning/Win32-Disk-Imager.shtml). Do not try to drag and drop or otherwise copy over the image without using dd or Win`32`Disk``Imager – it won't work. If you're still not clear on what to do, the community on the Raspberry Pi Wiki has written a [guide for beginners](http://elinux.org/RPi_Easy_SD_Card_Setup) on how to set up your SD card.

## Image Notes

### Image Features

- Typical Linux tools and editors are included
- SSH server included, launches on boot by default
- Xorg and MATE desktop included
- Midori web browser is included
- Hexxeh's rpi-update is included
- Updated to the latest Raspberry Pi firmware and kernel as of June 19th

### Additional Notes

- Reset 'root' and 'raspbian' passwords and [OpenSSH server host keys](http://www.cyberciti.biz/faq/howto-regenerate-openssh-host-keys/) to avoid security issues.

- Configure your timezone:

- Configure locale settings:

  - `apt-get install locales`

    `dpkg-reconfigure locales`

- Configure keyboard settings:

  - `apt-get install keyboard-configuration`

    `dpkg-reconfigure keyboard-configuration`

- Install Gnu C/C++ compiler and tools:

  - `apt-get install build-essential`

- Scrap Midori. I found it's just too slow on the Raspberry Pi.

  - `apt-get purge midori` `apt-get autoremove`

- Install Net``Surf, a fast, low-resource browser much more suited to the Raspberry Pi.

  - `apt-get install netsurf-gtk`

- Launch MATE desktop from 'raspbian' user account

- The major purpose of this image is to evaluate whether MATE can be a viable desktop environment for the Raspberry Pi. Memory use of the default MATE configuration without any applications running is:

  - # free -h

    ```
     total used free shared buffers cached
    Mem: 215M 201M 14M 0B 9.9M 115M
    -/+ buffers/cache: 75M 139M
    Swap: 243M 6.3M 237M
    ```

- This indicates that the Raspbian OS and MATE consume about 75M when idle. Leaving 139M free for application use.

You can use the standard Debian way to update your installation:

- `apt-get update && apt-get dist-upgrade`

To get the newest kernel and firmware, [rpi-update](https://github.com/Hexxeh/rpi-update) is included in this image.

Verify the system time and date are set correctly and then run the following command:
