---
author: onmyway133
comments: true
date: 2013-01-22 16:45:56+00:00
layout: post
slug: voip-sample-rate-bit-rate
title: VoIP sample rate, bit rate & bit depth
wordpress_id: 212
categories:
- Others
tags:
- bit rate
- sample rate
- voip
---

In order to understand why sample rate and bitrate came about, you need to understand a little bit about how all things digital work. Digital works like a ticking second hand. Whereas time and the world as we know it seems continuous and seamless, digital breaks things like time up into little measurements. When we’re talking about measurements of time, we are talking about ticks just like the ticking of a second hand. If anything is to happen digitally, it has to happen on a tick. The rate of these ticks is measured in hertz. A 2ghz computer has 2,000,000,000,000 ticks a second. That’s a lot of ticks.




**What is a Sample Rate?**  

Sample rate is



 is represented by t.




![sampled_signal](http://www.fantageek.com/wp-content/uploads/2013/01/sampled_signal.png)   [![analog_signal](http://www.fantageek.com/wp-content/uploads/2013/01/analog_signal.png)](http://www.fantageek.com/212/voip-sample-rate-bit-rate/analog_signal/#main)




If we start to split up the smooth analog signal into digital chunks, you will start to see something like the second image. With each tick of the clock we measure what’s happening at each tick ‘t’. That measurement is documented, represented by the balls on the signal graph. How often we do these measurements is called the sample rate. The higher the rate, the closer you’ll get to the smoothness of the first image. Measuring things in bits like this is called quantizing and the measurements are called samples (hence sample rate).




The sample rate can be thought of as how often or how much the sound is described.




CD quality audio has 44,100 of these measurements a second. That’s called 44.1 kilohertz (khz)




**What is a Bit Rate?**  

With what we just learned in mind, consider that in order for these ticks to make any sense at all they need to actually be measuring something. What is it that we’re measuring? Volume. Volume is represented by the height of the balls in the image. With each tick a new measurement of the volume is made. How do we describe the volume? Is it a range from 0 to 100? 0 to 2000? 0 to 1? The range of volumes that can be described is the bit rate. Now, in each of these examples, 0 means totally silent and 100, 2000, or 1, respectively, means as-loud-as-it-can-get. So the only difference between each of these ranges is not how loud the sound can be but how many different volumes can be described. We only have two choices for ’0 to 1′, ie. is there a sound or not? But from 0 to 2000 we can have half volume (1000), quarter volume (500), or even somewhere in between (829). The higher the bitrate, the more accurately we can communicate exactly how loud the volume of the ‘real’ sound we want to describe is.




The bit rate can be thought of as how well the sound is described.




CD quality   audio has 65,536 volumes to choose from for every sample that’s measured. That’s called 16-bit audio (because 2 to the 16th power is 65,536).




**What is bit depth**  

In digital audio, bit depth describes the number of bits of information recorded for each sample.Bit depth directly corresponds to the resolution of each sample in a set of digital audio data.  

Common examples of bit depth include CD quality audio, which is recorded at 16 bits, and DVD-Audio, which can support up to 24-bit audio.




**Reference**  

1. [http://thestereobus.com](http://thestereobus.com/2008/01/12/sample-rate-and-bitrate-the-guts-of-digital-audio/)  

2. [http://en.wikipedia.org](http://en.wikipedia.org/wiki/Audio_bit_depth)
