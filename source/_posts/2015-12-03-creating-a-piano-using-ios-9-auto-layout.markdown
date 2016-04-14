---
layout: post
author: onmyway133
title: "Creating a piano using iOS 9 Auto Layout"
date: 2015-12-03 23:39:32 +0700
comments: true
categories:
---

In the beginning, people use frame and Autoresizing Mask, then they use Auto Layout, then iOS 9 encourages them to use `NSLayoutAnchor`, `UILayoutGuide` and `UIStackView`

NSLayoutAnchor
--

> The NSLayoutAnchor class is a factory class for creating NSLayoutConstraint objects using a fluent API. Use these constraints to programmatically define your layout using Auto Layout.

It has 3 subclasses

`NSLayoutDimension`

- func constraintEqualToConstant(_ c: CGFloat) -> NSLayoutConstraint!

`NSLayoutXAxisAnchor`

- Allows working with horizontal constraints
- Prevent these

```swift
// This constraint generates an incompatible pointer type warning
cancelButton.leadingAnchor.constraintEqualToAnchor(saveButton.topAnchor, constant: 8.0).active = true
```

`NSLayoutYAxisAnchor`

- Allows working with vertical constraints
- Prevent these

```swift
// This constraint generates an incompatible pointer type warning
cancelButton.topAnchor.constraintEqualToAnchor(saveButton.trailingAnchor, constant: 8.0).active = true
```

UILayoutGuide
--
Previously, we used dummy views to aid constraints. Now we use `UILayoutGuide`

### Define an equal spacing between a series of views

![](http://www.fantageek.com/images/UILayoutGuide_Spacing.png)

See full [gist](https://gist.github.com/onmyway133/fcf892aac565d36ee890)

```swift
let space1 = UILayoutGuide()
view.addLayoutGuide(space1)

let space2 = UILayoutGuide()
view.addLayoutGuide(space2)

space1.widthAnchor.constraintEqualToAnchor(space2.widthAnchor).active = true

saveButton.trailingAnchor.constraintEqualToAnchor(space1.leadingAnchor).active = true

cancelButton.leadingAnchor.constraintEqualToAnchor(space1.trailingAnchor).active = true
cancelButton.trailingAnchor.constraintEqualToAnchor(space2.leadingAnchor).active = true

clearButton.leadingAnchor.constraintEqualToAnchor(space2.trailingAnchor).active = true
```

### Layout guides can also act as a black box, containing a number of other views and controls

![](http://www.fantageek.com/images/UILayoutGuide_Container.png)

See the full [gist](https://gist.github.com/onmyway133/faa135b4db601d0db9a8)

```swift
let container = UILayoutGuide()
view.addLayoutGuide(container)

// Set interior constraints
label.lastBaselineAnchor.constraintEqualToAnchor(textField.lastBaselineAnchor).active = true
label.leadingAnchor.constraintEqualToAnchor(container.leadingAnchor).active = true

textField.leadingAnchor.constraintEqualToAnchor(label.trailingAnchor, constant: 8.0).active = true
textField.trailingAnchor.constraintEqualToAnchor(container.trailingAnchor).active = true

textField.topAnchor.constraintEqualToAnchor(container.topAnchor).active = true
textField.bottomAnchor.constraintEqualToAnchor(container.bottomAnchor).active = true

// Set exterior constraints
// The contents of the container can be treated as a black box
let margins = view.layoutMarginsGuide

container.leadingAnchor.constraintEqualToAnchor(margins.leadingAnchor).active = true
container.trailingAnchor.constraintEqualToAnchor(margins.trailingAnchor).active = true

// Must use NSLayoutConstraint with the scene's top and bottom layout guides.
NSLayoutConstraint(item: container,
    attribute: .Top,
    relatedBy: .Equal,
    toItem: topLayoutGuide,
    attribute: .Bottom,
    multiplier: 1.0,
    constant: 20.0).active = true
```

### layoutMarginsGuide
Margins are now represented as `layoutMarginsGuide`, a subclass of `UILayoutGuide`

### topLayoutGuide and bottomLayoutGuide
In the container example, we saw how we must use `NSLayoutConstraint` with the `topLayoutGuide`. `topLayoutGuide` and `bottomLayoutGuide` are object conforming to `UILayoutSupport` protocol

### layoutFrame
> The layout guide defines a rectangular space in its owning view’s coordinate system. This property contains a valid CGRect value by the time its owning view’s layoutSubviews method is called.

In the above container example, the container layout guide frame is

```
(16.0, 40.0, 343.0, 21.0)
```

Piano
--

![](http://www.fantageek.com/images/piano.png)

See [Piano on Github](http://github.com/onmyway133/Piano) on how to create a Piano using `UILayoutGuide`, `NSLayoutAnchor` and `UIStackView`
