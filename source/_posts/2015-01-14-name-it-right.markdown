---
author: onmyway133
comments: true
date: 2015-01-14 16:17:03+00:00
layout: post
slug: name-it-right
title: Name it right
wordpress_id: 1539
categories:
- iOS
---

Just as how iOS [shows us how to adopt best coding practices and standards](http://nshipster.com/the-death-of-cocoa/), it shows us how to name things right and consistently as well.

I take examples from UIKIt and Foundation to demonstrate, in no particular order. Also, we should avoid grammar and spelling mistakes.



#### performSelector:onThread:withObject:waitUntilDone:modes:



We can write `performSelector::::` but here correct name parameters help us understand what each parameter requires



#### showAlertViewForTaskWithErrorOnCompletion:delegate



Method which has no return begins with a verb



#### filteredArrayUsingPredicate:



Method which returns value start with a noun, notice how `filtered` is. We can prefix noun with past participle and adjective.



#### imageNamed:



Notice `Named`, not `Name`. This is shorthand of `imageWhichIsNamed`
Read [Reduced relative clauses](http://random-idea-english.blogspot.com/2012/02/reduced-relative-clauses-lesson-and.html)



#### deselectRowAtIndexPath:animated:



Notice `animated`, not `animate`. `animated` is is used to present a state



#### UIView



Object is a noun, in single form



#### superview



Getter does not look like this `getSuperview`



#### isUserInteractionEnabled



Getter for boolean is normally prefixed with `is` when appropriated



#### NSEnumerator



An object should be named so that people understand its main and only job



#### NSApplicationDidBecomeActiveNotification



If it is a notification, it should have `notification` in its name



#### contentsOfDirectoryAtURL:includingPropertiesForKeys:options:error:



Notice `including...`, not `include`



#### respondsToSelector:



Notice `responds`, not `respond`. Objective-C allows us to read code like English sentence, so there must be agreement between subject and verb



#### viewWithTag:



Don't use taggedView, because `viewWithTag` allows us to specify the tag parameter more naturally.



#### runModalForDirectory:file:types:



Notice now we name `file`, not `andFile`. `and` keyword is needless



#### presentationControllerForPresentedViewController:presentingViewController:sourceViewController:



Long method name are more expressive



#### [NSArray array]



Creates and returns an empty array.



#### alloc



This is an abbreviation for `allocate`. Use abbreviations consistently



#### 





## Reference







  1. [Introduction to Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html#//apple_ref/doc/uid/10000146-SW1)


  2. [English Part of Speech](https://www.englishclub.com/grammar/parts-of-speech-table.htm)


  3. [What are the naming conventions of functions that return boolean?](http://stackoverflow.com/questions/13639535/what-are-the-naming-conventions-of-functions-that-return-boolean)


  4. [objective-c-style-guide by Raywenderlich team](https://github.com/raywenderlich/objective-c-style-guide)


  5. [Learn Objective-C: Cocoa Naming Conventions](http://www.binpress.com/tutorial/learn-objectivec-cocoa-naming-conventions/88)


  6. [Objective C method naming convention](http://stackoverflow.com/questions/8410602/objective-c-method-naming-convention)


  7. [How to name things: the hardest problem in programming](http://www.slideshare.net/pirhilton/how-to-name-things-the-hardest-problem-in-programming)


