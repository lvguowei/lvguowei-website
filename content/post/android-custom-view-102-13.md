+++
author = "Guowei Lv"
categories = ["Android Custom View 102"]
keywords = ["Android", "custom view", "Dashboard"]
description = "Draw a dashboard meter"
featured= "android-custom-view-102.png"
featuredpath= "/img"
title = "Android Custom View 102 (Part 13)"
date = 2021-08-05T21:30:48+03:00
+++

Today let's draw a dashboard meter.

I have drawn the blueprint this time:

{{< img-post "/img" "dashboardmeter.jpg" "Blueprint" "center" >}}

Here is the code:

{{< highlight kotlin>}}

class Dashboard @JvmOverloads constructor(
    context: Context, attrs: AttributeSet? = null, defStyleAttr: Int = 0
) : View(context, attrs, defStyleAttr) {

    private val BOTTOM_ANGLE = 120
    private val RADIUS = dp2px(150)
    private val ARM_LENGTH = dp2px(120)
    private val DASH_WIDTH = dp2px(2)
    private val DASH_LENGTH = dp2px(10)
    private val DASH_NUM = 12

    private val paint = Paint(Paint.ANTI_ALIAS_FLAG)
    private var effect: PathDashPathEffect
    private val dash = Path()

    init {
        paint.style = Paint.Style.STROKE
        paint.strokeWidth = dp2px(2)
        dash.addRect(0.0f, 0.0f, DASH_WIDTH, DASH_LENGTH, Path.Direction.CW)
        val arc = Path()
        arc.addArc(
            width / 2 - RADIUS,
            height / 2 - RADIUS,
            width / 2 + RADIUS,
            height / 2 + RADIUS,
            90 + BOTTOM_ANGLE / 2.0f,
            360.0f - BOTTOM_ANGLE
        )

        val pathMeasure = PathMeasure(arc, false)

        effect = PathDashPathEffect(
            dash,
            (pathMeasure.length - dp2px(2)) / DASH_NUM,
            0.0f,
            PathDashPathEffect.Style.MORPH
        )
    }

    override fun onDraw(canvas: Canvas) {
        super.onDraw(canvas)

        // draw arc
        canvas.drawArc(
            width / 2 - RADIUS,
            height / 2 - RADIUS,
            width / 2 + RADIUS,
            height / 2 + RADIUS,
            90 + BOTTOM_ANGLE / 2.0f,
            360.0f - BOTTOM_ANGLE,
            false,
            paint
        )
        paint.pathEffect = effect

        // draw dashes
        canvas.drawArc(
            width / 2 - RADIUS,
            height / 2 - RADIUS,
            width / 2 + RADIUS,
            height / 2 + RADIUS,
            90 + BOTTOM_ANGLE / 2.0f,
            360.0f - BOTTOM_ANGLE,
            false,
            paint
        )

        paint.pathEffect = null

        // draw arm
        canvas.drawLine(
            width / 2.0f,
            height / 2.0f,
            (cos(Math.toRadians(getAngleFromMark(3).toDouble())) * ARM_LENGTH).toFloat() + width / 2,
            (sin(Math.toRadians(getAngleFromMark(3).toDouble())) * ARM_LENGTH).toFloat() + height / 2,
            paint
        )

    }

    private fun getAngleFromMark(n: Int) =
        90 + BOTTOM_ANGLE / 2 + ((360 - BOTTOM_ANGLE) / DASH_NUM) * n
}

fun dp2px(dp: Int) =
    TypedValue.applyDimension(
        TypedValue.COMPLEX_UNIT_DIP,
        dp.toFloat(),
        Resources.getSystem().displayMetrics
    )

{{< /highlight >}}

Here is how the custom view looks:

{{< img-post "/img" "dashboardmeterscreenshot.png" "Blueprint" "center" >}}
