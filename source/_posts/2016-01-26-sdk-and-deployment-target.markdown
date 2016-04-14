---
layout: post
author: onmyway133
title: "SDK and Deployment Target"
date: 2016-01-26 22:22:13 +0700
comments: true
categories:
---

I see that my answer to the question [What's the meaning of Base SDK, iOS deployment target, Target, and Project in xcode](http://stackoverflow.com/questions/18568572/whats-the-meaning-of-base-sdk-ios-deployment-target-target-and-project-in-xc/22536773#22536773) gets lots of views, so I think I need to elaborate more about it

Good read

- [ iOS 7 TechTalk - Architecting Modern Apps, Part 2](https://developer.apple.com/tech-talks/videos/)
- [Configuring a Project for SDK-Based Development](https://developer.apple.com/library/ios/documentation/DeveloperTools/Conceptual/cross_development/Configuring/configuring.html)

![](http://i.stack.imgur.com/CJF6Y.png)

Base SDK
--

- We can't configure this anymore, as Xcode will use the latest SDK. For Xcode 7, the SDK is iOS 9
- If we upgrade Xcode, it will use the newer version of the SDK. Like Xcode 7.2, the SDK is iOS 9.1
- > Choosing the latest SDK for your project lets you use the new APIs introduced in the OS update that corresponds to that SDK. When new functionality is added as part of a system update, the system update itself does not typically contain updated header files reflecting the change. The SDKs, however, do contain updated header files.

Deployment Target
--

- We can set in Xcode -> Target -> Deployment Info -> Deployment Target
- State that we support this iOS version

What does it mean
--

> So, a modern App might use iOS 9 as the Target SDK, and iOS 7 as the deployment target. This means that you can run on iOS 7, iOS 8 and iOS 9, and that you have available to you any iOS 9 calls when actually running on iOS 9.

.

> Each .sdk directory resembles the directory hierarchy of the operating system release it represents: It has usr, System, and Developer directories at its top level. OS X .sdk directories also contain a Library directory. Each of these directories in turn contains subdirectories with the headers and libraries that are present in the corresponding version of the operating system with Xcode installed.

.

> The libraries in an iOS or OS X SDK are stubs for linking only; they do not contain executable code but just the exported symbols. SDK support works only with native build targets.

So the SDK is just like stub and header only. It means that we can use certain APIs, but on OS that does not have the real symbols for those APIs, it crashes

available
--

Swift 2 introduces `available` construct that guards against failure when trying to use newer APIs.

Note that `available` is runtime, not compile time. All the code is inside your executable

```swift
if #available(iOS 9, OSX 10.10, *) {
    // Code to execute on iOS 9, OS X 10.10
} else {

}
```

deprecated APIs
--

- [Finding Instances of Deprecated API Usage](https://developer.apple.com/library/ios/documentation/DeveloperTools/Conceptual/cross_development/Using/using.html#//apple_ref/doc/uid/20002000-SW6)

> Always check to see if you are using deprecated APIs; though still available, deprecated APIs are not guaranteed to be available in the future

Compile time vs Runtime
--

- [Conditionally Compiling for Different SDKs](https://developer.apple.com/library/ios/documentation/DeveloperTools/Conceptual/cross_development/Using/using.html#//apple_ref/doc/uid/20002000-SW5)

```swift
#if (arch(i386) || arch(x86_64)) && os(iOS)
    // code inside gets inserted into executable when builds for simulator
#else
    // code inside gets inserted into executable when builds for device
#endif
```

```swift
#if os(OSX)
    import Cocoa
#elseif os(iOS)
    import UIKit
#endif
```

```swift
// All the code gets inserted into executable, but is run depending on the version of the OS
if #available(iOS 9, *) {
    // use UIStackView
} else {
    // show your manual Auto Layout skill
}
```

Weakly vs strongly linked
--

- [Weak Linking and Apple Frameworks](https://developer.apple.com/library/ios/documentation/DeveloperTools/Conceptual/cross_development/Configuring/configuring.html#//apple_ref/doc/uid/10000163i-CH1-SW2)
- [What does it mean to “weak-link” a framework?](http://stackoverflow.com/questions/16922013/what-does-it-mean-to-weak-link-a-framework)
- [Frameworks and Weak Linking](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WeakLinking.html)

> For example, suppose in Xcode you set the deployment target (minimum required version) to “OS X v10.5” and the base SDK (maximum allowed version) to “OS X v10.6”. During compilation, the compiler would weakly link interfaces that were introduced in OS X v10.6 while strongly linking interfaces defined in earlier versions of the OS. This would allow your application to run in OS X v10.5 and take advantage of newer features when available.

.

> None of the (platform) frameworks is really "included in the bundle". Instead, your app has a reference ("link") to a framework once you add it to the "Link Binary with Library" build phase. The frameworks are pre-installed on the devices. When you run an app, all the app's framework references are resolved by the dynamic linker (on the device), which means the framework code is loaded so your app can use it.

Reference
--

- [Checking API Availability With Swift](http://useyourloaf.com/blog/checking-api-availability-with-swift.html)
