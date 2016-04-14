---
author: onmyway133
comments: true
date: 2013-06-23 02:46:32+00:00
layout: post
slug: visual-studio-2012-c-project-load-failed
title: Visual Studio 2012 C++ project load failed
wordpress_id: 437
categories:
- Others
tags:
- '2012'
- C
- constraint
- export
- fail
- fix
- found
- match
- 'no'
- project
- visual studio
- vs
---

Visual Studio 2012 is great. But when opening it for the 2nd time, the weird thing happended. I couldn't create new C++ project, and my previous C++ project failed to load. What we 'll see is the error


<blockquote>No exports were found that match the constraint:
ContractName Microsoft.VisualStudio.Shell.Interop.IVsHierarchy
RequiredTypeIdentity Microsoft.VisualStudio.Shell.Interop.IVsHierarchy</blockquote>


The solution is to install this [update KB2781514](http://support.microsoft.com/kb/2781514). This works for me.
