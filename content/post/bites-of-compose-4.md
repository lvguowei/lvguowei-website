+++
author = "Guowei Lv"
categories = ["Jetpack Compose"]
description = "Learn Jetpack Compose one bite at a time"
linktitle = ""
featured = "compose-promo.png"
featuredpath = "/img"
featuredalt = ""
title = "Bites of Compose 4"
date = 2023-02-01T21:21:32+02:00
+++

This time we are focusing on `derivedStateOf` and how it is different from `remember`.

Let's look at this simple example:

# Situation 1
What will happen when user clicks on the `Text`?

{{< highlight kotlin >}}
@Composable
private fun Situation1() {
    var name by remember {
        mutableStateOf("guowei")
    }
     val uppercase by remember {
        derivedStateOf { name.uppercase() }
    }
    Text(uppercase, modifier = Modifier.clickable { name = "hello" })
}
{{< /highlight >}}

---

# Answer
>The text will change from "GUOWEI" to "HELLO".

>This is because the `uppercase` state is listening to changes of another state `name`. So when `name` changes, `uppercase` will also change accordingly.
>This is the basic usage of `derivedStateOf`.

# Situation 2

But can we achieve the same behaviour without using `derivedStateOf`? Take a look at the following code, what will happen if the user clicks on the text?

{{< highlight kotlin >}}
@Composable
private fun Situation2() {
    var name by remember {
        mutableStateOf("guowei")
    }

    val uppercase = remember(name) {
        name.uppercase()
    }

    Text(uppercase, modifier = Modifier.clickable { name = "hello" })
}
{{< /highlight >}}

---

# Answer

>Text will change to "HELLO", the same as in Situation1.
>Hmm, does this mean `derivedStateOf` and `remember()` are interchangeable?
>Let's explore more.

# Situation 3
Let's change from `name` to `names`. What will happen if the user clicks on the names Column?

{{< highlight kotlin >}}
@Composable
private fun Situation3() {
    val names = remember {
        mutableStateListOf("Bob", "Jane")
    }

    val uppercaseNames by remember {
        derivedStateOf { names.map { it.uppercase() } }

    }

    Column(modifier = Modifier.clickable { names.add("hello") }) {
        uppercaseNames.forEach {
            Text(it)
        }
    }
}
{{< /highlight >}}

---

# Answer

>There will be a "HELLO" appearing at the end of the Column. So basically `derivedStateOf` still works fine.

# Situation 4
Let's now see if the `remember` version still works.
What happens if the user clicks the Column?

{{< highlight kotlin >}}
@Composable
fun Situation4() {
    val names = remember {
        mutableStateListOf("Bob", "Jane")
    }

    val uppercaseNames = remember(names) {
        names.map { it.uppercase() }
    }

    Column(modifier = Modifier.clickable { names.add("hello") }) {
        uppercaseNames.forEach {
            Text(it)
        }
    }
}
{{< /highlight >}}

---

# Answer

> Nothing! But, why?
> Clicking the Column will add the new "hello" into `names`, no problem with that. But since `names` is pointing the same object in memory,
> `remember(names)` will think nothing has changed, it compares `names` to itself.(well, if instead of adding a new element we assign a new object to `names`, then that is another story).
> So if you use `remember` on `Int` or `String`, there is no problem, because the only way to change them is to assign a new value. But with `List` or `Map`, there is a problem, `remember` only works if you assign a new value, but NOT when the object itself changed from imside. OK, now seems that `derivedStateOf` can apply to more cases than `remember`, but is there a case where only `remember` will work? Let's see the next example.

# Situation 5
What will happen if user clicks on the text?

{{< highlight kotlin >}}
@Composable
fun Situation5() {
    var name by remember {
        mutableStateOf("guowei")
    }
    UpperCasedName(name) { name = "hello" }
}

@Composable
fun UpperCasedName(name: String, onClick: () -> Unit) {
    val upperCased = remember(name) { name.uppercase() }
    Text(upperCased, modifier = Modifier.clickable { onClick() })
}
{{< /highlight >}}

---

# Answer
> The text will change to "hello".
> Let's go through what has happened in this case:
> 1. `onClick()` callback is called.
> 2. `name = "hello"` executed.
> 3. Trigger recompose.
> 4. `UpperCasedName("hello")` is called.
> 5. Inside `UpperCasedName()`, `remember(name)` sees that name has changed from "guowei" to "hello", so the code inside it will be executed again, `upperCased` will be "HELLO".
> 6. Recompose of `Text`.

# Situation 6

Now, let's see the `derivedStateOf` version will also work. What will happen if user clicks the text this time?

{{< highlight kotlin >}}
@Composable
fun Situation6() {
    var name by remember {
        mutableStateOf("guowei")
    }
    UpperCasedName(name) { name = "hello" }
}

@Composable
fun UpperCasedName(name: String, onClick: () -> Unit) {
    val upperCased by remember {
        derivedStateOf { name.uppercase() }
    }
    Text(upperCased, modifier = Modifier.clickable { onClick() })
}
{{< /highlight >}}

---

# Answer
> Nothing will happen. Let's go through what has happened here also.

> 1 - 4: The same as in Situation 5.

> 5. Since `name` is just a string, and the `remember` inside `UpperCasedName` has no parameter, so `upperCased` will get the remembered value. So nothing will change in this case.

> Seems that the `observe chain` is cut off by passing the `name` as a parameter into another function. This is because we used `by`,and `name` is a deligated variable. So on the calling side this is the same as `UpperCasedName("hello") { name = "hello" }`.
> OK, then is there a way to keep the observe chain inside the other function? what if we pass the state object itself?

# Situation 7

What will happen if user click the text?

{{< highlight kotlin >}}
@Composable
fun Situation7() {
    val name = remember {
        mutableStateOf("guowei")
    }
    UpperCasedName(name) { name.value = "hello" }
}

@Composable
fun UpperCasedName(name: State<String>, onClick: () -> Unit) {
    val upperCased by remember {
        derivedStateOf { name.value.uppercase() }
    }
    Text(upperCased, modifier = Modifier.clickable { onClick() })
}
{{< /highlight >}}

---

# Answer
> "GUOWEI" will change to "HELLO".
> This time the State object is passed to the other function, so the obervation is still preserved.
> But, we normally wouldn't use this way. Because it is too restricting to only allow a State of String to be passed to the function. So the reusability is poor.

# Situation 8

Let's change things a bit again, what will happen if user clicks on the Column?

{{< highlight kotlin >}}
@Composable
fun Situation8() {
    val names = remember {
        mutableStateListOf("Bob", "Jane")
    }
    UpperCasedName(names) { names.add("hello") }
}

@Composable
fun UpperCasedName(names: List<String>, onClick: () -> Unit) {
    val upperCaseNames by remember {
        derivedStateOf { names.map { it.uppercase() } }
    }
    Column(modifier = Modifier.clickable { onClick() }) {
        upperCaseNames.forEach {
            Text(it)
        }
    }
}
{{< /highlight >}}

---

# Answer
> "HELLO" will be appended to the list of names.
> The key to understand this is to understand what is passed to the function `UpperCasedName`.
> `names` is a `MutableList` object, and because `MutableList<E> : List<E>`, so it can be directly passed to the `UpperCasedName()` function. So even though the function's parameter is defined as `List<String>`, we can actually pass in a state object like `MutableList<String>`.

# Situation 9

Let's again change things a little and see if things are still working.

{{< highlight kotlin >}}
@Composable
fun Situation9() {
    var names by remember {
        mutableStateOf(listOf("Bob", "Jane"))
    }
    UpperCasedName(names) { names = mutableListOf("hello", "kitty") }
}

@Composable
fun UpperCasedName(names: List<String>, onClick: () -> Unit) {
    val upperCaseNames by remember {
        derivedStateOf { names.map { it.uppercase() } }
    }
    Column(modifier = Modifier.clickable { onClick() }) {
        upperCaseNames.forEach {
            Text(it)
        }
    }
}
{{< /highlight >}}

---

# Answer
> Nothing will change.
> Till now, can you figure out why and how to fix?
> OK, the fix is to use both parameterized remember and derivedStateOf together.

{{< highlight kotlin >}}
@Composable
fun UpperCasedName(names: List<String>, onClick: () -> Unit) {
    val upperCaseNames by remember(names) {
        derivedStateOf { names.map { it.uppercase() } }
    }
    Column(modifier = Modifier.clickable { onClick() }) {
        upperCaseNames.forEach {
            Text(it)
        }
    }
}
{{< /highlight >}}

> So this covers both cases:
> 1. If the `names` changes to a totally different object, the parameterized `remember` will cover it.
> 2. If the `names` is a State object and its internal elements change, then the `derivedStateOf` will cover it.

