---
layout: post
author: onmyway133
title: "A simple resolver for Swift"
date: 2015-12-18 12:06:07 +0700
comments: true
categories:
---


The Marvel world
--

### Ant Man

We know Ant Man is Hank Pym

```swift
struct AntManSuit {
    let name: String
}

struct HankPym {
    let suit = AntManSuit(name: "Ant Man ID #101")

    func fight() {
        print("Fighting with the suit named " + suit.name)
    }
}

let hankPym = HankPym()
hankPym.fight()

```

Everytime HankPym is created, he always uses the Ant Man suit. This time he is so coupled to the role Ant Man

### More suits
Well, he does not have to be too dependent on the Ant Man suit. We know Hank Pym is a genius scientist, he has more suits to use. Let's make it decoupled

Using Dependency Injection

```swift
protocol Suit {
    var name: String { get }
}

struct AntManSuit: Suit {
    let name: String
}

struct YellowJacketSuit: Suit {
    let name: String
}

struct HankPym {
    let suit: Suit

    func fight() {
        print("Fighting with the suit named " + suit.name)
    }
}

let suit = YellowJacketSuit(name: "Yellow Jacket ID #33")
let hankPym = HankPym(suit: suit)
hankPym.fight()
```

Now Hank Pym can be more flexible on which suit to use.

Dependency Injection
--

The technique we just saw is called `Dependency Injection`, in which Hank Pym does not need to create the Suit, it will be provided through constructor or property.

Dependency Inversion Principle
--
In the first example, Hank Pym is dependent on the concrete implementation of the Suit

In the second example, both Hank Pym and the suits are dependent on the Suit protocol. This way Hank Pym only knows about the Suit protocol, and future suits must be crafted to that it conforms to the Suit protocol

This way the dependency is inverted

> High level modules should not depend upon low level modules. Both should depend upon abstractions.

> What is the high level policy? It is the abstractions that underlie the application, the
truths that do not vary when the details are changed

Inversion of Control Container
--
You may ask yourself [Why is Inversion of Control named that way?](http://programmers.stackexchange.com/questions/205681/why-is-inversion-of-control-named-that-way)

### Framework vs library
People said "the framework calls you but you call the library"

### Command line vs GUI
See [What is Inversion of Control?](http://stackoverflow.com/a/3108/1418457)

For example, in an old school menu, you might have:

```
print "enter your name"
read name
print "enter your address"
read address
etc...
store in database
```

thereby controlling the flow of user interaction.

In a GUI program or some such, instead we say

```
when the user types in field a, store it in NAME
when the user types in field b, store it in ADDRESS
when the user clicks the save button, call StoreInDatabase
```

You how have a brief understanding of how IoC means

### IoC container
In the 2nd example of the Suit protocol, you can see how there is a inversion of control. What if there is a container that contains all the Suit conformances?

Let's use my [Resolver](https://github.com/onmyway133/Resolver)

```swift
let resolver = Resolver()
resolver.register {
    YellowJacketSuit(name: "YellowJacket ID #404") as Suit
}

let suit = try! resolver.resolve() as Suit
let hankPym = HankPym(suit: suit)
```

Quite helpful, right? :]

Features
--
Actually, IoC container helps you more than that.

- Circular Dependency Injection
- Auto Injection
- Object Scope
- ...

There are some IoC containers in Swift

### [Swinject](https://github.com/Swinject/Swinject)

```swift
let container = Container()
container.register(AnimalType.self) { _ in Cat(name: "Mimi") }
container.register(PersonType.self) { r in
     PetOwner(pet: r.resolve(AnimalType.self)!)
}
```

Swinject requires explicit type declaration. It has SwinjectStoryboard, which helps configuring the dependency for your view controller

### [Dip](https://github.com/AliSoftware/Dip)
Dip leverages generic and encourage protocols

```swift
container.register { ServiceImp() as Service }
let service = try! container.resolve() as Service
```

You 'll learn a lot just by reading Dip source code, on how factory and factory type are stored and checked using generic

```swift
public func resolve<T, F>(tag tag: Tag? = nil, builder: F->T) throws -> T {
    let key = DefinitionKey(protocolType: T.self, factoryType: F.self, associatedTag: tag)
    let nilTagKey = tag.map { _ in DefinitionKey(protocolType: T.self, factoryType: F.self, associatedTag: nil) }

    guard let definition = (self.definitions[key] ?? self.definitions[nilTagKey]) as? DefinitionOf<T, F> else {
      throw DipError.DefinitionNotFound(key)
    }

    let usingKey: DefinitionKey? = definition.scope == .ObjectGraph ? key : nil
    return _resolve(tag, key: usingKey, definition: definition, builder: builder)
  }
```

Build your own simple IoC container
--
You may have discovered, that the idea of all those framework is to use closure as factory method

```swift
let factory = {
    YellowJacketSuit(name: "YellowJacket ID #007") as Suit
}

let suit = factory()
```

All we have to do is to store these factories closure

Take a look at my gist [SimpleResolver.swift](https://gist.github.com/onmyway133/3244cde1eaf8f2cd2ecb)

```swift
class SimpleResolver {
    var factories = [String: Any]()

    func factory<T>(factory: () -> T) {
        let key = String(T.self)
        factories[key] = factory
    }

    func resolve<T>() -> T {
        let key = String(T.self)
        if let factory = factories[key] as? () -> T {
            return factory()
        } else {
            fatalError("Registration not found")
        }
    }
}

let resolver = SimpleResolver()

resolver.factory {
    YellowJacketSuit(name: "YellowJacket IS #009") as Suit
}

let suit = resolver.resolve() as Suit
print(suit.name)
```

Reference
--
- [Dependency Injection Framework for Swift - Introduction to Swinject](https://yoichitgy.github.io/post/dependency-injection-framework-for-swift-introduction-to-swinject/)
- [IoC container in Swift](http://ilya.puchka.me/ioc-container-in-swift/)
- [swift-ioc](https://github.com/chakrit/swift-ioc)
- [Dip](https://github.com/AliSoftware/Dip)
- [Typhoon](http://typhoonframework.org/)
