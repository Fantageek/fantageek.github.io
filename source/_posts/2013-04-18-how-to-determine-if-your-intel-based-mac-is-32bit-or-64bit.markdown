---
author: onmyway133
comments: true
date: 2013-04-18 03:42:49+00:00
layout: post
slug: how-to-determine-if-your-intel-based-mac-is-32bit-or-64bit
title: How to check if your Intel-based Mac is 32bit or 64bit
wordpress_id: 292
categories:
- Others
tags:
- 32bit
- 64bit
- intel
- mac osx
---

For those geeks, this is a very easy problem. But for newbies, checking whether your Mac is 32bit or 64bit can help in case of installing new softwares or packages. Here are some tips

**Way 1**
Launch System Profiler
In Contents section, choose Software
If "64-bit Kernel and Extensions" says "Yes", then you are 64bit, else you are 32bit

**Way 2**
Run Terminal with this
[code language="css"]
sysctl hw.cpu64bit_capable
[/code]
If the output is 1, then you are 64bit

**Way 3**
Run Terminal with this 
[code language="css"]
uname -a
[/code]
If it shows x86_64, then you are 64bit

Hope it help

