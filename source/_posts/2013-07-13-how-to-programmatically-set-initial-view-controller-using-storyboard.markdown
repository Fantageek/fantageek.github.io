---
author: onmyway133
comments: true
date: 2013-07-13 09:04:13+00:00
layout: post
slug: how-to-programmatically-set-initial-view-controller-using-storyboard
title: How to programmatically set initial view controller using Storyboard
wordpress_id: 481
categories:
- iOS
tags:
- app
- initial
- ios
- main view controller
- navigation
- storyboard
- view controller
---

If for some reasons, you don't want to use the Automatic setting initial view control feature of Storyboard, here is the solution

Step 1: Make sure that view controller (the view controller you want to make it initial) has StoryboardID
[![StoryboardID](http://www.fantageek.com/wp-content/uploads/2013/07/Screen-Shot-2013-07-13-at-3.58.21-PM.png)](http://www.fantageek.com/wp-content/uploads/2013/07/Screen-Shot-2013-07-13-at-3.58.21-PM.png)

Step 2: Disable that view controller 's Is Initial View Controller
[![Initial view controller option](http://www.fantageek.com/wp-content/uploads/2013/07/Screen-Shot-2013-07-13-at-4.03.22-PM.png)](http://www.fantageek.com/wp-content/uploads/2013/07/Screen-Shot-2013-07-13-at-4.03.22-PM.png)

Step 3: Remove Main storyboard file base name value in app Target Info
[![Main storyboard file base name](http://www.fantageek.com/wp-content/uploads/2013/07/Screen-Shot-2013-07-13-at-3.59.12-PM.png)](http://www.fantageek.com/wp-content/uploads/2013/07/Screen-Shot-2013-07-13-at-3.59.12-PM.png)

Step 4: Some code in AppDelegate.m

[code language="objc"]
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"MainStoryboard_iPhone"
                                                             bundle: nil];

    MainViewController *mainViewController = (MainViewController*)[mainStoryboard
                                                       instantiateViewControllerWithIdentifier: @"SBMainViewController"];

    UINavigationController *navigationController = [[UINavigationController alloc] initWithRootViewController:mainViewController];

    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    [self.window setRootViewController:navigationController];
    [self.window setBackgroundColor:[UIColor whiteColor]];
    [self.window makeKeyAndVisible];

    return YES;
}
[/code]
