---
author: onmyway133
comments: true
date: 2015-06-18 17:26:48+00:00
layout: post
slug: understanding-sdwebimage-cache
title: Understanding SDWebImage - Cache
wordpress_id: 1707
categories:
- iOS
---

## Store



SDWebImage stores UIImage in `memCache` first, then into disk. UIImage gets compressed to `NSData` before going to disk
In `SDImageCache.m`

```objc
- (void)storeImage:(UIImage *)image recalculateFromImage:(BOOL)recalculate imageData:(NSData *)imageData forKey:(NSString *)key toDisk:(BOOL)toDisk {
    if (!image || !key) {
        return;
    }

    [self.memCache setObject:image forKey:key cost:image.size.height * image.size.width * image.scale * image.scale];

    if (toDisk) {
    // ...

    if (imageIsPng) {
                    data = UIImagePNGRepresentation(image);
                }
                else {
                    data = UIImageJPEGRepresentation(image, (CGFloat)1.0);
                }
#else
                data = [NSBitmapImageRep representationOfImageRepsInArray:image.representations usingType: NSJPEGFileType properties:nil];
#endif
            }

            if (data) {
                if (![_fileManager fileExistsAtPath:_diskCachePath]) {
                    [_fileManager createDirectoryAtPath:_diskCachePath withIntermediateDirectories:YES attributes:nil error:NULL];
                }

                [_fileManager createFileAtPath:[self defaultCachePathForKey:key] contents:data attributes:nil];
            }
}
```

Note that `NSFileManager` `createFileAtPath:contents:data:attributes:nil:` returns NO if the write operation fails. This includes not enough disk space. See [detect no disk space iPhone SDK](http://stackoverflow.com/a/7618759/1418457)



## Retrieval



SDWebImage will first check to see if the images are in local before going to make network request
In `SDImageCache.m`

```objc
- (NSOperation *)queryDiskCacheForKey:(NSString *)key done:(SDWebImageQueryCompletedBlock)doneBlock {
    if (!doneBlock) {
        return nil;
    }

    if (!key) {
        doneBlock(nil, SDImageCacheTypeNone);
        return nil;
    }

    // First check the in-memory cache...
    UIImage *image = [self imageFromMemoryCacheForKey:key];
    if (image) {
        doneBlock(image, SDImageCacheTypeMemory);
        return nil;
    }

    NSOperation *operation = [NSOperation new];
    dispatch_async(self.ioQueue, ^{
        if (operation.isCancelled) {
            return;
        }

        @autoreleasepool {
            UIImage *diskImage = [self diskImageForKey:key];
            if (diskImage) {
                CGFloat cost = diskImage.size.height * diskImage.size.width * diskImage.scale * diskImage.scale;
                [self.memCache setObject:diskImage forKey:key cost:cost];
            }

            dispatch_async(dispatch_get_main_queue(), ^{
                doneBlock(diskImage, SDImageCacheTypeDisk);
            });
        }
    });

    return operation;
}
```



## How UIImage is created



SDWebImage uses `SDWebImageDownloaderOperation` and `NSURLConnection` to kick off network requests.
UIImage is created by a call to `sd_imageWithData:`

```objc
+ (UIImage *)sd_imageWithData:(NSData *)data {
    UIImage *image;
    NSString *imageContentType = [NSData sd_contentTypeForImageData:data];
    if ([imageContentType isEqualToString:@"image/gif"]) {
        image = [UIImage sd_animatedGIFWithData:data];
    }
#ifdef SD_WEBP
    else if ([imageContentType isEqualToString:@"image/webp"])
    {
        image = [UIImage sd_imageWithWebPData:data];
    }
#endif
    else {
        image = [[UIImage alloc] initWithData:data];
        UIImageOrientation orientation = [self sd_imageOrientationFromImageData:data];
        if (orientation != UIImageOrientationUp) {
            image = [UIImage imageWithCGImage:image.CGImage
                                        scale:image.scale
                                  orientation:orientation];
        }
    }


    return image;
}
```



## Use another SDImageCache



From [SDWebImage](https://github.com/rs/SDWebImage),



<blockquote>
  It is also possible to use the async based image cache store independently. SDImageCache maintains a memory cache and an optional disk cache. Disk cache write operations are performed asynchronous so it doesn't add unnecessary latency to the UI.
  The SDImageCache class provides a singleton instance for convenience but you can create your own instance if you want to create separated cache namespace
</blockquote>
