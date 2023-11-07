+++
author = "Guowei Lv"
categories = ["Jetpack Compose"]
description = "Mental model of LayoutModifier"
linktitle = ""
featured = "compose-promo.png"
featuredpath = "/img"
featuredalt = ""
title = "Bites of Compose 13"
date = 2023-11-07T12:43:25+02:00
+++

When we have multiple `LayoutModifiers`, how do we think about them?

In this post, I will introduce a useful mental model.

First we need to understand that `LayoutModifier` determines size and position of a component.

The model is simple, let's say we have the following code:


{{< highlight kotlin >}}
Text(
    text = "Hello",
    modifier = Modifier
        .padding(20.dp)
        .background(Color.Green) // ignore this for now, only for understanding purpose
        .size(50.dp)
)
{{< /highlight >}}

We can think of this as:

> [PaddingModifier(20dp), [SizeModifier(50dp), ComponentInternal]]

The `ComponentInternal` is the original size and position of the `Text("hello")`.

The final size and position will be determined by the following process:
1. `PaddingModifier(20dp)`: whatever comes to me from the right side, I will add a 20dp padding around it.
2. `SizeModifier(50dp)`: whatever comes to me from the right side, I dictate your size to be exactly 50dp.

The end result is:

{{< img-post "/img" "layoutmodifiermental1.png" "" "center" >}}

Now let's look at another example:

{{< highlight kotlin >}}
Text(
    text = "Hello",
    modifier = Modifier
        .size(100.dp)
        .size(50.dp)
        .background(Color.Green)
)
{{< /highlight >}}

This looks weird, has two `SizeModifier`. Then what is the final size???

Based on our mental model:

> [SizeModifier(100dp), [SizeModifier(50dp), TextSize]]

This final size is determined by the following process:

1. `SizeModifier(100dp)`: whatever comes to me from the right side, I dictate your size to be exactly 100dp
2. `SizeModifier(50dp)`: whatever comes to me from the right side, I dictate your size to be exactly 50dp. But I also need to obey the constraints passed to me from my left side (exactly 100dp), the rule is that I always listen to my left side, so the finally result will be 100dp
3.  There is a param called `enforceIncoming`, and for `SizeModifier` it is set to true, which means that the left one will overwrite the right one.

The result will be:

{{< img-post "/img" "layoutmodifiermental2.png" "" "center" >}}

Let's flip things around:

{{< highlight kotlin >}}
Text(
    text = "Hello",
    modifier = Modifier
        .size(50.dp)
        .size(100.dp)
        .background(Color.Green)
)
{{< /highlight >}}

This time the size will be 50dp.

There is actually this `requiredSize` modifier, let's see how that behaves.

{{< highlight kotlin >}}
Text(
    text = "Hello",
    modifier = Modifier
        .size(100.dp)
        .requiredSize(50.dp)
        .background(Color.Green)
)
{{< /highlight >}}

The only difference here is that in `requiredSize` modifier, the `enforceIncoming` is set to false, which means the `left overwrites the right` does not work anymore.

1. `SizeModifier(100dp)`: whatever comes to me from the right side, I dictate your size to be exactly 100dp.
2. `RequiredSizeModifier(50dp)`: whatever comes to me from the right size, I dictate your size to be exactly 50dp, and I will not listen to the constraints passed to me from my left side.

So the end result is:

{{< img-post "/img" "layoutmodifiermental4.png" "" "center" >}}
