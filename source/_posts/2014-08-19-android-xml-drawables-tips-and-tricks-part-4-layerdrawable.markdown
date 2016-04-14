---
author: akhoi90
comments: true
date: 2014-08-19 16:14:09+00:00
layout: post
slug: android-xml-drawables-tips-and-tricks-part-4-layerdrawable
title: 'Android XML drawables Tips - Part 4: LayerDrawable'
wordpress_id: 1265
categories:
- Android
---

In this post series, I will share some cases I used Android drawable in more efficient ways. They can help your code looks cleaner, separating Java vs. XML, or can even reduce the complexity of your View hierachy.





Types of Drawable I will be writing about in this series are: LevelList / Bitmap / StateList / Layer / Clip Drawable





<!-- more -->






    
  * [Part 1: LevelListDrawable](http://www.fantageek.com/1155/using-of-android-xml-drawables-part-1-levellistdrawable/)

    
  * [Part 2: BitmapDrawable](http://www.fantageek.com/1183/using-of-android-xml-drawables-part-2-bitmapdrawable/)

    
  * [Part 3: StateListDrawable](http://www.fantageek.com/1215/using-of-android-xml-drawables-part-3-statelistdrawable/)

    
  * [Part 4: LayerDrawable](http://www.fantageek.com/1265/android-xml-drawables-tips-and-tricks-part-4-layerdrawable/)





### Part 4: LayerDrawable



[Developer Docs](http://developer.android.com/reference/android/graphics/drawable/LayerDrawable.html)

[Assets in this post](http://www.fordesigner.com/maps/6575-0.htm)

In this part, I'll show how to make a single curtain background for both side left and right in a screen. This is what it'll look like:

[![layout-2014-08-19-223803](http://www.fantageek.com/wp-content/uploads/2014/08/layout-2014-08-19-223803-300x187.png)](http://www.fantageek.com/wp-content/uploads/2014/08/layout-2014-08-19-223803.png)



Imagine the designer gave me just 2 images for left and right side of the curtain:

[![curtain_left](http://www.fantageek.com/wp-content/uploads/2014/08/curtain_left-109x300.png)](http://www.fantageek.com/wp-content/uploads/2014/08/curtain_left.png) and [![curtain_right](http://www.fantageek.com/wp-content/uploads/2014/08/curtain_right-109x300.png)](http://www.fantageek.com/wp-content/uploads/2014/08/curtain_right.png)

which is put in:

**res/drawable.../curtain_left.png**

**res/drawable.../curtain_right.png**

Now I am writing a single XML just for all our need:

[code language="xml"]

<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
 <item>
   <bitmap android:src="@drawable/curtain_left" android:gravity="left"/>
 </item>
 <item>
   <bitmap android:src="@drawable/curtain_right" android:gravity="right"/>
 </item>
</layer-list>

[/code]

and name it as **res/drawable/layers_curtain.xml
**Set the xml file above as background of the root layout of the screen:

[code language="xml"]

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
 xmlns:tools="http://schemas.android.com/tools"
 android:layout_width="match_parent"
 android:layout_height="match_parent"
 android:background="@drawable/layers_curtains"
...
>

</LinearLayout>

[/code]



### Notes






    
  * You can use tool to make a 9-patch image to archive the same result:

    
  * Will lose aspect ratio of the curtain image resource because of "fill_vertical" attribute



[![nine_curtains.9](http://www.fantageek.com/wp-content/uploads/2014/08/nine_curtains.9-232x300.png)](http://www.fantageek.com/wp-content/uploads/2014/08/nine_curtains.9.png)





[_Download above image_](http://www.fantageek.com/wp-content/uploads/2014/08/nine_curtains.9.png)





### What does this help?






    
  * don't need to add any more View for the background

    
  * cost no effort for re-designing or making 9-patch version of the background.


