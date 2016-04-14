---
author: onmyway133
comments: true
date: 2013-06-17 01:27:32+00:00
layout: post
slug: build-libyuv-for-ios
title: Build libyuv for iOS
wordpress_id: 375
categories:
- iOS
tags:
- armv7
- chromium
- depot tools
- ios
- libyuv
- yuv
---

Hi, in this tutorial, I'll show you how to build libyuv for iOS 6.1 targeting armv7




As some of you may know, libyuv is an open source library used for


 performing scaling and conversion related to YUV




**Depot tools**  

libyuv seems to be part of Chromium project. In order to interact with Chromium source, we must use depot tools. Depot tools contain many useful utilities, one of those is gclient, which we'll use to get libyuv




Create a new folder (mine is depot_tools). Cd to it. Then type this command to fetch depot tools  

[code language="objc"]<br /><br />
git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git<br /><br />
[/code]




You need to add depot tools to your PATH variable as well




**Get libyuv**  

Create a new folder (mine is libyuv). Cd to it. Then type these to get libyuv  

[code language="objc"]<br /><br />
gclient config http://libyuv.googlecode.com/svn/trunk<br /><br />
gclient sync<br /><br />
[/code]




**Build libyuv for armv7**  

Cd to libyuv/trunk. Type these  

[code language="objc"]<br /><br />
export GYP_DEFINES=&quot;target_arch=arm armv7=1&quot;<br /><br />
export DEVPATH=/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer<br /><br />
export CC=${DEVPATH}/usr/bin/gcc<br /><br />
export CXX=${DEVPATH}/usr/bin/g++<br /><br />
export RANLIB=${DEVPATH}/usr/bin/ranlib<br /><br />
export AR=${DEVPATH}/usr/bin/ar<br /><br />
export AS=${DEVPATH}/usr/bin/as<br /><br />
export LIBTOOL=${DEVPATH}/usr/bin/libtool<br /><br />
gclient runhooks<br /><br />
xcodebuild -project libyuv.xcodeproj -configuration Release -arch armv7 -sdk iphoneos6.1<br /><br />
[/code]  

Now you can find your lib in libyuv/trunk/xcodebuild/Release-iphoneos




**Reference**  

[https://code.google.com/p/libyuv/wiki/GettingStarted](https://code.google.com/p/libyuv/wiki/GettingStarted)
