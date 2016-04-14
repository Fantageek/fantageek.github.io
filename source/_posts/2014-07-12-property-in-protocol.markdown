---
author: onmyway133
comments: true
date: 2014-07-12 18:41:43+00:00
layout: post
slug: property-in-protocol
title: Property in protocol
wordpress_id: 1121
categories:
- iOS
tags:
- appdelegate
- ios
- property
- protocol
- window
---

A protocol is a group of related properties and methods that can be implemented by any class. In iOS I can have a protocol that declares a property

```objc
@protocol MyProtocol <NSObject>

@property (nonatomic, strong) NSString *name;

@end
```

<!-- more -->


And I have MyClass that conforms to this MyProtocol

```objc
@interface MyClass : NSObject <MyProtocol>

@property (nonatomic, strong) NSString *identifier;

@end
```

Here are 2 things that worth mentioned
1. The identifier property in MyClass declares and generates getter, setter and backing _identifier variable
2. The name property only declares that MyClass has a getter, setter in the header. It does not generate getter, setter implementation and backing variable.
3. I can't redeclare this name property, as it already declared by the protocol. Do this will yell an error

```objc
@interface MyClass () // Class extension

@property (nonatomic, strong) NSString *name;

@end
```



#### Usage


So to use MyClass with that name property, we have to do either
1. Declare the property again (AppDelegate.h does this way)

```objc
@interface MyClass : NSObject <MyProtocol>

@property (nonatomic, strong) NSString *name;
@property (nonatomic, strong) NSString *identifier;

@end
```

2. Synthesize ourself
```objc
@implementation MyClass

@synthesize name;

@end
```



#### UIApplicationDelegate


UIApplicationDelegate protocol declares a property

```objc
@property (nonatomic, retain) UIWindow *window NS_AVAILABLE_IOS(5_0);
```

And your AppDelegate does this

```objc
@interface AppDelegate : UIResponder <UIApplicationDelegate>

@property (nonatomic, strong) UIWindow *window;

@end
```



#### Reference


1. [Can't declare another window](http://stackoverflow.com/questions/24715715/cant-declare-another-window/24715954#24715954)
2. [How to handle Objective-C protocols that contain properties?](http://stackoverflow.com/a/844785/1418457)
