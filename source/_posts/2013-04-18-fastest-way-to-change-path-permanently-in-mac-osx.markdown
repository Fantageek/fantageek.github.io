---
author: onmyway133
comments: true
date: 2013-04-18 04:52:29+00:00
layout: post
slug: fastest-way-to-change-path-permanently-in-mac-osx
title: Fastest way to change PATH permanently in Mac OSX
wordpress_id: 295
categories:
- Others
tags:
- best
- editor
- free
- mac
- mac osx
- text
---

The PATH variables list the directories containing the executables that the shell looks for. To see what is in your PATH, you can

[code language="css"]
echo $PATH
[/code]

To add another directory to your PATH, you can

[code language="css"]
export PATH=$PATH:/Users/me/mydir
[/code]

Or

[code language="css"]
export PATH=/Users/me/mydir:$PATH
[/code]

If you want to prepend your directory

But this is just a temporary solution. Your PATH will return to its previous state when you restart your Terminal. So the fastest, safest way is to use [TextWrangler](http://www.barebones.com/products/textwrangler/). It is free and cool

Cd to _/etc_. Open _paths_ with TextWrangler. _paths_ is the file that contains the PATH variables, and we will change it

[code language="css"]
open -a TextWrangler paths
[/code]

Now change it with whatever your like, with caution of cause :). Then type your admin password just before you save it
