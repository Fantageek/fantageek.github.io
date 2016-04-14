---
author: onmyway133
comments: true
date: 2013-05-21 16:38:24+00:00
layout: post
slug: build-opencore-amr-for-windows
title: Build opencore-amr for Windows
wordpress_id: 345
categories:
- Others
tags:
- amr
- mingw
- opencore
- windows
---

[AMR (Apdative multi rate)](https://en.wikipedia.org/wiki/Adaptive_Multi-Rate_audio_codec) is a codec optimized for compression audio data. It has 2 versions, narrow band and wide band.  

Opencore-amr is an implementation of it, which is extracted from Android source.




This tutorial will guide you how to build opencore-amr for Windows, with the help of minGW




**Install minGW first**  

Download [mingw-get-inst](http://sourceforge.net/projects/mingw/files/) for a quick GUI installation. Choose the folder where you want to install minGW (C:\minGW for example). Remember to check for _msys_ addon (_msys_ is an optimized shell for minGW operation)




After installation is completed, add _C:\minGW\bin_ to your PATH  

More information about minGW, visit [http://www.mingw.org/wiki/Getting_Started](http://www.mingw.org/wiki/Getting_Started)




**Download opencore-amr**  

Download here [http://sourceforge.net/projects/opencore-amr/files/opencore-amr/](http://sourceforge.net/projects/opencore-amr/files/opencore-amr/)  

The latest version is 0.1.3




**Build opencore-amr**  

Open msys  

Cd to where you put opencore-amr  

Run




[code language="css"]  

./configure  

[/code]




Then, run




[code language="css"]  

make  

[/code]




Now you should see your lib   _libopencore-amrnb.a_ in _opencore-amr-0.1.3\amrnb\.libs_ and your include file in _opencore-amr-0.1.3\amrnb_




The important include files are _interf_enc.h_ and _interf_dec.h_




You can then use opencore-arm in your favorite Visual Studio. The archives of static libraries generated with MinGW are generally compatible with Visual C++ compiler/linker.




If you still encounter errors, maybe you need to add some minGW 's libraries. Follow this [answer on SO](http://stackoverflow.com/questions/2096519/from-mingw-static-library-a-to-visual-studio-static-library-lib).




If you still can't build it, you can use this already built opencore-amr from [Suresh](http://www.codeproject.com/Articles/332109/AMR-Audio-Encoding)
