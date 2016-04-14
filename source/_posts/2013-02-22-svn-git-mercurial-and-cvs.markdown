---
author: onmyway133
comments: true
date: 2013-02-22 00:56:01+00:00
layout: post
slug: svn-git-mercurial-and-cvs
title: Compare subversion system SVN, Git, Mercurial, and CVS
wordpress_id: 245
categories:
- Others
tags:
- cvs
- git
- mercurial
- svn
- version control
---

**What is Subversion**  

Subversion is a free/open source version control system . That is, Subversion manages files and directories, and the changes made to them, over time. This allows you to recover older versions of your data or examine the history of how your data changed. In this regard, many people think of a version control system as a sort of “time machine.”




[![images](http://www.fantageek.com/wp-content/uploads/2013/02/images.jpg)](http://www.fantageek.com/245/svn-git-mercurial-and-cvs/images/#main)




Some version control systems are also software configuration management (SCM) systems. These systems are specifically tailored to manage trees of source code and have many features that are specific to software development—such as natively understanding programming languages, or supplying tools for building software.




**Which version control is right for your project?**  

The main difference between version control systems is whether they are server based or peer-to-peer. Beyond that, you will also want to consider speed, functionality, and the learning curve associated with the system.




**Concurrent Versions System (CVS)**  

CVS ([http://www.nongnu.org/cvs/](http://www.nongnu.org/cvs/)) has been around since the 80s, and has been very popular with both commercial and open source developers. It is released under the GNU license, and uses a system to let users “check out” the code they are going to work on and “check in” their changes. Originally, CVS handled conflicts between two programmers by only allowing for the latest version of the code to be worked on and updated. As such, it was a first come, first serve system where the user must publish changes quickly to ensure that other users haven’t beat them to the punch




+ Has been in use for many years and is considered mature technology  

- Moving or renaming files does not include a version update  

- Security risks from symbolic links to files  

- No atomic operation support, leading to source corruption  

- Branch operations are expensive as it is not designed for long-term branching




**Apache Subversion (SVN)**  

SVN [(http://subversion.apache.org/]((http://subversion.apache.org/)) was created as an alternative to CVS that would fix some bugs in the CVS system while maintaining high compatibility with it.  

SVN is currently the king of server-based version control. It has all of the good features of CVS and improves upon them




+ Newer system based on CVS  

+ Includes atomic operations  

+ Cheaper branch operations  

+ Wide variety of plug-ins for IDEs  

+/- Does not use peer-to-peer model  

- Still contains bugs relating to renaming files and directories  

- Insufficient repository management commands  

- Slower comparative speed




**Git**  

First developed by Linus Torvalds of Linux fame, Git ([http://git-scm.com/](http://git-scm.com/)) takes a radical approach that differs greatly from CVS and SVN. The original concepts for Git were to make a faster, distributed revision control system that would openly defy conventions and practices used in CVS  

Git has a clear speed improvement over its competitors, and for projects that lend themselves to distributed systems, it is a clear improvement. The primary downside cited for Git is that it can be at times difficult to explain to others, and there is likely to be a slow down in production as programmers adapt to it. Once it is learned, however, the speed increases and better branch management will reclaim that time and more




+ Great for those who hate CVS/SVN  

+ Dramatic increase in operation speed  

+ Cheap branch operations  

+ Full history tree available offline  

+/- Distributed, peer-to-peer model  

- Learning curve for those used to SVN  

- Not optimal for single developers  

- Limited Windows support compared to Linux




**Mecurial**  

Mecurial ([http://mercurial.selenic.com/](http://mercurial.selenic.com/)) began close to the same time as Git and is   also a distributed revision control tool. It was originally made to compete with Git for Linux kernel development, and as Git was selected, Mecurial has seen less success in that area.  

It’s different from other revision control systems in that Mecurial is primarily implemented in Python as opposed to C




+ Easier to learn than Git  

+ Better documentation  

+/- Distributed model  

- No merging of two parents  

- Extension-based rather than scriptability  

- Less out of the box power




**Reference**  

1. [http://svnbook.red-bean.com](http://svnbook.red-bean.com/en/1.7/svn.intro.whatis.html)  

2. [http://biz30.timedoctor.com](http://biz30.timedoctor.com/git-mecurial-and-cvs-comparison-of-svn-software/)
