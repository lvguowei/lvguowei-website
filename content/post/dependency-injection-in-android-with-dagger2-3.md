+++
author = "Guowei Lv"
categories = ["Android + Dagger2"]
description = "Component as injector"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Dependency Injection in Android With Dagger2 (3)"
date = 2021-05-02T14:56:51+03:00
+++

In previous post, we replaced our Pure Dependency Injection with Dagger2. But, the
conversion is without too much thoughts.

One improvement we can do is to follow Dagger's convention of using **Component as Injector**.

{{< figure src="/img/component-as-injector.png" >}}

We can make the following mental models:

1. Dagger's `@Module` is for creating objects.
2. Dagger's `@Component` is for injecting objects into application classes.

