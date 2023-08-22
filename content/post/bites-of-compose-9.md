+++
author = "Guowei Lv"
categories = ["Jetpack Compose"]
description = "using native Canvas in Compose"
linktitle = ""
featured = "compose-promo.png"
featuredpath = "/img"
featuredalt = ""
title = "Bites of Compose 9"
date = 2023-08-22T13:13:13+03:00
+++

Let's see an example of using the Android's native `Canvas` in `Compose`.

One of the things that is impossible to do in Compose is doing 3D rotation.

Let's see how to get hold of the native `Canvas` and do it the old way.

{{< img-post "/img" "3d-rotate-compose.gif" "" "center" >}}

{{< highlight kotlin >}}
@Composable
fun MyView() {
    val image = ImageBitmap.imageResource(R.drawable.avatar)
    val paint by remember {
        mutableStateOf(Paint())
    }
    val animatable = remember {
        Animatable(0f)
    }
    val camera by remember {
        mutableStateOf(Camera())
    }

    LaunchedEffect(Unit) {
        animatable.animateTo(360f, infiniteRepeatable(tween(2000)))
    }

    Box(modifier = Modifier.padding(50.dp)) {
        Canvas(modifier = Modifier.size(100.dp)) {
            drawIntoCanvas {
                it.translate(size.width / 2, size.height / 2)
                it.rotate(-45f)
                camera.save()
                camera.rotateX(animatable.value)
                camera.applyToCanvas(it.nativeCanvas)
                camera.restore()
                it.rotate(45f)
                it.translate(-size.width / 2, -size.height / 2)
                it.drawImageRect(
                    image,
                    dstSize = IntSize(size.width.roundToInt(), size.height.roundToInt()),
                    paint = paint
                )
            }
        }

    }
}
{{< /highlight >}}
