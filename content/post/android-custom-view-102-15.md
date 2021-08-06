+++
author = "Guowei Lv"
categories = ["Android Development"]
keywords = ["Android", "custom view", "Dashboard"]
description = "Draw circled avatar"
featured= "android-custom-view-102.png"
featuredpath= "/img"
title = "Android Custom View 102 (Part 15)"
date = 2021-08-06T14:14:33+03:00
+++

Let's use Xfermode to draw a circled avatar.

The original avatar image is this:

{{< img-post "/img" "avatar_sqr.png" "Original Avatar Image" "center" >}}

We will implement a custom view that clips it into a circle.

{{< highlight kotlin>}}

class Avatar @JvmOverloads constructor(
    context: Context, attrs: AttributeSet? = null, defStyleAttr: Int = 0
) : View(context, attrs, defStyleAttr) {

    private val WIDTH = dp2px(300)

    private val PADDING = dp2px(50)

    val paint = Paint(Paint.ANTI_ALIAS_FLAG)

    val bitmap = getAvatar(WIDTH.toInt())

    val xfermode = PorterDuffXfermode(PorterDuff.Mode.SRC_IN)

    val savedArea = RectF()

    override fun onSizeChanged(w: Int, h: Int, oldw: Int, oldh: Int) {
        super.onSizeChanged(w, h, oldw, oldh)
        savedArea.set(
            PADDING,
            PADDING,
            PADDING + WIDTH,
            PADDING + WIDTH
        )
    }

    override fun onDraw(canvas: Canvas) {
        super.onDraw(canvas)

        val saved = canvas.saveLayer(savedArea, paint)
        canvas.drawOval(PADDING, PADDING, PADDING + WIDTH, PADDING + WIDTH, paint)
        paint.xfermode = xfermode
        canvas.drawBitmap(bitmap, dp2px(50), dp2px(50), paint)
        paint.xfermode = null
        canvas.restoreToCount(saved)
    }
    
    private fun getAvatar(width: Int): Bitmap {
        val options: BitmapFactory.Options = BitmapFactory.Options()
        options.inJustDecodeBounds = true
        BitmapFactory.decodeResource(resources, R.drawable.avatar, options)
        options.inJustDecodeBounds = false
        options.inDensity = options.outWidth
        options.inTargetDensity = width
        return BitmapFactory.decodeResource(resources, R.drawable.avatar, options)
    }
}

{{< /highlight >}}


{{< img-post "/img" "avatar_cir.png" "Avatar Circled" "center" >}}

For detailed explanation of Xfermode, please refer to [this post](https://www.lvguowei.me/post/android-custom-view-102-2/)
