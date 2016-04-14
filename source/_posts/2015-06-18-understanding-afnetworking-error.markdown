---
author: onmyway133
comments: true
date: 2015-06-18 16:33:39+00:00
layout: post
slug: understanding-afnetworking-error
title: Understanding AFNetworking - Error
wordpress_id: 1695
categories:
- iOS
---

When using AFNetworking, I want to get the status code in case of failure



## NSError in failure block



```objc
AFHTTPSessionManager *sessionManager = [AFHTTPSessionManager manager];
    [sessionManager GET:@"" parameters:nil success:^(NSURLSessionDataTask *task, id responseObject) {

    } failure:^(NSURLSessionDataTask *task, NSError *error) {
        NSLog(@"%@", error);
    }];
```

The `error.code` is not the HTTP status code, it is `NSError` that AFNetworking creates due to serializer error



## How AFNetworking creates NSError





### AFURLSessionManager.m



In `URLSession:task:didCompleteWithError:`

```objc
NSError *serializationError = nil;
            responseObject = [manager.responseSerializer responseObjectForResponse:task.response data:[NSData dataWithData:self.mutableData] error:&serializationError];
```



### AFErrorWithUnderlyingError



This is called in `validateResponse:data:error:` and `responseObjectForResponse:data:error` of  `AFURLResponseSerialization` classes

```objc
static NSError * AFErrorWithUnderlyingError(NSError *error, NSError *underlyingError) {
    if (!error) {
        return underlyingError;
    }

    if (!underlyingError || error.userInfo[NSUnderlyingErrorKey]) {
        return error;
    }

    NSMutableDictionary *mutableUserInfo = [error.userInfo mutableCopy];
    mutableUserInfo[NSUnderlyingErrorKey] = underlyingError;

    return [[NSError alloc] initWithDomain:error.domain code:error.code userInfo:mutableUserInfo];
}
```



### Error Domain



In `AFURLResponseSerialization.h`



<blockquote>
  Error codes for `AFURLResponseSerializationErrorDomain` correspond to codes in `NSURLErrorDomain`.
</blockquote>



For example
```objc
validationError = AFErrorWithUnderlyingError([NSError errorWithDomain:AFURLResponseSerializationErrorDomain code:NSURLErrorCannotDecodeContentData userInfo:mutableUserInfo], validationError);
```



## HTTP Status code





### Inspect userInfo of NSError



The NSError userInfo contains `AFNetworkingOperationFailingURLResponseErrorKey` and `AFNetworkingOperationFailingURLResponseDataErrorKey` that we can inspect to get NSURLResponse and NSData



### Replace NSError



Sometimes we don't care about `NSURLErrorDomain`, we can get HTTP Status Code from NSURResponse and replace the code in the NSError

```objc
NSURLSessionDataTask *task = [self.manager dataTaskWithRequest:request
                                                     completionHandler:^(NSURLResponse *response, id responseObject, NSError *error)
                                      {
                                         if (error) {
                                             error = [self errorFromError:error
                                                           responseObject:responseObject
                                                                 response:response];
                                              [subscriber sendError:error];
                                          } else {
                                              [subscriber sendNext:responseObject];
                                              [subscriber sendCompleted];
                                          }
                                      }];
- (NSError *)errorFromError:(NSError *)error
             responseObject:(id)responseObject
                   response:(NSURLResponse *)response {

    NSMutableDictionary *userInfo = [NSMutableDictionary dictionaryWithDictionary:error.userInfo];
    if (responseObject) {
        userInfo[kNetworkProviderResponseObjectKey] = responseObject;
    }

    NSInteger HTTPStatusCode = error.code;
    if (response) {
        if ([response isKindOfClass:[NSHTTPURLResponse class]]) {
            NSHTTPURLResponse *HTTPResponse = (id)response;
            HTTPStatusCode = HTTPResponse.statusCode;
        }
    } else {
        HTTPStatusCode = 408;
    }

    return [NSError errorWithDomain:error.domain code:HTTPStatusCode userInfo:userInfo];
}

```



## Reference







  1. [OSStatus](http://www.osstatus.com/)
