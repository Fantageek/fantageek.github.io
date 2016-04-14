---
author: onmyway133
comments: true
date: 2013-09-24 18:26:47+00:00
layout: post
slug: make-your-own-sliding-menu-on-android-tutorial-part-2
title: Make your own sliding menu on Android tutorial – Part 2
wordpress_id: 562
categories:
- Android
tags:
- android
- facebook
- fly out
- fragment
- framelayout
- menu
- sliding
- tutorial
---

This is the part 2 of the tutorial. If you forget, here is the link to [part 1](http://www.fantageek.com/blog/2013/09/24/make-your-own-sliding-menu-on-android-tutorial-part-1/).

Link to [Github](https://github.com/onmyway133/slidingmenu)

In the first part, we learn about the idea, the structure of the project and how MainActivity uses the MainLayout. Now we learn how to actually implement the MainLayout

DISPLAY MENU AND CONTENT VIEW
--

First we have MainLayout as a subclass of LinearLayout

```java
public class MainLayout extends LinearLayout
```

We then need declare the constructors
<!-- more -->

```java
public MainLayout(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public MainLayout(Context context) {
        super(context);
    }
```

and override some useful methods

```java
@Override
    protected void onAttachedToWindow() {
        super.onAttachedToWindow();

        // Get our 2 child View
        menu = this.getChildAt(0);
        content = this.getChildAt(1);

        // Initially hide the menu
        menu.setVisibility(View.GONE);
    }
```

onAttachedToWindow() is called when MainLayout is attached to window. At this point it has a Surface and will start drawing. Note that this function is guaranteed to be called before onDraw. Here we set child views to our view and content variable

```java
menu = this.getChildAt(0);
content = this.getChildAt(1);
```

and initially hide the menu. Note that View.GONE tells the view to not take up space in the layout

```java
menu.setVisibility(View.GONE);
```

In onMeasure(), we compute menuRightMargin, this variable is the amount of right space the menu should not occupy. In this case, we want the menu to take up 90% amount of the screen width. onMeasure() is called to ask all children to measure themselves and compute the measurement of this layout based on the children

```java
 @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
        menuRightMargin = mainLayoutWidth * 10 / 100;
    }
```

Finally, we need to override onLayout(), this is called from layout when this view should assign a size and position to each of its children. This is where we position the menu and content view.

```java
@Override
    protected void onLayout(boolean changed, int left, int top, int right, int bottom) {
        // True if MainLayout 's size and position has changed
        // If true, calculate child views size
        if(changed) {
            // Note: LayoutParams are used by views to tell their parents how they want to be laid out

            // content View occupies the full height and width
            LayoutParams contentLayoutParams = (LayoutParams)content.getLayoutParams();
            contentLayoutParams.height = this.getHeight();
            contentLayoutParams.width = this.getWidth();

            // menu View occupies the full height, but certain width
            LayoutParams menuLayoutParams = (LayoutParams)menu.getLayoutParams();
            menuLayoutParams.height = this.getHeight();
            menuLayoutParams.width = this.getWidth() - menuRightMargin;
        }

        // Layout the child views
        menu.layout(left, top, right - menuRightMargin, bottom);
        content.layout(left + contentXOffset, top, right + contentXOffset, bottom);

    }
```

Note for the use of the contentXOffset variable.  It is the content that is moving, not the menu. So contentXOffset is used to translate the content horizontally when it is moving

ADDING ANIMATION
--

So the main idea of sliding menu is to change contentXOffset and call offsetLeftAndRight for the content to move the content. But for the content 's new position to survive, we need to actually layout it on onLayout(), as shown in previous code snippet For more information, see [Flyin menu using offsetLeftAndRight not preserving after a layout](http://sentinelweb.co.uk/offsetleftandright-not-preserving-after-a-layout/)

To better control sliding state, we declare MenuState enumeration

```java
private enum MenuState {
        HIDING,
        HIDDEN,
        SHOWING,
        SHOWN,
    };
```

HIDDEN state is when menu is fully hidden, and SHOWN state is when menu is fully shown. HIDDING state is when menu is about to hide, and SHOWING state is when menu is about to show. Initially currentMenuState is set to HIDDEN so that the menu won't show up on first launch.

The main method of our MainLayout is toggleMenu, which, as it name implied, allow us to toggle menu

```java
public void toggleMenu() {
        // Do nothing if sliding is in progress
        if(currentMenuState == MenuState.HIDING || currentMenuState == MenuState.SHOWING)
            return;

        switch(currentMenuState) {
        case HIDDEN:
            currentMenuState = MenuState.SHOWING;
            menu.setVisibility(View.VISIBLE);
            menuScroller.startScroll(0, 0, menu.getLayoutParams().width,
                    0, SLIDING_DURATION);
            break;
        case SHOWN:
            currentMenuState = MenuState.HIDING;
            menuScroller.startScroll(contentXOffset, 0, -contentXOffset,
                    0, SLIDING_DURATION);
            break;
        default:
            break;
        }

        // Begin querying
        menuHandler.postDelayed(menuRunnable, QUERY_INTERVAL);

        // Invalite this whole MainLayout, causing onLayout() to be called
        this.invalidate();
    }
```

Here we use a Scroller to faciliate sliding animation. Note that Scroller does not perform any visual effect, it is just a base for us to track animation by querying the Scroller's methods. Bills has a good answer on SO [Android: Scroller Animation?](http://stackoverflow.com/questions/5495855/android-scroller-animation/6219382#6219382)

The Scroller uses a custom Interpolator to make the sliding more natural. It moves faster in the end. The formula is here

```java
interpolator(t) = (t-1)5 + 1
```

If the menu is in HIDDEN state, we set its visibility to VISIBLE and start scrolling.  Here the content is moving horizontally, so we scroll from left edge to menu width. Note that menu takes up only 90% of the screen width.

If the menu is in SHOWN state, we start scrolling from the content 's current x position to the left edge.

The 3rd parameter to the startScroll() method is the distance we want to scroll, a negative sign indicates that we want to scroll from right to left.

You can tweak SLIDING_DURATION and QUERY_INTERVAL to your desire. SLIDING_DURATION is the duration of the scrolling. QUERY_INTERVAL is how often we perform querying the Scroller for information. I set it to 16ms so that we have an fps of about 60, which is too high :D

Here the querying is achieved via calling adjustContentPosition() in MenuRunnable

```java
// Begin querying
menuHandler.postDelayed(menuRunnable, QUERY_INTERVAL);
```

```java
// Query Scroller
protected class MenuRunnable implements Runnable {
        @Override
        public void run() {
            boolean isScrolling = menuScroller.computeScrollOffset();
            adjustContentPosition(isScrolling);
        }
    }
```

Here we call computeScrollOffset to check if the scrolling is finished or not

```java
private void adjustContentPosition(boolean isScrolling) {
        int scrollerXOffset = menuScroller.getCurrX();

        //Log.d("MainLayout.java adjustContentPosition()", "scrollerOffset " + scrollerOffset);

        // Translate content View accordingly
        content.offsetLeftAndRight(scrollerXOffset - contentXOffset);

        contentXOffset = scrollerXOffset;

        // Invalite this whole MainLayout, causing onLayout() to be called
        this.invalidate();

        // Check if animation is in progress
        if (isScrolling)
            menuHandler.postDelayed(menuRunnable, QUERY_INTERVAL);
        else
            this.onMenuSlidingComplete();
    }
```

We base on getCurrX to update out contentXOffset and translate content view. Remember to call invalidate() everytime the content position is changed. We continue moving the content view until scrolling is finished

Finally, in onMenuSlidingComplete(), we set the currentMenuState accordingly

```java
private void onMenuSlidingComplete() {
        switch (currentMenuState) {
        case SHOWING:
            currentMenuState = MenuState.SHOWN;
            break;
        case HIDING:
            currentMenuState = MenuState.HIDDEN;
            menu.setVisibility(View.GONE);
            break;
        default:
            return;
        }
    }
```

HANDLING GESTURE
--

To support gesture, we first attach OnTouchListener to the content view. We do this in onMeasure()

```java
content.setOnTouchListener(new OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                return MainLayout.this.onContentTouch(v, event);
            }
        });
```

And in onContentTouch() we handle the ACTION_DOWN, ACTION_MOVE and ACTION_UP to allow dragging the content view

Please note that we use getRawX() instead of getX() for consistent behavior. More information see PeyloW 's answer here [How do I know if a MotionEvent is relative or absolute?](http://stackoverflow.com/questions/1410885/how-do-i-know-if-a-motionevent-is-relative-or-absolute)

Here I use curX and diffX to track previous position and how the difference in distance. When user is dragging, we continuously update the content view 's position. Please also prevent user from dragging beyond the left edge and right margin border

When the user release his/her finger, we base on lastDiffX to decide if the menu should show or hide.

```java
public boolean onContentTouch(View v, MotionEvent event) {
        // Do nothing if sliding is in progress
        if(currentMenuState == MenuState.HIDING || currentMenuState == MenuState.SHOWING)
            return false;

        // getRawX returns X touch point corresponding to screen
        // getX sometimes returns screen X, sometimes returns content View X
        int curX = (int)event.getRawX();
        int diffX = 0;

        switch(event.getAction()) {
        case MotionEvent.ACTION_DOWN:
            //Log.d("MainLayout.java onContentTouch()", "Down x " + curX);

            prevX = curX;
            return true;

        case MotionEvent.ACTION_MOVE:
            //Log.d("MainLayout.java onContentTouch()", "Move x " + curX);

            // Set menu to Visible when user start dragging the content View
            if(!isDragging) {
                isDragging = true;
                menu.setVisibility(View.VISIBLE);
            }

            // How far we have moved since the last position
            diffX = curX - prevX;

            // Prevent user from dragging beyond border
            if(contentXOffset + diffX <= 0) {                 // Don't allow dragging beyond left border                 // Use diffX will make content cross the border, so only translate by -contentXOffset                 diffX = -contentXOffset;             } else if(contentXOffset + diffX > mainLayoutWidth - menuRightMargin) {
                // Don't allow dragging beyond menu width
                diffX = mainLayoutWidth - menuRightMargin - contentXOffset;
            }

            // Translate content View accordingly
            content.offsetLeftAndRight(diffX);

            contentXOffset += diffX;

            // Invalite this whole MainLayout, causing onLayout() to be called
            this.invalidate();

            prevX = curX;
            lastDiffX = diffX;
            return true;

        case MotionEvent.ACTION_UP:
            //Log.d("MainLayout.java onContentTouch()", "Up x " + curX);

            Log.d("MainLayout.java onContentTouch()", "Up lastDiffX " + lastDiffX);

            // Start scrolling
            // Remember that when content has a chance to cross left border, lastDiffX is set to 0
            if(lastDiffX > 0) {
                // User wants to show menu
                currentMenuState = MenuState.SHOWING;

                // No need to set to Visible, because we have set to Visible in ACTION_MOVE
                //menu.setVisibility(View.VISIBLE);

                //Log.d("MainLayout.java onContentTouch()", "Up contentXOffset " + contentXOffset);

                // Start scrolling from contentXOffset
                menuScroller.startScroll(contentXOffset, 0, menu.getLayoutParams().width - contentXOffset,
                        0, SLIDING_DURATION);
            } else if(lastDiffX < 0) {
                // User wants to hide menu
                currentMenuState = MenuState.HIDING;
                menuScroller.startScroll(contentXOffset, 0, -contentXOffset,
                        0, SLIDING_DURATION);
            }

            // Begin querying
            menuHandler.postDelayed(menuRunnable, QUERY_INTERVAL);

            // Invalite this whole MainLayout, causing onLayout() to be called
            this.invalidate();

            // Done dragging
            isDragging = false;
            prevX = 0;
            lastDiffX = 0;
            return true;

        default:
            break;
        }

        return false;
    }
```
