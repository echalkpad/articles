# Voice Activated Light Switch

[Original URL](http://arunpn.com/projects/voice-activated-light-switch/)

> You are in bed, warm and comfy, watching a movie. You have the covers pulled over, about to fall asleep. At that moment you realize that the lights are still on. Damn! Now you've got to decide...

You are in bed, warm and comfy, watching a movie. You have the covers pulled over, about to fall asleep. At that moment you realize that the lights are still on. Damn! Now you've got to decide whether to get up from the comfort of your bed to switch off the lights or let it stay on for the whole night and just fall asleep.

I face this dilemma almost every night. Leaving the lights on causes a marked reduction in the quality of my sleep and more importantly makes me feel guilty. So I decided to fix this. The easiest way to fix it would have been to connect the lights to an extension cord and tape it to the bed frame. But where is the fun in that? ![:-)](http://arunpn.com/wp-includes/images/smilies/icon_smile.gif) So I built a voice activated light switch. With this, all I need to do is just say "Lights off" from my bed and it turns off the lights.

Here is a video of me and my friend Senthil trying it out on a lazy Sunday afternoon:

We have music running in the background to add environmental noise typical to a bedroom.

<span>
  <strong>Technical details:</strong>
</span>

<br>
The following diagram gives an overview of the whole thing.

[![LightSwitchOverview](http://107.170.211.19/wp-content/uploads/2015/02/LightSwitchOverview.jpg)](http://107.170.211.19/wp-content/uploads/2015/02/LightSwitchOverview.jpg)

Components used:<br>
* [Wit.ai](https://wit.ai/) (Speech Processing)<br>
* [Raspberry Pi](http://amzn.to/1gWvAM2) (Compute device)<br>
* [Powerswitch Tail 2](http://amzn.to/1MuowTC) (Relay Switch)

A wit.ai instance is trained to understand basic voice commands and intents. This instance runs on the wit.ai cloud. Then a program is written to run on the Raspberry Pi. This program accepts voice input from the microphone and sends it to the wit.ai instance. The program then parses the response it got back from the wit.ai instance. From the intent that was identified by wit.ai, the program sends a command to the switching circuit to toggle the light switch. I have shared the source code and instructions to use here: [Github repository](https://github.com/arunpn123/VoiceActivatedLightSwitch.git)

Here is a picture of the whole switch setup sitting under my desk.

[![LightSwitchDeployment](http://107.170.211.19/wp-content/uploads/2015/02/LightSwitchDeployment.jpg)](http://107.170.211.19/wp-content/uploads/2015/02/LightSwitchDeployment.jpg)

<span>
  <strong>Things that did not work:</strong>
</span>

For posterity's sake, it is good to keep track of things that did not work. So here they are. Building a working prototype did not take more than a couple of days. But getting the speech recognition to work properly took some time.

Initially, I wanted to keep the speech processing local. This would have the advantage of eliminating the latency involved in sending the voice command to an online service and getting the translation back. CMU Sphinx was the best one out there. But configuring it and training the model looked like it would take a lot of time. So I figured I would keep this as my last option.

Next, I tried out Google's speech recognition. Steven Hickson from Georgia Tech had published code that uses it [2]. This one could not understand my Indian accent properly. There were a bunch of hilarious instances. I wanted to name the device as Chitti [1]. But it identified itself several times as titty :-D. It understood my voice commands only like 30% of the time. After too many attempts trying to modulate my voice and use different sets of keywords, I chalked it up as not good for my use.

Since I plan to use this device for a long time, I did not want to pass AC current through a homemade relay switch that I wire up. So I purchased the Powerswitch Tail.

<span>
  <strong>
  <span>Credits:</span>
</strong>
</span>

1. My brother Bharat with whom I bounced ideas off.
2. My roommates and friends Mukunthan, Senthil, Sivaguru, Shriram for testing the speech recognition.
3. My friend Sanjeev for recommending Wit.ai for speech recognition.

<span>
  <strong>References:</strong>
</span>

<br>
[1] Chitti is a fictional Andro-Humanoid robot in the movie [Endhiran](http://en.wikipedia.org/wiki/Enthiran).<br>
[2] <https://github.com/StevenHickson/PiAUISuite><br>
[3] [http://computers.tutsplus.com/articles/how-to-control-a-lamp-with-voice-commands-and-a-raspberry-pi–mac-60825](http://computers.tutsplus.com/articles/how-to-control-a-lamp-with-voice-commands-and-a-raspberry-pi--mac-60825)

**Source code**:<br>
<https://github.com/arunpn123/VoiceActivatedLightSwitch.git>
