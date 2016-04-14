---
author: onmyway133
comments: true
date: 2014-08-24 01:30:48+00:00
layout: post
slug: how-to-create-xcode-plugin
title: How to create Xcode plugin
wordpress_id: 1297
categories:
- iOS
tags:
- how
- plugin
- tutorial
- xcode
---

I list here some good references that teach how to build your own Xcode plugin. The best way to build an Xcode plugin is to learn from some with the same functionalities

<!-- more -->



#### Find good Xcode plugins



Just search on Google and you'll find a lot. Some are nice to curate the lists
1. [Xcode Plugins - NSHipster](http://nshipster.com/xcode-plugins/)
2. [My favorite Xcode plugins](http://brettterpstra.com/2014/04/28/my-favorite-xcode-plugins/)
3. [Best Xcode plugins](http://iosdevtips.co/post/82232620790/best-xcode-plugins)
4. [Xcode Plugin Listing – Quality Xcode Plugins](http://maniacdev.com/xcode-plugins)
5. [Complete (more or less) list of Xcode plugins](http://cocoanaut.com/useful-xcode-4-plugins/)
6. [A Few Helpful Xcode Plugins](http://bencoding.com/2013/03/19/a-few-helpful-xcode-plugins/)
7. [Alcatraz](http://alcatraz.io/) Alcatraz is an Xcode plugin that manages Xcode plugins

You can also learn from my plugin [XcodeWay](https://github.com/onmyway133/XcodeWay), which is very simple. It just contains menu items and actions



Tutorial
--

`blackdogfoundry` has some very good tutorials on creating Xcode plugin. Check it out
The only thing he missed is the DVTPlugInCompatibilityUUIDs in your Info.plist

```xml
<key>DVTPlugInCompatibilityUUIDs</key>
	<array>
		<string>AD68E85B-441B-4301-B564-A45E4919A6AD</string>
		<string>A2E4D43F-41F4-4FB9-BB94-7177011C9AED</string>
		<string>63FC1C47-140D-42B0-BB4D-A10B2D225574</string>
		<string>37B30044-3B14-46BA-ABAA-F01000C27B63</string>
		<string>640F884E-CE55-4B40-87C0-8869546CAB7A</string>
	</array>
```

Check it this issue [Plugin not loaded in XCode 5.1](https://github.com/macoscope/CodePilot/issues/15) to find out more about DVTPlugInCompatibilityUUIDs problem

Also, see the talk by Marin (Reference No.8), for more about DVTPlugInCompatibilityUUIDs

Private APIs
--

You sometime need to use private APIs to create Xcode plugin.
For example, [Get the path of current project opened in Xcode plugin](http://stackoverflow.com/a/22758211/1418457)

```objc
NSArray *workspaceWindowControllers = [NSClassFromString(@"IDEWorkspaceWindowController") valueForKey:@"workspaceWindowControllers"];

id workSpace;

for (id controller in workspaceWindowControllers) {
    if ([[controller valueForKey:@"window"] isEqual:[NSApp keyWindow]]) {
        workSpace = [controller valueForKey:@"_workspace"];
    }
}

NSString *workspacePath = [[workSpace valueForKey:@"representingFilePath"] valueForKey:@"_pathString"];
```

Some plugin like [ShowInGithub](https://github.com/larsxschneider/ShowInGitHub) use [class-dump](https://github.com/nygard/class-dump) to generate the header APIs and include them in their Header Search Path, this way, they can work with private APIs more easily

NSTask
--

NSTask can help you run command as if you run on the terminal, really powerful. I use it a lot

[NSTask tutorial](www.raywenderlich.com/36537/nstask-tutorial)

Cocoa programming
--
Creating an Xcode plugin means using a lots of Cocoa APIs. You should have some idea of [Cocoa programming](https://developer.apple.com/technologies/mac/cocoa.html)

Reference
--
- [Xcode Plugins](http://nshipster.com/xcode-plugins/)
- [Xcode5 Plugin Template](https://github.com/kattrali/Xcode5-Plugin-Template)
- [Creating an Xcode4 Plugin](http://www.blackdogfoundry.com/blog/creating-an-xcode4-plugin/)
- [Common Xcode4 Plugin Techniques](http://www.blackdogfoundry.com/blog/common-xcode4-plugin-techniques/)
- [Debugging your Xcode plugin](http://www.blackdogfoundry.com/blog/debugging-your-xcode-plugin/)
- [Alcatraz internals by Marin Usalj](https://speakerdeck.com/supermarin/alcatraz-internals)
- [Everything you didn't about Alcatraz](http://vimeo.com/98274952)
- [How To Create an Xcode Plugin](http://www.raywenderlich.com/94020/creating-an-xcode-plugin-part-1)
- [Writing Xcode plugin in Swift](http://merowing.info/2015/12/writing-xcode-plugin-in-swift/)
