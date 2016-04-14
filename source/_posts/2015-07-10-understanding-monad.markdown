---
author: onmyway133
comments: true
date: 2015-07-10 07:39:10+00:00
layout: post
slug: understanding-monad
title: Understanding Monad
wordpress_id: 1737
categories:
- iOS
---

I love functional programming. As always, a search will lead you into [What is (functional) reactive programming?](http://stackoverflow.com/questions/1028250/what-is-functional-reactive-programming), which will strike you back. If you dont fully understand that, no problem. People can answer that because they have played with functional paradigm for years, and they can `feel` it.

So why not just pick a functional language and play with it, then after that come back to that question later, you will be amazed by how much you can understand the answers there.

To me, a function language must ensure

- First class function: which treat function as normal value that can be passed to another function and as a return value
- No side effect. Remember `y = f(x)` which just receives input `x` and return output `y`. No contact with the outside world. Search for `proceduce vs function` and you will know why

## It is not hard
To me, it is `not hard`, it is just `challenging` and `fun`

Yeah, functor, applicative functor and monad are fun

## Learning
My favorite language for learning functional paradigm is Javascript, Swift, Haskell and Elixir. Why they may not fully support functional attributes or encourage true functional behaviour, it is OK with me to learn the concepts.

I must admit that knowing several programming languages benefits you a long way. While there are many resources to learn functional programming and particularly, monad. I find these very useful. Learn in this order

- [mostly-adequate-guide](https://github.com/MostlyAdequate/mostly-adequate-guide) This is by far the great book ever. It teaches me how to understand map, apply, join, chain easily. Javascript is very easy to read, so read this book first
- [Learn You a Haskell for Great Good!
](http://learnyouahaskell.com/chapters) Yeah, people can't just ignore Haskell when they talk about functional programming. This book has very nice examples
- [Functional Programming in Swift](https://www.objc.io/books/fpinswift/) I like Swift and I really like how functional programming work in Swift
- [Functors, Applicatives, And Monads In Pictures](http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html) Through these illustrated pictures, we 'll understand better

Visit [SwiftyMonad](https://github.com/onmyway133/SwiftyMonad) for my experiment in Swift

### Computational Context

### Map with Functor

### Apply with Applicative Functor

### Bind with Monad

## Reference
  * [Functor and Monad in Swift](http://www.javiersoto.me/post/106875422394)
  * [Functors, Applicatives, And Monads In Pictures](http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html)
  * [Back to the Futures](https://realm.io/news/swift-summit-javier-soto-futures/)
  * [The Monad Among Us](https://realm.io/news/swift-summit-al-skipp-monads/)
  * [Compelling Code](https://realm.io/news/compelling-code/)
  * [Functional Reactive Programming without Black Magic](https://www.youtube.com/watch?v=AcDaWe3S75c&list=PLdr22uU_wISpW6XI1J0S7Lp-X8Km-HaQW&index=15)
  * [Monads are not Monsters](https://www.youtube.com/watch?v=vg7cOF30Svo&index=4&list=PLdr22uU_wISpW6XI1J0S7Lp-X8Km-HaQW)
  * [Railway oriented programming](http://fsharpforfunandprofit.com/posts/recipe-part2/)
  * [You Could Have Invented Monads! (And Maybe You Already Have.)](http://blog.sigfpe.com/2006/08/you-could-have-invented-monads-and.html)
  * [mostly-adequate-guide](http://drboolean.gitbooks.io/mostly-adequate-guide/content/ch9.html)
  * [Functioning as a Functionalist](https://www.youtube.com/watch?v=rJosPrqBqrA)


