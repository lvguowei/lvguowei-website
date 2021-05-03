+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "Component Dependencies"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Dependency Injection in Android With Dagger2 (4)"
date = 2021-05-02T22:53:45+03:00
+++

Let's look at the `PresentationComponent` more closely.

{{< figure src="/img/component-dependencies-1.png" >}}

One strange thing is that `PresentationModule` has `ActivityComponent` as its dependency, and it `@Provide` all the objects provided by `ActivityComponent` already.

This is rather ugly, can we do something about it?

The answer is **Component Dependencies**.

The idea is that we can make `PresentationComponent` depends on `ActivityComponent`. That way, `PresentationComponent` automatically gets access to all the objects exposed by `ActivityComponent`.

After this change, it looks like this:

{{< figure src="/img/component-dependencies-2.png" >}}

