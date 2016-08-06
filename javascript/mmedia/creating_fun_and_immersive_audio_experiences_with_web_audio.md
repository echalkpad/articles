# Creating Fun and Immersive Audio Experiences with Web Audio

[Original URL](http://www.sitepoint.com/creating-fun-immersive-audio-experiences-web-audio/)

> This article is part of a web development series from Microsoft. Thank you for supporting the partners who make SitePoint possible. Today, thanks to the power of the Web Audio API, you can create...

_This article is part of a web development series from Microsoft. Thank you for supporting the partners who make SitePoint possible._

Today, thanks to the power of the [**Web Audio** API](https://dev.windows.com/en-us/microsoft-edge/platform/status/?utm_source=SitePoint&utm_medium=article66&utm_campaign=SitePoint/webaudioapi?filter=f3f0000bf&search=web%20audio), you can create **immersive audio experiences** directly in the browser – there's **no need for any plug-ins** here. Today I'd like to share with you what I've learned while building the audio engine of our [Babylon.js](http://www.babylonjs.com/?WT.mc_id=16530-DEV-sitepoint-article66) open-source gaming engine.

By the end of this article, you'll know how to build [this kind of experience](https://dev.windows.com/en-us/microsoft-edge/testdrive/demos/musiclounge/?utm_source=SitePoint&utm_medium=article66&utm_campaign=SitePoint):

![Cubes](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/11/1447591174web-audio-api-immersive-audio01-cubes.png)

**Usage:** move the cubes with your mouse or touch near the center of the white circle to mix [my music](https://soundcloud.com/david-rousset/coding4fun-ms-techdays-2015) in this 3D experience using WebGL& Web Audio. View the [source code](https://github.com/MicrosoftEdge/Demos/tree/master/musiclounge).

## Web Audio in a nutshell

Web Audio is the most advanced audio stack for the web.

![Web Audio Stack](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/11/1447591180web-audio-api-immersive-audio02-audio-stack.png)

From [Learn Web Audio API](http://www.devbattles.com/en/sand/post-69-Learn+Web+Audio+API)

If you ever tried to do something else than streaming some sounds or music using the [HTML5 audio element](http://www.w3.org/TR/html5/embedded-content-0.html#the-audio-element), you know how limited it was. Web Audio allows you to break all the barriers and provides you **access to the complete stream and audio pipeline** like in any modern native application. It works thanks to an **audio routing graph made of audio nodes**. This gives you **precise control over time, filters, gains, analyzer, convolver and 3D spatialization**.

It's being [widely supported now](https://dev.windows.com/en-us/microsoft-edge/platform/status/webaudioapi?utm_source=SitePoint&utm_medium=article66&utm_campaign=SitePoint/?filter=f3f0000bf&search=web%20audio) (Microsoft Edge, Chrome, Opera, Firefox, Safari, iOS, Android, FirefoxOS and Windows Mobile 10). In Edge (but I'm guessing in other browsers too), it's been **rendered in a separate thread** than the main JS thread. This means that almost all the time, it will have few if **no performance impact** on your app or game. The **codecs supported are at least WAV and MP3** by all browsers and some of them also support OGG. Edge even [supports the multi-channel Dolby Digital Plus™ audio format!](http://blogs.windows.com/msedgedev/2015/05/26/announcing-dolby-audio-for-high-performance-audio-in-microsoft-edge/?WT.mc_id=16530-DEV-sitepoint-article66)! You then need to pay attention to that to build a web app that will run everywhere and provides potentially multiple sources for all browsers.

### Audio routing graph explained

![Audio routing graph](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/11/1447591186web-audio-api-immersive-audio03-audio-routing-graph.png)

**Note:** _this picture has been built using the graph displayed into the awesome Web Audio tab of the Firefox DevTools. [I love this tool.](https://twitter.com/davrous/status/660130281752522752) :) So much that I've planned to mimic it via a [Vorlon.js](http://www.vorlonjs.io/?WT.mc_id=16530-DEV-sitepoint-article66) plug-in. I've started working on it but it's still very early draft._

Let's have a look to this diagram. Every node can have something as an input and be connected to the input of another node. In this case, we can see that MP3 files act as the source of the **AudioBufferSource** node, connected to a **Panner** node (which provides spatialization effects), connected to **Gain** nodes (volume), connected to an **Analyser** node (to have access to frequencies of the sounds in real-time) finally connected to the **AudioDestination** node (your speakers). You see also you can control the volume (gain) of a specific sound or several of them at the same time. For instance, in this case, I've got a "global volume" being handled via the final gain node just before the destination and some kind of tracks' volume with some gains node placed just before the analyser node.

Finally, Web Audio provides 2 [psychoacoustics](https://en.wikipedia.org/wiki/Psychoacoustics) models to simulate spatialization:

- **equalpower** (set by default) to support classic speakers for your laptop or floorstanding speakers.
- **HRTF** which provides the best results with an headphone as it simulates [binaural recording](https://en.wikipedia.org/wiki/Binaural_recording).

**Note:** _MS Edge currently only supports equalpower._

And you're set for the basics. To start playing with some code, I would suggest you reading those great resources:

For instance, let's imaging we'd like to create such an audio graph:

![Audio graph](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/11/1447591192web-audio-api-immersive-audio04-audio-graph.png)

I'd like to play 2 synchronized music that will each have its own gain connected to their own analyser. This means that we'll be able to display the frequencies of each music on separate canvas and play with the volume of each one separately also. Finally, we will have a global volume that will act on both music at the same time and a third analyser that will display the frequencies on both music cumulated on a third canvas.

You'll find the demo here: [analysersSample.html](http://david.blob.core.windows.net/webaudio/demos/articlesample1/analysersSample.html)

![Analyser sample demo](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/11/1447591198web-audio-api-immersive-audio05-analysers-sample-demo.png)

Simply have a look to the source code using your favorite browser and it's F12 tool.

![Analyser sample debug js](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/11/1447591203web-audio-api-immersive-audio06-analyser-sample-debug-js.png)

**sound.js** is abstracting the logic to load a sound, decode it and play it. **analyser.js** is abstracting the job to do to use the Web Audio analyser and display frequencies on a 2D canvas. Finally, **analysersSample.js** is using both files to create the above result.

Play with the sliders to act on the volume on each track or on the global volume to review what's going on. For instance, try to understand if you're putting the global volume to 0, why can you still see the 2 analysers showing data on the right?

To be able to play the 2 tracks synchronized, you just need to load them via a XHR2 request, decode the sound and as soon as those 2 asynchronous operations are done, simply call the Web Audio play function on each of them.

## Web Audio in Babylon.js

### Basics

I wanted to keep our "_simple & powerful_" philosophy while working on the audio stack. Even if Web Audio is not that complicated, it's still very verbose to me and you're quickly doing boring and repetitive tasks to build your audio graph. My objective was then to encapsulate that to provide a very simple abstraction layer and to let people **create 2D or 3D sounds without being a sound engineer**.

Here is the signature we've chosen to create a sound:

```
varnewSound = new BABYLON.Sound("nameofyoursound", "URLToTheFile",
yourBabylonScene,
callbackFunctionWhenSoundReadyToBePlayed,
optionsViaJSONObject);
```

For instance, here is how to create new sounds in Babylon.js:

```
varsound1 = new BABYLON.Sound("sound1", "./sound1.mp3", scene,
 null, { loop: true, autoplay: true }
);

varsound2 = new BABYLON.Sound("sound2", "./sound2.wav", scene,
 function() { sound2.play(); },
 { playbackRate: 2.0 }
);

varstreamingSound = new BABYLON.Sound("streamingSound", "./sound3.mp3", scene,
 null, { autoplay: true, streaming: true }
);
```

First sound will be played automatically in loop as soon as it will be loaded from the web server and decoded by Web Audio.

Second sound will be played once thanks to the callback function that will trigger the `play()` function on it once decoded and ready to be played. It will be played also at a 2x playback rate.

The last sound is using the "`streaming: true`" option. It means we'll use the **HTML5 Audio element** instead of doing a XHR2 request and using a Web Audio `AudioBufferSource` object. It could be useful if you'd like to stream a music rather than waiting to download it completely. Still, you can apply all the magic of Web Audio on top of this HTML5 Audio element: analyser, 3D spatialization and so on.

You can play with this logic into our playground such as with this very simple sample: [http://www.babylonjs-playground.com/index.html?22](http://www.babylonjs-playground.com/index.html?22/?WT.mc_id=16530-DEV-sitepoint-article66)

### Engine, Tracks and memory foot print

The Web Audio audio context is only created at the very last moment. When the Babylon.js audio engine is created in the Babylon.js main engine constructor, it only checks for Web Audio support and set a flag. Also, every sound is living inside a sound track. By default, the sound will be added into the main sound track attached to the scene.

The Web Audio context and the main track (containing its own gain node attached to the global volume) are really created only after the very first call to _new BABYLON.Sound()_.

We've done that to avoid creating some audio resources for scenes that won't use audio, which is today the case of most of our scenes hosted on [http://www.babylonjs.com/](http://www.babylonjs.com/?WT.mc_id=16530-DEV-sitepoint-article66). Still, the audio engine is ready to be called at any time. This helps us also to detach the audio engine from the core game engine if we'd like to build a core version of Babylon.js without any audio code inside it to make it more lightweight. At last, we're currently working with big studios that pay of lot of attention to resources being used.

To better understand that, we're going to use Firefox and its Web Audio tab.

– Navigate to: [http://www.babylonjs-playground.com/](http://www.babylonjs-playground.com/?WT.mc_id=16530-DEV-sitepoint-article66) and open the Web Audio tab via F12\. You should see that:

![Babylon.js playground demo](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/11/1447591208web-audio-api-immersive-audio07-babylonjs-playground-demo.png)

No audio context has been created yet.

– Into the playground editor on the left, add this line of code just before "_return scene_" for instance:

```
varmusic = new BABYLON.Sound("Violons", "sounds/violons11.wav",
 scene, null, { loop: true, autoplay: true }
);
```

– You should now see this audio graph in F12:

![Babylon.js playground demo](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/11/1447591213web-audio-api-immersive-audio08-babylonjs-playground-demo.png)

The audio context has been dynamically created after the first call to `new BABYLON.Sound()` as well as the main volume, the main track volume and the volume associated to this sound.

### 3D Sounds

#### Basics

Web Audio is highly inspired by models used in libraries such as [OpenAL](https://www.openal.org/) (Open Audio Library) for 3D. **Most of the 3D complexity will then be handled for you by Web Audio.** It will handle the 3D position of the sounds, their direction and their velocity (to apply a [Doppler effect](https://en.wikipedia.org/wiki/Doppler_effect)).

As a developer, you then need to ask to Web Audio to position your sound in the 3D space and to set and/or update the position and orientation of the [Web Audio listener](http://webaudio.github.io/web-audio-api/#AudioListener) (your virtual ears) using the `setPosition()` and `setOrientation()` function. But if you're not a 3D guru, it could be a bit complex. That's why, we're doing that for you in Babylon.js. We're updating the Web Audio listener with the position of our camera in the 3D world. It's **like some virtual ears** (the listener) **were stick onto some virtual eyes** (the camera).

Up to now, we've been using 2D sound. To transform a sound into a spatial sound, you need to specify that via the options:

```
varmusic = new BABYLON.Sound("music", "music.wav",
 scene, null, { loop: true, autoplay: true, spatialSound: true }
);
```

Default properties of a spatial sound are:

- distanceModel (the attenuation) is using a "**linear**" equation by default. Other options are "_inverse_" or "_exponential_".
- maxDistance is set to **100**. This means that once the listener is farther than 100 units from the sound, the volume will be 0\. You can't hear the sound anymore.
- panningModel is set to "**equalpower**" following the specifications. The other available option is "_HRTF_". The specification says it's: "_a higher quality spatialization algorithm using a convolution with measured impulse responses from human subjects. This panning method renders stereo output_". This is the best algorithm when using a headphone.

**maxDistance** is only used when using the "_linear_" attenuation. Otherwise, you can tune the attenuation of the other models using the **rolloffFactor** and **refDistance** options. Both are set to 1 by default but you can change it of course.

For instance:

```
varmusic = new BABYLON.Sound("music", "music.wav", scene, null, {
 loop: true, autoplay: true, spatialSound: true,
 distanceModel: "exponential", rolloffFactor: 2
});
```

Default position of sound in the 3D world is _(0,0,0)_. To change that, use the `setPosition()` function:

```
music.setPosition(new BABYLON.Vector3(100, 0, 0));
```

To have a better understanding, please [have a look to this sample](http://www.babylonjs-playground.com/#2AH4YH/?WT.mc_id=16530-DEV-sitepoint-article66) into our playground:

![Babylon.js playground demo](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/11/1447591218web-audio-api-immersive-audio09-babylonjs-playground-demo.png)

Move into the scene using keyboard & mouse. Each sound is represented by a purple sphere. When you're entering a sphere, you'll start hearing one the music. The sound is louder at the center of the sphere and fall down to 0 when leaving the sphere. You'll also notice that the sound is being balanced between the right & left speakers as well as front / rear (which is harder to notice using only classic stereo speakers).

#### Attaching a sound to a mesh

The previous demo is cool but didn't reach my "**simple & powerful**" bar. I wanted something even more simple to use. I finally ended up with the following approach.

Simply create a _BABYLON.Sound_, attach it to an existing mesh and you're done! Only 2 lines of code! If the mesh is moving, the sound will move with it. You have nothing to do, everything else is being handled by Babylon.js.

Here's the code to use:

```
varmusic = new BABYLON.Sound("Violons", "sounds/violons11.wav",
 scene, null, { loop: true, autoplay: true }
);

// Sound will now follow the box mesh position
music.attachToMesh(box);
```

Calling the `attachToMesh()` function on a sound will transform it automatically into a spatial 3D sound. Using the above code, you'll fall into default Babylon.js values: a _linear_ attenuation with a _maxDistance_ of 100 and a panning model of type "_equalpower_".

Put your headphone and [launch this sample into our playground](http://www.babylonjs-playground.com/index.html?23/?WT.mc_id=16530-DEV-sitepoint-article66):

![Babylon.js playground - attachToMesh()](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/11/1447591224web-audio-api-immersive-audio10-babylonjs-playground-attachtomesh.png)

Don't move the camera. You should hear my music rotating around your head as the cube is moving. Want to have an **even better experience with your headphone?**

Launch the same demo into a browser supporting HRTF (like Chrome or Firefox), click on the "**Debug layer**" button to display it and scroll down to the audio section. **Switch** the current panning model **from "Normal speakers"** (equalpower) **to "Headphone"** (HRTF). You should enjoy a slightly better experience. If not, change your headphone... or your ears. ;-)

![Babylon.js playground debug layer](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/11/1447591229web-audio-api-immersive-audio11-babylonjs-playground-debug-layer.png)

**Note:** you can also move into the 3D scene using arrow keys & mouse (like in a FPS game) to check the impact of the 3D sound positioning.

#### Creating a spatial directional 3D sound

By default, spatial sounds are omnidirectional. But you can have directional sounds if you'd like to.

**Note:** directional sounds only work for spatial sounds attached to a mesh.

Here is the code to use:

```
varmusic = new BABYLON.Sound("Violons", "violons11.wav", 
 scene, null, { loop: true, autoplay: true }
);
music.setDirectionalCone(90, 180, 0);
music.setLocalDirectionToMesh(new BABYLON.Vector3(1, 0, 0));
music.attachToMesh(box);
```

`setDirectionalCone` takes 3 parameters:

- **coneInnerAngle**: size of the inner cone in degree
- **coneOuterAngle**: size of the outer cone in degree
- **coneOuterGain**: volume of the sound when you're outside the outer cone (between 0.0 and 1.0)

Outer angle of the cone must be superior or equal to the inner angle, otherwise an error will be logged and the directional sound won't work.

`setLocalDirectionToMesh()` is simply the orientation of the cone related to the mesh you're attached to. By default, it's _(1,0,0)_. On the right of the object if you want to.

You can play with [this sample from our playground](http://www.babylonjs-playground.com/#1BO0YS/?WT.mc_id=16530-DEV-sitepoint-article66) to better understand the output:

![Babylon.js playground - setlocaldirectionToMesh()](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/11/1447591235web-audio-api-immersive-audio12-babylonjs-playground-setlocaldirectiontomesh.png)

Move into the 3D scene. If you're inside the **space defined by the grey cone**, you should hear the music, if not you'll not hear it as the **coneOuterGain** is set to 0.

### Going further

Play with our famous **Mansion Web Audio** [demo](http://www.babylonjs.com/index.html?MANSION/?WT.mc_id=16530-DEV-sitepoint-article66):

![Mansion Web Audio](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/11/1447591239web-audio-api-immersive-audio13-mansion-web-audio.jpg)

![Mansion Web Audio](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/11/1447591244web-audio-api-immersive-audio14-mansion-web-audio.png)

![Mansion Web Audio](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/11/1447591250web-audio-api-immersive-audio15-mansion-web-audio.png)

Try to find the interactive objects in the Mansion scene, they're almost all associated to a sound. To build this scene, our 3D artist, **Michel Rousseau, hasn't use a single line of code!** He made it entirely using our new Actions Builder tool and 3DS Max. The Actions Builder is indeed currently only available in our 3DS Max exporter. We will soon expose it into our [sandbox tool](http://www.babylonjs.com/sandbox/?WT.mc_id=16530-DEV-sitepoint-article66). If you're interested in building a similar experience, please read those articles:

But, the best thing to definitely understand and learn how we've been using Web Audio in our gaming engine is **by reading our source code**:

- [BABYLON.AudioEngine](https://github.com/BabylonJS/Babylon.js/blob/master/src/Audio/babylon.audioEngine.ts) being created by the Babylon.js core engine as static.
- [BABYLON.SoundTrack](https://github.com/BabylonJS/Babylon.js/blob/master/src/Audio/babylon.soundtrack.ts) containing several sounds. You have at least one track being created if you're creating a sound, this is the main track.
- [BABYLON.Sound](https://github.com/BabylonJS/Babylon.js/blob/master/src/Audio/babylon.sound.ts) containing the main logic you'll interact with.
- [BABYLON.Analyser](https://github.com/BabylonJS/Babylon.js/blob/master/src/Audio/babylon.analyser.ts) that can be attached to the audio engine itself (to view the output via the global volume) or on a specific track.

Please also have a look into [BABYLON.Scene](https://github.com/BabylonJS/Babylon.js/blob/master/src/babylon.scene.ts) and the `_updateAudioParameters()` function to understand how we're updating the Web Audio listener based on the current position & orientation of the camera.

Finally, here are some useful resources to read:

## Building a demo using multitracks & analysers

In order to show you what you can quickly do with our Babylon.js audio stack, I've built [this fun demo](http://david.blob.core.windows.net/webaudio/demos/articlesample2/multitracks.html):

![Multitracks & Analysers](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/11/1447591256web-audio-api-immersive-audio16-multitracks-analysers.png)

It uses several features from Babylon.js such as the **assets loader** that will automatically load the remote WAV files and display a default loading screen, **multi sounds tracks** and several **analyser objects** being connected to the sound tracks.

Please have a look to the source code of **multitracks.js** via your F12 tool to check how it works.

[This demo](http://david.blob.core.windows.net/webaudio/demos/articlesample2/multitracks.html) is based on a music I've already composed in the past that I've slightly re-arranged: [The Geek Council](https://soundcloud.com/david-rousset/the-geek-council). You can do your own mixing for the first part of the music by playing with the sliders. You can click as many time and as quickly as possible on the "**_Epic cymbals_**" button to add a dramatic effect. Finally, click on the "**_Initiate epic end sequence_**". It will wait for the end of the current loop and will stop the 4 first tracks (drums, epic background, pads & horns) to play the final epic sequence! :) Click on the "**_Reset_" button to restart from the beginning.**

**Note:** it turns out that to **have great sounds loops**, without any glitch, **you need to avoid using MP3** as explained in this [forum](http://www.html5gamedevs.com/topic/10976-seamless-loop/): "_The simplest solution is to just not use .mp3\. If you use .ogg or .m4a instead you should find the sound loops flawlessly, as long as you generate those files from the original uncompressed audio source, not from the mp3 file! The process of compressing to mp3 inserts a few ms of silence at the start of the audio, making mp3 unsuited to looping sounds. I read somewhere that the silence is actually supposed to be file header information but it incorrectly gets treated as sound data!_". That's why, I'm using WAV files in this demo. It's the only clean solution working accross all browsers.

## And what about the Music Lounge demo?

The [music lounge demo](https://dev.windows.com/en-us/microsoft-edge/testdrive/demos/musiclounge/?utm_source=SitePoint&utm_medium=article66&utm_campaign=SitePoint) is basically re-using everything being described before. But you're missing a last piece to be able to recreate it as it's based on a feature I haven't explained yet: the `customAttenuationFunction`.

You've got the option to **provide your own attenuation function** to Babylon.js to replace the default one provided by Web Audio (linear, exponential and inverse) as [explained in our documentation](http://doc.babylonjs.com/tutorials/16._Playing_sounds_and_music#creating-your-own-custom-attenuation-function).

For instance, if you're trying this [playground demo](http://www.babylonjs-playground.com/#1YIXEO/?WT.mc_id=16530-DEV-sitepoint-article66), you'll see I'm using this stupid attenuation function:

```
music.setAttenuationFunction(function (currentVolume, currentDistance,
maxDistance, refDistance, rolloffFactor) {
 returncurrentVolume 10_misconceptions_about_neural_networks.md 10_things_to_do_after_installing_ubuntu_1510.md a_developer’s_journey_into_linux_containers.md aerofsgockerize.md altitudeloginwithssh.md alvarotrigopagepilingjs.md andrewchildsovercast.md animatecss.md an_introduction_to_visual_programming_using_noflo.md anyone_can_learn_to_code_an_lstmrnn_in_python_part_1_rnn.md artificial_neural_networks_for_beginners_»_loren_on_the_art_of_matlab.md automating_docker_logging_elasticsearch_logstash_kibana_and_logspout.md a_valid_ssl_certificate_for_every_ip_address__pivotal_pov.md a_walk_through_the_sais_algorithm__screwtapes_notepad.md b2_cloud_storage.md basecamptrix.md better_logging_in_nodejs_using_bunyan.md be_with_130_premade_layouts_the_hard_work_was_already_done_for_you.md binaryjs.md blog.md bndwpick.md brief_survey_on_methods_for_attacking_tor_hidden_service.md building_frontend_web_apps_with_plain_javascript.md checkpoint_and_restore_docker_container_with_criu.md clipboardjs.md cmusatyalabopenface.md coding_math.md connorathertonwalkway.md containers_101.md cooking_with_docker_and_coreos_on_os_x.md coreos__kubernetes_step_by_step.md coreos.md create_a_rest_api_with_nodejs.md create_your_own_streaming_service_with_emby.md cryptdbcryptdb.md cryptographic_module_standards_at_a_crossroads_after_snowden’s_revelations.md cyrusandgdbdashboard.md dask__parallel_processing_through_blocked_algorithms¶.md data data.json delightimfreegeodb.md develop_command_line_application_using_nodejs.md developer_edition_44_new_visual_editing_and_memory_management_tools.md digitaloceangodo.md document_clustering_with_python.md dokku__the_smallest_paas_implementation_youve_ever_seen.md dosa_by_shon.md dosnapshot.md economic_secrets_of_the_dark_web—the_safe_easy_way_for_anyone_to_be_a_little_drug_lord.md eivind88prm.md even_easier_restful_api_with_nodejs_and_express_framework.md exploring_the_relationship_between_javascript_and_the_modern_html_dom.md extending_layer_2_across_container_hosts.md file_input_to_gnuplot_through_python.md fix_time_machine_sparsebundle_nas_based_backup_errors.md flaskpotion.md flowchartjs.md free_ssltls_certificates.md fuzzing_with_american_fuzzy_lop.md fyeah_swift_4_features_that_changed_how_i_code_with_jacob_schwartz.md gernestutron.md get_coreos_logs_into_elk_in_5_minutes.md getting_started_·_bootstrap_table.md getting_started.md google_project_hosting.md grammarlyrockercompose.md hacker_codex.md hacking_gsm_signals_with_an_rtlsdr_and_topguw.md headroomjs.md how_can_i_repair_my_hard_drive_if_my_mac_won’t_start.md how_to_convert_videos_in_linux_using_the_command_line.md how_to_create_a_fullscreen_video_opening_animation.md how_to_create_a_network_backup_with_apples_timemachine.md how_to_create_an_os_x_mavericks_install_drive_updated.md how_to_split_large_audio_files_on_linux.md how_to_use_an_xbox_360_controller_with_os_x_yosemite.md html html5_games_3d_collision_detection.md httpie_a_cli_http_client_that_will_make_you_smile.md httpie_parse_url.sh http_live_streaming_in_javascript.md httpsmediumcombibblioorg60youtubechannelsthatwillmakeyousmarter44d8315c2548.md icodeforlovedoapi.md iiseymourchromote.md imjasonhghfs.md install_sandstorm.md instant_loading_web_apps_with_an_application_shell_architecture_—_google_developers.md introducing_lemur.md introduction__building_web_apps_with_go.md introduction__getting_started_guide.md introduction.md introduction_to_monte_carlo_tree_search.md intro_to_webgl_with_threejs.md jack000expose.md jamesobdesk.md jamiehdigitaloceancsharp.md javascript_animation_physics_and_input_tracking.md jmschreipomegranate.md jquery_responsive_lightbox.md jquery_treetable.md json_lines.md jssequencediagrams.md just_a_theory.md jwagnersmartcropjs.md kairos_by_rodrigogs.md keendashboards.md khankatex.md koalalorenzopythondigitalocean.md launching_nginscript_and_looking_ahead__nginx.md learning_golang_build_a_stock_notifier_·_kyle_redelinghuys.md learning_jquery.md letsencryptletsencrypt.md let’s_write_a_web_extension.md let_your_applications_fly.md links_list.txt looking_at_open_source_paas_technologies.md mangoslideout.md many_js_frameworks_but_vuejs_is_different.md mateogianoliosshync.md maxogdenlinux.md md metabase_documentation.md metadata_investigation__inside_hacking_team.md meteor_react_tutorial_updated__match_made_in_heaven.md micc83edittable.md mistiomistio.md monitor_and_control_applications_using_supervisor_part_2__tuts_code_tutorial.md nanobox.md nats__cloud_native_open_source_high_performance_messaging.md natural_language_basics_with_textblob.md nightwatchjs.md nodejs_mvc_framework.md now_available_for_download_processing.md null.md offline_web_apps_on_github_pages.md open_source_slackalternative_adopts_markdown.md openvswitchovs.md opsing_with_packer_and_terraform.md parse_urls_json.sh pattern_lab.md png prototype_interaction_and_animation.md psdjs.md pyra_full_prototypes_soon.md quench.md quintus_javascript_html5_game_engine.md raathigeshsemanticedit.md rain1017memdb.md readtext.md reclaiming_a_timemachine_volume’s_disk_space.md resilient_substructure_for_your_microservice.md richardknopgooauth2server.md selzplyr.md setting_up_mastermaster_replication_with_mysql_on_debian_8_jessie.md setting_up_nginx_with_docker.md silentbicycleguff.md simplify_your_life_with_an_ssh_config_file.md single_page_apps_in_depth.md skavanaghkeybox.md smdahlenvagrantdigitalocean.md snapcraft_tutorial.md sqljs.md sql_vs_nosql_you_do_want_to_have_a_relational_storage_by_default.md storejs.md teslajs.md tgriesserbookshelf.md the_http2_web_server_with_automatic_https.md the_incomplete_collection_of_nodejs_performance_tips_—_node_and_beyond.md the_javascript_library_for_modern_browsers_and_touch_devices_no jquery.md the_javascript_tutorial.md the_math_i_learned_after_i_thought_had_already_learned_math.md the_new_art_of_war_how_trolls_hackers_and_spies_are_rewriting_the_rules_of conflict.md the_power_of_pcap__retrospection_in_practice.md the_trusted_source_for_javascript_packages_meteor_resources_and_tools.md the_what_how_and_why_of_single_var_pattern_in_javascript.md tidy timelinemax_a_primer.md time_machine_for_every_unix_out_there.md timothycrosleyhug.md top_15_facebook_open_source_projects_you_must_know_and_use_for_your_maker’s_projects.md ˈtræfɪk_·_ˈtræfɪk.md tridiv.md tugboat.md tutorials.md twitterfabricgalley.md twofactor_authentication_download.md unity_2d_flappy_bird_tutorial.md unixdaemon.md url_to_filename.csv using_an_offline_gnupg_master_key.md using_feathersjs_as_an_open_source_alternative_to_firebase_—_all_about_feathersjs.md using_the_office_ui_fabric_with_angular_js_to_create_an_office_addin.md valerylisaydigitaloceanapi.md voice_activated_light_switch.md watchman__a_file_watching_service.md webxray.md what_is_scholarly_html.md whose_xterm_is_it_anyway.md why_buy_an_electric_bike_if_we_can_transform_that_one_we_have.md wikiimunesexamples_–_imunes.md wkoszekflviz.md wmiradropkit.md write_vanilla_comments_that_automagically_convert_into_full_test_spec_files.md wso2_machine_learner_why_would_you care.md yahoogryffin.md yoannmoinetnipplejs.md your_pull_request_tracking_tool.md zenit.md currentDistance / maxDistance;
});
```

Near the object, volume is almost 0\. The further, the louder. It's completely counter intuitive but it's a sample to give you the idea.

In the case of the Music Lounge demo, it has been very useful. Indeed, the volume of each track doesn't depend on the distance of each cube from the camera but on the distance between the cube and the center of the white circle. If you're looking at the [source code](https://github.com/MicrosoftEdge/Demos/blob/master/musiclounge/src/musicLounge.soundCube.ts), the magic happens here:

```
attachedSound.setAttenuationFunction((currentVolume, currentDistance,
 maxDistance, refDistance, rolloffFactor) => {
 vardistanceFromZero = BABYLON.Vector3.Zero()
 .subtract(this._box.getBoundingInfo().boundingSphere.centerWorld)
 .length();

 if(distanceFromZero<maxDistance) { 
 returncurrentVolume 10_misconceptions_about_neural_networks.md 10_things_to_do_after_installing_ubuntu_1510.md a_developer’s_journey_into_linux_containers.md aerofsgockerize.md altitudeloginwithssh.md alvarotrigopagepilingjs.md andrewchildsovercast.md animatecss.md an_introduction_to_visual_programming_using_noflo.md anyone_can_learn_to_code_an_lstmrnn_in_python_part_1_rnn.md artificial_neural_networks_for_beginners_»_loren_on_the_art_of_matlab.md automating_docker_logging_elasticsearch_logstash_kibana_and_logspout.md a_valid_ssl_certificate_for_every_ip_address__pivotal_pov.md a_walk_through_the_sais_algorithm__screwtapes_notepad.md b2_cloud_storage.md basecamptrix.md better_logging_in_nodejs_using_bunyan.md be_with_130_premade_layouts_the_hard_work_was_already_done_for_you.md binaryjs.md blog.md bndwpick.md brief_survey_on_methods_for_attacking_tor_hidden_service.md building_frontend_web_apps_with_plain_javascript.md checkpoint_and_restore_docker_container_with_criu.md clipboardjs.md cmusatyalabopenface.md coding_math.md connorathertonwalkway.md containers_101.md cooking_with_docker_and_coreos_on_os_x.md coreos__kubernetes_step_by_step.md coreos.md create_a_rest_api_with_nodejs.md create_your_own_streaming_service_with_emby.md cryptdbcryptdb.md cryptographic_module_standards_at_a_crossroads_after_snowden’s_revelations.md cyrusandgdbdashboard.md dask__parallel_processing_through_blocked_algorithms¶.md data data.json delightimfreegeodb.md develop_command_line_application_using_nodejs.md developer_edition_44_new_visual_editing_and_memory_management_tools.md digitaloceangodo.md document_clustering_with_python.md dokku__the_smallest_paas_implementation_youve_ever_seen.md dosa_by_shon.md dosnapshot.md economic_secrets_of_the_dark_web—the_safe_easy_way_for_anyone_to_be_a_little_drug_lord.md eivind88prm.md even_easier_restful_api_with_nodejs_and_express_framework.md exploring_the_relationship_between_javascript_and_the_modern_html_dom.md extending_layer_2_across_container_hosts.md file_input_to_gnuplot_through_python.md fix_time_machine_sparsebundle_nas_based_backup_errors.md flaskpotion.md flowchartjs.md free_ssltls_certificates.md fuzzing_with_american_fuzzy_lop.md fyeah_swift_4_features_that_changed_how_i_code_with_jacob_schwartz.md gernestutron.md get_coreos_logs_into_elk_in_5_minutes.md getting_started_·_bootstrap_table.md getting_started.md google_project_hosting.md grammarlyrockercompose.md hacker_codex.md hacking_gsm_signals_with_an_rtlsdr_and_topguw.md headroomjs.md how_can_i_repair_my_hard_drive_if_my_mac_won’t_start.md how_to_convert_videos_in_linux_using_the_command_line.md how_to_create_a_fullscreen_video_opening_animation.md how_to_create_a_network_backup_with_apples_timemachine.md how_to_create_an_os_x_mavericks_install_drive_updated.md how_to_split_large_audio_files_on_linux.md how_to_use_an_xbox_360_controller_with_os_x_yosemite.md html html5_games_3d_collision_detection.md httpie_a_cli_http_client_that_will_make_you_smile.md httpie_parse_url.sh http_live_streaming_in_javascript.md httpsmediumcombibblioorg60youtubechannelsthatwillmakeyousmarter44d8315c2548.md icodeforlovedoapi.md iiseymourchromote.md imjasonhghfs.md install_sandstorm.md instant_loading_web_apps_with_an_application_shell_architecture_—_google_developers.md introducing_lemur.md introduction__building_web_apps_with_go.md introduction__getting_started_guide.md introduction.md introduction_to_monte_carlo_tree_search.md intro_to_webgl_with_threejs.md jack000expose.md jamesobdesk.md jamiehdigitaloceancsharp.md javascript_animation_physics_and_input_tracking.md jmschreipomegranate.md jquery_responsive_lightbox.md jquery_treetable.md json_lines.md jssequencediagrams.md just_a_theory.md jwagnersmartcropjs.md kairos_by_rodrigogs.md keendashboards.md khankatex.md koalalorenzopythondigitalocean.md launching_nginscript_and_looking_ahead__nginx.md learning_golang_build_a_stock_notifier_·_kyle_redelinghuys.md learning_jquery.md letsencryptletsencrypt.md let’s_write_a_web_extension.md let_your_applications_fly.md links_list.txt looking_at_open_source_paas_technologies.md mangoslideout.md many_js_frameworks_but_vuejs_is_different.md mateogianoliosshync.md maxogdenlinux.md md metabase_documentation.md metadata_investigation__inside_hacking_team.md meteor_react_tutorial_updated__match_made_in_heaven.md micc83edittable.md mistiomistio.md monitor_and_control_applications_using_supervisor_part_2__tuts_code_tutorial.md nanobox.md nats__cloud_native_open_source_high_performance_messaging.md natural_language_basics_with_textblob.md nightwatchjs.md nodejs_mvc_framework.md now_available_for_download_processing.md null.md offline_web_apps_on_github_pages.md open_source_slackalternative_adopts_markdown.md openvswitchovs.md opsing_with_packer_and_terraform.md parse_urls_json.sh pattern_lab.md png prototype_interaction_and_animation.md psdjs.md pyra_full_prototypes_soon.md quench.md quintus_javascript_html5_game_engine.md raathigeshsemanticedit.md rain1017memdb.md readtext.md reclaiming_a_timemachine_volume’s_disk_space.md resilient_substructure_for_your_microservice.md richardknopgooauth2server.md selzplyr.md setting_up_mastermaster_replication_with_mysql_on_debian_8_jessie.md setting_up_nginx_with_docker.md silentbicycleguff.md simplify_your_life_with_an_ssh_config_file.md single_page_apps_in_depth.md skavanaghkeybox.md smdahlenvagrantdigitalocean.md snapcraft_tutorial.md sqljs.md sql_vs_nosql_you_do_want_to_have_a_relational_storage_by_default.md storejs.md teslajs.md tgriesserbookshelf.md the_http2_web_server_with_automatic_https.md the_incomplete_collection_of_nodejs_performance_tips_—_node_and_beyond.md the_javascript_library_for_modern_browsers_and_touch_devices_no jquery.md the_javascript_tutorial.md the_math_i_learned_after_i_thought_had_already_learned_math.md the_new_art_of_war_how_trolls_hackers_and_spies_are_rewriting_the_rules_of conflict.md the_power_of_pcap__retrospection_in_practice.md the_trusted_source_for_javascript_packages_meteor_resources_and_tools.md the_what_how_and_why_of_single_var_pattern_in_javascript.md tidy timelinemax_a_primer.md time_machine_for_every_unix_out_there.md timothycrosleyhug.md top_15_facebook_open_source_projects_you_must_know_and_use_for_your_maker’s_projects.md ˈtræfɪk_·_ˈtræfɪk.md tridiv.md tugboat.md tutorials.md twitterfabricgalley.md twofactor_authentication_download.md unity_2d_flappy_bird_tutorial.md unixdaemon.md url_to_filename.csv using_an_offline_gnupg_master_key.md using_feathersjs_as_an_open_source_alternative_to_firebase_—_all_about_feathersjs.md using_the_office_ui_fabric_with_angular_js_to_create_an_office_addin.md valerylisaydigitaloceanapi.md voice_activated_light_switch.md watchman__a_file_watching_service.md webxray.md what_is_scholarly_html.md whose_xterm_is_it_anyway.md why_buy_an_electric_bike_if_we_can_transform_that_one_we_have.md wikiimunesexamples_–_imunes.md wkoszekflviz.md wmiradropkit.md write_vanilla_comments_that_automagically_convert_into_full_test_spec_files.md wso2_machine_learner_why_would_you care.md yahoogryffin.md yoannmoinetnipplejs.md your_pull_request_tracking_tool.md zenit.md (1 - distanceFromZero / maxDistance); 
 } 
 else{ 
 return 0; 
 }
});
```

We're computing the distance between the current cube `(this._box)` and the (0,0,0) coordinates which happens to be the center of the circle. Based on this distance, we're setting a linear attenuation function for the volume of the sound associated to the cube. Rest of the demo is using sounds attached to the various meshes (the cubes) and an analyser object connected to the global volume to animate the outer circle made of particles.

I hope you'll enjoy this article as much as I had writing it. I also hope that it will bring you **new ideas to create cool Web Audio / WebGL applications** or demos!

## More hands-on with Web Development

This article is part of the web development series from Microsoft tech evangelists on practical JavaScript learning, open source projects, and interoperability best practices including [Microsoft Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?WT.mc_id=16530-DEV-sitepoint-article66) browser and the new [EdgeHTML rendering engine](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=16530-DEV-sitepoint-article66).

We encourage you to test across browsers and devices including Microsoft Edge – the default browser for Windows 10 – with free tools on [dev.modern.IE](https://dev.windows.com/en-us/microsoft-edge/?utm_source=SitePoint&utm_medium=article66&utm_campaign=SitePoint):

More in-depth learning from our engineers and evangelists:

Our community open source projects:

More free tools and back-end web dev stuff:
