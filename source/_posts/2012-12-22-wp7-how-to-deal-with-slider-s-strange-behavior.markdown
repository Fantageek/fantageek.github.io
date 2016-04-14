---
author: onmyway133
comments: true
date: 2012-12-22 17:36:56+00:00
layout: post
slug: wp7-how-to-deal-with-slider-s-strange-behavior
title: WP7 how to deal with slider 's strange behavior
wordpress_id: 134
categories:
- Windows Phone
tags:
- behavior
- gesture
- slider
- wp7
---

Sometimes the slider in your   app can be dragged for very short range. Don't panic, as this strange behavior is caused by using toolkit GestureListener incorrectly.




The solution is disable Gesture listener when starting dragging slider, then enable Gesture when not dragging slider anymore.




The XAML part




[sourcecode language="csharp"]  

<Slider Height="84" Name="fixedSlider" ManipulationStarted="disableGestures" ManipulationCompleted="restoreGestures" />  

[/sourcecode]




The code behind




[sourcecode language="csharp"]  

GestureType prevGestureType;  

private void disableGestures(object sender, ManipulationStartedEventArgs e)  

{  

    prevGestureType = TouchPanel.EnabledGestures;  

    TouchPanel.EnabledGestures = GestureType.None;  

    fixedSlider.IsHitTestVisible = false;  

}




private void restoreGestures(object sender, ManipulationCompletedEventArgs e)  

{  

    TouchPanel.EnabledGestures = prevGestureType;  

}  

[/sourcecode]




**Reference**  

1. [http://stackoverflow.com/questions/13342825/wp7-slider-strange-behavior](http://stackoverflow.com/questions/13342825/wp7-slider-strange-behavior)
