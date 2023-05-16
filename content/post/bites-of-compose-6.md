+++
author = "Guowei Lv"
categories = ["Jetpack Compose"]
description = "What is Modifier after all?"
linktitle = ""
featured = "compose-promo.png"
featuredpath = "/img"
featuredalt = ""
title = "Bites of Compose 6"
date = 2023-05-15T21:58:15+03:00
+++

In previous post we talked about the companion object `Modifier` and how it's implementing the interface `Modifier`.
Now it is time to go deeper and see Modifier's internals.

# Situation 1

What is Modifier after all?

---

# Answer

Well, what if I tell you `Modifier` is just a binary tree data structure.

Let's dive into the source code to prove this.

First let's take a look at the class hierachy, we have 3 pieces: `Modifier(interface)`, `Element` and `CombinedModifier`.

`Element` is like a leaf node.

`CombinedModifier` is like a parent node that contains two children nodes: left(outer) and right(inner).

{{< figure src="/img/modifiers.png" >}}

The code below is take straight from the `Modifier` source code, and I put in some contrived usecases to help you understand things better.

{{< highlight kotlin >}}
fun main(args: Array<String>) {
    // outer ----------> inner
    //val mod = MyModifier(1) then MyModifier(2) then MyModifier(3)
    val mod = CombinedModifier(MyModifier(1), CombinedModifier(MyModifier(2), MyModifier(3)))
    println(mod)
    println("------------------------")

    val allLessThanFive = mod.all { e ->
        val n = (e as MyModifier).n
        n < 5
    }
    println("All less than 5? $allLessThanFive")
    println("------------------------")

    val anyBiggerThanFour = mod.any { e ->
        val n = (e as MyModifier).n
        n > 4
    }
    println("Any bigger than 4? $anyBiggerThanFour")
    println("------------------------")

    val sum = mod.foldIn(0) { acc, e ->
        val n = (e as MyModifier).n
        println("...adding $n")
        acc + n
    }
    println("Sum is $sum")
    println("------------------------")

    val sum2 = mod.foldOut(0) { e, acc ->
        val n = (e as MyModifier).n
        println("...adding $n")
        acc + n
    }
    println("Sum is $sum2")

}

interface Modifier {

    fun <R> foldIn(initial: R, operation: (R, Element) -> R): R

    fun <R> foldOut(initial: R, operation: (Element, R) -> R): R

    fun any(predicate: (Element) -> Boolean): Boolean

    fun all(predicate: (Element) -> Boolean): Boolean

    infix fun then(other: Modifier): Modifier =
        if (other === Modifier) this else CombinedModifier(this, other)

    interface Element : Modifier {
        override fun <R> foldIn(initial: R, operation: (R, Element) -> R): R =
            operation(initial, this)

        override fun <R> foldOut(initial: R, operation: (Element, R) -> R): R =
            operation(this, initial)

        override fun any(predicate: (Element) -> Boolean): Boolean = predicate(this)

        override fun all(predicate: (Element) -> Boolean): Boolean = predicate(this)
    }

    companion object : Modifier {
        override fun <R> foldIn(initial: R, operation: (R, Element) -> R): R = initial
        override fun <R> foldOut(initial: R, operation: (Element, R) -> R): R = initial
        override fun any(predicate: (Element) -> Boolean): Boolean = false
        override fun all(predicate: (Element) -> Boolean): Boolean = true
        override infix fun then(other: Modifier): Modifier = other
        override fun toString() = "Modifier"
    }
}

class CombinedModifier(
    private val outer: Modifier,
    private val inner: Modifier
) : Modifier {
    override fun <R> foldIn(initial: R, operation: (R, Modifier.Element) -> R): R =
        inner.foldIn(outer.foldIn(initial, operation), operation)

    override fun <R> foldOut(initial: R, operation: (Modifier.Element, R) -> R): R =
        outer.foldOut(inner.foldOut(initial, operation), operation)

    override fun any(predicate: (Modifier.Element) -> Boolean): Boolean =
        outer.any(predicate) || inner.any(predicate)

    override fun all(predicate: (Modifier.Element) -> Boolean): Boolean =
        outer.all(predicate) && inner.all(predicate)

    override fun toString() = "[" + foldIn("") { acc, element ->
        if (acc.isEmpty()) element.toString() else "$acc, $element"
    } + "]"
}

class MyModifier(val n: Int) : Modifier.Element
{{< /highlight >}}

And here is the result of running the code above:

{{< highlight kotlin >}}
[MyModifier@1a407d53, MyModifier@3d8c7aca, MyModifier@5ebec15]
------------------------
All less than 5? true
------------------------
Any bigger than 4? false
------------------------
...adding 1
...adding 2
...adding 3
Sum is 6
------------------------
...adding 3
...adding 2
...adding 1
Sum is 6

{{< /highlight >}}


