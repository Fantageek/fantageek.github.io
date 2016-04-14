---
author: onmyway133
comments: true
date: 2015-06-18 16:30:27+00:00
layout: post
slug: understanding-afnetworking-operation
title: Understanding AFNetworking - Operation
wordpress_id: 1686
categories:
- iOS
---

Hi, in this AFNetworking gotcha series, I'll share something I learn about AFNetworking and remind you of some properties you may overlook



#### AFHTTPRequestOperation is an NSOperation



Take a look at AFURLConnectionOperation.m and its overridden "start" method, you 'll learn how AFNetworking starts an NSURLConnection. The same holds true for NSURLSession

<!-- more -->

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

- (void)operationDidStart {
    [self.lock lock];
    if (![self isCancelled]) {
        self.connection = [[NSURLConnection alloc] initWithRequest:self.request delegate:self startImmediately:NO];

        NSRunLoop *runLoop = [NSRunLoop currentRunLoop];
        for (NSString *runLoopMode in self.runLoopModes) {
            [self.connection scheduleInRunLoop:runLoop forMode:runLoopMode];
            [self.outputStream scheduleInRunLoop:runLoop forMode:runLoopMode];
        }

        [self.connection start];
    }
    [self.lock unlock];

    dispatch_async(dispatch_get_main_queue(), ^{
        [[NSNotificationCenter defaultCenter] postNotificationName:AFNetworkingOperationDidStartNotification object:self];
    });
}
```



### You don't have to call incrementActivityCount



Take a look at AFNetworkActivityIndicatorManager.m

By setting `isNetworkActivityIndicatorVisible` to `YES` for `sharedManager`, the network activity indicator will show and hide automatically as requests start and finish. You should not ever need to call `incrementActivityCount` or `decrementActivityCount` yourself.



### Retrieving Response Body via AFNetworking with an HTTP Error Status Code



Normally, AFNetworking doesn't allow you to retrieve response in failure block [HTTPClient -> AFHTTPSessionManager | Response body on error?](https://github.com/AFNetworking/AFNetworking/issues/1397)

Take a look at AFHTTPRequestOperation.m and you 'll know why

```objc
- (id)responseObject {
    [self.lock lock];
    if (!_responseObject && [self isFinished] && !self.error) {
        NSError *error = nil;
        self.responseObject = [self.responseSerializer responseObjectForResponse:self.response data:self.responseData error:&error];
        if (error) {
            self.responseSerializationError = error;
        }
    }
    [self.lock unlock];

    return _responseObject;
}
```

Gred writes a custom ResponserSerializer thats insert the response in to userInfo [Retrieving Response Body via AFNetworking with an HTTP Error Status Code](http://blog.gregfiumara.com/archives/239)



### You must use dispatch_group with AFHTTPSessionManager



For AFHTTPRequestOperationManager, you have batchOfRequestOperations:progressBlock:completionBlock:, but somehow it does not work with AFHTTPSessionManager. But you can still learn the idea from how Mattt uses dispatch_group to do batch requests

```objc
+ (NSArray *)batchOfRequestOperations:(NSArray *)operations
                        progressBlock:(void (^)(NSUInteger numberOfFinishedOperations, NSUInteger totalNumberOfOperations))progressBlock
                      completionBlock:(void (^)(NSArray *operations))completionBlock
{
    if (!operations || [operations count] == 0) {
        return @[[NSBlockOperation blockOperationWithBlock:^{
            dispatch_async(dispatch_get_main_queue(), ^{
                if (completionBlock) {
                    completionBlock(@[]);
                }
            });
        }]];
    }

    __block dispatch_group_t group = dispatch_group_create();
    NSBlockOperation *batchedOperation = [NSBlockOperation blockOperationWithBlock:^{
        dispatch_group_notify(group, dispatch_get_main_queue(), ^{
            if (completionBlock) {
                completionBlock(operations);
            }
        });
    }];
   [...]
}
```



### completionQueue



The dispatch queue for the `completionBlock` of request operations. If `NULL` (default), the main queue is used.



### Configure RequestSerializer



RequestSerializer and its subclasses have a lot of useful properties and methods that you may overlook



#### setAuthorizationHeaderFieldWithUsername



Sets the "Authorization" HTTP header set in request objects made by the HTTP client to a basic authentication value with Base64-encoded username and password. This overwrites any existing value for this header.



#### HTTPMethodsEncodingParametersInURI



HTTP methods for which serialized requests will encode parameters as a query string. `GET`, `HEAD`, and `DELETE` by default.
I sometimes forget this property, so my params in POST request gets insert into body



### Configure ResponseSerializer





#### acceptableStatusCodes



The acceptable HTTP status codes for responses. When non-`nil`, responses with status codes not contained by the set will result in an error during validation.

Take a look at  to see how AFNetworking handle statusCodes

```objc
- (BOOL)validateResponse:(NSHTTPURLResponse *)response
                    data:(NSData *)data
                   error:(NSError * __autoreleasing *)error
{
    [...]

        if (self.acceptableStatusCodes && ![self.acceptableStatusCodes containsIndex:(NSUInteger)response.statusCode]) {
            NSDictionary *userInfo = @{
                                       NSLocalizedDescriptionKey: [NSString stringWithFormat:NSLocalizedStringFromTable(@"Request failed: %@ (%ld)", @"AFNetworking", nil), [NSHTTPURLResponse localizedStringForStatusCode:response.statusCode], (long)response.statusCode],
                                       NSURLErrorFailingURLErrorKey:[response URL],
                                       AFNetworkingOperationFailingURLResponseErrorKey: response
                                       };

            validationError = AFErrorWithUnderlyingError([NSError errorWithDomain:AFURLResponseSerializationErrorDomain code:NSURLErrorBadServerResponse userInfo:userInfo], validationError);

            responseIsValid = NO;
        }
    }

    if (error && !responseIsValid) {
        *error = validationError;
    }

    return responseIsValid;
}
```



#### acceptableContentTypes



The acceptable MIME types for responses. When non-`nil`, responses with a `Content-Type` with MIME types that do not intersect with the set will result in an error during validation.
