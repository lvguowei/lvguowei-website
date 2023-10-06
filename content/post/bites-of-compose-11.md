+++
author = "Guowei Lv"
categories = ["Jetpack Compose"]
description = "LookAheadLayout"
linktitle = ""
featured = "compose-promo.png"
featuredpath = "/img"
featuredalt = ""
title = "Bites of Compose 11"
date = 2023-10-06T12:59:22+03:00
+++

Let's see an example of how to use `LookAheadLayout` to implement shared view transition animation.

Here is the code:

{{< highlight kotlin >}}
@Composable
fun Avatar(modifier: Modifier = Modifier) {
    Box(
        modifier = modifier
            .size(100.dp)
            .background(Color.Green)
    )
}

@OptIn(ExperimentalComposeUiApi::class)
@Composable
fun CustomLookAheadLayout3() {
    var flag by remember {
        mutableStateOf(false)
    }

    var lookaheadOffset by remember { mutableStateOf(Offset.Zero) }
    val lookaheadOffsetAnim by animateOffsetAsState(
        targetValue = lookaheadOffset,
        animationSpec = TweenSpec(durationMillis = 500),
        label = ""
    )

    val sharedView = remember {
        movableContentOf {
            Avatar(
                modifier = Modifier
                    .intermediateLayout { measurable, constraints ->
                        val placeable = measurable.measure(constraints)
                        layout(placeable.width, placeable.height) {
                            lookaheadOffset = coordinates?.let {
                                lookaheadScopeCoordinates.localLookaheadPositionOf(it)
                            } ?: Offset.Zero
                            placeable.placeRelative(
                                (lookaheadOffsetAnim - lookaheadOffset).x.roundToInt(),
                                (lookaheadOffsetAnim - lookaheadOffset).y.roundToInt()
                            )
                        }
                    }
                    .clickable {
                        flag = !flag
                    }

            )
        }
    }
    Box(modifier = Modifier.fillMaxSize()) {
        LookaheadScope {
            if (flag) {
                Column(horizontalAlignment = Alignment.CenterHorizontally) {
                    Spacer(modifier = Modifier.height(200.dp))
                    sharedView()
                    repeat(5) {
                        Spacer(modifier = Modifier.height(20.dp))
                        Box(
                            modifier = Modifier
                                .width(400.dp)
                                .height(20.dp)
                                .background(Color.Gray)
                        )

                    }
                }
            } else {
                sharedView()
            }
        }
    }
}
{{< /highlight >}}

{{< img-post "/img" "lookaheadlayout.gif" "" "center" >}}

I'm too lazy to write the explanation, ask me if you want me to ;-)

