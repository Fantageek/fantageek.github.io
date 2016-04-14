---
author: onmyway133
comments: true
date: 2015-06-18 16:34:53+00:00
layout: post
slug: understanding-mantle-value-transformer
title: Understanding Mantle - Value Transformer
wordpress_id: 1699
categories:
- iOS
---

Mantle is my number 1 choice for the model class. It has a lot of predefined value transformers, you can take a look at NSValueTransformer+MTLInversionAdditions and NSValueTransformer+MTLPredefinedTransformerAdditions.



### Predefined value transformer



Currently Mantle 1.5 supports these value transformers




mtl_invertedTransformer
mtl_JSONDictionaryTransformerWithModelClass
mtl_JSONArrayTransformerWithModelClass
mtl_valueMappingTransformerWithDictionary
URLValueTransformer
booleanValueTransformer


The transformer without parameters (like URLValueTransformer and booleanValueTransformer are registered with NSValueTransformer in the "load" method

[code language="objc"]
+ (void)load {
	@autoreleasepool {
		MTLValueTransformer *URLValueTransformer = [MTLValueTransformer
			reversibleTransformerWithForwardBlock:^ id (NSString *str) {
				if (![str isKindOfClass:NSString.class]) return nil;
				return [NSURL URLWithString:str];
			}
			reverseBlock:^ id (NSURL *URL) {
				if (![URL isKindOfClass:NSURL.class]) return nil;
				return URL.absoluteString;
			}];
		
		[NSValueTransformer setValueTransformer:URLValueTransformer forName:MTLURLValueTransformerName];

		MTLValueTransformer *booleanValueTransformer = [MTLValueTransformer
			reversibleTransformerWithBlock:^ id (NSNumber *boolean) {
				if (![boolean isKindOfClass:NSNumber.class]) return nil;
				return (NSNumber *)(boolean.boolValue ? kCFBooleanTrue : kCFBooleanFalse);
			}];

		[NSValueTransformer setValueTransformer:booleanValueTransformer forName:MTLBooleanValueTransformerName];
	}
}
[/code]

But eventually, those transformers end up calling reversibleTransformerWithForwardBlock:reverseBlock: and the real converter is the MTLJSONAdapter



### Custom value transformer



If those value transformer do not suit your need. You can learn from what Mantle already does, and build your own value transformer

For example, this is a custom value transformer from my FTGValueTransformer that converts the first object in a JSON array to a model

[code language="objc"]
+ (NSValueTransformer *)ftg_JSONFirstObjectInArrayTransformerWithModelClass:(Class)modelClass {
	NSValueTransformer *dictionaryTransformer = [self mtl_JSONDictionaryTransformerWithModelClass:modelClass];

	return [MTLValueTransformer
            reversibleTransformerWithForwardBlock:^ id (NSArray *dictionaries) {
                if (dictionaries == nil || dictionaries.count == 0) {
                    return nil;
                }

                NSAssert([dictionaries isKindOfClass:NSArray.class], @"Expected a array of dictionaries, got: %@", dictionaries);

                NSDictionary *firstDictionary = [dictionaries firstObject];

                NSAssert([firstDictionary isKindOfClass:NSDictionary.class], @"Expected a dictionary or an NSNull, got: %@", firstDictionary);

                id model = [dictionaryTransformer transformedValue:firstDictionary];

                return model;
            }
            reverseBlock:^ id (id model) {
                if (model == nil) {
                    return nil;
                }

                NSAssert([model isKindOfClass:MTLModel.class], @"Expected an MTLModel or an NSNull, got: %@", model);

                NSDictionary *dict = [dictionaryTransformer reverseTransformedValue:model];

                if (dict) {
                    return @[ dict ];
                }

                return @[];
            }];
}
[/code]



### reversibleTransformerWithForwardBlock:reverseBlock:



The reversibleTransformerWithForwardBlock is called to convert from JSON to model, and reverseBlock is called to convert model back to JSON

Take a look at this example, it is a value transformer that convert NSNumber to NSString

[code language="objc"]
+ (NSValueTransformer *)ftg_stringValueTransformer {
    NSValueTransformer *stringValueTransformer = [MTLValueTransformer
                                                  reversibleTransformerWithBlock:^ id (NSNumber *number) {
                                                      if (![number isKindOfClass:NSNumber.class]) {
                                                          return  nil;
                                                      }
                                                      
                                                      return number.stringValue;
                                                  }];

    return stringValueTransformer;
}
[/code]

In the reversibleTransformerWithBlock, the parameter is of kind NSNumber (in JSON dictionary) and the return value is of kind NSString (the property you declare on your model)



#### transformerWithBlock:



If you just need to convert from JSON to model, you can just use transformerWithBlock, which just hav reverseBlock set to nil



### How to hook into Mantle



If for some reason, you don't want to use those value transformer, you can override initWithDictionary method (remember to call super).

[code language="objc"]
- (instancetype)initWithDictionary:(NSDictionary *)dictionaryValue error:(NSError **)error {
    self = [super initWithDictionary:dictionaryValue error:error];
    if (self == nil) return nil;

    // Store a value that needs to be determined locally upon initialization.
    _retrievedAt = [NSDate date];

    return self;
}
[/code]



#### Reference







  1. [Mantle](https://github.com/Mantle/Mantle)


  2. [Possible to access first object in nested array ?](https://github.com/Mantle/Mantle/issues/350)


  3. [FTGValueTransformer](https://github.com/onmyway133/FTGValueTransformer)


  4. [How to hook into Mantle](http://stackoverflow.com/questions/23237352/how-to-hook-into-mantle)


