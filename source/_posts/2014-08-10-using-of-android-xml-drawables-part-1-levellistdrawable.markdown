---
author: akhoi90
comments: true
date: 2014-08-10 17:12:04+00:00
layout: post
slug: using-of-android-xml-drawables-part-1-levellistdrawable
title: 'Android XML drawables Tips - Part 1: LevelListDrawable'
wordpress_id: 1155
categories:
- Android
---

In this post series, I will share some cases I used Android drawable in more efficient ways. They can help your code looks cleaner, separating Java vs. XML, or even reduce the complexity of your View hierachy.

<!-- more -->

Types of Drawable I will be writing about in this series are: LevelList / Bitmap / StateList / Layer / Clip Drawable




  * [Part 1: LevelListDrawable](http://www.fantageek.com/1155/using-of-android-xml-drawables-part-1-levellistdrawable/)


  * [Part 2: BitmapDrawable](http://www.fantageek.com/1183/using-of-android-xml-drawables-part-2-bitmapdrawable/)


  * [Part 3: StateListDrawable](http://www.fantageek.com/1215/using-of-android-xml-drawables-part-3-statelistdrawable/)


  * [Part 4: LayerDrawable](http://www.fantageek.com/1265/android-xml-drawables-tips-and-tricks-part-4-layerdrawable/)




### **Part 1: LevelListDrawable**


[Developers Docs](http://developer.android.com/reference/android/graphics/drawable/LevelListDrawable.html)

Imagine I am developing a multiple choice question game. After user end a series of questions, I will show an result dialog in which there's one medal image. The medal image has 3 states: bronze / silver / gold but show only one at a time corresponding to how many correct answer that user made. The logic is below:


<blockquote>Show Bronze medal -  complete 1 correct answer

Show Silver medal    -  complete 5 questions correctly

Show Gold medal      -  complete all 10 questions correctly</blockquote>


**Step 1:** I will create a LevelListDrawable in XML and name it in: **res/drawable/levels_result_medal.xml**

```xml
<level-list xmlns:android="http://schemas.android.com/apk/res/android" >
<item android:drawable="@drawable/medal_bronze" android:maxLevel="4"/>
<item android:drawable="@drawable/medal_silver" android:maxLevel="9"/>
<item android:drawable="@drawable/medal_gold" android:maxLevel="10"/>
</level-list>
```

**Step 2:** Set it as image source of the medal ImageView in your layout:

```xml
<ImageView
android:id="..." android:layout_width="..." android:layout_height="..."
android:src="@drawable/levels_result_medal"
/>
```

**Step 3:** My Java code for the switching logic will look like this:

```java
medalImageView.setImageLevel(nUserCorrectAnswered);
// nUserCorrectAnswered is a variable having value range of [0..10].
```



### **Notes**






  * LevelListDrawable has also minLevel attribute for more customization


  * LevelListDrawable does not allow negative for max/min (will make runtime crash if so)


  * You can invoke level change in Drawable instance by:



[code language="java"]
medalImageView.getDrawable().setLevel(nUserCorrectAnswered);
[/code]



  * setImageLevel() / setLevel() allow negative parameter.


  * setLevel() is also used with ClipDrawable


  * For unlucky zero, if you want to set an image for it as well (show a unavailable state image of medal when user have no correct answer), just add one more item in level-list with maxLevel="0"




### What does it help?






  * With this type of Drawable, you might save some lines of code for switching logic (if / switch case ...) or even more if you are implementing a graphics having more states than the medal (battery states, progress bar, ...).


  * This also helps separate Java code and XML code so your class may look cleaner.
