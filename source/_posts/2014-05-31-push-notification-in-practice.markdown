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

Here are some notes for working with Push Notification

## How to register

- [iOS 8 Push Notification Registration](http://www.joshwright.com/tips/ios-8-push-notification-registration)

## When to register

- [Registering, Scheduling, and Handling User Notifications](https://developer.apple.com/library/mac/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/IPhoneOSClientImp.html)

> Never cache a device token; always get the token from the system whenever you need it. If your app previously registered for remote notifications, calling the registerForRemoteNotifications method again does not incur any additional overhead, and iOS returns the existing device token to your app delegate immediately. In addition, iOS calls your delegate method any time the device token changes, not just in response to your app registering or re-registering

> The user can change the notification settings for your app at any time using the Settings app. Because settings can change, always call the registerUserNotificationSettings: at launch time and use the application:didRegisterUserNotificationSettings: method to get the response. If the user disallows specific notification types, avoid using those types when configuring local and remote notifications for your app.

## How to handle

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


### Case 1

Your app is running in the foreground (user is interacting with your app). No notification banner appears, instead `application:didReceiveRemoteNotification:`` is called with userInfo containing the payload


### Case 2

Your app is running in the background (user is interacting with another app or the phone is locked). Notification banner appears.

- Case 2a: User taps on the Notification banner and select Close: Nothing happens
- Case 2b: User taps on the Notification banner and select View: Your app is opened and application:didReceiveRemoteNotification: is called with userInfo containing the payload
- Case 2c: User taps directly on your app icon: Your app is just opened and no method is called



#### Case 3

Your app is terminated (User kill your app or it is killed by the OS).

- Case 3a: User taps on the Notification banner and select Close: Nothing happens
- Case 3b: User taps on the Notification banner and select View: Your app is launched and application:didFinishLaunchingWithOptions: is called with launchOptions[UIApplicationLaunchOptionsRemoteNotificationKey] containing the payload
- Case 3c: User taps on directly on your app icon: Your app is launched and application:didFinishLaunchingWithOptions: is called with launchOptions[UIApplicationLaunchOptionsRemoteNotificationKey] being nil

In case 2a, 2c, 3a, 3c there is no way for your app to know about this payload

Having acknowledge of those cases is useful but usually we just have to distinct between the 2 states, as you see in the handlePushNotification method. They are
- When the app is in foreground
- When user is brought from background to foreground, or from terminated to foreground

Read more about [UIApplicationState here](https://developer.apple.com/library/ios/documentation/uikit/reference/UIApplication_Class/Reference/Reference.html#//apple_ref/doc/c_ref/UIApplicationState)

Usually, the use of push notification is to display a specific article, a specific DetailViewController, ... in your app. So the good practices are
- When the app is in foreground: Gently display some kind of alert view and ask the user whether he would like to go to that specific page or not
- When user is brought from background to foreground, or from terminated to foreground: Just navigate to that specific page. For example, if you use UINavigationController, you can set that specific page the top most ViewController, if you use UITabBarController, you can set that specific page the selected tab, something like that

### Some refactoring

Putting everything in your appDelegate is not a good idea. You have to create several new classes
- PushNotification: this is your model to present the JSON received
- PushNotificationParser: to parse JSON to your model
- PushNotificationFilter: to filter only the interested payload
- PushNotificationManager: to register, receive and handle the push notification



### UrbanAirship

If you use UrbanAirship to manage push notification, I highly recommend
- [AFUrbanAirshipClient](https://github.com/mattt/AFUrbanAirshipClient) for old projects. It uses AFNetworking version 1
- [Orbiter](https://github.com/mattt/Orbiter) for new projects. It uses AFNetworking version 2



### Some quotes to note

From [Handling Local and Remote Notifications](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/IPhoneOSClientImp.html#//apple_ref/doc/uid/TP40008194-CH103-SW1)

> If the action button is tapped (on a device running iOS), the system launches the application and the application calls its delegate’s application:didFinishLaunchingWithOptions: method (if implemented); it passes in the notification payload (for remote notifications) or the local-notification object (for local notifications).

> If the application icon is tapped on a device running iOS, the application calls the same method, but furnishes no information about the notification . If the application icon is clicked on a computer running OS X, the application calls the delegate’s applicationDidFinishLaunching: method in which the delegate can obtain the remote-notification payload

From [application:didReceiveRemoteNotification:](https://developer.apple.com/library/ios/documentation/uikit/reference/uiapplicationdelegate_protocol/Reference/Reference.html)

> If the app is not running when a push notification arrives, the method launches the app and provides the appropriate information in the launch options dictionary. The app does not call this method to handle that push notification. Instead, your implementation of the application:willFinishLaunchingWithOptions: or application:didFinishLaunchingWithOptions: method needs to get the push notification payload data and respond appropriately.

> If your delegate also implements the application:didReceiveRemoteNotification:fetchCompletionHandler: method, the app object calls that method instead of this one.

By "no running" here, Apple really means "your app is terminated". Because "no running" also means that your app is in background

### Reference

1. [Scheduling, Registering, and Handling Notifications](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/IPhoneOSClientImp.html)
2. [Handle push notifications when arrived in ios application](http://www.abdus.me/ios-programming-tips/handle-push-notifications-when-arrived-ios/)
3. [How to respond to push notification view if app is already running in the background](http://stackoverflow.com/questions/5099483/how-to-respond-to-push-notification-view-if-app-is-already-running-in-the-backgr)
4. [didReceiveRemoteNotification when in background](http://stackoverflow.com/questions/5056689/didreceiveremotenotification-when-in-background)
