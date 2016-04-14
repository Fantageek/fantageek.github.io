---
layout: post
author: onmyway133
title: "Symbolic breakpoints in Xcode"
date: 2016-01-15 12:23:17 +0700
comments: true
categories:
---

Symbolic breakpoints are breakpoints based on symbols like functions or methods.

In Swift
--

You can set symbolic breakpoints on any free functions, instance and class methods, whether in your own classes or not.

Here is an example

```swift
func freeFunction() {
    print("freeFunction")
}

class Robot {
    init() {
        print("init")
    }

    func instanceMethod() {
        print("instanceMethod")
    }

    func instanceMethod(noise: Int) {
        print("instanceMethod")
    }

    func instanceMethodWithArguments(a: Int, b: Int) -> Int {
        print("instanceMethodWithArguments")
        return a + b
    }

    func instanceMethodWithArguments(a: Int, b: Int, c: Int) -> Int {
        print("instanceMethodWithArguments")
        return a + b + c
    }

    class func classMethod() {
        print("classMethod")
    }

    struct MyStruct {
        static func staticMethod() {
            print("staticMethod")
        }
    }
}

struct MyStruct {
    static func staticMethod() {
        print("staticMethod")
    }
}
```

You can have some breakpoints in Swift like

```
UIViewController.viewDidLoad
freeFunction
Robot.instanceMethod
instanceMethodWithArguments
Robot.classMethod
Robot.init
Robot.MyStruct.staticMethod
```

- There seems no difference between instance and class methods !!
- `Robot.MyStruct.staticMethod` says that it must match `staticMethod` inside Robot.MyStruct
- With `instanceMethodWithArguments`, Xcode Breakpoint panel shows options for `(Robot)(Swift.Int)(b: Swift.Int) -> Swift.Int` and `(Robot)(Swift.Int)(b: Swift.Int)(c: Swift.Int) -> Swift.Int`. This may remind you of [Instance Methods are Curried Functions in Swift](http://oleb.net/blog/2014/07/swift-instance-methods-curried-functions/)
- I thought that symbolic breakpoint must match `Selector`, like `instanceMethodWithArguments:b:c`, but it is not
- Objective-C style `-[UIViewController viewDidLoad]` or `-[Robot instanceMethod]` does not work !!

And talking about Swift selector, you may want to take a look at this proposal [Referencing the Objective-C selector of a method](https://github.com/apple/swift-evolution/blob/master/proposals/0022-objc-selectors.md)

In Objective-C
--

```objc
void freeFunction() {
    NSLog(@"freeFunction");
}


@interface Robot : NSObject

@property (nonatomic, copy) NSString *name;

- (void)instanceMethod;
- (void)instanceMethod:(NSInteger)a b:(NSInteger)b;
+ (void)classMethod;

@end

@implementation Robot

- (instancetype)initWithName:(NSString *)name {
    self = [super init];
    self.name = name;

    return self;
}

- (void)instanceMethod {
    NSLog(@"instanceMethod");
}

- (void)instanceMethod:(NSInteger)a b:(NSInteger)b {
    NSLog(@"instanceMethod:b:");
}

+ (void)classMethod {
    NSLog(@"classMethod");
}

@end
```

You can have some breakpoints in Objective-C like

```
-[UIViewController viewDidLoad]
freeFunction
-[Robot initWithName:]
-[Robot instanceMethod]
-[Robot instanceMethod:b:]
+[Robot classMethod]
```

- We must specify the correct method with arguments like `-[Robot instanceMethod:b:]`
- I use another Swift class into this Objective-C project

```swift
@objc public class Animal: NSObject {
    @objc func hello() {
        print("hello")
    }
}
```

but the symbolic breakpoint `-[Animal hello]` does not work. Only `Animal.hello` works !!

Syntax
--

- There are Swift syntax `Animal.hello` and Objective-C syntax `-[Animal hello]`. It seems that Swift syntax must be used on Swift files. So in Swift project, we should use Swift syntax
- `init` is instance method, see [How to swizzle init in Swift](http://stackoverflow.com/questions/34317766/how-to-swizzle-init-in-swift)

System symbols
--

Symbolic breakpoints are helpful in case you want to inspect system behaviours

### Auto Layout
- [Autolayout Breakpoints](http://nshint.io/blog/2015/08/17/autolayout-breakpoints/)

### Breakpoints_v2.xcbkptlist
- [Breakpoints_v2.xcbkptlist](https://gist.github.com/Ashton-W/5c1ede17f8cec1f8b529) A list of common symbolic breakpoints and actions

### swift_willThrow
- [Breakpoint on all throws](http://www.robertvojta.com/2015/07/23/breakpoint-on-all-throws/)

### objc_exception_throw
It is your `Exception Breakpoint` with `Break` set to `Throw`

There's a `Break` type `Catch` also

### Swizzle

Symbolic breakpoints help when you want to understand system call, like when you try to make Xcode plugin

See [How To Create an Xcode Plugin: Part 1/3](http://www.raywenderlich.com/94020/creating-an-xcode-plugin-part-1) on how to break in `initWithIcon:message:parentWindow:duration`

### Assembly
Break on system classes and you want to understand the parameters there, so an understanding of assembly helps

- [How to get parameters using symbolic breakpoints in Objective-C](http://stackoverflow.com/questions/15513045/how-to-get-parameters-using-symbolic-breakpoints-in-objective-c)
- [A Whirlwind Detour of Assembly](http://www.raywenderlich.com/94020/creating-an-xcode-plugin-part-1)
- [Disassembling the Assembly, Part 1](https://mikeash.com/pyblog/friday-qa-2011-12-16-disassembling-the-assembly-part-1.html)
- [iOS Assembly Tutorial: Understanding ARM](http://www.raywenderlich.com/37181/ios-assembly-tutorial)

### Interesting

Some interesting questions

- [How to automatically set breakpoints on all methods in XCode?](http://stackoverflow.com/questions/9275195/how-to-automatically-set-breakpoints-on-all-methods-in-xcode)

chisel
--

Facebook has [chisel](https://github.com/facebook/chisel), which is a collection of lldb commands in Python

Specifically, `bmessage`

> Set a symbolic breakpoint on the method of a class or the method of an instance without worrying which class in the hierarchy actually implements the method.

Reference
--

- [Xcode Breakpoint Wizardry](https://www.bignerdranch.com/blog/xcode-breakpoint-wizardry/)
- [Dancing in the Debugger — A Waltz with LLDB](https://www.objc.io/issues/19-debugging/lldb-debugging/)
