---
layout: post
author: onmyway133
title: "App backed by website in iOS 9"
date: 2016-01-10 11:00:39 +0700
comments: true
categories:
---

iOS 9 introduces new ways for your app to work better, backed by your websites

Smart App Banners
--

- [Promoting Apps with Smart App Banners](https://developer.apple.com/library/mac/documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html)

> If the app is already installed on a user's device, the banner intelligently changes its action, and tapping the banner will simply open the app. If the user doesn’t have your app on his device, tapping on the banner will take him to the app’s entry in the App Store

> To add a Smart App Banner to your website, include the following meta tag in the head of each page where you’d like the banner to appear:

```
<meta name="apple-itunes-app" content="app-id=myAppStoreID, affiliate-data=myAffiliateData, app-argument=myURL">
```

Universal Links
--

- [Support Universal Links](https://developer.apple.com/library/ios/documentation/General/Conceptual/AppSearch/UniversalLinks.html)

> When you support universal links, iOS 9 users can tap a link to your website and get seamlessly redirected to your installed app without going through Safari. If your app isn’t installed, tapping a link to your website opens your website in Safari.

Web Markup
--

- [Mark Up Web Content](https://developer.apple.com/library/ios/documentation/General/Conceptual/AppSearch/WebContent.html#//apple_ref/doc/uid/TP40016308-CH8-SW1)

> If some or all of your app’s content is also available on your website, you can use web markup to give users access to your content in search results. Using web markup lets the Applebot web crawler index your content in Apple’s server-side index, which makes it available to all iOS users in Spotlight and Safari search results.

Shared Web Credentials
--

- [Shared Web Credentials Reference](https://developer.apple.com/library/ios/documentation/Security/Reference/SharedWebCredentialsRef/)

> Shared web credentials is a programming interface that enables native iOS apps to share credentials with their website counterparts. For example, a user may log in to a website in Safari, entering a user name and password, and save those credentials using the iCloud Keychain. Later, the user may run a native app from the same developer, and instead of the app requiring the user to reenter a user name and password, shared web credentials gives it access to the credentials that were entered earlier in Safari.

Reference
--

- [WWDC 2015 Session 509 Seamless Linking to Your App](https://developer.apple.com/videos/play/wwdc2015-509/)
- [Integrating your Site and your iOS App](https://www.youtube.com/watch?v=JDAZKDAUpqY)
