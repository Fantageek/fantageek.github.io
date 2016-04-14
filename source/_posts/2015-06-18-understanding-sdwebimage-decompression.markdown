---
author: onmyway133
comments: true
date: 2015-06-18 16:34:06+00:00
layout: post
slug: understanding-sdwebimage-decompression
title: Understanding SDWebImage - Decompression
wordpress_id: 1697
categories:
- iOS
---

## How does UIImage decompression happen



This is a very good blog post explaining how UIImage decompression work [Avoiding Image Decompression Sickness](http://www.cocoanetics.com/2011/10/avoiding-image-decompression-sickness/)




> This delay results from the fact that images need to be decompressed from their file incarnation to be rendered on screen. Unfortunately UIImage does this decompression at the very latest possible moment, i.e. when it is to be displayed.



Basically, there are 3 steps
- Get compressed image data from server (PNG, JPEG, ...)
- Initialize UIImage with that compressed image data
- When that UIImage is about to be shown onto UIImageView, that image data from the UIImage will be decompressed. And that decompressed data is what is displayed on UIImageView




> Then there’s the question of “How fast can I get these pixels on screen?”. The answer to this is comprised of 3 main time intervals:
  - time to alloc/init the UIImage with data on disk
  - time to decompress the bits into an uncompressed format
  - time to transfer the uncompressed bits to a CGContext, potentially resizing, blending, anti-aliasing it





## shouldDecompressImages


>   Decompressing images that are downloaded and cached can improve performance but can consume lot of memory. Defaults to YES. Set this to NO if you are experiencing a crash due to excessive memory consumption.

SDWebImage will decompress UIImage when received data from network, or when loaded from disk



### SDWebImageDownloaderOperation



```objc

- (void)connection:(NSURLConnection *)connection didReceiveData:(NSData *)data {
    //
    if (partialImageRef) {
                UIImage *image = [UIImage imageWithCGImage:partialImageRef scale:1 orientation:orientation];
                NSString *key = [[SDWebImageManager sharedManager] cacheKeyForURL:self.request.URL];
                UIImage *scaledImage = [self scaledImageForKey:key image:image];
                if (self.shouldDecompressImages) {
                    image = [UIImage decodedImageWithImage:scaledImage];
                }
                else {
                    image = scaledImage;
                }
                CGImageRelease(partialImageRef);
                dispatch_main_sync_safe(^{
                    if (self.completedBlock) {
                        self.completedBlock(image, nil, nil, NO);
                    }
                });
            }
    //
}

```



### SDImageCache



```objc
- (UIImage *)diskImageForKey:(NSString *)key {
    NSData *data = [self diskImageDataBySearchingAllPathsForKey:key];
    if (data) {
        UIImage *image = [UIImage sd_imageWithData:data];
        image = [self scaledImageForKey:key image:image];
        if (self.shouldDecompressImages) {
            image = [UIImage decodedImageWithImage:image];
        }
        return image;
    }
    else {
        return nil;
    }
}
```



## Decompression



The simplest way is to tell UIImage to draw
```objc
- (void)decompressImage:(UIImage *)image
{
	UIGraphicsBeginImageContext(CGSizeMake(1, 1));
	[image drawAtPoint:CGPointZero];
	UIGraphicsEndImageContext();
}

```

For a more fine tuned approach, see `SDWebImageDecoder`



## Where are the decompressed images





### [UIImage imageNamed]





<blockquote>
  The biggest thing that +imageNamed: does is decode the image data from the source file, which almost always significantly inflates the data size (for example, a screen sized PNG file might consume a few dozen KBs when compressed, but consumes over half a MB decompressed - width * height * 4). By contrast +imageWithContentsOfFile: will decompress that image everytime the image data is needed.
</blockquote>





## Reference







  1. [Difference between [UIImage imageNamed…] and [UIImage imageWithData…]?](http://stackoverflow.com/questions/316236/difference-between-uiimage-imagenamed-and-uiimage-imagewithdata)


  2. [UIImage decompression causing scrolling lag](http://stackoverflow.com/questions/10149165/uiimage-decompression-causing-scrolling-lag)


  3. [How is SDWebImage better than X?](https://github.com/rs/SDWebImage/wiki/How-is-SDWebImage-better-than-X%3F)


  4. [What does SDWebImageDecoder do?](https://github.com/rs/SDWebImage/issues/1173)


  5. [Why the image file size is big](https://github.com/rs/SDWebImage/issues/755)


  6. [Dispelling the UIImage imageNamed: FUD](http://stackoverflow.com/questions/924740/dispelling-the-uiimage-imagenamed-fud)
