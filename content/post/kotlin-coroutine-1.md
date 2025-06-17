+++
author = "Guowei Lv"
categories = ["Kotlin Coroutine"]
description = "What is actually a Coroutine?"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "What is a Coroutine?"
date = 2025-06-17T11:53:34+03:00
+++

The first question we should ask when learning `Kotlin Coroutine` is: what is a `Coroutine` after all?

Let's go back to the `Thread` world and ask the same question, what is a `Thread`?
Here is code that will create a simple `Thread` and start it:

{{<highlight kotlin>}}
val thread = thread {  }
{{< /highlight >}}

So the answer seems obvious, a `Thread` is just the `Thread` object returned.

Easy.

Can we say the same in the `Coroutine` world?

Let's see how we create a coroutine and start it:

{{<highlight kotlin>}}
fun main() = runBlocking<Unit> {
    val scope = CoroutineScope(EmptyCoroutineContext)
    
    val job = scope.launch {
        innerScope = this
    }
}
{{< /highlight >}}

OK, `launch` will create and start a new coroutine, so the returned object should be the Coroutine itself, right?
Wait, the returned object is a `Job`, hmmm...
Also what is this `CoroutineScope` thing?

What if I tell you that the returned job is also a `CoroutineScope`?

See this code:

{{<highlight kotlin>}}
fun main() = runBlocking<Unit> {
    val scope = CoroutineScope(EmptyCoroutineContext)
    var innerScope: CoroutineScope? = null
    
    val job = scope.launch {
        innerScope = this
    }
    
    job.join()
    
    print(innerScope === job) // true
}
{{< /highlight >}}

If we dig deeper of the `launch` code:

{{<highlight kotlin>}}
public fun CoroutineScope.launch(
    context: CoroutineContext = EmptyCoroutineContext,
    start: CoroutineStart = CoroutineStart.DEFAULT,
    block: suspend CoroutineScope.() -> Unit
): Job {
    val newContext = newCoroutineContext(context)
    val coroutine = if (start.isLazy)
        LazyStandaloneCoroutine(newContext, block) else
        StandaloneCoroutine(newContext, active = true)
    coroutine.start(start, coroutine, block)
    return coroutine
}
{{< /highlight >}}

There is this `StandaloneCoroutine` or `LazyStandaloneCoroutine` is returned as the Job.

Let's keep going:

`StandaloneCoroutine` inherits from `AbstractCoroutine`:


{{<highlight kotlin>}}
public abstract class AbstractCoroutine<in T>(
    parentContext: CoroutineContext,
    initParentJob: Boolean,
    active: Boolean
) : JobSupport(active), Job, Continuation<T>, CoroutineScope
{{< /highlight >}}

which is both a `Job` and `CoroutineScope`!!!

Here is how I make sense of all these: Coroutine's api is split up into 2 parts: Job and CoroutineScope. Job is responsible for certain things like start/cancel, query status, etc, and CoroutineScope is a higher level of controller which contains Job and other stuff. Even though the internal implementation combines the two, but on the user facing apis they are cast into Job and CoroutineScope for type safety and ease of use.
