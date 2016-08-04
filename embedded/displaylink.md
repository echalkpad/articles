# DisplayLink

[Original URL](https://wiki.archlinux.org/index.php/DisplayLink)

> DisplayLink devices on Linux still only have experimental support. While some people have had success in using them, it is generally not an easy process and not guaranteed to work. The steps on this...

DisplayLink devices on Linux still only have experimental support. While some people have had success in using them, it is generally not an easy process and not guaranteed to work. The steps on this page describe the generally most successful methods of using external monitors with DisplayLink.

## <span id="Installation" class="mw-headline">Installation</span>

**Note:** Modern USB 3.0 DisplayLink adapters are not supported as of October 2014.[[1]](http://displaylink.org/forum/showthread.php?t=1748) Older DisplayLink chips (USB 2.0) are supported.

Install the 

<span class="plainlinks archwiki-template-pkg">
  <a href="https://www.archlinux.org/packages/?name=xf86-video-fbdev" class="external text">xf86-video-fbdev</a>
</span>

 package, which provides framebuffer video for X.org.

Hardware-level support is provided by the [kernel module](https://wiki.archlinux.org/index.php/Kernel_modules "Kernel modules") `udlfb`, which should be loadable by default in Arch.

### <span id="udl" class="mw-headline">udl</span>

The kernel [DRM](http://en.wikipedia.org/wiki/Direct_Rendering_Manager "wikipedia:Direct Rendering Manager") driver for DisplayLink is `udl`, a rewrite of the original [udlfb](https://www.kernel.org/doc/Documentation/fb/udlfb.txt) driver. It allows configuring DisplayLink monitors using [Xrandr](https://wiki.archlinux.org/index.php/Xrandr "Xrandr").

First, the setup and installation:

After that, run:

```
# xrandr --listproviders

Providers: number : 2
Provider 0: id: 0x49 cap: 0xb, Source Output, Sink Output, Sink Offload crtcs: 2 outputs: 8 associated providers: 0 name:Intel
Provider 1: id: 0x13c cap: 0x2, Sink Output crtcs: 1 outputs: 1 associated providers: 0 name:modesetting
```

In the above output, we can see that provider 0 is the system's regular graphics provider (Intel), and provider 1 (modesetting) is the DisplayLink provider. To use the DisplayLink device, connect provider 1 to provider 0:

```
# xrandr --setprovideroutputsource 1 0
```

and xrandr will add a DVI output you can [use as normal with xrandr](https://wiki.archlinux.org/index.php/Xrandr#Configuration "Xrandr"). This is still experimental but supports hotplugging and when works, it's by far the simplest setup. If it works then everything below is unnecessary.

## <span id="Configuration" class="mw-headline">Configuration</span>

These instructions assume that you already have an up and running X server and are simply adding a monitor to your existing setup.

### <span id="Load_the_framebuffer_device" class="mw-headline">Load the framebuffer device</span>

Before your system will recognize your DisplayLink device, the `udl` kernel module must be loaded. To do this, run

```
# modprobe udl
```

If your DisplayLink device is connected, it should show some visual indication of this. Although a green screen is the standard indicator of this, other variations have been spotted and are perfectly normal. Most importantly, the output of `dmesg` should show something like the following, indicating a new DisplayLink device was found:

```
usb 2-1.1: new high-speed USB device number 7 using ehci-pci
usb 2-1.1: New USB device found, idVendor=17e9, idProduct=03e0
usb 2-1.1: New USB device strings: Mfr=1, Product=2, SerialNumber=3
usb 2-1.1: Product: Lenovo LT1421 wide
usb 2-1.1: Manufacturer: DisplayLink
usb 2-1.1: SerialNumber: 6V9BBRM1
[drm] vendor descriptor length:17 data:17 5f 01 00 15 05 00 01 03 00 04
udl 2-1.1:1.0: fb1: udldrmfb frame buffer device
[drm] Initialized udl 0.0.1 20120220 on minor 1
```

Furthermore, `/dev` should contain a new `fb` device, likely `/dev/fb1` if you already had a framebuffer for your primary display.

To automatically load `udl` at boot, create the file `udl.conf` in `/etc/modules-load.d/` with the following contents:

```
/etc/modules-load.d/udl.conf

udl
```

For more information on loading kernel modules, see [Kernel modules#Loading](https://wiki.archlinux.org/index.php/Kernel_modules#Loading "Kernel modules").

### <span id="Configuring_X_Server" class="mw-headline">Configuring X Server</span>

There are three popular ways people use DisplayLink devices with X on desktop Linux computers:

- With `xrandr` and `udl` (recommended)
- Xinerama with a single X server
- Two separate X servers, linked together in some way

While Xinerama is probably the more desirable setup, it is also less likely to work. Both methods are described below.

#### <span id="xrandr" class="mw-headline">xrandr</span>

The `udl` kernel driver is the only one that works wiith [xrandr](https://wiki.archlinux.org/index.php/Xrandr "Xrandr"). Once the driver is loaded, the DisplayLink monitor is listed as an output provider:

```
$ xrandr --listproviders

Providers: number : 2
Provider 0: id: 0x43 cap: 0xb, Source Output, Sink Output, Sink Offload crtcs: 2 outputs: 2 associated providers: 1 name:Intel
Provider 1: id: 0xcb cap: 0x2, Sink Output crtcs: 1 outputs: 1 associated providers: 1 name:modesetting
```

In the above example, provider 1 is the DisplayLink device, and provider 0 is the default display. Running `xrandr --current` gives a list of available screens:

```
$ xrandr --current

Screen 0: minimum 320 x 200, current 1600 x 900, maximum 8192 x 8192
LVDS1 connected 1600x900+0+0 (normal left inverted right x axis y axis) 309mm x 174mm
 1600x900 60.0*+ 40.0 
 1440x900 59.9 
 1360x768 59.8 60.0 
 1152x864 60.0 
 1024x768 60.0 
 800x600 60.3 56.2 
 640x480 59.9 
VGA1 disconnected (normal left inverted right x axis y axis)
DVI-1-0 connected (normal left inverted right x axis y axis)
 1366x768 60.0 +
 1368x768_59.90 59.9 
 1368x768_59.90 (0xd0) 85.7MHz
 h: width 1368 start 1440 end 1584 total 1800 skew 0 clock 47.6KHz
 v: height 768 start 769 end 772 total 795 clock 59.9Hz
```

If the above does not list the DisplayLink screen, then you will need to offload DisplayLink to the main GPU:

```
xrandr --setprovideroutputsource 1 0
```

Once the screen is available, refer to [Xrandr](https://wiki.archlinux.org/index.php/Xrandr "Xrandr") for info on setting it up. For automating the configuration process, see [displaylink.sh](https://github.com/nathantypanski/displaylink.sh).

#### <span id="Xinerama_setup" class="mw-headline">Xinerama setup</span>

You must update or create an [xorg.conf](https://wiki.archlinux.org/index.php/Xorg.conf "Xorg.conf") with a properly configured `ServerLayout` to use a DisplayLink monitor, as Xorg will prefer internal monitors by default. The DisplayLink device is normally only usable if it is set as `screen0` and the internal display as `screen1`.

Add this to the bottom of your `xorg.conf`:

```
/etc/X11/xorg.conf

 ################ DisplayLink ###################
 Section "Device"
 Identifier "DisplayLinkDevice"
 Driver "fbdev" 
 BusID "USB" # needed to use multiple DisplayLink devices 
 Option "fbdev" "/dev/fb0" # change to whatever device you want to use
 # Option "rotate" "CCW" # uncomment for rotation
 EndSection

 Section "Monitor"
 Identifier "DisplayLinkMonitor"
 EndSection

 Section "Screen"
 Identifier "DisplayLinkScreen"
 Device "DisplayLinkDevice"
 Monitor "DisplayLinkMonitor"
 DefaultDepth 16
 EndSection
```

Then edit your server layout to look something like this

```
Screen     0   "DisplayLinkScreen"
Screen      1   "Internal" RightOf "DisplayLinkScreen"
Option      "Xinerama" "on"
```

Change _Internal_ to your main display, then restart X.

#### <span id="Dual_X_setup" class="mw-headline">Dual X setup</span>

Refer to the steps covered in the [Gentoo Wiki](http://wiki.gentoo.org/wiki/DisplayLink#Two_X_server) for details on this setup.

## <span id="Troubleshooting" class="mw-headline">Troubleshooting</span>

### <span id="Screen_redraw_is_broken" class="mw-headline">Screen redraw is broken</span>

If you are using `udl` as your kernel driver and the monitor appears to work, but is only updating where you move the mouse or when windows change in certain places, then you probably have the wrong modeline for your screen. Getting a proper modeline for your screen with a command like

```
gtf 1366 768 59.9
```

where `1366` and `768` are the horizontal and vertical resolutions for your monitor, and `59.9` is the refresh rate from its specs. To use this, create a new mode with `xrandr` like follows:

```
xrandr --newmode "1368x768_59.90" 85.72 1368 1440 1584 1800 768 769 772 795 -HSync +Vsync
```

and add it to [Xrandr](https://wiki.archlinux.org/index.php/Xrandr "Xrandr"):

```
xrandr --addmode DVI-0 1368x768_59.90
```

Then tell the monitor to use that mode for the DisplayLink monitor, and this should fix the redraw issues. Check the [Xrandr](https://wiki.archlinux.org/index.php/Xrandr "Xrandr") page for information on using a different mode.

### <span id="X_crashes_or_keeps_blank" class="mw-headline">X crashes or keeps blank</span>

If X crashes, or nothing is shown at load, try to start X only using the external display by updating `xorg.conf` as follows:

```
 Screen     0   "DisplayLinkScreen"
 #Screen        1   "Internal" RightOf "DisplayLinkScreen"
 #Option        "Xinerama" "on"
```

There are reported instances in which Xinerama requires all its `Screen` color depths to be the same. In both `Screen` sections, try changing the `DefaultDepth` to 16.

```
Section "Screen"
 ...
 DefaultDepth 16
 ...
EndSection
```

**Note:** With fbdev this is not true anymore, because fbdev provides virtual 24 bit support. Everything may be used with `DefaultDepth 24`. However, USB 2.0 has less bandwidth than a standard monitor, so a performance update may be seen from setting it to 16 bit mode.

### <span id="Cannot_start_in_framebuffer_mode._Please_specify_busIDs_for_all_framebuffer_devices" class="mw-headline">Cannot start in framebuffer mode. Please specify busIDs for all framebuffer devices</span>

With two monitors configured in Xinerama mode, `/var/log/Xorg.0.log` will sometimes display the following error:

```
Fatal server error:
Cannot run in framebuffer mode. Please specify busIDs for all framebuffer devices
```

As indicated by [this Gentoo Forums thread](http://forums.gentoo.org/viewtopic-t-809655.html), KMS might be incompatible with fbdev. They also suggested the possible fix of running `startx -- -retro` which will allow X to run even when, to X itself, it doesn't appear to be working.

**Note:** A [bug report](https://bugs.freedesktop.org/show_bug.cgi?id=65384) has been filed for this issue.

### <span id="DisplayLink_refresh_rate_is_extremely_slow_with_gnome_3" class="mw-headline">DisplayLink refresh rate is extremely slow with gnome 3</span>

If once you set up your DisplayLink your entire desktop becomes slow, and maximizing a window on the displaylink screen reduces the lag, try adding the screen manually via xrandr:

```
xrandr -q
xrandr --output [screen_here] --right-of [other_screen] --auto
```

## <span id="See_Also" class="mw-headline">See Also</span>
