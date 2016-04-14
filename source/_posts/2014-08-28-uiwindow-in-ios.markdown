---
author: onmyway133
comments: true
date: 2014-08-28 16:05:57+00:00
layout: post
slug: uiwindow-in-ios
title: UIWindow in iOS
wordpress_id: 1317
categories:
- iOS
tags:
- alert
- ios
- level
- uiwindow
---

In this article, I 'll share what I know about UIWindow



keyWindow
--


An app can have many UIWindow. The key window is the one that is designated to receive keyboard and other non-touch related events. Only one window at a time may be the key window.

You call makeKeyAndVisible or makeKeyWindow to make a UIWindow become the keyWindow. Note that UIWindow is hidden by default, so makeKeyAndVisible both makes a UIWindow become keyWindow and set its hidden property to NO

<!-- more -->



UIWindow is always portrait
--


Simply add the following code in application:didFinishLaunchingWithOptions:

```objc
UIView *view = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 100, 100)];
    view.backgroundColor = [UIColor greenColor];
    view.autoresizingMask = UIViewAutoresizingFlexibleRightMargin | UIViewAutoresizingFlexibleBottomMargin;
    [self.window addSubview:view];
    view.layer.zPosition = FLT_MAX;
```

{% raw %}
then rotate the device/simulator, you will see that the green color view is always at position {0, 0},
{% endraw %}

[![Screen Shot 2014-08-31 at 8.32.39 PM](http://www.fantageek.com/wp-content/uploads/2014/08/Screen-Shot-2014-08-31-at-8.32.39-PM-190x300.png)](http://www.fantageek.com/wp-content/uploads/2014/08/Screen-Shot-2014-08-31-at-8.32.39-PM.png)
[![Screen Shot 2014-08-31 at 8.32.43 PM](http://www.fantageek.com/wp-content/uploads/2014/08/Screen-Shot-2014-08-31-at-8.32.43-PM-300x215.png)](http://www.fantageek.com/wp-content/uploads/2014/08/Screen-Shot-2014-08-31-at-8.32.43-PM.png)

The UIWindow's coordinate system is always in portrait orientation. It applies the rotation by setting its rootViewController's view's transform. So starting in iOS 4, application is advised to have rootViewController. See this wonderful answer from rob [After rotation UIView coordinates are swapped but UIWindow's are not?](http://stackoverflow.com/a/8598515/1418457)



Keyboard is a window
--


Note that UIApplication has an instance method "windows"



> The app's visible and hidden windows. (read-only)
This property contains the UIWindow objects currently associated with the app. This list does not include windows created and managed by the system, such as the window used to display the status bar



You can get the keyboard window by traversing this windows array. Code extracted from SVProgressHUD. Note that when the keyboard is shown, the windows array contains another window of type UITextEffectsWindow, that is the keyboard

```objc
- (CGFloat)visibleKeyboardHeight {

    UIWindow *keyboardWindow = nil;
    for (UIWindow *testWindow in [[UIApplication sharedApplication] windows]) {
        if(![[testWindow class] isEqual:[UIWindow class]]) {
            keyboardWindow = testWindow;
            break;
        }
    }

    for (__strong UIView *possibleKeyboard in [keyboardWindow subviews]) {
        if([possibleKeyboard isKindOfClass:NSClassFromString(@"UIPeripheralHostView")] || [possibleKeyboard isKindOfClass:NSClassFromString(@"UIKeyboard")])
            return possibleKeyboard.bounds.size.height;
    }

    return 0;
}
```

As said previously, The UIWindow's coordinate system is always in portrait orientation. Keyboard is a window, so its frame is always (0 0; 320 480) regardless of device orientation. When you rotate the device, the system applies a rotation transform to the keyboard window

User rotates the device to portrait

```objc
<UITextEffectsWindow: 0x8e3ecc0; frame = (0 0; 320 480); opaque = NO; gestureRecognizers = <NSArray: 0x8e3f240>; layer = <UIWindowLayer: 0x8e3ee40>>
```

User rotates the device to landscape
```objc
<UITextEffectsWindow: 0x8e3ecc0; frame = (0 0; 320 480); transform = [0, 1, -1, 0, -80, 80]; opaque = NO; gestureRecognizers = <NSArray: 0x8e3f240>; layer = <UIWindowLayer: 0x8e3ee40>>
```



Notification
--


If you register for UIKeyboardWillShowNotification, you can confirm this

```objc
[[NSNotificationCenter defaultCenter] addObserverForName:UIKeyboardWillShowNotification object:nil queue:nil usingBlock:^(NSNotification *note) {
        NSLog(@"%@", note);
    }];
```


User rotates the device to portrait
{% raw %}
UIKeyboardFrameEndUserInfoKey = "NSRect: {{0, 264}, {320, 216}}";
{% endraw %}

User rotates the device to landscape
{% raw %}
UIKeyboardFrameEndUserInfoKey = "NSRect: {{0, 0}, {162, 480}}";
{% endraw %}


So in your UIKeyboardWillShowNotification handler, you should convert it to your view coordinate system. Code extracted from [Keyboard “WillShow” and “WillHide” vs. Rotation](http://stackoverflow.com/a/14351009/1418457)

```objc
- (void) keyboardWillShow:(NSNotification *)aNotification
{
     CGRect keyboardFrame = [[[aNotification userInfo] objectForKey:UIKeyboardFrameBeginUserInfoKey] CGRectValue];
    CGRect convertedFrame = [self.view convertRect:keyboardFrame fromView:self.view.window];

    ......
    /* Do whatever you want now with the new frame.
     * The width and height will actually be correct now
     */
    ......
}
```



Statusbar is a window
--


As said previously, the windows array does not contain the statusbar window. Statusbar window is of type UIStatusBarWindow, you can get it with the following code (extracted from FLEX)

```objc
- (UIWindow *)statusWindow
{
    NSString *statusBarString = [NSString stringWithFormat:@"_statusBarWindow"];
    return [[UIApplication sharedApplication] valueForKey:statusBarString];
}
```

Like the keyboard window, the status bar frame is unchanged. When you rotate the device, the system applies rotation transform to the status bar

User rotates the device to portrait
```objc
<UIStatusBarWindow: 0x8f61e30; frame = (0 0; 320 480); gestureRecognizers = <NSArray: 0x8f62e40>; layer = <UIWindowLayer: 0x8f620d0>>
```

User rotates the device to landscape
```objc
<UIStatusBarWindow: 0x8f61e30; frame = (0 0; 320 480); transform = [0, 1, -1, 0, 0, 0]; gestureRecognizers = <NSArray: 0x8f62e40>; layer = <UIWindowLayer: 0x8f620d0>>
```



statusBarFrame
--


UIApplication has a property called statusBarFrame, which is always reported in portrait coordinate system.

```objc
CGRect rect = [UIApplication sharedApplication].statusBarFrame;
NSLog(@"statusBarFrame %@", NSStringFromCGRect(rect));
```


User rotates the device to portrait
{% raw %}
statusBarFrame {{0, 0}, {320, 20}}

User rotates the device to landscape
statusBarFrame {{300, 0}, {20, 480}}
{% endraw %}


If you merely want to get the status height, regardless of orientation, you can just get the max

```objc
float statusBarHeight = MIN([UIApplication sharedApplication].statusBarFrame.size.height, [UIApplication sharedApplication].statusBarFrame.size.width);
```



Notification
--


When the device is rotated, the system issues UIApplicationWillChangeStatusBarFrameNotification and UIApplicationDidChangeStatusBarFrameNotification.

Let's now handle UIApplicationDidChangeStatusBarFrameNotification to see

```objc
[[NSNotificationCenter defaultCenter] addObserverForName:UIApplicationWillChangeStatusBarFrameNotification object:nil queue:nil usingBlock:^(NSNotification *note) {
        NSLog(@"%@", note);
    }];

    [[NSNotificationCenter defaultCenter] addObserverForName:UIApplicationDidChangeStatusBarFrameNotification object:nil queue:nil usingBlock:^(NSNotification *note) {
        NSLog(@"%@", note);
    }];
```

I don't know why but the WillChange notification gives me the final result, while the DidChange notification give me the old result. By result, I mean the value of the statusBarFrame
I also see that no libraries use the DidChange notification



How to create alert view
--


To create an alertview, you can simply present an UIViewController (with smaller size). For advanced cases, you must understand the presentation context. And the way to use your alertview is a bit hard. What we always want is a simple call

```objc
[alertView show];
```

It 's not to say that some view can appears on top of your alert view, so they defeats your purpose as an alertview

The fancy way is to work with UIWindow. I see many libraries use one of these 2 approaches



Don't use rootViewController approach
--


Some libraries like OLGhostAlertView, SVProgressHUD, WYPopoverController, MTStatusBarOverlay ... don't use rootViewController. They can create new UIWindow (MTStatusBarOverlay) or use existing UIWindow, they addSubview directly to the window, so they handle orientation by listen to UIApplicationDidChangeStatusBarOrientationNotification or UIApplicationWillChangeStatusBarFrameNotification

The handler is like this, extracted from SVProgressHUD. The idea is manually get the orientation and applies a rotation transform to their view

```objc
- (void)handleOrientationChange:(NSNotification *)note
{
    UIInterfaceOrientation orientation = [[UIApplication sharedApplication] statusBarOrientation];

    CGRect orientationFrame = [UIScreen mainScreen].bounds;
    CGRect statusBarFrame = [UIApplication sharedApplication].statusBarFrame;

    if(UIInterfaceOrientationIsLandscape(orientation)) {
        float temp = orientationFrame.size.width;
        orientationFrame.size.width = orientationFrame.size.height;
        orientationFrame.size.height = temp;

        temp = statusBarFrame.size.width;
        statusBarFrame.size.width = statusBarFrame.size.height;
        statusBarFrame.size.height = temp;
    }

    switch (orientation) {
        case UIInterfaceOrientationPortraitUpsideDown:
            rotateAngle = M_PI;

            break;
        case UIInterfaceOrientationLandscapeLeft:
            rotateAngle = -M_PI/2.0f;

            break;
        case UIInterfaceOrientationLandscapeRight:
            rotateAngle = M_PI/2.0f;

            break;
        default: // as UIInterfaceOrientationPortrait
            rotateAngle = 0.0;

            break;
    }

}
```



How to add view to existing window
--


Libraries have their own strategy to add view to existing window

SVProgressHUD tries to add to the front window
```objc
if(!self.overlayView.superview){
        NSEnumerator *frontToBackWindows = [[[UIApplication sharedApplication]windows]reverseObjectEnumerator];

        for (UIWindow *window in frontToBackWindows)
            if (window.windowLevel == UIWindowLevelNormal) {
                [window addSubview:self.overlayView];
                break;
            }
    }
```



How the OS creates AlertView
--


Watch WWDC 2014 Session 228 A Look Inside Presentation Controllers, they said



> Behind the scenes, the framework creates a window on your app's behalf, but this predates iOS 8 window rotation behavior, so this window is technically still in portrait.

We then add the action sheet to that window and mimic the transform hierarchy of the presenting view to get into the right orientation.





Use rootViewController approach
--


Some libraries, like FLEX, SIAlertView, ... create new UIWindow and assign rootViewController to that window. This way orientation is reported to the rootViewController. They then simply add view to the rootViewController.view and handle orientation right in the view controller.

Code extracted from FLEXViewExplorerViewController
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

- (BOOL)shouldAutorotate
{
    UIViewController *viewControllerToAsk = [self viewControllerForStatusBarAndOrientationProperties];
    BOOL shouldAutorotate = YES;
    if (viewControllerToAsk && viewControllerToAsk != self) {
        shouldAutorotate = [viewControllerToAsk shouldAutorotate];
    }
    return shouldAutorotate;
}

- (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
{
    [...]
}

- (void)didRotateFromInterfaceOrientation:(UIInterfaceOrientation)fromInterfaceOrientation
{
    [...]
}
```

Since FLEX creates new Window, it must consults the app original Window for rotation capability. Note the infoPlistSupportedInterfaceOrientationsMask and viewControllerForStatusBarAndOrientationProperties, you can learn much from it



UIWindow is a UIView
--


Since UIWindow is a UIView subclass, you can do many things you already know with UIView. For example, FLEX override pointInside:withEvent: to intercept touch through its toolbar

```objc
- (BOOL)pointInside:(CGPoint)point withEvent:(UIEvent *)event
{
    BOOL pointInside = NO;
    if ([self.eventDelegate shouldHandleTouchAtPoint:point]) {
        pointInside = [super pointInside:point withEvent:event];
    }
    return pointInside;
}
```



UIWindow in iOS 8
--


In iOS 8, Apple introduce Size Classes, and "Rotation is an animated bounds change". And UIWindow is reported in device orientation

WWDC 2014 Session 216 Building Adaptive Apps with UIKit



> Trait Environments are a new protocol that are able to return their current Trait Collection, and these include Screens, Windows, View Controllers, and also Views.

All of these are able to return their current Trait Collection to you to use to determine how your interface should be laid out.



See [Is [UIScreen mainScreen].bounds.size becoming orientation-dependent in iOS8?](http://stackoverflow.com/questions/24150359/is-uiscreen-mainscreen-bounds-size-becoming-orientation-dependent-in-ios8/24153540#24153540)

UIScreen is now interface oriented:





  * [UIScreen bounds] now interface-oriented


  * [UIScreen applicationFrame] now interface-oriented


  * Status bar frame notifications are interface-oriented


  * Keyboard frame notifications are interface-oriented





Reference
--






  1. [Multiple UIWindows](http://b2cloud.com.au/tutorial/multiple-uiwindows/)


  2. [To create a new UIWindow over the main window](http://stackoverflow.com/questions/19995526/to-create-a-new-uiwindow-over-the-main-window)


  3. [Advantages, problems, examples of adding another UIWindow to an iOS app?](http://stackoverflow.com/questions/8232398/advantages-problems-examples-of-adding-another-uiwindow-to-an-ios-app)


  4. [The difference between a UIWindow and a UIView](http://stackoverflow.com/questions/8233976/the-difference-between-a-uiwindow-and-a-uiview)


  5. [Using multiple UIWindows in iOS applications](http://shaune.com.au/using-multiple-uiwindows-in-ios-applications/)


  6. [Technical Q&A QA1688 Why won't my UIViewController rotate with the device?](https://developer.apple.com/library/ios/qa/qa1688/_index.html)


  7. [After rotation UIView coordinates are swapped but UIWindow's are not?](http://stackoverflow.com/questions/8598315/after-rotation-uiview-coordinates-are-swapped-but-uiwindows-are-not)


  8. [WWDC 2014 Session 214 View Controller Advancement in iOS 8](https://developer.apple.com/videos/wwdc/2014/)
