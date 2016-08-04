# Scantool - OBDII Car Diagnostic Software for Linux

[Original URL](https://samhobbs.co.uk/2015/04/scantool-obdii-car-diagnostic-software-linux)

> My car has been a real pain recently: it has a recurring error code that the garage can't seem to fix (apparently, it's a fault in the air conditioning, but the car doesn't even have air con!)....

My car has been a real pain recently: it has a recurring error code that the garage can't seem to fix (apparently, it's a fault in the air conditioning, but the car _doesn't even have air con!_). Taking the car to the garage to get the code cleared means I lose the car for a day, which is getting annoying.

So, I decided to buy myself a USB connecter for the car's On Board Diagnostics (OBD) and see if I could get it working with Linux (specifically Kubuntu, but I doubt it matters). I was pleased to find that there was some decent Free Software available for Linux called [Scantool](http://sourceforge.net/projects/scantool/) that enabled me to read and clear the codes on my car.

Here's how to install and use the software, and some screenshots...

## What is an OBD Connector and Where to get one

Since 2004, the EU has required all cars to have a compatible connector for On Board Diagnostics. In 2007, the regulations were updated and now require all cars to have an OBD-II connector within 0.61m of the steering wheel, which makes it easily accessible. My boring old 2004 Ford Fiesta has one behind a little flap under the steering wheel.

![OBD-II.jpg](https://samhobbs.co.uk/sites/default/files/OBD-II.jpg)

OBD connectors come in USB and bluetooth variants, and you can pay through the nose for a fancy one or go with a cheap chinese knock-off. I opted for a [generic £6 USB model from Amazon](http://www.amazon.co.uk/gp/product/B00IWLLX1Y?psc=1&redirect=true&ref_=oh_aui_detailpage_o02_s00), which took ages to arrive but worked fine.

## Installing scantool

Installation for this one is really simple: it's in the Ubuntu Universe section. Install with these commands:

```
sudo apt-get update
sudo apt-get install scantool
```

The software needs to access the OBD device, which is found at `/dev/ttyUSB0`. This is owned by root with the group `dialout`, so the proper way to run the scantool software is to add your user to the dialout group, e.g.:

```
sudo usermod -a -G dialout sam
```

When researching before trying the software out, I noticed someone recommending you run scantool with root so that it can read the device. While running it with `sudo` would work, it violates the [principle of least privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege): scantool doesn't need superuser privileges to run, it just needs to be able to read the OBD interface. Running it as root is lazy - _don't do it_!

## Usage / Screenshots

To make the changes to your groups take effect, you need to either log out and back in, or you can use the `newgrp` command, which will let you use the new group straight away by changing your group ID to the one you specify, in this case dialout:

```
newgrp dialout
```

Then check you are in the dialout group:

```
groups
```

(You should see dialout as one of the groups in the list).

Now turn the ignition on, and then launch scantool:

```
scantool
```

You should see the main menu:

![scantool_mainmenu.png](https://samhobbs.co.uk/sites/default/files/scantool_mainmenu.png)

If you select "read codes" now you will see the error codes along with explanations of what they mean (the codes are standard, but sometimes manufacturers have non-standard meanings, so you may get more than one explanation for each code):

![scantool_readcodes2.png](https://samhobbs.co.uk/sites/default/files/scantool_readcodes2.png)

If you know what the code means and you are sure you want to clear it, you can select **clear**. _Obligatory warning: I am not a mechanic. If you're not sure what the codes mean, ask a mechanic, and don't clear it unless you're sure it's OK._

![scantool_readcodes_cleartroublecodes.png](https://samhobbs.co.uk/sites/default/files/scantool_readcodes_cleartroublecodes.png)

Back to the main menu, and choose sensor data to see realtime readouts from the sensors on the car (the car needs to be running for this to work):

![scantool_sensordata_carrunning.png](https://samhobbs.co.uk/sites/default/files/scantool_sensordata_carrunning.png)

The **tests** option isn't implemented in this build, but the software still does more than enough to be useful!
