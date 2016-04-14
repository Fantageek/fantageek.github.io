---
layout: post
author: onmyway133
title: "Library not found for -lPods"
date: 2015-11-04 23:23:48 +0700
comments: true
categories:
---

I'm changing an old Objective C project to build with Xcode 7 and support 64bit. As you already know, iOS is moving really fast and there's more requirements added every year

- [64-bit Requirement for App Updates](https://developer.apple.com/news/?id=04082015a)
- [Configuring App Transport Security Exceptions in iOS 9 and OSX 10.11](http://ste.vn/2015/06/10/configuring-app-transport-security-ios-9-osx-10-11/)
- [Why I’m not enabling Bitcode](https://medium.com/@FredericJacobs/why-i-m-not-enabling-bitcode-f35cd8fbfcc5)
- [Working with App Thinning in iOS 9](http://www.appcoda.com/app-thinning/)

My app uses HockeySDK and Crashlytics, so I use Cocoapods to streamline the process.

After `pod install`

```ruby
pod 'HockeySDK', '~> 3.8'
pod 'Fabric', '~> 1.5'
pod 'Crashlytics', '~> 3.3'
```

For now, [Crashlytics podspec](https://github.com/CocoaPods/Specs/blob/master/Specs/Crashlytics/3.3.4/Crashlytics.podspec.json) does not declare that it depends on `Fabric`, but in fact it does. So we need to to declare Fabric

Library not found for -lPods
--
Firstly, I get this famous linker error `Library not found for -lPods`

Most of the time, it is because our app can't link against the library that Cocoapods has generated

I outline many solutions here [Library not found for -lPods](https://github.com/onmyway133/ios-issues/issues/27)

$(inherited)
--
In my case, my fixes are to add `$(inherited)` to the following places in target `Build Settings`

- FRAMEWORK_SEARCH_PATHS (Framework Search Paths)
- HEADER_SEARCH_PATHS (Header Search Paths)
- LIBRARY_SEARCH_PATHS (Library Search Paths)
- OTHER_LDFLAGS (Other Linker Flags)
- GCC_PREPROCESSOR_DEFINITIONS (Preprocessor Macros)

And

> Ensure that Build Active Architectures Only settings for both of your project and the Pods project were equal for debug and release configuration!

Since Cocoapods target sets Debug configuration to NO, so I have to set my app target Debug configuration to NO, too
