+++
author = "Guowei"
categories = ["Kotlin"]
description = "Implement a simpler version of by lazy"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "How by Lazy Works"
date = 2021-12-10T06:49:43+02:00
+++

`by lazy` is implemented by using the "property delegation" in Kotlin.
But if you look into the source code and trying to understand what is going on, it can be confusing, because it is full of locks and generics and where's the `getValue()`` function they say that the delegation must implement??

In the handmade spirit (best way to learn is by doing it yourself), let's do a stripped down version ourselves.

First, let's wriite a `MyLazy` interface.

{{< highlight kotlin>}}
interface MyLazy {
    val value: Int
}
{{< /highlight >}}

This is not really necessary, since we are not going to have multiple implementations of Lazy, but since there is interface `Lazy` in kotlin, let's just add one too.


Next, let's add the required `getValue()` function, by using an extention function. This is where I get stuck when reading the original implementation in kotlin. This also reveals one biggest downside of extentiion functions, poor code discoverability.

{{< highlight kotlin>}}
operator fun MyLazy.getValue(thisRef: Any?, property: KProperty<*>) = value
{{< /highlight >}}
Then let's add `MyLazyImpl` to implement `MyLazy` interface.
{{< highlight kotlin>}}
object UNINITIALISED
class MyLazyImpl(val initializer: () -> Int) : MyLazy {
    private var _value: Any = UNINITIALISED
    override val value: Int
        get() {
            if (_value == UNINITIALISED) {
                _value = initializer()
            }
            return _value as Int
        }
}
{{< /highlight >}}

Finally, let's add this helper function.

{{< highlight kotlin>}}
fun myLazy(initializer: () -> Int): MyLazy = MyLazyImpl(initializer)
{{< /highlight >}}

And now we are done!
