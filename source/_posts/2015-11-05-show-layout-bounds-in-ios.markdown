---
author: onmyway133
layout: post
title: "Show layout bounds in iOS"
date: 2015-11-05 00:32:57 +0700
comments: true
categories:
---

On Android, we have an option called `Show layout bounds`, which allows us to see bounding areas of your views displayed in vibrant blue and purple.

- [Layout bounds](http://tysmith.me/post/27035355999/layoutbounds)

![](http://38.media.tumblr.com/tumblr_m719seXwah1qfcybd.png)

Border
--
On iOS, with the help of runtime, we can do the same. Using [Border](https://github.com/onmyway133/border)

All you have to do is

```ruby
pod 'Border', '~> 1.0'
```

![](https://camo.githubusercontent.com/70b3a0998d0e97d0a45749a2a6e676baa1a90404/68747470733a2f2f7261772e6769746875622e636f6d2f6f6e6d797761793133332f426f726465722f6d61737465722f53637265656e73686f74732f73637265656e73686f74312e706e67)

How Border works
--
The idea is to swizzle `didMoveToSuperview` in `constructor`

```objc
static void swizzleInstanceMethod(Class class, SEL originalAction, SEL swizzledAction) {
    method_exchangeImplementations(class_getInstanceMethod(class, originalAction),
                                   class_getInstanceMethod(class, swizzledAction));
}

static void swizzle() {
    swizzleInstanceMethod(UIView.class, @selector(didMoveToSuperview),
                          @selector(ftg_didMoveToSuperview));
}

__attribute__((constructor)) static void FTGBorderConstructor(void) {
    @autoreleasepool {
        swizzle();
    }
}

#pragma mark - UIView
@interface UIView (FTGBorder)
@end

@implementation UIView (FTGBorder)

- (void)ftg_didMoveToSuperview {
    self.layer.borderWidth = 1;
    self.layer.borderColor = [UIColor brownColor].CGColor;

    [self ftg_didMoveToSuperview];
}

@end
```

Reference
--
- [How exactly does __attribute__((constructor)) work?](http://stackoverflow.com/questions/2053029/how-exactly-does-attribute-constructor-work)
- [__attribute__ directives in Objective-C](https://blog.twitter.com/2014/attribute-directives-in-objective-c)
- [__attribute__](http://nshipster.com/__attribute__/)
