+++
categories = ["Android Development"]
keywords = ["SeekBar", "Android"]
description = "How to customize the colors for android seekbar"
title = "Customize Android Seekbar Color"
date = 2017-08-23T20:50:44+03:00
featured = "featured-android-design.png"
featuredpath = "/img"
+++

I was tasked to create a simple media player app for a shop demo, and I'm using the built-in `SeekBar` for the volume control. All goes well, until I want to change the color of it. The design has it like the thumb of the `SeekBar` is white, and first half is green and rest half is grey. It took me almost a whole day to find a satisfying anwser, come on Android!

But anyway, here is the simplest way that I can find.

1. Define an `volume_control.xml`

{{< highlight xml >}}

<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android" >
    <item android:id="@android:id/background">
        <shape android:shape="rectangle" >
            <solid
                android:color="@color/grey" />
        </shape>
    </item>
    <item android:id="@android:id/progress">
        <clip>
            <shape android:shape="rectangle" >
                <solid
                    android:color="@color/green" />
            </shape>
        </clip>
    </item>
</layer-list>

{{< /highlight >}}

2. Then in the layout file, define a `SeekBar` as follows:

{{< highlight xml >}}

<SeekBar
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:max="100"
    android:maxHeight="3dp" 
    android:minHeight="3dp"
    android:progressDrawable="@drawable/volume_seekbar"
    android:thumbTint="@color/white"/>
{{< /highlight >}}

Pay attention to the `maxHeight` and `minHeight`, without them the bar will look very thick.
Also that to change the color of `thumb`, you can do `thumbTint`.

I have seen other solutions like create a new theme and assign it to the SeekBar, or completely write a SeekBar yourself, but they all seem overkill for such a simple task.
