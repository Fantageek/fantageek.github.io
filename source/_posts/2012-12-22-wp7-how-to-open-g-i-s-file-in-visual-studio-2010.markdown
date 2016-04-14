---
author: onmyway133
comments: true
date: 2012-12-22 17:09:22+00:00
layout: post
slug: wp7-how-to-open-g-i-s-file-in-visual-studio-2010
title: WP7 How to open g.i.cs file in Visual Studio 2010
wordpress_id: 117
categories:
- Windows Phone
tags:
- file
- g.i.s
- open
- wp7
---

In Windows Phone 7 , the g.i.cs file is where UI objects are defined and fetched from XAML.

<!-- more -->

For example:

[sourcecode language="csharp" wraplines="false"]
this.LayoutRoot = ((System.Windows.Controls.Grid)(this.FindName("LayoutRoot")));
[/sourcecode]

You can't modify it, but looking at how it works may reveal some good things.

There are several ways to open it

1. Right click on _InitializeComponent()_ in page constructor and choose _Go to definitions_

2. Choose "show all files" icon in _Solution Explorer_, select _Obj -> Debug/Release_ to see g.i.cs files

**Reference:**

1. [http://stackoverflow.com/questions/13508236/how-to-open-g-i-cs-file-in-visual-studio-2010](http://stackoverflow.com/questions/13508236/how-to-open-g-i-cs-file-in-visual-studio-2010)
