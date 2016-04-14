---
author: onmyway133
comments: true
date: 2013-05-18 11:21:31+00:00
layout: post
slug: install-pkg-config-for-mac-osx
title: Install pkg-config for Mac OSX
wordpress_id: 318
categories:
- iOS
tags:
- config
- mac osx
- pkg
- pkg-config
---

For those who don't know about pkg-config, it is a tool that help we compile applications and libraries by inserting the correct compiler options.




Now, how can we install pkg-config for Mac OSX ?




**Get MacPort**  

For no headache, install MacPort. It helps a lot when install open source software on the Mac.  

Install here [http://www.macports.org/install.php](http://www.macports.org/install.php). You should choose "pkg" installer for quick use. Remember to select the right Mac version




**Install pkg-config**  

Run Terminal. Cd to where you want to install pkg-config, and enter this




[code language="css"]  

sudo port install pkgconfig  

[/code]





**Oops**, if you receive this error




[code language="css"]  

port command not found  

[/code]




It is because **port** is install in _/opt/local/bin_  by default. You should add _/opt/local/bin_ to your PATH




Run Terminal, and enter this for temporary PATH append




[code language="css"]  

export PATH=$PATH:/opt/local/bin  

[/code]




Fore more information about pkg-config, visit  

1. [Guide to pkg-config](http://people.freedesktop.org/~dbn/pkg-config-guide.html)  

2. [pkg-config](http://www.freedesktop.org/wiki/Software/pkg-config/)
