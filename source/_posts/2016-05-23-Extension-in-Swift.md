---
title: Extension in Swift
date: 2016-05-23 21:04:25
tags:
---

Swift allows us to define more methods on existing class using extension.

```swift
extension UIView {

  func shake() {

  }

  func fade() {

  }

  func centerIn(anotherView: UIView) {

  }
}
```

If you 're afraid of the naming conflict, you can prefix your methods. Or a better way, reverse it :dancer: , like

```swift
view.animation.shake()
view.animation.fade()
view.layout.centerIn(anotherView)
```

This way, no more conflict and we make it clear that `shake()` and `fade()` belongs to `animation` category

Actually, `animation` and `layout` are properties in `UIView` extension. This may cause naming conflict, but the number of them is reduced

This is how it works

```swift
extension UIView {

  struct Animation {
    let view: UIView

    func shake() {
      // Shake the view
    }

    func fade() {
      PowerfulAnimationEngine.fade(view)
    }
  }

  var animation: Animation {
    return Animation(view: self)
  }

  struct Layout {
    let view: UIView

    func centerIn(anotherView: UIView) {

    }
  }

  var layout: Layout {
    return Layout(view: self)
  }
}
```
