---
author: onmyway133
comments: true
date: 2013-09-24 11:38:19+00:00
layout: post
slug: make-your-own-sliding-menu-on-android-tutorial-part-1
title: Make your own sliding menu on Android tutorial - Part 1
wordpress_id: 549
categories:
- Android
tags:
- android
- drawer
- facebook
- menu
- navigation
- sliding
---

I can't deny that Facebook is so amazing, they made trends and people want to follow. That is the case of the sliding menu.

Searching many threads on SO, like these [create android Sliding Menu like Facebook,gmail](http://stackoverflow.com/questions/16735398/create-android-sliding-menu-like-facebook-gmail), [Android sliding menu similar to the one on facebook](http://stackoverflow.com/questions/16983794/android-sliding-menu-similar-to-the-one-on-facebook), [Android - Sliding menu with sub menu](http://stackoverflow.com/questions/15264567/android-sliding-menu-with-sub-menu) ... they will mostly introduce you to some good sliding menu libraries, like this [SlidingMenu](https://github.com/jfeinstein10/SlidingMenu) and some [tutorials](http://www.michenux.net/android-sliding-menu-part-1-657.html) on how to use it. And of course, not forget to mention the [Navigation Drawer](http://developer.android.com/design/patterns/navigation-drawer.html) that shipped with the Android SDK

If you are a do-it-yourself guy like me, then here is the tutorial for you. It is mostly based on the following Youtube videos and guides
<!-- more -->
1. [Creating a Slide-In Menu in Android - Part 1: Showing the menu](http://www.youtube.com/watch?v=YeR7McJIltk)

2. [Creating a Slide-In Menu in Adnroid - Part 2: Adding animation](http://www.youtube.com/watch?v=UYPmRhiUMOI)

3. [The Making of Prixing #1: Fly-in App Menu](http://cyrilmottier.com/2012/05/22/the-making-of-prixing-fly-in-app-menu-part-1/)

4. [The Making of Prixing #2: Swiping the Fly-in App Menu](http://cyrilmottier.com/2012/05/29/the-making-of-prixing-2-swiping-the-fly-in-app-menu/)

5. [The Making of Prixing #3: Polishing the Sliding App Menu](http://cyrilmottier.com/2012/06/08/the-making-of-prixing-3-polishing-the-sliding-app-menu/)

WHAT WILL WE ACHIEVE
--

The final result of this tutorial will look like this.


![sliding_menu_showcase](http://www.fantageek.com/images/2013/09/sliding_menu_showcase.png)]


User will then be able to get the menu by touching the Menu button, or just by dragging.

The menu contains a list of Android UI controls and when selected, the main content view will shows the according screen.

The source code is available on [Github](https://github.com/onmyway133/SlidingMenu)

THE IDEA
--

A sliding menu app typically has 2 views : the menu view (the green rectangle) and the content view (the red rectangle)

![sliding-menu-idea](http://www.fantageek.com/images/2013/09/sliding-menu-idea.png)

We have many ways to implement the sliding effect, all depend on your intention

1. Let the menu view hold still, while moving the content view. The content view usually sits on top of the menu view and cover the entire screen

2. Opposite of 1. Let the content view hold still, while moving the  menu view. The menu usually sits on top of the content view and occupy some amount of space, so that user can touch and drag it

3. The same as 2.  but when the menu is moving, the content is moving, too.

When the menu is shown, we should disable interacting with the content view.

The 1st solution is what we will do in this tutorial. It's much the same with Facebook sliding menu

OVERVIEW OF THE PROJECT
--

For better understanding, you should take a look at the project to see how things are organized

**/src/com.entropy.slidingmenu2**

MainActivity.java : this should be your first screen. It contains the MainLayout and handle ListView click event to display the right content

**/src/com.entropy.slidingmenu2.fragment**

FragmentMain.java : the default content view

FragmentButton.java : show one button on the content view

The content view is really simple since it's just for demo. I use Fragment so we can easily change the content view according to the ListView item selected

**/src/com.entropy.slidingmenu2.layout**

MainLayout.java : the most important part of this tutorial. For ease and reusability, it's best to create a custom LinearLayout. This will be used to position the menu and content views appropriately and handle sliding effect

**/res/layout**

activity.main: represent the visual structure for MainLayout

fragment_main: represent the content view

THE PREPARATION
--

I list here the required knowledge just for reference

1. [Fragment](http://developer.android.com/training/basics/fragments/index.html) : used to show the content view

2. [Support library ](http://developer.android.com/tools/support-library/index.html): we use v4 in this tutorial to support Fragment for older devices

3. [Custom layout](http://developer.android.com/training/custom-views/index.html) : our MainLayout is a custom layout

4. [Scroller ](http://developer.android.com/reference/android/widget/Scroller.html): to facilitate the sliding animation

5. [Gesture](http://developer.android.com/training/gestures/detector.html) : to allow drag and drop

Without further ado, let's get started

DESIGNING activity_main.xml
--

The activity_main.xml represents the content of our custom layout. Since its a custom layout, we must declare like this

```java
<com.entropy.slidingmenu2.layout.MainLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >
```

Inside it are the 2 LinearLayout. Both are declared with attributes but the final display will be decided by the code :)

```xml
android:layout_width="match_parent"
android:layout_height="match_parent"
```

The first is for the menu, which holds a ListView

The second is for the content, which holds the simulated action bar ^^ and the FrameLayout. The FrameLayout is where fragments will show up

WORKING WITH MainActivity.java
--

The MainActivity is the default activity, it contains our MainLayout and handle menu item selection

1. In the onCreate() method, we do the following

Inflate the MainLayout and its as the contentView for our activity

```java
mainLayout = (MainLayout)this.getLayoutInflater().inflate(R.layout.activity_main, null);
setContentView(mainLayout);
```

Init the ListView for the menu

```java
lvMenuItems = getResources().getStringArray(R.array.menu_items);
        lvMenu = (ListView) findViewById(R.id.activity_main_menu_listview);
        lvMenu.setAdapter(new ArrayAdapter(this,
                android.R.layout.simple_list_item_1, lvMenuItems));
        lvMenu.setOnItemClickListener(new OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
                onMenuItemClick(parent, view, position, id);
            }

        });
```

Init the menu button

```java
btMenu = (Button) findViewById(R.id.activity_main_content_button_menu);
        btMenu.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View v) {
                // Show/hide the menu
                toggleMenu(v);
            }
        });
```

Display FragmentMain as the initial Fragment for our content view

```java
FragmentManager fm = MainActivity.this.getSupportFragmentManager();
FragmentTransaction ft = fm.beginTransaction();

FragmentMain fragment = new FragmentMain();
ft.add(R.id.activity_main_content_fragment, fragment);
ft.commit();
```

2. The toggleMenu() is a utility method to toggle the menu, it will calls MainLayout 's toggleMenu() method. We 'll talk about this later

3. onMenuItemClick() is used to handle menu item selection. What it does is to create the according Fragment and replace it with the current one

4. Handle the Back button. If the menu is shown, we should hide it, or we can quit the app

```java
@Override
    public void onBackPressed() {
        if (mainLayout.isMenuShown()) {
            mainLayout.toggleMenu();
        }
        else {
            super.onBackPressed();
        }
    }
```

Let's continue to [part 2](http://www.fantageek.com/blog/2013/09/25/make-your-own-sliding-menu-on-android-tutorial-part-2/)
