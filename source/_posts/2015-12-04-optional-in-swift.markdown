---
layout: post
author: onmyway133
title: "Optional in Swift"
date: 2015-12-04 17:50:20 +0700
comments: true
categories:
---

Meet Optional
--
You have seen these

```swift
// The image object for the specified file, or nil if the method could not find the specified image.
init?(named name: String) -> UIImage

// Returns the first element of self, or nil if self is empty.
var first: Self.Generator.Element? { get }

// The index path of the cell or nil if the specified cell is not in the collection view
func indexPathForCell(_ cell: UICollectionViewCell) -> NSIndexPath?
```

You will see lots of optional throughout Foundation and UIKit. It represents a context in which there is a value, or there is not.

In Objective, you can have a pointer which can points to something, or nil. But Optional makes it more generic. You can now have `Int?, UIImage?, String?, UIWindow? ...`

The idea of optional type is not new, you may have seen it through `data Maybe a = Nothing | Just a` in Haskell, `enum Option<T> { None, Some(T) }` in Rust, `Optional<T>` in Java 8, `System.Nullable<T>` in C#, ...

In Swift, optional is all about `?` and `!`. The latter is for the lazy one

Optional is enum
--
### Implementation
Optional is implemented using associated type enum and generic

```swift
enum Optional<T> {
    case Some(T)
    case None
}
```

### Declaration
When you see
```swift
let name: String? = "hello world"
```

It is actually
```swift
let name = Optional<String>.Some("hello world")
```

### Checking
When you see
```swift
if let name = name {
    print(name)
}
```

It is actually
```swift
if case let .Some(name) = name {
    print(name)
}
```

Forced Unwrapping
--
You can use `!` to forcibly unwrap an optional. If there isn't a value, it will crash `fatal error: unexpectedly found nil while unwrapping an Optional value`

```swift
let name: String? = "Fantageek"
print(name!)
```


### Implementation
Forced Unwrapping operator `!` is implemented like this

```swift
let name = Optional<String>.Some("Fantageek")

postfix operator ! {}

postfix func !<T> (optional: Optional<T>) -> T {
    if case let .Some(value) = optional {
        return value
    } else {
        fatalError("unexpectedly found nil while unwrapping an Optional value")
    }
}

print(name!)
```

Implicitly Unwrapped Optionals
--
Optional is good, but you need to check for its value every time you need to use

```swift
let name: String? = "Fantageek"

if let name = name {
    print(name)
}
```

In case you 're sure that the optional always has a value, you can use `!` to define `Implicitly Unwrapped Optionals`, which is an optional that automatically unwraps its value when you use it

```swift
let name: String! = "Fantageek"

// Just use it
print(name)
```

Implicitly Unwrapped Optionals are Optionals, so you can check for nil and use optional binding

```swift
if let name = name {
    print(name)
}
```

Optional in collection
--
Because optional is an enum, we can have collection of values and nils

```swift
let names: [String?] = ["Shanks", "Big Mom", nil, "Kaido"]

let fruits: [String: String?] = [
    "Luffy": "Gomu Gomu",
    "Zoro": nil,
    "Ace": "Mera Mera",
    "Shanks": nil,
    "Smoker": "Moku Moku"
]
```

Optional binding
--
We can use optional binding through `if let`. Even cooler, Swift now support multiple optional binding

```swift
let firstName: String? = "Monkey"
let lastName: String? = "Luffy"

if let firstName = firstName, lastName = lastName {
    print(firstName + " " + lastName)
}
```

Double optional
--
Swift has special syntax for double optional, that is `??`

```swift
let name: String?? = "hello world" // Optional<Optional<String>>

if let outer = name, inner = outer {
    print(inner)
}
```

Double optional is also a result of a this cast

```swift
let name: AnyObject? = "Fantageek"
let result = name as? String? // String??
```

Nil Coalescing Operator
--
> The nil coalescing operator (a ?? b) unwraps an optional a if it contains a value, or returns a default value b if a is nil.

The right hand of `??` is actually a closure

```swift
func ??<T>(optional: T?, defaultValue: @autoclosure () -> T) -> T
```

```swift
let name: String? = "Fantageek"
let website = name ?? "No website"
```

It can be chained

```swift
let result = maybe ?? possibly ?? "default"
```

It works in string interpolation, too

```swift
let website = "\(name ?? "No website")"
```

It is implemented like this

```swift
a != nil ? a! : b
```

Optional chaining
--
Optional might be nil, so querying for its methods, properties, subscripts will return another optional

```swift
let name: String? = "Fantageek"

let uppercase = name?.uppercaseString // String?
let count = name?.characters.count // Distance?
```

```swift
let items:[Int?] = [1, 2, 3, nil, 4, 5]
let item = items[4]
```

### Optional delegate
A delegate might be nil, and it might not implement optional methods. So we must use `?` when calling it

```swift
var delegate: UITableViewDataSource? = // get from somewhere
let tableView = UITableView()

let sectionCount = delegate?.numberOfSectionsInTableView?(tableView) // Int?
```

### Optional callback
A callback might be nil

```swift
var callback: (String? -> Void)? = // get from somwhere

callback?("a response")
```

Optional pattern
--
> The new optional pattern, myValue?, is equivalent to .Some(myValue) for optionals

If you have an optional enum

```swift
enum Weapon {
    case Sword(attack: Int)
    case Shield(defense: Int)
    case Potion(property: String)
}

let weapon: Weapon? = .Sword(attack: 10)
```

You can ask for the case

```swift
if case .Sword? = weapon {
    print("a sword")
}

// equivalent
if case .Some(.Sword) = weapon {
    print("a sword")
}
```

You can ask for the value associated with the case

```swift
if case let .Sword(attack)? = weapon {
    print("attack " + "\(attack)")
}

// equivalent
if case let .Some(.Sword(attack)) = weapon {
    print("attack " + "\(attack)")
}
```

Of course, it works with `switch`, too

```swift
switch weapon {
case let .Sword(attack)?:
    print("attack " + "\(attack)")
default:
    print("")
}

// equivalent
switch weapon {
case let .Some(.Sword(attack)):
    print("attack " + "\(attack)")
default:
    print("")
}

```

> An optional pattern matches values wrapped in a Some(Wrapped) case of an Optional<Wrapped> or ImplicitlyUnwrappedOptional<Wrapped> enumeration

These are equivalent

```swift
let someOptional: Int? = 42
// Match using an enumeration case pattern
if case .Some(let x) = someOptional {
    print(x)
}

// Match using an optional pattern
if case let x? = someOptional {
    print(x)
}
```

> The optional pattern provides a convenient way to iterate over an array of optional values in a for-in statement, executing the body of the loop only for non-nil elements.

```swift
let arrayOfOptionalInts: [Int?] = [nil, 2, 3, nil, 5]
// Match only non-nil values
for case let number? in arrayOfOptionalInts {
    print("Found a \(number)")
}
```

Cast
--
There are `as?` and `as!`, see [Type Casting in Swift 2](http://www.fantageek.com/blog/2015/09/23/type-casting-in-swift/)

Initializer
--

### Failable Initializers
> A failable initializer creates an optional value of the type it initializes

```swift
struct Animal {
    let species: String
    init?(species: String) {
        if species.isEmpty { return nil }
        self.species = species
    }
}

let someCreature = Animal(species: "Giraffe") // Animal?
```

Swift also supports `The init! Failable Initializer`

Throw
--
`try?` tries to perform an operation that may throw, returns an optional containing the returned value if succeeded. `try!` does the same, but you get an Implicitly Unwrapped Optional

```swift
enum Error: ErrorType {
    case NotAMember
}

func isInStrawHatPirates(name: String) throws -> String {
    let members = [
        "Monkey D. Luffy",
        "Roronoa Zoro",
        "Nami",
        "Usopp",
        "Sanji",
        "Chopper",
        "Nico Robin",
        "Franky",
        "Brook",
    ]

    if members.contains(name) {
        return "welcome"
    } else {
        throw Error.NotAMember
    }
}

let message = try? isInStrawHatPirates("Sanji") // String?
```

Functional optional
--
Optional is a functor, applicative functor, and monad

Note that all these methods are implemented as extension on Optional, see my [gist](https://gist.github.com/onmyway133/48c969c8baf4e7e77ee0)

### map
> If self == nil, returns nil. Otherwise, returns f(self!).

```swift
func countCharacters(string: String) -> Int {
    return string.characters.count
}

let name: String? = "hello"

let count = name.map(countCharacters) // Int?
```

It is implemented like this

```swift
extension Optional {
    public func map<U>(@noescape f: (Wrapped) throws -> U) rethrows -> U? {
        if let value = self {
            return try f(value)
        } else {
            return nil
        }
    }
}
```

### flatMap
> Returns nil if self is nil, f(self!) otherwise.

Let 's use `countCharactersIfNotEmpty`, a function returning nil if it receives empty string. Yeah, it does not like empty string

```swift
func countCharactersIfNotEmpty(string: String) -> Int? {
    if string.isEmpty {
        return nil
    } else {
        return string.characters.count
    }
}

let count = name.flatMap(countCharactersIfNotEmpty) // Int?

```

It is implemented like this

```swift
public func flatMap<U>(@noescape f: (Wrapped) throws -> U?) rethrows -> U? {
        if let value = self {
            return try f(value)
        } else {
            return nil
        }
    }
```

### map vs flatMap

Note that in `map`, `f` is of type `Wrapped -> U`, while in `flatMap`, `f` is of type `Wrapped -> U?`

If you use `map` with `Wrapped -> U?`, you 'll get double optional

```swift
let countWithMap = name.map(countCharactersIfNotEmpty)  // Int??
let countWithFlatMap = name.flatMap(countCharactersIfNotEmpty) // Int?
```

`flatMap` can also be implemented using `map + flatten`, hence the name `flatMap`. As you already know, it is called `Forced Unwrapping`

```swift
public func flatMap<U>(@noescape f: (Wrapped) throws -> U?) rethrows -> U? {
        let result = try self.map(f) // U??
        return result!
    }
```

### apply

Here is how Optional works like applicative functor, note that `f` is of type `(Wrapped -> U)?`

```swift
let name: String? = "Fantageek"

let function: (String -> Int)? = countCharacters
let countWithApply = name.apply(function) // Int?
```

This is how it is implemented

```swift
public func apply<U>(f: (Wrapped -> U)?) -> U? {
        if let f = f {
            return self.map(f)
        } else {
            return nil
        }
    }
```

Reference
--
- [Option type](https://en.wikipedia.org/wiki/Option_type)
- [Swift 2: Control Flow Pattern Matching Example](http://austinzheng.com/2015/09/23/pmatch-control-flow/)
- [Implementing printing versions of “try?” and “try!” — on steroids! in #swiftlang](http://ericasadun.com/2015/11/05/implementing-printing-versions-of-try-and-try-on-steroids-in-swiftlang/)
- [Match Me if you can: Swift Pattern Matching in Detail](http://appventure.me/2015/08/20/swift-pattern-matching-in-detail/#sec-4-1)
- [What is an Optional in Swift](http://www.drewag.me/posts/what-is-an-optional-in-swift)
- [Swift 2.0: Map and FlatMap Demystified](http://www.uraimo.com/2015/10/08/Swift2-map-flatmap-demystified/)
- [The Power Of Map And FlatMap Of Swift Optionals](http://blog.xebia.com/the-power-of-map-and-flatmap-of-swift-optionals/)
- [Functor and Monad in Swift ](http://www.javiersoto.me/post/106875422394)
- [Swift Functors, Applicatives, and Monads in Pictures](http://www.mokacoding.com/blog/functor-applicative-monads-in-pictures/)
- [Matching with Swift's Optional Pattern](http://www.figure.ink/blog/2015/12/6/matching-with-swifts-optional-pattern)
