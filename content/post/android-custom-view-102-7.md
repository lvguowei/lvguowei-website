+++
author = "Guowei Lv"
categories = ["Android Development"]
keywords = ["Android", "custom view"]
description = "QQ Step View"
featured= "android-custom-view-102.png"
featuredpath= "/img"
title = "Android Custom View 102 (Part 7)"
date = 2020-05-10T12:47:01+03:00
+++

Today we are going to write a custom view that count your steps.

{{< img-post "/img" "qqstepview.png" "Screen Shot" "center" >}}

There are 2 parts to draw: the arcs and the text.

The arcs are easy to draw, only thing to keep in mind is to remember the border width.

The tricky part is to draw the text.

Suppose we want the text to be in the center of the view.

Since the `canvas.drawText()` will use the baseline to draw. That means we need to figure out the `dy` from the center to the baseline.

{{< figure src="/img/stepviewblueprint.jpg" >}}

This is how to do it:

{{< highlight java>}}
val fontMetrics = textPaint.fontMetricsInt
val dy = (fontMetrics.bottom - fontMetrics.top) / 2 - fontMetrics.bottom
{{< /highlight >}}


The [full source code](https://github.com/lvguowei/QQStepView)
