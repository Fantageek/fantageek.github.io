---
layout: post
author: onmyway133
title: "Push vs Pull Signal"
date: 2016-01-03 09:21:36 +0700
comments: true
categories:
---

The idea of Signal may originate from [Elm Reactivity](http://elm-lang.org/guide/reactivity), and it has now been widely adopted in iOS

I once asked [What are examples of hot and cold signal in ReactiveCocoa?](http://stackoverflow.com/questions/29374792/what-are-examples-of-hot-and-cold-signal-in-reactivecocoa)

- [When to use IEnumerable vs IObservable?](http://stackoverflow.com/questions/17082255/when-to-use-ienumerable-vs-iobservable)
- [ReactiveCocoa Framework Overview](Framework Overview)
- [Rx – for beginners (part 9): Hot Vs. Cold observable](http://blogs.microsoft.co.il/bnaya/2010/03/13/rx-for-beginners-part-9-hot-vs-cold-observable/)
- [RxSwift Hot and Cold Observables](https://github.com/ReactiveX/RxSwift/blob/master/Documentation/HotAndColdObservables.md)

Whether it is hot vs cold, Signal vs Signal Producer, Observable vs Enumerable, ... it's good to understand how it gets implemented, so that to have a good sense of how they work

Monad
--

- [Understanding Monad](http://www.fantageek.com/blog/2015/07/10/understanding-monad/)

Basically, Signal and its Result are just monads, which are thing that can be mapped and chained.

Signal makes use of deferred execution callback blocks, and `push vs pull` is just how the Signal updates its value and the order the callbacks are called

Execution callback block is that we pass a function to another function, and it will get called when appropriated

Sync vs Async
--

Monad can be in either sync or async mode. Sync is easier to understand, but async is somewhat you're already familiar and used in practice

Basically,

- Sync: you get the returned value right away via `return`
- Aync: you get the returned value via callback block

Here is an example of a simple function

```
// Sync
func sum(a: Int, b: Int) -> Int {
    return a + b
}

// Async
func sum(a: Int, b: Int, completion: Int -> Void) {
    // Assumed it is a very long task to get the result
    let result = a + b

    completion(result)
}
```

Here is an example of `Event`

```
// Sync
public func map<U>(@noescape f: T -> U) -> Event<U> {
    switch self {
    case let .Next(value):
        return .Next(value: f(value))
    case let .Failed(error):
        return .Failed(error: error)
    }
}

// Async
public func map<U>(f: (T, U -> Void) -> Void) -> ((Event<U> -> Void) -> Void) {
        return { g in   // g: Event<U> -> Void
            switch self {
            case let .Next(value):
                f(value) { transformedValue in  // transformedValue: U
                    g(.Next(value: transformedValue))
                }
            case let .Failed(error):
                g(.Failed(error: error))
            }
        }
    }
```

Push Signal
--

- [Signal.swift](https://github.com/onmyway133/Signal/blob/master/Pod/Classes/Signal.swift)
- [UIKonf 2015 - Jens Ravens: Functional Reactive Programming without Black Magic](https://www.youtube.com/watch?v=AcDaWe3S75c)

Take a look at my Push Signal, called [Signal](https://github.com/onmyway133/Signal/blob/master/Pod/Classes/Signal.swift), it is like how [Promise A+ Then](https://github.com/onmyway133/Then/blob/master/Pod/Classes/Promise.swift) works

### Implementation

```
public final class Signal<T> {
    var event: Event<T>?
    var callbacks: [Event<T> -> Void] = []

    func notify() {
        guard let event = event else {
            return
        }

        callbacks.forEach { callback in
            callback(event)
        }
    }

    func update(event event: Event<T>) {
        dispatch_sync(lockQueue) {
            self.event = event
        }

        notify()
    }

    public func subscribe(f: Event<T> -> Void) -> Signal<T> {
        // Callback
        if let event = event {
            f(event)
        }

        callbacks.append(f)

        return self
    }

    public func map<U>(f: T -> U) -> Signal<U> {
        let signal = Signal<U>()

        subscribe { event in
            signal.update(event: event.map(f))
        }

        return signal
    }
}
```

### Usage
```
let signal = Signal<String>()

signal.map { value in
        return value.characters.count
    }.subscribe { event in
        if case let .Next(value) = event {
            XCTAssert(value == 4)
        } else {
            XCTAssert(false)
        }
}

signal.sendNext("test")
```

### Callbacks
Given a chained signals like this

A -(map)-> B -(flatMap)-> C -(flatMap)-> D -(subscribe)

- The idea is we send event to the source signal, and it propagates events through via callbacks.
- Triggered by sending event to the source signal.
- We must keep A as it keeps the others around
- We subscribe the last D
- We send event to the first A
- A 's callback gets called, it it in turn calls callback of B with the result of A 's map, then B 's callback calls C 's callback with the result of B
's flatMap, ...

Pull Signal
--

- [Future.swift](https://github.com/onmyway133/Signal/blob/master/Pod/Classes/Future.swift)
- [Swift Sync and Async Error Handling - iOS Conf SG 2015](https://www.youtube.com/watch?v=mbd6g7NfR-8)

Take a look at my Pull Signal, called [Future](https://github.com/onmyway133/Signal/blob/master/Pod/Classes/Future.swift)

### Implementation

Here `operation` is a task, when called and completed, will notify its `completion`

```
public struct Future<T> {
    let operation: (Event<T> -> Void) -> Void

    public init(operation: (Event<T> -> Void) -> Void) {
        self.operation = operation
    }

    public func start(completion: Event<T> -> Void) {
        operation() { event in
            completion(event)
        }
    }

    public func map<U>(f: T -> U) -> Future<U> {
        return Future<U> { completion in
            self.start { event in
                completion(event.map(f))
            }
        }
    }
}
```

### Usage

```
let _ = Future<String> { completion in
        // There is some work here
        completion(Event(value: "test"))
    }
    .map { value in
        value.characters.count
    }.start { event in
        if case let .Next(value) = event {
            XCTAssert(value == 4)
        } else {
            XCTAssert(false)
        }
    }
```

### Callbacks
Given a chained signals like this

A -(map)-> B -(flatMap)-> C -(flatMap)-> D -(subscribe)

- The idea is we subscribe to the final signal D, and it cause the previous signals to action.
- Triggered by subscribing to the final signal.
- We must keep D as it keeps the others around
- We subscribe the last D
- D 's operation actions, and it cause C 's operation to action, ... then A 's operation actions. It is in A that the task is performed (like fetching network, retrieving database, file access, heavy computation, ...) to get the result, and A 's completion gets called. Then A's completion calls B 's completion with the result mapped by B 's map, ... all the way to the subscriber 's completion block
