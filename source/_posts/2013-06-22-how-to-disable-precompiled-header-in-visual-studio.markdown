---
author: onmyway133
comments: true
date: 2013-06-22 04:42:44+00:00
layout: post
slug: how-to-disable-precompiled-header-in-visual-studio
title: How to disable Precompiled header in Visual Studio ?
wordpress_id: 429
categories:
- Others
- Windows 8
tags:
- C
- disable
- header
- pch
- precompile
- precompiled
- project
- stdafx
- visual studio
---

As you may know, precompiled header (pch.h, stdafx.h) is header file that is compiled into an intermediate form that is faster to process for the compiler. Each subsequent compilation is faster because the stable code does not need to be recompiled

To disable Precompiled header
1. Right click on your project, choose Properties
2. Configuration Propeties -> C/C++ -> Precompiled Headers
3. In the right panel, select Not Using Precompiled Headers

[![precompiled_header](http://www.fantageek.com/wp-content/uploads/2013/06/precompiled_header.png)](http://www.fantageek.com/wp-content/uploads/2013/06/precompiled_header.png)
