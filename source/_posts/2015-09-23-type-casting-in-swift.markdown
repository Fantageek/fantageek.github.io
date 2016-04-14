---
layout: post
author: onmyway133
title: "Type casting in Swift 2"
date: 2015-09-23 23:28:21 +0700
comments: true
categories:
---

## as vs as! vs as?
- `as` used for upcasting and type casting to bridged type
- `as?` used for safe casting, return nil if failed
- `as!` used to force casting, crash if failed


- `as!` can't cast raw type to optional
- `as!` and `as?` works by inspect the source type against the destination type (if the source type or destination type is optional, inspect the type inside)
- If succeeded, `as?` returns optional that contains source type casted as destination type, while `as!` return source type casted as destination type

```
let rawString: AnyObject = "Fantageek"
    let optionalString: AnyObject? = "Fantageek"
    let nilString: AnyObject? = (nil as String?)

    let rawInt: AnyObject = Int(3)
    let optionalInt: AnyObject? = Int(3)
    let nilInt: AnyObject? = (nil as Int?)

	   // as
//    let result1 = rawString as String // AnyObject is not convertible to String
//    let result2 = optionalString as String // AnyObject? is not convertible to String
//    let result3 = nilString as String // AnyObject? is not convertible to String
//    let result4 = rawInt as String // AnyObject is not convertible to String
//    let result5 = optionalInt as String // AnyObject? is not convertible to String
//    let result6 = nilInt as String // AnyObject? is not convertible to String

	// as!
    let result7 = rawString as! String // String "Fantageek"
    let result8 = optionalString as! String // String "Fantageek"
//    let result9 = nilString as! String // unexpectedly found nil while unwrapping an Optional value
//    let result10 = rawInt as! String // Could not cast value of type '__NSCFNumber' (0x107c98130) to 'NSString' (0x1082afb00).
//    let result11 = optionalInt as! String // Could not cast value of type '__NSCFNumber' (0x101e95130) to 'NSString' (0x1024acb00).
//    let result12 = nilInt as! String // unexpectedly found nil while unwrapping an Optional value

//    let result13 = rawString as! String! // Cannot downcast from AnyObject to a more optional type String!
//    let result14 = rawString as! String? // Cannot downcast from AnyObject to a more optional type String?
    let result15 = optionalString as! String! // String! "Fantageek"
    let result16 = optionalString as! String? // String? "Fantageek"

    // as?
    let result17 = rawString as? String // String? "Fantageek"
    let result18 = optionalString as? String // String? "Fantageek"
    let result19 = nilString as? String // String? nil
    let result20 = rawInt as? String // String? nil
    let result21 = optionalInt as? String // String? nil
    let result22 = nilInt as? String // String? nil
    // case 22 is interesting, because nilInt is Optiona<Int>.None while result22 is Optional<String>.None

//    let result23 = rawString as? String! // Cannot downcast from AnyObject to a more optional type String!
//    let result24 = rawString as? String? // Cannot downcast from AnyObject to a more optional type String?
    let result25 = optionalString as? String! // String!? "Fantageek"
    let result26 = optionalString as? String? // String?? "Fantageek
    let result27 = optionalInt as? String? // String?? nil




    // Up casting
    let button = UIButton()
    let view = button as UIView



	 // Brigded type casting
    let string = "3"
    let nsString = string as NSString
    let value = nsString.floatValue // 3

```

## Reference
- [Type Casting](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/TypeCasting.html)
- [The as! Operator](https://developer.apple.com/swift/blog/?id=23)
- [Type Casting in Swift](http://www.codingexplorer.com/type-casting-swift/)
