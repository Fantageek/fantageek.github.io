---
author: onmyway133
comments: true
date: 2014-12-19 05:15:26+00:00
layout: post
slug: using-parse-sdk-with-mantle
title: Using Parse SDK with Mantle
wordpress_id: 1513
categories:
- iOS
---

I'm using Parse SDK and it works great. We can also subclass Parse for more convenience, but I just don't want to depend much on Parse, so I use Mantle as my model, instead of PFObject.



## Model



Here is my base model object

`FTGBaseObject.h`
[code language="objc"]
@interface FTGBaseObject : MTLModel <MTLJSONSerializing>

@property (nonatomic, copy) NSString *pfObjectID;
@property (nonatomic, strong) NSDate *createdAt;
@property (nonatomic, strong) NSDate *updatedAt;

@end
[/code]

`FTGBaseObject.m`
[code language="objc"]
@implementation FTGBaseObject

+ (NSDictionary *)JSONKeyPathsByPropertyKey {
    return @{
             @"pfObjectID": NSNull.null,
             @"createdAt": NSNull.null,
             @"updatedAt": NSNull.null,
             };
}


#pragma mark - Creation
+ (instancetype)objectWithPFObject:(PFObject *)pfObject
{
    FTGBaseObject *object = [[self alloc] init];
    [object updateWithPFObject:pfObject];
    return object;
}

+ (NSArray *)objectsWithPFObjects:(NSArray *)pfObjects
{
    NSMutableArray *results = [NSMutableArray array];
    for (PFObject *pfObject in pfObjects) {
        FTGBaseObject *object = [[self class] objectWithPFObject:pfObject];
        [results addObject:object];
    }

    return results;
}

- (void)updateWithPFObject:(PFObject *)pfObject
{
    if (pfObject) {
        // TODO: Need to check for pfObject isDataAvailable?
        [self updateWithDictionary:pfObject.ftg_toDictionary];
        [self updateWithCorePFObject:pfObject];
    }
}

- (void)updateWithDictionary:(NSDictionary *)dictionary
{
    if (dictionary.count > 0) {
        FTGBaseObject *updatedObject = [MTLJSONAdapter modelOfClass:[self class]
                                                fromJSONDictionary:dictionary
                                                             error:nil];
        // This is a method to make mergeValuesForKeysFromModel to not update nil values
        [self ftg_mergeValuesForKeysFromModel:updatedObject];
    }
}

- (void)updateWithCorePFObject:(PFObject *)pfObject
{
    self.pfObjectID = pfObject.objectId;
    self.updatedAt = pfObject.updatedAt;
    self.createdAt = pfObject.createdAt;
}


- (NSDictionary *)toJSONDictionary
{
    return [MTLJSONAdapter JSONDictionaryFromModel:self].ftg_dictionaryByRemovingNull;
}

- (PFObject *)pfObject
{
    if (self.pfObjectID) {
        PFObject *pfObject = [PFObject objectWithoutDataWithClassName:self.pfObjectClassName objectId:self.pfObjectID];
        return pfObject;
    }

    return nil;
}

- (PFObject *)toPFObject
{
    PFObject *pfObject = self.pfObject;

    if (!pfObject) {
        pfObject = [PFObject objectWithClassName:self.pfObjectClassName dictionary:nil];
    }

    [pfObject ftg_updateWithDictionary:self.toJSONDictionary];
    return pfObject;
}

+ (NSString *)pfObjectClassName
{
    return nil;
}

- (NSString *)pfObjectClassName
{
    return [[self class] pfObjectClassName];
}

@end
[/code]

`PFObject+FTGAdditions`
[code language="objc"]
- (NSDictionary *)ftg_toDictionary
{
    NSMutableDictionary *result = [NSMutableDictionary dictionary];
    for (NSString *key in self.allKeys) {
        [result setObject:self[key] forKey:key];
    }

    return result;
}

- (void)ftg_updateWithDictionary:(NSDictionary *)dictionary
{
    for (NSString *key in dictionary.allKeys) {
        id value = dictionary[key];
        [self setObject:value forKey:key];
    }
}
[/code]



## Treat PFObject as JSON dictionary



In the FTGBaseObject above, we can see that PFObject is treated as JSON dictionary, just as a JSON dictionary you normally get from RESTful APIs

For example to map from PFUser to our FTGUser object, 
[code language="objc"]

@implementation FTGUser

+ (NSDictionary *)JSONKeyPathsByPropertyKey {
    return @{@"username": @"username",
             @"firstName": @"first_name",
             @"location": @"location",
             };
}

+ (NSString *)pfObjectClassName
{
    // PFUser is stored in Parse as _User
    return @"_User";
}

+ (NSValueTransformer *)locationJSONTransformer {
    return [MTLValueTransformer reversibleTransformerWithForwardBlock:^id(PFObject *pfObject) {
        return [FTGLocation objectWithPFObject:pfObject];
    } reverseBlock:^id(FTGLocation *object) {
        return [object toPFObject];
    }];
}
@end
[/code]

Notice how our FTGUser object properties use camelCase while PFUser columns is stored as dash_case.

[code language="objc"]

PFQuery *query = [PFQuery queryWithClassName:@"_User"];
[query whereKey:@"first_name" equalTo:@"Steven"];
PFObject* pfObject = [query findObjects].firstObject;
FTGUser *user = [FTGUser objectWithPFObject:pfObject];

[/code]



## Treat Parse network API as network layer



You can wrap Parse APIs like `PFQuery`, `saveInBackgroundWithBlock`, ... in the network layer, just like how you use AFNetworking in your network layer.

Notice the FTGBaseObject, it does not hold any reference to the PFObject as we treat PFObject as JSON dictionary, its mainly usage is the dictionary to update our FTGBaseObject.

FTGBaseObject keeps pfObjectID, and uses this pfObjectID to populate an empty PFObject through the `pfObject` method. For example when user updates his first name, we just need

[code language="objc"]

FTGUser *user; // Suppose got this before
PFObject *pfObject = [user pfObject]; // I prefer using dot notation :] like user.pfObject
pfObject[@"first_name"] = @"Steven";
[pfObject saveInBackgroundWithBlock:^(BOOL succeeded, NSError *error) {
   [user updateWithPFObject:pfObject];
}

[/code]

For best practice, we should not call `saveInBackgroundWithBlock` directly like this, move it into its own network layer



## We store FTGBaseObject ourselves



Since we don't keep PFObject around, we don't have to store it, no need for such things as [PFObject+NSCoding](https://github.com/eladb/Parse-NSCoding)

We can archive or save FTGBaseObject to Core Data, Mantle supports this as well.



## Parse Local Datastore



Parse just announces Local Datastore, it is great with just simple `pin` and `unpin` methods. This works well with our solution. Since we have clear separation with PFObject, we don't care if it is from network or the local store, we just get PFObjects out and update our FTGBaseObject. FTGBaseObject is the first class citizen here



## Reference







  1. [Mantle](https://github.com/Mantle/Mantle)


  2. [Abstracting Parse objects?](http://stackoverflow.com/questions/14310099/abstracting-parse-objects)


  3. [Subclass PFObject](https://www.parse.com/questions/subclass-pfobject)


  4. [Parse Local Datastore](http://blog.parse.com/2014/12/09/parse-local-datastore-for-ios/)


