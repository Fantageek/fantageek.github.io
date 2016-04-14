---
author: onmyway133
comments: true
date: 2013-09-10 08:00:32+00:00
layout: post
slug: build-opus-for-ios
title: Build Opus for iOS
wordpress_id: 533
categories:
- iOS
tags:
- armv7
- autoreconf
- build
- ios
- opus
---

I've followed [https://github.com/zimuliu/opus-ios-build](https://github.com/zimuliu/opus-ios-build) but I can't seem to make it working. I need Opus built for armv7 urgently and I don't have time to look through the scripts. This is the quick (but not so dirty) solution :D
I will make a full tutorial if I have time

Follow these steps
1. Download Opus at [http://www.opus-codec.org/downloads/](http://www.opus-codec.org/downloads/)
At this time, the newest version is 1.0.3, so look for **Source code: opus-1.0.3.tar.gz** and download it

2. Download scripts at [https://github.com/zimuliu/opus-ios-build](https://github.com/zimuliu/opus-ios-build). The scripts will be in **opus-ios-build-master**

3. Rename **opus-1.0.3** to **opus-armv7** and place it in **opus-ios-build-master**

4. Cd to **opus-armv7** and enter the following commands in Terminal
`./configure -host=arm-apple-darwin`

5. Edit config.sh
`OPUS_DIR="1.0.3"
ARCHS="armv7"`

6. Edit build.sh
Comment out CXXCPP and CPP
`# export CXXCPP="$PLATFORM/Developer/usr/bin/llvm-cpp"
# export CPP="$CXXCPP"`

Replace autogen with
`autoreconf --force --install --verbose`

7. Enter Terminal and run
`sh build.sh`

There you go, libopus.a will be in the armv7 dist folder

Solution 2
1. Prepare 3 directory opus, opus-armv7, opus-armv7s
2. replace autogen, comment CPP
3. run config.sh, build.sh, combine.sh

Solution 3
1. Follow build.sh
2. lipo
