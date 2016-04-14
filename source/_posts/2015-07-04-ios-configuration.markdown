---
author: onmyway133
comments: true
date: 2015-07-04 04:19:32+00:00
layout: post
slug: ios-configuration
title: iOS configuration
wordpress_id: 1732
categories:
- iOS
---

This is about creating multiple targets from a project.



### Source file



Source files (.m, .plist, ...) must be checked into appropriate target



### Info.plist



Each target should have its own Info.plist. Info.plist should not be checked into the target. Otherwise, you will get a warning



<blockquote>
  The Copy Bundle Resources build phase contains this target's Info.plist file
</blockquote>



Instead, it must be specified in the target 's `Build Setting -> Packaging -> Info.plist file`. It can be absolute or relative to the project file



### Entitlement



Entitlement should not be checked into the target

Instead, it must be specified in the target 's `Build Setting -> Code Signing -> Code Signing Entitlement`. Ut can be absolute or relative to the project file



### Asset Catalog



Asset Catalog must be checked into the appropriate target. The fix for multiple target with multiple Asset Catalog together with Cocoapods is [Multiple targets and Asset Catalog with Cocoapods](https://github.com/2359media/ios-dev-discussions/issues/7)



### Read Build Setting Reference



[Build Setting Reference](https://developer.apple.com/library/mac/documentation/DeveloperTools/Reference/XcodeBuildSettingRef/1-Build_Setting_Reference/build_setting_ref.html) has some predefined macros that can be useful.



### Preprocessor Macro



Under target `Build Setting -> Apple LLVM Preprocessing -> Preprocessor Macros`, we can define our own macro, which can be useful in code.

For example, in Distro A target, I can set macro DISTRO_A to both Debug and Release Configuration.



### Configuration



Multiple targets can have different configuration (server path, Facebook ID, ...). This can be achieved by several ways



#### ifdef



[code lang=text]
#ifdef DISTRO_A
NSString *serverPath = @"http://distro-a.com";
#else
NSString *serverPath = @"http://distro-b.com";
#endif
[/code]



#### 1 config.h file and multiple config.m file for each target



`Config.h`

[code lang=text]
extern NSString *serverPath;
[/code]

`Config.m` (Distro A target)

[code lang=text]
NSString *serverPath = @"http://distro-a.com";
[/code]

`Config.m` (Distro B target)

[code lang=text]
NSString *serverPath = @"http://distro-b.com";
[/code]



#### Protocol and Abstract Factory



We can define a protocol
`Config.h`
[code language="objc"]
@protocol Config <NSObject>

- (NSString *)serverPath;

@end
[/code]

`DistroAConfig.h`
[code language="objc"]
@interface DistroAConfig : NSObject <Config>

@end
[/code]

`DistroAConfig.m`
[code language="objc"]
@implementation DistroAConfig

- (NSString *)serverPath {
    return @"http://distro-a.com";
}

@end
[/code]



### Theme



Each target can have different themes (different color, font, text size, spacing, ...). We can use protocol together with Abstract Factory pattern. Each target has a ThemeFactory that can produces HomeTheme, SettingTheme, AboutTheme, FavoriteTheme, ... This way you only need to use `ifdef` once

`Theme.h`
[code language="objc"]
id<ThemeFactory> themeFactory();
[/code]

`Theme.m`
[code language="objc"]
id<ThemeFactory> themeFactory() {
#ifdef DISTRO_A
    return [DistroAHomeThemeFactory new];
#else
    return [DistroBThemeFactory new];
#endif
}
[/code]

`ThemeFactory.h`
[code language="objc"]
@protocol ThemeFactory <NSObject>

- (id<HomeTheme>)home;

@end
[/code]

`HomeTheme.h`
[code language="objc"]
@protocol HomeTheme <NSObject>

- (UIColor *)titleColor;
- (UIFont *)titleFont;

@end
[/code]

`DistroAThemeFactory.h`
[code language="objc"]
@interface DistroAThemeFactory : NSObject <ThemeFactory>
@end
[/code]

`DistroAThemeFactory.m`
[code language="objc"]
@implementation DistroAThemeFactory

- (id<HomeTheme>)home {
    return [DistroAHomeTheme new];
}

@end
[/code]

`DistroAHomeTheme.h`
[code language="objc"]
@interface DistroAHomeTheme : NSObject <HomeTheme>

@end
[/code]

`DistroAHomeTheme.m`
[code language="objc"]
@implementation DistroAHomeTheme

- (UIColor *)titleColor {
    return [UIColor redColor];
}

- (UIFont *)titleFont {
    return [UIFont boldSystemFontOfSize:15];
}

@end
[/code]

`HomeViewController.m`
[code language="objc"]
#import "Theme.h"

self.label.textColor = themeFactory().home.titleColor;
[/code]



## Reference







  * [Create Multiple Targets/Apps for 1 Xcode Project](http://samwize.com/2014/05/22/create-multiple-targets-slash-apps-for-1-xcode-project/)


  * [YAML-based Configuration for ObjC Projects](http://lowlevelbits.org/yaml-based-configuration-for-objc-projects/)


  * [Info.plist preprocessing](http://ilya.puchka.me/info-plist-preprocessing/)


