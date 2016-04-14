---
author: onmyway133
comments: true
date: 2014-10-09 16:43:14+00:00
layout: post
slug: objective-c-has-that-named-parameter
title: Objective-C has that named parameter
wordpress_id: 1432
categories:
- iOS
---

Swift supports named parameter. Objective-C in fact has that feature long ago



#### Swift





<blockquote>
  When encapsulated in a class (or struct or enum), the first parameter name of a method is not included externally, while all following parameter names are included externally when the method is called
</blockquote>



[code language="objc"]
class Person {
    func performThing1(thing1 :String, thing2 :String) {
        println("thing1 \(thing1) thing2 \(thing2)")
    }

    func performThing3(thing3 :String, _ thing4 :String) {
        println("thing3 \(thing3) thing4 \(thing4)")
    }

}

let person = Person()
person.performThing1("one", thing2: "two")
person.performThing3("three", "four")
[/code]



#### Objective C





<blockquote>
  Methods can also take parameters, also called arguments. A message with a single argument affixes a colon (:) to the selector name and puts the parameter right after the colon. This construct is called a keyword; a keyword ends with a colon, and a parameter follows the colon. A method that takes multiple parameters has multiple keywords, each followed by a colon.
</blockquote>



[code language="objc"]
@implementation FTGPerson

- (void)performThing1:(NSString *)thing1 thing2:(NSString *)thing2
{
    NSLog(@"thing1 %@ thing 2 %@", thing1, thing2);
}

// Note how the 2nd external parameter name is discarded
- (void)performThing3:(NSString *)thing3 :(NSString *)thing4
{
    NSLog(@"thing3 %@ thing 4 %@", thing3, thing4);
}

@end

FTGPerson *person = [FTGPerson new];
[person performThing1:@"one" thing2:@"two"];
[person performThing3:@"three" :@"four"];

[/code]



#### Reference







  1. [Method names in Objective-C](http://oleb.net/blog/2010/12/method-names-in-objective-c/)


  2. [For Those Who Have Never Used Objective-C](http://www.tomdalling.com/blog/coding-styleconventions/for-those-who-have-never-used-objective-c/)


  3. [The Many Faces of Swift Functions](http://www.objc.io/issue-16/swift-functions.html)


  4. [Why must the last part of an Objective-C method name take an argument (when there is more than one part)?](http://stackoverflow.com/questions/4479967/why-must-the-last-part-of-an-objective-c-method-name-take-an-argument-when-ther)


  5. [Message](https://developer.apple.com/library/mac/documentation/General/Conceptual/DevPedia-CocoaCore/Message.html)


