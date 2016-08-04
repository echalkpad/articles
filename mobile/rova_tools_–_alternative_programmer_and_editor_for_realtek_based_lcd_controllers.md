# ROVA Tools – Alternative programmer, and editor for Realtek based LCD Controllers

[Original URL](http://tech.mattmillman.com/lcd/rovatools/)

> If you recognise this, chances are you'be been tinkering with LCD panels too: RM5451 LCD Controller The RM3251/RM5451/RM5251 LCD controllers offered hobbyists one of the first ever...

If you recognise this, chances are you'be been tinkering with LCD panels too:

[![RM5451 LCD Controller](http://techmattmillman.s3.amazonaws.com/wp-content/uploads/2014/05/rm5451.jpg)](http://techmattmillman.s3.amazonaws.com/wp-content/uploads/2014/05/rm5451.jpg) RM5451 LCD Controller The [RM3251/RM5451/RM5251](http://tech.mattmillman.com/lcd/rm5251/) LCD controllers offered hobbyists one of the first ever opportunities to create their own LCD display centric contraptions. Prior to this, the LVDS interface featured by almost all LCD panels was a mystery, with scores of tinkerers wondering how on earth to interface this to something more familiar, i.e. VGA or DVI.

One day, I saw this on eBay:

[![ROVA USB-TOOLS Programmer](http://techmattmillman.s3.amazonaws.com/wp-content/uploads/2014/05/rova_usb_tool-800x465.jpg)](http://techmattmillman.s3.amazonaws.com/wp-content/uploads/2014/05/rova_usb_tool.jpg) ROVA USB-TOOLS Programmer I've seen a few of these over the years, normally you get one posted to you when you engage with a LCD controller firmware company, right after signing a non-disclosure agreement, and handing over a lump of cash.

But on eBay? This is interesting. I had to hit 'buy'. The seller, NJYTouch has managed to get their hands on the programmer software for this family of boards, and a whole bunch of firmware images for different panels, and they're selling it on eBay for hobbyist money.

Disappointingly no source code was included, but this was no surprise to me. I've not seen a single line of source code for these controllers in my life. That isn't to say it's not out there, there's plenty of sites flogging off leaked source snapshots of these boards.

The good news is that the CPUs on these boards are almost always 8051s, which is pretty easy to reverse engineer.

Enter ROVATools. My creation of the last few years. I first started on it in 2011, then abandoned it due to lack of time, and picked it up again in 2014.

There are two main programs:

## ROVAEdit (The editor)

[![ROVAEdit GFF LCD Controller firmware editor](http://techmattmillman.s3.amazonaws.com/wp-content/uploads/2014/05/ROVAEdit.png)](http://techmattmillman.s3.amazonaws.com/wp-content/uploads/2014/05/ROVAEdit.png) ROVAEdit LCD Controller firmware editor This gives one access to pretty much all of what you'd need to change when creating an image for an unsupported panel. This program can only edit the firmware for supported R.RMxxxx boards.

## ROVATool – Alternative programming software

[![](http://techmattmillman.s3.amazonaws.com/wp-content/uploads/2014/05/ROVATool2.png)](http://techmattmillman.s3.amazonaws.com/wp-content/uploads/2014/05/ROVATool2.png) ROVATool – Alternative programmer for R.RMxxxx and PCB800099 The mainstay of my tool suite is descrambling the .GFF files so they can be edited. Because I don't have a mechanism to re-create .GFF files, this software has to be used to program the controllers.

## What's supported

The following controllers:

- R.RM3251
- R.RM3451
- R.RM5251
- R.RM5451
- [PCB800099](http://tech.mattmillman.com/lcd/pcb800099/) (Program only, no editing)

The following programmers:

Normally the Parallel programmer is for PCB800099 and ROVA USB-TOOLS is for R.RMxxxx, but with this software, you can use either programmer to program either board. I wouldn't recommend buying the Parallel programmer (aside from the obvious, how many PCs still have a Parallel port?) – The USB Programmer is just a much cleaner and easier solution.

The [ROVA USB-TOOLS is sold by NJYTouch](http://www.njytouch.com/NJY-Programming-Tool-Kit-For-USB-Port.htm), search eBay for _"1PC Programmer for Burning LCD Controller Board DIY USB"_

[![The supported Parallel port programmer. There's more than one design of these, and chances are, they're not all compatible.](http://techmattmillman.s3.amazonaws.com/wp-content/uploads/2014/05/DSC_9552-800x490.jpg)](http://techmattmillman.s3.amazonaws.com/wp-content/uploads/2014/05/DSC_9552.jpg) The supported Parallel port programmer. There's more than one design of these, and chances are, they're not all compatible. [![rtd2660ftdi](http://techmattmillman.s3.amazonaws.com/wp-content/uploads/2015/09/rtd2660ftdi.jpg)](http://techmattmillman.s3.amazonaws.com/wp-content/uploads/2015/09/rtd2660ftdi.jpg) Programming an RTD2660 based board with the Adafruit FT232H 

<span>Other benefits</span>

Even if you don't need to make an image for an unsupported LCD panel, there's still some benefits of using my tools:

- Ability to remove OSD popups which can pollute the display output
- Up-to-date programmer software with 64-bit operating system support
- No requirement for the ancient DLPortIO driver, for those concerned about the security vulnerabilities exposed by this package

[![OSD Popup](http://techmattmillman.s3.amazonaws.com/wp-content/uploads/2014/05/dviin1.jpg)](http://techmattmillman.s3.amazonaws.com/wp-content/uploads/2014/05/dviin1.jpg) OSD Popups making your project look unprofessional? Banish them with ROVAEdit ;-) [![ROVATool EDID Reader](http://techmattmillman.s3.amazonaws.com/wp-content/uploads/2014/05/edidreader.png)](http://techmattmillman.s3.amazonaws.com/wp-content/uploads/2014/05/edidreader.png) ROVATools comes with a built in EDID reader to read EDIDs directly from displays over the wire. Handy for the casual screen hacker.

## Download

I've split the download of this into two files:

- [ROVATool](http://tech.mattmillman.com/wp-content/files/rovatool_v2.0.zip) – This is the programmer only, for both RTD2120 and RTD2660 platforms
- [ROVASuite](http://tech.mattmillman.com/wp-content/files/rovasuite_v1.0.zip) – This is the programmer and editor, for only RTD2120 platforms
