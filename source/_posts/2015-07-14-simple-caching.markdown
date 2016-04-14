---
author: onmyway133
comments: true
date: 2015-07-14 13:10:27+00:00
layout: post
slug: simple-caching
title: Simple caching
wordpress_id: 1764
categories:
- iOS
---

## Memory and disk



This simple cache uses both memory and disk cache
- Retrieval: Ask memory first. If not, ask disk, then set back to memory cache
- Save: Save to both memory and disk.

I used to use [TMCache](https://github.com/tumblr/TMCache), but then I found [Realm](https://realm.io/docs/objc/latest/) to be much better.



## Code



Take a look at my gist [SimpleCache](https://gist.github.com/onmyway133/50d7139314bc196bf2e2)



## Reference







  * [Haneke](https://github.com/Haneke/Haneke)


  * [FastImageCache](https://github.com/path/FastImageCache)


  * [SDWebImage](https://github.com/rs/SDWebImage)


  * [Caching in theory and practice](https://blogs.dropbox.com/tech/2012/10/caching-in-theory-and-practice/)


  * [iOS image caching. Libraries benchmark (SDWebImage vs FastImageCache)](https://bpoplauschi.wordpress.com/2014/03/21/ios-image-caching-sdwebimage-vs-fastimage/)


