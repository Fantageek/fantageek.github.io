---
author: onmyway133
comments: true
date: 2013-11-09 02:26:45+00:00
layout: post
slug: install-both-xcode-4-and-xcode-5
title: Install both Xcode 4 and Xcode 5
wordpress_id: 611
categories:
- iOS
---

This is all I know when installing both Xcode 4 and Xcode 5. Hope it helps someone


## Install Xcode 4


Go to [https://developer.apple.com/downloads/](https://developer.apple.com/downloads/) and download Xcode 4.6.3
You must login to download


## Install Xcode 5




### Rename the old one


Since Xcode install itself as an independent package, we should rename the old one to differentiate from the new one.
Go to /Applications and rename XCode.app to XCode4.app
<!-- more -->


### Get Xcode 5


Go to Mac App Store or [https://developer.apple.com/downloads/](https://developer.apple.com/downloads/) to download Xcode 5
Install and you should have 2 Xcode in your /Applications folder
Xcode4.app is for Xcode 4 and Xcode.app is for Xcode 5

If you're curious, right click on Xcode4.app or Xcode.app and select Show Package Contents to see what are inside the package


### Check the current Xcode


Go to Terminal and type

[code language="objc"]
xcode-select -print-path
[/code]

to see the current selected Xcode. If you see

[code language="objc"]
/Applications/Xcode.app/Contents/Developer
[/code]

then you are using Xcode 5. If not, switch to it

[code language="objc"]
xcode-select -switch /Applications/Xcode.app/Contents/Developer
[/code]


### Use the old SDK (iOS 5.1 SDK, iOS 6.1 SDK, ...)


Copy (or make alias) the SDKs from Xcode 4 to Xcode 5

Go to

[code language="objc"]
/Applications/Xcode4.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/
[/code]

and copy (or copy alias) iPhone5.1.sdk, iPhoneOS6.1.sdk, ... to

[code language="objc"]
/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/
[/code]

To check, open your Xcode projects (that targets iOS version older than 7), look for the iOS Deployment Target under Project -> Info and check if you can select 5.1, 6.1, ...

[![ios deployment target check](http://www.fantageek.com/wp-content/uploads/2013/11/Screen-Shot-2013-11-09-at-9.53.17-AM.png)](http://www.fantageek.com/wp-content/uploads/2013/11/Screen-Shot-2013-11-09-at-9.53.17-AM.png)


### Use the old Simulator (iPhone 5.1 Simulator, iPhone 6.1 Simulator, ...)


Go to Xcode Preference -> Download and install. If you don't want to download, simply use the old ones

Go to

[code language="objc"]
/Applications/Xcode4.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs
[/code]

and copy (copy alias does not work!!) iPhoneSimulator5.1.sdk, iPhoneSimulator6.1.sdk, ... to

[code language="objc"]
/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs
[/code]

To check, look for the Simulator list

[![iphone simulator list check](http://www.fantageek.com/wp-content/uploads/2013/11/Screen-Shot-2013-11-09-at-9.54.55-AM.png)](http://www.fantageek.com/wp-content/uploads/2013/11/Screen-Shot-2013-11-09-at-9.54.55-AM.png)


### Use the old Command Line Tools


I recommend installing new Command Line Tools. Simply go to Xcode Preference -> Download
