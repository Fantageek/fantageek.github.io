---
author: onmyway133
comments: true
date: 2013-06-23 03:18:18+00:00
layout: post
slug: build-openssl-for-windows-8-desktop
title: Build OpenSSL for Windows 8 desktop
wordpress_id: 441
categories:
- Windows 8
tags:
- build
- command
- compile
- openssl
- prompt
- windows 8
---

This tutorial will show you how to  build OpenSSL for Windows 8 desktop




First, make sure you 've already downloaded [OpenSSL source code](http://www.openssl.org/source/). You also need [ActivePerl](http://www.activestate.com/activeperl/downloads) to process the Configure file




**Build 32bit static library**  

Open VS2012 x86 Native Tool Command Prompt  

Cd to your OpenSSL directory  

Run the following commands




[code language="css"]  

perl Configure VC-WIN32 no-asm no-shared  

ms\do_ms  

nmake -f ms\nt.mak  

nmake -f ms\nt.mak install  

[/code]




**Build 64bit static library**  

Open VS2012 x64 Native Tool Command Prompt  

Cd to your OpenSSL directory  

Run the following commands




[code language="css"]  

perl Configure VC-WIN64A no-asm no-shared  

ms\do_win64a  

nmake -f ms\nt.mak  

nmake -f ms\nt.mak install  

[/code]




You 'll find your libraries at out32 folder. These are libeay32.lib (AKA libcrypto.a) and ssleay32 (AKA libssl.a), which can be used in your application




**Note**  

1. If you want to build dynamic libraries, use the following




[code language="css"]  

nmake -f ms\ntdll.mak  

nmake -f ms\ntdll.mak install  

[/code]




2. If you want to build a Debug version, simply add "debug" to the perl command




[code language="css"]  

perl Configure debug VC-WIN32 no-asm no-shared  

[/code]
