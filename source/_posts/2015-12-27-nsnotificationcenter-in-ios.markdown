---
layout: post
author: onmyway133
title: "NSNotificationCenter in iOS"
date: 2015-12-27 16:42:17 +0700
comments: true
categories:
---

`NSNotificationCenter` is like central hub where notifications are posted and received by any parts of the app

Registering
--

Registering for notification n times, you get notified n times

debugDescription
--
As of iOS 9+,

> NSNotificationCenter and NSDistributedNotificationCenter will now provide a debug description when printing from the debugger that will list all registered observers including references that have been zeroed out for aiding in debugging notification registrations. This data is only valid per the duration of the breakpoint since the underlying store must account for multithreaded environments. Wildcard registrations for notifications where the name or object that was passed to the addObserver method family was null will be printed in the debug description as *.

```swift
print(NSNotificationCenter.defaultCenter().debugDescription)
```

Observer
--

### addObserver(_:selector:name:object:)
> Adds an entry to the receiver’s dispatch table with an observer, a notification selector and optional criteria: notification name and sender.

```swift
override func viewDidLoad() {
    super.viewDidLoad()

    NSNotificationCenter.defaultCenter().addObserver(self, selector: "playerDidReachEndNotificationHandler:", name: AVPlayerItemDidPlayToEndTimeNotification, object: player.currentItem)
  }

func playerDidReachEndNotificationHandler(notification: NSNotification) {
    guard let playerItem = notification.object as? AVPlayerItem else { return }
    playerItem.seekToTime(kCMTimeZero)
  }
```

- NSNotificationCenter holds weak reference to its observer
- Regular notification centers deliver notifications on the thread in which the notification was posted

### addObserverForName(_:object:queue:usingBlock:)
> Adds an entry to the receiver’s dispatch table with a notification queue and a block to add to the queue, and optional criteria: notification name and sender.

```swift
observer = NSNotificationCenter.defaultCenter().addObserverForName(AVPlayerItemDidPlayToEndTimeNotification, object: player.currentItem, queue: NSOperationQueue.mainQueue()) { [weak self] notification in
        self?.playerDidReachEndNotificationHandler(notification)
    }
```

- The returned of this method is an opaque object to act as the observer, and `NSNotificationCenter` holds a strong reference to this object
- Avoid strongly capturing objects in the block. See [NSNotificationCenter with blocks considered harmful](http://sealedabstract.com/code/nsnotificationcenter-with-blocks-considered-harmful/)

When to unregister ?
--

The idea is to unregister as soon as we don't need to receive notifications. Most of the time, it is our `deinit`

```swift
deinit {
    NSNotificationCenter.defaultCenter().removeObserver(self)
  }
```

See [Unregistering an Observer](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Notifications/Articles/Registering.html#//apple_ref/doc/uid/20000723-CEGCCFID)

> Before an object that is observing notifications is deallocated, it must tell the notification center to stop sending it notifications. Otherwise, the next notification gets sent to a nonexistent object and the program crashes

### iOS 9+
See [OS X 10.11 and iOS 9 Release Notes
Cocoa Foundation Framework](https://developer.apple.com/library/mac/releasenotes/Foundation/RN-Foundation/index.html#10_11NotificationCenter)

> In OS X 10.11 and iOS 9.0 NSNotificationCenter and NSDistributedNotificationCenter will no longer send notifications to registered observers that may be deallocated

> This means that observers are not required to un-register in their deallocation method

However,

> Block based observers via the -[NSNotificationCenter addObserverForName:object:queue:usingBlock] method still need to be un-registered when no longer in use since the system still holds a strong reference to these observers

So we must call `removeObserver` on the returned `observer` object
```swift
deinit {
    NSNotificationCenter.defaultCenter().removeObserver(observer)
  }
```

Synchrony
--
> A notification center delivers notifications to observers synchronously. In other words, when posting a notification, control does not return to the poster until all observers have received and processed the notification

If you have performance problem of posting notifications to large number of observers, consider using

- dispatch_after
- Many `NSNotificationCenter`
- `NSNotificationQueue`

NSNotificationQueue
--
- Enqueuing methods return immediately
- Associated with the run loop modes
- There 's `defaultQueue` for the current thread

```swift
let queue = NSNotificationQueue(notificationCenter: NSNotificationCenter.defaultCenter())
    let notification = NSNotification(name: "CacheDidUpdateNotification", object: self)
    queue.enqueueNotification(notification, postingStyle: .PostASAP)
```

Reference
--
- [Notification Programming Topics](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Notifications/Introduction/introNotifications.html#//apple_ref/doc/uid/10000043-SW1)
- [NSNotificationCenter with blocks considered harmful](http://sealedabstract.com/code/nsnotificationcenter-with-blocks-considered-harmful/)
- [Delegation or Notification](http://useyourloaf.com/blog/delegation-or-notification.html)
- [Friday Q&A 2010-01-08: NSNotificationQueue](https://mikeash.com/pyblog/friday-qa-2010-01-08-nsnotificationqueue.html)
