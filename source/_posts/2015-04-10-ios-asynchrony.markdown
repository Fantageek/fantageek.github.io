---
author: onmyway133
comments: true
date: 2015-04-10 03:30:41+00:00
layout: post
slug: ios-asynchrony
title: iOS Asynchrony
wordpress_id: 1654
categories:
- iOS
---

### [GCD](https://developer.apple.com/library/prerelease/mac/documentation/Performance/Reference/GCD_libdispatch_Ref/index.html)





<blockquote>
  Grand Central Dispatch (GCD) comprises language features, runtime libraries, and system enhancements that provide systemic, comprehensive improvements to the support for concurrent code execution on multicore hardware in iOS and OS X.
</blockquote>



[code language="objc"]
// Doing something on the main thread

dispatch_queue_t myQueue = dispatch_queue_create("My Queue",NULL);
dispatch_async(myQueue, ^{
    // Perform long running process
    
    dispatch_async(dispatch_get_main_queue(), ^{
        // Update the UI
        
    });
}); 

// Continue doing other stuff on the 
// main thread while process is running.
[/code]



### [PromiseKit](https://github.com/mxcl/PromiseKit)





<blockquote>
  A promise is an object that wraps an asynchronous task. Pass that object around, and write clean, ordered code; a logical, simple, modular stream of progression from one asynchronous task to another.
</blockquote>



[code language="objc"]
[self login].then(^{

    // our login method wrapped an async task in a promise
    return [API fetchKittens];

}).then(^(NSArray *fetchedKittens){

    // our API class wraps our API and returns promises
    // fetchKittens returned a promise that resolves with an array of kittens
    self.kittens = fetchedKittens;
    [self.tableView reloadData];

}).catch(^(NSError *error){

    // any errors in any of the above promises land here
    [[[UIAlertView alloc] init…] show];

});
[/code]



### [Bolt](https://github.com/BoltsFramework/Bolts-iOS)





<blockquote>
  A task represents the result of an asynchronous operation. Typically, a BFTask is returned from an asynchronous function and gives the ability to continue processing the result of the task. When a task is returned from a function, it's already begun doing its job.
  
  A task is not tied to a particular threading model: it represents the work being done, not where it is executing.
</blockquote>



[code language="objc"]
[[self saveAsync:obj] continueWithBlock:^id(BFTask *task) {
  if (task.isCancelled) {
    // the save was cancelled.
  } else if (task.error) {
    // the save failed.
  } else {
    // the object was saved successfully.
    PFObject *object = task.result;
  }
  return nil;
}];
[/code]



### [ReactiveCocoa](https://github.com/ReactiveCocoa/ReactiveCocoa)





<blockquote>
  ReactiveCocoa (RAC) is an Objective-C framework inspired by Functional Reactive Programming. It provides APIs for composing and transforming streams of values.
  
  Rather than using mutable variables which are replaced and modified in-place, RAC provides signals (represented by RACSignal) that capture present and future values.
  
  By chaining, combining, and reacting to signals, software can be written declaratively, without the need for code that continually observes and updates values.
</blockquote>



[code language="objc"]
[[[[client 
    logInUser] 
    flattenMap:^(User *user) {
        // Return a signal that loads cached messages for the user.
        return [client loadCachedMessagesForUser:user];
    }]
    flattenMap:^(NSArray *messages) {
        // Return a signal that fetches any remaining messages.
        return [client fetchMessagesAfterMessage:messages.lastObject];
    }]
    subscribeNext:^(NSArray *newMessages) {
        NSLog(@"New messages: %@", newMessages);
    } completed:^{
        NSLog(@"Fetched all messages.");
    }];
[/code]



### Reference







  1. [Taming Callbacks in iOS: Bolts Framework](http://www.dimroc.com/2014/12/17/bolt-framework-promisekit-or-reactive-cocoa-for-async/)


  2. [Asynchronous Operations in iOS with Grand Central Dispatch](http://jeffreysambells.com/2013/03/01/asynchronous-operations-in-ios-with-grand-central-dispatch)


  3. [PromiseKit: Promises for iOS](http://sapandiwakar.in/promisekit-promises-for-ios/)


  4. [Recommended resources for learning ReactiveCocoa](http://www.fantageek.com/1649/recommended-resources-for-learning-reactivecocoa-2/)


