---
author: onmyway133
comments: true
date: 2016-01-15 09:11:15+00:00
layout: post
slug: ios-best-pratices
title: iOS best practices
wordpress_id: 1526
categories:
- iOS
---

Here are practices I found useful when developing iOS.
They can be tools or framework that greatly affect your style. Sometimes, it applies to other platform as well

Architecture
--

We're always looking for ways to structure our apps better, I collect some cool stuffs in [ios-architecture](https://github.com/onmyway133/ios-architecture)

Name it right
--

- [Name It Right](http://www.fantageek.com/blog/2015/01/14/name-it-right/)
- [Cocoa style for Objective C](http://cocoadevcentral.com/articles/000082.php)
- [swift-style-guide](https://github.com/github/swift-style-guide)
- [swift-style-guide](https://github.com/raywenderlich/swift-style-guide)

Naming is hard. But we can start by naming correctly and with correct spelling. The best way is to stick with the platform naming convention

Project structure
--

- [iOS App Architecture, Part 1: Setting up](http://merowing.info/2014/03/subjective-guide-to-writing-ios-apps-part-1-introduction/)

Some prefer to group their projects by object role, like Model, View, View Controller. But I prefer to group by feature or use case, learning from Clean Architecture

- [Clean Architecture](https://vimeo.com/43612849) When you look at a software system, and all you see is MCV in a web configuration, then the architecture of that system is hiding the use cases of that system and exposing the delivery mechanism.


I often group my code like this

- Share: Contains code that can be useful to other project. Not related to any project. Can be extract to a Pod
- Engine: Contains code that is specific to this project. Like UserManager, ViewAnimator, ...
- Modules: Separate each screen into module. In each module, there are view, view model and wireframe

There are some architectures like MVC, MVVM, VIPER, VIP, ... and you may want to leverage some templates to help creating projects faster, like

- https://github.com/freesuraj/SwiftTemplate
- https://github.com/romsi/VIPERXCodeTemplate

And learn how to make them

- [Custom Xcode File Templates](https://littlebitesofcocoa.com/89-custom-xcode-file-templates)

There are also some scripts to bootstrap your project

- [crafter](https://github.com/krzysztofzablocki/crafter) Crafter - Xcode project configuration CLI made easy
- [xcake](https://github.com/jcampbell05/xcake) Create your Xcode projects automatically using a stupid simple DSL.
- [liftoff](https://github.com/thoughtbot/liftoff) CLI for creating and configuring new Xcode projects

Functional Reactive Programming
--

The Reactive Extension inspired libraries are cool, and they greatly changes the way you develop, especially signal chaining

- [ReactiveCocoa](https://github.com/ReactiveCocoa/ReactiveCocoa)
- [RxSwift](https://github.com/ReactiveX/RxSwift)

Unidirectional data flow
--

Another trend is React inspired libraries, which encourage Unidirectional data flow

- [react-native](https://github.com/facebook/react-native)
- [Swift-Flow](https://github.com/Swift-Flow/Swift-Flow)
- [Delta](https://github.com/thoughtbot/Delta)
- [ReduxKit](https://github.com/ReduxKit/ReduxKit)

Data manipulation
--

Leverage some functional style for better data manipulation

- [Swiftz](https://github.com/typelift/Swiftz) It defines functional data structures, functions, idioms, and extensions that augment the Swift standard library.
- [Dollar.swift](https://github.com/ankurp/Dollar.swift) A functional tool-belt for Swift Language similar to Lo-Dash or Underscore.js in Javascript

Prefer decoration over extension
--

This maybe subjective. But in some case decoration wins

Often when we want to add custom color, font or animate a view, or custom `NSDateFormatter`, we often make categories on `UIColor, UIFont, UIView, NSDateFormatter`, ...

But most of the case, we can group them inside class, like `ColorBucket, FontBucket, ViewAnimator, DateFormatterBucket`, ...

So, instead of

```objc
@interface UIColor (Additions)

- (UIColor *)customColor;

@end
```

We can have ColorBucket

```objc
@interface ColorBucket

+ (UIColor *)customColor;

@end
```

Mixin
--

- [Mixins and Traits in Swift 2.0](http://matthijshollemans.com/2015/07/22/mixins-and-traits-in-swift-2/)

Another way to add functionality to an entity is mixin, which is cool

IoC container
--

Usage of IoC container can greatly change the app workflow, which praises Dependency Injection

- [Swinject](https://github.com/Swinject/Swinject)
- [Dip](https://github.com/AliSoftware/Dip)

Single Responsibility Principle
--

Always keep this in mind :]

Performance
--

Understanding of performance issue, especially some common uses like tableview pre rendering, helps

- [Easy UITableView optimizations](http://www.nsprogrammer.com/2013/10/easy-uitableview-buffering.html#uitv_sec2)
- [25 iOS App Performance Tips & Tricks](http://www.raywenderlich.com/31166/25-ios-app-performance-tips-tricks)
- [iOS Quick Tip: 5 Tips to Increase App Performance](http://code.tutsplus.com/tutorials/ios-quick-tip-5-tips-to-increase-app-performance--mobile-18503)
- [Mastering UIKit performance](https://yalantis.com/blog/mastering-uikit-performance/)

Main thread
--

It's good to care about our main thread so see if it is blocked

- [PSPDFUIKitMainThreadGuard.m](https://gist.github.com/steipete/5664345) This is a guard that tracks down UIKit access on threads other than main
- [Watchdog](https://github.com/wojteklu/Watchdog) Class for logging excessive blocking on the main thread

Overlay app icon with build information
--

- [Overlaying application version on top of your icon](http://merowing.info/2013/03/overlaying-application-version-on-top-of-your-icon/)

Show TODO FIXME as warnings
--

- [HOW TO HIGHLIGHT YOUR TODOS, FIXMES, & ERRORS IN XCODE](http://krakendev.io/blog/generating-warnings-in-xcode)
- [XToDo](https://github.com/trawor/XToDo) Xcode plugin to collect and list the `TODO`,`FIXME`,`???`,`!!!!`

Use this script in Run Script Build Phase

```sh
KEYWORDS="TODO:|VERIFY:|FIXME:|\?\?\?:|\!\!\!:"
find "${SRCROOT}" -name "*.h" -or -name "*.m" -print0 | xargs -0 egrep --with-filename --line-number --only-matching "($KEYWORDS).*\$" | perl -p -e "s/($KEYWORDS)/ warning: \$1/"
```

Breakpoints
--

There are some common needed breakpoints, like [Breakpoints_v2.xcbkptlist](https://gist.github.com/Ashton-W/5c1ede17f8cec1f8b529)

Code snippets
--

Code snippets auto generate common code

- [XcodeSwiftSnippets](https://github.com/burczyk/XcodeSwiftSnippets)
- [ACCodeSnippetRepositoryPlugin](https://github.com/acoomans/ACCodeSnippetRepositoryPlugin)

Utility class
--

We can avoid duplicated helper classes in projects by using some cool frameworks

- [Sugar](https://github.com/hyperoslo/Sugar) Sugar is a sweetener for your Cocoa implementations.

Xcode plugins
--

Using Xcode plugins can boost your productivity

- [Xcode Plugins](http://nshipster.com/xcode-plugins/)

And learning to write your own can assist you with more tasks

- [How To Create an Xcode Plugin](http://www.raywenderlich.com/94020/creating-an-xcode-plugin-part-1)

Xcode keyboard shortcuts
--

Navigate faster

- [Xcode Efficiency Tips: Keyboard Shortcuts](http://spin.atomicobject.com/2014/03/23/xcode-keyboard-shortcuts/)

xcconfig
--

Build yourself some predefined xcconfig

- [Using xcconfig files for your XCode Project](http://www.jontolof.com/cocoa/using-xcconfig-files-for-you-xcode-project/)
- [Warnings-xcconfig](https://github.com/boredzo/Warnings-xcconfig) An xcconfig (Xcode configuration) file for easily turning on a boatload of warnings in your project or its targets.
- [XcodeWarnings](https://github.com/jonreid/XcodeWarnings) xcconfig to enable lots of Xcode warnings

Strongly typed resource
--

There's some scripts that generate strongly typed resource classes

- [SwiftGen](https://github.com/AliSoftware/SwiftGen) A collection of Swift tools to generate Swift code (enums for your assets, storyboards, Localizable.strings, …)
- [R.swift](https://github.com/mac-cain13/R.swift) Get strong typed, autocompleted resources like images, fonts and segues in Swift projects

Continuous learning
--

Technologies move really fast. We must learn continuously. I have [ios-resources](https://github.com/onmyway133/ios-resources) to keep track of all the cool stuffs

- [Programming](http://merowing.info/2016/01/programming/)
- [How to be awesome Swift developer](http://blog.krzyzanowskim.com/2015/12/28/how-to-be-awesome-swift-developer/)

Also, I leverage Github as my notes [ios-issues](https://github.com/onmyway133/ios-issues)

Reference
--

- [ios-good-practices by futurice](https://github.com/futurice/ios-good-practices)
- [What are best practices that you use when writing Objective-C and Cocoa?](http://stackoverflow.com/questions/155964/what-are-best-practices-that-you-use-when-writing-objective-c-and-cocoa)
- [iOS-Best-Practices by jverkoey](https://github.com/jverkoey/iOS-Best-Practices)
- [iOS 7 Best Practices; A Weather App Case Stud](http://www.raywenderlich.com/55384/ios-7-best-practices-part-1)
- [iOS Tips by Krzysztof Zabłocki](http://www.merowing.info/2014/02/ios-tips/#.VAHx17ySwik)
