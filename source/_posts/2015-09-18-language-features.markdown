---
layout: post
author: onmyway133
title: "Language features"
date: 2015-09-18 23:29:11 +0700
comments: true
categories: programming
---

As you know, in the [Pragmatic Progarmmer](http://www.amazon.com/The-Pragmatic-Programmer-Journeyman-Master/dp/020161622X), section `Your Knowledge Portfolio`, it is said that
> Learn at least one new language every year. Different languages solve the same problems in different ways. By learning several different approaches, you can help broaden your thinking and avoid getting stuck in a rut. Additionally, learning many languages is far easier now, thanks to the wealth of freely available software on the Internet

I see learning programming languages as a chance to open up my horizon and learn some new concepts. It also encourage good habit like immutability, composition, modulation, ...

I'd like to review some of the features of all the languages I 've been played with. Some are useful, some just make me intertested or say "wow"

### Curly braces
Each language can have its own style of grouping block of code, but I myself like the curly braces the most, which are cute :]

Some like C, Java, Swift, ... use curly braces

`Swift`
```
init(total: Double, taxPct: Double) {
    self.total = total
    self.taxPct = taxPct
    subtotal = total / (taxPct + 1)
  }
```

Some like Haskell, Python, ... use indentation

`Haskell`
```
bmiTell :: (RealFloat a) => a -> String  
bmiTell bmi  
    | bmi <= 18.5 = "You're underweight, you emo, you!"  
    | bmi <= 25.0 = "You're supposedly normal. Pffft, I bet you're ugly!"  
    | bmi <= 30.0 = "You're fat! Lose some weight, fatty!"  
    | otherwise   = "You're a whale, congratulations!"  
```

Some like Elixir use keyword list

`ELixir`
```
if false, do: :this, else: :that
```

### Named parameter
Language like Objective C, Swift offer named parameter, which make it easier to reason about a function call
```
func sayHello(to person: String, and anotherPerson: String) -> String {
    return "Hello \(person) and \(anotherPerson)!"
}
```

### Explicit type
Language like C, Swift, Java, ... have type information in parameter and in return, which make it easier to reason about a function call

`Swift`
```
func sayHello(personName: String, alreadyGreeted: Bool) -> String {
    if alreadyGreeted {
        return sayHelloAgain(personName)
    } else {
        return sayHello(personName)
    }
}
```
### List comprehension
Languages like Haskell, Pythin, Elixir, support list comprehension

`Elixir`
```
iex> for n <- [1, 2, 3, 4], do: n * n
[1, 4, 9, 16]
```

### First class function
I enjoy functional programming, so first class function support in Javascript, Swift, Haskell, Elixir, ... really make me happy

`Haskell`
```
zipWith' (*) (replicate 5 2) [1..]
```

### Curry
> Currying is the technique of translating the evaluation of a function that takes multiple arguments (or a tuple of arguments) into evaluating a sequence of functions, each with a single argument (partial application)

Language like Swift, Haskell, ... have curry by default. Some like Javascript can use libraries (Lodash, ...) to achieve this. In Haskell, every function officially only takes one parameter.

`Haskell`
```
multThree :: (Num a) => a -> a -> a -> a  
multThree x y z = x * y * z
```

By calling functions with too few parameters, we're creating new functions on the fly.

`Javascript`
```
var curry = require('lodash.curry');
var map = curry(function(f, ary) {
  return ary.map(f);
});
var getChildren = function(x) {
  return x.childNodes;
};

var allTheChildren = map(getChildren);
```

### Pattern matching    
I find pattern matching as a better way around `if else` statement

Swift supports pattern matching in `switch` statement

`Swift`
```
enum Trades {
    case Buy(stock: String, amount: Int, stockPrice: Float)
    case Sell(stock: String, amount: Int, stockPrice: Float)
}

let aTrade = Trades.Buy(stock: "APPL", amount: 200, stockPrice: 115.5)

switch aTrade {
case .Buy(let stock, let amount, _):
    process(stock, amount)
case .Sell(let stock, let amount, _):
    process(stock, amount * -1)
}

```

Some like Haskell, Elixir, ... also pattern matches on function name, which makes it work great for recursion

`Haskell`
```
sayMe :: (Integral a) => a -> String  
sayMe 1 = "One!"  
sayMe 2 = "Two!"  
sayMe 3 = "Three!"  
sayMe 4 = "Four!"  
sayMe 5 = "Five!"  
sayMe x = "Not between 1 and 5"  


map _ []     = []
map f (x:xs) = f x : map f xs
```

In Elixir, the `=` operator is actually a  match operator

`Elixir`
```
iex> x = 1
1
iex> x
1
iex> 1 = x
1
iex> 2 = x
** (MatchError) no match of right hand side value: 1
```

### Recursion
Some language like Haskell, Elixir, ... don't use loop, they use recursion with performance in mind, no overflow.

`Haskell`
```
length' :: (Num b) => [a] -> b  
length' [] = 0  
length' (_:xs) = 1 + length' xs  
```

### Laziness
Some languages support infinite collection, thanks to their laziness.

Haskell is lazy, if you map something over a list several times and filter it several times, it will only pass over the list once

`Haskell`
```
largestDivisible :: (Integral a) => a  
largestDivisible = head (filter p [100000,99999..])  
    where p x = x `mod` 3829 == 0
```

Elixir defines the concept of `Eager` with `Enum` and `Lazy` with `Stream`

`Elixir`
```
1..100_000 |> Enum.map(&(&1 * 3)) |> Enum.filter(odd?) |> Enum.sum
```

### Custom operator
Elixir is famous for its `pipe |>` operator
> The |> symbol used in the snippet above is the pipe operator: it simply takes the output from the expression on its left side and passes it as the first argument to the function call on its right side

`Elixir`
```
1..100_000 |> Enum.map(&(&1 * 3)) |> Enum.filter(odd?) |> Enum.sum
```

Haskell often takes advantage of this custom `-:` operator
`Haskell`
```
x -: f = f x  

(0,0) -: landLeft 1 -: landRight 1 -: landLeft 2
```

### Functor, Applicative Functor, Monoid, Monad
I really like enjoy Haskell because of these typeclasses. It realizes common pattern (map, apply, join, bind, ...) with comptutational context. It really enlightens me when I  find that `function` is a Monad as well (you should read the `Reader monad`)

`Haskell`
```
instance Monad Maybe where  
    return x = Just x  
    Nothing >>= f = Nothing  
    Just x >>= f  = f x  
    fail _ = Nothing  

landLeft :: Birds -> Pole -> Maybe Pole  
landLeft n (left,right)  
    | abs ((left + n) - right) < 4 = Just (left + n, right)  
    | otherwise                    = Nothing  

landRight :: Birds -> Pole -> Maybe Pole  
landRight n (left,right)  
    | abs (left - (right + n)) < 4 = Just (left, right + n)  
    | otherwise                    = Nothing

ghci> return (0,0) >>= landLeft 1 >>= banana >>= landRight 1  
Nothing
```

List comprehension in Haskell is just syntactic sugar for using lis as Monad
`Haskell`
```
ghci> [ (n,ch) | n <- [1,2], ch <- ['a','b'] ]  
[(1,'a'),(1,'b'),(2,'a'),(2,'b')]  
```

`Swift`
```
enum Result<T> {
    case Value(T)
    case Error(NSError)
}

extension Result {
    func map<U>(f: T -> U) -> Result<U> {
        switch self {
            case let .Value(value):
                return Result<U>.Value(f(value))
            case let .Error(error):
                return Result<U>.Error(error)
        }
    }
}

extension Result {
    static func flatten<T>(result: Result<Result<T>>) -> Result<T> {
        switch result {
            case let .Value(innerResult):
                return innerResult
            case let .Error(error):
                return Result<T>.Error(error)
        }
    }
}

extension Result {
    func flatMap<U>(f: T -> Result<U>) -> Result<U> {
        return Result.flatten(map(f))
    }
}
```

### Trait and mixin
Languages like Scala, ... support `trait`

> Similar to interfaces in Java, traits are used to define object types by specifying the signature of the supported methods. Unlike Java, Scala allows traits to be partially implemented; i.e. it is possible to define default implementations for some methods

`Scala`
```
trait Similarity {
  def isSimilar(x: Any): Boolean
  def isNotSimilar(x: Any): Boolean = !isSimilar(x)
}

class Point(xc: Int, yc: Int) extends Similarity {
  var x: Int = xc
  var y: Int = yc
  def isSimilar(obj: Any) =
    obj.isInstanceOf[Point] &&
    obj.asInstanceOf[Point].x == x
}
```

Swift can uses `Protocol Extension` to [achieve trait](http://matthijshollemans.com/2015/07/22/mixins-and-traits-in-swift-2/)

`Swift`
```
protocol GunTrait {
    func shoot() -> String {
        return "Shoot"
    }
}

protocol RenderTrait {
    func render() -> String {
        return "Render"
    }
}

struct Player: GameObject, AITrait, GunTrait, RenderTrait, HealthTrait {

}
```

Ruby supports `Mixin` via `Module`

`Ruby`
```
module Greetings
  def hello
    puts "Hello!"
  end

  def bonjour
    puts "Bonjour!"
  end

  def hola
    puts "Hola!"
  end
end

class User
  include Greetings
end
```

### Many more things
There are many more interesting things as well

- Swift initialization rule make it explicit when using initializer
- Go goroutine and channel for concurrent code
- Elixir process for easy concurrent and message communication. You'll be amazed by how process encpsulates state
- Haskell data type encourages immutability and thread safe code
- Elixir macro for great extension of the language
- Elixir protocol vs behaviour. Scala trait. Haskell typeclass
- Swift generic. Haskell type constraint
- Elixir sigil
- C# async and await
- Javascript generator
- C# lambda

## Reference
* [Programming with Style](http://www.riedquat.de/prog/style)
* [Swift pattern matching in detail](http://appventure.me/2015/08/20/swift-pattern-matching-in-detail/)
