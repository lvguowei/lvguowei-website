+++
author = "Guowei Lv"
categories = ["Jetpack Compose"]
description = "rememberCoroutineScope"
linktitle = ""
featured = "compose-promo.png"
featuredpath = "/img"
featuredalt = ""
title = "Bites of Compose 8"
date = 2023-08-16T09:35:52+03:00
+++

Today's topic is `rememberCoroutineScope`.

# Situation 1

How to launch a `Coroutine` in Compose?

Can we "just do it" ?

{{< highlight kotlin >}}
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            lifecycleScope.launch {  }
        }
    }
}
{{< /highlight >}}

---

### Answer

Apparently, no. Android studio gives an error saying
>"Calls to launch should happen inside a LaunchedEffect and not composition".

OK, now we know that we shouldn't do it and how to fix it by using `LaunchedEffect`.

But why? what can go wrong?

Well, basically 2 things:

1. The `lifecycleScope` we used here is not matching the `Composable`'s life cycle.
The `Coroutine` launched from a `Composable` should match the life cycle of the `Composable`, meaning when the `Composable` is removed from screen, the launched coroutine should also be canceled. So ideally each `Composable` should have its own `CoroutineScope` and we should use that one instead of the `lifecycleScope` from `Activity` or `Fragment`.

2. Since `Composable` functions can be executed multiple times (so called recomposition), the coroutine will be launched multiple times too, that's clearly not what we want. We want the launched Coroutine to at least be able to live across recompositions.

`LaunchedEffect` solves exactly the above two problems.

# Situation 2

What if we only need one of the above 2 conditions? E.g. we only need the `Composable`'s `CoroutinScope`, but the launching of the coroutine is not happening inside the `Composable`'s composing process?

Let's take a look at the following example:

{{< highlight kotlin >}}
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            val scope = rememberCoroutineScope()
            Button(onClick = {
                scope.launch { }
            }) {
                Text("")
            }
        }
    }
}
{{< /highlight >}}

The launching of the Coroutine is inside the `onClick` callback of the `Button`.

OK, That's all about `rememberCoroutineScope`.
