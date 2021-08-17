+++
author = "Guowei Lv"
categories = ["Android Development"]
keywords = ["Android", "custom view", "Canvas"]
description = "Using camera in canvas transformations"
featured= "android-custom-view-102.png"
featuredpath= "/img"
title = "Android Custom View 102 (Part 17)"
date = 2021-08-17T12:59:09+03:00
+++

Let's look at how to use camera in canvas transformation.

First this is what we want to implement:

{{< img-post "/img" "cameraview1.jpg" "Camera View" "center" >}}

The trick is to draw the upper and lower part separately. And we are using this "reverse drawing" technique.

# Upper part

{{< highlight kotlin>}}
override fun onDraw(canvas: Canvas) {
    super.onDraw(canvas)

    canvas.save()
    canvas.drawBitmap(image, leftPadding, topPadding, paint)
    canvas.restore()
    }
{{< /highlight >}}

{{< img-post "/img" "cameraview2.jpg" "Camera View" "center" >}}

{{< highlight kotlin>}}
override fun onDraw(canvas: Canvas) {
    super.onDraw(canvas)

    canvas.save()
    canvas.translate(-(leftPadding + imageSize / 2), -(topPadding + imageSize / 2))
    canvas.drawBitmap(image, leftPadding, topPadding, paint)
    canvas.restore()
}
{{< /highlight >}}

{{< img-post "/img" "cameraview3.jpg" "Camera View" "center" >}}

{{< highlight kotlin>}}
override fun onDraw(canvas: Canvas) {
    super.onDraw(canvas)

    canvas.save()
    canvas.rotate(foldDegree)
    canvas.translate(-(leftPadding + imageSize / 2), -(topPadding + imageSize / 2))
    canvas.drawBitmap(image, leftPadding, topPadding, paint)
    canvas.restore()
}
{{< /highlight >}}

{{< img-post "/img" "cameraview4.jpg" "Camera View" "center" >}}

{{< highlight kotlin>}}
override fun onDraw(canvas: Canvas) {
    super.onDraw(canvas)

    canvas.save()
    canvas.clipRect(-imageSize, -imageSize, imageSize, 0.0f)
    canvas.rotate(foldDegree)
    canvas.translate(-(leftPadding + imageSize / 2), -(topPadding + imageSize / 2))
    canvas.drawBitmap(image, leftPadding, topPadding, paint)
    canvas.restore()
}
{{< /highlight >}}

{{< img-post "/img" "cameraview5.jpg" "Camera View" "center" >}}

{{< highlight kotlin>}}
override fun onDraw(canvas: Canvas) {
    super.onDraw(canvas)

    canvas.save()
    canvas.rotate(-foldDegree)
    canvas.clipRect(-imageSize, -imageSize, imageSize, 0.0f)
    canvas.rotate(foldDegree)
    canvas.translate(-(leftPadding + imageSize / 2), -(topPadding + imageSize / 2))
    canvas.drawBitmap(image, leftPadding, topPadding, paint)
    canvas.restore()
}
{{< /highlight >}}

{{< img-post "/img" "cameraview5.jpg" "Camera View" "center" >}}

{{< highlight kotlin>}}
override fun onDraw(canvas: Canvas) {
    super.onDraw(canvas)

    canvas.save()
    canvas.translate(leftPadding + imageSize / 2, topPadding + imageSize / 2)
    canvas.rotate(-foldDegree)
    canvas.clipRect(-imageSize, -imageSize, imageSize, 0.0f)
    canvas.rotate(foldDegree)
    canvas.translate(-(leftPadding + imageSize / 2), -(topPadding + imageSize / 2))
    canvas.drawBitmap(image, leftPadding, topPadding, paint)
    canvas.restore()
}
{{< /highlight >}}

{{< img-post "/img" "cameraview6.jpg" "Camera View" "center" >}}

# Lower part

{{< highlight kotlin>}}
override fun onDraw(canvas: Canvas) {
    super.onDraw(canvas)

    canvas.clipRect(-imageSize, 0.0f, imageSize, imageSize)
    canvas.rotate(foldDegree)
    canvas.translate(-(leftPadding + imageSize / 2), -(topPadding + imageSize / 2))
    canvas.drawBitmap(image, leftPadding, topPadding, paint)
}
{{< /highlight >}}

{{< img-post "/img" "cameraview7.jpg" "Camera View" "center" >}}

{{< highlight kotlin>}}
override fun onDraw(canvas: Canvas) {
    super.onDraw(canvas)

    camera.applyToCanvas(canvas)
    canvas.clipRect(-imageSize, 0.0f, imageSize, imageSize)
    canvas.rotate(foldDegree)
    canvas.translate(-(leftPadding + imageSize / 2), -(topPadding + imageSize / 2))
    canvas.drawBitmap(image, leftPadding, topPadding, paint)
}
{{< /highlight >}}

{{< img-post "/img" "cameraview8.jpg" "Camera View" "center" >}}

{{< highlight kotlin>}}
override fun onDraw(canvas: Canvas) {
    super.onDraw(canvas)

    canvas.rotate(-foldDegree)
    camera.applyToCanvas(canvas)
    canvas.clipRect(-imageSize, 0.0f, imageSize, imageSize)
    canvas.rotate(foldDegree)
    canvas.translate(-(leftPadding + imageSize / 2), -(topPadding + imageSize / 2))
    canvas.drawBitmap(image, leftPadding, topPadding, paint)
}
{{< /highlight >}}

{{< img-post "/img" "cameraview9.jpg" "Camera View" "center" >}}

{{< highlight kotlin>}}
override fun onDraw(canvas: Canvas) {
    super.onDraw(canvas)

    canvas.translate(leftPadding + imageSize / 2, topPadding + imageSize / 2)
    canvas.rotate(-foldDegree)
    camera.applyToCanvas(canvas)
    canvas.clipRect(-imageSize, 0.0f, imageSize, imageSize)
    canvas.rotate(foldDegree)
    canvas.translate(-(leftPadding + imageSize / 2), -(topPadding + imageSize / 2))
    canvas.drawBitmap(image, leftPadding, topPadding, paint)
}
{{< /highlight >}}

{{< img-post "/img" "cameraview10.jpg" "Camera View" "center" >}}

# Complete code

{{< highlight kotlin>}}
override fun onDraw(canvas: Canvas) {
    super.onDraw(canvas)

    canvas.save()
    canvas.translate(leftPadding + imageSize / 2, topPadding + imageSize / 2)
    canvas.rotate(-foldDegree)
    canvas.clipRect(-imageSize, -imageSize, imageSize, 0.0f)
    canvas.rotate(foldDegree)
    canvas.translate(-(leftPadding + imageSize / 2), -(topPadding + imageSize / 2))
    canvas.drawBitmap(image, leftPadding, topPadding, paint)
    canvas.restore()

    canvas.translate(leftPadding + imageSize / 2, topPadding + imageSize / 2)
    canvas.rotate(-foldDegree)
    camera.applyToCanvas(canvas)
    canvas.clipRect(-imageSize, 0.0f, imageSize, imageSize)
    canvas.rotate(foldDegree)
    canvas.translate(-(leftPadding + imageSize / 2), -(topPadding + imageSize / 2))
    canvas.drawBitmap(image, leftPadding, topPadding, paint)
}
{{< /highlight >}}
