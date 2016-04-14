---
layout: post
author: onmyway133
title: "Digit grouping in Swift"
date: 2016-01-21 00:10:09 +0700
comments: true
categories:
---

When working on [Scale](https://github.com/onmyway133/Scale) I think it's good to have a way to group the digit so that it is easier to reason

Luckily, Swift already supports this. See [The Swift Programming Language - Numeric Literals](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/TheBasics.html#//apple_ref/doc/uid/TP40014097-CH5-ID309)

> Numeric literals can contain extra formatting to make them easier to read. Both integers and floats can be padded with extra zeros and can contain underscores to help with readability. Neither type of formatting affects the underlying value of the literal

```swift
let paddedDouble = 000123.456
let oneMillion = 1_000_000
let justOverOneMillion = 1_000_000.000_000_1
```

Talking about grouping digits after the decimal point, it is interesting too [Convention of digit grouping after decimal point](http://math.stackexchange.com/questions/182775/convention-of-digit-grouping-after-decimal-point)

So now we have

```swift
public enum MetricUnit: Double {
    case nano = 0.000_000_001
    case micro = 0.000_001
    case milli = 0.001
    case centi = 0.01
    case deci = 0.1
    case base = 1
    case deka = 10
    case hecto = 100
    case kilo = 1_000
    case mega = 1_000_000
    case giga = 1_000_000_000
    case tera = 1_000_000_000_000
    case peta = 1_000_000_000_000_000

    static var defaultScale: Double {
        return MetricUnit.base.rawValue
    }
}
```
