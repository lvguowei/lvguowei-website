---
title: "Android Custom Views 101 (Part III)"
date: 2017-07-08T14:47:36+03:00
categories: ["Android Development"]
keywords:
  - Android
  - Custom View
description: Learn about Androd Custom View and ViewGroup
featured: "featured-android-custom-view.png"
featuredalt: "Android custom view"
featuredpath: "/img"
---

In this post, we finally gonna take a look at how to implement `onMeasure()` properly.

When implementing a custom view, we should always consider its **lower and upper size limit**. In this case since the format of the time is fixed (hh:mm:ss). So we just need to get the width and height of it.

Suppose we set the `MAX_TIME = "00:00:00"`, then to get its width in pixel we can do:

{{< highlight java >}}
Paint.FontMetrics metrics = numberPaint.getFontMetrics();
int maxTextWidth = (int) Math.ceil(numberPaint.measureText(MAX_TIME));
{{< /highlight >}}

The `measureText()` only returns the width, so to get the height we have to find another way.
Based on the doc, `FontMetrics.top` is the maximum space a font can be from the baseline upwards, and `FontMetrics.bottom` is the maximum space a font can be below the baseline. So we can calculate the maximum font height to be:

{{< highlight java >}}
int maxTextHeight = (int) Math.ceil(metrics.bottom - metrics.top);
{{< /highlight >}}

Now we pick the bigger one from `maxTextHeight` and `maxTextWidth` to be the `measuredContentSize`:

{{< highlight java >}}
int contentSize = Math.max(maxTextHeight, maxTextWidth);
{{< /highlight >}}

Since we have figured out what size the TimerView wants to be, we now need to consider what its parent has told it. We can use a helper function `resolveSize()` to get the final measured size. (check its source code to see how the size is determined based on different spec)

{{< highlight java >}}
int measuredWidth = resolveSize(contentSize, widthMeasureSpec);
int measuredHeight = resolveSize(contentSize, heightMeasureSpec);
{{< /highlight >}}

Finally, we set call:

{{< highlight java >}}
setMeasuredDimension(measuredWidth, measuredHeight);
{{< /highlight >}}

Additionally, we have to consider also paddings. Which is very easy to do.

{{< highlight java >}}
int contentWidth = maxTextWidth + getPaddingLeft() + getPaddingRight();
int contentHeight = maxTextHeight + getPaddingTop() + getPaddingBottom();
int contentSize = Math.max(contentWidth, contentHeight);
{{< /highlight >}}

Now we are done. [source code](https://github.com/lvguowei/TimerView/tree/a0a929fe4ecc3dfb13066d200d9294c567ff296a)
