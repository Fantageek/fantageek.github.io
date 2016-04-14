---
author: onmyway133
comments: true
date: 2014-08-18 08:57:14+00:00
layout: post
slug: design-pattern-in-ios
title: Design Pattern in iOS
wordpress_id: 1261
categories:
- iOS
tags:
- design pattern
- ios
---

When talking about design pattern, I very much like this saying





<blockquote>Separating what changes from what stays the same</blockquote>




Normally, I tend to think of software engineer as world creator, and classes are citizen. You should assign each person small and specific tasks, don't let him do too much. Make him loosely coupled with others. Also, when you create another world, you should be able to call the experienced citizen (code resuse)

<!-- more -->


I will list here some examples where iOS makes use of design patterns. This list follows [SourceMaking list](http://sourcemaking.com/design_patterns)



### Creational design patterns




####  Abstract Factory


Creates an instance of several families of classes
Class Cluster (NSArray, NSDictionary, ...)



####  Builder 


Separates object construction from its representation
NSDateComponents
NSURLComponents



####  Factory Method 


Creates an instance of several derived classes
[NSLayoutConstraint constraintWithItems...]



####  Object Pool


Avoid expensive acquisition and release of resources by recycling objects that are no longer in use
dequeueReusableCellWithIdentifier



####  Prototype 


A fully initialized instance to be copied or cloned
NSCopying



####  Singleton 


A class of which only a single instance can exist
[UIApplication sharedApplication]
[NSFileManager defaultManager]




### Structural design patterns




####  Adapter 


Match interfaces of different classes



####  Bridge 


Separates an object’s interface from its implementation



####  Composite 


A tree structure of simple and composite objects
UIView
NSCompoundPredicate
UIDynamicBehavior



####  Decorator 


Add responsibilities to objects dynamically
Category
UITableViewDelegate



####  Facade 


A single class that represents an entire subsystem




####  Flyweight 


A fine-grained instance used for efficient sharing




####  Private Class Data 


Restricts accessor/mutator access



####  Proxy 


An object representing another object
NSProxy
UIAppearance



### Behavioral design patterns




####  Chain of responsibility 


A way of passing a request between a chain of objects
UIResponder



####  Command 


Encapsulate a command request as an object
NSInvocation



####  Interpreter 


A way to include language elements in a program



####  Iterator 


Sequentially access the elements of a collection
NSEnumerator



####  Mediator 


Defines simplified communication between classes
UIPresentationController



####  Memento 


Capture and restore an object's internal state
NSKeyedArchiver



####  Null Object 


Designed to act as a default value of an object



####  Observer 


A way of notifying change to a number of classes
KVO
NSNotificationCenter



####  State 


Alter an object's behavior when its state changes



####  Strategy 


Encapsulates an algorithm inside a class



####  Template method 


Defer the exact steps of an algorithm to a subclass



####  Visitor 


Defines a new operation to a class without change



### Misc




#### MVC


Model-View-Controller



#### MVVM


Model-View-ViewModel



#### Specification


Business rules can be recombined by chaining the business rules together using boolean logic.



#### Lazy initialization


Tactic of delaying the creation of an object, the calculation of a value, or some other expensive process until the first time it is needed.



#### Receptionist


Addresses the general problem of redirecting an event occurring in one execution context of an application to another execution context for handling



#### Multiton


Ensure a class has only named instances, and provide global point of access to them.



#### Reactor


A reactor object provides an asynchronous interface to resources that must be handled synchronously.




#### Reference


1. [Cocoa Fundamentals Guide: Cocoa Design Patterns](https://developer.apple.com/legacy/library/documentation/Cocoa/Conceptual/CocoaFundamentals/CocoaDesignPatterns/CocoaDesignPatterns.html)
2. [Start Developing iOS Apps Today: Using Design Patterns](https://developer.apple.com/library/ios/referencelibrary/GettingStarted/RoadMapiOS/DesignPatterns.html)
3. [iOS Design Patterns](http://www.raywenderlich.com/46988/ios-design-patterns)
4. [Cocoa Design Patterns by Erik Buck and Donald Yacktman](http://www.amazon.com/Cocoa-Design-Patterns-Erik-Buck/dp/0321535022/ref=pd_bxgy_b_img_y)
5. [Pro Objective-C Design Patterns for iOS by Carlo Chung](http://www.amazon.com/Pro-Objective-C-Design-Patterns-iOS/dp/1430233303)
6. [Head First Design Patterns](http://www.amazon.com/Head-First-Design-Patterns-Freeman/dp/0596007124/)
7. [Design Patterns](http://sourcemaking.com/design_patterns)
