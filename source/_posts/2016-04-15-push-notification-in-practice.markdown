---
author: onmyway133
comments: true
date: 2014-05-31 17:02:49+00:00
layout: post
slug: push-notification-in-practice
title: Push Notification in practice
wordpress_id: 984
categories:
- iOS
tags:
- ios
- notification
- practice
- push
---

Here are my notes for working with Push Notification, updated for iOS 9

## How to register

- Register to receive push notification

`registerForRemoteNotificationTypes` is deprecated in iOS 8+

```swift
UIApplication.sharedApplication().registerForRemoteNotifications()
```

- Register to alert user through UI

> If your app displays alerts, play sounds, or badges its icon, you must call this method during your launch cycle to request permission to alert the user in these ways

```swift
let types: UIUserNotificationType = [.Badge, .Sound, .Alert]
let categories = Set<UIUserNotificationCategory>()
let settings = UIUserNotificationSettings(forTypes: types, categories: categories)

UIApplication.sharedApplication().registerUserNotificationSettings(settings)
```

You don't need to wait for `registerUserNotificationSettings` to callback before calling `registerForRemoteNotifications`

## When to register

- [Registering, Scheduling, and Handling User Notifications](https://developer.apple.com/library/mac/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/IPhoneOSClientImp.html)

> Never cache a device token; always get the token from the system whenever you need it. If your app previously registered for remote notifications, calling the registerForRemoteNotifications method again does not incur any additional overhead, and iOS returns the existing device token to your app delegate immediately. In addition, iOS calls your delegate method any time the device token changes, not just in response to your app registering or re-registering

> The user can change the notification settings for your app at any time using the Settings app. Because settings can change, always call the registerUserNotificationSettings: at launch time and use the application:didRegisterUserNotificationSettings: method to get the response. If the user disallows specific notification types, avoid using those types when configuring local and remote notifications for your app.

## didReceiveRemoteNotification

About `application:didReceiveRemoteNotification:`

> Implement the application:didReceiveRemoteNotification:fetchCompletionHandler: method instead of this one whenever possible. If your delegate implements both methods, the app object calls the application:didReceiveRemoteNotification:fetchCompletionHandler: method.

> If the app is not running when a remote notification arrives, the method launches the app and provides the appropriate information in the launch options dictionary. The app does not call this method to handle that remote notification. Instead, your implementation of the application:willFinishLaunchingWithOptions: or application:didFinishLaunchingWithOptions: method needs to get the remote notification payload data and respond appropriately.

About `application:didReceiveRemoteNotification:fetchCompletionHandler:`

This is for silent push notification with `content-available`

> Unlike the application:didReceiveRemoteNotification: method, which is called only when your app is running in the foreground, the system calls this method when your app is running in the foreground or background

> In addition, if you enabled the remote notifications background mode, the system launches your app (or wakes it from the suspended state) and puts it in the background state when a push notification arrives. However, the system does not automatically launch your app if the user has force-quit it. In that situation, the user must relaunch your app or restart the device before the system attempts to launch your app automatically again.

> If the user opens your app from the system-displayed alert, the system may call this method again when your app is about to enter the foreground so that you can update your user interface and display information pertaining to the notification.

## How to handle

Usually, the use of push notification is to display a specific article, a specific DetailViewController, ... in your app. So the good practices are
- When the app is in foreground: Gently display some kind of alert view and ask the user whether he would like to go to that specific page or not
- When user is brought from background to foreground, or from terminated to foreground: Just navigate to that specific page. For example, if you use UINavigationController, you can set that specific page the top most ViewController, if you use UITabBarController, you can set that specific page the selected tab, something like that

```swift
- func handlePushNotification(userInfo: NSDictionary) {
    // Check applicationState
	if (applicationState == UIApplicationStateActive) {
        // Application is running in foreground
        showAlertForPushNotification(userInfo)
    }
    else if (applicationState == UIApplicationStateBackground || applicationState == UIApplicationStateInactive) {
    // Application is brought from background or launched after terminated
        handlePushNotification(userInfo)
    }
}
```
Here we create another method `handlePushNotification:`` to handle push notification. When you receive push notification, 3 cases can occur


#### Case 1: Foreground

Loud push

- No system alert
- `application:didReceiveRemoteNotification:fetchCompletionHandler:` called

Silent push

- No system alert
- `application:didReceiveRemoteNotification:fetchCompletionHandler:` called

#### Case 2: Background

Loud push

- System alert
- No method called
- Tap notification and `application:didReceiveRemoteNotification:fetchCompletionHandler:` called
- Tap on App Icon and nothing is called

Silent push

- System alert
- `application:didReceiveRemoteNotification:fetchCompletionHandler:` called. If app is suspended, its state changed to `UIApplicationStateBackground`
- Tap notification and `application:didReceiveRemoteNotification:fetchCompletionHandler:` called
- Tap on App Icon and nothing is called

#### Case 3: Terminated

Loud push

- System alert
- No method called
- Tap notification and `application:didFinishLaunchingWithOptions:` with `launchOptions`,  `application:didReceiveRemoteNotification:fetchCompletionHandler:` called
- Tap on App Icon and `application:didFinishLaunchingWithOptions:` is called with `launchOptions` set to nil

Silent push

- System alert
- `application:didReceiveRemoteNotification:fetchCompletionHandler:` called. If app was not killed by user, it is woke up and state changed to `UIApplicationStateInactive`.
- Tap notification and `application:didFinishLaunchingWithOptions:` with `launchOptions`,  `application:didReceiveRemoteNotification:fetchCompletionHandler:` called
- Tap on App Icon and `application:didFinishLaunchingWithOptions:` is called with `launchOptions` set to nil

## System alert

System alert only show if the payload contains "alert"

```json
{
    "aps" : {
        "alert" : {
            "title" : "Game Request",
            "body" : "Bob wants to play poker",
            "action-loc-key" : "PLAY"
        },
        "badge" : 5
    },
    "param1" : "bar",
    "param2" : [ "bang",  "whiz" ]
}
```

## Silent push payload

For now I see that silent push must contain "sound" for `application:didReceiveRemoteNotification:fetchCompletionHandler:` to be called when app is in background

```json
{
 "aps": {
   "content-available": 1,
   "alert": "hello" // include this if we want to show alert
   "sound": "" // this does the trick
  },
  "param1": 1,
  "param2": "text"
}
```

## Reference

- [Scheduling, Registering, and Handling Notifications](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/IPhoneOSClientImp.html)
- [Handle push notifications when arrived in ios application](http://www.abdus.me/ios-programming-tips/handle-push-notifications-when-arrived-ios/)
- [How to respond to push notification view if app is already running in the background](http://stackoverflow.com/questions/5099483/how-to-respond-to-push-notification-view-if-app-is-already-running-in-the-backgr)
- [didReceiveRemoteNotification when in background](http://stackoverflow.com/questions/5056689/didreceiveremotenotification-when-in-background)
- [How to handle remote notification with background mode enabled](http://samwize.com/2015/08/07/how-to-handle-remote-notification-with-background-mode-enabled/)
- [Remote notification method called twice](http://stackoverflow.com/questions/20569201/remote-notification-method-called-twice)
