---
author: onmyway133
comments: true
date: 2013-08-31 03:47:09+00:00
layout: post
slug: debug-cc-in-linux-with-nemiver
title: Debug C/C++ in Linux with Nemiver
wordpress_id: 509
categories:
- Others
tags:
- C
- debug
- gnome
- gui
- linux
- nemiver
- ubuntu
---

Debugging C/C++ in Linux with gdb can be frustrating, even with an experienced developers. Come around Stack Overflow and we can see many threads asking about a GUI debugging tool. Many answers are raised : ddd, Eclipse CDT, KDBG, ... but I personally think Nemiver is the best because of its simplicity and many features supported.

Nemiver is for GNOME environment only

**Installing Nemiver**

We can install Nemiver in 2 ways

In Ubuntu, we can specify this command


<blockquote>sudo apt-get install nemiver</blockquote>


Install from source : Go to [https://projects.gnome.org/nemiver/](https://projects.gnome.org/nemiver/) and follow the instructions there.

**Debugging with Nemiver**

I will show you quick tutorial on how to use Nemiver to debug a C program, let's take rtpproxy for example.

Run Nemiver with administration role, or you can enter these command in Terminal


<blockquote>sudo nemiver</blockquote>


Go to File -> Load Executable to load the program

[![nemiver (1)](http://www.fantageek.com/wp-content/uploads/2013/08/nemiver-1.png)](http://www.fantageek.com/wp-content/uploads/2013/08/nemiver-1.png)

We can specify the arguments to our program

[![nemiver (2)](http://www.fantageek.com/wp-content/uploads/2013/08/nemiver-2.png)](http://www.fantageek.com/wp-content/uploads/2013/08/nemiver-2.png)

Nemiver will by default break in the main(). We can File -> Open Source File, Nemiver will show the source files associated with this program, we can then choose the files and set breakpoints into them.

[![nemiver (4)](http://www.fantageek.com/wp-content/uploads/2013/08/nemiver-4.png)](http://www.fantageek.com/wp-content/uploads/2013/08/nemiver-4.png)

Nemiver then stops at the breakpoints and show us many information about thread, variable and logs

[![nemiver (3)](http://www.fantageek.com/wp-content/uploads/2013/08/nemiver-3.png)](http://www.fantageek.com/wp-content/uploads/2013/08/nemiver-3.png)

**Reference**

1. [projects.gnome.org/nemiver](https://projects.gnome.org/nemiver/)

2. [Debugging with Nemiver ](http://fosswire.com/post/2008/04/debugging-with-nemiver/)

3. [Which is the best Linux C/C++ debugger (or front-end to gdb) to help teaching programming](http://stackoverflow.com/questions/79537/which-is-the-best-linux-c-c-debugger-or-front-end-to-gdb-to-help-teaching-pr)

4. [GUI debugger for c++ on linux](http://stackoverflow.com/questions/5344764/gui-debugger-for-c-on-linux)

5. [Can I use Eclipse CDT to debug a prebuilt C++ executable?](http://stackoverflow.com/questions/15490468/can-i-use-eclipse-cdt-to-debug-a-prebuilt-c-executable?lq=1)

6. [Nemiver lecture](https://secweb.cs.odu.edu/~zeil/cs252/website/Lectures/progdevx/pages/nemiver.html)
