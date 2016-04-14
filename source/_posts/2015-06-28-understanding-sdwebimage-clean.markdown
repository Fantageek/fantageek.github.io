---
author: onmyway133
comments: true
date: 2015-06-28 15:20:11+00:00
layout: post
slug: understanding-sdwebimage-clean
title: Understanding SDWebImage - Clean
wordpress_id: 1720
categories:
- iOS
---

## clearDisk



Erase all files on disk cache. In `SDImageCache.m`
```objc
- (void)clearDiskOnCompletion:(SDWebImageNoParamsBlock)completion
{
    dispatch_async(self.ioQueue, ^{
        [_fileManager removeItemAtPath:self.diskCachePath error:nil];
        [_fileManager createDirectoryAtPath:self.diskCachePath
                withIntermediateDirectories:YES
                                 attributes:nil
                                      error:NULL];

        if (completion) {
            dispatch_async(dispatch_get_main_queue(), ^{
                completion();
            });
        }
    });
}
```



## cleanDisk



Erase some files based on cache time and cache size
```objc
- (void)cleanDiskWithCompletionBlock:(SDWebImageNoParamsBlock)completionBlock {
   // ...
   // Erase old files
   NSDate *expirationDate = [NSDate dateWithTimeIntervalSinceNow:-self.maxCacheAge];
        NSMutableDictionary *cacheFiles = [NSMutableDictionary dictionary];
        NSUInteger currentCacheSize = 0;

        // Enumerate all of the files in the cache directory.  This loop has two purposes:
        //
        //  1. Removing files that are older than the expiration date.
        //  2. Storing file attributes for the size-based cleanup pass.
        NSMutableArray *urlsToDelete = [[NSMutableArray alloc] init];

    // ...
    // Erase more until cache size is preservered
    // If our remaining disk cache exceeds a configured maximum size, perform a second
        // size-based cleanup pass.  We delete the oldest files first.
        if (self.maxCacheSize > 0 && currentCacheSize > self.maxCacheSize) {
            // Target half of our maximum cache size for this cleanup pass.
            const NSUInteger desiredCacheSize = self.maxCacheSize / 2;

   // ...
}
```
