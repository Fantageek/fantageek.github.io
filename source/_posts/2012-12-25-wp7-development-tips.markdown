---
author: onmyway133
comments: true
date: 2012-12-25 01:50:03+00:00
layout: post
slug: wp7-development-tips
title: WP7 development tips
wordpress_id: 158
categories:
- Windows Phone
tags:
- development
- secret
- tips
- wp7
---

Welcome back, today I will start writing all stuff, all the secret I 've revealed when developing for Windows Phone 7.  

<!-- more -->




1. When setting _UIElement_'s _Visibility_ to _Collapsed_, the OS set its _Height_ to 0. So if you need to have a Height of 0, simply set _Height="0"_, which is faster in term of show time.  

2. When clearing data source, _ListBox_ which uses _VirtualizingStackPanel_ does not rememeber its last view, whereas _StackPanel_ does  

3. _ListBox_ which is inside _ScrollViewer_ will lose its UI virtualization, even if you use _VirtualizingStackPanel_  

4. Using _GestureListener_ incorrectly will cause other _UIElement_ to not work, i.e _Slider_, ...  

5. _AdControl_, _ContextMenu_ may be a source of memory leak.  

6. Panorama control still enable swiping when there is only 1 _PanoramaItem_, whereas _Pivot_ does not.  

7. Pivot control will load the current _PivotItem_, as well as its direct left and right _PivotItem_.  

8. Sometimes SIP keyboard does not show up, check your phone for physical keyboard problem.  

9. There is no way to rate a Song for 3rd app  

10. When emulator rendering has overlap issue, the workaround is to set _LayoutRoot_ 's _Background_ to a solid color  

11. Supposed you're using _ObservableCollection_ bindSource as binding source for _ListBox_. When adding items to bindSource quickly, _ListBox_ seems to suppress creating _ListBoxItem_ until all items are added. It is [advised to add items in batch](http://blogs.msdn.com/b/rohantha/archive/2010/09/12/silverlight-wp7-list-scroll-with-items-as-image-description-from-web-bing-image-search.aspx)  

12. _WebBrowser_ control does not have scroll bar  

13. Using _StoryBoard_ when ever possible, because all of its animation is done in Compositor Thread, which leverages GPU  

14. Use _ProgressIndicator_ for best performance and UX familiarity  

15. Collapse unimportant _UIElement_ to decrease load time, and show them when Page is completely loaded.  

16. Always unsubscribe to event source that  can continue to run when the Page is navigated from, because that may cause memory leak. Examples are timer, BackgroundAudioPlayer.PlayeStateChanged, ...  

17. Using file is much faster than Local Database  

18. Remember the limit 260 characters of query string  

19. 




**And more**, Tim Heuer has collected invaluable tips which Jeff Wilcox tweeted for hours. Check out at his blog about [Windows Phone 7 Developer Tips and Tricks](Windows Phone 7 Developer Tips and Tricks)
