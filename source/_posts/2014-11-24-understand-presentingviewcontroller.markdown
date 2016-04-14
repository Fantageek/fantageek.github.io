---
author: onmyway133
comments: true
date: 2014-11-24 14:39:21+00:00
layout: post
slug: understand-presentingviewcontroller
title: Understanding presentingViewController
wordpress_id: 1502
categories:
- iOS
---

## presentViewController:animated:completion:



You maybe familiar with

[code language="objc"]
[vc1:presentViewController:vc2 animated:YES completion:nil]
[/code]

After this vc1.presentedViewController == vc2. You may also think that vc2.presentingViewController == vc1 but this is not always the case.
When a presenting view controller happens, there maybe 3 objects



### Presented view controller



This is vc2



### Original presenter



This is vc1, and sometime called source view controller. At this stage, vc1.presentedViewController == vc2



### Presenting view controller



This is vc2.presentingViewController, but it is not always vc1. This is the view controller whose view is replaced or covered by vc2.view. It is by default the view controller whose view occupies the entire screen, it can be either root view controller or an already presented view controller. It might not be the same with vc1.

For example, the presenting view controller is the root view controller, we have rootViewController.presentedViewController == vc2. So in this example, vc2 can be a `presentedViewController` of both original presenter (vc1) and presenting view controller (root view controller)

If vc1 occupies the whole screen, original presenter and presenting view controller are the same (vc1)

In another world, vc2.presentingViewController will only point to the real presenting view controller.



## dismissViewControllerAnimated:completion:



We can send `dismissViewControllerAnimated:completion` to any of those 3 objects, the system will forward that message to the presenting view controller.

Continue with the example of the rootViewController as the real presenting view controller, if you say
[code language="objc"]
[vc2:dismissViewControllerAnimated:YES completion:nil]
[/code]

then root view controller with be the one that receives this message.



## Chain of presented view controller



A view controller can have at most 1 presentedViewController at a time. However, a presented view controller (vc2) can itself presents another view controller (vc3)

[code language="objc"]
[vc2:presentViewController:vc3 animated:YES completion:nil]
[/code]



## Presentation context



So what does it mean by "occupy the whole screen". Read [Presentation Contexts Provide the Area Covered by the Presented View Controller](https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/ModalViewControllers/ModalViewControllers.html)



<blockquote>
  The area of the screen used to define the presentation area is determined by the presentation context. By default, the presentation context is provided by the root view controller, whose frame is used to define the frame of the presentation context. However, the presenting view controller, or any other ancestor in the view controller hierarchy, can choose to provide the presentation context instead. In that case, when another view controller provides the presentation context, its frame is used instead to determine the frame of the presented view. This flexibility allows you to limit the modal presentation to a smaller portion of the screen, leaving other content visible.
  
  When a view controller is presented, iOS searches for a presentation context. It starts at the presenting view controller by reading its definesPresentationContext property. If the value of this property is YES, then the presenting view controller defines the presentation context. Otherwise, it continues up through the view controller hierarchy until a view controller returns YES or until it reaches the window’s root view controller.
  
  When a view controller defines a presentation context, it can also choose to define the presentation style. Normally, the presented view controller determines how it presented using its modalTransitionStyle property. A view controller that sets definesPresentationContext to YES can also set providesPresentationContextTransitionStyle to YES. If providesPresentationContextTransitionStyle is set to YES, iOS uses the presentation context’s modalPresentationStyle to determine how the new view controller is presented.
</blockquote>





### definesPresentationContext





<blockquote>
  When a view controller is presented, iOS starts with the presenting view controller and asks it if it wants to provide the presentation context. If the presenting view controller does not provide a context, then iOS asks the presenting view controller's parent view controller. iOS searches up through the view controller hierarchy until a view controller provides a presentation context. If no view controller offers to provide a context, the window's root view controller provides the presentation context.
  
  If a view controller returns YES, then it provides a presentation context. The portion of the window covered by the view controller's view determines the size of the presented view controller's view. The default value for this property is NO.
</blockquote>





## Reference







  1. [Programming iOS 8 by Matt Neuburg](http://shop.oreilly.com/product/0636920034261.do)


  2. [Presenting View Controllers](http://useyourloaf.com/blog/2012/10/08/presenting-view-controllers.html)


  3. [Chapter 19. View Controllers - Presented View Controller](http://www.apeth.com/iOSBook/ch19.html#_presented_view_controller)


