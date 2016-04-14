---
author: onmyway133
comments: true
date: 2013-12-26 14:50:26+00:00
layout: post
slug: why-objective-c-metaprogramming-dramatically-improves-your-ios-app
title: Why Objective-C Metaprogramming Dramatically Improves Your iOS App
wordpress_id: 632
categories:
- iOS
tags:
- ios
- meta
- programming
---

#### Foreword




The original post is from [http://kurtedelbrock.com/objective-c-metaprogramming/](http://kurtedelbrock.com/objective-c-metaprogramming/) but don't know why it isn't there. So I backup it, just as reference for myself, and for those who are interested




  
  

  





For those of you who work with me, you know that I evenly split my time between complaining about how chunky, verbose, and buggy Objective-C can be and evangelizing (better) languages like Haskell, Clojure, and Ruby. Of course, Apple won’t use languages like that on iOS, so I was beginning to accept that building iPhone and iPad apps would never be as fun or as seamless as deploying web apps with Rack or Node.js.




<!-- more -->




That is, until I learned about metaprogramming in Objective-C, which completely changed how I architect and implement applications. The beauty of it is that it allows developers to create templates, domain specific languages and overrides that ultimately improve programmer efficiency and minimize the occurence of bugs. First, though, it might be helpful to look at some of the sticking points in Objective-C to help understand why metaprogramming will solve them.




### Two Reasons Why Objective-C Can Be Painful




#### 1. Objective-C (and Cocoa) Greatly Restrain Programmer Efficiency




This is probably no surprise to someone who’s done any iOS programming, but it takes an ungodly amount of code to do even the most simple tasks in Objective-C, be it trying to print yesterday’s date to the console or implementing a UITableView from an array. It is not uncommon for more than half of the code in a UIController to be related to creating a table view, and all of this view coding in the controller can kill the meaning and usefulness of a model-view-controller architecture pretty quickly.




Furthermore, the infix notation used to send messages to objects makes the language much more difficult for a developer to read and comprehend even though the semantics are almost identical to C++ and Java. Even if you can get around all of the brackets and the colons, the blurring between the method name and first parameter that’s common in defining Objective-C selectors, along with the convention of very long method names, can make the language especially difficult.




Even when you get more comfortable with   the language, it doesn’t help the fact that Objective-C is a very large language. It seems to go directly against the tenets of creating a simple, DSL-style API by having a large variety of similar sounding methods as well as incorporating all of the functionality of C (Objective-C is a superset of C, after all). Thankfully, as Ash Furrow points out in Why Objective-C is Hard to Learn , Apple has been taking steps to add new constructs to Objective-C that make the language simpler, such as blocks, dot-notation for getters and setters, and synthesized properties.




#### 2. Objective-C Often Lacks Abstraction




The pain points above are the general complaints you’ll often hear when people describe why they don’t like Objective-C. However, one of the main reasons why I don’t always like Objective-C, and a reason I don’t hear all that often, is that it’s too low level.




In general, programming languages need to do two things: resource allocation, and sequencing. Resource allocation is things like creating objects, setting variables, pointers and memory, and this is usually abstracted quite well by things like garbage collection and automatic reference counting. However, sequencing is more difficult to abstract because it usually consists of adding more keywords and standard functions to make commands more clear and also to bundle them together in usable units. Functional programming languages solve sequencing abstraction by getting rid of sequencing all together and using, for instance, Haskell’s lazy evaluating. In Objective-C, it seems that the best way to add sequence abstraction is to create less keywords instead of more via tightly refactored domain specific languages, which is something that metaprogramming can improve greatly.




### So What Is Metaprogramming?




Put simply, it’s code that generates code. It comes in a variety of factors, such as code templates, Rails-style generators, adding methods and classes on the fly, and all kinds of other runtime hacking. Objective-C has a strong set of tools you can use to metaprogram, the most useful of which is the Objective-C Runtime tools.




### …And What Are The Benefits?




Metaprogramming speeds up development, reduces bugs, and is a lot of fun. Here’s why:




#### 1. Less Bugs




The templates and reusable code you create in the process of metaprogranning only has to be written once, and therefore only has to go through QA once. Much like object-oriented patterns, metaprogranning reduces the size of the codebase and maximizes reuse.




#### 2. Faster Development




We often do a lot of tedious work and boilerplate coding when we are working on iOS apps, and metaprogramming can help us avoid that by templatizing the patterns and interactions we use most often.




#### 3. More Refactored Code




Refactoring normally focuses on breaking code into smaller and more reusable pieces. Metaprogramming takes it a step further by allowing you to take those pieces and rearrange them as needed, often removing the need for “if-then” heavy methods and non-DRY friendly boilerplate code spread across different objects.




### Awesome Examples of Metaprogramming




Here are some of the things you can do with metaprogramming in Objective-C and iOS to make developing apps much easier and also much more fun. Note that metaprogramming may not be the best way to do this, but it’s really interesting nonetheless. If you want even more examples of ways to speed up iOS development and autogenerate code, you should subscribe to the newsletter .




#### 1. Domain Specific Languages (dynamic method naming)




Domain specific languages (DSLs) are “mini-programming languages” you can use to access functionality with semantic languages. If you’ve ever used ActiveRecord, Cucumber, or RSpec on Ruby, you’ve used a DSL.




Lately, I’ve been working on creating a DSL based on Mongoid used for fetching and accessing objects from Core Data. Here’s a quick demonstration of the language:




Model *objects = [[[Users findByName:@"John"]  

roleNot:@"Administrator"]  

first];  

I use the dynamic method naming functionality of the Objective-C Runtime functions to add a whole slew of methods to the Model class based on the names of the attributes I added in Core Data. While my DSL is a work in progress, here’s some of the language constructs I’ve implemented.




* findBy{field};  

* {field}Not;  

* {field}Gte;  

* {field}Gt;  

* {field}Lte;  

* {field}Lt;  

These are all chainable, so you can do things like:




Model *objects = [[[Users findByName:@"John"] tap:^(id obj){  

[obj count]; // Puts the number of users named John  

}] index:3];  

Tap lets you inject a block into any place in the method chain. This lets you do things like debug pieces of the chain or hold a pointer to intermediate steps without having to assign parts to a variable. I stole it from Ruby, so you can learn more about tap from Tom Mortel’s explanation .




To create the dynamic methods, I overrode the resolveClassMethod: in a subclass. There, I just do some pattern matching with the name of the selector, and add the method to the runtime by attaching the handleFindBy function:




if ([methodName hasPrefix:@"findBy"] && [arrayOfFields valueForKey:fieldName])  

{  

Class selfMetaClass = objc_getMetaClass([NSStringFromClass([self class]) UTF8String]);  

class_addMethod(selfMetaClass, aSEL, (IMP) handleFindBy, "@");  

}




#### 2. Validation




One of the best ways to prevent simple errors and bugs is to validate inputs provided both by the user and by the development team. Core Data provides functionality that can do the validation for us, but that only applies when the input is being saved to a persistent store. What about vanilla properties synthesized on your object and used to hold things like emails or phone numbers? You don’t want to copy-paste a ton of validation code to each of the custom setters. Metaprogramming to the rescue.




You can use metaprogramming to replace a synthesized or overridden setter with a method that calls a validation method instead that will in turn call the original setter if validation passes. I would do this with a wrapper method that looks like this:




[SexyValidator validatesEmailAddressFor:@[ @"emailAddress", @"oldEmailAddress" ]];  

This would ultimately boil down to method swizzling that would look like this:




// This is more pseudo-code than code and meant more as an example than as something that could run. As I finish this, I'll add it to GitHub and share it here.




// Move your original setter to a new method  

class_addMethod(selfMetaClass, @selector(setterForEmail:), (IMP) originalImp, "@");




// And replace it with a new method  

class_replaceMethod(selfMetaClass, @selector(setEmail:), (IMP) validatesEmail, "@");




// And implement it  

void validatesEmail(id self, SEL _cmd, NSString* content) {  

if ([content isEmail])  

{  

[self setterForEmail:content];  

} else {  

// tell the user that it failed validation  

}  

}




#### 3. Templates




A lot of the coding I find myself doing is ridiculously redundant, especially when it comes to setting up views with content based on Core Data. In particular, I often need a table view that lists the results of a query, has built-in search functionality, and loads a view controller that shows a single object whenever a user taps on a specific cell. To stop myself from recreating the wheel, I created templates for the list view and the single view, and use metaprogramming to fill in the classes when needed. Here’s an example of the key functionality:




// Create a subclass called ReportListViewController, where "Report" is the entity name/model you want to display




NSString *className = NSStringFromClass([self class]); // converts ReportListViewController to a string  

NSString *specificClass = [className trimListViewController]; // returns "Report"  

Now, whenever I want my template to fill in the specific class, I can do that with:




NSClassFromString(specificClass);




// For instance, fetching all the records  

[NSClassFromString(specificClass) all];




#### 4. Mocking




Another good use for metaprogramming is mocking properties and method calls during unit testing. Sometimes, you don’t want to fully implement a function in order to get it working in a unit test, so instead, you can use metaprogramming to replace the method with something that returns the value you expect.




For instance, let’s say you wrote a test that relies on a count method, but you haven’t gotten around to writing it yet. Or, even more likely, you don’t want to have to add a bunch of data to the models so that count returns something meaningful. You want count to return 3, even when it should really be 0.




To do that, you can use metaprogramming to replace the count method with return 3; . Basically, mocking teaches your methods how to lie appropriately. To implement this, all you need to is create a block with the code you actually want:




int block = ^{ return 3; }  

Then, convert that block into an IMP, and feed it to class_replaceMethod :




IMP blockAsImp = imp_implementationWithBlock(impyBlock);  

class_replaceMethod(selfMetaClass, @selector(count:), (IMP) blockAsImp, "@");  

Et voilà! Now your objects will lie to you.
