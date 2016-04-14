---
layout: post
author: onmyway133
title: "Tuple shuffle in Swift"
date: 2015-11-27 08:51:35 +0700
comments: true
categories:
---

Tuple shuffle exists in Swift.

```swift
var a = "one", b = "two"
(a, b) = (b, a)
print(a) // "two"
print(b) // "one"
```

```swift
var items = ["one", "two", "three"]
(items[0], items[2]) = (items[2], items[0])
print(items) // "["three", "two", "one"]"
```

With an example of array shuffle using `Fisher-Yates`, let's leverage tuple shuffle instead of `swap`

```swift
extension CollectionType {
    /// Return a copy of `self` with its elements shuffled
    func shuffle() -> [Generator.Element] {
        var list = Array(self)
        list.shuffleInPlace()
        return list
    }
}

extension MutableCollectionType where Index == Int {
    /// Shuffle the elements of `self` in-place.
    mutating func shuffleInPlace() {
        // empty and single-element collections don't shuffle
        if count < 2 { return }

        for i in 0..<count - 1 {
            let j = Int(arc4random_uniform(UInt32(count - i))) + i
            guard i != j else { continue }
            (self[i], self[j]) = (self[j], self[i])
            // instead of swap(&self[i], &self[j])
        }
    }
}

[1, 2, 3, 4, 5, 6, 7, 8, 9, 10].shuffle()

```

Reference
--
- [Swift: Swappity Doo-dah!](http://ericasadun.com/2015/06/02/swift-swappity-doo-dah/)
