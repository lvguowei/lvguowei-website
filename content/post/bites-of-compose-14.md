+++
author = "Guowei Lv"
categories = ["Jetpack Compose"]
description = "Mental model of LayoutModifier + DrawModifier"
linktitle = ""
featured = "compose-promo.png"
featuredpath = "/img"
featuredalt = ""
title = "Bites of Compose 14"
date = 2023-11-20T20:33:01+02:00
+++

In previous post, we discussed the mental model of multiple `LayoutModifiers`.
Now let's talk about the mental model of the combination of `LayoutModifier` and `DrawModifier`.

First of all, we need to know that everything on screen is drawn using some `DrawModifier`: `Text`, `Image`, `Background`, etc.

Let me give you the mental model directly.

Given the following code:
{{< highlight kotlin >}}
Modifier.drawModifier1().drawModifier2().drawModifier3().layoutModifier1()
  .drawModifier4().drawModifier5().layoutModifier2()
  .drawModifier6().layoutModifier3()
{{< /highlight >}}

The model is:
{{< img-post "/img" "drawmodifiermodel.png" "" "center" >}}

Notice that the modifiers are always attached to its nearest `LayoutModifier` on the right.
The drawing order is indicated by the arrows.
And the "chain" is relying on the calling of `drawContent()` within the `DrawModifier`.

Example:

{{< highlight kotlin >}}
@Composable
fun DrawModifierAndLayoutModifier() {
    Box(
        modifier = Modifier
            .drawWithContent {
                drawRect(
                    color = Color.Green,
                )
                drawContent()
            }
            .drawWithContent {
                drawRect(
                    color = Color.Red,
                    topLeft = Offset(20.dp.toPx(), 20.dp.toPx()),
                )
                drawContent()
            }
            .drawWithContent {
                drawRect(
                    color = Color.Blue,
                    topLeft = Offset(40.dp.toPx(), 40.dp.toPx()),
                )
                drawContent()
            }
            .drawWithContent {
                drawRect(
                    color = Color.Black,
                    topLeft = Offset(60.dp.toPx(), 60.dp.toPx()),
                )
                drawContent()
            }
            .requiredSize(200.dp)
            .drawWithContent {
                drawRect(
                    color = Color.Yellow,
                )
                drawContent()
            }
            .drawWithContent {
                drawRect(
                    color = Color.Cyan,
                    topLeft = Offset(10.dp.toPx(), 10.dp.toPx()),
                )
                drawContent()
            }
            .drawWithContent {
                drawRect(
                    color = Color.Magenta,
                    topLeft = Offset(20.dp.toPx(), 20.dp.toPx()),
                )
                drawContent()
            }
            .drawWithContent {
                drawRect(
                    color = Color.Gray,
                    topLeft = Offset(30.dp.toPx(), 30.dp.toPx()),
                )
                drawContent()
            }
            .requiredSize(40.dp)
    )
}

{{< /highlight >}}

{{< img-post "/img" "drawmodifiermodel2.png" "" "center" >}}

