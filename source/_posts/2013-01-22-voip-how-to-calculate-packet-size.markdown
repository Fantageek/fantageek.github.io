---
author: onmyway133
comments: true
date: 2013-01-22 16:51:03+00:00
layout: post
slug: voip-how-to-calculate-packet-size
title: VoIP how to calculate packet size
wordpress_id: 220
categories:
- Others
tags:
- packet size
- voip
---

As you have probably observed in your studies, there is a determined method for calculating VoIP packet sizes. The packet size depends on many different variables, so there is no great answer for an "average" packet size -- average depends on the environment. Just as an example, if you currently have VoIP running within a LAN and want to provision a new WAN so you can use VoIP to another site, knowing how big your VoIP packets are on the LAN won't help. See below for a VoIP packet size calculation for a typical LAN, which will get you started.




The general formula for VoIP packet size is this: Frame overhead + Encapsulation overhead + IP overhead + Voice payload.




Let's say the packet is going across our LAN, so right now the frame overhead is 18 Bytes, for Ethernet II. (This size would change later if the packet crosses a trunk with 802.1Q tagging or ISL encapsulation, or is destined for the WAN, where a different link layer framing will probably be in use.)




Encapsulation overhead would include things like IPSec tunnels for security. Suppose we are not encapsulating this voice packet, so there is no overhead here.




"IP overhead" has overhead occuring at layer 3 and above, so for SIP phones this means IP (20 Bytes), UDP (8 Bytes), and RTP (12 Bytes). This is a total of 40 Bytes of IP overhead.




Lastly, you must calculate the size of the actual voice payload. Suppose we use the G.711 codec, which gives us a codec bandwidth of 64kbps. Also suppose our phones have a packetization period of 20ms (meaning 20ms worth of voice goes into every packet). With these two numbers, we can figure out the size of the voice payload. Since one second of voice contains 64 kilobits of data ("64 kbps"), it is easy to calculate how many bits
  find the amount of Bytes per payload:




64000 bits * .02 seconds = 1280 bits of voice per payload  

1280 bits / 8 bits per byte = 160 Bytes of voice per payload




The total overhead is 58 Bytes (18 + 40)  

The total VoIP packet size is 218 Bytes (160 + 58 )




In the interest of full disclosure, it is easy to get a bit rate per second from here; just convert 218 Bytes into bits and multiply by the packetization rate (which is the inverse of your packetization period, in this case 50 packets per second). The bit rate for ONE stream of this voice is 87.2kbps... we hope the user isn't just talking to himself, so double that for an actual phone conversation.




There are lots of other little things, like VAD and various header compressions, that you may need to factor into these calculations as well. As you can see, any one of these many things being off will give you a different answer, so knowing how to go about the entire process is important.




**Reference**  

1. [www.techexams.net](www.techexams.net)
