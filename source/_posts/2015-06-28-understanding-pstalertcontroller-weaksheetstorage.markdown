---
author: onmyway133
comments: true
date: 2015-06-28 15:43:50+00:00
layout: post
slug: understanding-pstalertcontroller-weaksheetstorage
title: Understanding PSTAlertController - weakSheetStorage
wordpress_id: 1729
categories:
- iOS
---

For those who don't use [PSTAlertController](https://github.com/steipete/PSTAlertController)



<blockquote>
  API similar to UIAlertController, backwards compatible to iOS 7. Will use the new shiny API when you run iOS 8.
</blockquote>



The thing I find interesting about it is its dance for `weakSheetStorage` and `strongSheetStorage`
Read [Understand strongSheetStorage vs weakSheetStorage](https://github.com/steipete/PSTAlertController/issues/3)



<blockquote>
  It's for exact object lifecycle management. The controller isn't retained but needs to stick around, and once show is called we bind the lifetime to the views presented, so everything is cleaned up at the correct time without worrying about manually retaining things.
</blockquote>





## Init



When PSTAlertController is initialized, it hold a strong reference to UIAlertView and UIActionSheet
[code language="objc"]
if (preferredStyle == PSTAlertControllerStyleActionSheet) {
                _strongSheetStorage = [[UIActionSheet alloc] initWithTitle:title delegate:self cancelButtonTitle:nil destructiveButtonTitle:nil otherButtonTitles:nil];
            } else {
                _strongSheetStorage = [[UIAlertView alloc] initWithTitle:title message:message delegate:self cancelButtonTitle:nil otherButtonTitles:nil];
            }
[/code]



## Show



PSTAlertController may not be kept by any object, but the system keeps UIAlertView and UIActionSheet, so when UIAlertView and UIActionSheet is shown, we attach PSTAlertController to these
[code language="objc"]
- (void)moveSheetToWeakStorage {
    NSParameterAssert(self.strongSheetStorage);

    objc_setAssociatedObject(self.strongSheetStorage, _cmd, self, OBJC_ASSOCIATION_RETAIN_NONATOMIC); // bind lifetime
    self.weakSheetStorage = self.strongSheetStorage;
    self.strongSheetStorage = nil;
}
[/code]
