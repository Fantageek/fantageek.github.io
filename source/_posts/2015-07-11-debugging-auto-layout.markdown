---
author: onmyway133
comments: true
date: 2015-07-11 14:05:50+00:00
layout: post
slug: debugging-auto-layout
title: Debugging Auto Layout
wordpress_id: 1747
categories:
- iOS
---

### hasAmbiguousLayout





<blockquote>
  Returns whether the constraints impacting the layout of the view incompletely specify the location of the view.
</blockquote>





### exerciseAmbiguityInLayout





<blockquote>
  This method randomly changes the frame of a view with an ambiguous layout between its different valid values, causing the view to move in the interface. This makes it easy to visually identify what the valid frames are and may enable the developer to discern what constraints need to be added to the layout to fully specify a location for the view.
</blockquote>





### _autolayoutTrace





<blockquote>
  This returns a string describing the whole view tree which tells you when a view has an ambiguous layout.
</blockquote>





### NSLayoutConstraint identifier





<blockquote>
  The name that identifies the constraint.
</blockquote>





### UIViewAlertForUnsatisfiableConstraints



[Autolayout Breakpoints](http://nshint.io/blog/2015/08/17/autolayout-breakpoints/)



## Reference







  * [Advanced Auto Layout Toolbox](http://www.objc.io/issues/3-views/advanced-auto-layout-toolbox/)


  * [Mysteries of Auto Layout, Part 2](http://asciiwwdc.com/2015/sessions/219)


