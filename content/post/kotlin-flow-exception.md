+++
author = "Guowei Lv"
categories = ["Kotlin Coroutine"]
description = "No try/catch inside Flow!?"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Flow exception handling (Part 1)"
date = 2026-05-04T11:53:34+03:00
+++

You may have heard that "No try/catch inside Flow" or "Only use catch() operator". But why?
Let's explore from the beginning.

Here is a very simple setup:


{{<highlight kotlin>}}


fun main() = runBlocking {
    val worker = Worker()
    val scope = CoroutineScope(EmptyCoroutineContext)
    val flow = flow {
        emit(1)
        emit(2)
        emit(3)
    }
    scope.launch {
        try {
            flow.collect { println(worker.doWork(it)) }
        } catch (e: Exception) {
            println("Error in collect: ${e.message}")
        }
    }

    delay(10000)
}

class Worker {
    fun doWork(n: Int): String =
        if (Random.nextBoolean()) {
            "Done on $n"
        } else {
            error("Work error on $n")
        }
}


{{< /highlight >}}

The `flow` simply emitting 3 numbers, and we do some work after collecting them. (with 50% rate of throwing an exception).

We can run it and hopefully you will see something like:

{{<highlight kotlin>}}
Error in collect: Work error on 1
{{< /highlight >}}


This is all good. But what if we try to play with fire by adding try/catch in the flow??

{{<highlight kotlin>}}
fun main() = runBlocking {
    val worker = Worker()
    val scope = CoroutineScope(EmptyCoroutineContext)
    val flow = flow {
        try {
            emit(1)
            emit(2)
            emit(3)
        } catch (e: Exception) {
            println("Silly try catch $e")
        }
    }
    scope.launch {
        try {
            flow.collect { println(worker.doWork(it)) }
        } catch (e: Exception) {
            println("Error in collect: ${e.message}")
        }
    }

    delay(10000)
}
{{< /highlight >}}

Run this and the error becomes:

{{<highlight kotlin>}}
Silly try catch java.lang.IllegalStateException: Work error on 1
{{< /highlight >}}

Wait wait, the exception is thrown during collecting the flow, but it got caught inside the `emit`???
In other words, the exception happens in "consuming part" got caught in the "producing part"? This is not good, we want the exception happens in the consuming part got handled in the consuming part!

Let's slow down, there is a reason why this works like this, and it is right in front of your eyes.

The key is `FlowCollector`. The definition of `collect` is:



{{<highlight kotlin>}}
/**
* Accepts the given [collector] and [emits][FlowCollector.emit] values into it.
*/
public suspend fun collect(collector: FlowCollector<T>)
{{< /highlight >}}

And the def of `flow {}` is:
{{<highlight kotlin>}}
public fun <T> flow(@BuilderInference block: suspend FlowCollector<T>.() -> Unit): Flow<T> = SafeFlow(block)
{{< /highlight >}}

You see the connection here? The FlowCollector we give in the `collect()` will be used when calling the `emit()`!

So this:
{{<highlight kotlin>}}
try { 
  flow.collect { println(worker.doWork(it)) }
} catch (e: Exception) {
  println("Error in collect: ${e.message}")
}
{{< /highlight >}}

is roughly equivalent to:

{{<highlight kotlin>}}
try {
  println(worker.doWork(1))
  println(worker.doWork(2))
  println(worker.doWork(3))
} catch(e: Exception) {
  println("Error in collect: ${e.message}")
}
{{< /highlight >}}

And if we surround `emit()` with try/catch, then it is like doing:

{{<highlight kotlin>}}
try {
  try {
    println(worker.doWork(1))
    println(worker.doWork(2))
    println(worker.doWork(3))
  } catch(e: Exception) {
    println("Silly try catch $e")
  }
} catch(e: Exception) {
  println("Error in collect: ${e.message}")
}
{{< /highlight >}}

The exception is now hijacked!!!

So now you understand why not to surround emit() with try/catch, or at least leave the emit() out side of the try/catch if you have a good reason to use one.
