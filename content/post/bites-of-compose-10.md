+++
author = "Guowei Lv"
categories = ["Jetpack Compose"]
description = "Layout()"
linktitle = ""
featured = "compose-promo.png"
featuredpath = "/img"
featuredalt = ""
title = "Bites of Compose 10"
date = 2023-08-24T14:51:21+03:00
+++

Let's see an example of how to do custom layout in compose using `Layout()`.

{{< highlight kotlin >}}
@Composable
fun CustomLayout(modifier: Modifier = Modifier, content: @Composable () -> Unit) {
    Layout(modifier = modifier, content = content) { measurables, constraints ->
        var width = 0
        var height = 0
        val offset = 10.dp.toPx().roundToInt()

        val placeables = measurables.map { measurable ->
            measurable.measure(constraints).also { placeable ->
                width = max(placeable.width, width)
                height += placeable.height
            }
        }

        layout(width = width + offset * placeables.size, height = height) {
            var totalHeight = 0
            var totalOffset = 0
            placeables.forEach {
                it.placeRelative(totalOffset, totalHeight)
                totalHeight += it.height
                totalOffset += offset
            }
        }
    }
}
{{< /highlight >}}

And here is how to use it:

{{< highlight kotlin >}}
@Preview(showBackground = true)
@Composable
fun CustomLayoutPreview() {
    CustomLayout {
        Box(
            Modifier
                .size(50.dp)
                .background(Color.Blue)
        )
        Box(
            Modifier
                .size(50.dp)
                .background(Color.Yellow)
        )
        Box(
            Modifier
                .size(50.dp)
                .background(Color.Red)
        )
        Box(
            Modifier
                .size(50.dp)
                .background(Color.Green)
        )
    }
}
{{< /highlight >}}

{{< img-post "/img" "compose-layout.png" "" "center" >}}
