---
author: onmyway133
comments: true
date: 2014-06-27 14:38:49+00:00
layout: post
slug: force-only-one-view-controller-to-be-landscape
title: Force only one View Controller to be landscape
wordpress_id: 1085
categories:
- iOS
tags:
- landscape
- orientation
- present
- view controller
---

In my app, I only want portrait mode in iPhone and landscape mode in iPad, except for the Movie Player View Controller which must always be in landscape for both iPhone and iPad
Here are the solutions together with my experience

Suppose MainViewController is the window.rootViewController and the MoviePlayerViewController is displayed via presentation
<!-- more -->



#### Support all posible orientation



Because your app support portrait (in every View Controller) and landscape (in Movie Player View Controller), so make sure you declare that your app ability to support those orientation



##### Specify in Info.plist



You can specify in Targets panel -> General tab -> Deployment Info section -> Device Orientation
[![Screen Shot 2014-06-27 at 9.12.31 PM](http://www.fantageek.com/wp-content/uploads/2014/06/Screen-Shot-2014-06-27-at-9.12.31-PM-300x105.png)](http://www.fantageek.com/wp-content/uploads/2014/06/Screen-Shot-2014-06-27-at-9.12.31-PM.png)
Note that it actually edit your Info.plist, which will looks like this

```xml
<array>
		<string>UIInterfaceOrientationPortrait</string>
		<string>UIInterfaceOrientationLandscapeLeft</string>
		<string>UIInterfaceOrientationLandscapeRight</string>
</array>
```



##### Specify in AppDegate



Implement application:supportedInterfaceOrientationsForWindow:
Note that this override your Info.plist. This is a more dynamic way as you can change the supported orientation during runtime



#### MainViewController.m



[code language="objc"]
- (BOOL)shouldAutorotate
{
    return YES;
}

- (NSUInteger)supportedInterfaceOrientations
{

    if (IS_IPAD) {
        return UIInterfaceOrientationMaskLandscape;
    }

    // Do not return UIInterfaceOrientationPortrait
    return UIInterfaceOrientationMaskPortrait;
}

- (void)displayMoviePlayerController
{
	[self presentViewController:moviePlayerViewController animated:YES completion:nil];
}
```



#### MoviePlayerViewController



[code language="objc"]
- (BOOL)shouldAutorotate
{
	return YES;
}

- (NSUInteger)supportedInterfaceOrientations
{
	return UIInterfaceOrientationMaskLandscape;
}

- (UIInterfaceOrientation)preferredInterfaceOrientationForPresentation
{
	return UIInterfaceOrientationLandscapeRight;
}

- (void)dismiss
{
	[self dismissViewControllerAnimated:YES completion:nil];
}
```



#### Note







  1. Look for every detail, I have overlook the enum and it wasted me an hour. You should return what the API asks
In Info.plist, the enum is of type UIInterfaceOrientation
In supportedInterfaceOrientations, the enum is of type UIInterfaceOrientationMask
In preferredInterfaceOrientationForPresentation, the enum is of type UIInterfaceOrientation
In application:supportedInterfaceOrientationsForWindow:, the num is of type UIInterfaceOrientationMask



  2. When the your ViewController ALREADY appears and user rotate the device,  supportedInterfaceOrientations is only called if shouldAutorotate returns YES. The default return value of shouldAutorotate is YES



  3. supportedInterfaceOrientations is called on the root View Controller or the top most View Controller that presents full screen. You should read more about **presentation context**






<blockquote>When the user changes the device orientation, the system calls this method on the root view controller or the topmost presented view controller that fills the window. If the view controller supports the new orientation, the window and view controller are rotated to the new orientation.</blockquote>







  1. The value returned by supportedInterfaceOrientations is intersected with the value return in application:supportedInterfaceOrientationsForWindow: (or Info.plist if you don't implement this AppDelegate method), if the result is empty, you 'll get a crash





<blockquote>The system intersects the view controller’s supported orientations with the app's supported orientations (as determined by the Info.plist file or the app delegate's application:supportedInterfaceOrientationsForWindow: method) to determine whether to rotate.</blockquote>







  1. When the ViewController first appear, shouldAutorotate and supportedInterfaceOrientations are called (many times), I don't know why !!!??





#### Reference







  1. [Forcing UIViewController orientation](http://b2cloud.com.au/tutorial/forcing-uiviewcontroller-orientation/)


  2. [Supported orientations has no common orientation with the application, and shouldAutorotate is returning YES](http://stackoverflow.com/a/14644218/1418457)


  3. [Presentation Contexts Provide the Area Covered by the Presented View Controller](https://developer.apple.com/library/ios/featuredarticles/viewcontrollerpgforiphoneos/ModalViewControllers/ModalViewControllers.html)


  4. Chapter 6: ViewController and section Rotation of the book [Programming iOS 7 4th Edtion](http://shop.oreilly.com/product/0636920031017.do)
