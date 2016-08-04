# stamparm/maltrail

[Original URL](https://github.com/stamparm/maltrail)

> General Maltrail is a malicious traffic monitoring system, utilizing publicly available blacklists containing malicious (or generally suspicious) trails (i.e. domain names, URLs and/or IPs), along...

## [](https://github.com/stamparm/maltrail#general)General

**Maltrail** is a malicious traffic monitoring system, utilizing publicly available blacklists containing malicious (or generally suspicious) trails (i.e. domain names, URLs and/or IPs), along with static lists compiled from various AV reports.

[![Reporting tool](https://camo.githubusercontent.com/c74fabdb8aa3f38bd8b4462294d8559372485008/687474703a2f2f692e696d6775722e636f6d2f47485159514c652e706e67)](https://camo.githubusercontent.com/c74fabdb8aa3f38bd8b4462294d8559372485008/687474703a2f2f692e696d6775722e636f6d2f47485159514c652e706e67)

The following blacklists (i.e. feeds) are being utilized:

As of static entries, the trails for the following malicious entries have been manually included (from various AV reports):

```
alureon, android_stealer, aridviper, axpergle, balamid, bankpatch, bedep, bubnix,
carbanak, careto, chewbacca, cleaver, computrace, conficker, cosmicduke,
couponarific, crilock, cryptolocker, cryptowall, ctblocker, cutwail, darkhotel,
defru, desertfalcon, destory, dorifel, dorkbot, drixed, duqu, dursg, dynamic_domain,
dyreza, emotet, equation, expiro, fakeran, fbi_ransomware, fiexp, fignotok, fin4,
finfisher, gamarue, gauss, geodo, gholee, htran, iframeref, jenxcus, kegotip,
kovter, lollipop, luckycat, mariposa, miniduke, nettraveler, neurevt, nitol,
no-ip_malware, nonbolqu, nuqel, nymaim, palevo, pdfjsc, pift, powelike, proslikefan,
pushdo, ransirac, redoctober, regin, reveton, rustock, sality, sathurbot, scieron,
sefnit, shylock, siesta, simda, sinkhole_kaspersky, sinkhole_shadowserver, sirefef,
smsfakesky, snake, snifula, sofacy, stuxnet, superfish, suspicious_domain,
suspicious_ipinfo, teerac, torpig, torrentlocker, tugspay, unruy, upatre, vawtrak,
virut, vobfus, vundo, wiper, zeroaccess, zlob, etc.
```

## [](https://github.com/stamparm/maltrail#introduction)Introduction

Maltrail is based on the sensor / server / client architecture. **Sensor**(s) is a standalone component running on the monitoring node (i.e. workstation connected to the SPAN port) where it "sniffs" the passing traffic for blacklisted trails (i.e. domain names, URLs and/or IPs). In case of a positive match, it sends the log event to the (central) server where it is stored inside the appropriate logging directory. If sensor is being run on same machine as server, logs are stored directly into the logging directory, while if it is being run remotelly, it is being sent via UDP to the server.

**Server**'s primary role is to provide backend support for the reporting web application. In most cases, server and sensor will be run on the same machine. So, to prevent potential disruptions, the front-end reporting part is (chosen to be) based on the fat-client architecture. Events (i.e. log entries) for the chosen date are streamed toward the **client**, where the reporting web application is responsible for the presentation part. Data is sent toward the client in compressed chunks, where they are processed sequentially. The final report is created in highly condensed form, practically allowing presentation of unconstrained number of events.

## [](https://github.com/stamparm/maltrail#users-manual)User's manual

**TODO documentation**

## [](https://github.com/stamparm/maltrail#requirements)Requirements

Installed [Python](http://www.python.org/download/) **2.6.x** or **2.7.x** is required, together with [Pcapy](http://corelabs.coresecurity.com/index.php?module=Wiki&action=view&type=tool&name=Pcapy).

## [](https://github.com/stamparm/maltrail#license)License

Maltrail is available under the [MIT license](https://github.com/stamparm/maltrail/blob/master/LICENSE).
