---
layout: post
author: onmyway133
title: "iOS Stretchy Header with Auto Layout"
date: 2015-11-02 00:11:17 +0700
comments: true
categories:
---

Stretchy header is cool. People are familiar with changing frames to achieve this, like [Design Teardown: Stretchy Headers](http://blog.matthewcheok.com/design-teardown-stretchy-headers/). But with Auto Layout, we can achieve this with much nicer declarative constraints

The demo project is [StretchyHeader](https://github.com/onmyway133/StretchyHeader)

![](https://github.com/onmyway133/StretchyHeader/raw/master/Assets/Demo.gif)

I use [SnapKit](https://github.com/SnapKit/SnapKit) to make it clear what constraints we need

scrollView
--
The `scrollView` should pin its 4 edges to the `ViewController 's view`
```swift
func setupScrollView() {
        scrollView = UIScrollView()
        scrollView.delegate = self

        view.addSubview(scrollView)
        scrollView.snp_makeConstraints { make in
            make.edges.equalTo(view)
        }
    }
```

scrollViewContentView
--
The `scrollViewContentView` must pin its 4 edges to the `scrollView` to help determine `scrollView` `contentSize`

The height of `scrollViewContentView` is determined by its subviews. The subviews inside must pin their `top` and `bottom` to the `scrollViewContentView`

```swift
func setupScrollViewContentView() {
        scrollViewContentView = UIView()

        scrollView.addSubview(scrollViewContentView)
        scrollViewContentView.snp_makeConstraints { make in
            make.edges.equalTo(scrollView)
            make.width.equalTo(view.snp_width)
        }
    }
```

header
--
The `header` must pin its top to the `scrollView` parent, which is the `ViewController 's view`

Read the `title` section, you 'll see that in order to make `header` stretchy, it must be pinned top and bottom

But if we scroll up, there will be a constraint conflict between these pinned `top` and `bottom` constraints

So we must declare `headerTopConstraint` priority as 999, and `headerLessThanTopConstraint`

```swift
func setupHeader() {
        header = UIImageView()
        header.image = UIImage(named: "onepiece")!

        scrollViewContentView.addSubview(header)
        header.snp_makeConstraints { make in
            // Pin header to scrollView 's parent, which is now ViewController 's view
            // When header is moved up, headerTopConstraint is not enough, so make its priority 999, and add another less than or equal constraint
            make.leading.trailing.equalTo(scrollViewContentView)
            self.headerTopConstraint =  make.top.equalTo(view.snp_top).priority(999).constraint
            self.headerLessThanTopConstraint = make.top.lessThanOrEqualTo(view.snp_top).constraint
        }
    }
```

title
--
The `title` must pin its top to the `scrollViewContentView` to help determine `scrollViewContentView` height

The `title` must also pin its top the `header` bottom in order to make `header` stretchy

```swift
func setupTitleLabel() {
        titleLabel = UILabel()
        titleLabel.numberOfLines = 0
        titleLabel.font = UIFont.preferredFontForTextStyle(UIFontTextStyleTitle1)
        titleLabel.text = "One Piece"

        scrollViewContentView.addSubview(titleLabel)
        titleLabel.snp_makeConstraints { make in
            make.leading.equalTo(scrollViewContentView).offset(20)
            make.trailing.equalTo(scrollViewContentView).offset(-20)
            // Pin to the header to make it stretchy
            make.top.equalTo(header.snp_bottom).offset(20)
            // Pin to the content view to help determine scrollView contentSize
            make.top.equalTo(scrollViewContentView.snp_top).offset(headerHeight)
        }
    }
```

scrollViewDidScroll
--
The header is always pinned to the top, unless you adjust it, here in `scrollViewDidScroll`

Here I use `Constraint`, which is a class from `SnapKit`, but the idea is to change the `constant` of the `NSLayoutConstraint`

```swift
func scrollViewDidScroll(scrollView: UIScrollView) {
        guard let headerTopConstraint = headerTopConstraint,
            headerLessThanTopConstraint = headerLessThanTopConstraint
            else {
                return
        }

        let y = scrollView.contentOffset.y
        let offset = y > 0 ? -y : 0

        headerLessThanTopConstraint.updateOffset(offset)
        headerTopConstraint.updateOffset(offset)
    }
```

By the way, did you just learn the story of One Piece :]

Reference
--
- [Creating parallax effect on UIScrollView using constraints](http://blog.domesticcat.com.au/ios/2014/03/19/creating-parallax-effect-on-uiscrollview-using-simple-constraints/)
- [ScrollviewParallax](https://github.com/BillCarsonFr/ScrollviewParallax)
