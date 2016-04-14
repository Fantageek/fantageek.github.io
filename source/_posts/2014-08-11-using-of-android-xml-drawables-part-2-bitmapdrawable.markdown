---
author: akhoi90
comments: true
date: 2014-08-11 15:58:16+00:00
layout: post
slug: using-of-android-xml-drawables-part-2-bitmapdrawable
title: 'Android XML drawables Tips - Part 2: BitmapDrawable'
wordpress_id: 1183
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





### Part 2: BitmapDrawable



[Developer Docs](http://developer.android.com/reference/android/graphics/drawable/BitmapDrawable.html)

[Assets used in this post](http://www.psawesome.com/tutorials/create-an-i-pad-inspired-bookshelf-using-photoshop)

One thing I want to archive in this blog post is this View:

![](http://i281.photobucket.com/albums/kk238/akhoi90/shelf_bitmap_drawable_zpsfb11e38a.png)

Let imagine that is a row item in a ListView. The question is how to show the horizontal wood bar by using a BitmapDrawable.

**Step 1: **Copy your asset (the horizontal shelf bar) to **res/drawable/shelf.png. **This is how it looks like: (you can notice it's shorter than the result screenshot)



![](http://i281.photobucket.com/albums/kk238/akhoi90/shelf_itself_zps2eb74db3.png)

**Step 2: **Create a BitmapDrawable in XML and put it in **res/drawable/bitmap_shelf.xml. **This resource will be set as background of the ListView item

[code language="xml"]

<bitmap xmlns:android="http://schemas.android.com/apk/res/android"
 android:gravity="bottom|fill_horizontal"
 android:src="@drawable/shelf" />

[/code]

I use 2 types of android:gravity in this bitmap:





  * fill_horizontal: to stretch shelf image by width


  * bottom: make the shelf image to be at bottom of background bitmap



**Step 3: **Design the ListView item layout, set background of this item layout to the bitmap we created above.

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:layout_width="match_parent"
android:layout_height="wrap_content"
android:background="@drawable/bitmap_shelf"
android:orientation="horizontal"
... >

<include layout="@layout/book" />
<include layout="@layout/book" />
<include layout="@layout/book" />
<!-- some more books -->
</LinearLayout>
```


And this is the result so far:
![](http://i281.photobucket.com/albums/kk238/akhoi90/shelf_bottom_wrong_zps5fe0688f.png)
**Step 4: **To make the effect more real, let add a bit magic to our item layout:

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
...
android:paddingBottom="xxdp"
...>
...
</LinearLayout>
```

With xx bottom padding for the parent layout, you lift bottom of books to match with the wood bar so it's like the books are up on the bar. Final result is:
![](http://i281.photobucket.com/albums/kk238/akhoi90/shelf_bitmap_drawable_zpsfb11e38a.png)




### Notes







  * To set a correct bottom padding for good result on multiple screens, remember that we calculate the padding base on the height of our shelf image asset. That height will be different on different screen density, so you may want to define the padding in XML dimensions for multiple dpi values.


  * Will lose aspect ratio of the wood bar image resource because of "fill_horizontal" attribute





### What does this help?







  * This is a way for you to avoid adding one more View holding the wood bar background. In this case we are showing book shelf in a ListView, so there will be posibly n row items. If you can reduce one View for each row, it matters, doesn't it?


  * Even when you set the shelf as background, without BitmapDrawable, you can not force the shelf to be at bottom by using its gravity attribute. Beside "bottom" or "fill_horizontal", there are some more gravity types and you can try for yourself!
