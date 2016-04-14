---
author: onmyway133
comments: true
date: 2015-07-11 14:00:12+00:00
layout: post
slug: understanding-layout-guide
title: Understanding Layout Guide
wordpress_id: 1743
categories:
- iOS
---

## iOS 8



`topLayoutGuide` and `bottomLayoutGuide`
- is a property on UIViewController
- inherits from UIView
- conforms to UILayoutSupport protocol



### [UILayoutSupport](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UILayoutSupport_Protocol/)





<blockquote>
  In iOS 7 and later, a view controller’s view occupies the full height of the screen when there are no opaque UIKit bars (such as opaque navigation or tab bars). To keep your content within the area of a view that is not overlaid by translucent or transparent UIKit bars, employ the topLayoutGuide and bottomLayoutGuide properties in the UIViewController class and take advantage of Auto Layout. Query these properties within your implementation of the UIViewController method viewDidLayoutSubviews.
  
  The guides work as follows:
  - The top layout guide indicates the distance, in points, between the top of a view controller’s view and the bottom of the bottommost bar that overlays the view
  - The bottom layout guide indicates the distance, in points, between the bottom of a view controller’s view and the top the bar (such as a tab bar) that overlays the view.
</blockquote>





### [topLayoutGuide](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/#//apple_ref/occ/instp/UIViewController/topLayoutGuide)





<blockquote>
  The topLayoutGuide property comes into play when a view controller is frontmost onscreen. It indicates the highest vertical extent for content that you don't want to appear behind a translucent or transparent UIKit bar (such as a status or navigation bar). This property implements the UILayoutSupport protocol and you can employ it as a constraint item when using the NSLayoutConstraint class.
  
  The value of this property is, specifically, the value of the length property of the object returned when you query this property. This value is constrained by either the view controller or by its enclosing container view controller (such as a navigation or tab bar controller), as follows:
  
  A view controller not within a container view controller constrains this property to indicate the bottom of the status bar, if visible, or else to indicate the top edge of the view controller's view.
  
  A view controller within a container view controller does not set this property's value. Instead, the container view controller constrains the value to indicate:
  - The bottom of the navigation bar, if a navigation bar is visible
  - The bottom of the status bar, if only a status bar is visible
  - The top edge of the view controller's view, if neither a status bar nor navigation bar is visible
  
  If a container navigation controller's navigation bar is visible and opaque, the navigation controller lays out the frontmost view controller's view so its top edge abuts the bottom of the navigation bar. In this case, the value of this property is 0.
</blockquote>





### [bottomLayoutGuide](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/#//apple_ref/occ/instp/UIViewController/topLayoutGuide)





## iOS 9





### [UILayoutGuide](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UILayoutGuide_Class_Reference/index.html#//apple_ref/occ/cl/UILayoutGuide)



`topLayoutGuide` and `bottomLayoutGuide` 
- inherits from UILayoutGuide



<blockquote>
  They do not participate in the view hierarchy. Instead, they simply define a rectangular region in their owning view’s coordinate system that can interact with Auto Layout.
  
  Layout guides can also act as a black box, containing a number of other views and controls. This lets you encapsulate part of your view, breaking your layout into modular chunks.
</blockquote>
