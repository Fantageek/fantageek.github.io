---
author: onmyway133
comments: true
date: 2013-07-05 03:14:44+00:00
layout: post
slug: convert-between-wchar-t-and-char
title: Convert between wchar_t* and char*
wordpress_id: 467
categories:
- Windows 8
tags:
- char
- convert
- MultiByteToWideChar
- wchar
- WideCharToMultiByte
---

This tutorial will show you some code snippets that perform conversion between wchar_t* and char* using WideCharToMultiByte and MultiByteToWideChar




#include <windows.h>




**Convert from wchar_t* to char***




[code language="cpp"]<br /><br />
char* wchar2char(wchar_t* wname)<br /><br />
{<br /><br />
 char* cname = NULL;<br /><br />
 int len_wname, len_cname;<br /><br />
 len_wname = wcslen(myFileName-&gt;Data());<br /><br />
 len_cname = WideCharToMultiByte(CP_ACP, 0, wname, len_wname, NULL, 0, NULL, FALSE);</p><br />
<p> if(len_cname &gt; 0)<br /><br />
 {<br /><br />
        cname = (char*)malloc(sizeof(char) * (len_cname+1));<br /><br />
  WideCharToMultiByte(CP_ACP, 0, wname, len_wname, cname, len_cname, NULL,   FALSE);<br /><br />
 }</p><br />
<p> cname[len_cname] = 0;</p><br />
<p> return cname;<br /><br />
}<br /><br />
[/code]




**Convert from char* to wchar_t***




[code language="cpp"]<br /><br />
wchar_t* char2wchar(char* cname)<br /><br />
{<br /><br />
 wchar_t* wname = NULL;<br /><br />
 int len_wname, len_cname;</p><br />
<p> len_cname = strlen(cname);<br /><br />
 len_wname = MultiByteToWideChar(CP_ACP,  0, cname, len_cname, NULL, 0);</p><br />
<p> if(len_wname &gt; 0)<br /><br />
 {<br /><br />
  wname = (wchar_t*)malloc(sizeof(wchar_t) * (len_wname+1));<br /><br />
  MultiByteToWideChar(CP_ACP, 0, cname, len_cname, wname, len_wname);<br /><br />
 }</p><br />
<p> wname[len_wname] = 0;</p><br />
<p> return wname;<br /><br />
}<br /><br />
[/code]
