---
author: onmyway133
comments: true
date: 2015-06-18 16:29:19+00:00
layout: post
slug: understanding-afnetworking-cache
title: Understanding AFNetworking - Cache
wordpress_id: 1683
categories:
- iOS
---

## Caching



I'm talking about `AFHTTPSessionManager`, but the same applies to `AFHTTPRequestOperationManager`. AFNetworking is just a wrapper around `AFURLSession` and `AFURLConnection`, and uses `NSURLCache` to make caching happen



### Does AFNetworking cache



AFNetworking only uses NSCache for caching images, as implemented in AFImageCache. Read more [How Does Caching Work in AFNetworking? : AFImageCache & NSUrlCache Explained](http://blog.originate.com/blog/2014/02/20/afimagecache-vs-nsurlcache/)



### Usage



We often usage `AFHTTPSessionManager` like this
```objc
[AFHTTPSessionManager manager]
```

Which is using default session configuration
```objc
- (instancetype)initWithSessionConfiguration:(NSURLSessionConfiguration *)configuration {
    self = [super init];
    if (!self) {
        return nil;
    }

    if (!configuration) {
        configuration = [NSURLSessionConfiguration defaultSessionConfiguration];
    }

    self.sessionConfiguration = configuration;
    ...
}
```



### NSURLSessionConfiguration



The `defaultSessionConfiguration` has 2 cache properties



#### URLCache





<blockquote>
  The URL cache for providing cached responses to requests within the session.

  This property determines the URL cache object used by tasks within sessions based on this configuration.
  To disable caching, set this property to nil.
  For default sessions, the default value is the shared URL cache object.
  For background sessions, the default value is nil.
  For ephemeral sessions, the default value is a private cache object that stores data in memory only, and is destroyed when you invalidate the session.
</blockquote>





#### requestCachePolicy





<blockquote>
  A predefined constant that determines when to return a response from the cache.

  This property determines the request caching policy used by tasks within sessions based on this configuration.
  Set this property to one of the constants defined in NSURLRequestCachePolicy to specify whether the cache policy should depend on expiration dates and age, whether the cache should be disabled entirely, and whether the server should be contacted to determine if the content has changed since it was last requested.
  The default value is NSURLRequestUseProtocolCachePolicy.
</blockquote>





### dataTaskWillCacheResponse





<blockquote>
  Sets a block to be executed to determine the caching behavior of a data task
</blockquote>



AFNetworking uses this block as a chance to us to intercept to the response

```objc
- (void)URLSession:(NSURLSession *)session
          dataTask:(NSURLSessionDataTask *)dataTask
 willCacheResponse:(NSCachedURLResponse *)proposedResponse
 completionHandler:(void (^)(NSCachedURLResponse *cachedResponse))completionHandler
{
    NSCachedURLResponse *cachedResponse = proposedResponse;

    if (self.dataTaskWillCacheResponse) {
        cachedResponse = self.dataTaskWillCacheResponse(session, dataTask, proposedResponse);
    }

    if (completionHandler) {
        completionHandler(cachedResponse);
    }
}
```

Reading `URLSession:dataTask:willCacheResponse:completionHandler` tells us that



<blockquote>
  The session calls this delegate method after the task finishes receiving all of the expected data. If you do not implement this method, the default behavior is to use the caching policy specified in the session’s configuration object. The primary purpose of this method is to prevent caching of specific URLs or to modify the userInfo dictionary associated with the URL response.

  The session calls this delegate method after the task finishes receiving all of the expected data. If you do not implement this method, the default behavior is to use the caching policy specified in the session’s configuration object. The primary purpose of this method is to prevent caching of specific URLs or to modify the userInfo dictionary associated with the URL response.
  - This method is called only if the NSURLProtocol handling the request decides to cache the response. As a rule, responses are cached only when all of the following are true:
  - The request is for an HTTP or HTTPS URL (or your own custom networking protocol that supports caching).
  - The request was successful (with a status code in the 200–299 range).
  - The provided response came from the server, rather than out of the cache.
  - The session configuration’s cache policy allows caching.
  - The provided NSURLRequest object's cache policy (if applicable) allows caching.
  - The cache-related headers in the server’s response (if present) allow caching.
  - The response size is small enough to reasonably fit within the cache. (For example, if you provide a disk cache, the response must be no larger than about 5% of the disk cache size.)
</blockquote>





### How to prevent caching



We can prevent caching by either
1. Implement `dataTaskWillCacheResponse` and return nil
2. Set `requestCachePolicy` to `NSURLRequestReloadIgnoringLocalCacheData`
```objc
AFHTTPSessionManager *sessionManager = [AFHTTPSessionManager manager];
sessionManager.session.configuration.requestCachePolicy = NSURLRequestReloadIgnoringLocalCacheData;
```

Note that `NSURLRequestReloadIgnoringLocalAndRemoteCacheData` is not implemented by the OS, and `NSURLRequestReloadIgnoringCacheData` is just synonym for `NSURLRequestReloadIgnoringLocalCacheData`
3. Set `URLCache` to nil
```objc
AFHTTPSessionManager *sessionManager = [AFHTTPSessionManager manager];
sessionManager.session.configuration.URLCache = nil;
```
4. NSURLRequest cachePolicy
NSURLRequest has `cachePolicy` that can be used to control caching for specific request



## Reference







  1. [URL Loading System Programming Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/URLLoadingSystem/URLLoadingSystem.html#//apple_ref/doc/uid/10000165-BCICJDHA)


  2. [NSURLProtocol Tutorial](http://www.raywenderlich.com/59982/nsurlprotocol-tutorial)


  3. [NSURLCACHE & NSCACHED URLRESPONSE](http://catthoughts.ghost.io/in-depth-nsurlcache/)


  4. [AFNetworking : How to know if response is using cache or not ? 304 or 200](http://stackoverflow.com/questions/12180727/afnetworking-how-to-know-if-response-is-using-cache-or-not-304-or-200)


  5. [AFNetworking 2.0 - Forced caching](http://stackoverflow.com/questions/25586244/afnetworking-2-0-forced-caching)


  6. [How to disable caching from NSURLSessionTask](http://stackoverflow.com/questions/24328461/how-to-disable-caching-from-nsurlsessiontask)


  7. [AFNetworking different cache settings for different content](http://stackoverflow.com/questions/24281331/afnetworking-different-cache-settings-for-different-content)


  8. [Why is NSURLSession don't use my configured NSURLCache?](http://stackoverflow.com/questions/20768968/why-is-nsurlsession-dont-use-my-configured-nsurlcache)


  9. [NSURLCache](http://nshipster.com/nsurlcache/)


  10. [Caching and NSURLConnection](https://blackpixel.com/writing/2012/05/caching-and-nsurlconnection.html)
