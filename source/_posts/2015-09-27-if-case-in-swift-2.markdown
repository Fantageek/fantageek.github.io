---
layout: post
author: onmyway133
title: "if case in Swift 2"
date: 2015-09-27 12:12:43 +0700
comments: true
categories:
---

If you 're trying to perform 2 nested `switch` just to find the most suitable combination, then `if case` in Swift 2 will save your day

Suppose I have a `Maybe` (Maybe is similar to Optional)

```
public enum Maybe<X> {
    case Just(X)
    case Nothing
}
```

A naive solution could use nested `switch`

```
func combine<X>(ma: Maybe<X>, mb: Maybe<X>, f:(X,X)->X) -> Maybe<X> {
    switch ma {
    case let .Just(valueA):
        switch mb {
        case let .Just(valueB):
            return .Just(f(valueA,valueB))
        case .Nothing:
            return .Nothing
        }
    case .Nothing:
        return .Nothing;
    }
}
```

Using `if case` with multiple `let` statements

```
func combine2<X>(ma: Maybe<X>, mb: Maybe<X>, f:(X,X)->X) -> Maybe<X> {
    if case let .Just(valueA) = ma, case let .Just(valueB) = mb {
        return .Just(f(valueA,valueB))
    } else {
        return .Nothing
    }
}
```

And enjoy

```
let ma = Maybe.Just(1)
let mb = Maybe.Just(2)
let mc = combine(ma, mb: mb, f: +)
let md = combine2(ma, mb: mb, f: +)
```

Here is the log if don't believe me :D

```
(lldb) po ma
▿ Maybe<Int>
  - Just : 1

(lldb) po mb
▿ Maybe<Int>
  - Just : 2

(lldb) po mc
▿ Maybe<Int>
  - Just : 3

(lldb) po md
▿ Maybe<Int>
  - Just : 3

```

Actually, there is another way, that is pattern matching against a tuple that contains both Maybe

```
func combine3<X>(ma: Maybe<X>, mb: Maybe<X>, f:(X,X)->X) -> Maybe<X> {
    switch (ma, mb) {
    case let (.Just(valueA), .Just(valueB)):
        return .Just(f(valueA, valueB))
    case _:
        return . Nothing
    }
}

```

This is OK for a simple case like this. But I find `if let` allows multiple statements, the following statement can access variable from the previous statements, which is nicer


## Reference
- [Match Me if you can: Swift Pattern Matching in Detail.](http://appventure.me/2015/08/20/swift-pattern-matching-in-detail/)
- [Swift 2: Pattern Matching with “if case”](http://natashatherobot.com/swift-2-pattern-matching-with-if-case/)
- [Swift 2: Control Flow Pattern Matching Examples](http://austinzheng.com/2015/09/23/pmatch-control-flow/)
