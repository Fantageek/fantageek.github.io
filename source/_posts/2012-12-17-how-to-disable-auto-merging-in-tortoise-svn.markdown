---
author: onmyway133
comments: true
date: 2012-12-17 02:37:22+00:00
layout: post
slug: how-to-disable-auto-merging-in-tortoise-svn
title: How to disable auto merging in Tortoise SVN
wordpress_id: 89
categories:
- Others
tags:
- auto
- disable
- merge
- svn
- tortoise
---

There is time we don't want the auto merging in TortoiseSVN. Here is the trick




<!-- more -->




In TortoiseSVN->Settings->Subversion configuration file, click on the edit button. Change the`[helpers]` section by adding



    
    <code>diff-cmd = "C:\\false.bat"
    diff3-cmd = "C:\\false.bat" 
    </code>




(note the double backslash) Create the file `C:\false.bat` which contains two lines



    
    <code>@type %9
    @exit 1 </code>




This effectively makes auto-merge fail every time, forcing the file into conflict.




The reason for the curious 'type %9' line is that the diff3-cmd sends the merged output to stdout. Subversion then takes this and overwrites your local  file with the merge results. Adding this line avoids getting an empty local file.




**Reference**  

1. [http://tortoisesvn.net/faq.html#noautomerge](http://tortoisesvn.net/faq.html#noautomerge)  

2. [http://stackoverflow.com/questions/1273113/update-from-svn-without-merging-automatically](http://stackoverflow.com/questions/1273113/update-from-svn-without-merging-automatically)
