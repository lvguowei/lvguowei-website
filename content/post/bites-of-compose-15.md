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


What's happening under the hood is that every time we change the value of `target` , a coroutine will get started to change the value of the `State` produced by calling `animateDpAsState()`.


It's all very simple and easy. But there is one limitation, you cannot set the animation's start value. I mean if the `Box` is 48.dp now, and I want to start the animation from 96.dp to 100.dp, there is no way to do it with `animateXXXAsState()` set of functions. We need something else, stay tuned for next post!

