+++
author = "Guowei Lv"
categories = ["Jetpack Compose"]
description = "Use LayoutModifier to implement padding"
linktitle = ""
featured = "compose-promo.png"
featuredpath = "/img"
featuredalt = ""
title = "Bites of Compose 12"
date = 2023-11-03T21:56:21+02:00
+++

`LayoutModifier` is a very important type of `Modifier` in Compose.
It can decorate its component's layout. So let's get an idea of what this means by
implementing padding using it.

Let's try to add a 10dp padding around a `Text`.

{{< highlight kotlin >}}

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        setContent {
            LayoutModifierPadding()
        }
    }
}

@Composable
fun LayoutModifierPadding() {
    Box(
        modifier = Modifier
            .size(100.dp)
            .background(Color.LightGray)
    ) {
        Text(
            "Hello!",
            modifier = Modifier
                .background(Color.Yellow)
                .layout { measurable, constraints ->
                    Log.d("test", "The parent Box wants the Text to be:")
                    Log.d(
                        "test",
                        "maxWidth: ${constraints.maxWidth}, maxHeight: ${constraints.maxHeight}"
                    )
                    val padding = 10.dp.roundToPx()

                    val newMaxWidth = constraints.maxWidth - padding * 2
                    val newMaxHeight = constraints.maxHeight - padding * 2

                    Log.d(
                        "test",
                        "But since we are trying to fit in a padding around it, the Text needs to be within:"
                    )
                    Log.d("test", "maxWidth: $newMaxWidth, maxHeight: $newMaxHeight")

                    val newConstraints = constraints.copy(
                        maxWidth = newMaxWidth,
                        maxHeight = newMaxHeight
                    )
                    Log.d("test", "Measure the Text with this new Constraint:")
                    val placeable = measurable.measure(newConstraints)
                    Log.d(
                        "test",
                        "The width and the height of the actual Text are: ${placeable.width}, ${placeable.height}"
                    )
                    val layoutWidth = placeable.width + padding * 2
                    val layoutHeight = placeable.height + padding * 2
                    Log.d(
                        "test",
                        "We then need to add the padding to them to get the final width and height of the new layout created by this modifier: $layoutWidth, $layoutHeight"
                    )

                    layout(layoutWidth, layoutHeight) {
                        Log.d(
                            "test",
                            "We then need to place the actual Text in the center shifted by padding from left and top"
                        )
                        placeable.placeRelative(padding, padding)
                    }
                }
                .background(Color.Green))

    }
}

{{< /highlight >}}

The code is heavily commented and the logs are explaining everything.

{{< highlight kotlin >}}
The parent Box wants the Text to be:
maxWidth: 150, maxHeight: 150

But since we are trying to fit in a padding around it, the Text needs to be within:
maxWidth: 120, maxHeight: 120

Measure the Text with this new Constraint:
The width and the height of the actual Text are: 53, 29

We then need to add the padding to them to get the final width and height of the new layout created by this modifier: 83, 59

We then need to place the actual Text in the center shifted by padding from left and top

{{< /highlight >}}

{{< img-post "/img" "modifierlayoutpadding.png" "" "center" >}}
