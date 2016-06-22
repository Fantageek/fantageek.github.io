---
title: Overloading function in Swift
date: 2016-06-22 23:58:59
tags:
---

### Function

Functions in Swift are distinguishable by

- parameter label
- parameter type
- return type

so that these are all valid

```swift
struct A {

  // return type
  func get() -> String { return "" }
  func get() -> Int { return 1 }

  // mix of parameter type and return type
  func get(param: String) -> String { return "" }
  func get(param: String) -> Int { return 1 }
  func get(param: Int) -> Int { return 1 }
  func get(param: Int) -> String { return "" }

  // parameter label
  func set(int: Int) {}
  func set(string: String) {}

  // concrete type from generic
  func get(param: Array<String>) -> String { return "" }
  func get(param: Array<Int>) -> Int { return 1 }
}

```

When you specialize a generic type, like `Array<Int>`, you're actually using a concrete type

Unfortunately, this does not work for NSObject subclass

> Method 'get()' with Objective-C selector 'get' conflicts with previous declaration with the same Objective-C selector

```swift
class B: NSObject {

  func get() -> String { return "" }
  func get() -> Int { return 1 }
}
```

### Generic function

We can overload generic functions as well

```swift
func f<T>(t: T) {
  print("T")
}

func f(string: String) {
  print("String")
}

func f(int: Int) {
  print("Int")
}
```
