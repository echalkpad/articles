# Ubuntu9.04

[Original URL](https://libdlo.freedesktop.org/wiki/Ubuntu9.04/)

> Ubuntu 9.04 follows similar steps, but requires some additional modifications vs the Ubuntu8.10 instructions, because of some new issues and increased reliance on xrandr. xf86-driver-displaylink...

Ubuntu 9.04 follows similar steps, but requires some additional modifications vs the [Ubuntu8.10](https://libdlo.freedesktop.org/wiki/Ubuntu8.10/) instructions, because of some new issues and increased reliance on xrandr.

1. [xf86-driver-displaylink](https://libdlo.freedesktop.org/wiki/xf86-driver-displaylink/) "make install" puts displaylink_drv file in /usr/local/lib/xorg/modules/drivers, while Ubuntu 9.04 only looks in /usr/lib by default

  - copy or link file to /usr/lib or change X configuration to also look in /usr/local/lib/xorg/modules/drivers

2. Unbuntu 9.04 relies on more X RandR support, which is incomplete in [xf86-driver-displaylink](https://libdlo.freedesktop.org/wiki/xf86-driver-displaylink/). In particular, there's a small but nasty problem of X coming up with the screen rotate not set normally. The effect to the end user is windows are sometimes positioned off-screen. In particular, with the GDM greeter/login, the login will come up on screen initially, but any menus will come up offscreen, and any move of the window will cause it to disappear. Once logged in, most other Gnome elements (like the taskbar) appear to be gone, because they're off screen.

These randr X/GDM errors may also be visible in the users' .xsession-errors.

Until the a proper fix is understood, a workaround is to call xrandr -o 0 within the user session. This can be done by hand each login, or run for every new user session by adding these three lines into /etc/gdm/Init/Default after the defintion of the gdmwhich() function:

```
XRANDR=`gdmwhich xrandr`
if [ "x$XRANDR" != "x" ] ; then
 $XRANDR -o 0
fi
```

It would seem like this could be fixed by setting some defaults in [xf86-driver-displaylink](https://libdlo.freedesktop.org/wiki/xf86-driver-displaylink/). Ideas or patches welcome!

1. There is at least one gnome bug that may cause GDM login prompt and the gnome shell to not load or crash easily. When these problems occur, log of errors appears in ~/.xsession-errors for the user logging in

  - glib bug for DISPLAY other than :0 (see detail below)
  - glib/gnome problems with xrandr functionality implemented in the displaylink X server Ubuntu 9.04 includes gnome glib 2.20, which has a small glibc bug that affects scenarios with multiple X servers running. There is a source patch available, but no package update, making Ubuntu 9.04 not recommended for 

    <span class="createlink"><a href="https://secure.freedesktop.org/write/libdlo/ikiwiki.cgi?page=DisplayLink&amp;from=Ubuntu9.04&amp;do=create">?</a>DisplayLink</span>

     users, unless you're willing to follow the steps below:

2. wget <http://ftp.gnome.org/pub/gnome/sources/glib/2.20/glib-2.20.1.tar.gz>

3. tar -zxvf glib-2.20.1.tar.gz

4. cd glib-2.20.1/gio
5. wget <http://launchpadlibrarian.net/27278363/C%3A%5Ccygwin%5Chome%5Cgwa%5Cglib-fix.patch> and rename it fix.patch
6. patch gdesktopappinfo.c fix.patch
7. cd ..
8. ./configure –prefix=/usr

  - configure may point out some packages you have to sudo apt-get install

9. sudo make install See <https://bugs.launchpad.net/glib/+bug/346964> for background
