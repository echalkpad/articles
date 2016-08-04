# Connect your Raspberry Pi to Wireless Enterprise Environments with WPA supplicant

[Original URL](https://netbeez.net/2014/10/14/connect-your-raspberry-pi-to-wireless-enterprise-environments-with-wpa-supplicant/)

> It's not a surprise that, at NetBeez, we love the Raspberry Pi! This little device can do magic on wired as well as wireless networks. Few weeks ago, Panos posted an article that offered some...

<embed src="https://netbeez.net/wp-content/uploads/2014/10/Wireless-Client-AP-Radius-300x147.png%20300w,%20https://netbeez.net/wp-content/uploads/2014/10/Wireless-Client-AP-Radius.png%20850w" class="alignleft size-medium wp-image-1351" width="300">

It's not a surprise that, at NetBeez, we love the Raspberry Pi! This little device can do magic on wired as well as wireless networks. Few weeks ago, Panos posted an article that offered some examples on how to use the [Raspberry Pi for distributed monitoring with Iperf](http://netbeez.net/2014/08/19/raspberry-pi-and-distributed-network-monitoring-iperf/). Here, I want to talk a little about using this platform, or any other Linux client, to connect to enterprise wireless networks.

## **

Wireless security**

First of all, there are different types of security settings that can be used for wireless networks. In the section below, I want to list each of one them, from the weakest to the most secure option and shed some light on all options available.

### Open

This configuration does not require any password. The wireless client simply connects to the open SSID. This is the case of a coffee shop, like Starbucks, or of an enterprise's guest internet access that offers free wireless services to its customers. Typically, a client that accesses such a free wireless service is redirected to a captive portal where it has to accept the terms and conditions of service before being able to use the network.

### WEP

WEP (Wired Equivalent Privacy) is a security setting that uses pre-defined passwords. It can be easily exploited and, for this reason, should not be used to protect your home or work wireless network.The most common versions of WEP are:

- WEP-40 also known as WEP-64: It uses 40 bits of key and a 24-bit initialization vector. It can be also expressed with a string of 10 hexadecimal characters (remember 4 bits for 1 hex).
- WEP-104 also known as WEP-128: It uses 104 bits of key and a 24-bit initialization vector. It can be also expressed with a string of 26 hexadecimal characters.

### WPA

WPA (Wi-Fi Protected Access) is, from a security perspective, more secure than WEP, but it can be still exploited by an attacker. For the key encryption protocol, WPA can be configured to use TKIP or CCMP, which is based on AES (CCMP and AES are used interchangeably). CCMP is more secure than TKIP and should be used when possible. However, not all the hardware vendors may support CCMP. For this reason, WPA networks can be configured with the following options:

- WEP CCMP
- WEP CCMP/TKIP (CCMP preferred, TKIP as fallback)
- WEP TKIP

For the authentication part, in WPA can either use:

- A Pre-Shared Key (WPA-PSK) that is convenient for a home or small office environment. In this setup, all the users share the same password so if this is compromised or one of the employees leave, you will have to change the password and update your access point and wireless clients.
- The Extensible Authentication Protocol (EAP), a framework that support different authentication methods. This is a form of enterprise authentication that creates a unique authentication profile for each user. The EAP framework will be discussed in the next paragraph.

### WPA2

Also known as IEEE 802.11i-2004, WPA2 only uses CCMP as the encryption algorithm. As with WPA, the authentication on WPA2 networks can be based on pre-shared keys (WPA2-PSK) or on the EAP framework.

****Do you want to learn how you can monitor your wireless network with NetBeez? Watch the video** :** [Watch End-User Visibility From a Wireless Perspective](https://netbeez.net/resources/watch-end-user-visibility-from-a-wireless-perspective/)

<embed src="https://netbeez.net/wp-content/uploads/2014/01/WiFi-BEEZ-300x232.png%20300w,%20https://netbeez.net/wp-content/uploads/2014/01/WiFi-BEEZ-1024x790.png%201024w,%20https://netbeez.net/wp-content/uploads/2014/01/WiFi-BEEZ.png%201052w" class="wp-image-5539 aligncenter" width="198">

## **WPA supplicant**

The WPA supplicant software (available [here](https://wiki.archlinux.org/index.php/WPA_supplicant)) is not mandatory if you are connecting your wireless client to a home network that uses one of the pre-shared key methods (WEP, WPA-PSK, WPA2-PSK) to authenticate its clients. Since in our case we want to deploy our wireless client in an enterprise network, the WPA supplicant software it is needed. Wireless enterprise networks generally use one of the most common WPA or WPA2 Extensible Authentication Protocol ([EAP](http://en.wikipedia.org/wiki/Extensible_Authentication_Protocol)) methods, such as PEAP, EAP-TLS, EAP-TTLS, and LEAP.

To instruct our wireless Raspberry Pi to use the WPA supplicant software, we have to edit the /etc/network/interfaces and add the following configuration lines under the wlan0 interface:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">auto </span>
  <span class="crayon-e">wlan0</span>
</p>
  <p>
  <span class="crayon-v">allow</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">hotplug </span>
  <span class="crayon-e">wlan0</span>
</p>
  <p>
  <span class="crayon-e">iface </span>
  <span class="crayon-e">wlan0 </span>
  <span class="crayon-e">inet </span>
  <span class="crayon-e">dhcp</span>
</p>
  <p>
  <span class="crayon-v">wpa</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">ssid </span>
  <span class="crayon-v">netbeez</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">enterprise</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">wireless</span>
</p>
  <p>
  <span class="crayon-v">pre</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">up </span>
  <span class="crayon-v">wpa_supplicant</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">B</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">Dwext</span>
  <span class="crayon-o">-</span>
  <span class="crayon-i">i</span>
  <span class="crayon-v">wlan0</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">c</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">etc</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">wpa_supplicant</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">wpa_supplicant</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">conf</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">f</span>
  <span class="crayon-o">/</span>
  <span class="crayon-t">var</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">log</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">wpa_supplicant</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">log</span>
</p>
  <p>
  <span class="crayon-v">post</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">down </span>
  <span class="crayon-v">killall</span>
  <span class="crayon-o">-</span>
  <span class="crayon-i">q</span>
  <span class="crayon-v">wpa_supplicant</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Then we have to edit the /etc/wpa_supplicant/wpa_supplicant.conf file based on your specific configuration:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">ctrl_interface</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">/</span>
  <span class="crayon-t">var</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">run</span>
  <span class="crayon-o">/</span>
  <span class="crayon-e">wpa_supplicant</span>
</p>
  <p>
  <span class="crayon-v">update_config</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">1</span>
</p>
  <p>
  <span class="crayon-v">network</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">ssid</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“”</span>
</p>
  <p><span class="crayon-v">proto</span><span class="crayon-o">=</span>””</p>
  <p><span class="crayon-v">pairwise</span><span class="crayon-o">=</span>””</p>
  <p><span class="crayon-v">group</span><span class="crayon-o">=</span>””</p>
  <p><span class="crayon-v">key_mgmt</span><span class="crayon-o">=</span>””</p>
  <p><span class="crayon-v">eap</span><span class="crayon-o">=</span>””</p>
  <p>
  <span class="crayon-v">identity</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“”</span>
</p>
  <p>
  <span class="crayon-v">password</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“”</span>
</p>
  <p>
  <span class="crayon-v">ca_cert</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“”</span>
</p>
  <p><span class="crayon-v">client_cert</span><span class="crayon-o">=</span>””</p>
  <p><span class="crayon-v">private_key</span><span class="crayon-o">=</span>””</p>
  <p><span class="crayon-v">private_key_password</span><span class="crayon-o">=</span>””</p>
  <p><span class="crayon-v">phase1</span><span class="crayon-o">=</span>””</p>
  <p>
  <span class="crayon-v">phase2</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“”</span>
</p>
  <p><span class="crayon-v">eapol_flags</span><span class="crayon-o">=</span>””</p>
</div>
 | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

In the following table I listed some of the most important configuration options to consider when editing the wpa_supplicant file:

Option   | Defines                                                 | Values
-------- | ------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
proto    | Wireless security settings                              | Default value: WPA RSNWPA = for _WPA only networks_RSN = for _WPA2 only networks_Leave default value for WPA/WPA2 networks.
key_mgmt | Accepted authentication protocols                       | Default value: WPA-PSK WPA-EAPWPA-PSK = for _WPA with pre-shared key_WPA-EAP = for _WPA using EAP methods_IEEE8021X = _IEEE 802.1X using EAP_NONE = _plaintext or static WEP_
pairwise | Accepted pairwise (unicast) ciphers for WPA             | Default value: CCMP TKIPCCMP = for _WPA2 only networks_TKIP = for _WPA only networks_Leave default value for WPA CCMP/TKIP networks
group    | Accepted pairwise (broadcast/multicast) ciphers for WPA | Default value: CCMP TKIP WEP40 WEP140CCMP = for _WPA CCMP or WPA2_TKIP = for _WPA TKIP_WEP104 = _for WEP with 128 bits key_WEP40 = _for WEP with 64 bits key_
auth_alg | Accepted authentication algorithms                      | If not set, automatic detection is usedOPEN = _required for WPA and WPA2_SHARED = _required for WEP_LEAP = _required for LEAP only_
psk      | Pre-shared key                                          | Specify the 256 bit pre-shared key, for WPA-PSK and WPA2-PSK networks
eap      | EAP methods                                             | Define the list of EAP methods accepted. If not set, all the methods are accepted by default. Otherwise, the user can restrict accepted methods, which should be space separated:PEAP = required for PEAPv0/EAP-MSCHAPV2TLS = required for the EAP-TLS with client and server certificateTTLS = required for EAP-TTLSLEAP = required for LEAP

## EAP methods

In wireless enterprise environments, the EAP framework is king. Below are the most common EAP methods along with their wpa_supplicant settings (taken from [here](http://www.lsi.upc.edu/lclsi/Manuales/wireless/files/wpa_supplicant.conf)):

### PEAP (Protected EAP)

There are many variations of the Protected EAP method, but the PEAPv0/EAP-MSCHAPV2 is generally the most common configuration that is used in an enterprise environment. This authentication protocol requires the server-side public key certificate to establish the secure TLS tunnel (PEAPv0) that protects the transmission of the user credentials (MS-CHAPV2).

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">network</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">key_mgmt</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">WPA</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">EAP</span>
</p>
  <p>
  <span class="crayon-v">eap</span>
  <span class="crayon-o">=</span>
  <span class="crayon-e">PEAP</span>
</p>
  <p>
  <span class="crayon-v">identity</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“&lt;em&gt;user@example.com&lt;/em&gt;”</span>
</p>
  <p>
  <span class="crayon-v">password</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“&lt;em&gt;password&lt;/em&gt;”</span>
</p>
  <p>
  <span class="crayon-v">ca_cert</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“/etc/cert/ca.pem”</span>
</p>
  <p>
  <span class="crayon-v">phase2</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“auth=MSCHAPV2”</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### EAP-TLS (Transport Security Layer)

This method is considered one of the most secure and relies on a RADIUS server as the authentication backend. It uses the Public Key Infrastructure (PKI) and requires an x.509 client side certificate to communicate with the authentication server. It is covered in the RFC 5216\. The client certificate, client private key, and CA certificate should be stored in PEM/DER format. If you receive your certificate in PKCS#12 format, you can convert it to the PEM format with the following commands:

# Extract the user certificate and private key from the P12 container

 | <div class="crayon-pre">
  <p><span class="crayon-e">openssl </span><span class="crayon-v">pkcs12</span><span class="crayon-o">-</span><span class="crayon-st">in</span><span class="crayon-v">example</span><span class="crayon-sy">.</span><span class="crayon-v">p12</span><span class="crayon-o">-</span><span class="crayon-e">out </span><span class="crayon-v">user</span><span class="crayon-sy">.</span><span class="crayon-i">pem</span>–<span class="crayon-v">clcerts</span></p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Extract the CA certificate from the P12 container

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">openssl </span>
  <span class="crayon-v">pkcs12</span>
  <span class="crayon-o">-</span>
  <span class="crayon-st">in</span>
  <span class="crayon-v">example</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">p12</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">out </span>
  <span class="crayon-v">ca</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">pem</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">cacerts</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">nokeys</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The wpa_supplicant.conf file will be configured the following way:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">network</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">key_mgmt</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">WPA</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">EAP</span>
</p>
  <p>
  <span class="crayon-v">eap</span>
  <span class="crayon-o">=</span>
  <span class="crayon-e">TLS</span>
</p>
  <p>
  <span class="crayon-v">identity</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“&lt;em&gt;user@example.com&lt;/em&gt;”</span>
</p>
  <p>
  <span class="crayon-v">ca_cert</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“/etc/cert/ca.pem”</span>
</p>
  <p><span class="crayon-v">client_cert</span><span class="crayon-o">=</span>”<span class="crayon-o">/</span><span class="crayon-v">etc</span><span class="crayon-o">/</span><span class="crayon-v">cert</span><span class="crayon-o">/</span><span class="crayon-v">user</span><span class="crayon-sy">.</span><span class="crayon-i">pem</span>”</p>
  <p><span class="crayon-v">private_key</span><span class="crayon-o">=</span>”<span class="crayon-o">/</span><span class="crayon-v">etc</span><span class="crayon-o">/</span><span class="crayon-v">cert</span><span class="crayon-o">/</span><span class="crayon-v">user</span><span class="crayon-sy">.</span><span class="crayon-i">key</span>”</p>
  <p><span class="crayon-v">private_key_password</span><span class="crayon-o">=</span>”<span class="crayon-o">&lt;</span><span class="crayon-v">em</span><span class="crayon-o">&gt;</span><span class="crayon-v">password</span><span class="crayon-o">&lt;</span><span class="crayon-o">/</span><span class="crayon-v">em</span><span class="crayon-o">&gt;</span>”</p>
</div>
 | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### EAP-TTLS (Tunneled Transport Layer Security)

This method is very similar to the PEAPv0/MSCHAPV2 method. A server-side public key certificate is used to authenticate the server and establish a secure tunnel through which the client credentials are transmitted. The client can be authenticated via a CA-signed certificate with the server or just authenticates via traditional username and password credentials.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">network</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">key_mgmt</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">WPA</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">EAP</span>
</p>
  <p>
  <span class="crayon-v">eap</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">TTLS</span>
</p>
  <p>
  <span class="crayon-p"># Phase1 / outer authentication</span>
</p>
  <p>
  <span class="crayon-v">anonymous_identity</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“anonymous@example.com”</span>
</p>
  <p>
  <span class="crayon-v">ca_cert</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“/etc/cert/ca.pem”</span>
</p>
  <p>
  <span class="crayon-p"># Phase 2 / inner authentication via user certificate</span>
</p>
  <p>
  <span class="crayon-v">phase2</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“autheap=TLS”</span>
</p>
  <p>
  <span class="crayon-v">ca_cert2</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“/etc/cert/ca2.pem”</span>
</p>
  <p>
  <span class="crayon-v">client_cert2</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“/etc/cer/user.pem”</span>
</p>
  <p>
  <span class="crayon-v">private_key2</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“/etc/cer/user.prv”</span>
</p>
  <p>
  <span class="crayon-v">private_key2_passwd</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“&lt;em&gt;password&lt;/em&gt;”</span>
</p>
  <p>
  <span class="crayon-v">priority</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">2</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### LEAP (Lightweight Extensible Authentication Protocol)

This method was developed by Cisco before the ratification of the 802.11i standard and is supported by a wide range of operating systems (Windows, Linux, Mac) and wireless vendors. LEAP supports Windows NT/2000 Active Directory profiles and authenticates against a RADIUS server. For this reason, LEAP was largely adopted in wireless enterprise environments and may still be used by several organizations. However, since it relies on the MS-CHAPv2 authentication protocol, it can be exploited, so it is highly recommended to use it with complex passwords or to adopt, if possible, a different EAP method.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">network</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">key_mgmt</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">WPA</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">EAP</span>
</p>
  <p>
  <span class="crayon-v">auth_alg</span>
  <span class="crayon-o">=</span>
  <span class="crayon-e">LEAP</span>
</p>
  <p>
  <span class="crayon-v">eap</span>
  <span class="crayon-o">=</span>
  <span class="crayon-e">LEAP</span>
</p>
  <p>
  <span class="crayon-v">identity</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“&lt;em&gt;user&lt;/em&gt;”</span>
</p>
  <p>
  <span class="crayon-v">password</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“&lt;em&gt;password&lt;/em&gt;”</span>
</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

I hope that this post was beneficial to shed some light on how you can configure your wireless Raspberry Pi, or Linux client running a WPA supplicant, to connect to a wireless enterprise environment.

**Do you want to learn how we do WiFi monitoring at NetBeez? Sign up for a one-to-one demo!**<br>
``

Email Address 

<span class="asterisk">* </span>

Name

Company
