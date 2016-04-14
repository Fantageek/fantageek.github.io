---
author: onmyway133
comments: true
date: 2014-06-27 15:18:00+00:00
layout: post
slug: understanding-weak-self-and-strong-self
title: Understanding weak self and strong self
wordpress_id: 1090
categories:
- iOS
tags:
- block
- capture
- retain cycle
- self
- strong
- weak
---

Blocks are wonderful. To avoid retain cycle you often see the weakSelf - strongSelf dance like this

```objc
__weak __typeof__(self) weakSelf = self;
    self.block = ^{
        __typeof__(self) strongSelf = weakSelf;
        [strongSelf doSomething];
        [strongSelf doSomethingElse];
    };
```

I bet you have read many articles out there about this and have some understanding. But here I want give some more note
<!-- more -->



#### When block is created





##### Block is object when it is copied



Blocks are created on the stack and will go away when their stack frame returns. While on the stack, a block has no effect on the storage or lifetime of anything it accesses.

If blocks need to exist after the stack frame returns, they can be copied to the heap and this action is an explicit operation. This way, a block will gain reference-counting as all objects in Cocoa. When they are copied, they take their captured scope with them, retaining any objects they refer



##### Blocks can capture values from the enclosing scope



As well as containing executable code, a block also has the ability to capture state from its enclosing scope. Note that block captures the variable along with its decorators (i.e. weak qualifier),

=> This explains why you need to declare self as __weak



#### When block is executed



When block is executed, it is possible for weakSelf to be non-nil for the first method (doSomething), but not for the second (doSomethingElse)

You may think, at first, this is a trick to use self inside the block avoiding the retain cycle warning. This is not the case. The strong reference to self is created at block execution time while using self in the block is evaluated at block declaration time, thus retaining the object.

For best practice, however, you should create a strong reference of your object using the weak one. This won't create a retain cycle either as the strong pointer within the block will only exist until the block completes (it's only scope is the block itself).

=> This explains why you need to declare another __strong self



### More explanation



This is the answer to reader Nikita





  1. As many people point out, "Blocks are created on the stack and will go away when their stack frame returns. While on the stack, a block has no effect on the storage or lifetime of anything it accesses."
Even if block (declared on the stack) increase reference count to all the object it accesses, this would be useless, because this block will be discard when function returns



  2. When block are copied (You see that people usually declare property (copy) for block), it will increase reference count to all the objects it accesses.






Why? because block are mean to be executed at a later time, so it need to keep strong reference to all the object it access. Block can be executed MANY TIMES, so IT WON'T RELEASE self AFTER this ran.

When you nil out the block, it will be dealloc, hence it will decrease the reference count to all the objects it access.

AFNetworking nil out the block after it is called, so you don't have to use weakself inside block http://www.fantageek.com/1376/afnetworking-gotcha-2/





  1. So there are cases when you don't have to use weakself inside block
a. Make sure the block is not copied, you simply declare and run it
b. Make sure the block is nil out after it is called





#### Reference







  1. [I finally figured out weakSelf and strongSelf](http://dhoerl.wordpress.com/2013/04/23/i-finally-figured-out-weakself-and-strongself/)


  2. [Working with Blocks](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithBlocks/WorkingwithBlocks.html)


  3. [Objective-C Blocks Caveat](http://albertodebortoli.github.io/blog/2013/08/03/objective-c-blocks-caveat/)


  4. [Objective-C Blocks Under the Hood](http://albertodebortoli.github.io/blog/2013/04/21/objective-c-blocks-under-the-hood/)


  5. [A look inside blocks: Episode 1](http://www.galloway.me.uk/2012/10/a-look-inside-blocks-episode-1/)


  6. [A look inside blocks: Episode 2](http://www.galloway.me.uk/2012/10/a-look-inside-blocks-episode-2/)


  7. [A look inside blocks: Episode 3](http://www.galloway.me.uk/2013/05/a-look-inside-blocks-episode-3-block-copy/)


  8. [From C Declarators to Objective-C Blocks Syntax](http://nilsou.com/blog/2013/08/21/objective-c-blocks-syntax/)


  9. [The Syntax of Objective-C Blocks](http://arigrant.com/blog/2014/1/18/the-syntax-of-objective-c-blocks)


  10. [Referring to weak self inside a nested block](http://stackoverflow.com/a/17105368/1418457)


  11. [Capturing My(self)](http://blackpixel.com/blog/2014/03/capturing-myself.html)
