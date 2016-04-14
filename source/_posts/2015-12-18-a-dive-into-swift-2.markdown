---
layout: post
author: onmyway133
title: "A dive into Swift 2"
date: 2015-12-18 12:05:56 +0700
comments: true
categories:
---

Here are some of my notes when diving into Swift

Default parameter reorder
--

If a function arguments have default values, you can reorder them at call site.

```swift
func sum(num1 num1: Int = 1, num2: Int = 2) -> Int {
    return num1 + num2
}

let s = sum(num2: 10, num1: 20) // 30
```

Note that the first parameter `num1` must have external name

Tuple shuffle
--
See [Tuple shuffle in Swift](http://www.fantageek.com/blog/2015/11/27/tuple-shuffle-in-swift/)

```swift
var a = "one", b = "two"
(a, b) = (b, a)
print(a) // "two"
print(b) // "one"
```

Be careful with generic
--
Here is when I'm trying to do a [Resolver](https://github.com/onmyway133/Resolver)

```swift
func registerSingleton<T>(singleton: T) {
    register {
        singleton
    }
}

func register<T>(factory: () -> T) {
    print(factory.dynamicType)
}
```

Here I mistakenly use it, because generic can accept any type, even closure

```swift
registerSingleton {
    "onmyway133"
}
```

And now `factory.dynamicType` is of type `() -> () -> String`, not what I expect `() -> String`

Short notation works for any static member
--

- [Help Yourself to Some Swift](http://bandes-stor.ch/blog/2015/11/28/help-yourself-to-some-swift/)

```swift
extension CAMediaTimingFunction {
    static var EaseInEaseOut: CAMediaTimingFunction {
        return CAMediaTimingFunction(name: kCAMediaTimingFunctionEaseInEaseOut)
    }
}

let function: CAMediaTimingFunction = .EaseInEaseOut
```

We can also `.init` because the type of the property is already specified

```swift
extension CAMediaTimingFunction {
    static var EaseInEaseOut: CAMediaTimingFunction {
        return .init(name: kCAMediaTimingFunctionEaseInEaseOut)
    }
}
```

Resolving generic type
--
Supposed we have contrived method like this, which does not accept any parameter

```swift
func resolve<T>() -> T {
        if String(T) == "String" {
            return "onmyway133" as! T
        } else {
            fatalError()
        }
    }
```

We can use `as` to resolve the type, or specify the type

```swift
let a = resolve() as String // "onmyway133"
let b: String = resolve() // "onmyway133"
```

You can learn more when reading [Dip](https://github.com/AliSoftware/Dip)

Reference instance method
--

- [Instance Methods are Curried Functions in Swift](http://oleb.net/blog/2014/07/swift-instance-methods-curried-functions/)

Supposed we have

```swift
class Cat {
    init() {

    }

    func meo() {
        print("meo meo")
    }
}
```

We can make a reference to that instance method
```swift
let cat = Cat()
let ref = Cat.meo
ref(cat)()
```

We can see that `ref` is of type `Cat -> () -> ()`

precondition
--
- [Swift asserts - the missing manual](http://blog.krzyzanowskim.com/2015/03/09/swift-asserts-the-missing-manual/)

Check for a condition, works even in release mode.

```swift
func hello(name: String) {
    precondition(!name.isEmpty, "Name must not be empty")
    print("hello " + name)
}
```
strideof
--

- [Data Type Size Calculation](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/BuildingCocoaApps/InteractingWithCAPIs.html#//apple_ref/doc/uid/TP40014216-CH8-ID185)
- [http://chris.eidhof.nl/posts/swift-c-interop.html](http://chris.eidhof.nl/posts/swift-c-interop.html)

> In C, the sizeof operator returns the size of any variable or data type. In Swift, you use the sizeof function to get the size of a given type, or the sizeofValue function to get the size of the type of a given value

```swift
print(strideof(timeval)) // "16"
```

Arguments are tuple
--

- [Tuple as Function arguments](https://medium.com/swift-programming/tuple-as-function-argument-619523b6e06b#.n05z7mpbw)

- Tuple elements must be the same number, of the same types, and have the same external parameter name as in function

```swift
func greet(name: String, age: Int) {
    print("Hello, this is " + name + ", \(age) years old")
}

let params = ("Luke", age: 17)

greet(params)
```
