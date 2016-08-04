# Hacking GSM Signals with an RTL-SDR and Topguw

[Original URL](http://www.rtl-sdr.com/hacking-gsm-signals-with-an-rtl-sdr-and-topguw/)

> The ability to hack some GSM signals has been around for some time now, but the steps to reproduce the hack have been long and difficult to set up. Recently RTL-SDR.com reader Bastien wrote into...

The ability to hack some GSM signals has been around for some time now, but the steps to reproduce the hack have been long and difficult to set up. Recently RTL-SDR.com reader Bastien wrote into us to let us know about his [recently released project called Topguw](https://github.com/bastienjalbert/topguw). Bastien's Topguw is a Linux based program that helps piece together all the steps required in the GSM hacking process. Although the steps are simplified, you will still need some knowledge of how GSM works, have installed Airprobe and Kraken, and you'll also need a 2TB rainbow table which keeps the barrier to this hack still quite high. Bastien writes about his software:

> <span>So like I said my software can “crack” SMS and call over GSM network. </span>

> <span>
>   <strong>How ?</strong>
> </span>

> <span>I put <span lang="en">quotation marks in crack because my software is not enough to deciphered GSM itself.</span> My software can make some steps of the known-plaintext attack, introduce by Karsten Nohl, and by the way, increase the time to decipher an SMS or call. I’ll not explain here all the steps because they are long and tedious, but there is a lot of work done behind the Gui.</span>

> <span>Actually my software can extract Keystream (or try to find some of them) from a capture file of GSM, or by sniffing GSM with a rtl-sdr device. Then you just have to use Kraken to crack the key and you’re able to decipher sms or call.</span>

> <span>
>   <strong>Why ?</strong>
> </span>

> <span>This hack is very interesting! With only a little receiver (rtl-sdr) and some hard-disk capacity (2Tb), everyone can try to hack the GSM. It’s very low cost compare to other hack vector. Moreover the success rate is really great if you guess the Keystream correctly. So when I started to done this with my hands I though -&gt; why don’t try to make something to do this automatically. </span>

> <br>

> <span>This is how Topguw was born.</span>

> <span>Topguw, I hope, will sensitize people about risk they take by calling or sending sms with GSM.</span>

> <span>My software is currently in beta version but I did run several time and I got good results. Maybe better than something done by hand. But Topguw is made to help people who want to learn the hack. This is why several files are made to help GSM reverse-engineering.</span>

Topguw can be downloaded from GitHub at <https://github.com/bastienjalbert/topguw>. Bastien has also uploaded a video showing his software in action. If you're interested in Bastiens YouTube channel as he plans to upload another video soon where he shows himself hacking his own GSM sms/call signals.

[https://www.youtube.com/watch?v=–VIisKqVYk](https://www.youtube.com/watch?v=--VIisKqVYk "Play Video "GSM Hacking - easier than ever with Topguw"")

Of course remember that hacking into GSM signals is very illegal and if you do this then you must check the legality of doing so in your country and only receive your own messages or messages that are intended for you.
