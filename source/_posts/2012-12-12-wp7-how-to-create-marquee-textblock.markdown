---
author: onmyway133
comments: true
date: 2012-12-12 10:47:14+00:00
layout: post
slug: wp7-how-to-create-marquee-textblock
title: WP7 how to create marquee TextBlock
wordpress_id: 66
categories:
- Windows Phone
tags:
- canvas
- marquee
- storyboard
- textblock
- windows phone
- wp7
- wp8
---

I created this article 1 year ago and didn't have time to write it. Now I write it but with little explanation, if you still does not understand, maybe you should read my source code
Last year, I was in need of a marquee TextBlock that can performs auto scrolling, I've found
[http://andrewbrobinson.com/2012/07/14/simple-marquees-in-windows-phone/](http://andrewbrobinson.com/2012/07/14/simple-marquees-in-windows-phone/)
[http://silverlightmarquee.codeplex.com/](http://silverlightmarquee.codeplex.com/)
But they didn't seem to fit my need, so I then created my own **MarqueeTextBlock**

THE IDEA
--
1. Create as a UserControl so it can be reused
2. Use Storyboard to perform translation animation because Storyboard is handled by GPU
3. Use Canvas to clip text when it goes out of this UserControl

WHAT WILL WE ACHIEVE
--
![marquee_textblock](http://fantageek.com/images/2012/12/marquee_textblock-200x140.png)

The source code is available on
 [Github](https://github.com/onmyway133/MarqueeTextBlock)

### CREATING MARQUEE TEXBLOCK

Right click on your project, choose Add -> New Item -> Windows Phone User Control and name it MarqueeTextBlock

DESIGNING MarqueeTextBlock.xaml
--

```xml
<Grid x:Name="LayoutRoot">
        <Canvas x:Name="canvas" Background="Transparent" Width="{Binding Width}" Height="{Binding Height}">
            <Canvas.Clip>
                <RectangleGeometry RadiusX="5" RadiusY="5" Rect="0, 0, 200, 100"/>
            </Canvas.Clip>
            <TextBlock x:Name="textBlock1"
                       Height="{Binding Height}"
                       Width="Auto"
                       TextWrapping="NoWrap"
                       Foreground="{Binding Foreground}"
                       Text="{Binding Text}"     
                       FontSize="{Binding MFontSize}"/>
        </Canvas>
    </Grid>
```

Here we use Canvas and RectangleGeometry to define the clipping area dynamically. For more info, see [Dynamic Clipping Width in Silverlight Within Canvas](http://stackoverflow.com/questions/3732396/dynamic-clipping-width-in-silverlight-within-canvas)
The MFontSize is an additional dependency property that allows user to specify font size
Remember to set TextWrapping to NoWrap

### WORKING WITH MarqueeTextBlock.xaml.cs


We have isLoaded to keep track of whether our control is loaded or not. We can't do marquee until control is fully loaded. To achieve this, we handle 2 events

```csharp
void MarqueeTextBlock_Unloaded(object sender, RoutedEventArgs e)
        {
            // stop marquee when control is unloaded
            StopMarquee();
        }
```

We simply stop marquee then the control is unloaded :)

```
void MarqueeTextBlock_Loaded(object sender, RoutedEventArgs e)
        {
            if (!isLoaded)
            {
                // control is loaded
                isLoaded = true;
                LayoutRoot.DataContext = this;
                // resize the clipping
                Rect rect = new Rect(0, 0, canvas.ActualWidth, canvas.ActualHeight);
                RectangleGeometry reo = new RectangleGeometry();
                reo.Rect = rect;
                this.canvas.Clip = reo;
                // always vertical alignment
                ChangeOffsetTop();
                // this is OK for marquee to start
                StartMarquee();
            }
            else
            {
                // just run it, after user come to this page again
                StartMarquee();
            }
        }
```

We set the text and font size by setting the DataContext. We then resize clipping area according to canvas 's size. We use ChangeOffsetTop to let the text centers vertically inside our control. If all goes well, the control "should" start marquee.
I say "should" because we only need to marquee if our text is longer than the available view

```csharp
bool ShouldStartMarquee()
        {
            return textBlock1.ActualWidth > LayoutRoot.ActualWidth;
        }
[/code]
Finally, the core method is StartMarquee()
[code language="csharp"]
public void StartMarquee()
        {
            // first stop all marquee
            StopMarquee();
            if (ShouldStartMarquee())
            {
                // change offset
                ChangeOffsetLeft(true);
                DoAnimationFirst();
            }
            else
            {
                // change offset
                ChangeOffsetLeft(false);
            }
        }
```

We ought to stop marquee before starting again. And in order to achieve better visual effect, we call ChangeOffetLeft to set the text initial left position. You can change this through the offset and defaultOffset variables. Thanks for the post [Unable to position Rectangle in Canvas in Silverlight 4](http://stackoverflow.com/questions/4342743/unable-to-position-rectangle-in-canvas-in-silverlight-4)

The animation is done using Storyboard via 2 methods
The DoAnimationFirst() is used to translate the text from the initial position to the far left, then the DoAnimationAfter() is used to translate the text from the far right to the right edge of the canvas, so that the text can be translated to the left again. This loops forever until the control is unloaded or user explicitly calls StopMarquee()

```csharp
void DoAnimationFirst()
        {
            storyboard = new Storyboard();
            TranslateTransform trans = new TranslateTransform() { X = 5.0, Y = 1.0 };
            textBlock1.RenderTransformOrigin = new Point(0.5, 0.5);
            textBlock1.RenderTransform = trans;
            // we must calculate the Duration
            DoubleAnimation moveAnim = new DoubleAnimation();
            int durationFirst = CalculateDurationFirst();
            moveAnim.Duration = TimeSpan.FromMilliseconds(durationFirst);
            moveAnim.From = 0;
            moveAnim.To = -textBlock1.ActualWidth - offset;
            Storyboard.SetTarget(moveAnim, textBlock1);
            Storyboard.SetTargetProperty(moveAnim, new PropertyPath("(UIElement.RenderTransform).(TranslateTransform.X)"));
            // subscribe to Completed event
            storyboard.Completed += new EventHandler(storyboard_Completed);
            storyboard.Children.Add(moveAnim);
            storyboard.FillBehavior = FillBehavior.HoldEnd;
            storyboard.Begin();
        }
```

```csharp
void DoAnimationAfter()
        {
            storyboard = new Storyboard();
            TranslateTransform trans = new TranslateTransform() { X = 5.0, Y = 1.0 };
            textBlock1.RenderTransformOrigin = new Point(0.5, 0.5);
            textBlock1.RenderTransform = trans;
            // we must calculate the Duration
            DoubleAnimation moveAnim = new DoubleAnimation();
            int durationAfter = CalculateDurationAfter();
            moveAnim.Duration = TimeSpan.FromMilliseconds(durationAfter);
            moveAnim.From = canvas.ActualWidth;
            moveAnim.To = -textBlock1.ActualWidth - offset;
            Storyboard.SetTarget(moveAnim, textBlock1);
            Storyboard.SetTargetProperty(moveAnim, new PropertyPath("(UIElement.RenderTransform).(TranslateTransform.X)"));
            storyboard.Children.Add(moveAnim);
            storyboard.RepeatBehavior = RepeatBehavior.Forever;
            storyboard.FillBehavior = FillBehavior.HoldEnd;
            storyboard.Begin();
        }
```


I left the CalculateDurationFirst() and CalculateDurationAfter() so you can tweak it to your own desire
Stopping marquee is just a step with stopping the Storyboard

```csharp
public void StopMarquee()
        {
            if (storyboard != null)
            {
                storyboard.Stop();
            }
        }
```

USING MARQUEE TEXTBLOCK
--
You can declare MarqueeTextBlock in your xaml like this

```xml
<entropy:MarqueeTextBlock x:Name="marquee1"
                                          Text="This is a very long text"
                                          MFontSize="30"
                                          Width="230"
                                          Height="80"/>
```

with xmlns:entropy="clr-namespace:DemoMarqueeTextBlock" is a custom xmlns that targets your application namespace
Then call StartMarquee() or StopMarquee() to toggle marquee

P/S: I haven't tried this on Windows Phone 8, I think it should run fine, too :)
