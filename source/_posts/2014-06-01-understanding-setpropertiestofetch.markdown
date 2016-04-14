---
author: onmyway133
comments: true
date: 2014-06-01 05:58:59+00:00
layout: post
slug: understanding-setpropertiestofetch
title: Understanding setPropertiesToFetch
wordpress_id: 1007
categories:
- iOS
---

Here is the [answer I made on SO about setPropertiesToFetch](http://stackoverflow.com/a/19850662/1418457)

The correct way of using setPropertiesToFetch

[code language="objc"]
NSEntityDescription *entity = [NSEntityDescription entityForName:@"Person" inManagedObjectContext:context];
[fetchRequest setEntity:entity];
[fetchRequest setResultType:NSDictionaryResultType]; // Remember to setResultType
[fetchRequest setPropertiesToFetch:
[NSArray arrayWithObjects:@"name", @"age", nil]]; 
NSArray *results = [self.managedObjectContext executeFetchRequest:fetchRequest
                                                               error:nil];

NSArray *nameArray = [results valueForKey:@"name"];
NSArray *ageArray = [results valueForKey:@"age"];
[/code]

results is not an array of Person objects, but an array of Dictionary. You can access the Dictionaries inside like this

[code language="objc"]
NSLog(@"%@", [results[0] valueForKey:@"name"]);
NSLog(@"%@", [results[0] valueForKey:@"age"]);
[/code]

If you only want to work with Model object (which CoreData fetches all properties/attribues of the entity), you can design your Model with Person and PersonDetail (which holds detail information about a person). This way

You can perform fetchRequest and get an array of Person objects
When accessing aPerson.detail (detail is a one-to-one relation with PersonDetail), CoreData will perform faulting for you

