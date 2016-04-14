---
author: onmyway133
comments: true
date: 2014-08-13 16:32:42+00:00
layout: post
slug: make-tutorial-screen-with-uiscrollview-and-autolayout
title: Make tutorial screen with UIScrollView and Autolayout
wordpress_id: 1237
categories:
- iOS
tags:
- autolayout
- contentsize
- contentview
- masonry
- tutorial
- uiscrollview
---

Hi, in this tutorial, I will show you some code that creates a tutorial screen. This is achieved using UIScrollView and Autolayout. I use Masonry as it provides SDL syntax

There are basically 2 approaches to deal with UIScrollView contentSize, and I choose the contentView approach

<!-- more -->



#### My Nib look like this



[![Screen Shot 2014-08-13 at 11.31.25 PM](http://www.fantageek.com/wp-content/uploads/2014/08/Screen-Shot-2014-08-13-at-11.31.25-PM-300x138.png)](http://www.fantageek.com/wp-content/uploads/2014/08/Screen-Shot-2014-08-13-at-11.31.25-PM.png)



#### Code



Place this inside viewDidLoad. Try read the code since its very succinct
[code language="objc"]
    // scrollView
    self.scrollView = [[UIScrollView alloc] init];
    [self.view addSubview:self.scrollView];
    [self.scrollView mas_makeConstraints:^(MASConstraintMaker *make) {
        make.edges.equalTo(self.view);
    }];

    // scrollViewContentView
    self.scrollViewContentView = [[UIView alloc] init];
    [self.scrollView addSubview:self.scrollViewContentView];
    [self.scrollViewContentView mas_makeConstraints:^(MASConstraintMaker *make) {
        make.edges.equalTo(self.scrollView);
    }];
[/code]



#### Note







  1. scrollView has 4 constraints (top, bottom, leading, trailing) to self.view


  2. scrollViewContentView has 4 constraints (top, bottom, leading, trailing) to scrollView. This means that when the scrollViewContentView changes its size, it actually affects scrollView contentSize. There are several ways to make scrollViewContentView to determine its own size
a) scrollViewContentView can make constraints to self.view
[code language="objc"]
[self.scrollViewContentView mas_makeConstraints:^(MASConstraintMaker *make) {
        make.height.equalTo(self.view.mas_height);
        make.width.equalTo(self.view.mas_width).multipliedBy(kPageCount);
}];
[/code]



b) The subviews of scrollViewContentView have enough constraints to determine scrollViewContentView size. For example, subviews has left and right constraints to scrollViewContentView
[code language="objc"]
IntroPageView *previousPage = nil;
    for (NSInteger i=0; i<kPageCount; ++i) {
        IntroPageView *page = [[IntroPageView alloc] init];
        page.titleLabel.text = titles[i];
        page.contentLabel.text = contents[i];
        [self.scrollViewContentView addSubview:page];

        [page mas_makeConstraints:^(MASConstraintMaker *make) {
            make.height.equalTo(self.view.mas_height);
            make.width.equalTo(self.view.mas_width);
            make.top.equalTo(self.scrollViewContentView.mas_top);

            if (i == 0) {
                make.left.equalTo(self.scrollViewContentView.mas_left);
            } else {
                make.left.equalTo(previousPage.mas_right);
            }

            if (i == kPageCount -1) {
                make.right.equalTo(self.scrollViewContentView.mas_right);
            }
        }];

        previousPage = page;
    }
[/code]



#### Reference







  1. [Technical Note TN2154 UIScrollView And Autolayout](https://developer.apple.com/library/ios/technotes/tn2154/_index.html)


  2. [Using UIScrollView with Auto Layout in iOS](http://spin.atomicobject.com/2014/03/05/uiscrollview-autolayout-ios/)


  3. [How to Use UIScrollView With Autolayout](http://samwize.com/2014/03/14/how-to-use-uiscrollview-with-autolayout/)


