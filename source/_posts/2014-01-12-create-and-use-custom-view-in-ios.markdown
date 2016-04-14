---
author: onmyway133
comments: true
date: 2014-01-12 03:57:18+00:00
layout: post
slug: create-and-use-custom-view-in-ios
title: Create and use custom view in iOS
wordpress_id: 646
categories:
- iOS
tags:
- 'Warning: error_log() has been disabled for security reasons in /home/fantagee/public_html/wp-includes/wp-db.php
  on line 1200'
---

This maybe old to most of you, but completely new to me, I just met iOS several months ago and it is a little late.




The problem today is how to do with UI reusability. During the development, we find the need to create custom views and want to reuse them everywhere we need to, whether in code or in another nib. This post is particular for the use with Nib, but I'll recap the use with code as well, and from a pragmatic point of view :D




The example will be about creating a Custom View (containing a Button and a Label) and reuse that custom view in the View Controller




<!-- more -->




## CREATE CUSTOM VIEW IN CODE

```objc
@interface CodeCustomView : UIView
@property (nonatomic, weak) UIButton *button;
@property (nonatomic, weak) UILabel *label;
@end
@implementation CodeCustomView
- (void)baseInit
{
    UIButton *button = [UIButton buttonWithType:UIButtonTypeSystem];
    UILabel *label = [[UILabel alloc] init];
    [self addSubview:button];
    [self addSubview:label];
    self.button = button;
    self.label = label;
}
- (id)initWithFrame:(CGRect)frame
{
    self = [super initWithFrame:frame];
    if (self) {
        [self baseInit];
    }
    return self;
}
- (id)initWithCoder:(NSCoder *)aDecoder
{
    self = [super initWithCoder:aDecoder];
    if (self) {
        [self baseInit];
    }
    return self;
}
```

Here the baseInit acts as init helper method, this is where the Button and Label get allocated. The initWithFrame and initWithCoder allows the CodeCustomView to be used in code and xib as well.




### USE CodeCustomView IN CODE




In the ViewController 's viewDidLoad method



```objc
- (void)viewDidLoad
{
    [super viewDidLoad];
    CodeCustomView *codeCustomView = [[CodeCustomView alloc] initWithFrame:CGRectMake(0, 0, 100, 100)];
    NSAssert(codeCustomView.button, @"");
    NSAssert(codeCustomView.label, @"");
    self.codeCustomView = codeCustomView;
    [self.view addSubview:codeCustomView];
}
```


> Every new view object you define should include a custom initWithFrame: initializer method. This method is responsible for initializing the class at creation time and putting your view object into a known state. You use this method when creating instances of your view programmatically in your code.





The CodeCustomView 's initWithFrame method is called, and its baseInit is called. It is the baseInit that the Button and Label get allocated, hence the NSAssert will succeed




### USE CodeCustomView IN NIB




> When you create a view controller in a storyboard, the attributes you configure in Interface Builder are serialized into an archive. Later, when the view controller is instantiated, this archive is loaded into memory and processed. The result is a set of objects whose attributes match those you set in Interface Builder. The archive is loaded by calling the view controller’s initWithCoder: method. Then, the awakeFromNib method is called on any object that implements that method. You use this method to perform any configuration steps that require other objects to already be instantiated




Do these steps to add the CodeCustomView to the ViewController view in Nib






  1. Open ViewController.xib


  2. Drag a UIView to the ViewController's view


  3. With that UIView selected, open Identity Inspector and specify CodeCustomView as its custom class


  4. Connect that UIView (CodeCustomView) to the ViewController 's codeCustomView property




For the first time the ViewController 's view is accessed, the ViewController will unarchive its associated ViewController.xib file, use NSCoder mechanism to create the UI objects in that Xib file. In this process, the CodeCustomView 's initWithCoder method is called




You can check whether the codeCustomView is OK in the ViewController 's viewDidLoad method, which is called when the ViewController 's view (and its subviews) is completely loaded from the Xib file




```objc
- (void)viewDidLoad
{
    [super viewDidLoad];
    NSAssert(self.codeCustomView.button, @"");
    NSAssert(self.codeCustomView.label, @"");
}
```


Wonder why the NSAssert succeeded ?




> If you plan to load instances of your custom view class from a nib file, you should be aware that in iOS, the nib-loading code does not use the initWithFrame: method to instantiate new view objects. Instead, it uses the initWithCoder: method that is part of the NSCoding protocol.

> Even if your view adopts the NSCoding protocol, Interface Builder does not know about your view’s custom properties and therefore does not encode those properties into the nib file. As a result, your own initWithCoder: method should perform whatever initialization code it can to put the view into a known state. You can also implement the awakeFromNib method in your view class and use that method to perform additional initialization





The Nib loading process is something like this (from pragmatic point of view)






  1. Create the CodeCustomView


  2. Call its initWithCoder


  3. Try to create objects Subview in the UIView, but since the UIView is empty, nothing happens




Since the CodeCustomView 's button and label properties are consider "custom properties", in the CodeCustomView 's initWithCoder method, we call baseInit to allocate those properties




Remember that we added a blank UIView and specified CodeCustomView as its custom class. That blank UIView makes the Nib loading process create a "placeholder" CodeCustomView object that contains nothing. But with intercepting the initWithCoder to call the baseInit, the Button and Label get allocated.




## CREATE CUSTOM VIEW IN XIB




To create Custom View in Xib, we first declare a custom class and specify that class for the Xib






  1. Go to File -> New File -> Cocoa Touch -> Objective C class


  2. Create a NibCustomView class which is a subclass of UIView


  3. Declare the properties to be connected in Xib




```objc
@interface NibCustomView : UIView
@property (nonatomic, weak) IBOutlet UIButton *button;
@property (nonatomic, weak) IBOutlet UILabel *label;
@end
@implementation NibCustomView
- (id)initWithCoder:(NSCoder *)aDecoder
{
    self = [super initWithCoder:aDecoder];
    if (self ) {
    }
    return self;
}
@end
```




  1. Go to File -> New -> File -> User Interface -> Empty (we don't need Files's owner)


  2. Name that NibCustomView.xib


  3. Drag the UIView to the canvas, specify NibCustomView as its custom class


  4. Drag UIButton and UILabel to that UIView


  5. Connect those UIButton and UILabel to the properties of the NibCustomView class




### USE NibCustomView IN CODE




The sample code to create NibCustomView in code is below, you may already know that





```objc
- (void)viewDidLoad
{
    [super viewDidLoad];
    NSArray *elementsInNib = [[NSBundle mainBundle] loadNibNamed:@"NibCustomView" owner:Nil options:nil];
    NibCustomView *nibCustomView = [elementsInNib lastObject];
    self.nibCustomView = nibCustomView;
    [self.view addSubview:nibCustomView];
}
```


The Nib loading process for the NibCustomView is something like this






  1. Create the NibCustomView


  2. Call its initWithCoder method


  3. Create the UILabel and UIButton, and connect them to the previous created NibCustomView 's properties




### USE NibCustomView IN NIB




Now comes to the hard part and the main reason for this post. I consider it hard because it doesn't seem ease at first (compared to how I usually use Custom View in Android and Windows Phone)




To add the NibCustomView to the ViewController view in Nib, we usually do this






  1. Open ViewController.xib


  2. Drag a UIView as a subview of the ViewController 's view


  3. Specify NibCustomView as a custom class for that UIView


  4. Connect that UIView to the nibCustomView IBOutlet property of the ViewController




Now inspect whether it works



```objc
- (void)viewDidLoad
{
    [super viewDidLoad];
    NSAssert(self.nibCustomView.button, @"");
    NSAssert(self.nibCustomView.label, @"");
}
```


The NSAssert will fail. Why ?




As previously said, when the ViewController 's view is accessed for the first time, it will trigger the ViewController 's Nib loading process to create the view for it, and the NibCustomView is created in this process, but not the way we want, let's see






  1. This Nib loading process create the NibCustomView object


  2. Calls its initWithCoder method


  3. Do nothing (it doesn't deal with the NibCustomView.xib, but with the blank UIView we just added to the ViewController's view, that's why the Button and Label properties are nil)




We call this NibCustomView object "placeholder", since it is created with a blank UIView. We want the "real thing", which is created with its according Nib file




The solution here is to use **awakeAfterUsingCoder**, which is called after initWithCoder




>Overridden by subclasses to substitute another object in place of the object that was decoded and subsequently received this message.




```objc
- (id)awakeAfterUsingCoder:(NSCoder *)aDecoder
{
    if ([self.subviews count] == 0) { // (1)
        NSArray *elementsInNib = [[NSBundle mainBundle] loadNibNamed:@"NibCustomView" owner:Nil options:nil]; // (2)
        NibCustomView *realThing = [elementsInNib lastObject];
        realThing.frame = self.frame; // (3)
        realThing.autoresizingMask = self.autoresizingMask;
        return realThing;
    }
    return self;
}
```


Here self is the "placeholder" object that the Nib loading process created, we want to substitute it with "realThing"






  1. (1) This check is to avoid infinite recursive call to awakeAfterUsingCoder, because loadNibNamed also calls awakeAfterUsingCoder. When the ViewController 's Nib loading process call this awakeAfterUsingCoder, the "placeholder" 's subviews count is 0


  2. (2) the loadNibNamed agains call awakeAfterUsingCoder, but the realThing 's subviews is 2 (there are Button and Label). This way awakeAfterUsingCoder is guaranteed to be called once


  3. (3) The "placeholder" object does not contains any subviews, but during the Nib loading process, the ViewController assigns to it frame, autoresizingMask, ...These properties are useful, we need to reassign them to realThing




Try to check again




```objc
- (void)viewDidLoad
{
    [super viewDidLoad];
    NSAssert(self.nibCustomView.button, @"");
    NSAssert(self.nibCustomView.label, @"");<
}
```


All NSAssert succeed. Yeah !




## More talkative




### awakeFromNib




This method will be called for both "placeholder" and "real thing", in this order






  1. initWithCoder


  2. awakeAfterUsingCoder


  3. awakeFromNib




> The nib-loading infrastructure sends an awakeFromNib message to each object recreated from a nib archive, but only after all the objects in the archive have been loaded and initialized. When an object receives an awakeFromNib message, it is guaranteed to have all its outlet and action connections already established.

> You must call the super implementation of awakeFromNib to give parent classes the opportunity to perform any additional initialization they require. Although the default implementation of this method does nothing, many UIKit classes provide non-empty implementations. You may call the super implementation at any point during your own awakeFromNib method.





### Issues with Initializers




Some memory recalled for [Issues with Initializers](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Initialization/Initialization.html)




> Although init... methods are required by their method signature to return an object, that object is not necessarily the one that was most recently allocated—the receiver of the init... message. In other words, the object you get back from an initializer might not be the one you thought was being initialized.

> Two conditions prompt the return of something other than the just-allocated object. The first involves two related situations: when there must be a singleton instance or when the defining attribute of an object must be unique. Some Cocoa classes—NSWorkspace, for instance—allow only one instance in a program; a class in such a case must ensure (in an initializer or, more likely, in a class factory method) that only one instance is created, returning this instance if there is any further request for a new one.
>




awakeAferUsingCoder is an example of the issue. Here we want to create and return "real thing" object instead of return the nonsense "placeholder"




### Reference




1. [Load custom views from a Nib](https://blog.compeople.eu/apps/?p=97)  

2. [iOS Programming Recipe 6: Creating a custom UIView using a Nib](http://nscookbook.com/2013/01/recipe-6-creating-a-custom-uiview-using-a-nib/)  

3. [Embedding custom-view Nibs in another Nib: Towards the holy grail](https://blog.compeople.eu/apps/?p=142)  

4. [How To Make a Custom UIView in iOS 5: A 5 Star Rating View](http://www.raywenderlich.com/1768/uiview-tutorial-for-ios-how-to-make-a-custom-uiview-in-ios-5-a-5-star-rating-view)  

5. [View Programming Guide for iOS](https://developer.apple.com/library/ios/documentation/windowsviews/conceptual/viewpg_iphoneos/CreatingViews/CreatingViews.html)  

6. [View Controller Programming Guide for iOS](https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/ViewLoadingandUnloading/ViewLoadingandUnloading.html)
