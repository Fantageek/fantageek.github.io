---
author: onmyway133
comments: true
date: 2014-09-07 04:33:40+00:00
layout: post
slug: avfoundation-vs-mediaplayer
title: AVFoundation vs MediaPlayer
wordpress_id: 1364
categories:
- iOS
tags:
- avfoundation
- avkit
- avplayer
- mediaplayer
- mpmovieplayercontroller
---

No offsense, but I think most developer are just library users now. Some genius have made great libraries for us to use. It 's just the usage difficulty that matters. So if we don't know how to use it, it's a shame. We 're engineers, right?

Today talk is about MPMoviePlayerController and AVPlayer.

<!-- more -->


### MPMoviePlayerController vs AVPlayer


At first, I use MPMoviePlayerController because it is very simple, in fact, it is a wrapper around AVPlayer. It offers a lot of useful notifications and properties.

But when my requirements change, a lot more features are needed, I need to change to AVPlayer to have more control. And that is the wisest decision I've ever made

You should use AVPlayer as soon as possible. Using it cost you just a little longer time than MPMoviePlayerController, but you have a lot of control.



### Custom controls


When building your own player, the built in controls of MPMoviePlayerController may not satisfy your need. I see many questions on SO are about custom controls.


#### MPMoviePlayerController


You have to set controlStyle to MPMovieControlStyleNone, set up Timer because currentPlaybackTime is not KVO compliance


#### AVPlayer


AVPlayer has no built in controls, but it has addPeriodicTimeObserverForInterval:queue:usingBlock: that makes handling the current time easily. The nicer thing about periodTimeObserver is that "The block is also invoked whenever time jumps and whenever playback starts or stops"




### Notification




#### MPMoviePlayerController


It has a lot of useful notifications, like MPMoviePlayerNowPlayingMovieDidChangeNotification, MPMoviePlayerLoadStateDidChangeNotification, MPMovieDurationAvailableNotification, ...


#### AVPlayer


The AVPlayerItem has some notifications AVPlayerItemDidPlayToEndTimeNotification, AVPlayerItemPlaybackStalledNotification, ...
If you want to have those same notifications as MPMoviePlayerController, you have to KVO some properties of AVPlayer like currentItem, currentTime, duration, ... You have to read documents to make sure which property is KVO compliance



### Seek

### 


#### MPMoviePlayerController


You can change the currentPlaybackTime to seek, but it results in jumping, because of efficiency and buffer status. I have to manually disable slider during seeking


#### AVPlayer


AVPlayer has this **seekToTime:toleranceBefore:toleranceAfter:completionHandler:** which allows you to specify the tolerance. Very nice



### Subtitle




#### MPMoviePlayerController


I have to schedule a timer that "wake" and "sleep" at the beginning and end time of a certain subtitle marker, respectively.


#### AVPlayer


AVPlayer has this **addBoundaryTimeObserverForTimes:queue:usingBlock:** that perfectly suits my need. I setup 2 boundary time observers, 1 for the beginning times, 1 for the end times. The beginning times is an array containing all beginning timestamps that a subtitle marker appears.



### Time scale


AVPlayer uses CMTime that offers timescale, which is a more comfortable way to specify more accurate time



### Volume


AVPlayer has **volume** property (relative to system volume) that allows me to programmatically changes the player volume



### Playable duration




#### MPMoviePlayerController


It has playableDuration property


#### AVPlayer


You have to compute yourself. See [http://stackoverflow.com/questions/6815316/how-can-i-get-the-playable-duration-of-avplayer](http://stackoverflow.com/a/12260669/1418457)



### Full screen


MPMoviePlayerController achieves full screen mode by creating another UIWindow, you learn from this to support full screen using AVPlayer, too



### Movie Source Type




#### MPMoviePlayerController


It has movieSourceType that provides clues to the playback system, hence improving load time

" If you know the source type of the movie, setting the value of this property before playback begins can improve the load times for the movie content."


#### AVPlayer


Read **Handling Different Types of Asset** on [AV Foundation Programming Guide](https://developer.apple.com/library/ios/documentation/AudioVideo/Conceptual/AVFoundationPG/Articles/02_Playback.html)

"To create and prepare an HTTP live stream for playback. Initialize an instance of AVPlayerItem using the URL. (You cannot directly create an AVAsset instance to represent the media in an HTTP Live Stream.)"



### AVAsset


AVPlayer allows you to access AVAsset, which provides you more information about the playback and load state



### Allow a range within a video to be playable


AVPlayerItem has **forwardPlaybackEndTime** and **reversePlaybackEndTime** that is used to specify a range that the player can play. When forwardPlaybackEndTime is specified and the playhead passes this points, AVPlayerItem will trigger AVPlayerItemDidPlayToEndTimeNotification

But it doesn't work in my case [forwardPlaybackEndTime does not work](http://stackoverflow.com/questions/25717819/forwardplaybackendtime-does-not-work)



### You can have many instances of AVPlayer playing at the same time





### Reference


1. [AV Foundation Programming Guide](https://developer.apple.com/library/mac/documentation/AudioVideo/Conceptual/AVFoundationPG/Articles/02_Playback.html)
2. [WWDC 2011 Session 405 Exploring AV Foundation](https://developer.apple.com/videos/wwdc/2011/)
3. [WWDC 2011 Session 415 Working With Media In AV Foundation](https://developer.apple.com/videos/wwdc/2011/)
4. [WWDC 2014 Session 503 Mastering Modern Media Playback](https://developer.apple.com/videos/wwdc/2014/)
5. [WWDC 2011 Session 406 AirPlay and External Displays in iOS apps](https://developer.apple.com/videos/wwdc/2011/)
6. [AVPlayerDemo](https://developer.apple.com/library/ios/samplecode/AVPlayerDemo/Introduction/Intro.html)
7. [VKVideoPlayer](https://github.com/viki-org/VKVideoPlayer)
8. [ALMoviePlayerController](https://github.com/alobi/ALMoviePlayerController)
9. [AV Foundation Playing Videos](http://mobiliture.com/ios/tutorials/av-foundation-playing-videos/)
10. [AVPlayer and MPMoviePlayerController differences](http://stackoverflow.com/questions/8146942/avplayer-and-mpmovieplayercontroller-differences)
11. [MPMoviePlayer Tips](http://jomnius.blogspot.com/2011/04/mpmovieplayer-tips.html)
