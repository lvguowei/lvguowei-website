+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "Help you understander better about measure and layout"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Fun Facts About OnMeasure() and OnLayout()"
date = 2022-01-03T21:48:45+02:00
+++


Let's understand more about Android view's measure and layout process, and have some fun along the way.

Let's say we have this:

{{< highlight xml>}}
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <com.example.stubornview.StubbornView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="@color/design_default_color_primary" />

</LinearLayout>
{{< /highlight >}}

{{< highlight kotlin>}}

class StubbornView @JvmOverloads constructor(
    context: Context, attrs: AttributeSet? = null, defStyleAttr: Int = 0
) : View(context, attrs, defStyleAttr) {

    /**
     * 1. Parent LinearLayout combines developer's requirements
     *    and the available space of itself into MeasureSpecs and
     *    pass them here.
     */
    override fun onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int) {
        val width = MeasureSpec.getSize(widthMeasureSpec)
        val widthMode = MeasureSpec.getMode(widthMeasureSpec)

        val height = MeasureSpec.getSize(heightMeasureSpec)
        val heightMode = MeasureSpec.getMode(heightMeasureSpec)

        Log.d("test", "Width: $width, mode: ${getMode(widthMode)}")
        Log.d("test", "Height: $height, mode: ${getMode(heightMode)}")

        /**
         * 2. StubbornView being naughty by ignoring parent's requirement and simply use 100px, 100px
         */
        setMeasuredDimension(100, 100)
    }

    /**
     * 3. Parent is not happy with the stubbornness of the child view, and choose to forcefully
     * carry out the match_parent rules.
     */
    override fun layout(l: Int, t: Int, r: Int, b: Int) {
        Log.d("test", "layout(l: $left, t: $top, r: $right, b: $bottom)")

        /**
         * 4. Being really stubborn, we overwrite the parent's final decision again
         *    before the position data is saved!
         */
        super.layout(l, t, l + 100, t + 100)
    }

    override fun onLayout(changed: Boolean, left: Int, top: Int, right: Int, bottom: Int) {
        super.onLayout(changed, left, top, right, bottom)

        /**
         * 5. Let's see what who wins. (StubbornView ofc)
         */
        Log.d("test", "onLayout(l: $left, t: $top, r: $right, b: $bottom)")
    }

    private fun getMode(mode: Int) = when (mode) {
        MeasureSpec.EXACTLY -> "EXACTLY"
        MeasureSpec.AT_MOST -> "AT_MOST"
        MeasureSpec.UNSPECIFIED -> "UNSPECIFIED"
        else -> ""
    }
}
{{< /highlight >}}

Here, the StubbornView really want to be 100px, 100px in size.

Try to change the parent to LinearLayout and see what is different?
