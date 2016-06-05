---
title: Property configuration in Swift
date: 2016-06-05 18:21:26
tags:
---

This post is like a sum up of sum ways to configure your property

##### Using a helper method

```swift
var label: UILabel!

override func viewDidLoad() {
  super.viewDidLoad()

  configureLabel()
}

func configureLabel() {
  label = UILabel()
  label.backgroundColor = UIColor.greenColor()
  view.addSubview(label)
}
```

##### Using anonymous function

```swift
lazy var label: UILabel = { [weak self] in
  let label = UILabel()
  label.backgroundColor = UIColor.greenColor()
  return label
}()
```

Ah, by the way, did you know that

- You shouldn't call access `label` in ViewController `deinit`, because it is `lazy` and we have `weak self`
- `lazy` increases your compile time

##### @noescape configure Block on init

I first saw it on https://github.com/AliSoftware/Dip/blob/develop/Sources/Dip.swift#L61

```swift
public init(@noescape configBlock: (DependencyContainer->()) = { _ in }) {
    configBlock(self)
}
```

##### configure Block as extension

This https://github.com/devxoul/Then makes it easier to configure your property as an extension to NSObject

```swift
extension Then where Self: AnyObject {

    public func then(@noescape block: Self -> Void) -> Self {
        block(self)
        return self
    }
}
```

so we have

```swift
lazy var label = UILabel().then {
    $0.backgroundColor = UIColor.greenColor()
}
```

oops, we can't use [weak self] here

##### init without extension

I try to avoid extension, after reading this http://nshipster.com/new-years-2016/

```swift
public func Init<Type>(value : Type, @noescape block: (object: Type) -> Void) -> Type
{
    block(object: value)
    return value
}
```

we can use it like

```swift
lazy var label = Init(UILabel()) {
    $0.backgroundColor = UIColor.greenColor()
}
```

again, we can't use `weak self`

##### anonymous function again

This https://gist.github.com/erica/4fa60524d9b71bfa9819 makes configuration easier

```swift
let label: UILabel = {
  $0.backgroundColor = UIColor.greenColor()
  return $0
}(UILabel())
```

again, we can't use `weak self`
