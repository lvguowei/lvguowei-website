+++
author = "Guowei Lv"
categories = ["Kotlin"]
description = "What is noinline and crossinline?"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Kotlin Noinline and Crossinline"
date = 2021-11-26T20:06:16+02:00
+++

# Compile time constant

{{< highlight kotlin>}}
const val NAME = "Guowei"
fun main() {
  tv.text = NAME
}
{{< /highlight >}}

After compile it will (almost) look like this:

{{< highlight kotlin>}}
// Imaginary code
fun main() {
  tv.text = "Guowei"
}
{{< /highlight >}}

# inline function

We can do similar things to functions, by adding keyword `inline`.

{{< highlight kotlin>}}
inline fun hello() {
  println("hello")
}
fun main() {
  hello()
}
{{< /highlight >}}

So at compile time, the `hello()` function will be copied to the calling place:

{{< highlight kotlin>}}
// Imaginary code
fun main() {
  println("hello")
}
{{< /highlight >}}

It seems that this will optimise the function stack at runtime, is this worth it?
The answer is no, this is not helping much, on the other hand, it may even hurt, because the same code is copied to many places, which results in bigger compiled code.

So what is the real reason behind the concept of inline function?

Let's change the `hello()` function a little bit:

{{< highlight kotlin>}}
inline fun hello(callback: () -> Unit) {
  println("hello")
  callback()
}
fun main() {
  hello {
    println(" world!")
  }
}
{{< /highlight >}}

The way Kotlin handles this lambda is to create a object every time this function is called, like this:

{{< highlight kotlin>}}
fun main() {
  val callback = object : Function0<Unit> {
    override fun invoke() {
      return println(" world!")
    }
  }
  
  hello(callback)
}
{{< /highlight >}}

Normally, creating a few objects is nothing, but what if this is called in some UI rendering code or inside some crazy loops?
The performance will become a problem.

Now comes the valid usecase of *inline* function, basically to inline not only the function body, but also the lambda body. Take a look:

{{< highlight kotlin>}}
// Imaginary code
inline fun hello(callback: () -> Unit) {
  println("hello")
  callback()
}
fun main() {
  println("hello")
  println(" world!")
}
{{< /highlight >}}

Now there is no temp function object to be created.

**So in summary, inline function is to improve the performance problems of lambdas.**

* What is noinline?

Well, it is very self-explanary, it means do not inline ;-)
It is not used on functions, but on function params.

Why do we need it? Let's look at an example:

{{< highlight kotlin>}}
inline function hello(preAction: () -> Unit, postAction: () -> Unit): () -> Unit {
  preAction()
  println("hello")
  postAction()
  return postAction
}

fun main() {
  hello({println("Hi")}, {println("world")})
}
{{< /highlight >}}

The compiled code will look like this:

{{< highlight kotlin>}}
// Imaginary code
fun main() {
  println("Hi")
  println("hello")
  println("world")
  return postAction // wait, what is postAction???
}
{{< /highlight >}}

As you can see, the lambda bodies are all inlined, and the name `postAction` loses its meaning.

The remedy is to add `noinline` ofc.

{{< highlight kotlin>}}
inline function hello(preAction: () -> Unit, noinline postAction: () -> Unit): () -> Unit {
  preAction()
  println("hello")
  postAction()
  return postAction
}
{{< /highlight >}}

How do you know when to use this? Well, I would say that Android studio will tell you.  :D

# What is crossinline

Let's first take a look at code:

{{< highlight kotlin>}}
inline fun hello(postAction: () -> Unit) {
  println("hello")
  postAction()
}

fun main() {
  hello {
    println("world")
    return // this is supposed to return from postAction
  }
}
{{< /highlight >}}

Now the compiled code will look like this:

{{< highlight kotlin>}}
fun main() {
  println("hello")
  println("world")
  return // now this becomes return from main!!!!!!
}
{{< /highlight >}}

What should we do now?

Kotlin's solution to this is to make rules:
Rule 1: `return` in lambda is not allowed, unless the lambda is a param to a inlined function.
And in such case, `return` is returning the outer outer function. So the previous example is valid, and the `return` in `hello()`is supposed to be returning from `main()`.

Let's make things more complex (I promise, this is the last time)

{{< highlight kotlin>}}
inline fun hello(postAction: () -> Unit) {
  println("hello")
  runOnUiThread {
    postAction()
  }
}

fun main() {
  hello {
    println("world")
    return
  }
}
{{< /highlight >}}

The `return` here is supposed to be returning from the `main()`, but now after compile, it will be inside the `runOnUithread()`, so how does this work now?

The Kotlin's solution is .... more rules:

Rule 2: Do not allow inline function's lambda param to be called from other lambdas. (e.g. `postAction()` cannot be wrapped inside `runOnuithread()`).

If we really want to do this, then we can add the `crossinline` to the lambda param.

{{< highlight kotlin>}}
inline fun hello(crossinline postAction: () -> Unit) {
  println("hello")
  runOnUiThread {
    postAction()
  }
}

fun main() {
  hello {
    println("world")
    return // after adding crossinline, return is not allowed here
  }
}
{{< /highlight >}}

But then there is this return problem again, so Kotlin's solution is ... you guessed it, more rules:

Rule 3: crossinline lambda cannot have return.

To be honest, all these is way too complex. So the rule of thumb is to rely on Android studio to tell you what to do when it detects a problem.

(What!?? the language is so complex that no one knows how to use it correctly so we have to use fancy features from IDE for help, I'm not feeling good about this)

{{< highlight kotlin>}}
{{< /highlight >}}
