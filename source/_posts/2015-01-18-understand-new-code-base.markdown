---
author: onmyway133
comments: true
date: 2015-01-18 10:11:45+00:00
layout: post
slug: understand-new-code-base
title: Understanding new code base
wordpress_id: 1578
categories:
- iOS
---

This post contains some tips I found useful when navigating to a new code base. Some may overlap with each other, but you know the point.



## [Aspects](https://github.com/steipete/Aspects)



This is the best library for Aspect Oriented Programming on iOS. Furthermore, its hooking system is useful

For example, this allows you to know which ViewController you are in
[code language="objc"]
[UIViewController aspect_hookSelector:@selector(viewWillAppear:) withOptions:AspectPositionAfter usingBlock:^(id<AspectInfo> aspectInfo, BOOL animated) {
    NSLog(@"View Controller %@ will appear animated: %tu", aspectInfo.instance, animated);
} error:NULL];
[/code]



## [FLEX](https://github.com/Flipboard/FLEX)



This allows us to easily detect which views we are seeing. I see that it's much better than Xcode 6 View Debugging. I don't like the idea of a massive storyboard with lots of ViewController in it, so I don't look at storyboard first.

[code language="objc"]
[[FLEXManager sharedManager] showExplorer];
[/code]



## [chisel](https://github.com/facebook/chisel)



LLDB is good, and it's much better with chisel. It provides lots of useful commands for you to interact with views and properties.



## Control-1



Xcode jumpbar menu 1 gives you some more info about class: Counterparts, Superclasses, Siblings, Includes, Included By, ...



## Symbolic Breakpoint



You can create symbolic breakpoint through Xcode or LLDB.



<blockquote>
  Symbolic breakpoints stop program execution when a specific function or method starts executing.
  You can specify the symbol as:
  A method name. For example, pathsMatchingExtensions:.
  A method of a particular class. For example, [SKTLine drawHandlesInView], people::Person::name().
  A function name. For example, _objc_msgForward.
</blockquote>





## Object graph



[objc-dependency-visualizer](https://github.com/PaulTaykalo/objc-dependency-visualizer) Objective-C class dependency visualizer. It's tool that helps to visualize current state of your project. It's really easy to see how tight your classes are coupled.
[ObjectGraph-Xcode](https://github.com/vampirewalk/ObjectGraph-Xcode) ObjectGraph can show oriented graph of dependencies between classes in your project.
[objc_dep](https://github.com/nst/objc_dep) Graph the import dependancies in an Objective-C project
[KSHObjcUML](https://github.com/kimsungwhee/KSHObjcUML) KSHObjcUML can show oriented graph of dependencies between Objective-C classes in your project



## Reference







  1. [Navigating a New Codebase: Tips and tricks for getting up to speed quickly](http://www.raywenderlich.com/79600/navigating-a-new-codebase)


  2. [Navigating and discovering an iOS codebase using lldb](https://medium.com/ios-os-x-development/navigating-and-discovering-a-code-base-using-lldb-bca7c10115cf)


