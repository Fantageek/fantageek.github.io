---
layout: post
author: onmyway133
title: "NSApplicationDelegate and notification"
date: 2016-01-25 23:24:57 +0700
comments: true
categories:
---

In an iOS project, we often see this in `AppDelegate`

```swift
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {

        return true
    }
}
```

But in a Cocoa project, we see this instead

```swift
@NSApplicationMain
class AppDelegate: NSObject, NSApplicationDelegate {



    func applicationDidFinishLaunching(aNotification: NSNotification) {
        // Insert code here to initialize your application
    }

    func applicationWillTerminate(aNotification: NSNotification) {
        // Insert code here to tear down your application
    }
}
```

In this case the param is of type `NSNotification`

Delegate and notification
--

Reading [Cocoa Core Competencies - Delegation](https://developer.apple.com/library/mac/documentation/General/Conceptual/DevPedia-CocoaCore/Delegation.html)

> The delegate of most Cocoa framework classes is automatically registered as an observer of notifications posted by the delegating object. The delegate need only implement a notification method declared by the framework class to receive a particular notification message. Following the example above, a window object posts an NSWindowWillCloseNotification to observers but sends a windowShouldClose: message to its delegate.

So the pattern is that the delegate should strip the `NS` and `Notification`, like `NSWindowWillCloseNotification` to `windowShouldClose:`
