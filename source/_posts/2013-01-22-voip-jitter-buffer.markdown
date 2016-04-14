---
author: onmyway133
comments: true
date: 2013-01-22 16:36:36+00:00
layout: post
slug: voip-jitter-buffer
title: VoIP Jitter buffer
wordpress_id: 208
categories:
- Others
tags:
- buffer
- jitter
- voip
---

A jitter buffer temporarily stores arriving packets in order to minimize delay variations. If packets arrive too late then they are discarded. A jitter buffer may be mis-configured and be either too large or too small.  

<!-- more -->




**Impact**




If a jitter buffer is too small then an excessive number of packets may be discarded, which can lead to call quality degradation. 




Lower settings cause less delay in the meeting, but meetings with lower settings are more susceptible to jitter effects caused by network congestion. Less data is buffered, increasing the likelihood that delayed or lost packets will produce a jitter effect in the media stream.




If a jitter buffer is too large then the additional delay can lead to conversational difficulty.




Higher settings are more effective at reducing jitter effects. With higher settings, more data is buffered, which allows more time for delayed packets to arrive at the client. However, higher settings also result in more delay (or latency) in the meeting. A user who is speaking will not be heard immediately by the other meeting participants. The delay in the meeting increases with the amount of time that data is held in the buffer.




**Resolution**




A typical jitter buffer configuration is 30mS to 50mS in size. In the case of   an adaptive jitter buffer then the maximum size may be set to 100-200mS. Note that if the jitter buffer size exceeds 100mS then the additional delay introduced can lead to conversational difficulty.




**Reference**  

1. [lib.boulder.ibm.com/infocenter/sametime](http://publib.boulder.ibm.com/infocenter/sametime/v7r5m1/index.jsp?topic=/com.ibm.help.sametime.admin.doc/st_adm_avserv_audframepacket_t.html)  

2. [http://www.voiptroubleshooter.com](http://www.voiptroubleshooter.com/problems/jitterbuffer.html)
