---
author: onmyway133
comments: true
date: 2014-10-08 16:19:05+00:00
layout: post
slug: cocoapods-gotcha
title: Understanding CocoaPods
wordpress_id: 1408
categories:
- iOS
---

Hi, in this post, I 'll share things I learn about Cocoapods



### What goes into your .gitignore if you're using Cocoapods



This question raises [debate on SO](http://stackoverflow.com/questions/9446644/what-goes-into-your-gitignore-if-youre-using-cocoapods), but I would recommend only track the Podfile and Podfile.lock. The Pods folder, .xcworkspace should go into .gitignore

Read more about the pros and cons [Not Cuckoo for CocoaPods](http://twobitlabs.com/2013/12/not-cuckoo-for-cocoapods/)



### pod install vs pod update



`pod install`



<blockquote>
  Every time the pod install command is run and it downloads and install new pods, it writes the version that it has installed, for each pods, in the Podfile.lock file. This file is intended to keep track of the installed version of each pod and to lock those versions
</blockquote>



`pod update`



<blockquote>
  When you run pod update SomePodName, CocoaPods will try to find an updated version of the pod SomePodName, without taking into account the version listed in Podfile.lock. It will update the pod to the latest version possible (as long as it matches the version restrictions in your Podfile).
</blockquote>



Read more [Detailed presentation of the commands](https://github.com/CocoaPods/CocoaPods/issues/760#issuecomment-46300500), [Cocoa​Pods on NSHipster](http://nshipster.com/cocoapods/)



### You can edit the source of the Pod



Yes, you can edit the source of the Pod, and your change will be overriden by the next `pod update`, unless you use `:path`. See this [reply by @orta](https://twitter.com/onmyway133/status/518376385434243073)



### Your .xcodeproj remains unchanged



Actually, it remains unchanged after the first `pod install`
As pointed out [Cocoapods](http://nshipster.com/cocoapods/)



<blockquote>
  CocoaPods will create a new Xcode project that creates static library targets for each dependency, and then links them all together into a libPods.a target. This static library becomes a dependency for your original application target. An xcworkspace file is created, and should be used from that point onward. This allows the original xcodeproj file to remain unchanged.
</blockquote>





### The sandbox is not in sync with the Podfile.lock



It's because of the [Manifest.lock](http://www.objc.io/issue-6/cocoapods-under-the-hood.html)



<blockquote>
  This is a copy of the Podfile.lock that gets created every time you run pod install. If you’ve ever seen the error The sandbox is not in sync with the Podfile.lock, it’s because this file is no longer the same as the Podfile.lock. Since the Pods directory is not always under version control, this is a way of making sure that developers update their pods before running, as otherwise the app would crash, or the build would fail in another, less visible, way.
</blockquote>





### Where are the Specs stored locally



The Specs repo is pulled into `./cocoapods/repos/master/Specs`



### References







  1. [February 2014 Tech Talk: CocoaPods](https://www.youtube.com/watch?v=wgq2nFJoab0)


  2. [UIKonf 2014 -- Orta Therox: CocoaPods](https://www.youtube.com/watch?v=TDAKOYQ7B-4)


  3. [MCE 2014: Orta Therox - CocoaPods and the road to 1.0](https://www.youtube.com/watch?v=xN5-nDZpuBU)


