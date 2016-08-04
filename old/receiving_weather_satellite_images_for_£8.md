# Receiving Weather Satellite Images for £8

[Original URL](http://mattg.co.uk/words/noaa_sdr/)

> Saturday 25th July 2015 What? I found out you can get weather satellite images directly from the satellite when they pass overhead, using an £8 freeview dongle! Although I've considered playing...

Saturday 25th July 2015

## What?

I found out you can get weather satellite images directly from the satellite when they pass overhead, using an £8 freeview dongle! Although I've considered playing with this kind of stuff before, I hadn't until this week.

I got this on my 4th attempt:

[![The UK as seen from the NOAA 15 satellite, 25th Jul 2015.](http://mattg.co.uk/words/noaa_sdr/201507251639_noaa15_sdr_sml.jpg)](http://mattg.co.uk/words/noaa_sdr/201507251639_noaa15_sdr.jpg) The UK as seen from NOAA15 satellite, 25th Jul 2015\. Click to make bigger.

## How?

There are several American NOAA weather satellites in a polar orbit around Earth, each of which will pass the same point below every 12 hours or so. The satellites transmit pictures via FM radio at 137MHz. Using a DAB/FM/Freeview dongle, you can receive this signal on a computer running software-defined radio (SDR) software, and then decode it into a picture.

This isn't a tutorial. It's mainly meant as a reminder for how I did it on my Mac, so I can do it again once I've forgotten. I am by no means an expert.

### I Used

- A [Nooelec R820T USB Dongle](http://www.amazon.co.uk/gp/product/B009VBUYA0) (£8 on Amazon).
- [CubicSDR](http://cubicsdr.com) to tune and receive the signal.
- [Soundflower](https://rogueamoeba.com/freebies/soundflower/) to pipe audio out of CubicSDR into:
- [WXtoIMG](http://www.wxtoimg.com) which automatically records when the satellite should be overhead, then decodes and processes the image. It will also tell you when the satellites are overhead next.
- A 2 metre bit of wire jammed in the screwhole at the base of the tiny antenna (not the MCX connector), in a vain attempt to have something slightly more tuned to 137MHz. (Its wavelength's around 2m. I seem to remember physics suggesting that matching that might help. I had no intention of making a proper antenna, as that would take loads more time / space / effort than I could be bothered with.)

### Method

1. Go outside. You're trying to pick up a signal from space. You need as few things in the way as possible.
2. Open Soundflower
3. Open CubicSDR

  - Tune to the satellite's frequency (137.xxMHz) with a bandwidth of 36kHz.
  - Set audio output to Soundflower (2ch)

4. Open WXtoIMG

  - Make sure the soundcard is set to Soundflower (2ch) in _Options->Recording Options_
  - _File->Update Keplers_ if you've never done it before, so it knows where the satellites are
  - _File->Satellite Pass List_ will tell you when to expect the pass

5. Set WXtoIMG to auto record _File->Record_

6. Watch CubicSDR until you see the data lines fade in.

  - If the lines aren't centered, drag it in the top left window until they are. They will drift over time (I'm assuming this is due to the [Doppler effect](https://en.wikipedia.org/wiki/Doppler_effect)).

7. For me, an image faded out of the noise on WXtoIMG when the satellite was above 20 degrees, and looked best once it had reached the highest point and was on its way back down. (I'm assuming this is due to buildings/etc blocking signal).

8. If the signal was good enough, WXtoIMG will process it. _Enhancements->MSA Multispectral Analysis_ made it look like a colour photo.

Other things I've received with this setup, or would like to try

- ✔ FM Radio
- ✔ ADS-B - The signal planes broadcast to say where they are and how fast they're going

  - I used [dump1090](https://github.com/antirez/dump1090), a command-line program which generates a map webpage [![dump1090 webpage screenshot](http://mattg.co.uk/words/noaa_sdr/dump1090_sml.jpg)](http://mattg.co.uk/words/noaa_sdr/dump1090.jpg)

- ✔ London bus drivers and control centre which I found by accident (around 140MHz)
- Air Traffic Control
- The International Space Station or other satellites

## Relevant Links

## Comments

I don't have a comments box, so please [tweet me](http://twitter.com/unnamedculprit) if you've got something to say!

> Here's how I received a weather satellite image with my laptop & an £8 dongle! <http://t.co/JtHa8zjyN6> [pic.twitter.com/wpDNClmd5j](http://t.co/wpDNClmd5j)

> -- Matt Gray (@unnamedculprit) [July 25, 2015](https://twitter.com/unnamedculprit/status/625038992464027648)
