# DisplayLink USB Devices on Linux Kernel 3.4.0

[Original URL](http://plugable.com/2012/06/21/displaylink-usb-devices-on-linux-kernel-3-4-0)

> Linux kernel 3.4.0 is the first to include a new driver for DisplayLink-based USB 2.0 devices, called "udl". udl is a port of the udlfb driver to Linux's DRM architecture. David...

Linux kernel 3.4.0 is the first to include a new driver for DisplayLink-based USB 2.0 devices, called "udl". udl is a port of the udlfb driver to Linux's DRM architecture. David Airlie is doing this work, and the potential is very exciting. Eventually, this architecture will lead to a host of advantages, including GPU-accelerated 3D rendering to USB graphics adapters.

Both the new "udl", and older "udlfb" framebuffer driver that we maintain are present in 3.4.0\. Unfortunately, the new udl DRM driver is still maturing, and can cause kernel panics. With USB graphics devices present, you can determine which driver ("udlfb" or "udl") is getting loaded with lsmod:

```
lsmod | grep "udl"
```

This change has a particular impact with Fedora 17 -- the first open source distro to have automatic USB multiseat support -- which shipped with Linux kernel 3.3.

Post-ship, Fedora 17 now offers a software update to kernel 3.4.0, which unfortunately causes problems: udl may be loaded for DisplayLink-based devices, and kernel panics are common and terminals often won't come up. To the user, it appears to break multiseat.

So to fix the issues you'll see with 3.4.0, we recommend disabling udl for the time being. The stable udlfb driver is still present in the kernel, and will get matched against your hardware automatically once udl is no longer loaded. The easiest way to do this is to run the following commands and reboot:

```
echo "blacklist udl" | sudo tee --append /etc/modprobe.d/udlfb.conf
sudo depmod -a
sudo dracut -f /boot/initramfs-$(uname -r).img $(uname -r)
```

One the server reboots, udlfb should match all USB graphics devices and be fully stable. Please let us know if you have any trouble. And in coming Linux kernel versions, udl will continue to improve and at some point udlfb will be able to be retired in favor of it.

[Plugable USB 2.0 Thin Client](http://plugable.com/products/dc-125 "Click to learn more about the DC-125")

![](http://dxg49ziwjgkgt.cloudfront.net/images/dc-125/main_256.jpg "DC-125")

## 

![](http://dxg49ziwjgkgt.cloudfront.net/wp-content/uploads/2014/09/us_flag.png "Amazon.com")![](http://dxg49ziwjgkgt.cloudfront.net/wp-content/uploads/2014/09/ca_flag.png "Amazon.ca")![](http://dxg49ziwjgkgt.cloudfront.net/wp-content/uploads/2014/09/uk_flag.png "Amazon.co.uk")![](http://dxg49ziwjgkgt.cloudfront.net/wp-content/uploads/2014/09/eu_flag.png "Amazon.co.uk")![](http://dxg49ziwjgkgt.cloudfront.net/wp-content/uploads/2014/09/de_flag.png "Amazon.de")![](http://dxg49ziwjgkgt.cloudfront.net/wp-content/uploads/2014/09/fr_flag.png "Amazon.fr")![](http://dxg49ziwjgkgt.cloudfront.net/wp-content/uploads/2014/09/es_flag.png "Amazon.es")![](http://dxg49ziwjgkgt.cloudfront.net/wp-content/uploads/2014/09/it_flag.png "Amazon.it")

### _Related_
