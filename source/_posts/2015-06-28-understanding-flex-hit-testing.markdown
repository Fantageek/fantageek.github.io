---
author: onmyway133
comments: true
date: 2015-06-28 15:25:04+00:00
layout: post
slug: understanding-flex-hit-testing
title: Understanding FLEX - Hit testing
wordpress_id: 1723
categories:
- iOS
---

We can't touch through FLEX 's bar to its underneath content. It's a good thing to learn



### FLEXWindow.m



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



### FLEXManager.m



```objc
- (BOOL)shouldHandleTouchAtPoint:(CGPoint)pointInWindow
{
    // Ask the explorer view controller
    return [self.explorerViewController shouldReceiveTouchAtWindowPoint:pointInWindow];
}
```



### FLEXExplorerViewController.m



```objc
- (BOOL)shouldReceiveTouchAtWindowPoint:(CGPoint)pointInWindowCoordinates
{
    BOOL shouldReceiveTouch = NO;

    CGPoint pointInLocalCoordinates = [self.view convertPoint:pointInWindowCoordinates fromView:nil];

    // Always if it's on the toolbar
    if (CGRectContainsPoint(self.explorerToolbar.frame, pointInLocalCoordinates)) {
        shouldReceiveTouch = YES;
    }

    // Always if we're in selection mode
    if (!shouldReceiveTouch && self.currentMode == FLEXExplorerModeSelect) {
        shouldReceiveTouch = YES;
    }

    // Always in move mode too
    if (!shouldReceiveTouch && self.currentMode == FLEXExplorerModeMove) {
        shouldReceiveTouch = YES;
    }

    // Always if we have a modal presented
    if (!shouldReceiveTouch && self.presentedViewController) {
        shouldReceiveTouch = YES;
    }

    return shouldReceiveTouch;
}
```



## Reference







  * [FLEX](https://github.com/flipboard/flex)
