+++
author = "Guowei Lv"
categories = ["Android Development"]
keywords = ["Android", "custom view", "Canvas"]
description = "More on canvas transformations"
featured= "android-custom-view-102.png"
featuredpath= "/img"
title = "Android Custom View 102 (Part 16)"
date = 2021-08-13T10:34:35+03:00
+++

In this post I want to go into details on canvas transformations, especially if we want to combine them.

Let's take the simple example of translation + rotation.
The end result is like this:

{{< img-post "/img" "canvastransform1.png" "Canvas transform" "center" >}}

We can easily see that the image has been translated to (300, 200) and then rotated 45 degrees.

{{< highlight kotlin>}}
class TransformView @JvmOverloads constructor(
    context: Context, attrs: AttributeSet? = null, defStyleAttr: Int = 0
) : View(context, attrs, defStyleAttr) {
    private val paint = Paint(Paint.ANTI_ALIAS_FLAG)
    private val image: Bitmap
    private val imageSize = dp2px(100)
    init {
        image = getImage(dp2px(100).toInt())
    }

    override fun onDraw(canvas: Canvas) {
        super.onDraw(canvas)

        canvas.save()
        canvas.translate(dp2px(200), dp2px(100))
        canvas.rotate(45.0f, imageSize / 2, imageSize / 2)
        canvas.drawBitmap(image, 0.0f, 0.0f, paint)
        canvas.restore()
        }
    }
{{< /highlight >}}

This seems straightforward, first we translate the canvas and then rotate it and lastly draw image on it. But do we fully understand it really? Let's try something else to test our understanding.

My friend Eva Luator argues that mathmatically, it doesn't matter if we first translate and then rotate or first rotate and then translate. He even drawed some pictures to show me:


{{< img-post "/img" "canvastransform4.jpg" "canvas transformations" "center" >}}
{{< img-post "/img" "canvastransform5.jpg" "canvas transformations" "center" >}}


I said we can try it and here is the result:

{{< highlight kotlin>}}
    override fun onDraw(canvas: Canvas) {
        super.onDraw(canvas)

        canvas.save()
        cannvas.rotate(45.0f, imageSize / 2, imageSize / 2)
        canvas.translate(dp2px(200), dp2px(100))
        canvas.drawBitmap(image, 0.0f, 0.0f, paint)
        canvas.restore()
        }
    }
{{< /highlight >}}

{{< img-post "/img" "canvastransform2.png" "Avatar Circled" "center" >}}

Uh-oh... not what we expected. But why?

The reason is that canvas transformations are **continuous**, meaning the latter transformation is done on the basis of the previous one.

Let me draw this for you:

{{< img-post "/img" "canvastransform3.jpg" "canvas transformations" "center" >}}

So this means that after each transformation, the coordinate system also changes, this can make things very complicated to for our minds.

Don't worry, there is this one trick that can help you "fix" the coordinate system, but we have to do the transformations in reverse order. Let me explain how this works:

Note that from now on we only use the original View's coordinate system.

What we want to do:
1. Rotate 45 degrees, pivot point is (imageSize / 2, imageSize / 2)
2. Translate to (200, 100)

Now pay attention, in code we have to reverse this order

{{< highlight kotlin>}}
    override fun onDraw(canvas: Canvas) {
        super.onDraw(canvas)

        canvas.save()
        canvas.translate(dp2px(200), dp2px(100))
        cannvas.rotate(45.0f, imageSize / 2, imageSize / 2)
        canvas.drawBitmap(image, 0.0f, 0.0f, paint)
        canvas.restore()
        }
    }
{{< /highlight >}}

Or we can try to do it the other way around:

1. Translate to (200, 100)
2. Rotate 45 degrees, pivot point is (200 + imageSize / 2, 100 + imageSize / 2)


Translate this into code we get:

{{< highlight kotlin>}}
    override fun onDraw(canvas: Canvas) {
        super.onDraw(canvas)

        canvas.save()
        cannvas.rotate(45.0f, dp2px(200) + imageSize / 2, dp2px(100) + imageSize / 2)
        canvas.translate(dp2px(200), dp2px(100))
        canvas.drawBitmap(image, 0.0f, 0.0f, paint)
        canvas.restore()
        }
    }
{{< /highlight >}}

Notice that the pivot point depends on where you want to do the rotation, after the translation or before.

And now you are a master of canvas transformations!
