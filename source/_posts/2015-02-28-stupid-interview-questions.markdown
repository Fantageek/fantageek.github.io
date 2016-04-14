---
author: onmyway133
comments: true
date: 2015-02-28 17:56:50+00:00
layout: post
slug: stupid-interview-questions
title: Stupid Interview Questions
wordpress_id: 1625
categories:
- Programming
---

The original is http://exold.com/article/stupid-interview-questions but it seems the site if off, so I made a copy here as a reference for myself and those who need

I was reading a blog post on some guy’s programming interview, where he complained about being asked to write a file copy function. Now, while it’s quite possible to take umbrage at this, if I were in that situation, I’d see it as a chance for some free entertainment _cool_.

Q. What do you mean by “copy”?
A. Ummm…copy the contents of the file to a new file.
Q. What about the date/time stamps?
A. No, those don’t need to be copied.
Q. Does the result file have to have the same name?
A. No.
Q. Can it have the same name?
A. Umm…no.
Q. Do I have to worry about name spoofing? What about the Turkish I?
A. Don’t worry about that.
Q. Does it have to be in the same location? Note that if it is in the same location, it (probably) can’t have the same name. Unless you copy it to itself (another question…).
A. Yes.
Q. What about the attributes?
A. Make the attributes the same.
Q. Should I modify the attributes of the source file? If this file copy is part of a backup or archive operation, it’d probably be a mistake to leave the ‘Archive’ attribute on.
A. No, leave them as-is.
Q. What if the source file has the Archive attribute off? If I make it off on the new file as well, it could screw up the user’s backup software.
A. Just make it the same. I don’t care about the user’s backup software.
Q. Well, I’m not sure that’s the best approach to take when thinking about designing software FOR users, but if you say so.
A. …
Q. What about compression? It’s a file attribute, but the copy destination may not support compression.
A. Don’t compress the copy.
Q. Even if the source is compressed, and the destination supports compression?
A. YES.
Q. What about encryption? What if the source file is encrypted, but the destination does not support encryption?
A. Don’t encrypt the copy if the destination doesn’t support it.
Q. Mmmmm, sorry, don’t mean to digress, but…that could be a serious security hole. Especially if wherever this file copy function ends up supports arbitrary parameters (directly or indirectly).
A. Look, just copy the damn file.
Q. What about the file creator?
A. Don’t care.
Q. What about the file owner?
A. Don’t care.
Q. What about file permissions? Is there a separate way we should handle inherited permissions and assigned permissions?
A. Forget the permissions.
Q. What operating system will this function be required to run on?
A. Windows XP.
Q. Home, or Pro, or Media Center, or one or more?
A. Pro.
Q. What service pack level are we aiming for?
A. Service Pack 2.
Q. Does that mean we don’t have to support any other SP level?
A. Yes, fine.
Q. How will the source file name be supplied?
A. As a parameter.
Q. Is it going to be supplied as a null-terminated string, a counted string, or an object?
A. Null-terminated string.
Q. Is it possible that a NULL pointer will be passed in?
A. No.
Q. Is it possible that an empty string will be passed in?
A. No.
Q. Is it possible that a malformed string will be passed in (e.g., no NULL terminator)?
A. No.
Q. What encoding will the filename be in?
A. Unicode.
Q. Sorry, umm…Unicode is not an encoding, really. If we’re using Unicode data, it has to have a specific encoding, like UTF-8, UCS-2, UTF-16, etc.
A. Fine. Make it UTF-8.
Q. OK. I’d like to note at this point that it’s a bit of a pain to have to transcode UTF-8 to UTF-16 in order to be able to supply it to Windows API calls.
A. Fine! UTF-16!
Q. Big-endian or little-endian?
A. ARGH. Whichever you want!
Q. Should we accept relative paths, or only fully-specified paths?
A. Only full paths.
Q. Are there any characteristics of the paths we are supposed to accept that I can screen the parameters on?
A. No. The caller will do all screening of paths and file names.
Q. How will the destination file name be generated or supplied?
…
Q. Am I required to support (or allow) an asynchronous copy operation?
A. No.
Q. How should I report errors? Exceptions? Error codes?
A. Whatever.
Q. Should I try and handle exceptions from called functions internally, or let them pass through to the caller?
A. Err…let them pass through.
Q. What if the destination file already exists?
A. It won’t.
Q. So the caller ensures that?
A. Right, sure.
Q. So if it does exist, I can just terminate the program, then? Obviously this would be a violation of preconditions, and who knows WHAT is going on.
A. Sure, whatever you want.
Q. What about alternate data streams?
A. Do whatever you want!
Q. Look, I’m sorry if you feel put-upon here, please don’t get hostile. I’m just trying to get a clear picture of the specs I need here. Obviously if I’m going to write a file copy method, instead of using one of the many extant file copy routines in various libraries and frameworks, it’s going to be fulfilling a specialized set of requirements, and I’m going to need to have good answers for these questions. If you want, I can hack something together in a minute, but I’d have to note that there were many unresolved issues as to requirements and purposes.
A. AIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIII

Mission accomplished. wink
