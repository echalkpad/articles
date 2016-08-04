# Open Modules for Open Minds

[Original URL](http://www.horizontalpitch.com/2015/09/1271/)

> Intro I can't hide it, I've always been attracted by the idea of "openness", be it open content, software or hardware. My first encounter with this world was – as for most people...

## Intro

I can't hide it, I've always been attracted by the idea of "openness", be it open content, software or hardware. My first encounter with this world was – as for most people I guess – open source software, to be more specific the Linux operating system.<br>
When I first got into Linux, it wasn't as widespread as it is now. Things were still kind of hard to get into, for example you had to compile the kernel from source, if you wanted to get your Soundblaster16 to play a sound. It goes without saying that most applications had to be compiled from source as well. I'm not a developer, so the fact that the software was open, wasn't really a big advantage for me and in the end I did use Linux mainly to "check it out". Still the very idea fascinated me and I could see where this was heading.<br>
Years later I got into audio DIY (I built several synths from kits, including the **SammichSID** and the **Shruthi**) and through that got into contact with the world of open source hardware and open micrcontroller-based software.<br>
All of these experiences somehow led me to create Papernoise (my small design company) and I guess this is why I feel a strong connection with this theme.<br>
But before we go any further, what is open source, really?

## What Does "Open Source" Actually Mean?

The term has its roots in the software, hacking and copyleft/no-copyright movement of the 80s and 90s and is commonly used to describe code, that is made available with a license that grants the rights to "study, change, and distribute the software to anyone and for any purpose" (1). An older, alternate terminology for it is "free software" (not to be confused with "freeware") or "libre software".<br>
Similarly, "open source hardware" describes projects where the CAD files, schematics, and anything needed to produce or modify the device are made available through an open license.

A complete definition of open source hardware can be found on the **Open Source Hardware Association**'s website:[www.oshwa.org](http://www.oshwa.org/definition/) and you can learn more about free software here: [www.gnu.org](https://www.gnu.org/philosophy/free-sw.html)

In a [recent post on the **Create Digital Music** blog](http://createdigitalmusic.com/2015/09/means-meeblip-synth-open-source-hardware/), Peter Kirn does a pretty good job at summing up what it takes to call your device "open source hardware":

> Manufacturers who want to call themselves open source hardware have some work to do. The OSHW definition is a really tough definition, but we have done our best to understand and follow it. You should definitely read the whole definition if you're interested, but here are the big points:

> 1. The design is public.
> 2. The source and documentation are public, and in a way that lets you modify it, using an all open source toolchain.
> 3. You can learn from that design, modify it, make the hardware yourself, and make and sell your own derivatives.
> 4. A license guarantees your rights to use the tool, without discriminating against how you use it or what you use it with. [...]

## Open Source Hardware and Software in the World of Modular Synths

There's a good chance that at least some of the modules you own and use have been released with an open source license, or have been made possible by people distributing their work in such a manner. Makers like **Mutable Instruments, QuBit Electronix, Monome, Befaco, Rebel Technology, HackMe, Music Thing Modular** and many others have been working this way for years, and with good success I'd dare to say.<br>
I got in touch with the above-mentioned companies, asking the makers why they decided to go with an open software and hardware licensing scheme, and how that worked out for them. I'll also be looking into examples of virtuous circles, in which a contribution from the community actually helped the original product to grow further, or in which an open project got developed further by another maker.

Since I collected quite an amount of material while researching this topic, I will have to split this into a couple of articles. So this is just the first of a series.

## Why Go Open?

At first it might seem counterintuitive to "give away" all your IP and innovative ideas, so others cannot only use parts of them, but also completely clone your devices and sell them (depending on the license of course). Yet, this seems to work pretty well for many people and has proven to be a great enrichment for the modular scene. I talked to some module makers to understand why they decided to embrace this type of licensing scheme.

[<embed src="http://www.horizontalpitch.com/wp-content/uploads/2015/09/mutable-700x233.jpg%20700w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/mutable-860x287.jpg%20860w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/mutable-680x227.jpg%20680w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/mutable-400x133.jpg%20400w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/mutable.jpg%20900w" class="wp-image-1284 size-medium" width="700">](http://www.horizontalpitch.com/wp-content/uploads/2015/09/mutable.jpg)

 Mutable Instruments modules, Photo by Elizabeth Busani

**Olivier Gillet, [Mutable Instruments](http://mutable-instruments.net/):**

I didn't even weigh the pros and cons – when I started tinkering with synths in summer 2009 it was clear to me that everything I did would have to be open source!

My attitude about open-source changed a lot around 2007-2010\. I had worked for a couple of years in tech companies which were very secretive about their technology. I had seen the efforts that go into intellectual property issues (protecting software against copy and debugging... the process of filing patents...) and it sounded like a very good recipe to lose focus and waste time outside of the "core" of the product – doable for a middle or large company, but a distraction for a one-man-band like me! I felt much more happy with the attitude I saw in academia where sharing and publishing is the norm – it felt much closer to my values. Around that time I spent more and more time working on a daily basis with open-source software and operating systems, and really came to appreciate their quality. At the same time I got much more confident with the quality of my own code – the primary reason I had not shared code before was that I was so ashamed of it. Add to that the fact that Mutable Instruments was just a hobby at that time, so I was not really concerned by the "business" aspects of the decision. It beautifully counterbalanced the climate of secrecy at my main employer at the time. So all the planets were aligned, sort of: for the first time I had the opportunity to do it, and felt all warm and fuzzy about doing it! But it doesn't explain why it is good<br>
in the first place...

First thing: in the audio/music instrument space we can hear so often things like "our fine circuits crafted by the most expert analog gurus"... "our cutting-edge patent-pending DSP algorithms"... And well, there's a lot of bullshit happening here, I can tell from occasionally looking at what's inside some products. This is enabled by the proprietary nature of the code or hardware – we don't really know what's inside, so we can believe what the marketing guy says.<br>
Nobody can get really critical, without admitting reverse engineering is their weekend hobby! Open-source is a good vaccine against that – since the innards are visible you can't lie about what's really happening in the product. I like this situation. Most Mutable Instruments products are so unsophisticated, often they are just a well-executed variant of the simplest thing that could possibly work, and I don't and can't lie about that! Other manufacturers aren't doing anything sophisticated either – but their stuff being proprietary allows them to play the mystique card.

There's more to this "integrity" theme. I am responsible for pretty much everything that goes into a module (graphic design aside!). Nobody reviews my code or schematics, but I know that once they are published, many eyes will look at them, and the extra pressure I get from knowing that makes me more thorough in my work. I can't just publish rotten code or dirty manufacturing hacks. I think it improves overall quality – and once in a while I get good tips from brilliant people who take the time to read my code or schematics.

Another important aspect is the opportunity for customization. When using audio gear, we often get frustrated by decisions the designers made – with gear from other brands I find myself wishing parameters had a larger range, or there was an extra LFO waveform, or the UI was laid out differently. Open-source allows people to address these limitations through firmware tweaks. Especially with digital modules, which could be capable of much more than what they initially do (at the cost of becoming more difficult to use). There's a point at which I find a module too deep and self-contained, but nothing prevents people to go one level deeper and add a built-in sequencer or modulation sources to an oscillator module, or cram rhythmic envelopes in a quad VCA!

Another thing I like about open-source is that it can give a second life to products I am no longer interested in producing and supporting. This is the case for all the DIY kits I have released over the years – even if Mutable Instruments has discontinued them, they exist in other forms. For example, the MIDIpal now exists as Audiothingies' MIDIbro and MIDIsizer's MIDIgal.

If I can add one last point: virtually any synth circuit is an<br>
iteration upon something coming from Buchla, Serge, or maybe Ken<br>
Stone, Ray Wilson, Ian Fritz, Yves Usson, René Schmitz, Jurgen Haible, Thomas Henry, Osamu Hoshuyama... It's kind of ridiculous to be jealous with our own "creations" when all of us in the synth community have learnt the trade by building and studying public schematics. Sharing and making open our stuff is a way of giving back! And Mutable Instruments had a kind of educational mission when it started with the DIY kits – show you what's inside so you can learn from it, that's more or less why the logo evolved from a figure of Saraswati.

[<embed src="http://www.horizontalpitch.com/wp-content/uploads/2015/09/monome-700x233.jpg%20700w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/monome-860x287.jpg%20860w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/monome-680x227.jpg%20680w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/monome-400x133.jpg%20400w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/monome.jpg%20900w" class="wp-image-1283 size-medium" width="700">](http://www.horizontalpitch.com/wp-content/uploads/2015/09/monome.jpg)

 Monome modules and grid, photo curtesy of Monome

**Brian Crabtree, [Monome](http://monome.org/modular/):**

I feel openness is part of the nature of the device, given its do-nothing (or do-everything) quality. This particular grid [_He is talking about their famous grid controller, Ed._] is a completely blank slate– encouraging musicians to envision how they want to interact with sound. And while not everyone is a programmer, we try to empower those looking to go down that road, and one of the best methods of doing this is to provide the source code for existing applications.

There was a moment early on when I decided it would be far more compelling to see how a community would collectively explore the grid, rather than just keep it to myself as an individual artist. There is a tendency to protect our musical discoveries– custom Max patches, or hardware configurations or whatever– and too closely associate these things with our own artistic identity. But I'm very glad to have released MLR (the quintessential live sample cutter) out into the wild, for example. Numerous people have taken it through countless mutations which has been great to see– features added that I wouldn't have ever considered. And more importantly it gives me great impetus to keep pushing and exploring new musical territory, rather than just sit contentedly with my secret formula.

It has been interesting to watch the evolution of open hardware. Monome existed prior to the Open Source Hardware movement and was one of the first commercial products available that had full hardware source posted. We didn't want to participate in the patent system, but we also didn't want to be exposed to its ills. I once wrote "our best protection is openness" which now rings wildly idealistic. We didn't (and don't) have money to compete with big corporations. All of these concerns simply distract from actually pursuing new ideas.

[<embed src="http://www.horizontalpitch.com/wp-content/uploads/2015/09/nebulae-700x233.jpg%20700w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/nebulae-860x287.jpg%20860w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/nebulae-680x227.jpg%20680w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/nebulae-400x133.jpg%20400w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/nebulae.jpg%20900w" class="wp-image-1285 size-medium" width="700">](http://www.horizontalpitch.com/wp-content/uploads/2015/09/nebulae.jpg)

 The Nebulae, photo curtesy of QuBit Electronics

**Andrew Ikenberry, [QuBit Electronix](http://www.qubitelectronix.com/):**

We chose to release various firmware for the Nebulæ so that people can take advantage of the extremely powerful and versatile synthesis engines of Csound and Pure Data which run on the Nebulæ.<br>
Csound and Pure Data have a large user base and are easy to program which has made it very easy for users to create their own programs.<br>
This makes the Nebulæ so much more than just a sample player. It becomes an open ended DSP platform that people can tailor to their own musical needs.

The other benefit is that many people have posted their code online for other users to download and run. We've seen a great community spring up of people interested in hacking the module and sharing their results with others.

[<embed src="http://www.horizontalpitch.com/wp-content/uploads/2015/09/befaco-700x233.jpg%20700w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/befaco-860x287.jpg%20860w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/befaco-680x227.jpg%20680w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/befaco-400x133.jpg%20400w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/befaco.jpg%20900w" class="wp-image-1287 size-medium" width="700">](http://www.horizontalpitch.com/wp-content/uploads/2015/09/befaco.jpg)

 The Befaco Eurorack System, photo curtesy of Befaco

**Manu Retamero, [Befaco](http://www.befaco.org/):**

The main reason we all are here is beacause some really nice people published their schematics online, took the time to write technical papers, wrote tutorials or spent HOURS AND HOURS replying to our questions and doubts.<br>
It makes sense to give back something to the DIY community. right? ![:)](http://www.horizontalpitch.com/wp-includes/images/smilies/simple-smile.png)

_Ed.: I also asked them about this relates to their workshop activities._

We like to offer as many possibilities as possible. From the expert DIYer that is able to etch boards and source components, to the musician that do not have the time to mess up with electronics and just want to make music.<br>
Some people have time and knowledge so they will go the extreme DIY way, some others are more like hobbysts that preffer to get a kit and do it at home, some other want the assembled unit and just make music....

Workshops, in the other hand, are way more than a soldering learn activity. It's a place where synth/music lovers gather for a whole weekend, share experiences, talk geeky non-stop, have a few drinks and go home with one or two modules! We love this formula as people really enjoy (try to imagine what kind of people come to an eurorack workshop...) and we get strong feedback and ideas from the users. Making all this more human.

[<embed src="http://www.horizontalpitch.com/wp-content/uploads/2015/09/owl-700x233.jpg%20700w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/owl-860x287.jpg%20860w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/owl-680x227.jpg%20680w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/owl-400x133.jpg%20400w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/owl.jpg%20900w" class="wp-image-1286 size-medium" width="700">](http://www.horizontalpitch.com/wp-content/uploads/2015/09/owl.jpg)

 The OWL, photo curtesy of Rebel Technology

**Martin Klang, [Rebel Technology](http://www.rebeltech.org/):**

The main reason we publish our work is that we want to encourage the creation and expansion of an open knowledge commons, around DIY synths, modular, and professional audio.<br>
Another reason for using the GPL is practical and selfish: we can have our cake and eat it! Firstly we get to share our work freely, knowing that whatever happens it will always be available to anyone who wants to build on it, rather than languishing in a corporate silo somewhere. Secondly, we are free to sell commercial licenses to anyone who wants our code for closed-source products. This is the dual-license scheme that has proved successful for projects like JUCE.

_Ed.: Rebel Technology's programmable module, the OWL, depends strongly on user-generated content, it's basically a community-powered module. I asked Martin if making open source modules goes beyond just releasing the sourcecode._

Yes it really is community powered, and that's what makes it great! It also means that while another manufacturer could copy our concept and our hardware, they can't copy the community. Supporting and nurturing a burgeoning community is difficult, and there are many things that we work hard to get better at: documentation, outreach, encouraging new members to get involved and share their output. As for going beyond just releasing source code, I think that it is absolutely necessary to do more. Simply dropping your code into a public Github repo is, to me, not open source, it's just lazy!

[<embed src="http://www.horizontalpitch.com/wp-content/uploads/2015/09/vectr-700x233.jpg%20700w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/vectr-860x287.jpg%20860w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/vectr-680x227.jpg%20680w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/vectr-400x133.jpg%20400w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/vectr.jpg%20900w" class="wp-image-1288 size-medium" width="700">](http://www.horizontalpitch.com/wp-content/uploads/2015/09/vectr.jpg)

 The Vectr, photo by Elizabeth Busani

**Matthew Heins, [HackMe](http://hackmeopen.com/):**

Sometime in 2008 I believe, I attended a Dorkbot meeting in Chicago where Todd Bailey presented his open source sampler **Where's the Party At?. I thought to myself, "This is awesome! I wanna do that!" Credit where it's due, I really appreciated the fact that he published his design and that I could learn from it. I knew right then that I wanted to start building synths and wanted to contribute to the open source community. I decided that I wanted to make a mono synth and that I would make it open source. That synth became Rockit.

[<embed src="http://www.horizontalpitch.com/wp-content/uploads/2015/09/radio-700x233.jpg%20700w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/radio-860x287.jpg%20860w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/radio-680x227.jpg%20680w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/radio-400x133.jpg%20400w,%20http://www.horizontalpitch.com/wp-content/uploads/2015/09/radio.jpg%20900w" class="size-medium wp-image-1289" width="700">](http://www.horizontalpitch.com/wp-content/uploads/2015/09/radio.jpg)

 The Radio Music module, photo curtesy of Music Thing Modular

**Tom Whitwell, [Music Thing Modular](http://musicthing.co.uk/modular/):**

My background was magazine and newspaper journalism – professional publishing, huge overheads, huge barriers to entry.

When blogging came along in 2000-2005, I was really interested in the way that new tech reduced the barrier to entry to zero; anyone could publish, anyone could find an audience. I set up [musicthing.blogspot.com](http://musicthing.blogspot.com) to experiment – writing for a small niche audience, finding an audience and a community, monetising in different ways (i.e. I wrote a blog, and it enabled me to transition my career from print magazines into digital – that paid me very well, but I didn't earn much directly from the blog)

When I got into electronics, I could see that technology was changing in a similar way. In the '70s, '80s, 90s, 2000s, the barrier to being an electronics manufacturer was huge. There was a hobbyist scene, with people etching their own boards in acid baths, and using things like veroboard, but that wasn't manufacturing, it was something different.

With Eurorack, there was already an infrastructure – the cases, the power supply, the community at Muffs – and I realised I could 'publish' a module and let other people do the manufacturing;

- PCBs from small-run producers like Seeed Studio or OSH Park
- Easy component sourcing from shared projects at Mouser or Farnell
- Front panels from your local laser cutting guy (or, more recently, from the PCB fabricators)

I realised I could 'publish' a circuit the way I 'published' a blog – I'd put it out into the world with very little cost, and the manufacturing overhead distributed among users.

My goal was to get the circuits used and enjoyed, because I could see there wasn't really any money in this kind of manufacturing. I had a job I liked and didn't want to set up as a small-scale manufacturer.

One barrier to success (in terms of spreading the designs) would be copyright stuff, so I published everything as CC-BY-SA, meaning anyone willing to help with distribution could be paid. They might pay me a royalty, they might not – it's up to them.

Thonk (and a few other people) has used the CC-BY-SA license to build his businesses – I've been very lucky because he's done a great job, earning a great reputation for well put-together kits. It could have gone wrong, but hasn't so far.

## Conclusions (For Now)

There's some recurring themes here. Most people I talked with seem to agree that enforcing copyright and patents is only eating up time, which is much better spent on creating new things. Another very central theme seems to be the fact that most module makers got where they are now, thanks to people who have been generously publishing their schematics and source codes. This is a very interesting point, which sparks a whole series of questions: how much do you have to modify an idea to be able to call it your own and is there really something like an original idea?<br>
But we're getting philosophical. Both copyright and patent laws were created to help businesses make profit from their innovations and their intellectual work, so the question is, does the open source approach work for those in the modular world, who have embraced it? Apparently it does. We'll talk about this in detail in the next article. Stay tuned!

### FootNotes

1. St. Laurent, Andrew M. (2008). Understanding Open Source and Free Software Licensing. O'Reilly Media. p. 4\. ISBN 9780596553951.