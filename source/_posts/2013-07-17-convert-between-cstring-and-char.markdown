---
author: onmyway133
comments: true
date: 2013-07-17 04:54:21+00:00
layout: post
slug: convert-between-cstring-and-char
title: Convert between CString and char*
wordpress_id: 495
categories:
- Others
tags:
- C
- char
- convert
- cstring
- mfc
---

This tutorial will show you some code snippets that perform conversion between CString and   char*




[code language="cpp"]  

char* CString2char(CString str)  

{  

 int len = str.GetLength();  

 char* result = new char[len];  

 sprintf(result, "%S", str);  

 return result;  

}




CString char2CString(char* str)  

{  

 return CString(str);  

}  

[/code]
