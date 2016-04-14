---
author: onmyway133
comments: true
date: 2014-06-01 06:02:24+00:00
layout: post
slug: understanding-registerdefaults
title: Understanding registerDefaults
wordpress_id: 1009
categories:
- iOS
---

Here is the [answer I made on SO about registerDefaults](http://stackoverflow.com/a/19837093/1418457)

[code language="objc"]
[[NSUserDefaults standardUserDefaults] setObject:@"Entropy" forKey:@"kName"]
[/code]
will save "Entropy" to a file named com.example.Demo.plist in Library/Preference folder (where com.example.Demo is your Bundle ID, see IOS Application Security Part 20 – Local Data Storage )

<!-- more -->



[code language="objc"]
[[NSUserDefaults standardUserDefaults] setObject:@"Mac OS X" forKey:@"kOS"];
NSDictionary *appDefaults = [NSDictionary dictionaryWithObjectsAndKeys:
                                @"Windows", @"kOS",
                                @"Google", @"kSearchEngine", nil];
[[NSUserDefaults standardUserDefaults] registerDefaults:appDefaults];

NSLog(@"%@", [[NSUserDefaults standardUserDefaults] objectForKey:@"kOS"]);
NSLog(@"%@", [[NSUserDefaults standardUserDefaults] objectForKey:@"kSearchEngine"]);
NSLog(@"%@", [[NSUserDefaults standardUserDefaults] objectForKey:@"kBrowser"]);
[/code]
will print "Mac OS X", "Google", (null)

In fact, registerDefaults

does not save to disk
only sets value for keys that haven't been set ("kOS" is set by setObject:forKey: and "kSearchEngine" is not set)
returns 0 for scalar values, nil for objects if that key is not set by both registerDefaults and setObject:forKey: ("kBrowser" in this case)




#### The usage of registerDefaults



Quoted from [Preferences and Settings Programming Guide](https://developer.apple.com/library/ios/documentation/cocoa/Conceptual/UserDefaults/AccessingPreferenceValues/AccessingPreferenceValues.html) 





<blockquote>If these standard default values are not appropriate for your app, you can register your own default values using the registerDefaults: method. This method places your custom default values in the NSRegistrationDomain domain, which causes them to be returned when a preference is not explicitly set.</blockquote>





Quoted from [How to Save Data with NSUserDefaults ](http://ganbarugames.com/2011/02/how-to-save-data-with-nsuserdefaults/)





<blockquote>Another tip is that you can initialize your NSUserDefaults with a pre-defined NSDictionary object. So for example you could set a default value to be “false” or “true” before the user ever had a chance to interact with your program. In my case, sometimes I create an array that represents all the levels in my game, and in each array value I store a boolean to check if a player has finished the level. To do this I create the data object and then register it with NSUserDefaults. If a previous value exists for the object, then nothing happens. Otherwise my blank object gets saved as the “default” defaults</blockquote>



