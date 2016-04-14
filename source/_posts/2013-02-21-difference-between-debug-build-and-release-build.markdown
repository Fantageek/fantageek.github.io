---
author: onmyway133
comments: true
date: 2013-02-21 04:01:21+00:00
layout: post
slug: difference-between-debug-build-and-release-build
title: Difference between Debug build and Release build
wordpress_id: 233
categories:
- Others
tags:
- build
- configuration
- debug
- release
---

### 1. Optimization




This is the main reason you package your final software with “Release” build. While debugging there are debugging symbols, extra lines of code, and the IDE’s book keeping attached with your software. So naturally your debug build is slower and heavier by several orders. For instance my debug executable was 6.15 MB while release executable is only 2.74 MB. See how it has doubled.  

In fact there is an option in IDE to turn off optimization, as optimization by compiler, modifies code in unpredictable manner, something which can make debugging hard or even impossible.




[![Debug configuration has Optimization turned off](http://vinayakgarg.files.wordpress.com/2012/03/debug_opt1.png?w=640&h=445)](http://vinayakgarg.files.wordpress.com/2012/03/debug_opt1.png)




### 




### 2. The protective cover is gone




If you are facing the trouble, “software crashes in release, while in debug it was running fine”, your trouble spot is most probably this one.  

In debug mode, the compiler is gentle on you and provides a protection against your um.. erroneous code, by






  * allocating extra memory before and after, each block of storage. And this extra space is initialized with values like 0 for integers. Thus your attempts to access beyond array size won’t let you corrupt some other data structure.


  * it also initializes variables to some safe value. Like the pointers are initialized with NULL value, or some ridiculous value that will cause segfault, if you try to access it. Thus the problem of wild pointers would not appear in debug, but cause troubles in release build.




So if your release build is crashing, you should first check for out of bound access and uninitialized variables.




### 3. Use Release Library for Release build and Debug Library


 for Debug build




Well, this is where I faulted. I was using debug library, of framework my program was dependent upon for Linking. While configuring project settings, I made these settings default, and thus they were used for both Debug and Release build. As long as I was debugging, everything went fine, but my release build, well crashed…




Most third party libraries support different builds, like Release or Debug, Static or Dynamic and so on. So while, you are developing code, and will be debugging actively, it is appropriate to have debug libraries linked to your project. This will aid debugging as, the library will provide much more detailed messages for warnings and errors.  

But in case of release, only use Release version of library for linking. Release build of libraries are optimized, and fail silently in case of error (you don’t want to show your users a scary message, right?!).




**Reference**  

1. [http://vinayakgarg.wordpress.com](http://vinayakgarg.wordpress.com/2012/03/31/difference-between-debug-build-and-release-build/)
