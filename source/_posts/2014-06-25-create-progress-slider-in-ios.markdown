---
author: onmyway133
comments: true
date: 2014-06-25 17:59:59+00:00
layout: post
slug: create-progress-slider-in-ios
title: Create Progress Slider in iOS
wordpress_id: 1076
categories:
- iOS
---

Progress Slider is very common in video player to indicate the current playback time and the playable duration (stream buffered)

[![Screen Shot 2014-06-26 at 12.52.47 AM](http://www.fantageek.com/wp-content/uploads/2014/06/Screen-Shot-2014-06-26-at-12.52.47-AM.png)](http://www.fantageek.com/wp-content/uploads/2014/06/Screen-Shot-2014-06-26-at-12.52.47-AM.png)

Here the slider minimum track color is blue, while the progress view underneath is red



#### How to do it


1. Add UISlider to your view (in Interface Builder or code)
2. Place an UIProgressView exactly behind your UISlider
3. Make the maximum track image invisible

[code language="objc"]
- (void)viewDidLoad
{
    [super viewDidLoad];
    // Do any additional setup after loading the view from its nib.

    [self.slider setMaximumTrackImage:[self transparentImage] forState:UIControlStateNormal];
}

- (UIImage *)transparentImage
{
    UIGraphicsBeginImageContextWithOptions((CGSize){ 1, 1 }, NO, 0.0f);
    UIImage *transparentImage = UIGraphicsGetImageFromCurrentImageContext();
    UIGraphicsEndImageContext();

    return transparentImage;
}
[/code]

I tried to set maximumTrackTintColor to clearColor but it didn't work



#### Reference


1. [UISlider with ProgressView combined](http://stackoverflow.com/a/4570100/1418457)

