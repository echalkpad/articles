# Building an SDR from scratch

[Original URL](http://electronics.kitchen/misc/freesrp/)

> The FreeSRP is an affordable SDR device I decided to design because there were no existing options that filled the gap between the $300 and relatively narrow bandwidth and low resolution HackRF and...

The FreeSRP is an affordable SDR device I decided to design because there were no existing options that filled the gap between the $300 and relatively narrow bandwidth and low resolution [HackRF](https://greatscottgadgets.com/hackrf/) and the better performing [USRP](https://www.ettus.com/product/details/UB200-KIT)s you can get for about $800\. Of course it will be fully open source, but some parts still need tidying up before I publish them.

The FreeSRP is based on the [Analog Devices AD9364](http://www.analog.com/en/products/rf-microwave/integrated-transceivers-transmitters-receivers/wideband-transceivers-ic/ad9364.html) transceiver. Some key specifications are:

- 56 MHz bandwidth
- 70 MHz to 6 GHz center frequency
- Full duplex operation

I am aware there are more alternatives now, such as the LimeSDR. However, I still think the FreeSRP is a useful tool and certainly has been a rewarding learning experience.

![HAB vs FreeSRP](http://electronics.kitchen/misc/freesrp/res/hab-vs-freesrp.png) 

<span class="img-caption">First High altitude ballon PCB vs. FreeSRP</span>

I started this project about 2 years ago, in the summer of 2014 (when I was 16). At that time, I had not done any serious hardware design apart from some (low speed) circuit boards for my [High Altitude Balloon](http://electronics.kitchen/nearspace). Therefore, I knew the FreeSRP's hardware would be challenging in almost every aspect: wide parallel buses running at relatively high speeds (100 MHz), USB 3.0 and RF signal paths requiring good performance at up to 6 GHz, complex power requirements with more than 7 rails at different voltages... Using modern ICs, and wanting to build a compact SDR system, I also needed to deal with high-density packages such as BGA or fine pitch QFN.

With my limited design experience I knew this was an ambitious project, but I could not even imagine what sorts of implications these requirements would have. So I started right away, knowing only that I wanted to use the AD9364 as my frontend/transceiver, and that I'd need an FPGA bridging the transceiver's interface to a separate USB 3.0 controller. I quickly decided on using a Xilinx Artix 7 FPGA based on cost, and a Cypress EZ-USB FX3 for the USB 3.0 interface, as it was the only option "easy" to implement at the time.

Reading the datasheets and looking at reference designs helped me understand further requirements and I gradually expanded the design until I had finished the [schematics](https://s3.amazonaws.com/freesrp/prototype-r2/freesrp-sch.pdf) a couple of weeks later.

I used Altium Designer for designing the FreeSRP. While it's not open-source, it was the most intuitive PCB package I found, and it offered many advanced features that would make my life a lot easier when routing controlled impedance traces, parallel buses, etc. When I finalize the design I might remake the whole thing in KiCad, which is open source.

## From initial design to functioning prototype

![Almost-final PCB layout for the first revision](http://electronics.kitchen/misc/freesrp/res/r0-layout.png) 

<span class="img-caption">Almost-final PCB layout for the first revision</span>

So I finished the schematics – now PCB layout had to be done. Especially for the initial prototype, cost was the most important constraint. I figured I could just barely do the layout I needed on a four-layer PCB using the [OSH Park](https://oshpark.com) fabrication service, the cheapest one I have found for low volume (three pieces) manufacturing. Apart from having four layers only, OSH Park's process offers quite good specifications: 5 mil traces with 5 mil spacing, 10 mil via holes, and the well-characterized Isola FR408 PCB substrate (important to ensure RF signal integrity at high frequencies).

Optimal component placement probably is the most important PCB design aspect, so I made sure everything was placed in a way that would minimize the length of connections. I also wanted to make the device as compact as possible to cut board costs and increase portability. Having placed about half of the components and knowing more or less how I'd do the rest, I started routing some connections.

Basically, I started on one side of the signal path (the USB interface) and built up the design by gradually adding parts following the signal path until I arrived at the RF interface and was done. Components not in this path (mostly the voltage regulators) were added as I saw that PCB space was available to be filled.

![Partially placed and routed board](http://electronics.kitchen/misc/freesrp/res/screenshot-partial.jpg) 

<span class="img-caption">Partially placed and routed board (only top layer visible)</span>

While the basic structure of the design stayed the same, I did move around, remove and remake lots of parts of the design several times until I got them "right".

Fanning out all of the balls on the BGA packages was probably the most painful aspect of the whole design – mainly due to only having 4 layers.

After some more weeks I was finally done. The design passed all rule checks, and I had carefully gone over it making sure nothing funny would get into production. This was one of my main worries, as repairing a four layer board with hundreds of SMD components after soldering would be virtually impossible.

### Manufacturing the first prototype

After much hesitation I ordered three boards from OSH Park, which arrived sometime in January 2015\. Yay!

![Bare PCB](http://electronics.kitchen/misc/freesrp/res/r0-bare.png) 

<span class="img-caption">First FreeSRP PCBs ever!</span>

I had also ordered a Kapton stencil to apply solder paste. Even [my reflow controller](http://electronics.kitchen/reflow) and oven were ready to be used. Now I needed to order the ca. 300 different components.

As the FreeSRP uses a PCB with double-sided load, I'd first place and solder all the components on the bottom layer. During the design phase I had made sure to only use small components: the bottom would get soldered again while soldering the upper side, but the surface tension of molten solder easily keeps small components in place even when upside down.

![PCB with top side stencil](http://electronics.kitchen/misc/freesrp/res/r0-stencil.jpg) 

<span class="img-caption">PCB with stencil on the top side, after applying solder paste. Bottom already soldered.</span>

#### Partial assembly

Having three PCBs allowed me to first partially assemble the prototype. I did one board with only the voltage regulators installed and found an issue with the 1.8V regulator. This was not a significant problem, however, as I could replace it with my lab power supply. Also, the 1.3V regulators the transceiver needs had a (non-fixable) layout problem – so at least the RF frontend part of my design would have to wait until the second revision for validation.

![First time I powered up the partially assembled board](http://electronics.kitchen/misc/freesrp/res/r0-first-power.jpg) 

<span class="img-caption">First time I powered up the partially assembled board</span>

The second board I'd assemble would contain the whole digital section of the SDR: USB interface and FPGA. It was time for hand-placing my first BGA components.

After an extremely tense (...expensive ICs, extremely critical alignment, no possibility to repair...) three hours of component placement, I very carefully placed the PCB in the oven. And – I did not really know what to expect – the soldering process went fine.

#### First power-up

I was really afraid of powering up my board. I knew the power supplies worked, but I could still potentially fry my painstaikingly hand-placed, expensive components by turning on my power supply.

I do not know of any optimal way to power up a previously untested design. What I ended up doing was just setting a very low current limit on my power supply, increasing it until either stuff seemed to be working, not releasing any magic smoke, or it seemed to draw too much current.

![PCB with digital sections assembled](http://electronics.kitchen/misc/freesrp/res/r0-testing-fpga.jpg) 

<span class="img-caption">Partially assembled board with USB controller and FPGA</span>

Luckily, current draw seemed normal, and the power LED was on. Neither the FPGA nor the USB 3.0 controller were warm to the touch. Looked fine! So I plugged it into a USB port on my computer, and the Cypress chip was recognized. The Xilinx tools also connected to the FPGA via JTAG, so everything seemed to be working.

(More design mistakes: The footprint for the USB 3.0 connector had an error that only allowed the USB 2.0 lanes to be accessible. Not a big deal for testing basic functionality, but I'd not be able to test USB 3.0 throughput.)

### Second revision

The second revision fixed all issues that I managed to find in the first version:

- USB 3.0 footprint
- 1.8V regulator
- 1.3V regulators
- LED indicators

That meant the digital/processing part of the system should be fully functional, and that I could assemble and test the transceiver/front-end.

I first assembled everything but the transceiver, so that I could start writing software and test USB 3.0 transfers. That went very well, and I made a lot of progress writing the firmware for the USB controller and learning the Verilog hardware description language to start implementing the parallel interface to the USB controller (I had never programmed an FPGA before starting this project).

Developing the FPGA design was one of the hardest parts of this project:

- I found it was quite difficult to find beginner-friendly documentation on using the tools and using the included IP blocks.
- Error messages from the [Vivado Design Suite](http://www.xilinx.com/products/design-tools/vivado.html) were often very difficult to understand for me, and using pre-made IP resulted in overwhelming numbers of warnings, that rendered them pretty much useless. It is very likely that I'm just using the tools in an incorrect way, but the lack of approachable documentation made properly understanding them harder than I expected. Also it takes several minutes to report seemingly trivial mistakes such as syntax errors.

![Screenshot showing Vivado warnings](http://electronics.kitchen/misc/freesrp/res/vivado-errors.png) 

<span class="img-caption">So many warnings it’s not fun anymore</span>

- Synthesizing and implementing your design every time you make a change is not practical, as it takes a very long time to do even for a small design (more than 15 minutes). You need to simulate as much as possible, which makes the learning curve for FPGA design even steeper.
- Debugging a design running on the FPGA is difficult without using the Xilinx-provided Integrated Logic Analyzer (ILA). The ILA, however, cost hundreds of dollars before it was made free in 2016 (so I could not take advantage of it until a late stage in the design, and often just resorted to using the onboard LEDs or routing signals to GPIO pins as to probe them with my oscilloscope).

Also, it took me until this time to understand the FPGAs clocking resources, so I realized I'd made some important design mistakes by not routing some of the clock signals from the transceiver to clock-capable inputs on the FPGA.

After having played around with the digital part of the design I decided it was time to try to assemble a new board, now including the transceiver. So, I placed by hand the 300 components, soldering the bottom side first. But when soldering the top side it happened: the controller on my reflow oven stopped working, and left the oven switched on. I lost the temperature readout, and could only switch the oven on and off manually. I tried to leave the oven on, hoping it would still solder fine, but worrying I would overshoot my intended peak temperature.

And it did not work: Even though the board looked great, there were shorts on main power rails, and powering it up, the FPGA was getting hot. I tried to fix it probing around, removing things, but nothing helped. It was completely fried. This took me some time getting over, as I had just lost about 400$ in parts. It was definitely not encouraging.

Nevertheless, I still wanted to finish this project, so I reordered some parts and tried again a week later. I was incredibly nervous during the whole assembly and soldering process (especially because I was using the last one of the three PCBs I had), but the manufacturing process worked this time (as it had every time but last time).

![Fully assembled FreeSRP](http://electronics.kitchen/misc/freesrp/res/r1-dead-xcvr.jpg) 

<span class="img-caption">Fully assembled FreeSRP in acrylic enclosure</span>

Everything that had worked on the previous, partially built prototype worked on this one, as well. But all my attempts to communicate with the transceiver over its SPI configuration port failed: it was not responding. I also noticed that it was getting hot (maybe to about 60°C) after just a few seconds of being powered on. This was very worrying, as the device should have been in a "sleep" state, dissipating very little power – so I figured the problem probably was in hardware.

After checking all power rails, I could still not find any possible issues. So I went back to the schematics and started checking all the support circuitry for the transceiver. It took me some time, but then I found this:

![Something awful](http://electronics.kitchen/misc/freesrp/res/shit.png) 

<span class="img-caption">Oh no…</span>

One 698Ω and one 546Ω valued resistor (in series, so 1234Ω total) instead of the 14.3kΩ resistor specified by the datasheet!

Asking on Analog Devices' support forum, I was told to try replacing the resistors with the correct one. I did that, and the part indeed did not warm up as much as before, but still, I was unable to get it to work. It was safe to assume the chip was dead. **:(**

At this point, I considered giving up on this project, acknowledging the fact that it was way too ambitious for the limited electronics knowledge I had. But I continued... concentrating on finishing as much of the software/FPGA design as possible using the partially working hardware.

Finally, I managed to write an FPGA design that incorporated Analog Devices' driver for the AD9364 (which handles calibration and set up for the transceiver), and generated test signals which could be introduced into the whole signal chain: This way I could work on the interface between USB controller and FPGA and continue writing the C++ library I had started to control the SDR from the computer.

I also extended the [osmocom gr-osmosdr GNURadio block](http://sdr.osmocom.org/trac/wiki/GrOsmoSDR), making my SDR fully compatible with GNURadio ([my fork is available here](https://github.com/FreeSRP/gr-osmosdr))!

### Third revision

![FreeSRP, third revision](http://electronics.kitchen/misc/freesrp/res/r2.jpg) 

<span class="img-caption">Latest version of the FreeSRP</span>

Fixed in this revision:

- Clock inputs into FPGA clock cabaple pins
- 14.3kΩ resistor
- Replaced transceiver oscillator with a crystal to simplify the reference clock circuitry (eliminating potential for further issues)

This, again, fixed all issues I'd discovered up to now. I also had done more revisions of the board and spent way more money than I initially planned for... even though in hindsight, having only three revisions for a fully functioning, moderately complex design, with no previous experience working on something like this, probably is not _that_ bad.

One major change I did was switching from the four-layer OSH Park process to a six layer stackup. This multiplied the cost of the prototype boards, but allowed me to dramatically increase clearance between many critical signal traces, allowing the parallel buses to finally run at the maximum required clock speeds very reliably.

I also got myself some very nice stainless steel stencils, which were super easy to use and produced much better results than the Kapton ones:

![PCB and stencil in stencilling jig](http://electronics.kitchen/misc/freesrp/res/steel-stencil.jpg) 

<span class="img-caption">PCB and steel stencil placed in stencilling jig</span>

I could verify that the transceiver was working very easily, as I had already developed all the software and integrated the driver provided by Analog Devices for the AD9364 into my design. It did not take me long to receive my first samples using GNURadio!

![FFT spectrum](http://electronics.kitchen/misc/freesrp/res/950mhz.png) 

<span class="img-caption">Spectrum around 950 MHz, at the full 56 MHz bandwidth</span>

Finally, all the hard work and failed attempts had paid off. And, some more weeks later I could verify the transmitter worked as well (again, fully compatible with GNURadio, with full duplex working as well – not at the full bandwidth, though). However, in this revision there is still a problem with the amplifier at the RF output, and the signal is extremely weak.

Still, at this point, I basically have a fully functioning SDR.

There still is a lot that needs to be done, though. For example, I still need to properly characterize the receiver and transmitter performance. Also, I'm thinking about doing a small production run – this means fixing the last few hardware issues, optimizing the design, making sure it is manufacturable...

## Available software and compatibility

To interface with the FreeSRP, I have written a small C++ library, [libfreesrp](https://github.com/FreeSRP/libfreesrp). It uses [libusb](http://www.libusb.org/) and is very easy to use as it is quite simple itself. Initializing the SDR is as simple as instantiating a `FreeSRP` object:

```
FreeSRP::FreeSRP srp;
cout << "version: " << srp.version() << endl;
```

Setting the center frequency and bandwidth:

```
srp.send_cmd(srp.make_command(SET_RX_LO_FREQ, 2.4e9));
srp.send_cmd(srp.make_command(SET_RX_SAMP_FREQ, 1e8));
```

You can then start the receiver and get a sample:

```
srp.start_rx();

sample s;
bool success = srp.get_rx_sample(s);
```

There are also some simple utilities. `freesrp-ctl` can be used to load a bitstream onto the FPGA and perform various other configurations (such as setting bandwidth and frequency) using a simple interactive command-line interface.

`freesrp-rec` enables the receiver and writes the received samples to a file or to `stdout`. This is useful because you can pipe samples into programs like [baudline](http://www.baudline.com/), in real time and at full bandwidth. For example:

```
freesrp-rec -f433.5e6 -b10e6 -g42 -o- | baudline -reset -stdin -channels 2 -quadrature -format le16 -samplerate 10000000
```

Using libfreesrp, I have extended [gr-osmosdr](http://sdr.osmocom.org/trac/wiki/GrOsmoSDR) to provide support for the FreeSRP in GNURadio. You now only need to specify `freesrp` in the osmocom Source and Sink blocks' device arguments. Here's an 802.15.4 transceiver made in GNURadio Companion, using some [gr-ieee802-15-4](https://github.com/bastibl/gr-ieee802-15-4) blocks:

![GNURadio companion flowchart](http://electronics.kitchen/misc/freesrp/res/gnuradio-802-15-4.png) 

<span class="img-caption">GNURadio Companion flowchart</span>

Conveniently, the FreeSRP now also works with all `gr-osmosdr` based utilities, such as `grgsm_scanner`. `grgsm_scanner` is a little program that will scan different frequencies looking for GSM base stations, reporting what it finds:

![GNURadio companion flowchart](http://electronics.kitchen/misc/freesrp/res/grgsm-scanner.png) 

<span class="img-caption">If you look up the codes you will know roughly where I live</span>

[Gqrx](http://gqrx.dk) uses the osmocom block, too, and also works flawlessly with the FreeSRP.

Recently I came across [luaradio](http://luaradio.io). Should be easy to add FreeSRP support to that as well!

## Conclusion

I have discovered that taking on large projects, even when not knowing how to tackle problems that might arise, is a very effective way of learning for me. It's just important to be persistent, as I've seen that almost any problem can be solved on your own – which is incredibly rewarding – even if you get stuck and seem to not make progress for a while.

And now I get to play with my SDR!

You can also check out the the [project page](http://electronics.kitchen/freesrp) or the [documentation](http://electronics.kitchen/docs/freesrp/). There's also [a reddit thread](https://www.reddit.com/r/electronics/comments/4uic5r/sdr_platform_ive_been_working_on/).

<span id="copyright">Copyright © 2016, Lukas Lao Beyer</span>
