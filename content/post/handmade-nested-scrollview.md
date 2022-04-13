+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "Understand how nested scrolling works in Android"
linktitle = ""
featured = "scroll.jpeg"
featuredpath = "/img"
featuredalt = ""
title = "Handmade NestedScrollView"
date = 2022-04-12T20:57:33+03:00
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

1. The touch events will first reach the parent `ScrollView`. The parent `ScrollView` then checks if it wants to intercept the event. (for `ScrollView`, the intercepting criteria is whether the events match the scrolling pattern). If not, it will pass the events to its child (inner `ScrollView`) to handle. If yes, it will intercept the upcoming events and not pass them to the inner `ScrollView`.
2. The child `ScrollView` also will call `requestDisallowInterceptTouchEvent(true)` to try to prevent the parent from intercepting the events when it detects the scrolling pattern, but it will never get the chance, because the parent will always detects the scroll pattern first and intercept the events.

What to do then? 

One hacky way is that when the child `ScrollView` receives `DOWN`, it tells parent to NOT intercept. And when it cannot scroll any more, it tells the parent you can now intercept.

{{< highlight kotlin>}}
class SimpleNestedScrollView(context: Context, attrs: AttributeSet) : ScrollView(context, attrs) {

    override fun dispatchTouchEvent(ev: MotionEvent): Boolean {
        if (ev.actionMasked == MotionEvent.ACTION_DOWN) parent.requestDisallowInterceptTouchEvent(
            true
        )

        if (ev.actionMasked == MotionEvent.ACTION_MOVE) {
            val offsetY = ev.y.toInt() - getInt("mLastMotionY")
            if (abs(offsetY) > 3) {
                if ((offsetY > 0 && isScrollToTop()) || (offsetY < 0 && isScrollToBottom())) {
                    parent.requestDisallowInterceptTouchEvent(false)
                }
            }
        }

        return super.dispatchTouchEvent(ev)
    }

    private fun isScrollToTop() = scrollY == 0

    private fun isScrollToBottom(): Boolean {
        return scrollY + height - paddingTop - paddingBottom == getChildAt(0).height
    }


    /**
     * Reflection
     */
    private fun getInt(field: String): Int {
        val f = ScrollView::class.java.getDeclaredField("mLastMotionY")
        f.isAccessible = true
        return f.get(this as ScrollView) as Int
    }

}
{{< /highlight >}}

**Holla, it works!**

{{< figure src="/img/simplenestedscrollview2.gif" >}}


*Note! This article is for educational purpose only, do not use this in production. Thank you.*
