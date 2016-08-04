# Reverse engineering Google Nest Devices

[Original URL](http://experimental-platform.tumblr.com/post/137835649425/reverse-engineering-google-nest-devices)

> Research effort for our Experimental Platform projectIn the buzzword-powered world of IoT there doesn't really seem to be as much innovation as some would have you think. The hundredth...

[![image](http://41.media.tumblr.com/2fe2bc2f546697dd4e21e3423f1f600c/tumblr_inline_o1dnmu7Dpx1tzfota_540.png)](http://t.umblr.com/redirect?z=https%3A%2F%2Fexperimental-platform.github.io%2F&t=MmQyMmUzMjY2ZWQ1MDVjOTBlNzBkOTFiZjgxMzc2NjI4YzUyOWZhMyxRRzRSUGZtMQ%3D%3D)

## Research effort for our [Experimental Platform project](http://t.umblr.com/redirect?z=https%3A%2F%2Fexperimental-platform.github.io%2F&t=MmQyMmUzMjY2ZWQ1MDVjOTBlNzBkOTFiZjgxMzc2NjI4YzUyOWZhMyxRRzRSUGZtMQ%3D%3D)

In the buzzword-powered world of IoT there doesn't really seem to be as much innovation as some would have you think. The hundredth "smart" camera, door lock or lamp don't really disrupt much, yet some players in this space seem to be getting far ahead of the others. A notable player in this space is Nest Labs who after their acquisition by Google rose to be one of the most prominent brand names in this space, now a subsidy of Alphabet. Here at [Protonet](http://t.umblr.com/redirect?z=https%3A%2F%2Fprotonet.info&t=ZDI1YTg2ZDNmNzg0YjY2YjJmOTM4NjBiZGY4ZTY2MWQxM2VlZjQ0OSxRRzRSUGZtMQ%3D%3D) we consider owning and having access to your own data as one of our core values - thus as much as we admire Alphabet's tech, we're sceptical about it's cloud only approach to smart home products. It's not much of a surprise then that we picked Nest products for our research efforts when looking into integration of different kinds of smart devices.

![image](http://40.media.tumblr.com/c5002db3cee1306b19592486574691e2/tumblr_inline_o1dbkaIyTi1tzfota_250.jpg) _Google Nest Cam_

## Access only via the cloud

A notable property of Nests offering is that they are exclusively web-operated products. The only way to access their cameras or thermostats data over network is to go through Nests cloud. Neither the Nest Cam nor the Nest Thermostat which I received to play with seem to have any services running on any network ports. This has two consequences:

- you [cannot operate](http://t.umblr.com/redirect?z=http%3A%2F%2Fmotherboard.vice.com%2Fread%2Fthe-internet-of-things-isnt-ready-for-babies&t=MDcxNjI3YzYwMzJmZjM2NDEyMDdhODJhNmJmMmExY2Y4ZmNmNjdiZSxRRzRSUGZtMQ%3D%3D) the camera or switch your thermostat's settings without Internet connection
- all data from the devices is transmitted to Alphabet, constantly, unless the devices are disconnected

The device controls are accessed either by a web browser app or by a mobile app. I chose to look into the latter's transmissions in order to figure out how we can access our own private data. I used [Packet Capture](http://t.umblr.com/redirect?z=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dapp.greyshirts.sslcapture&t=YzNiNTAyMDA4ZGFlZTkxMWNmMTc1NDJiOWJiN2QzZWE5OTY0MjM1NCxRRzRSUGZtMQ%3D%3D) which in addition to doing the obvious, sets up a VPN and a phony SSL certificate to eavesdrop on all HTTPS traffic.

![image](http://41.media.tumblr.com/380e4e7021cf0dd3d9bf9f99bf683fc1/tumblr_inline_o1dib6zDbf1tzfota_400.jpg) _Google Nest Thermostat_

## Reverse engineering procedure

Logging in seems pretty straightforward - the app sends a JSON object with email and plaintext password to its server and receives a session cookie.

```
POST /session HTTP/1.1
Content-Type: application/json;charset=UTF-8
User-Agent: Nest/[redacted] (Android; Obsidian) [redacted]
Host: home.nest.com
/.../
{"password":"foo", "email":"bar"}
```

Then the application uses the cookie to create a session with dropcam servers since apparently Dropcam's separate infrastructure has been preserved. The API then provides several endpoints, e.g. for fetching the list of cameras with their numerous data fields like this:

```
{
 "talkback_stream_host": "stream-delta.dropcam.com:443",
 "is_streaming_enabled": true,
 "last_connected_time": redacted,
 "direct_nexustalk_host": "redacted",
 "timezone": "redacted",
 "id": redacted,
 "live_stream_host": "redacted",
 "description": "",...
```

_see all the code when klicking on the link below_

```
Nest Cam
```

By another endpoint we can also retrieve a list of "cues" within a certain time period, i.e. points of time where motion detection has been triggered, and yet another allows to capture a current picture as jpeg. Example code in Go to do these things follows at the end of this post.

## Nest thermostat sends a vast array of data back "home"

By far the greatest hope of integration with other IoT devices and home-made automation logic we've put into the thermostat. Indeed, after descending through a hierarchy of objects including user, building and device, one receives a whole array of different data about the thermostat...but the current temperature is not among them.

```
{
 "alt_heat_delivery": "forced-air",
 "alt_heat_source": "gas",
 "alt_heat_x2_delivery": "forced-air",
 "alt_heat_x2_source": "gas",
 "auto_away_enable": true,
 "auto_away_reset": false,
 "auto_dehum_enabled": false,
 "auto_dehum_state": false,
 "aux_heat_delivery": "forced-air",
 "aux_heat_source": "electric",...
```

_see all the code when klicking on the link below_

```
Nest Thermostat Object
```

So the question remains, is the temperature value not sent to Nest at all or is it sent but unreachable by the API?

## Go Code for accessing Nest Thermostat

Here's a complete code sample for logging in, downloading cues, making a screenshot and dumping thermostat info:

```
package main

import (
    "bytes"
    "encoding/json"
    "errors"
    "fmt"
    flags "github.com/jessevdk/go-flags"
    "io"
    "io/ioutil"
    "net/http"
    "os"...
```

_see all the code when klicking on the link below_

```
Nest Go Code
```

Nest devices are excellent pieces of hardware engineering, yet creating a walled garden around the user's own data is a shady move. All of my private data should be easibly accessible to me though open API without any gimmicks. In its [press release](http://t.umblr.com/redirect?z=https%3A%2F%2Fnest.com%2Fpress%2Fnest-introduces-nest-weave-creating-most-comprehensive-developer-platform-for-the-home%2F&t=MjhkYzY5YWJkYTllYjBhMWJiODk2YjgyYzgyMmU2OTA5NzBhMDBjNCxRRzRSUGZtMQ%3D%3D) Nest promised introducing a public API and its [reference docs](http://t.umblr.com/redirect?z=https%3A%2F%2Fdeveloper.nest.com%2Fdocumentation%2Fcloud%2Fapis&t=YmY3Yzk2NDVmODNlNmRhOWVjZTc1OTI2NzMwZmFmOTUxZmUwYTZiMCxRRzRSUGZtMQ%3D%3D) are already available. This API however seems limited in many ways compared to the internal API used by Nest mobile app - and to add insult to injury - many of its features require an active Nest subscription. Time will show if Nest shall repent and make its products more open - in the meantime at Protonet's Hamburg office we continue to explore what's possible on the intersection of open, malleable Internet technology and hardware devices.

Looking to root your Nest devices? Here are two useful links:

[Reversing the Dropcam Part 2: Rooting your Dropcam](http://t.umblr.com/redirect?z=http%3A%2F%2Fblog.includesecurity.com%2F2014%2F04%2Freverse-engineering-dropcam-rooting-the-device.html&t=OWRkMmJmMjdjZGQ0YjI0NDk3OTAyYzVlMmI4MTQ0NWFkYmVhODkxNyxRRzRSUGZtMQ%3D%3D)<br>
[](http://t.umblr.com/redirect?z=https%3A%2F%2Fblog.exploitee.rs%2F2014%2Fgoogle-nest-exploiting-dfu-for-root%2F&t=ZGE0MzZiNTNkM2FiNzU5Nzc4MDYwNDgxMDA1YmY1YjYxNGRjNWE4NyxRRzRSUGZtMQ%3D%3D)[Google Nest: Exploiting DFU For Root](http://t.umblr.com/redirect?z=https%3A%2F%2Fblog.exploitee.rs%2F2014%2Fgoogle-nest-exploiting-dfu-for-root%2F&t=ZGE0MzZiNTNkM2FiNzU5Nzc4MDYwNDgxMDA1YmY1YjYxNGRjNWE4NyxRRzRSUGZtMQ%3D%3D)

Written by [Kamil Domański](http://t.umblr.com/redirect?z=https%3A%2F%2Fgithub.com%2Fkdomanski&t=MmEwMTkxMWQxMGRlNjNmNGU2MDdiZDk2ZGFhODJlMDg1N2JhYjE4MSxRRzRSUGZtMQ%3D%3D)
