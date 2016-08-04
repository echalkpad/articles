# ESP8266 Troubleshooting Guide

[Original URL](http://randomnerdtutorials.com/esp8266-troubleshooting-guide/)

> The ESP8266 has a few common issues, specially when you are trying to flash a new firmware or uploading scripts. This is a companion guide to the Home Automation using ESP8266 and...

The ESP8266 has a few common issues, specially when you are trying to flash a new firmware or uploading scripts.

_This is a companion guide to the [Home Automation using ESP8266](http://randomnerdtutorials.com/home-automation-using-esp8266/) and [Password Protected Web Server](http://randomnerdtutorials.com/password-protected-web-server/) eBooks._

Here's a compilation with some of the most common problems with the ESP8266 and how to fix them.

## ESP8266 Troubleshooting – NodeMCU Flasher

### **Where do I download the NodeMCU flasher?**

Go to the NodeMCU flasher GitHub repository and download the flasher for your Windows PC bit version by clicking the button that says "Raw":

- [NodeMCU flasher Windows 64 bits](https://github.com/nodemcu/nodemcu-flasher/blob/master/Win64/Release/ESP8266Flasher.exe)
- [NodeMCU flasher Windows 32 bits](https://github.com/nodemcu/nodemcu-flasher/blob/master/Win32/Release/ESP8266Flasher.exe)

### **Which settings should I use with the NodeMCU flasher?**

The NodeMCU flasher already comes with the right settings by default.

If you have changed some of the settings, I highly recommend that you re-download the NodeMCU flasher.

Here's my current settings:

![nodemcu flasher](http://i1.wp.com/randomnerdtutorials.com/wp-content/uploads/2016/01/nodemcu-flasher.png?resize=300%2C174%20300w,%20http://i1.wp.com/randomnerdtutorials.com/wp-content/uploads/2016/01/nodemcu-flasher.png?w=572%20572w)

### **After I press the "Flash" button nothing happens, the NodeMCU flasher doesn't start the flashing process**

If you pressed the "Flash" button and nothing happens... It means one of these two things:

- Problem 1 – Your ESP isn't in flash mode (double-check if GPIO 0 is connected to GND on power up)
- Problem 2 – Your FTDI Programmer can't supply enough current to your ESP

![nothing happens](http://i1.wp.com/randomnerdtutorials.com/wp-content/uploads/2016/01/nothing-happens.png?resize=300%2C174%20300w,%20http://i1.wp.com/randomnerdtutorials.com/wp-content/uploads/2016/01/nothing-happens.png?w=571%20571w)

**Problem 1** – How to make your ESP go into flash mode:

1. Close the NodeMCU flasher window
2. Remove power from your ESP8266
3. Having your ESP connected like [this](http://randomnerdtutorials.com/wp-content/uploads/2015/02/ESP8266-Flasher.png) (double check that GPIO 0 is connected to GND)
4. Apply power to your ESP8266 and open the NodeMCU flasher
5. Press the "Flash" button

If it's still saying "Waiting MAC", then try the following:

1. Having the NodeMCU flasher still open
2. Connect a wire from your ESP8266 reset pin to GND
3. Remove that wire from GND and connect to VCC

Repeat steps 2 and 3 a few times until your ESP reboots and hopefully the NodeMCU flasher can detect your ESP and start the flashing process.

**Problem 2 –** If your FTDI can't supply enough current, you might need to buy a new FTDI programmer or power your ESP8266 with an external power supply.

### **NodeMCU flasher gets stuck at a certain percentage**

![nodemcu stuck](http://i1.wp.com/randomnerdtutorials.com/wp-content/uploads/2016/01/nodemcu-stuck.png?resize=300%2C177%20300w,%20http://i1.wp.com/randomnerdtutorials.com/wp-content/uploads/2016/01/nodemcu-stuck.png?w=561%20561w)

1. Remove power from your ESP+FTDI:
2. Close the NodeMCU flasher window
3. Plug your ESP+FTDI to your computer again
4. Open NodeMCU flasher and try the 19200 baud rate

If this process fails, please repeat the same procedure for the next baud rates (38400, 57600, 74880 and 115200).

I don't know why, but at least 5 people faced the same problem and this trick solved it. So at a baud rate of 57600 or 115200 I think it will flash 100%. I don't have any logical explanation, since this is not very common.

### **How does the NodeMCU flasher should look after a successful flash?**

It should have a green arrow in the bottom left.

![nodemcu flasher flashed](http://i1.wp.com/randomnerdtutorials.com/wp-content/uploads/2016/01/nodemcu-flasher-flashed.png?resize=300%2C176%20300w,%20http://i1.wp.com/randomnerdtutorials.com/wp-content/uploads/2016/01/nodemcu-flasher-flashed.png?resize=768%2C450%20768w,%20http://i1.wp.com/randomnerdtutorials.com/wp-content/uploads/2016/01/nodemcu-flasher-flashed.png?w=813%20813w)

### **Unbricking the FTDI Programmer on Windows PC**

If you have a brand new FTDI Programmer and you need to install your FTDI drivers on Windows, visit this website for the official drivers: <http://www.ftdichip.com/Drivers/VCP.htm>.

In alternative, you can contact the seller that sold you the FTDI Programmer.

If you're having trouble installing the FTDI drivers on Windows 7/8/8.1/10 it's very likely that FTDI is bricked.

Follow this tutorial to fix that: <http://youtu.be/SPdSKT6KdF8>.

In the video I mentioned earlier, the guy tells you to download the drivers from the FTDI website, read carefully the YouTube description of his video to find all the links. Here's the drivers you need: <http://www.ftdichip.com/Drivers/CDM/CDM%20v2.12.00%20WHQL%20Certified.zip>

## ESP8266 Troubleshooting – ESPlorer IDE

### **ESPlorer IDE Error: only one tcp server allowed**

This means that you tried to upload multiple scripts and you ESP is still running the old script with a web server. To fix it, you simply need to send these commands to delete all the files and restart your ESP:

1. file.format()
2. node.restart()

### **ESPlorer IDE Error: can't autodetect firmware**

![can't autodetect firmware](http://i1.wp.com/randomnerdtutorials.com/wp-content/uploads/2016/01/cant-autodetect-firmware.png?resize=300%2C130%20300w,%20http://i1.wp.com/randomnerdtutorials.com/wp-content/uploads/2016/01/cant-autodetect-firmware.png?w=590%20590w)

This sounds like your ESP it's still in flash mode. How to make your ESP go into user mode:

1. Close the ESPlorer IDE connection
2. Remove power from your ESP8266
3. Having your ESP with GPIO 0 connected to VCC
4. Apply power to your ESP8266 and re-establish the connection with ESPlorer IDE

If it's still saying "can't autodetect firmware", then try the following:

1. Having the ESPlorer IDE connection still printing "....."
2. Connect a wire from your ESP8266 reset pin to GND
3. Remove that wire from GND and connect to VCC

Repeat steps 2 and 3 a few times until your ESP reboots and hopefully the ESPlorer IDE can detect your ESP.

### **My code disappears when I restart the ESP8266**

If you upload a script to your ESP and when you restart it your ESP doesn't do anything. It means that your ESP couldn't find the script or it occurred a memory issue.

This can be solved like this:

1. You didn't save your script with this exact name 'init.lua'. Re-uploading the same script, but with the 'init.lua' name should solve your problem
2. It can be a memory issue. Re-flashing the ESP with NodeMCU flasher usually solves that problem

### **Finding the ESP8266 IP Address**

Before you start make sure you check these two items:

1. Verify that the script uploaded to your ESP has the right network credentials
2. Make sure your ESP is near your router

Here's what you can do to find the IP address:

**Solution 1** – Sending a command with the ESPlorer IDE

1. Send the command "print(wifi.sta.getip())" with the ESPlorer IDE and it should print your IP Address

![ESP IP Address](http://i2.wp.com/randomnerdtutorials.com/wp-content/uploads/2016/01/ESP-IP-Address.png?resize=300%2C22%20300w,%20http://i2.wp.com/randomnerdtutorials.com/wp-content/uploads/2016/01/ESP-IP-Address.png?w=536%20536w)

**Solution 2** – Install an IP Scanner Software

1. An IP Scanner software searches for all the devices in your network
2. Download this free software:

  1. Windows PC: www.advanced-ip-scanner.com
  2. MAC OS X, Windows or Linux: <http://angryip.org>

3. Install one of these softwares (while having your ESP running with that web server script)

4. Open the IP Scanner software and click "Scan"
5. Let that process finish (it can take a couple of minutes)

In my case, it found my ESP. Now if I type 192.168.1.95 in my browser I can see the ESP web server.

![advanced ip scanner](http://i1.wp.com/randomnerdtutorials.com/wp-content/uploads/2016/01/advanced-ip-scanner.png?resize=300%2C239%20300w,%20http://i1.wp.com/randomnerdtutorials.com/wp-content/uploads/2016/01/advanced-ip-scanner.png?w=590%20590w)

## ESP8266 Troubleshooting – Arduino IDE

### **I can't upload scripts to my ESP8266 using the Arduino IDE**

How to make your ESP go into upload mode when using the Arduino IDE:

1. Remove power from your ESP8266
2. Having your ESP connected like [this](http://randomnerdtutorials.com/wp-content/uploads/2015/02/ESP8266-Flasher.png) (double check that GPIO 0 is connected to GND)
3. Apply power to your ESP8266 and open the Arduino IDE
4. Press the "Upload" button

## **Did you experience other problems?**

**<span>Please leave a comment below</span>

, so I can update the troubleshooting guide with more problems and solutions.**

Thanks for reading,

-Rui Santos
