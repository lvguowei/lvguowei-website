+++
author = "Guowei Lv"
categories = ["Android Custom View 102"]
keywords = ["Android", "custom view"]
description = "ColorTrackTextView"
featured= "android-custom-view-102.png"
featuredpath= "/img"
title = "Android Custom View 102 (Part 8)"
date = 2020-05-12T22:21:03+03:00
+++

Let's take a look at what we are going to build.

{{< figure src="/img/tracktextview2.gif" >}}


{{< figure src="/img/tracktextview.gif" >}}

The secret is to clip canvas before drawing the text!

{{< highlight kotlin>}}
private fun drawText(canvas: Canvas, paint: Paint, start: Int, end: Int) {
    canvas.save()

    rect.set(start, 0, end, height)
    canvas.clipRect(rect)

    val textString = text.toString()
    paint.getTextBounds(textString, 0, textString.length, bounds)

    val x = width / 2 - bounds.width() / 2

    val fontMetrics = changePaint.fontMetrics
    val dy = (fontMetrics.bottom - fontMetrics.top) / 2 - fontMetrics.bottom
    val baseline = height / 2 + dy

    canvas.drawText(textString, x.toFloat(), baseline, paint)

    canvas.restore()
}
{{< /highlight >}}

Full source code can be found [here](https://github.com/lvguowei/ColorTrackTextView)
