# DisplayLink on Fedora Linux

[Original URL](https://nameq.wordpress.com/2011/09/21/displaylink-dl-165-running-on-linux-fedora/)

> As a next step I've tailored this hexadecimal array into a C array as follows: unsigned char sony_sdmhs53_edid[] = { 0x00, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0x00, 0x4d, 0xd9, 0x50, 0x22,...

As a next step I've tailored this hexadecimal array into a C array as follows: unsigned char sony_sdmhs53_edid[] = { 0x00, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0x00, 0x4d, 0xd9, 0x50, 0x22, 0x01, 0x01, 0x01, 0x01, 0x0b, 0x0e, 0x01, 0x03, 0x0c, 0x1e, 0x17, 0x78, 0xea, 0x8c, 0x3e, 0xa4, 0x58, 0x4d, 0x91, 0x24, 0x15, 0x4f, 0x51, 0xa1, 0x08, 0x00, 0x01, 0x01, 0x01, 0x01, 0x01, 0x01, 0x01, 0x01, 0x01, 0x01, 0x01, 0x01, 0x01, 0x01, 0x01, 0x01, 0x64, 0x19, 0x00, 0x40, 0x41, 0x00, 0x26, 0x30, 0x18, 0x88, 0x36, 0x00, 0x30, 0xe4, 0x10, 0x00, 0x00, 0x18, 0x00, 0x00, 0x00, 0xfd, 0x00, 0x39, 0x3f, 0x1c, 0x31, 0x09, 0x00, 0x0a, 0x20, 0x20, 0x20, 0x20, 0x20, 0x20, 0x00, 0x00, 0x00, 0xfc, 0x00, 0x53, 0x44, 0x4d, 0x2d, 0x48, 0x53, 0x35, 0x33, 0x0a, 0x20, 0x20, 0x20, 0x20, 0x00, 0x00, 0x00, 0xff, 0x00, 0x31, 0x32, 0x35, 0x33, 0x37, 0x30, 0x36, 0x0a, 0x20, 0x20, 0x20, 0x20, 0x20, 0x00, 0xce, };

and added this to 

<span>
  <code>udlfb.c</code>
</span>

The next step was to hardcode the EDID in 

<span>
  <code>udlfb.c</code>
</span>

 since it wasn't being able to successfully get it dynamically through the USB device.<br>
Below you can see the changes i've made Binary files udlfb.git/.git/index and udlfb.new/.git/index differ diff -uNrp udlfb.git/.git/logs/HEAD udlfb.new/.git/logs/HEAD --- udlfb.git/.git/logs/HEAD 2011-09-23 00:02:10.376129589 +0100 +++ udlfb.new/.git/logs/HEAD 2011-09-22 04:46:35.000000000 +0100 @@ -1 +1 @@ -0000000000000000000000000000000000000000 1811e5c48a29cc925f9b7a49465e06255196a80f Nuno M Reis 1316732530 +0100 clone: from <http://git.plugable.com/webdav/udlfb> +0000000000000000000000000000000000000000 1811e5c48a29cc925f9b7a49465e06255196a80f root 1316663195 +0100 clone: from <http://git.plugable.com/webdav/udlfb> diff -uNrp udlfb.git/.git/logs/refs/heads/master udlfb.new/.git/logs/refs/heads/master --- udlfb.git/.git/logs/refs/heads/master 2011-09-23 00:02:10.376129589 +0100 +++ udlfb.new/.git/logs/refs/heads/master 2011-09-22 04:46:35.000000000 +0100 @@ -1 +1 @@ -0000000000000000000000000000000000000000 1811e5c48a29cc925f9b7a49465e06255196a80f Nuno M Reis 1316732530 +0100 clone: from <http://git.plugable.com/webdav/udlfb> +0000000000000000000000000000000000000000 1811e5c48a29cc925f9b7a49465e06255196a80f root 1316663195 +0100 clone: from <http://git.plugable.com/webdav/udlfb> diff -uNrp udlfb.git/udlfb.c udlfb.new/udlfb.c --- udlfb.git/udlfb.c 2011-09-23 00:02:10.379129638 +0100 +++ udlfb.new/udlfb.c 2011-09-22 05:56:36.000000000 +0100 @@ -75,6 +75,138 @@ static int console = 1; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Allow fbcon t static int fb_defio = 1; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Detect mmap writes using page faults _/ static int shadow = 1; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Optionally disable shadow framebuffer_ /

```
+
+unsigned char sony_sdmhs53_edid[] = {
+0x00,
+0xff,
+0xff,
+0xff,
+0xff,
+0xff,
+0xff,
+0x00,
+0x4d,
+0xd9,
+0x50,
+0x22,
+0x01,
+0x01,
+0x01,
+0x01,
+0x0b,
+0x0e,
+0x01,
+0x03,
+0x0c,
+0x1e,
+0x17,
+0x78,
+0xea,
+0x8c,
+0x3e,
+0xa4,
+0x58,
+0x4d,
+0x91,
+0x24,
+0x15,
+0x4f,
+0x51,
+0xa1,
+0x08,
+0x00,
+0x01,
+0x01,
+0x01,
+0x01,
+0x01,
+0x01,
+0x01,
+0x01,
+0x01,
+0x01,
+0x01,
+0x01,
+0x01,
+0x01,
+0x01,
+0x01,
+0x64,
+0x19,
+0x00,
+0x40,
+0x41,
+0x00,
+0x26,
+0x30,
+0x18,
+0x88,
+0x36,
+0x00,
+0x30,
+0xe4,
+0x10,
+0x00,
+0x00,
+0x18,
+0x00,
+0x00,
+0x00,
+0xfd,
+0x00,
+0x39,
+0x3f,
+0x1c,
+0x31,
+0x09,
+0x00,
+0x0a,
+0x20,
+0x20,
+0x20,
+0x20,
+0x20,
+0x20,
+0x00,
+0x00,
+0x00,
+0xfc,
+0x00,
+0x53,
+0x44,
+0x4d,
+0x2d,
+0x48,
+0x53,
+0x35,
+0x33,
+0x0a,
+0x20,
+0x20,
+0x20,
+0x20,
+0x00,
+0x00,
+0x00,
+0xff,
+0x00,
+0x31,
+0x32,
+0x35,
+0x33,
+0x37,
+0x30,
+0x36,
+0x0a,
+0x20,
+0x20,
+0x20,
+0x20,
+0x20,
+0x00,
+0xce,
+};
+
 /bin /run /sbin articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii When building as a separate module against an arbitrary kernel,
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii check on build presence of other kernel modules we have dependencies on.
@@ -814,6 +946,7 @@ error:

 #endif

+/*
 static int dlfb_get_edid(struct dlfb_data *dev, char *edid, int len)
 {
    int i;
@@ -841,6 +974,7 @@ static int dlfb_get_edid(struct dlfb_dat

    return i;
 }
+*/

 static int dlfb_ops_ioctl(struct fb_info *info, unsigned int cmd,
                unsigned long arg)
@@ -1304,20 +1438,23 @@ static int dlfb_setup_modes(struct dlfb_
             struct fb_info *info,
             char *default_edid, size_t default_edid_size)
 {
-   int i;
+   int i,j;
    const struct fb_videomode *default_vmode = NULL;
    int result = 0;
-   char *edid;
+   char *edid = NULL;
    int tries = 3;

    if (info->dev) /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old only use mutex if info has been registered */
        mutex_lock(&info->lock);
-
+   
    edid = kmalloc(EDID_LENGTH, GFP_KERNEL);
    if (!edid) {
        result = -ENOMEM;
        goto error;
    }
+   for(j = 0; j < EDID_LENGTH; j++){
+       edid[j] = sony_sdmhs53_edid[j];
+   }   

    fb_destroy_modelist(&info->modelist);
    memset(&info->monspecs, 0, sizeof(info->monspecs));
@@ -1329,11 +1466,13 @@ static int dlfb_setup_modes(struct dlfb_
     */
    while (tries--) {

-       i = dlfb_get_edid(dev, edid, EDID_LENGTH);
+       i = 128;

-       if (i >= EDID_LENGTH)
+       if (i >= EDID_LENGTH){
            fb_edid_to_monspecs(edid, &info->monspecs);
-
+           pr_warn("teste\t%x",edid[8]);
+           pr_warn("teste\t%x",sony_sdmhs53_edid[8]);
+       }
        if (info->monspecs.modedb_len > 0) {
            dev->edid = edid;
            dev->edid_size = i;
@@ -1407,8 +1546,8 @@ static int dlfb_setup_modes(struct dlfb_
         articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii default to resolution safe for projectors
         articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii (since they are most common case without EDID)
         */
-       fb_vmode.xres = 800;
-       fb_vmode.yres = 600;
+       fb_vmode.xres = 1024;
+       fb_vmode.yres = 768;
        fb_vmode.refresh = 60;
        default_vmode = fb_find_nearest_mode(&fb_vmode,
                         &info->modelist);
```

After applying this changes I've get ridden of the message I was getting earlier.

```
[ 236.494596] udlfb: Unable to get valid EDID from device/display
```
