# HTTP Live Streaming In Javascript

[Original URL](https://blog.peer5.com/http-live-streaming-in-javascript/)

> A flashocalypse is approaching - paraphrasing John Luther, JW Player (TL;DR) We can finally play HLS using only javascript. It works faster than Flash, unlocking the potential to stream live in 4k...

> [A flashocalypse is approaching](http://www.streaminglearningcenter.com/blogs/dash-the-most-popular-format-(almost)-no-one-is-using-.html) - paraphrasing John Luther, [JW Player](https://blog.peer5.com/http-live-streaming-in-javascript/jwplayer.com)

(TL;DR)<br>
We can finally play HLS using only javascript. It works faster than [Flash](http://www.adobe.com/products/flashplayer.html), unlocking the potential to stream live in 4k and 60fps.

## What is HLS?

HLS, which stands for HTTP Live Streaming, is a video format invented by Apple for live streaming. It's file based, meaning you can serve it over an HTTP connection on the web.

In its simplest form, you have a manifest file, a.k.a playlist, that is the 'main' file for a stream. This file contains metadata about the stream and a list of URIs for the video segments of the stream. When a player requests this playlist, a server responds with an updated manifest file that specifies segments that have recently been added to the stream.<br>
![HLS Manifest - HLS Live Streaming With Peer5](https://blog.peer5.com/content/images/2015/11/hls-manifest.jpg) This file format has many advantages over alternatives like RTP or RTMP in terms of device support, ease of deployment, and scalability. As a result, it has become a mainstream technology for live streaming, and is widely used for VoD as well.

## HLS On All Platforms

If you're familiar with HLS you might be thinking, "isn't HLS already supported by HTML?" The answer: sort of! When Apple invented HLS, they also implemented a 'player' mechanism inside of their browser. This meant that in Safari you could add a line of HTML to the page, for example, `<video src=’path/to/playlist.m3u8’></video>`, and voila, you'd have a working HLS player!

But, for various reasons, other browsers didn't implement this, meaning that systems needed to be designed to stream differently depending on which device and browser a user was operating on. A stream might use HLS on Safari or iOS and RTMP or HDS on desktop. Eventually Chrome and Firefox implemented an HLS player for their mobile versions, but Chrome, Firefox, and IE still don't use this technology on their desktop versions. A technology for desktop browsers was needed to stream HLS on all devices. Eventually, a few great libraries were released to solve this problem. [Flashls](http://www.flashls.org/), which is open source, and [osmfhls](http://osmfhls.kutu.ru/), which is proprietary, are two Flash plugin libraries for players that enable HLS on desktop.

## Playing HLS Without Flash

HLS experienced mainstream adoption, but Flash was still a requirement. Flash is heavy, slow, and [full of security flaws](http://www.computerworld.com/article/2971721/security/stop-the-flash-madness-5-bugs-a-week.html). Experts have been predicting Flash's demise for a while [now](http://www.wsj.com/articles/tech-world-prepares-obituary-for-adobe-flash-1437341315), and an alternative is needed. To add some context, we'll have to take a quick detour to another video format, or rather a standard, MPEG-DASH. Dash basically takes all of the good stuff from HLS and gets rid of the bad pieces. Like HLS, it's also file based, however, in order to support this new format in the browser [W3C](https://blog.peer5.com/http-live-streaming-in-javascript/www.w3.org) decided to create new HTML5 API's called Media Source Extensions (MSE) and Encrypted Media Extensions (EME). MSE give developers an API to control the video tag's video buffer and inject data into it. EME is an API that provides DRM support. As a result, it's become possible to inject data into the browser's video player and use it to implement HLS.

Technically, this requires HLS playback logic that requests HLS files from a server, transmuxes them into the correct mp4 format in javascript, and injects them into the video tag using MSE. There are a few FOSS libraries that do just that, including: [hlsjs](https://github.com/dailymotion/hls.js), videojs's [videojs-contrib-hls](https://github.com/videojs/videojs-contrib-hls), and [hasplayer](https://github.com/Orange-OpenSource/hasplayer.js/tree/master). Now we have HLS in the desktop browser, with only javascript, no Flash necessary. It's supported in Chrome, Firefox 42+, IE11+, Edge, and Opera.

## Performance

It's time for comparisons, performance, and analysis. In this section, I will show the results for tests run on videojs5 (with videojs-contrib-hls v1.0), its flash predecessor videojs4, hls.js, and its flash predecessor flashls. I'll focus on CPU performance testing.

For each player I used Chrome's Timeline pane to track the browser's fps, Profile pane to record how much CPU time was utilized, and task manager to monitor the overall CPU usage.

### Hlsjs

![HLSJS Timeline - HLS Video Streaming Test](https://blog.peer5.com/content/images/2015/11/hlsjs-timeline.jpg) The main thread held 60fps, except for the timestamps around 4,000ms and 14,000ms (each 10 seconds) where it dropped to 30fps. This can probably be traced to the handling of the segment.

![HLSJS Profile - HLS Video Streaming Test](https://blog.peer5.com/content/images/2015/11/hlsjs-profile.jpg) I also ran CPU profiling for 20,098 ms. The total CPU time while profiling was 729.6ms (3.6% of the experiment length), 74% of which was Chrome's program thread.

Examining the Chrome task manager, CPU usage was mostly 0-2%, with 4% peaks.

### Videojs5

![VideoJS5 Timeline - HLS Video Streaming Test](https://blog.peer5.com/content/images/2015/11/videojs5-timeline.jpg) The main thread was a constant 60fps.

![VideoJS5 Profile - HLS Video Streaming Test](https://blog.peer5.com/content/images/2015/11/videojs5-profile.jpg) A CPU profiling test of 20,143ms showed that the total CPU run time was 794.83ms (3.9%), of which 77.8% was Chrome's program thread.

Examining the Chrome task manager, CPU usage was mostly 0-1%, with 5% peaks.

### Videojs4

![VideoJS4 Timeline - HLS Video Streaming Test](https://blog.peer5.com/content/images/2015/11/videojs4-timeline.jpg) Here we saw a big impact on fps, which decreased to 6 frames per second. This stalled the main thread for up to 160ms at a time.

![VideoJS4 Profile - HLS Video Streaming Test](https://blog.peer5.com/content/images/2015/11/videojs4-profile.jpg) CPU profiling for 20,084ms showed that the total CPU run time was 5,385.9ms (26.8%) and only 27.8% was Chrome's program thread.

Examining the Chrome task manager, CPU usage was 16% on average, with peaks of 24% of CPU (divided between flash - 20% and page - 4%)

### Chromeless Flashls

![Flashls Timeline - HLS Video Streaming Test](https://blog.peer5.com/content/images/2015/11/flashls-timeline.jpg) The main thread held 60 fps, and dropped to 30 fps every 10 seconds.

![Flashls Profile - HLS Video Streaming Test](https://blog.peer5.com/content/images/2015/11/flashls-profile.jpg) CPU profiling of 20,092ms showed that CPU was active for 1,311.6ms (6.5%), 92.8% of which was Chrome's program thread.

Examining the Chrome task manager, CPU usage was mostly 3-5%, with peaks of 8% (3% flash, 3% flash gpu, 3% page)

### Conclusion

Both videojs5 and hlsjs performed very well. They easily played 30fps HD videos without setting back the main thread. Videojs5 showed superb performance with 60fps videos as well and didn't drop any frames. Hlsjs dropped frames when processing new segments.

Ranking the libraries cpu performance, I would suggest:

1. videojs5
2. hlsjs
3. flashls,
4. videojs4

The comparison isn't shown here, but both javascript players started faster than their Flash counterparts did due to Flash's asynchronous load requirements for the .swf file and the crossdomain file.

It's important to mention that the libraries used are still very young and videojs still hasn't released javascript HLS support. We will likely see improvements in all of the libraries. These innovations, as well as others, will likely drive higher quality video and create better viewing experiences on the web.

Peer5 is a peer-to-peer CDN that improves content delivery for live and on-demand video streams. Want to try Flashless HLS? Sign up for your free trial [here](https://app.peer5.com/register).<br>
After registering, select JW7 and check the "Play HLS in Javascript" checkbox.

### Notes

1. Further tests need to be done with 4k videos.
2. All tests were done on _Intel Corei7-4810MQ machine_.<br>
  The stream tested is [60fps-HD video](http://www.streambox.fr/playlists/x36xhzz/url_9/193039199_mp4_h264_aac_fhd_7.m3u8):<br>
  _Average segment duration: 9.92 seconds<br>
  Segment bitrate: Average: 6.31 Mbits/sec, Max: 10.83 Mbits/sec<br>
  Average segment structural overhead: 193.93 kbits/sec (3.07 %)<br>
  Track ID: 1<br>
  Audio Codec: AAC-LC<br>
  Audio sample rate: 44100 Hz<br>
  ACL: Stereo (L R)<br>
  Track ID: 2<br>
  Video Codec: avc1<br>
  H.264 profile: High<br>
  H.264 level: 4.0<br>
  Video resolution: 1920x1080 pixels<br>
  Video average IDR interval: 3.706 seconds, Standard deviation: 2.775<br>
  Video frame rate: 60.000 fps<br>
  Average video bitrate: 5.96 Mbits/sec<br>
  Average audio bitrate: 151.79 kbits/sec_
