---
author: onmyway133
comments: true
date: 2013-09-06 15:11:36+00:00
layout: post
slug: how-to-enable-and-disable-hyper-v-in-windows-8
title: How to enable and disable Hyper-V in Windows 8
wordpress_id: 526
categories:
- Windows 8
tags:
- disable
- enable
- hyper-v
- vmware
- windows 8
---

If, for some reasons (like installing   VMWare), you want to disable Hyper-V, then here are the commands.




Remember to open Command Prompt as Administrator




How to disable




<blockquote>
dism.exe /Online /Disable-Feature:Microsoft-Hyper-V /All

> 
> </blockquote>




How to enable




<blockquote>
dism.exe /Online /Enable-Feature:Microsoft-Hyper-V /All

> 
> </blockquote>
