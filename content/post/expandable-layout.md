+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "A sketch"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "How to implement an ExpandableLayout"
date = 2021-12-17T21:15:42+02:00
+++

It is quite common in Android that we need some expandable widget to show and hide information.
This is my first attempt, note that this is only a "sketch", and I intentionally leave some room for improvement.

One interesting detail worth mentioning is how the animation is done. I used the `reverse()` function to play animation backwards in order to achieve a smooth and continuous feel.

{{< img-post "/img" "expandable-layout.gif" "Expandable Layout" "center" >}}

Here is the code:

{{< highlight kotlin>}}
package com.example.expandablelayout

import android.animation.Animator
import android.animation.AnimatorListenerAdapter
import android.animation.ValueAnimator
import android.content.Context
import android.util.AttributeSet
import android.view.View
import android.view.ViewGroup
import androidx.cardview.widget.CardView
import androidx.core.view.updateLayoutParams

class ExpandableLayout @JvmOverloads constructor(
    context: Context, attrs: AttributeSet? = null
) : CardView(context, attrs) {

    private lateinit var titleView: View
    private lateinit var contentLayout: ViewGroup
    private lateinit var arrow: View

    private var expanded = false

    private var animating = false

    private var animDuration = 1000L

    private val expandAnimator: ValueAnimator = ValueAnimator.ofFloat(0f, 1f).apply {
        duration = animDuration
        addUpdateListener {
            val progress = it.animatedValue as Float
            val wrapContentHeight = contentLayout.measureWrapContentHeight()
            contentLayout.updateLayoutParams {
                height = (wrapContentHeight * progress).toInt()
            }

            arrow.rotation = progress * 180
        }
        addListener(object : AnimatorListenerAdapter() {
            override fun onAnimationStart(animation: Animator?) {
                super.onAnimationStart(animation)
                animating = true
            }

            override fun onAnimationEnd(animation: Animator?) {
                super.onAnimationEnd(animation)
                animating = false
            }
        })
    }


    override fun onFinishInflate() {
        super.onFinishInflate()
        val parentLayout = getChildAt(0) as ViewGroup

        titleView = parentLayout.getChildAt(0)
        contentLayout = parentLayout.getChildAt(1) as ViewGroup
        arrow = parentLayout.findViewById(R.id.arrowImageView)

        if (expanded) {
            arrow.rotation = 180f
        } else {
            contentLayout.updateLayoutParams {
                height = 0
            }
            arrow.rotation = 0f

        }

        titleView.setOnClickListener {
            when {
                animating -> {
                    expandAnimator.reverse()
                    expanded = !expanded
                }
                expanded -> {
                    expandAnimator.reverse()
                    expanded = false
                }
                else -> {
                    expandAnimator.start()
                    expanded = true
                }
            }
        }
    }
}

fun ViewGroup.measureWrapContentHeight(): Int {
    this.measure(
        View.MeasureSpec
            .makeMeasureSpec((this.parent as View).measuredWidth, View.MeasureSpec.EXACTLY),
        View.MeasureSpec
            .makeMeasureSpec(0, View.MeasureSpec.UNSPECIFIED)
    )
    return measuredHeight
}

{{< /highlight >}}


{{< highlight kotlin>}}
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <com.example.expandablelayout.ExpandableLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_margin="20dp"
        app:cardCornerRadius="12dp"
        app:cardElevation="20dp">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:background="?selectableItemBackground"
                android:orientation="horizontal">

                <TextView
                    android:id="@+id/title"
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:gravity="center"
                    android:padding="20dp"
                    android:text="Books that I love"
                    android:textSize="18sp"
                    android:textStyle="bold" />


                <ImageView
                    android:id="@+id/arrowImageView"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_gravity="center"
                    android:layout_margin="20dp"
                    android:src="@drawable/ic_baseline_arrow_drop_down_24" />
            </LinearLayout>


            <FrameLayout
                android:id="@+id/content"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:background="@color/cardview_light_background">

                <TextView
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:background="#e8f4f8"
                    android:padding="20dp"
                    android:text="Introduction to Algorithms\n\nThe Art of Computer Programming\n\nStructure and Interpretation of Computer Programs\n\nProgramming Pearls\n\nPatterns of Enterprise Application Architecture"
                    android:textStyle="italic" />
            </FrameLayout>
        </LinearLayout>


    </com.example.expandablelayout.ExpandableLayout>
</LinearLayout>

{{< /highlight >}}
