+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "Understand how nested scrolling works in Android"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Handmade NestedScrollView"
date = 2022-04-12T20:57:33+03:00
draft = true
+++

I'm learning how the nested scrolling machanism works on Android, but couldn't really find any in-depth material. So I turned into the Chinese community,
and found [this incredible article](https://juejin.cn/post/6844903761060577294). It is very long and detailed to death, I don't really have time to go through it all.
I find the first half, a handmade SimpleNestedScrollView to be quite interesting, let me put that part in English here.

# Understand the problem

*If you have a `ScrollView` inside another `ScrollView`(same scrolling direction, both vertical or horizontal), then what to expect of the scrolling behaviour?*

Well, let's try it out and see for ourselves:
{{< highlight xml>}}
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <ScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_gravity="center"
        android:background="@color/teal_200">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical">

            <TextView
                android:layout_width="match_parent"
                android:layout_height="300dp"
                android:background="#223399"
                android:gravity="center"
                android:padding="20dp"
                android:text="Header"
                android:textColor="#FFFFFF"
                android:textSize="28sp" />

            <ScrollView
                android:layout_width="match_parent"
                android:layout_height="250dp">

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="300dp"
                    android:background="@color/purple_200"
                    android:orientation="vertical">

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="100dp"
                        android:gravity="center"
                        android:text="scroll view item 1" />

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="100dp"
                        android:gravity="center"
                        android:text="scroll view item 2" />

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="100dp"
                        android:gravity="center"
                        android:text="scroll view item 3" />

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="100dp"
                        android:gravity="center"
                        android:text="scroll view item 4" />

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="100dp"
                        android:gravity="center"
                        android:text="scroll view item 5" />
                </LinearLayout>
            </ScrollView>

            <TextView
                android:layout_width="match_parent"
                android:layout_height="200dp"
                android:background="#999999"
                android:gravity="center"
                android:text="This is some content" />

            <TextView
                android:layout_width="match_parent"
                android:layout_height="200dp"
                android:background="#888888"
                android:gravity="center"
                android:text="This is some content more" />
        </LinearLayout>
    </ScrollView>
</LinearLayout>
{{< /highlight >}}


{{< figure src="/img/simplenestedscrollview1.gif" >}}

We can clearly see that the result is: **the inner scroll view cannot really scroll**.

# First improvement

Why the inner `ScrollView` cannot scroll? 

To understand this, we need to understand the touch event dispatching and intercepting machanism in the android view system.

You can read my [previous article](https://www.lvguowei.me/post/understand-touch-event-android/) for fundamentals in detail, but I will explain what happens here also:









{{< highlight xml>}}
{{< /highlight >}}

{{< highlight xml>}}
{{< /highlight >}}

{{< highlight xml>}}
{{< /highlight >}}

{{< highlight xml>}}
{{< /highlight >}}

{{< highlight xml>}}
{{< /highlight >}}
