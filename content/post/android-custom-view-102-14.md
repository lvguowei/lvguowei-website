+++
author = "Guowei Lv"
categories = ["Android Custom View 102"]
keywords = ["Android", "custom view", "Dashboard"]
description = "Draw piechart"
featured= "android-custom-view-102.png"
featuredpath= "/img"
title = "Android Custom View 102 (Part 14)"
date = 2021-08-06T12:30:31+03:00
+++

In this tutorial let's see how to draw a piechart.

{{< highlight kotlin>}}

class PieChart @JvmOverloads constructor(
    context: Context, attrs: AttributeSet? = null, defStyleAttr: Int = 0
) : View(context, attrs, defStyleAttr) {

    val paint: Paint = Paint(Paint.ANTI_ALIAS_FLAG)
    val bounds: RectF = RectF()

    val RADIUS = dp2px(150)
    val OFFSET = dp2px(50)
    var offsetIndex = 2

    val angles = arrayOf(60.0f, 120.0f, 30.0f, 150.0f)
    val colors = arrayOf("#07004d", "#2d82b7", "#42e2b8", "#f3dfbf")

    override fun onSizeChanged(w: Int, h: Int, oldw: Int, oldh: Int) {
        super.onSizeChanged(w, h, oldw, oldh)
        bounds.set(width / 2 - RADIUS, height / 2 - RADIUS, width / 2 + RADIUS, height / 2 + RADIUS)
    }

    override fun onDraw(canvas: Canvas) {
        super.onDraw(canvas)

        var currentAngle = 0.0f
        for (i in angles.indices) {
            paint.color = Color.parseColor(colors[i])
            canvas.save()
            if (offsetIndex == i) {
                canvas.translate(
                    (cos(Math.toRadians((currentAngle + angles[i] / 2).toDouble())) * OFFSET).toFloat(),
                    (sin(Math.toRadians((currentAngle + angles[i] / 2).toDouble())) * OFFSET).toFloat()
                )
            }

            canvas.drawArc(bounds, currentAngle, angles[i], true, paint)
            canvas.restore()
            currentAngle += angles[i]
        }
    }
}

{{< /highlight >}}

{{< img-post "/img" "piechart.png" "piechart" "center" >}}
