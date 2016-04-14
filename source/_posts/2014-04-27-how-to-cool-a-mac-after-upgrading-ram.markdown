---
author: akhoi90
comments: true
date: 2014-04-27 09:18:20+00:00
layout: post
slug: how-to-cool-a-mac-after-upgrading-ram
title: How to cool a Mac after upgrading RAM
wordpress_id: 769
categories:
- Miscellaneous
---

My Mac is 13in Mid-2012 MacBook Pro with original 4GB of RAM.




After upgrade it to the 8GB RAM Crucial kit, everything's fine except the computer did crash then restart after long hours work, especially while I'm watching video.




At first I thought it was an RAM uncompatibility and downloaded some tools to check it out. Tool I used was the famous Mac memtest [http://osxdaily.com/2011/05/03/memtest-mac-ram-test/](http://osxdaily.com/2011/05/03/memtest-mac-ram-test/)




Just follow the guide and it led to the end of the test, my Mac crashed and restarted again, althought the statuses of passed test sections on the console screen is all OK!  

Re-check again at Crucial home page, after a system scanning, the result was a Guaranteed-compatible upgrade.  

Re-run the memtest tool again, it crash right away.




So at end I came up with the solution that was check the machine temperature. Seems it was very hot at the graphics stuff, which never made the computer crash or restart before. I did a search and used the smcFanControl [http://www.eidac.de/?p=243](http://www.eidac.de/?p=243) to tune the fan speed up to 6000rpm while runing memtest!




The fan 's sound was louder, but I was patiently waiting for some good


 result which might help me stay out of RAM error! It finally got the test's result. No crash/restart with   all tests was OK.




I thought I got some clues from that. The reason was my computer's getting hot after the RAM upgrade. So I kept monitoring my machine's temp. It was so high while watching video, ~80 C, but the fan did not rise the speed up. I must use the smcFanControl to set the speed at 3000rpm to keep the GPU temperature at ~60. (Normal at 2000rpm).




At the speed of 3000rpm, the fan retains quiet and the computer is acceptable warm. Need to search if keeping this speed in every normal start-ups may harm the computer, but with what I am opening now is Chrome (with playing Youtube and 5 other tabs), Eclipse, Skype and few other programs, I think it is totally a good try!




Phew!




P/S: The fan speed number we set in smcFanControl is only the minimum speed we allow the fan to run. It's not a static value.
