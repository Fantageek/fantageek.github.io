---
author: onmyway133
comments: true
date: 2015-06-28 15:28:44+00:00
layout: post
slug: understanding-flex-orientation
title: Understanding FLEX - Orientation
wordpress_id: 1726
categories:
- iOS
---

FLEX 's bar always follow the orientation of your app. To do that, FLEX custom UIWindow 's rootViewController must ask your app for orientation and status bar style



### FLEXExplorerViewController.m



This is the core method, it finds the correct viewController to ask
```objc
// Try to get the preferred status bar properties from the app's root view controller (not us).
// In general, our window shouldn't be the key window when this view controller is asked about the status bar.
// However, we guard against infinite recursion and provide a reasonable default for status bar behavior in case our window is the keyWindow.

- (UIViewController *)viewControllerForStatusBarAndOrientationProperties
{
    UIViewController *viewControllerToAsk = [[[UIApplication sharedApplication] keyWindow] rootViewController];

    // On iPhone, modal view controllers get asked
    if ([[UIDevice currentDevice] userInterfaceIdiom] == UIUserInterfaceIdiomPhone) {
        while (viewControllerToAsk.presentedViewController) {
            viewControllerToAsk = viewControllerToAsk.presentedViewController;
        }
    }

    return viewControllerToAsk;
}
```

For orientation, it simply asks the found viewController

```objc
- (NSUInteger)supportedInterfaceOrientations
{
    UIViewController *viewControllerToAsk = [self viewControllerForStatusBarAndOrientationProperties];
    NSUInteger supportedOrientations = [FLEXUtility infoPlistSupportedInterfaceOrientationsMask];
    if (viewControllerToAsk && viewControllerToAsk != self) {
        supportedOrientations = [viewControllerToAsk supportedInterfaceOrientations];
    }

    // The UIViewController docs state that this method must not return zero.
    // If we weren't able to get a valid value for the supported interface orientations, default to all supported.
    if (supportedOrientations == 0) {
        supportedOrientations = UIInterfaceOrientationMaskAll;
    }

    return supportedOrientations;
}
```



## Reference







  * [FLEX](https://github.com/flipboard/flex)
