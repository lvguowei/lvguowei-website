+++
author = "Guowei Lv"
categories = ["Jetpack Compose"]
description = "Learn Jetpack Compose one bite at a time"
linktitle = ""
featured = "compose-promo.png"
featuredpath = "/img"
featuredalt = ""
title = "Bites of Compose 1"
date = 2023-01-05T22:26:54+02:00
+++

I'm planning to write a series of short articles about Android's Jetpack Compose UI framework.
You can use it to test your understanding or maybe learn a few things along the way.

# Situation 1
Take a look at the Composable below, what will happen if the button is clicked?

{{< highlight kotlin >}}
@Composable
fun Situation1() {
    var name = "Guowei"

    Column {
        Text(name)
        Button(onClick = { name = "Hello" }) {
            Text("Change name!")
        }
    }
}
{{< /highlight >}}

---

### Answer

> Nothing. The `name` is a plain Kotlin variable, changing it has no effect in Compose world.

# Situation 2

A slight change of the previous case, what will happen if the button is clicked this time?

{{< highlight kotlin >}}
@Composable
fun Situation2() {
    val name = mutableStateOf("Guowei")

    Column {
        Text(name.value)
        Button(onClick = { name.value = "Hello" }) {
            Text("Change name!")
        }
    }
}
{{< /highlight >}}

---

### Answer

> Nothing! Let's go through what happens when the button is clicked.
>
> The State `name`'s value will be set to "hello", and this will trigger a recompose of the view.
>
> So the `Situation2()` function will be executed again, and `name` will be again assigned a new State with the inital value of "Guowei".
>
> Hence, nothing changes.


# Situation 3

What will happen if the button is clicked ?

{{< highlight kotlin >}}
@Composable
fun Situation3() {
    val name = remember { mutableStateOf("Guowei") }

    Column {
        Text(name.value)
        Button(onClick = { name.value = "Hello" }) {
            Text("Change name!")
        }
    }
}
{{< /highlight >}}

---

### Answer
>The text will be changed from "Guowei" to "Hello".
>
> Notice the State `name` is wrapped around a `remember` function this time.
>So when the recomposition happens, the `name` will not be initialized with a new object, but the previous object will be returned instead.
>
>So no matter how many times `Situation()` is executed, the name will always be the same object. 



# Situation 4

What will happen when the button is clicked?

{{< highlight kotlin >}}

@Composable
fun Situation4() {
    var name by remember { mutableStateOf("Guowei") }

    Column {
        Text(name)
        Button(onClick = { name = "Hello" }) {
            Text("Change name!")
        }
    }
}
{{< /highlight >}}

---

### Answer

This is the same with Situation 3, but used the `by` keyword to simplify things a bit.

Actually, the reason that we can use `by` here, is because of these two ext functions from `SnapshotState.kt`:

{{< highlight kotlin >}}

/**
 * Permits property delegation of `val`s using `by` for [State].
 *
 * @sample androidx.compose.runtime.samples.DelegatedReadOnlyStateSample
 */
@Suppress("NOTHING_TO_INLINE")
inline operator fun <T> State<T>.getValue(thisObj: Any?, property: KProperty<*>): T = value
{{< /highlight >}}

{{< highlight kotlin >}}

/**
 * Permits property delegation of `var`s using `by` for [MutableState].
 *
 * @sample androidx.compose.runtime.samples.DelegatedStateSample
 */
@Suppress("NOTHING_TO_INLINE")
inline operator fun <T> MutableState<T>.setValue(thisObj: Any?, property: KProperty<*>, value: T) {
    this.value = value
}
{{< /highlight >}}
