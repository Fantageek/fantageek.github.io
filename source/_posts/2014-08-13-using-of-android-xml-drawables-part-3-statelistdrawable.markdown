---
author: akhoi90
comments: true
date: 2014-08-13 16:36:11+00:00
layout: post
slug: using-of-android-xml-drawables-part-3-statelistdrawable
title: 'Android XML drawables Tips – Part 3: StateListDrawable'
wordpress_id: 1215
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




### Part 3: StateListDrawable


[Developer Docs](developer.android.com/reference/android/graphics/drawable/StateListDrawable.html)

This post will demonstrate the using of StateListDrawable (selector) to switch between checked/unchecked state of items in a ListView. Along with that, BitmapDrawable is also used like the way we already used in [Part 2](http://www.fantageek.com/1183/using-of-android-xml-drawables-part-2-bitmapdrawable/) to make the background looks better with less effort.

Now let's get it started with this screenshot, this's what we want to get done:

![](http://i281.photobucket.com/albums/kk238/akhoi90/device-2014-08-12-234305_zpsb5f2ec59.png)

Above is a ListView with 5 items, there're 2 items in checked (activated) state.

**Step 1: Resources**

For each item background, we have a pair of images for checked and unchecked states:

[![stawberry_unchecked](http://www.fantageek.com/wp-content/uploads/2014/08/stawberry_unchecked.png)](http://www.fantageek.com/wp-content/uploads/2014/08/stawberry_unchecked.png)[![stawberry_checked](http://www.fantageek.com/wp-content/uploads/2014/08/stawberry_checked.png)](http://www.fantageek.com/wp-content/uploads/2014/08/stawberry_checked.png)









put them in appropriate drawables folder:

**res/drawable-.../strawberry_checked.png**

**res/drawable-.../strawberry_unchecked.png**

...

**Step 2: Drawables**

Create StateListDrawable in XML for each type of those fruit backgrounds. Below is the one for the coconut:

[code language="xml"]
<selector xmlns:android="http://schemas.android.com/apk/res/android">
 <item android:state_activated="true" android:drawable="@drawable/coconut_checked"/>
 <item android:state_activated="false" android:drawable="@drawable/coconut_unchecked"/>
</selector>
[/code]

Notice that the activated state is equivalent to the state if the item is checked. (checked <=> activated="true" / unchecked <=> activated="false")

and place it in **/res/drawable/selector_coconut.xml**
Now we have created 5 selector XML drawables.

**Step 3: Layout**

Let's move on the layout of our Activity, we have only ListView in the screen, and this's it:

[code language="java"]

<ListView
android:id="@+id/listview"
android:layout_width="match_parent"
android:layout_height="match_parent"
android:choiceMode="multipleChoice"/>

[/code]

**Step 4: The  Adapter**

We want items, we must have an adapter for our ListView. This demo doesn't require complex items so I write a simple Adapter, you should re-write it in your better own ways:

[code language="java"]

class FruitAdapter extends ArrayAdapter<Integer> {
  public FruitAdapter(Context context) {
    super(context, 0, new ArrayList<Integer>());
    addAll(R.drawable.selector_coconut, R.drawable.selector_lychee, R.drawable.selector_papaya,
    R.drawable.selector_pineapple, R.drawable.selector_strawberry);
  }

  @Override
  public View getView(int position, View convertView, ViewGroup parent) {
    convertView = new View(getContext());
    convertView.setBackgroundResource(getItem(position));

    return convertView;
  }
}

[/code]

Remember to set this as adapter of our ListView in Activity code:

[code language="java"]
ListView listview = (ListView) findViewById(R.id.listview);
listview.setAdapter(new FruitAdapter(this));
[/code]

Now this is how we got so far:
[![device-2014-08-13-230922](http://www.fantageek.com/wp-content/uploads/2014/08/device-2014-08-13-230922-168x300.png)](http://www.fantageek.com/wp-content/uploads/2014/08/device-2014-08-13-230922.png)**Step 5: Recall BitmapDrawable**

As you remember in [Part 2](http://www.fantageek.com/1183/using-of-android-xml-drawables-part-2-bitmapdrawable/), we use bitmap with its gravity attributes: bottom and fill_horizontal. Now to make the backgrounds look just right, bitmap is useful again. Change all of our selector XML resource to like this (the lychee one):

[code language="xml"]

<selector xmlns:android="http://schemas.android.com/apk/res/android">
 <item android:state_activated="true">
 <bitmap android:src="@drawable/lychee_checked" android:gravity="center"/>
 </item>
 <item android:state_activated="false">
 <bitmap android:src="@drawable/lychee_unchecked" android:gravity="center"/>

</item>
</selector>

[/code]

Run the project, and:

![](http://i281.photobucket.com/albums/kk238/akhoi90/device-2014-08-12-234305_zpsb5f2ec59.png)


### Notes


This demo's showing a multiple choice ListView, you can try change its choiceMode="singleChoice" in XML layout.


### What does this help?






  * Take advange of embeding bitmap inside a selector item to get the background displayed right.


  * A proper way to deal with high-lighting checked ListView items, and unchecked items as well.


  * You should try embed more drawable tag inside a selector item (shape, layer-list, color, ...). It'll be so much fun!
