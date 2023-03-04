+++
author = "Guowei Lv"
categories = ["Jetpack Compose"]
description = "Learn Jetpack Compose one bite at a time"
linktitle = ""
featured = "compose-promo.png"
featuredpath = "/img"
featuredalt = ""
title = "Bites of Compose 5"
date = 2023-03-03T20:19:56+02:00
+++

Let's talk about Modifier.

# Situation 1

What is the simplest Modifier?

---

# Answer

{{< highlight kotlin >}}
Modifier
{{< /highlight >}}

What is it? a class? an object?

It is a *companion object*, which implements the `Modifier` interface.

{{< highlight kotlin >}}
companion object : Modifier {
    override fun <R> foldIn(initial: R, operation: (R, Element) -> R): R = initial
    override fun <R> foldOut(initial: R, operation: (Element, R) -> R): R = initial
    override fun any(predicate: (Element) -> Boolean): Boolean = false
    override fun all(predicate: (Element) -> Boolean): Boolean = true
    override infix fun then(other: Modifier): Modifier = other
    override fun toString() = "Modifier"
}
{{< /highlight >}}

The formal version is `Modifier.Companion`, but can be shortened as `Modifier`.

I know, this can be confusing, but let's write a simpler example to understand it step by step.

First we need to understand that the so called `companion object`, is just a kind of global object attached to a class or interface itself, this is similar to the static members of a class in Java.

For example:
{{< highlight kotlin >}}
class Person {
    companion object {
        const val TAG = "person"
        fun talk() = print("I'm talking...")
    }
}

{{< /highlight >}}

The "Person class" has a `TAG` property and a "static function" `talk()`. And to access this companion object we can just write `Person.Companion` or simply `Person`.

{{< highlight kotlin >}}
Person.TAG
Person.talk()
Person.Companion.TAG
Person.Companion.talk()
{{< /highlight >}}

Also, the companion object can implement interface.

{{< highlight kotlin >}}
interface Runnable {
    fun run()
}

class Person {
    companion object : Runnable {
        const val TAG = "person"
        fun talk() = print("I'm talking...")
        override fun run() {
            print("Running...")
        }
    }
}

Person.run()
{{< /highlight >}}

And not only classes, but interfaces can also have companion object. And if its companion object implements itself...

{{< highlight kotlin >}}
interface Car {
    fun drive()
    fun stop()

    companion object : Car {
        override fun drive() {
            print("drive")
        }

        override fun stop() {
            print("stop")
        }
    }
}
{{< /highlight >}}

This is exactly how Modifer is doing.

# Situation 2
What does this mean?

{{< highlight kotlin >}}
Box(modifer: Modifier = Modifier)
{{< /highlight >}}

---

# Answer
Passing in the most basic `Modifier` object as default parameter.

# Situation 3
Is this companion object `Modifier` used anywhere else?

---

# Answer

Yes, it is also used when you do something like:

{{< highlight kotlin >}}
Modifier.padding(4.dp)
Modifier.background(Color.Red)
...
{{< /highlight >}}

Let's look into the source code:

{{< highlight kotlin >}}
fun Modifier.background(
    color: Color,
    shape: Shape = RectangleShape
) = this.then(
    Background(
        color = color,
        shape = shape,
        inspectorInfo = debugInspectorInfo {
            name = "background"
            value = color
            properties["color"] = color
            properties["shape"] = shape
        }
    )
)
{{< /highlight >}}

As you can see, `background()` is an extension function of the companion object `Modifier` (not the interface `Modifier`!).
