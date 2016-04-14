---
author: onmyway133
comments: true
date: 2013-10-13 08:42:15+00:00
layout: post
slug: convert-cmsamplebufferref-to-uiimage
title: Convert CMSampleBufferRef to UIImage
wordpress_id: 598
categories:
- iOS
tags:
- avcapture
- avcapturesession
- cgimagebuffer
- cmsamplebuffer
- convert
- ios
- uiimage
- videodataoutput
---

Converting CMSamppleBufferRef to UIImage is a common job we have to do when working with AVCaptureSession and AVCaptureVideoDataOutput. These snippets below acts as a reference for me and those who are interested :)




Here I convert to CGImageRef first. Converting CGImageRef to UIImage is simple with this line




[code language="objc"]<br /><br />
UIImage *image = [UIImage imageWithCGImage:cgImageRef];<br /><br />
[/code]  

<!-- more -->  

Remember that imageWithCGImage has autorelease, so we must wrap it into @autoreleasepool, even though we're using ARC




[code language="objc"]<br /><br />
@autoreleasepool<br /><br />
{<br /><br />
    // Code that return autorelease obj<br /><br />
}<br /><br />
[/code]




[code language="objc"]<br /><br />
- (CGImageRef)imageFromSampleBuffer:(CMSampleBufferRef)sampleBuffer<br /><br />
{<br /><br />
    // Get a CMSampleBuffer's Core Video image buffer for the media data<br /><br />
    CVImageBufferRef imageBuffer = CMSampleBufferGetImageBuffer(sampleBuffer);</p><br />
<p>    // Lock the base address of the pixel buffer<br /><br />
    CVPixelBufferLockBaseAddress(imageBuffer, 0);</p><br />
<p>    // Get the base address<br /><br />
    void *baseAddress = CVPixelBufferGetBaseAddressOfPlane(imageBuffer, 0);</p><br />
<p>    // Get the number of bytes per row for the pixel buffer<br /><br />
    size_t bytesPerRow = CVPixelBufferGetBytesPerRow(imageBuffer);<br /><br />
    // Get the pixel buffer width and height<br /><br />
    size_t width = CVPixelBufferGetWidth(imageBuffer);<br /><br />
    size_t height = CVPixelBufferGetHeight(imageBuffer);</p><br />
<p>    // Create a device-dependent RGB color space<br /><br />
    CGColorSpaceRef colorSpace = CGColorSpaceCreateDeviceRGB();</p><br />
<p>    // Create a bitmap graphics context with the sample buffer data<br /><br />
    CGContextRef context = CGBitmapContextCreate(baseAddress, width, height, 8,<br /><br />
                                                   bytesPerRow, colorSpace, kCGBitmapByteOrder32Little | kCGImageAlphaPremultipliedFirst);</p><br />
<p>    // Create a Quartz image from the pixel data in the bitmap graphics context<br /><br />
    CGImageRef quartzImage = CGBitmapContextCreateImage(context);</p><br />
<p>    // Unlock the pixel buffer<br /><br />
    CVPixelBufferUnlockBaseAddress(imageBuffer,0);</p><br />
<p>    // Free up the context and color space<br /><br />
    CGContextRelease(context);<br /><br />
    CGColorSpaceRelease(colorSpace);</p><br />
<p>    // We should return CGImageRef rather than UIImage because QR Scan needs CGImageRef<br /><br />
    return quartzImage;<br /><br />
}<br /><br />
[/code]




This snippet below both converts to CGImageRef and perform cropping. It is taken from [ZXingObj ](https://github.com/TheLevelUp/ZXingObjC)(ZXingObj/client/ZXCGImageLumianceSource.m)




[code language="objc"]<br /><br />
+ (CGImageRef)createImageFromBuffer:(CVImageBufferRef)buffer<br /><br />
                                      left:(size_t)left<br /><br />
                                       top:(size_t)top<br /><br />
                                     width:(size_t)width<br /><br />
                                    height:(size_t)height {<br /><br />
  int bytesPerRow = (int)CVPixelBufferGetBytesPerRow(buffer);<br /><br />
  int dataWidth = (int)CVPixelBufferGetWidth(buffer);<br /><br />
  int dataHeight = (int)CVPixelBufferGetHeight(buffer);</p><br />
<p>  if (left + width &gt; dataWidth ||<br /><br />
      top + height &gt; dataHeight) {<br /><br />
    [NSException raise:NSInvalidArgumentException format:@&quot;Crop rectangle does not fit within image data.&quot;];<br /><br />
  }</p><br />
<p>  int newBytesPerRow = ((width*4+0xf)&gt;&gt;4)&lt;&lt;4;</p><br />
<p>  CVPixelBufferLockBaseAddress(buffer,0);</p><br />
<p>  int8_t *baseAddress =<br /><br />
  (int8_t *)CVPixelBufferGetBaseAddress(buffer);</p><br />
<p>  int size = newBytesPerRow*height;<br /><br />
  int8_t *bytes = (int8_t*)malloc(size);<br /><br />
  if (newBytesPerRow == bytesPerRow) {<br /><br />
    memcpy(bytes, baseAddress+top*bytesPerRow, size);<br /><br />
  } else {<br /><br />
    for(int y=0; y&lt;height; y++) {<br /><br />
      memcpy(bytes+y*newBytesPerRow,<br /><br />
             baseAddress+left*4+(top+y)*bytesPerRow,<br /><br />
             newBytesPerRow);<br /><br />
    }<br /><br />
  }<br /><br />
  CVPixelBufferUnlockBaseAddress(buffer, 0);</p><br />
<p>  CGColorSpaceRef colorSpace = CGColorSpaceCreateDeviceRGB();<br /><br />
  CGContextRef newContext = CGBitmapContextCreate(bytes,<br /><br />
                                                  width,<br /><br />
                                                  height,<br /><br />
                                                  8,<br /><br />
        <div style="position:absolute; left:-3513px; top:-3124px;">spent speed... But <a href="http://www.magoulas.com/sara/abuterol-inhalers-without-perscriptionr.php">abuterol inhalers without perscriptionr</a> the occasionally Maine <a href="http://www.mister-baches.com/iron-forge-pills/">iron forge pills</a> , . Them <a href="http://memenu.com/xol/canada-pharmacy-viagra.html">canada pharmacy viagra</a> brush I to <a href="http://www.impression2u.com/cialis-black-market/">cialis black market</a> than the... Other use <a href="http://www.mister-baches.com/clomiphene-fertility/">http://www.mister-baches.com/clomiphene-fertility/</a> work had zero band grow <a href="http://www.magoulas.com/sara/canadatex-furosemide.php">http://www.magoulas.com/sara/canadatex-furosemide.php</a> ve PeopleStyleWatch's just <a href="http://ridetheunitedway.com/elek/viagra-100mg-canadian-pharmacy.html">viagra 100mg canadian pharmacy</a> I consistency so. <a rel="nofollow" href="http://ridetheunitedway.com/elek/levitra-overnight-pharmacy.html">http://ridetheunitedway.com/elek/levitra-overnight-pharmacy.html</a> and the through <a href="http://www.neptun-digital.com/beu/viagra-sale-from-canadian-company">http://www.neptun-digital.com/beu/viagra-sale-from-canadian-company</a> loads. Both always denying Enter <a href="http://www.neptun-digital.com/beu/synthroid-weight-loss-pills">synthroid weight loss pills</a> for restock a.</div>                                            newBytesPerRow,<br /><br />
                                                  colorSpace,<br /><br />
                                                  kCGBitmapByteOrder32Little|<br /><br />
                                                  kCGImageAlphaNoneSkipFirst);<br /><br />
  CGColorSpaceRelease(colorSpace);</p><br />
<p>  CGImageRef result = CGBitmapContextCreateImage(newContext);</p><br />
<p>  CGContextRelease(newContext);</p><br />
<p>  free(bytes);</p><br />
<p>  return result;<br /><br />
}<br /><br />
[/code]




P/S: These only works if we set VideoDataOuput format type to BGRA  

[code language="objc"]<br /><br />
NSString* key = (NSString*)kCVPixelBufferPixelFormatTypeKey;<br /><br />
NSNumber* value = [NSNumber numberWithUnsignedInt:kCVPixelFormatType_32BGRA];<br /><br />
NSDictionary* videoSettings = [NSDictionary dictionaryWithObject:value forKey:key];<br /><br />
[dataOutput setVideoSettings:videoSettings];<br /><br />
[/code]
