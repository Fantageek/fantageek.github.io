---
author: onmyway133
comments: true
date: 2013-02-05 09:09:24+00:00
layout: post
slug: tortoisesvn-repository-has-not-been-enabled-to-accept-revision-propchanges
title: TortoiseSVN Repository has not been enabled to accept revision propchanges
wordpress_id: 230
categories:
- Others
tags:
- hooks
- revision
- tortoisesvn
---

Sometimes you want to change your log message when using SVN. And it does not allow you to do that. Mostly the error you will see is "Repository has not been enabled to accept revision propchanges"




[![problemaddingcomments](http://www.fantageek.com/wp-content/uploads/2013/02/problemaddingcomments.jpg)](http://www.fantageek.com/230/tortoisesvn-repository-has-not-been-enabled-to-accept-revision-propchanges/problemaddingcomments/#main)




In this case, there are 2 solutions




**Solutions 1**  

To correct this, we needed to create a file in the “hooks” folder of my Subversion repository. On my system, it was located at C:\svn\repository\hooks. I created a file called “pre-revprop-change.bat”, and I set the contents to this:




[sourcecode language="csharp"]  

rem Only allow log messages to be changed.  

if “%4″ == “svn:log” exit 0  

echo Property ‘%4′ cannot be changed >&2  

exit 1  

[/sourcecode]




**Solution 2**  

Place this in a pre-revprop-change.cmd




[sourcecode language="csharp"]  

IF %5 EQU M GOTO CONT1  

GOTO FAIL  

:CONT1  

IF %4 EQU svn:log GOTO OK  

:FAIL  

echo "Changing revision properties other than svn:log is prohibited" >&2  

exit 1  

:OK  

exit 0  

[/sourcecode]




After saving the file with those contents, I was able to edit the revision comments without encountering the error message.




**Reference**  

1. [http://blog.tfanshteyn.com](http://blog.tfanshteyn.com/2007/12/subversion-pre-revprop-change-hook.html)  

2. [http://tortoisesvn.net](http://tortoisesvn.net/docs/release/TortoiseSVN_en/tsvn-repository-hooks.html)
