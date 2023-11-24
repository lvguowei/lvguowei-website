+++
author = "Guowei Lv"
categories = ["Jetpack Compose"]
description = "Animatable"
linktitle = ""
featured = "compose-promo.png"
featuredpath = "/img"
featuredalt = ""
title = "Bites of Compose 16"
date = 2023-11-24T21:24:41+02:00
+++

In previous post we talked about how to do animation in Compose using `animateXXXAsState()` function. And also see that though easy to use, it has limited customization capabilities.

Now, let's take a look at a lower level(and more powerful) way: `Animatable`.

{{< highlight kotlin >}}
@Composable
fun AnimatableDemo() {
    val coroutineScope = rememberCoroutineScope()
    val anim = remember {
        Animatable(48.dp, Dp.VectorConverter)
    }

    Box(
        modifier = Modifier
            .size(anim.value)
            .background(Color.Green)
            .clickable {
                coroutineScope.launch {
                    val current = anim.value
                    anim.snapTo(current + 40.dp)
                    anim.animateTo(current + 20.dp)
                }
            }
    )
}
{{< /highlight >}}

{{< img-post "/img" "animatable.gif" "" "center" >}}

As we can see, we are able to control the animation more freely.

Actually, `animateXXXAsState()` is using `Animatable` under the hood!
