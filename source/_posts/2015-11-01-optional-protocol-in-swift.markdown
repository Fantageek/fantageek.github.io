---
layout: post
author: onmyway133
title: "Optional protocol in Swift"
date: 2015-11-01 09:46:36 +0700
comments: true
categories:
---

Swift supports optional protocol via `@objc` keyword, like [OPTIONAL PROTOCOL](http://en.swifter.tips/objc-protocol/)

```swift
@objc protocol OptionalProtocol {
    optional func optionalMethod()         // Optional
    func necessaryMethod()                 // Required
    optional func anotherOptionalMethod()  // Optional
}
```

If there's Enum, Tuple (which are not available in Objective C) in your protocol methods, then this @objc trick does not work

Luckily, Swift 2 supports Protocol Extension, which we can use to provide default implementation. So the conformance only has to implement the methods that it truly needs

Take a look at [PlayerDelegate](https://github.com/onmyway133/Cine/blob/master/Pod/Classes/Helper/PlayerDelegate.swift) in my [Cine](https://github.com/onmyway133/Cine)

You need to mark your protocol extension `public` in order for your consumer to use it

`PlayerDelegate.swift`
```swift
public protocol PlayerDelegate : class {
    func player(player: Player, didChangeStatus status: Status)
    func player(player: Player, didChangePlaybackState state: PlaybackState)
    func player(player: Player, didChangeLoadState state: LoadState)

    func player(player: Player, didFinishWithResult: Result)

    func playerDidChangeCurrentItem(player: Player)
    func playerDidLoadDuration(player: Player)
    func playerDidTick(player: Player)

    func playerDidBeginSeeking(player: Player)
    func playerDidEndSeeking(player: Player)
}

public extension PlayerDelegate {
    func player(player: Player, didChangeStatus status: Status) {}
    func player(player: Player, didChangePlaybackState state: PlaybackState) {}
    func player(player: Player, didChangeLoadState state: LoadState) {}

    func player(player: Player, didFinishWithResult: Result) {}

    func playerDidChangeCurrentItem(player: Player) {}
    func playerDidLoadDuration(player: Player) {}
    func playerDidTick(player: Player) {}

    func playerDidBeginSeeking(player: Player) {}
    func playerDidEndSeeking(player: Player) {}
}
```
