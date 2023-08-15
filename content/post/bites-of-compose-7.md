+++
author = "Guowei Lv"
categories = ["Jetpack Compose"]
description = "All about side effect"
linktitle = ""
featured = "compose-promo.png"
featuredpath = "/img"
featuredalt = ""
title = "Bites of Compose 7"
date = 2023-08-02T18:24:39+03:00
+++

Let's talk about side effects in Compose.

# Situation 1

What will happen when the button is clicked?

{{< highlight kotlin >}}
@Composable
fun Test() {
    var flag by remember {
        mutableStateOf(false)
    }

    Column {
        Button(onClick = {
            flag = !flag
        }) {
            Text("change")
        }

        Text(flag.toString())

        Log.d("test", "flag is $flag")
    }
}
{{< /highlight >}}

---

### Answer

The `Text` on the screen will change, also there will be a log entry in logcat.

This logging behaviour is something called a `side-effect` in compose world.

In general, it is not recommended to write side-effect directly inside `Composables`, because in each recomposition round,
the code might be executed multiple times or even half a time (canceled half way). So in this case, we might get multiple loggings.

# Situation 2

What will happen if button is clicked?

{{< highlight kotlin >}}
@Composable
fun Test() {
    var flag by remember {
        mutableStateOf(false)
    }

    Column {
        Button(onClick = {
            flag = !flag
        }) {
            Text("change")
        }

        Text(flag.toString())

        SideEffect {
            Log.d("test", "flag is $flag")
        }
    }
}
{{< /highlight >}}

---

### Answer

The same as previous. But it is guaranteed to be executed **once per recomposition**.

# Situation 3

What will happen if button is clicked?

{{< highlight kotlin >}}
@Composable
fun Test() {
    var flag by remember {
        mutableStateOf(false)
    }

    Column {
        Button(onClick = {
            flag = !flag
        }) {
            Text("change")
        }

        if (flag) {
            DisposableEffect(Unit) {
                Log.d("test", "enter screen")
                onDispose {
                    Log.d("test", "leave screen")
                }
            }
            Text("Show me")
        }
    }
}
{{< /highlight >}}

---

### Answer

When `Text` is shown, "enter screen" is logged, and when it is hidden, "leave screen" is logged.

So basically `DisposableEffect` is very similar to `SideEffect`, only difference is that it can also detect when a composable is going off screen.

# Situation 4

What will happen if button is clicked?

{{< highlight kotlin >}}
@Composable
fun Test() {
    var flag by remember {
        mutableStateOf(false)
    }

    Column {
        Button(onClick = {
            flag = !flag
        }) {
            Text("change")
        }

        DisposableEffect(flag) {
            Log.d("test", "enter screen")
            onDispose {
                Log.d("test", "leave screen")
            }
        }
    }
}
{{< /highlight >}}

---

### Answer

"leave screen" is logged first, then "enter screen" will be logged.

Here we are looking at the `key` param, how it works is that when the `key` changed, the `DisposableEffect` will be restarted.
The restart means:

1. The onDispose callback will be first called.
2. The enter screen callback will be called.

This ordering is useful, e.g. if we have some cleanup code inside onDispose, it will guarantee that the cleanup code will always be called first.

# Situation 5

Let's test our understanding a bit by the following example, please try to describe what will be logged when the app is firstly launched and also when the button is clicked.

{{< highlight kotlin >}}
@Composable
fun Test() {
    var flag by remember {
        mutableStateOf(false)
    }

    Column {
        Button(onClick = {
            flag = !flag
        }) {
            Text("change")
        }

        if (flag) {
            Text("show me")
        }

        SideEffect {
            Log.d("test", "enter screen: from SideEffect")
        }

        DisposableEffect(Unit) {
            Log.d("test", "enter screen: from DisposableEffect")
            onDispose {
                Log.d("test", "leave screen: from DisposableEffect")
            }
        }
    }
}
{{< /highlight >}}

---

### Answer

When the app first launched:

> enter screen: from DisposableEffect

> enter screen: from SideEffect

After clicking the button:

> enter screen: from SideEffect

Note that the logs from `DisposableEffect` are not triggered when button is clicked.
