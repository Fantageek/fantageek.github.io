---
author: onmyway133
comments: true
date: 2015-06-18 16:31:04+00:00
layout: post
slug: understanding-afnetworking-block
title: Understanding AFNetworking - Block
wordpress_id: 1689
categories:
- iOS
---

Hi, in this AFNetworking gotcha series, I’ll share something I learn about AFNetworking and remind you of some properties you may overlook



<!-- more -->



## Why you don't have to use weakSelf inside success and failure block



You don't have to use weakSelf (you don't have to be afraid of retain cycle) in success and failure block because those blocks will be nil after called

"completionBlock is manually nilled out in AFURLConnectionOperation to break the retain cycle."

Take a look at AFURLConnectionOperation.m
When AFNetworking creates AFHTTPRequestOperation, it will call the overridden **setCompletionBlock** method, **completionBlock** is a property of NSOperation

```objc
- (void)setCompletionBlock:(void (^)(void))block {
    [self.lock lock];
    if (!block) {
        [super setCompletionBlock:nil];
    } else {
        __weak __typeof(self)weakSelf = self;
        [super setCompletionBlock:^ {
            __strong __typeof(weakSelf)strongSelf = weakSelf;

#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Wgnu"
            dispatch_group_t group = strongSelf.completionGroup ?: url_request_operation_completion_group();
            dispatch_queue_t queue = strongSelf.completionQueue ?: dispatch_get_main_queue();
#pragma clang diagnostic pop

            dispatch_group_async(group, queue, ^{
                block();
            });

            dispatch_group_notify(group, url_request_operation_completion_queue(), ^{
                [strongSelf setCompletionBlock:nil];
            });
        }];
    }
    [self.lock unlock];
}
```

You see, after calling the block, it will nil out the completionBlock

So basically, when AFNetworking receives network response, the call stack is like this

[code language="language=text""]
completionBlock (NSOperation) -> anonymous block (set in setCompletionBlock) -> anonymous block (set in setCompletionBlockWithSuccess:failure) -> success block
```

NOTE: In case you go to a ViewController, make a request then dismiss this ViewController before the request finishes, I'd suggest using weak strong dance to allow the ViewController to be deallocated right away



## POST and PUT request will make parameters into HTTP request body



AFNetworking has this property `HTTPMethodsEncodingParametersInURI`



<blockquote>
  HTTP methods for which serialized requests will encode parameters as a query string. `GET`, `HEAD`, and `DELETE` by default.
</blockquote>



It is set in the base class `AFHTTPRequestSerializer`

```objc
- (instancetype)init {
    self = [super init];
    if (!self) {
        return nil;
    }

    //...

    // HTTP Method Definitions; see http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html
    self.HTTPMethodsEncodingParametersInURI = [NSSet setWithObjects:@"GET", @"HEAD", @"DELETE", nil];

    // ...
    return self;
}
```

Then this method `requestBySerializingRequest:withParameters:error:` will use this `HTTPMethodsEncodingParametersInURI` property to determine if the parameters should go into body or not

```objc
- (NSURLRequest *)requestBySerializingRequest:(NSURLRequest *)request
                               withParameters:(id)parameters
                                        error:(NSError *__autoreleasing *)error
{
    NSParameterAssert(request);

    NSMutableURLRequest *mutableRequest = [request mutableCopy];

    // ...
        if ([self.HTTPMethodsEncodingParametersInURI containsObject:[[request HTTPMethod] uppercaseString]]) {
            mutableRequest.URL = [NSURL URLWithString:[[mutableRequest.URL absoluteString] stringByAppendingFormat:mutableRequest.URL.query ? @"&%@" : @"?%@", query]];
        } else {
            if (![mutableRequest valueForHTTPHeaderField:@"Content-Type"]) {
                [mutableRequest setValue:@"application/x-www-form-urlencoded" forHTTPHeaderField:@"Content-Type"];
            }
            [mutableRequest setHTTPBody:[query dataUsingEncoding:self.stringEncoding]];
        }
    }

    return mutableRequest;
}
```

About `x-www-form-urlencoded`, take a look at [application/x-www-form-urlencoded or multipart/form-data](http://stackoverflow.com/questions/4007969/application-x-www-form-urlencoded-or-multipart-form-data)



<blockquote>
  For application/x-www-form-urlencoded, the body of the HTTP message sent to the server is essentially one giant query string -- name/value pairs are separated by the ampersand (&), and names are separated from values by the equal symbal (=). An example of this would be:

  MyVariableOne=ValueOne&MyVariableTwo=ValueTwo
</blockquote>





## Multipart POST request



AFNetworking supports Multipart POST request

```objc
- (AFHTTPRequestOperation *)POST:(NSString *)URLString
                      parameters:(id)parameters
       constructingBodyWithBlock:(void (^)(id <AFMultipartFormData> formData))block
                         success:(void (^)(AFHTTPRequestOperation *operation, id responseObject))success
                         failure:(void (^)(AFHTTPRequestOperation *operation, NSError *error))failure;
```

It eventually calls this method
```objc
- (NSMutableURLRequest *)multipartFormRequestWithMethod:(NSString *)method
                                              URLString:(NSString *)URLString
                                             parameters:(NSDictionary *)parameters
                              constructingBodyWithBlock:(void (^)(id <AFMultipartFormData> formData))block
                                                  error:(NSError *__autoreleasing *)error
{
    NSParameterAssert(method);
    NSParameterAssert(![method isEqualToString:@"GET"] && ![method isEqualToString:@"HEAD"]);

    NSMutableURLRequest *mutableRequest = [self requestWithMethod:method URLString:URLString parameters:nil error:error];

    __block AFStreamingMultipartFormData *formData = [[AFStreamingMultipartFormData alloc] initWithURLRequest:mutableRequest stringEncoding:NSUTF8StringEncoding];

    if (parameters) {
        for (AFQueryStringPair *pair in AFQueryStringPairsFromDictionary(parameters)) {
            NSData *data = nil;
            if ([pair.value isKindOfClass:[NSData class]]) {
                data = pair.value;
            } else if ([pair.value isEqual:[NSNull null]]) {
                data = [NSData data];
            } else {
                data = [[pair.value description] dataUsingEncoding:self.stringEncoding];
            }

            if (data) {
                [formData appendPartWithFormData:data name:[pair.field description]];
            }
        }
    }

    if (block) {
        block(formData);
    }

    return [formData requestByFinalizingMultipartFormData];
}
```

As you can see, it first construct the body with the parameters, then apply the block. For how to construct the the Multipart POST request yourself using NSURLConnection, take a look at [Sending Multipart Forms with Objective-C](http://nthn.me/posts/2012/objc-multipart-forms.html)

[code lang=text]
--YOUR_BOUNDARY_STRING
Content-Disposition: form-data; name="photo"; filename="calm.jpg"
Content-Type: image/jpeg

YOUR_IMAGE_DATA_GOES_HERE
--YOUR_BOUNDARY_STRING
Content-Disposition: form-data; name="message"

My first message
--YOUR_BOUNDARY_STRING
Content-Disposition: form-data; name="user"

1
--YOUR_BOUNDARY_STRING
```
