---
author: onmyway133
comments: true
date: 2013-05-18 09:01:13+00:00
layout: post
slug: build-openssl-for-ios
title: Build openssl for iOS
wordpress_id: 316
categories:
- iOS
tags:
- ios
- openssl
---

[SSL](http://en.wikipedia.org/wiki/OpenSSL) is a must for those who want to support security over internet. The famous open source implementation of it, OpenSSL is written in C and thus very lightweight can can be wrapped in many platforms and languages.




When it comes to iOS, you have to build it yourself, because you won't find any iOS binaries on the [OpenSSL homepage](http://www.openssl.org/related/binaries.html)




Fortunately, Felix has provided us with the script that can be used right away. In brief, it does




1. Check if OpenSSL exits. If not, it start downloading  

2. Configure the appropriate flag  

3. Build


 for both armv6 and armv7, then merge them into one lib




You can find his script at [http://x2on.de/2010/12/16/tutorial-script-for-building-openssl-for-ios-iphoneipad/](http://x2on.de/2010/12/16/tutorial-script-for-building-openssl-for-ios-iphoneipad/)
