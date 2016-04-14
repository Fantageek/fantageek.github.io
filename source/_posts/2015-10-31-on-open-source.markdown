---
layout: post
author: onmyway133
title: "On open source"
date: 2015-10-31 10:54:56 +0700
comments: true
categories:
---

It was back in 2013 when I first dived into open source world. I started working with [pjsip](http://www.pjsip.org/) to develop a VOIP application, then I stumbled [CSipSimple](https://code.google.com/p/csipsimple/) which is the Android binding of the lib. The maintainer [Regis](https://twitter.com/r3gis3R) actually taught me a lot

I asked him [What is a branded version?](https://groups.google.com/forum/#!topic/csipsimple-dev/ihZN8s_-g_4)

> Hi Regis,
This question is not related to coding, but to business :)
I've read some of your answers about branding CSipSimple. What is it?
You grant other permission to make the same app as yours (use same source, make little modification, then publish to Google Play, ...). Since this is open source, how do you and them benefit from this branding ?

And he surprised me with his answer

> I don't make any money from csipsimple at all. It's a pure opensource and free as in speech project.

> I develop it on my free time and just so that it benefit users.

It was my first impression about how people dedicated themselves to open source community.

Afterward as I started working with Android and iOS, I used more open source libraries

Why
--
The reason was simple that it speeded up my development time by not reinventing the wheel. Those libraries are necessary, like AFNetworking, Mantle, Masonry, ... and are highly rated and under actively development

[Libraries Used in the Top 100 iOS Apps](https://medium.com/ios-os-x-development/libraries-used-in-the-top-100-ios-apps-5b845ad927b7#.8cu7xn1ve)

Actually, using a libraries is like relying on someone else to do the job for us. We are likely to give away some control of our app. So we 'd better understand that someone else before trusting him, and only ask for help if needed.

Here are some good checklists

- [Save your future self from broken apps by asking these questions BEFORE adding a third-party framework to your app](http://roadfiresoftware.com/2015/08/save-your-future-self-from-broken-apps/)
- [Considerations for Choosing 3rd Party Swift Libraries](https://www.andrewcbancroft.com/2015/10/27/considerations-for-choosing-3rd-party-swift-libraries/)

Trending
--
I think open source is the way to move our community forward. Luckily, people have the same feeling, too

- [Becoming Open Source by Default](http://code.dblock.org/2015/02/09/becoming-open-source-by-default.html)
- [Open source at Artsy](https://www.objc.io/issues/22-scale/artsy/)
- [Parse Open Sourcing Our SDKs](http://blog.parse.com/announcements/open-sourcing-our-sdks/)

Put on your yellow hat
--

I find using and open sourcing libraries benefit me a lot

- Learn the project structure: I like to read [open source projects ](https://github.com/dkhamsing/open-source-ios-apps) to see how they structure their projects, as well as some techniques I don't know
- Learn how the [libraries](https://github.com/onmyway133/ios-resources/blob/master/Tool/ios-libraries.md) are made, like [Understanding PSTAlertController - weakSheetStorage](http://www.fantageek.com/blog/2015/06/28/understanding-pstalertcontroller-weaksheetstorage/), [Understanding FLEX - Hit Testing](http://www.fantageek.com/blog/2015/06/28/understanding-flex-hit-testing/), [Understanding AFNetworking - Cache](http://www.fantageek.com/blog/2015/06/18/understanding-afnetworking-cache), ...
- Joining Github issues and pull requests are ways to learn how people have come up to such design decisions, how to discuss properly, how the projects evolves over time, ... Examples are [Should Swift 2 support be folded into RAC 3.0?](https://github.com/ReactiveCocoa/ReactiveCocoa/issues/2187), [Layout guides on iOS 9](https://github.com/SnapKit/Masonry/issues/207)
- By open sourcing libraries, not only you contribute back to the community, but you actually learn yourself responsibility. You learn how to structure your code, well defined public APIs, robust testing. You also learn how to write smaller, reusable components
- By having your libraries public, you got more people to review your code, resulting in more reliable and security software. I love this saying "Pull requests are welcome"
- You are more socialized, and who knows, you can get more friends and an environment that suits you. This is the case for me :]

Reference
--
* [Library-Oriented Programming](https://www.youtube.com/watch?v=lqNUTW0F4bw)
- [The OpenSource Experience](http://alisoftware.github.io/oss/2015/12/05/the-opensource-experience/)
