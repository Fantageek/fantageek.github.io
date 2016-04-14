---
author: onmyway133
comments: true
date: 2013-09-22 09:13:18+00:00
layout: post
slug: how-to-see-ios-simulator-app-directory-in-finder
title: How to see iOS simulator app directory in Finder
wordpress_id: 546
categories:
- iOS
---

For those of you who want to see where iOS simulator puts your apps' directories and files, here is the guide




It is in




<blockquote>~/Library/Application Support/iPhone simulator/6.1/Applications/AECCA0A4-41B7-45B7-A277-3D517ADEA34C/
> 
> 
</blockquote>




Here  

_6.1_ is your iOS simulator version  

_AECCA0A4-41B7-45B7-A277-3D517ADEA34C_ is the sandbox folder. You can sort this by date to easily find your app




**Can't see Library folder**  

Library folder is in /Users/username  

If you can't see it, you need to disable its Hidden flag. Open Terminal and type




<blockquote>chflags nohidden ~/Library/
> 
> 
</blockquote>




**Quick way to open Library


 folder in Terminal**




<blockquote>cd ~/Library  

open .
> 
> 
</blockquote>
