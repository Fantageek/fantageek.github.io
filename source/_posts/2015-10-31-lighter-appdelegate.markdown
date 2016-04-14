---
layout: post
author: onmyway133
title: "Lighter AppDelegate"
date: 2015-10-31 22:55:04 +0700
comments: true
categories:
---

There is [Lighter View Controllers](https://www.objc.io/issues/1-view-controllers/lighter-view-controllers/), and there is Lighter AppDelegate, too

Since working with iOS, I really like the delegate pattern, in which it helps us defer the decision to another party.

The iOS application delegates its event to AppDelegate, which over time will be a big mess. Usually, the AppDelegate is where you put your root view controller setup, crash tracking, push notification, debugging, ... and we just somehow violent the Single Responsibility principle. Moreover, it makes us hard to reason about code in AppDelegate

Service
--
I like to think of each task in AppDelegate as a service. And the AppDelegate distributes the events into each service via ServiceDispatcher. Simple plain old composition and looping

I tend to have RootService as a place to setup root view controllers

It looks like this

`ServiceDispatcher.swift`
```swift
class ServiceDispatcher : NSObject, UIApplicationDelegate {
    let services: [UIApplicationDelegate]

    init(services: [UIApplicationDelegate]) {
        self.services = services
    }

    func application(application: UIApplication,
        didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {

        services.forEach { service in
            service.application?(application, didFinishLaunchingWithOptions: launchOptions)
        }

        return true
    }

    func applicationDidBecomeActive(application: UIApplication) {
        services.forEach { service in
            service.applicationDidBecomeActive?(application)
        }
    }

    func applicationWillResignActive(application: UIApplication) {
        services.forEach { service in
            service.applicationWillResignActive?(application)
        }
    }

    func applicationWillEnterForeground(application: UIApplication) {
        services.forEach { service in
            service.applicationWillEnterForeground?(application)
        }
    }

    func applicationDidEnterBackground(application: UIApplication) {
        services.forEach { service in
            service.applicationDidEnterBackground?(application)
        }
    }
}
```

`RootService.swift`
```swift
class RootService : NSObject, UIApplicationDelegate {
    func application(application: UIApplication,
        didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {

        appDelegate().window = UIWindow(frame: UIScreen.mainScreen().bounds)
        showHome()
        appDelegate().window?.makeKeyAndVisible()

        return true
    }
}

extension RootService {
    func showHome() {
        let home = HomeWireframe().makeHome()
        let navC = UINavigationController(rootViewController: home!)
        appDelegate().window?.rootViewController = navC
    }
}

extension RootService {
    func appDelegate() -> AppDelegate {
        return UIApplication.sharedApplication().delegate as! AppDelegate
    }
}
```

`AppDelegate.swift`
```swift
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?
    let serviceDispatcher = ServiceDispatcher(services: [RootService()])


    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {

        serviceDispatcher.application(application, didFinishLaunchingWithOptions: launchOptions)

        return true
    }

    func applicationWillResignActive(application: UIApplication) {
        serviceDispatcher.applicationWillResignActive(application)
    }

    func applicationDidEnterBackground(application: UIApplication) {
        serviceDispatcher.applicationDidEnterBackground(application)
    }

    func applicationWillEnterForeground(application: UIApplication) {
        serviceDispatcher.applicationWillEnterForeground(application)
    }

    func applicationDidBecomeActive(application: UIApplication) {
        serviceDispatcher.applicationDidBecomeActive(application)
    }
}
```

I have more services like DebugService, PushNotificationService, CrashTrackingService, ...

The downside to this approach is that in real life, there will be dependencies between those services, like that UserService must be called before RootService? In this case, I have to use comment to explain why I have that decision, which is hard for newcomers to understand at first. Take a look at [How to Move Bootstrapping Code Out of AppDelegate](http://christiantietze.de/posts/2015/10/bootstrapping-appdelegate/) for how dependencies are managed

[JSDecoupledAppDelegate](https://github.com/JaviSoto/JSDecoupledAppDelegate) comes with another approach, in which service events are named according to the functions, like `appStateDelegate`, `appDefaultOrientationDelegate`, `watchInteractionDelegate`, ...

But for me, Service and ServiceDispatcher suit my need

Reference
--
- [SERVICE-ORIENTED APPDELEGATE](http://sizeof.io/service-oriented-appdelegate/)
- [JSDecoupledAppDelegate](https://github.com/JaviSoto/JSDecoupledAppDelegate)
- [How to Move Bootstrapping Code Out of AppDelegate](http://christiantietze.de/posts/2015/10/bootstrapping-appdelegate/)
