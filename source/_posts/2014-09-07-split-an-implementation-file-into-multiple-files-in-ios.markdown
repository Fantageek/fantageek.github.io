---
author: onmyway133
comments: true
date: 2014-09-07 16:02:24+00:00
layout: post
slug: split-an-implementation-file-into-multiple-files-in-ios
title: Split an implementation file into multiple files in iOS
wordpress_id: 1373
categories:
- iOS
tags:
- category
- class extension
- file
- implementation
- ios
- split
---

If you have a big class and and you want to split the implementation into multiple smaller parts, there are several options



#### Composition



A big class of course has many responsibilities. It's your job to identify them and introduce new classes that is responsible for just one thing. There's the Decorator pattern that may fit this, but under the hood, it is just composition



#### Use the include trick



@rob introduces the #include trick [Split Objective-C Code into multiple files](http://stackoverflow.com/a/13357948/1418457)



#### Use categories



Objective C has categories feature, and we can take this approach



### Using category and class extension



Suppose I have the big class called FTGPerson and I want to split it. Here I use class extension to provide private header and category for functions. Here the template and hope you can get the idea



#### Class



File -> New -> File -> Cocoa Touch Class
`FTGPerson.h`

[code language="objc"]
@interface FTGPerson : NSObject

@property (nonatomic, copy) NSString *publicProperty;

@end
[/code]

`FTGPerson.m`
[code language="objc"]
#import "FTGPerson.h"

@implementation FTGPerson

- (void)method
{
    self.publicProperty = @"property";
}

@end
[/code]



#### Class Extension



File -> New -> File -> Objective-C File -> File: Private, File Type: Extension

`FTGPerson_Private.h`
[code language="objc"]
#import "FTGPerson.h"

@interface FTGPerson ()

@property (nonatomic, copy) NSString *privateProperty1;
@property (nonatomic, copy) NSString *privateProperty2;

@end
[/code]



#### Category1



File -> New -> File -> Objective-C File -> File: Category1, File Type: Category
`FTGPerson+Category1.h`
[code language="objc"]
#import "FTGPerson.h"

@interface FTGPerson (Category1)

@end
[/code]

`FTGPerson+Category1.m`
[code language="objc"]
#import "FTGPerson+Category1.h"
#import "FTGPerson_Private.h"

@implementation FTGPerson (Category1)

- (void)method1
{
    self.privateProperty1 = @"property 1";
}

@end
[/code]



#### Category 2



File -> New -> File -> Objective-C File -> File: Category2, File Type: Category
`FTGPerson+Category1.h`
[code language="objc"]
#import "FTGPerson.h"

@interface FTGPerson (Category2)

@end
[/code]

`FTGPerson+Category2.m`
[code language="objc"]
#import "FTGPerson+Category2.h"
#import "FTGPerson_Private.h"

@implementation FTGPerson (Category2)

- (void)method2
{
    self.privateProperty2 = @"property 2";
}

@end
[/code]



### Reference







  1. [Learn Objective-C, Objects (Part 1): Splitting Classes Into Multiple Files](http://www.binpress.com/tutorial/learn-objectivec-objects-part-1-splitting-classes-into-multiple-files/58)


  2. [An Objective-C class extension header - for what?](http://stackoverflow.com/questions/11913361/an-objective-c-class-extension-header-for-what)


