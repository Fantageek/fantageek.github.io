---
author: onmyway133
comments: true
date: 2014-09-18 17:14:54+00:00
layout: post
slug: how-to-make-all-operations-run-on-a-certain-thread-in-ios
title: How to make all operations run on a certain thread in iOS
wordpress_id: 1381
categories:
- iOS
---

When using pjsip, I had a need to ensure that all pjsip calls must happen on a certain thread, because pjsip uses Thread Local Storage to prevent multiple threads from calling pjsip

Today, I read AFNetworking source code and see that it has this feature. All the code is in AFURLConnectionOperation.m

Entry point in NSRunLoop
--


```objc
+ (void)networkRequestThreadEntryPoint:(id)__unused object {
    @autoreleasepool {
        [[NSThread currentThread] setName:@"AFNetworking"];

        NSRunLoop *runLoop = [NSRunLoop currentRunLoop];
        [runLoop addPort:[NSMachPort port] forMode:NSDefaultRunLoopMode];
        [runLoop run];
    }
}
```



Thread creation
--

```objc
+ (NSThread *)networkRequestThread {
    static NSThread *_networkRequestThread = nil;
    static dispatch_once_t oncePredicate;
    dispatch_once(&oncePredicate, ^{
        _networkRequestThread = [[NSThread alloc] initWithTarget:self selector:@selector(networkRequestThreadEntryPoint:) object:nil];
        [_networkRequestThread start];
    });

    return _networkRequestThread;
}
```



Perform operation on this thread
--

```objc
- (void)start {
    [self.lock lock];
    if ([self isCancelled]) {
        [self performSelector:@selector(cancelConnection) onThread:[[self class] networkRequestThread] withObject:nil waitUntilDone:NO modes:[self.runLoopModes allObjects]];
    } else if ([self isReady]) {
        self.state = AFOperationExecutingState;

        [self performSelector:@selector(operationDidStart) onThread:[[self class] networkRequestThread] withObject:nil waitUntilDone:NO modes:[self.runLoopModes allObjects]];
    }
    [self.lock unlock];
}
```



Reference
--

1. [Understanding NSRunLoop](http://stackoverflow.com/questions/12091212/understanding-nsrunloop)
2. [What does NSRunLoop do?](http://stackoverflow.com/questions/16477433/what-does-nsrunloop-do)
3. [NSRunLoop Internals](https://mikeash.com/pyblog/friday-qa-2010-01-01-nsrunloop-internals.html)
4. [Run Run Run NSRunLoop](http://hackazach.net/code/2013/08/09/run-run-run-nsrunloop/)
