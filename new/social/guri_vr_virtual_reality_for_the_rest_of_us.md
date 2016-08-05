# Guri VR: Virtual Reality for the Rest of Us

[Original URL](https://source.opennews.org/en-US/articles/virtual-reality-rest-us/)

> (Topher McCulloch, licensed under CC) Tool reality guri vr webvr JavaScript virtual

![Guri VR: Virtual Reality for the Rest of Us](https://source.opennews.org/media/cache/cc/6d/cc6dbcc0323d89cd38ba24172d69264e.jpg) ([Topher McCulloch](https://www.flickr.com/photos/tofu_mugwump/), licensed under 

[<span class="caps">CC</span>](https://creativecommons.org/licenses/by-sa/2.0/)

)

## <span class="category"><a href="https://source.opennews.org/en-US/category/tool/">Tool</a> <span class="tags"><a href="https://source.opennews.org/en-US/articles/tags/reality/">reality</a> <a href="https://source.opennews.org/en-US/articles/tags/guri-vr/">guri vr</a> <a href="https://source.opennews.org/en-US/articles/tags/webvr/">webvr</a> <a href="https://source.opennews.org/en-US/articles/tags/javascript/">JavaScript</a> <a href="https://source.opennews.org/en-US/articles/tags/virtual/">virtual</a></span></span>

 Guri 

<span class="caps">VR</span>

: Virtual Reality for the Rest of Us

When Gustavo Cerati, a legendary Argentinian musician and songwriter, was [asked to share his best advice](https://youtu.be/TSHtBUKgBds?t=7m10s) for new musicians, he refused--saying instead that "experiences are not transferable." You may agree or may not with his statement, but if you've ever worn an Oculus Rift or a similar virtual reality (

<span class="caps">VR</span>

) headset, you'll know we are getting closer and closer to transferable experiences.

Journalism seems to be a good use case for 

<span class="caps">VR</span>

 technology. The New York Times has delivered a wide range of stories through their [nytvr app](http://www.nytimes.com/marketing/nytvr/), available on Android and iOS; the Washington Post [took us to Mars](https://www.washingtonpost.com/graphics/business/mars-journey/); and The Guardian showed us what it's like [to survive solitary confinement](https://www.theguardian.com/world/2016/apr/27/6x9-could-you-survive-solitary-confinement-vr).

On the other hand, companies like [Google](https://vr.google.com/), [Facebook](https://www.facebook.com/oculusvr/), and [Microsoft](https://www.microsoft.com/microsoft-hololens/en-us) are pushing the limits of the technology in partnership with game makers, artists and developers. We are still experimenting and trying to understand what the future of Virtual Reality will look like in the near future.

With the rise of 

<span class="caps">VR</span>

, Mozilla, Google, and other companies expressed interest in developing open standards for making the web the home of the 

<span class="caps">VR</span>

 ecosystem, which led to [WebVR](https://webvr.info/), a JavaScript 

<span class="caps">API</span>

 that provides access to Virtual Reality devices. The [specification draft](https://w3c.github.io/webvr/) is available, and we can even use what is called a [polyfill](https://github.com/borismus/webvr-polyfill) to use WebVR today, before this 

<span class="caps">API</span>

 is added in your mobile browser.

With WebVR, a web developer can access a wide range of device-specific information that's needed to create 

<span class="caps">VR</span>

 apps: position, orientation, velocity, acceleration, field of view, and eye distance.

I got really interested in the development of WebVR technology and the impact it could have in journalism. This led me to explore a variety of tools for making 

<span class="caps">VR</span>

 easier to develop, both for journalists and for developers without graphics programming knowledge.

Here are three tools for making 

<span class="caps">VR</span>

 in the open web, in descending order of required technical skills. But even if the first are harder to use for non-technical people, I'll bet that if you can write just couple of lines of JavaScript, you'll be able to create amazing 

<span class="caps">VR</span>

 scenes.

### A Note on Asset Generation and Usage

The first question I get in every workshop is, "how can I take my own pictures and videos?" And it makes a lot of sense. If we want to create our own experiences, we need to be able to capture our own world.

#### Taking 360° Panoramas

![360-degree panorama](https://source.opennews.org/media/img/uploads/article_images/071916gurivrpanorama.jpg) ([NikonFDSLR](https://www.flickr.com/photos/nikonfdslr/27569806332/in/photolist-J1fsUu-HKH6JJ-HM1fPg-GHp3PQ-GED8m4-HrTb5z-H1Ue2b-Ha3Ve4-GJR5F3-GevH2Y-GAtHjL-GzWCEN-G4XAUe-FPReBF-Gspv4v-FAYb5f-GtVEve-Go8mZA-Go8jqh-GMyNBW-GD2rkM-FPjbQG-FFiCzg-Gwm8aM-GoVUoF-FuXQgo-FuXn6b-Fv8WLr-FuDtxW-FYESMY-GhDSGC-Fm9HMU-G9QvSn-Fgjryw-Fgjrtm-FDB5F9-F71AZp-FUvg7x-FkW7Th-EyG4qe-Ff5hgq-ERzRSG-Fdzg4y-GbGJH6-FUDZWp-FSL9oe-FYBP6k-FYBJAD-FWjNGG-F4my3a))

The preferred format for this kind of 360° panoramas is called "equirectangular," and the good news is, you can take them with your phone.

The Android Camera has a mode called [Photo Sphere](https://support.google.com/googlecamera/?hl=en#2839084) and an [active community](https://plus.google.com/u/0/communities/115970110085205516914) around the functionality. The [Cardboard Camera](https://play.google.com/store/apps/details?id=com.google.vr.cyclops&hl=en) is also a good option, enabling the ability to record audio along with your panoramas.

On iOS, you can install the [Google Street View app](https://itunes.apple.com/app/id904418768?mt=8240) to take this kind of picture--there are more apps that will help you, but this one works really well.

If you don't want your own pictures, you can always download one from the [Flickr Equirectangular group](https://www.flickr.com/groups/equirectangular/). You can search between more than 15000 equirectangular pictures (remember to add attributions to the photographers and be careful with the licenses).

#### Taking 360° Videos

This gets tricky because usually you will need a special camera. We are in a stage where consumer cameras are not perfect yet, but I can point to a couple of cameras that do the job:

- The [Ricoh Theta S](https://theta360.com/es/about/theta/s.html) and the [Samsung Gear 360](http://www.samsung.com/global/galaxy/gear-360/) are tiny 360° cameras that can make your life easier, and both come with their own apps and software for preview, editing and uploading videos. The battery duration and heat are real issues, but you can get pretty decent videos.
- For higher quality, 

  <span class="caps">VR</span>

   makers sometimes use a rig with [GoPros](https://gopro.com/spherical). There are a lot of different options and more cameras and techniques are coming out.

#### Recording Audio for 

<span class="caps">VR</span>

Recording audio is easier, and you can use your regular audio recorder to do this. If you want to record in 360°, there are special mics. You can take a look at different options [here](http://elevr.com/audio-for-vr-film/).

#### Uploading Your Assets

Equirectangular pictures and videos are regular media files. Your panorama file can be opened and edited with your favorite photography app, meaning you can upload your assets to the same services you use to upload your images and videos.

The only catch is that if you host your pictures in a domain that is not the same as your 

<span class="caps">VR</span>

 app (your website), the service needs to support 

[<span class="caps">CORS</span>](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS)

. If you don't know what 

<span class="caps">CORS</span>

 is, don't worry, you can upload your images to services like [Imgur](https://imgur.com/) and also your videos to a public Dropbox folder, and it will work. For uploading to 

<span class="caps">AWS</span>

 S3, make sure you have a 

[<span class="caps">CORS</span>

 policy](http://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html) set up.

### WebVR Starter Kit

I was happy to discover that, thanks to the work of amazing developers, you can actually create simple 

<span class="caps">VR</span>

 scenes that work on the Oculus or Google Cardboard with just one or two lines of JavaScript or 

<span class="caps">HTML</span>

.

The first project I want to highlight is the [WebVR Starter Kit](https://github.com/povdocs/webvr-starter-kit) by Brian Chirls. This library allows developers (or people with very little JavaScript experience) to create 

<span class="caps">VR</span>

 scenes in seconds. The [examples](https://github.com/povdocs/webvr-starter-kit#examples) really show the power of the library.

The main idea is that by adding this library to your website (as a .js script) your website is automagically converted to a 3D 

<span class="caps">VR</span>

 scene. The only thing you need to do is just add objects (like boxes, spheres, audios, panoramas, videos) with attributes like position, color, and material. You can even add simple animation.

You can, for example, play God and indicate that you want a wooden floor and a sky with two lines:

```
VR.floor().setMaterial(‘wood’);

VR.sky(); 
```

Then you can add a green cylinder, position it, and tell everybody it's yours:

```
VR.box({ color: 'green' }).moveTo(0, 2, 0)

VR.text('This is my cylinder').moveTo(0, 1, 0) 
```

Create a nice atmosphere for [this little bunny video](http://jsbin.com/pawepezasa/embed?js,output) in just three lines of code.

### A-Frame with 

<span class="caps">HTML</span>

After some time playing around with the amazing WebVR starter kit, I found out that a Mozilla team working on WebVR experiments, known as [MozVR](http://mozvr.com/), was creating a framework that can be used by people who can't write a single line of JavaScript.

It's called A-Frame. The idea is to create 

<span class="caps">VR</span>

 scenes using 

<span class="caps">HTML</span>

 tags and properties, making the 

<span class="caps">VR</span>

 development process pretty much like building a website. [The A-Frame project website](https://aframe.io/) is full of [examples](https://aframe.io/examples/showcase/shopping/), the [documentation](https://aframe.io/docs/guide/) is really good, and the [community](https://aframe.io/community/) is active and growing. Inside every example on their website, there is a link to the source code.

This time, instead of using JavaScript to create our objects we will use 

<span class="caps">HTML</span>

-like tags. Most of the WebVR starter kit objects are available, including handy resources like 3D model loaders and arrow controls for moving inside the scene.

Let's say we want to get a videosphere surrounding the scene and a box inside. We would express it via the `a-box` and `a-videosphere` tags:

```
<a-scene>

 <a-assets> <video id="antarctica" autoplay loop="true" src="antarctica.mp4"> </a-assets>

 <a-videosphere src="#antarctica"></a-videosphere> <a-box color="red" depth="2" height="4" width="0.5"></a-box>

</a-scene> 
```

[As you can see](http://jsbin.com/pafujah/1/embed?html,output), it's really easy to create--for example--a spherical video just with some lines of 

<span class="caps">HTML</span>

.

Also, if you're still not sure about the power of this tool, check out [Mars: an interactive journey](https://www.washingtonpost.com/graphics/business/mars-journey/)--a Washington Post 

<span class="caps">VR</span>

 experience made with A-Frame.

### Guri 

<span class="caps">VR</span>

, for Journalists and Non-Developers

After playing around with all these amazing tools available to create 

<span class="caps">VR</span>

 experiences, two questions popped into my mind:

- Is there a way to add context to a story told with 

  <span class="caps">VR</span>

  ?

- Is the A-Frame learning curve reachable for storytellers and journalists, or should I create something simpler for them?

Based on those questions, and after some experimentation, I created [Guri 

<span class="caps">VR</span>](https://gurivr.com/)

. Guri is a set of tools focused on the creation of 

<span class="caps">VR</span>

 experiences based on intuitive descriptions, and it's targeted at journalists and other non-developers. The main tool is the Guri editor. This online tool allows the users to express in plain English what they want to experience and generate a shareable and embeddable link with the 

<span class="caps">VR</span>

 scene.

The output is a 

<span class="caps">HTML</span>

 file using autogenerated A-Frame markup. This is helpful if you want to create a prototype and then pass the code to a developer to change it.

You can play around with the editor at [GuriVR.com](https://gurivr.com/), but let's see how you can describe a basic scene. For example I can write down this into the editor:

> _My first scene lasts 5 seconds and has a skyblue background and text saying "This is my first scene"._

> _The second is 30 seconds and has just a panorama located at <https://ucarecdn.com/8e6da182-c794–4692–861d-d43da2fd5507/> along with the audio <https://ucarecdn.com/49f6a82b–30fc–4ab9–80b5–85f286d67830/>_

And [this is the result.](https://s3.amazonaws.com/gurivr/s/a5ed8c57de2f728042179af3.html)

Since one of the goals is to remove the friction between the users and the 

<span class="caps">VR</span>

 generation, the editor includes a file uploader. To upload a file, you just need to drag it to the editor and select where to place it based on the cursor position.

![Screenshot](https://source.opennews.org/media/img/uploads/article_images/071916gurivrdemo.gif)

Since my goal was to present a friendly interface for 

<span class="caps">VR</span>

-scene generation, I also wanted to interact with existing tools. For example, I started working on an A-Frame Chartbuilder [component](https://impronunciable.github.io/aframe-chartbuilder-component/basic/). You can feed the component with the 

<span class="caps">JSON</span>

 output from [ChartBuilder](https://quartz.github.io/Chartbuilder/), and it will draw a 3D representation of the chart you just made. This works on any A-Frame scene, but I also added it to the Guri Editor. There is also a [guide](https://gurivr.com/guide) to help you getting started with the tool.

![Screenshot](https://source.opennews.org/media/img/uploads/article_images/071916gurivrchartbuilder.gif)

### <span class="caps">VR</span>

 Tweetbot

In the end, Guri is an 

<span class="caps">API</span>

 that accepts a 

<span class="caps">JSON</span>

 file describing what we want and translating it into A-Frame so that it can be easily modified after.

Thinking about even easier ways to create a 

<span class="caps">VR</span>

 scene, I developed a proof of concept using Twitter. Using GuriVR and the Twitter 

<span class="caps">API</span>

, I use [@guri_vr](https://twitter.com/guri_vr) as a 

<span class="caps">VR</span>

 bot. You can tweet an equirectangular picture, and if you mention @guri_vr on that tweet, it will tweet you back with the 

<span class="caps">VR</span>

 scene link embed into a Twitter Card, so you can even watch the experience without leaving Twitter:

> [@guri_vr](https://twitter.com/guri_vr) [pic.twitter.com/0MufoQhiCv](https://t.co/0MufoQhiCv)

> -- Dan Zajdband (@impronunciable) [July 9, 2016](https://twitter.com/impronunciable/status/751870377429704704)

> [@impronunciable](https://twitter.com/impronunciable) your scene is here <https://t.co/n2O4S2OTh7>

> -- Guri 

> <span class="caps">VR</span>

>  (@guri_vr) [July 9, 2016](https://twitter.com/guri_vr/status/751870382219599872)

For now, it only works with a single picture, but it can be easily modified to allow multiple panoramas as scenes or intertitles.

### Transferable Experiences, Open Web

Guri 

<span class="caps">VR</span>

 is open source, and it's under heavy development. You can [fork it](https://github.com/opennewslabs/guri-vr) and help make it great. I'm also looking for feedback from storytellers and other people interested in creating 

<span class="caps">VR</span>

 without coding skills.

<span class="caps">VR</span>

 opens up powerful new ways of telling meaningful stories, and it's important to be able to prototype these stories easily. As with any new technology, there is a potentially steep learning curve and a lot of hype around 

<span class="caps">VR</span>

. But I think that the right uses can be very beneficial for newsrooms and their readers, and by using open web standards, we are as close as possible to the public. I encourage you to try these tools--and see for yourself.

### About [Dan Zajdband](https://source.opennews.org/people/dan-zajdband/)

![](https://pbs.twimg.com/profile_images/646001954540417024/E-n4f2k1_400x400.jpg)

Find me on Twitter as [@impronunciable](https://twitter.com/impronunciable).

[comments powered by](http://disqus.com)
