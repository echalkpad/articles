# Install NetBeez software agent

[Original URL](https://netbeez.net/product/install-netbeez-software-agent/)

> SOFTWARE AGENT 1- Add the NetBeez software repository, update the database, and install the netbeez-agent package:

**<embed src="https://netbeez.net/wp-content/uploads/2016/01/software-agent-icon-150x150.png%20150w,%20https://netbeez.net/wp-content/uploads/2016/01/software-agent-icon-300x300.png%20300w,%20https://netbeez.net/wp-content/uploads/2016/01/software-agent-icon.png%20500w" class="alignleft wp-image-7405" width="50">**

<br>
**SOFTWARE AGENT**

**1-** Add the NetBeez software repository, update the database, and install the netbeez-agent package:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-p">#!/bin/sh</span>
</p>
  <p>
  <span class="crayon-e">sudo </span>
  <span class="crayon-i">echo</span>
  <span class="crayon-s">“deb <a href="http://repo.netbeez.net">http://repo.netbeez.net</a> wheezy main”</span>
  <span class="crayon-o">&gt;&gt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">etc</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">apt</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">sources</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">list</span>
</p>
  <p>
  <span class="crayon-e">sudo </span>
  <span class="crayon-v">wget</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">O</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">http</span>
  <span class="crayon-o">:</span>
  <span class="crayon-c">//repo.netbeez.net/netbeez_pub.key | sudo apt-key add -</span>
</p>
  <p>
  <span class="crayon-e">sudo </span>
  <span class="crayon-v">apt</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">get </span>
  <span class="crayon-e">update</span>
</p>
  <p>
  <span class="crayon-e">sudo </span>
  <span class="crayon-v">apt</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">get </span>
  <span class="crayon-e">install </span>
  <span class="crayon-v">netbeez</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">agent</span>
</p>
</div>
 | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**2-** Save the private key that you received with the welcome email at step 1 as file /etc/netbeez/netbeez-agent.pem<br>
**3-** Edit the configuration file /etc/netbeez/netbeez-agent.conf and replace "hostname" with your dashboard instance name

 | <div class="crayon-pre">
  <p>
  <span class="crayon-sy">{</span>
  <span class="crayon-s">“host”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“hostname.netbeezcloud.net”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">“port”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“20019”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">“secure_port”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“20018”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">“interfaces”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“eth0”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">“model”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“virtual-model-ova”</span>
  <span class="crayon-sy">}</span>
</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**<embed src="https://netbeez.net/wp-content/uploads/2016/01/virtual-agent-icon-150x150.png%20150w,%20https://netbeez.net/wp-content/uploads/2016/01/virtual-agent-icon-300x300.png%20300w,%20https://netbeez.net/wp-content/uploads/2016/01/virtual-agent-icon.png%20500w" class="alignleft wp-image-7406" width="50">**

<br>
**VIRTUAL AGENT**

**1-** Download the OVA file

**OVA:** [netbeez-virtual-agent.ova](https://storage.googleapis.com/netbeez-public/netbeez-virtual-agent.ova)<br>
**MD5:** 

<span class="s1">
  <a href="https://storage.googleapis.com/netbeez-public/netbeez-virtual-agent.md5">netbeez-virtual-agent.md5</a>
</span>

**2-** Load the OVA on VirtualBox (available [here](https://www.virtualbox.org/wiki/Downloads)) or vSphere with the following settings:

**vCPU:** 1<br>
**Sockets:** 1 or 2<br>
**RAM:** 1 or 2 GB<br>
**HDD:** Thin provisioning

**3-** Once the OVA is booted, login with netbeez/netbeez (username/password) and follow this procedure to configure the virtual agent:

**i)** Save the private key that you received with the welcome email at step 1 as file /etc/ssl/certs/netbeez.pem

**ii)** 

<span class="s1">Edit the configuration file </span>

<span class="s1">/etc/init.d/nbagent_prod </span>

<span class="s1">and specify your instance address. </span>

<span class="s1">Replace the &lt;blank&gt; entry in line 21 with your instance name.</span>

<span class="s1">For example, if your instance name (hostname) is acme, your instance </span>

<span class="s1">address will be acme.netbeezcloud.net, and your line 21 </span>

<span class="s1">will look like:</span>

<span class="s1">DAEMON_ARGS=” acme.netbeezcloud.net 20019 20018″</span>

<span class="s1">P.S. Leave the space before your instance address.</span>

![Raspberry_Pi_Logo.svg](https://netbeez.net/wp-content/uploads/2015/08/Raspberry_Pi_Logo.svg_.png)<br>
**RASPBERRY PI IMAGE**

You can download and install a preconfigured NetBeez Raspbian image on a Raspberry Pi. The image has been tested on the Raspberry Pi Model B, Model B+, and Model 2.

**1-** Download the NetBeez Raspian image

**IMG:** [generic_free_rpi-model-b_0.11.0_20160119.zip](https://console.developers.google.com/m/cloudstorage/b/netbeez-public/o/generic_free_rpi-model-b_0.11.0_20160119.zip)<br>
**MD5:** 

<span class="s1">
  <a href="https://console.developers.google.com/m/cloudstorage/b/netbeez-public/o/generic_free_rpi-model-b_0.11.0_20160119.zip.md5">generic_free_rpi-model-b_0.11.0_20160119.zip.md5</a>
</span>

**2-** Install the NetBeez Raspbian on your Raspberry Pi on an 8 GB SD card. Instructions can be found [here](https://www.raspberrypi.org/documentation/installation/installing-images/).<br>
**3-** Edit the configuration file /etc/netbeez/netbeez-agent.conf and replace "hostname" with your dashboard instance name

 | <div class="crayon-pre">
  <p>
  <span class="crayon-sy">{</span>
  <span class="crayon-s">“host”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“hostname.netbeezcloud.net”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">“port”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“20019”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">“secure_port”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“20018”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">“interfaces”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“eth0”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">“model”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“rpi-model-b”</span>
  <span class="crayon-sy">}</span>
</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## FAQ

**What's should I do after I have loaded the agents?**

Login to your dashboard instance, whose URL was sent with the welcome email, and then create and review your monitoring targets.

**The OVA file I downloaded has .ovf extension?**

This is a known issue with Chrome, please see the reported issue [here](https://code.google.com/p/chromium/issues/detail?id=311218). You can still load and use the image.
