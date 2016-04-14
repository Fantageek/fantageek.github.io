---
author: onmyway133
comments: true
date: 2014-08-18 08:57:48+00:00
layout: post
slug: handle-reachability-in-ios
title: Handling reachability in iOS
wordpress_id: 1263
categories:
- iOS
tags:
- ios
- reachability
---

Here are what I learn about reachability handling in iOS, aka checking for internet connection. Hope you will find it useful, too



#### The naive way



Some API you already know in UIKit can be used for checking internet connection. Most of them a synchronous code, so you 'd better call them in a background thread

<!-- more -->

[code language="objc"]
- (BOOL)connectedToInternet
{
    NSString *string = [NSString stringWithContentsOfURL:[NSURL URLWithString:@"http://www.google.com"]
                                                encoding:NSUTF8StringEncoding
                                                   error:nil];

    return string ? YES : NO;
}
[/code]

[code language="objc"]
- (BOOL)connectedToInternet
{
   NSURL *url = [NSURL URLWithString:@"http://www.google.com"];
   NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
   [request setHTTPMethod:@"HEAD"];
   NSHTTPURLResponse *response;
   [NSURLConnection sendSynchronousRequest:request returningResponse:&response error: NULL];

   return ([response statusCode] == 200) ? YES : NO;
}
[/code]



#### Using SystemConfiguration framework



After importing the SystemConfiguration framework, you can use either SCNetworkReachabilityGetFlags to synchronously get the reachability status, or provide a callback to SCNetworkReachabilitySetCallback to be notified about reachability status change.

Note that SCNetworkReachabilityGetFlags is synchronous.



<blockquote>The System Configuration framework reachability API () operates synchronously by default. Thus, seemingly innocuous routines like SCNetworkReachabilityGetFlags can get you killed by the watchdog. If you're using the reachability API, you should use it asynchronously. This involves using the SCNetworkReachabilityScheduleWithRunLoop routine to schedule your reachability queries on the run loop
</blockquote>



[code language="objc"]
- (BOOL) isConnectionAvailable
{
	SCNetworkReachabilityFlags flags;
        BOOL receivedFlags;
 
        SCNetworkReachabilityRef reachability = SCNetworkReachabilityCreateWithName(CFAllocatorGetDefault(), [@"dipinkrishna.com" UTF8String]);
        receivedFlags = SCNetworkReachabilityGetFlags(reachability, &flags);
        CFRelease(reachability);
 
        if (!receivedFlags || (flags == 0) )
        {
        	return FALSE;
        } else {
		return TRUE;
	}
}
[/code]

Note that SCNetworkReachabilitySetCallback notifies only when reachability status changes



<blockquote>Assigns a client to the specified target, which receives callbacks when the reachability of the target changes.</blockquote>





#### Using some libraries



Libraries make our life easier, but to live well with them, you must surely understand them. There are many reachability libraries on Github, but here I want to mention the most popular: Reachability from tonymillion and AFNetworkReachabilityManager (a sub module of AFNetworking) from mattt. Both use SystemConfiguration under the hood.



#### Reachability



Some people use Reachability like this

[code language="objc"]
- (void)testInternetConnection
{   
    internetReachableFoo = [Reachability reachabilityWithHostname:@"www.google.com"];

    // Internet is reachable
    internetReachableFoo.reachableBlock = ^(Reachability*reach)
    {
        // Update the UI on the main thread
        dispatch_async(dispatch_get_main_queue(), ^{
            NSLog(@"Yayyy, we have the interwebs!");
        });
    };

    // Internet is not reachable
    internetReachableFoo.unreachableBlock = ^(Reachability*reach)
    {
        // Update the UI on the main thread
        dispatch_async(dispatch_get_main_queue(), ^{
            NSLog(@"Someone broke the internet :(");
        });
    };

    [internetReachableFoo startNotifier];
}
[/code]

Looking into the method "startNotifier", you will see that it only uses SCNetworkReachabilitySetCallback and it means this callback will only be called if reachability status changes.

If you want to know the reachability status directly, for example, the reachability status at app launch, you must use the method "isReachable". This method under the hood uses SCNetworkReachabilityGetFlags which is synchronous, and it locks the calling thread.

Reachability has reachabilityForLocalWiFi, which is interesting :)

[code language="objc"]
+(Reachability*)reachabilityForLocalWiFi
{
    struct sockaddr_in localWifiAddress;
    bzero(&localWifiAddress, sizeof(localWifiAddress));
    localWifiAddress.sin_len            = sizeof(localWifiAddress);
    localWifiAddress.sin_family         = AF_INET;
    // IN_LINKLOCALNETNUM is defined in <netinet/in.h> as 169.254.0.0
    localWifiAddress.sin_addr.s_addr    = htonl(IN_LINKLOCALNETNUM);
    
    return [self reachabilityWithAddress:&localWifiAddress];
}
[/code]



#### AFNetworkReachabilityManager



With AFNetworkReachabilityManager, all you have to do is

[code language="objc"]
- (void)trackInternetConnection
{
    [[AFNetworkReachabilityManager sharedManager] startMonitoring];
    [[AFNetworkReachabilityManager sharedManager] setReachabilityStatusChangeBlock:^(AFNetworkReachabilityStatus status) {
        // Handle the status
    }];
}
[/code]

What is nice about AFNetworkReachabilityManager is that in the "startMonitoring" method, it both uses SCNetworkReachabilitySetCallback and calls AFNetworkReachabilityStatusForFlags to get the initial reachability status in a background thread, and calls the AFNetworkReachabilityStatusBlock. So in the user 's point of view, all we care about is the AFNetworkReachabilityStatusBlock handler.

AFNetworking has all the features that Reachability has, and its code is well structured. Another cool thing about it is that it is already in your AFNetworking pod. It's hard to find projects without AFNetworking these days



#### isReachableViaWWAN vs isReachableViaWiFi



Take a look at the method AFNetworkReachabilityStatusForFlags and you will know the story

[code language="objc"]
static AFNetworkReachabilityStatus AFNetworkReachabilityStatusForFlags(SCNetworkReachabilityFlags flags) {
    [...]
    status = AFNetworkReachabilityStatusUnknown;
    if (isNetworkReachable == NO) {
        status = AFNetworkReachabilityStatusNotReachable;
    }
#if	TARGET_OS_IPHONE
    else if ((flags & kSCNetworkReachabilityFlagsIsWWAN) != 0) {
        status = AFNetworkReachabilityStatusReachableViaWWAN;
    }
#endif
    else {
        status = AFNetworkReachabilityStatusReachableViaWiFi;
    }

    return status;
}
[/code]

isReachableViaWWAN is supposed to be for iOS Device



#### How to use AFNetworkReachabilityManager



I've asked a question here [Issue 2262](https://github.com/AFNetworking/AFNetworking/issues/2262), you should take a look at it

The safe way is not to use the sharedManager, but use managerForDomain

[code language="objc"]
AFNetworkReachabilityManager *afReachability = [AFNetworkReachabilityManager managerForDomain:@"www.google.com"];
    [afReachability setReachabilityStatusChangeBlock:^(AFNetworkReachabilityStatus status) {
        if (status < AFNetworkReachabilityStatusReachableViaWWAN) {
            [FTGAlertView showMessage:@"No internet connection"];
        }
    }];

    [afReachability startMonitoring];
[/code]

You should read the question 7 and 8 in the Reference below to know more about SCNetworkReachabilityCreateWithName vs SCNetworkReachabilityCreateWithAddress, and about the zero address



#### Reference







  1. [Reachability](https://github.com/tonymillion/Reachability)


  2. [AFNetworkReachabilityManager](https://github.com/AFNetworking/AFNetworking/blob/master/AFNetworking/AFNetworkReachabilityManager.m)


  3. [How to check for internet connection synchronously?](http://stackoverflow.com/questions/24535842/how-to-check-for-internet-connection-synchronously/25133851#25133851)


  4. [iOS: Check whether internet connection is available](http://dipinkrishna.com/blog/2012/04/ios-check-internet-connection/)


  5. [Check if Active Internet Connection Exists on iOS Device](http://ios-blog.co.uk/tutorials/check-if-active-internet-connection-exists-on-ios-device/)


  6. [Technical Q&A QA1693 Synchronous Networking On The Main Thread](https://developer.apple.com/library/ios/qa/qa1693/_index.html)


  7. [How to check for network reachability on iOS in a non-blocking manner?](http://stackoverflow.com/a/11314946/1418457)


  8. [understanding INADDR_ANY for socket programming - c](http://stackoverflow.com/questions/16508685/understanding-inaddr-any-for-socket-programming-c)


