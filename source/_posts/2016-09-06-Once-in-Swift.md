---
title: Once in Swift
date: 2016-09-06 21:40:44
tags:
---

There are times we want to run an action just once. It would be nice if we can encapsulate this

## dispatch_once

`dispatch_once` is meant to be used for action that runs once and only once

## Once

We can have

```swift
class Once {

  var already: Bool = false

  func run(@noescape block: () -> Void) {
    guard !already else { return }

    block()
    already = true
  }
}
```

Then we can use it like

```swift
class ViewController: UIViewController {
  let once = Once()

  override func viewDidAppear(animated: Bool) {
    super.viewDidAppear(animated)

    once.run {
      cameraMan.setup()
    }
  }
}

```
