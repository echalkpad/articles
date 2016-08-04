# Lilliput DisplayLink monitor

[Original URL](http://wiki.wildsong.biz/index.php/Lilliput_DisplayLink_monitor)

> I am configuring this 7" USB connected monitor with 8track on a car computer. Mounting options I stuck a Ram mount 3" pad to the back of the monitor with foam back mounting tape. So far it's holding...

I am configuring this 7" USB connected monitor with [8track](http://wiki.wildsong.biz/index.php/8track "8track") on a car computer.

## <span id="Mounting_options" class="mw-headline">Mounting options</span>

I stuck a Ram mount 3" pad to the back of the monitor with foam back mounting tape. So far it's holding on just fine. Now I can mount it to any standard 3/4" ball mount Ram mounts. Probably won't use it on the handlebars of my bike, but I could!!

I thought the little chrome plastic support leg on the back was in the way of the cables, but it just broke off when I was adjusting it! Snap! No more leg problem.

### <span id="This_did_not_work" class="mw-headline">This did not work</span>

I got a [RAM floor mount](http://www.gpscity.com/ram-mount-vehicle-floor-mount-with-octagon-button-end.html) and adapter today (1/31/12). It's hard to tell what this really is from the pictures. It is just a thick round aluminum rod with a pinch clamp that bolts to one of the seat bracket bolts.

You bolt it to the floor and bend it to get the top end where you want it. Then you slid a piece of flexible plastic tubing over it for aesthetics, clamp another pinch bolt adapter (not included with the arm for some reason) to the top, and you can now use standard RAM ball and socket type parts to attach the monitor.

The problems are (1) it gets in the way of the rails on the seat, and (2) the screen vibrates too much. You could hang a mike on it but a display moves around too much when the car is on the road. (Might work in a Coupe Deville. Not in a lowered Yaris.)

## <span id=".28No_longer.29_Building_a_kernel" class="mw-headline">(No longer) Building a kernel</span>

If you are using Ubuntu or Debian, the DisplayLink driver (udlfb) is included. This is great news, makes using DisplayLink far easier than it was a few months ago!

## <span id="Installing_the_X11_driver" class="mw-headline">Installing the X11 driver</span>

Needed to do

2014-01-17 won't build on Ubuntu 13.04!!

```
sudo apt-get install xorg-dev
git clone http://git.plugable.com/webdav/xf-video-udlfb
#(or later to refresh the source tree do 'git pull' after the 'cd'.)
cd xf-video-udlfb
./configure
make
sudo make install
# installs into /usr/local/lib/xorg/modules/drivers
-rwxr-xr-x 1 root bwilson 993 Aug 25 21:37 displaylink_drv.la
-rwxr-xr-x 1 root bwilson 135488 Aug 25 21:37 displaylink_drv.so
```

See <http://karuppuswamy.com/wordpress/2010/07/19/how-to-get-lilliput-displaylink-based-usb-monitor-um-70-17e902a9-working-in-ubuntu-linux/> and <http://mulchman.org/blog/?tag=displaylink>

### <span id="Steps_1-3" class="mw-headline">Steps 1-3</span>

lsusb shows me

```
Bus 001 Device 007: ID 17e9:02a9 Newnham Research
Bus 001 Device 006: ID 0eef:0001 D-WAV Scientific Co., Ltd eGalax TouchScreen
```

dmesg shows

```
[ 8920.790073] usb 1-1: new high speed USB device using ehci_hcd and address 3
[ 8920.940506] hub 1-1:1.0: USB hub found
[ 8920.940858] hub 1-1:1.0: 4 ports detected
[ 8921.221400] usb 1-1.1: new full speed USB device using ehci_hcd and address 4
[ 8926.945768] usb 1-1: clear tt 1 (8040) error -71
[ 8926.949835] input: eGalax Inc. USB TouchController as /devices/pci0000:00/0000:00:1a.7/usb1/1-1/1-1.1/1-1.1:1.0/input/input5
[ 8926.949966] generic-usb 0003:0EEF:0001.0004: input,hidraw3: USB HID v2.10 Pointer [eGalax Inc. USB TouchController] on usb-0000:00:1a.7-1.1/input0
[ 8926.954007] hub 1-1:1.0: hub_port_status failed (err = -71)
[ 8926.958135] usb 1-1: clear tt 1 (8040) error -71
[ 8926.962260] hub 1-1:1.0: hub_port_status failed (err = -71)
[ 8926.966384] hub 1-1:1.0: hub_port_status failed (err = -71)
[ 8926.966398] usb 1-1: USB disconnect, address 3
[ 8926.966400] usb 1-1.1: USB disconnect, address 4
[ 8932.680017] usb 1-1: new high speed USB device using ehci_hcd and address 5
[ 8932.830492] hub 1-1:1.0: USB hub found
[ 8932.830586] hub 1-1:1.0: 4 ports detected
[ 8933.110134] usb 1-1.1: new full speed USB device using ehci_hcd and address 6
[ 8943.221404] /build/buildd/linux-2.6.35/drivers/hid/usbhid/hid-core.c: usb_submit_urb(ctrl) failed
[ 8943.221418] generic-usb 0003:0EEF:0001.0005: timeout initializing reports
[ 8943.221485] input: eGalax Inc. USB TouchController as /devices/pci0000:00/0000:00:1a.7/usb1/1-1/1-1.1/1-1.1:1.0/input/input6
[ 8943.221630] generic-usb 0003:0EEF:0001.0005: input,hidraw3: USB HID v2.10 Pointer [eGalax Inc. USB TouchController] on usb-0000:00:1a.7-1.1/input0
[ 8943.461327] usb 1-1.2: new high speed USB device using ehci_hcd and address 7
[ 8943.584182] udlfb: module is from the staging directory, the quality is unknown, you have been warned.
[ 8943.585687] usb 1-1.2: dlfb: allocated 4 65024 byte urbs
[ 8943.668471] usb 1-1.2: dlfb: set_par mode 800x480
[ 8943.669134] usb 1-1.2: dlfb: DisplayLink USB device /dev/fb1 attached. 800x480 resolution. Using 1500K framebuffer memory
[ 8943.669153] usbcore: registered new interface driver udlfb
[ 8943.669154] VMODES initialized
```

Driver is loaded

```
lsmod | grep udlfb
udlfb 15573 0 
sysimgblt 2571 1 udlfb
sysfillrect 3973 1 udlfb
syscopyarea 3664 1 udlfb
```

### <span id="Step_4." class="mw-headline">Step 4.</span>

For Ubuntu

In Ubuntu 10.10 there is no fbcon module so I don't need to disable it. I do have /dev/fb0 and /dev/fb1 devices.

After booting today I actually see text on the small monitor. So I am trying to disable that with the kernel boot option in /etc/default/grub

The instructions referenced above said to add this line,

```
GRUB_CMDLINE_LINUX_DEFAULT=ï¿½vga=normal nomodesetï¿½
```

but while that did not work on Ubuntu 10.10, this one DID

```
GRUB_GFXPAYLOAD_LINUX=text
```

followed by 'sudo update-grub' and a reboot, and I now have the much-sought-after GREEN SCREEN. YAY!

For Debian,

we're still on Grub (not Grub2) so there is no /etc/default/grub file. Directly edit the /boot/grub/menu.lst file – but – I already have a green screen on the monitor so I think I am already there.

### <span id="Step_5." class="mw-headline">Step 5.</span>

did the edit

### <span id="Step_6." class="mw-headline">Step 6.</span>

FAIL – Ubuntu has no /etc/X11/xorg.conf file! I search and find /usr/share/X11/xorg.conf.d/

I find that if I copy the one from the site and put it on Raven, it moves the X11 display to the Lilliput! The 24" monitor goes black. This is WRONG on Raven but would be okay on 8track so I proceed on to moving the monitor over there and start over at step 1...

I ended up re-writing my own xorg.conf file for 8track. I won't need any output on the VGA when the system is deployed; it will have only the Lilliput.

Now.. the test... restarting gdm, here we go... Oh. I forgot I don't RUN gdm on 8track! So, exit X11 (Ctl-Alt-Backspace) and run 'startx' again. It fails! There is no 'displaylink' driver found.

Get source from <http://projects.unbit.it/downloads/> Install in /usr/local. Try again... works...

I will put the xorg.conf file here when it is finalized. I am trying to get the touchscreen to work now.

### <span id="Xorg.conf_debugging_tip" class="mw-headline">Xorg.conf debugging tip</span>

1. Get set up with a VGA monitor and the Lilliput.
2. Run startx to test
3. Exit with Ctl Alt Backspace
4. Look at /var/log/X.org.log to see what happened (eg which drivers really loaded)
5. Edit xorg.conf
6. startx to test again
7. Repeat until things are working!

## <span id="Touchscreen" class="mw-headline">Touchscreen</span>

For the Lilliput UM70 you don't need the driver, usbhid works fine, but the setup.sh file in the driver package will set up xorg.conf for you. Back up your almost working xorg.conf in case you don't like the results.

Download source code for the 32-bit driver from <http://home.eeti.com.tw/web20/eGalaxTouchDriver/linuxDriver.htm>

Latest version: 8/31/2011 eGalaxTouch-3.06.5625-32b-k26.tar.gz

"eGalaxTouch for Linux

The new Linux public driver supports most of the Linux distribution 32/64 bit version, including, Ubuntu, Debian, SuSE(openSuSE), Fedora Core, Mandriva, Slackware and so on.

Please feel free to contact our touch_fae@eeti.com if you need ."

Here is the output from setup.sh

```
(*) Linux driver installer for eGalaxTouch controller 

(I) Check user permission: root, you are the supervisor.
(I) Begin to setup the eGalaxTouch driver.
(I) Extract eGalaxTouch driver archive to /usr/local/eGalaxTouch32.
(I) Create eGalaxTouch utility shortcut in /usr/bin.
(I) Create TKCal tool shortcut in /usr/bin.
(I) Check X window version: 1.10.x
(I) Copy X module: x110/egalax_drv.so to /usr/lib/xorg/modules/input.

(Q) Which interface controller do you use?
(I) [1] RS232 [2] PS/2 [3] USB : '''3'''
(I) Using interface: USB
(I) Found a HID compliant touch controller.

(I) Found X configuration file: /etc/X11/xorg.conf.
(I) Removed touch configuration from /etc/X11/xorg.conf.
(I) Add touch configuration into /etc/X11/xorg.conf.
(I) Copy udev rule: 52-egalax-void.conf to /usr/share/X11/xorg.conf.d.
(I) Create eGalaxTouch utility shortcut.

(I) Please reboot the system for some changes to take effect.
(I) After booting, type "eGalaxTouch" to do calibration.
```

To disable the mouse device, which fights with the touch screen, add the following new input-device to your xorg.conf:

```
 Section "InputDevice"
 Identifier "dummy"
 Driver "void"
 Option "Device" "/dev/input/mice"
 Option "CorePointer"'
 EndSection
```

Note the CorePointer line which forces the dummy device to override the default-pointer. Also I added Option "CoreKeyboard" to the keyboard entry and added this line to "ServerLayout"-section so that the dummy mouse gets added:

```
 InputDevice "dummy"
```

This does indeed disable the mouse, I no longer see any stupid frustrating annoying comments about it loading default devices. It has to have a dummy mouse because if you put CorePointer in the touch screen descriptor, it ignores that and still adds the default mouse.

From X.org.log

```
(II) LoadModule: "egalax"
(II) Loading /usr/lib/xorg/modules/input//egalax_drv.so
(II) Module egalax: vendor="X.Org Foundation"
 compiled for 1.4.0.90, module version = 3.3.0
 Module class: X.Org XInput Driver
 ABI class: X.Org XInput driver, version 2.0
```

### <span id="Calibration" class="mw-headline">Calibration</span>

The latest version of the driver added a calibration program that runs under X11!!! AWESOME, THANKS eGalax people!!!

There are many settings in there. It will take some time to figure it all out.

But in the meantime, the Tool->4pt Calibration worked fine and I now have a usable touchscreen!!
