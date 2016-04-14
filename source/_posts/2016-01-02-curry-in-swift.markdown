---
layout: post
author: onmyway133
title: "Curry in Swift"
date: 2016-01-02 10:58:39 +0700
comments: true
categories:
---

Haskell is notorious for `currying`, and Swift has currying, too

I love ReactiveCocoa, RxSwift and I always take time to dig into it. The other day, I was practise making Signal based on this talk [UIKonf 2015 - Jens Ravens: Functional Reactive Programming without Black Magic](https://www.youtube.com/watch?v=AcDaWe3S75c)

Take a look at my repo [Signal](https://github.com/onmyway133/Signal)

filter
--

I was making a filter for a `Signal`. The idea of filter is that we should update signal if the Event is Next with right filtered value

`Signal.swift`
```
public func filter(f: T -> Bool) -> Signal<T>{
    let signal = Signal<T>()
    subscribe { result in
        switch(result) {
        case let .Success(value):
            if f(value) {
                signal.update(result)
            }
        case let .Error(error): signal.update(.Error(error))
        }
    }
    return signal
}
```

2 params
--

But having `Event` as another monad, I think it should be more encapsulated if that switching logic gets moved into the `Event`. Here the filter takes 2 params

`Event.swift`
```
func filter(f: T -> Bool, callback: (Event<T> -> Void)) {
        switch self {
        case let .Next(value) where f(value):
            callback(self)
        case .Failed:
            callback(self)
        default:
            break
    }
}
```

`Signal.swift`
```
public func filter(f: T -> Bool) -> Signal<T> {
    let signal = Signal<T>()

    subscribe { event in
        event.filter(f, callback: signal.update)
    }

    return signal
}
```

Currying
--

With currying, we can make `filter` a more abstract function, and defer the decision to pass the `callback` param. It is a little carried away but I find it helpful this way

Now `filter` accepts 1 param, and it returns a function that takes `callback` as its param

`Event.swift`
```
func filter(f: T -> Bool) -> ((Event<T> -> Void) -> Void) {
        return { g in
            switch self {
            case let .Next(value) where f(value):
                g(self)
            case .Failed:
                g(self)
            default:
                break
            }
        }
    }
```

`Signal.swift`
```
public func filter(f: T -> Bool) -> Signal<T> {
        let signal = Signal<T>()

        subscribe { event in
            event.filter(f)(signal.update)
        }

        return signal
    }
```

Curry syntax in Swift 2
--

Swift 2 supports curry syntax function

```swift

func sum(a: Int)(b: Int) -> Int {
    return a + b
}

let sumWith5 = sum(5)
let result = sumWith5(b: 10)
```

No more curry syntax in Swift 3
--

You may want to find out

- [Bidding farewell to currying](http://ericasadun.com/2015/12/18/bidding-farewell-to-currying/)

Reference
--

- [Higher order functions](http://learnyouahaskell.com/higher-order-functions)
- [What is the advantage of currying?](http://programmers.stackexchange.com/questions/185585/what-is-the-advantage-of-currying)
- [Practical use of curried functions?](http://stackoverflow.com/questions/4888480/practical-use-of-curried-functions?lq=1)
- [Introduction to Function Currying in Swift](https://robots.thoughtbot.com/introduction-to-function-currying-in-swift)
- [Swift Function Currying](http://www.russbishop.net/swift-function-currying)
