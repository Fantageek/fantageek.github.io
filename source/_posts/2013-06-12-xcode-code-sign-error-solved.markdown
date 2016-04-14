---
author: onmyway133
comments: true
date: 2013-06-12 17:21:25+00:00
layout: post
slug: xcode-code-sign-error-solved
title: Xcode code sign error solved
wordpress_id: 371
categories:
- iOS
tags:
- authority
- certificate
- code
- error
- mac osx
- sign
- xcode
---

Have you ever been in this situation, you have your development certificate (Enterprise certificate in my case) ready. Click both on .mobileprevision and .p12 to install them in your Keychain.




All went OK. But deploying on your device in Xcode popups the error "Code sign error".  

Now, open Keychain Access, choose login as Keychains and My certificates as Category, click on your development certificate, you probably see that




[code language="css"]  

This certificate was signed by an unknown authority  

[/code]




This is because your Mac is missing Apple Root Certificate, which   is a known authority :D  

Visit [Apple Root Certification Authority site,](http://www.apple.com/certificateauthority/) you may need to download and install






  * Apple Inc. Root Certificate


  * Apple Computer, Inc. Root Certificate


  * Worldwide Developer Relations




Now back to your development certificate in Keychain Access, you should see




[code language="css"]  

This certificate is valid  

[/code]




Now you should have no problem deploy your app to your device, open Xcode and start happy coding
