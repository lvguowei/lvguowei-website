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


# Situation 6

What will happen when the app is launched?

{{< highlight kotlin >}}
@Composable
fun Test() {
    var flag by remember {
        mutableStateOf(false)
    }
    Column {
        LaunchedEffect(key1 = Unit) {
            delay(3000)
            flag = !flag
        }
        if (flag) {
            Text("show me")
        }
    }
}
{{< /highlight >}}

---

### Answer

Easy, the `Text` will show after 3 seconds.

Here we used the `LaunchedEffect`, which works similarly to `DisposableEffect` except that it will start a coroutine for the code to run into.

# Situation 7

What will be logged?

{{< highlight kotlin >}}
@Composable
fun Test() {
    var name by remember {
        mutableStateOf("Unknown")
    }

    Button(onClick = { name = "Bob" }) {
        Text("change")
    }
    Column {
        LaunchedEffect(key1 = Unit) {
            delay(3000)
            Log.d("test", name)
        }

    }
}
{{< /highlight >}}

---

### Answer

Well, if nothing is done, then "Unknown" will be logged.
But if you are quick enough to click the button, then "Bob" will be logged.

We also observe here that when `name` got changed, the `LaunchedEffect` block will not be recomposed, this is different
from a normal Composable function. Well, it kind of makes sense, because the code block inside `LaunchedEffect` will be saved later to be executed inside coroutine,
so it is not really UI code that needs to be refreshed immediately.

# Situation 8
Now let's extract out a function for the `LaunchedEffect` part and will things change?

{{< highlight kotlin >}}
@Composable
fun Test() {
    var name by remember {
        mutableStateOf("Unknown")
    }

    Button(onClick = { name = "Bob" }) {
        Text("change")
    }
    CustomLaunchedEffect(name = name)
}

@Composable
private fun CustomLaunchedEffect(name: String) {
    LaunchedEffect(Unit) {
        delay(3000)
        Log.d("test", name)
    }
}
{{< /highlight >}}

---

### Answer

It's not working anymore, even after click the button, "Unknown" is still logged.

Why is that?

Because name is a `State`, but if we pass it to a function it will reduce to a normal string inside the function. After the click, the `CustomLaunchedEffect` will get recomposed, but the `LaunchedEffect` inside it will not (because of the Unit key). So the String object hold by `LaunchedEffect` now is different with the one in the param of `CustomLaunchedEffect`, thus not able to log the correct value.

# Situation 9

Let's see if we can fix this problem, how about now?

{{< highlight kotlin >}}
@Composable
private fun CustomLaunchedEffect(name: String) {
    val remembered = remember(name) {
        mutableStateOf(name)
    }
    LaunchedEffect(Unit) {
        delay(3000)
        Log.d("test", remembered.value)
    }
}
{{< /highlight >}}

---

### Answer

Nope, still not working. But why? We have already created a `State` out of the `name`, this is exactly like when we do not have `CustomLaunchedEffect` yet, right?
Wrong, look careful, we use `remember(name)` here, so what happens is this:

1. When the app launched, `CustomLaunchedEffect` is called with `name = Unknown`.

2. `remember(name)` will create a `State` object with value of "Unknown" and pass it to `LaunchedEffect`.

3. User clicks the `Button`, now `CustomLaunchedEffect` is called with a new value "Bob".

4. Since "Bob" != "Unknown", `remembered` will be assigned a new `State` object with value of "Bob".

5. `LaunchedEffect` will not be reached because it has the same `key = Unit`, hence the `remembered` `State` object it holds is still the old one. See?

# Situation 10

OK, so how to actually fix that?

---

### Answer
So the key to solve this problem is to have the "same object" shared between `CustomLaunchedEffect` and `LaunchedEffect`.

{{< highlight kotlin >}}
@Composable
private fun CustomLaunchedEffect(name: String) {
    val remembered = remember {
        mutableStateOf(name)
    }
    remembered.value = name

    LaunchedEffect(Unit) {
        delay(3000)
        Log.d("test", remembered.value)
    }
}
{{< /highlight >}}

This slightly weird looking code is the proper solution.

Hmm, interesting, this seems to be a unique problem that only `LaunchedEffect` has (and only in the case of inside a function).

Actually, there is a helper function `rememberUpdatedState` that does exactly the same thing. So the above code can be simplified as:

{{< highlight kotlin >}}
@Composable
private fun CustomLaunchedEffect(name: String) {
    val remembered by rememberUpdatedState(newValue = name)
    LaunchedEffect(Unit) {
        delay(3000)
        Log.d("test", remembered)
    }
}
{{< /highlight >}}

