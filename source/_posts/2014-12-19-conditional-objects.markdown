---
author: onmyway133
comments: true
date: 2014-12-19 05:09:24+00:00
layout: post
slug: conditional-objects
title: Conditional Objects
wordpress_id: 1508
categories:
- iOS
---

Excerpted from [Archives and Serializations Programming Guide](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Archiving/Articles/archives.html#//apple_ref/doc/uid/20000946-142208)



<blockquote>
  Another problem presented by object graphs is that it is not always appropriate to archive the entire graph. For example, when you encode an NSView object, the view can have many links to other objects: subviews, superviews, formatters, targets, windows, menus, and so on. If a view encoded all of its references to these objects, the entire application would get pulled in. Some objects are more important than others, though. A view’s subviews always should be archived, but not necessarily its superview. In this case, the superview is considered an extraneous part of the graph; a view can exist without its superview, but not its subviews. A view, however, needs to keep a reference to its superview, if the superview is also being encoded in the archive.
  
  To solve this dilemma, NSCoder introduces the concept of a conditional object. A conditional object is an object that should be encoded only if it is being encoded unconditionally elsewhere in the object graph. A conditional object is encoded by invoking encodeConditionalObject:forKey:. If all requests to encode an object are made with these conditional methods, the object is not encoded and references to it decode to nil. If the object is encoded elsewhere, all the conditional references decode to the single encoded object.
  
  Typically, conditional objects are used to encode weak references to objects.
  
  NSCoder does not implement support for conditional objects; NSCoder’s implementation of encodeConditionalObject:forKey: simply encodes the object by invoking encodeObject:forKey:. It is the responsibility of its concrete subclasses to keep track of conditional objects and to not encode objects unless they are needed.
</blockquote>





## Example



Code sample is from [Mantle](https://github.com/Mantle/Mantle/blob/master/Mantle/MTLModel%2BNSCoding.m)

[code language="objc"]
- (void)encodeWithCoder:(NSCoder *)coder {
	if (coderRequiresSecureCoding(coder)) verifyAllowedClassesByPropertyKey(self.class);

	[coder encodeObject:@(self.class.modelVersion) forKey:MTLModelVersionKey];

	NSDictionary *encodingBehaviors = self.class.encodingBehaviorsByPropertyKey;
	[self.dictionaryValue enumerateKeysAndObjectsUsingBlock:^(NSString *key, id value, BOOL *stop) {
		@try {
			// Skip nil values.
			if ([value isEqual:NSNull.null]) return;
			
			switch ([encodingBehaviors[key] unsignedIntegerValue]) {
					// This will also match a nil behavior.
				case MTLModelEncodingBehaviorExcluded:
					break;
					
				case MTLModelEncodingBehaviorUnconditional:
					[coder encodeObject:value forKey:key];
					break;
					
				case MTLModelEncodingBehaviorConditional:
					[coder encodeConditionalObject:value forKey:key];
					break;
					
				default:
					NSAssert(NO, @"Unrecognized encoding behavior %@ on class %@ for key \"%@\"", self.class, encodingBehaviors[key], key);
			}
		} @catch (NSException *ex) {
			NSLog(@"*** Caught exception encoding value for key \"%@\" on class %@: %@", key, self.class, ex);
			@throw ex;
		}
	}];
}
[/code]
