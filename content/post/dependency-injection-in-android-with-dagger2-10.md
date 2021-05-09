+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "Providers"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Dependency Injection in Android With Dagger2 (10)"
date = 2021-05-09T22:53:22+03:00
+++

Let's take a look at the `ViewMvcFactory`:

{{< highlight kotlin >}}
class ViewMvcFactory @Inject constructor(
    private val layoutInflaterProvider: LayoutInflater,
    private val imageLoaderProvider: ImageLoader
)
{{< /highlight >}}

One thing worth paying attention is that this is a factory. Factory is used to create objects. So this means that its dependencies: `LayoutInflater` and `ImageLoader` will be reused to create objects everytime.

We know that `ImageLoader` has no state so that it can be reused, but what if it can't? What we know is that `ImageLoader` is on Dagger's graph, it may be scoped or it may not be, but we want just to follow how it is defiined in the graph. In other words, if `ImageLoader` is scoped, then the `ViewMvcFactory` will get the same instance everytime it is used, otherwise, we will create new instance of `ImageLoader` everytime.

It is very simple to achieve this, just wrap it with `Provider<>`.

{{< highlight kotlin >}}
class ViewMvcFactory @Inject constructor(
    private val layoutInflaterProvider: Provider<LayoutInflater>,
    private val imageLoaderProvider: Provider<ImageLoader>
)
{{< /highlight >}}


