# Share 360 Panosphere Images on Facebook and YouTube

[Original URL](http://climbcodecreate.com/2016/01/27/share-360-panosphere-images-on-facebook-and-youtube/)

> Here's a tutorial that shows you how to share your 360 panosphere images that you've taken with your Android phone onto Facebook or YouTube. Skill Level: Intermediate...

Here's a tutorial that shows you how to share your 360 panosphere images that you've taken with your Android phone onto Facebook or YouTube.

**Skill Level**: Intermediate

**Prerequisites**: Terminal or Command Line

**List of Tools**:

- [Android File Utility](https://www.android.com/filetransfer/)
- [ffmpeg](https://www.ffmpeg.org/)
- [360 Video Metadata Tool](https://github.com/google/spatial-media/releases/download/v2.0/360.Video.Metadata.Tool.mac.zip)

**Steps:**

1. Transfer your Panosphere image to your desktop. Ideally you'd want to use [Android File Utility](https://www.android.com/filetransfer/)
2. Install [ffmpeg](https://www.ffmpeg.org/) utility on your machine
3. Install [360 Video Metadata Tool](https://github.com/google/spatial-media/releases/download/v2.0/360.Video.Metadata.Tool.mac.zip)
4. Use the following script to convert your pano image to an mp4.

  - `ffmpeg -loop 1 -i source.jpg -c:v libx264 -r 1/60 -t 60 -vf scale=4096:-1 destination.mp4`
  - Replace source.jpg with your source file.
  - Replace destination.mp4 with your desired output filename.
  - 4096×2048 is the recommended resolution for 360 video upload to Facebook. The framerate is set to 1/60fps. The duration is set to 60seconds.

5. Open the video file with the 360 metadata tool, select the "spherical" input box, then select "Save as" and name your new video destination_injected.mp4

6. Go to your Facebook timeline and post your new video. It will take Facebook some time to process your video, but you will be notified once your video complete.
7. Enjoy your new panosphere on either desktop or mobile device.

**Final Output**:

**Link if the video above doesn't play**:<br>
<https://www.facebook.com/climbcodecreate/videos/222423284764139><br>
**References**:

- <https://support.google.com/youtube/answer/6178631?hl=en>
- <https://trac.ffmpeg.org/wiki/Create%20a%20video%20slideshow%20from%20images>
