+++
author = "Guowei Lv"
categories = ["Jetpack Compose"]
description = "animateXXXAsState()"
linktitle = ""
featured = "compose-promo.png"
featuredpath = "/img"
featuredalt = ""
title = "Bites of Compose 15"
date = 2023-11-23T10:52:09+02:00
+++

Let's take a look at the simplest way to implement animation in Compose, which is using `animateXXXAsState()` set of functions.

Let's say we want to increase the size of a `Box` by `10.dp` every time we click on it.

We already know how to do it without animation:

{{< highlight kotlin >}}
@Preview
@Composable
fun AnimationDemo() {
    var target by remember {
        mutableStateOf(48.dp)
    }

    Box(
        modifier = Modifier
            .size(target)
            .background(Color.Green)
            .clickable { target += 10.dp }
    )
}

{{< /highlight >}}

To add in some animation, we need to create another `animatedTarget` that will change over time.

{{< highlight kotlin >}}

@Preview
@Composable
fun AnimationDemo() {
    var target by remember {
        mutableStateOf(48.dp)
    }
    val animatedTarget by animateDpAsState(target, label = "")

    Box(
        modifier = Modifier
            .size(animatedTarget)
            .background(Color.Green)
            .clickable { target += 10.dp }
    )
}
{{< /highlight >}}

We change the `target` in `onClick`, but use the `animatedTarget` to set the size of the `Box`.
