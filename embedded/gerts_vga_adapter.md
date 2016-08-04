# Gert's VGA Adapter

[Original URL](https://www.raspberrypi.org/blog/gert-vga-adapter/)

> The Raspberry Pi has an HDMI port to connect a display. If your monitor only has VGA, you have to use an adapter. Because this requires a digital-to-analogue conversion, those adapters can be quite...

The Raspberry Pi has an HDMI port to connect a display. If your monitor only has VGA, you have to use an adapter. Because this requires a digital-to-analogue conversion, those adapters can be quite pricey, and they can draw lots of power. So our friend Gert van Loo (who developed the [Alpha board](http://www.raspberrypi.org/alpha-boards-in-manufacture/) that became the Raspberry Pi, and the man behind the [Gertboard](http://uk.farnell.com/gertboard/gertboard/assembled-gertboard-for-raspberry/dp/2250034) and [Gertduino](http://www.element14.com/community/docs/DOC-64326/l/gertduino-add-on-board-for-raspberry-pi)) has created a VGA adapter that uses the Pi's GPIO.

[![VGA-My_Pi_700](https://www.raspberrypi.org/wp-content/uploads/2014/09/VGA-My_Pi_700-500x310.jpg)](https://www.raspberrypi.org/wp-content/uploads/2014/09/VGA-My_Pi_700.jpg)

This wasn't possible on the Model A or B, but now the [B+](http://www.raspberrypi.org/introducing-raspberry-pi-model-b-plus/ "New product launch! Introducing Raspberry Pi Model B+") exposes 40 GPIO pins, there's more to play with. As well as just allowing you to connect a VGA monitor natively, it **also means you can use it as a secondary monitor alongside HDMI**. And unlike composite video, the DPI interface can be run independent of the HDMI. The software for dual screens is still under development, but we expect that to arrive in the next couple of weeks. Running two screens at maximum resolution will consume SDRAM bandwidth, and is yet to be tested. (And there's a catch: as the board uses most of your GPIO pins, you lose access to them.)

The VGA output supports the same resolution as your HDMI one: from 640 x 480 up to 1920 x 1024 at 60fps. At the highest resolution the pixel quality is almost as good as HDMI. The adapter uses a simple resistor ladder network as a digital-to-analogue converter, so the colour quality depends on how well-balanced your resistors are. There is slight colour banding, and with 6 bits per channel you have a maximum of 262144 colours.

Dom has been working on the software side and the new DPI (read: VGA) driver software has been added to the latest release.

[![IMG_0549.resized](https://www.raspberrypi.org/wp-content/uploads/2014/09/IMG_0549.resized-500x333.jpg)](https://www.raspberrypi.org/wp-content/uploads/2014/09/IMG_0549.resized.jpg)

_"Where can I buy one?"_, I hear you ask. Currently, nowhere. But Gert has made the VGA adapter open hardware, so you can make it yourself, or find yourself an enthusiastic partner and have it made. All the data is in the public domain on [GitHub](https://github.com/fenlogic/vga666). Besides the manual and schematics, you will also find the database for the PCB and the Gerber files. The PCB design supports both through-hole and SMD parts. The design consists of:

- 1 PCB
- 2 connectors
- 20 resistors

The cost is not prohibitive, but having a single PCB made is rather expensive, so you might want to collect a group of interested people and order a batch; if you're interested in doing that, head over to the [forums](http://www.raspberrypi.org/forums/) and see if you can organise a group buy.

See vga666 at [github.com/fenlogic/vga666](https://github.com/fenlogic/vga666) (it's 6 bits per colour channel, hence 666...)

Gert's looking to get the PCBs produced, and hopefully the manufacturer will be able to put them on sale (we'll update with a link) – but they're so easy to make we anticipate they'll be generally available before long anyway. Gert says he expects in due time that a far-east manufacturer will see fit to sell them for two dollars.

Want to see a prototype? Of course you do.

[![IMG_0545.resized](https://www.raspberrypi.org/wp-content/uploads/2014/09/IMG_0545.resized-500x333.jpg)](https://www.raspberrypi.org/wp-content/uploads/2014/09/IMG_0545.resized.jpg) Click to embiggen, and marvel at Gert's work soldering together some of those teeny resistors.

**Update 23/9: The PiHut have launched a Kickstarter project to crowd fund this as a product. It's available in kit form for £6 and the target has already been reached – get yours now at [kickstarter.com](https://www.kickstarter.com/projects/pisupply/gert-vga-666-kit-hardware-vga-for-raspberry-pi)!**
