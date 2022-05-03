+++
author = "Guowei Lv"
categories = ["Functional Programming"]
description = "Stream, Lazy, Map, Filter, EVAL"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Separating Program Evaluation From Description"
date = 2022-05-03T20:18:32+03:00
+++

> 5.3 Separating program evaluation from description

This is the title of Chapter 5.3 from the book [Functional Programming in Kotlin](https://www.manning.com/books/functional-programming-in-kotlin).

{{< figure src="/img/fpik.png" >}}

Everyone knows that a program is a list of instructions that will be executed/evaluated in the order they are written in.

{{< highlight kotlin >}}
fun exec() {
  doThis()
  doThat()
  doMore()
}
{{< /highlight >}}

So the description decides the evaluation. What does it mean to separate them? I mean, can they be different?

Let's define a lazy `Stream`:

{{< highlight kotlin >}}
sealed class Stream<out A>

data class Cons<out A>(
    val head: () -> A,
    val tail: () -> Stream<A>
) : Stream<A>()

object Empty : Stream<Nothing>()
{{< /highlight >}}

Here are some helper functions:

{{< highlight kotlin >}}
// Memoized constructor
fun <A> cons(hd: () -> A, tl: () -> Stream<A>): Stream<A> {
    val head: A by lazy(hd)
    val tail: Stream<A> by lazy(tl)
    return Cons({ head }, { tail })
}

// Convinient empty constructor
fun <A> empty(): Stream<A> = Empty

// Helper function to create a Stream
fun <A> streamOf(vararg xs: A): Stream<A> =
    if (xs.isEmpty()) empty()
    else cons({ xs[0] }, { streamOf(*xs.sliceArray(1 until xs.size)) })
{{< /highlight >}}


Next, let's define `map` and `filter` for it.

{{< highlight java >}}
fun <A, B> Stream<A>.map(f: (A) -> B): Stream<B> {
    return when (this) {
        is Cons -> {
            cons(
                { f(head()) },
                { tail().map(f) })
        }
        is Empty -> {
            empty()
        }
    }
}

fun <A> Stream<A>.filter(p: (A) -> Boolean): Stream<A> {
    return when (this) {
        is Cons -> {
            if (p(head())) {
                cons(head) { tail().filter(p) }
            } else {
                tail().filter(p)
            }
        }
        is Empty -> {
            empty()
        }
    }
}
{{< /highlight >}}

What actually will happen if we do this?

{{< highlight java >}}
streamOf(1, 2, 3, 4)
        .map { it + 10 }
        .filter { it % 2 == 0 }
        .map { it * 3 }
{{< /highlight >}}

There is a trace analysis in the book, but it is rather abstract. I sort of get the idea, but not a very solid one.
So I think I can break down the steps by hand, to get a better mental model of how to think of such operations.

Here we go:

{{< highlight kotlin >}}
streamOf(1, 2, 3, 4)
    .map { it + 10 }
    .filter { it % 2 == 0 }
    .map { it * 3 }
{{< /highlight >}}

{{< highlight kotlin >}}
cons({ 1 }, { streamOf(2, 3, 4) })
    .map { it + 10 }
    .filter { it % 2 == 0 }
    .map { it * 3 }
{{< /highlight >}}

{{< highlight kotlin >}}
cons({ 11 }, { streamOf(2, 3, 4)
                   .map { it + 10 } })
    .filter { it % 2 == 0 }
    .map { it * 3 }
{{< /highlight >}}

{{< highlight kotlin >}}
streamOf(2, 3, 4)
    .map { it + 10 }
    .filter { it % 2 == 0 }
    .map { it * 3 }
{{< /highlight >}}

{{< highlight kotlin >}}
cons({ 2 }, { streamOf(3, 4) })
    .map { it + 10 }
    .filter { it % 2 == 0 }
    .map { it * 3 }
{{< /highlight >}}

{{< highlight kotlin >}}
cons({ 12 }, { streamOf(3, 4)
                    .map { it + 10 }})
    .filter { it % 2 == 0 }
    .map { it * 3 }
{{< /highlight >}}

{{< highlight kotlin >}}
cons({ 12 }, { streamOf(3, 4)
                   .map { it + 10 }
                   .filter { it % 2 == 0}})
    .map { it * 3 }
{{< /highlight >}}

{{< highlight kotlin >}}
cons({ 36 }, { streamOf(3, 4)
                   .map { it + 10 }
                   .filter { it % 2 == 0 }
                   .map { it * 3 }})
{{< /highlight >}}

The pattern here is that the first value is always prepared, and the rest is wrapped for later.

If we just write:
{{< highlight kotlin >}}
streamOf(1, 2, 3, 4)
{{< /highlight >}}

Then it will be evaluated as:
{{< highlight kotlin >}}
cons({ 1 }, { streamOf(2, 3, 4) })
{{< /highlight >}}

Notice that the first value 1 is prepared, and the rest is wrapped in a function.

Now how about we add a map to it:
{{< highlight kotlin >}}
streamOf(1, 2, 3, 4)
    .map { it + 10 }
{{< /highlight >}}

This will be evaluated as:

{{< highlight kotlin >}}
cons({ 11 }, { streamOf(2, 3, 4)
                   .map { it + 10 } })
{{< /highlight >}}

Again, notice that the first value 11 is already prepared, and the rest is in a function.

Let's add the filter now:

{{< highlight kotlin >}}
streamOf(1, 2, 3, 4)
        .map { it + 10 }
        .filter { it % 2 == 0 }
{{< /highlight >}}

This will be evaluated as:

{{< highlight kotlin >}}
cons({ 12 }, { streamOf(3, 4)
                   .map { it + 10 }
                   .filter { it % 2 == 0}})
{{< /highlight >}}

The filter is more interesting, cause it will keep evaluating until it finds the first item that will not be filted out.
Thus there are a few more steps to get the first value 12 there.

I think you get it now, the pattern is quite obvious.

So back to the question from the beginning of this article.

What is the description?

In this case the description is:

{{< highlight kotlin >}}
streamOf(1, 2, 3, 4)
        .map { it + 10 }
        .filter { it % 2 == 0 }
        .map { it * 3 }
{{< /highlight >}}

We would expect something like this:

{{< highlight kotlin >}}
(1, 2, 3, 4) ->
(11, 12, 13, 14) ->
(12, 14) ->
(36, 48)
{{< /highlight >}}

But in reality, the evalutaion order is:

{{< highlight kotlin >}}
1 ->
11 ->
2 ->
12 ->
36 ->
...
{{< /highlight >}}

Thus the separation from description from evalutaion.
