+++
author = "Guowei Lv"
categories = ["Android + Dagger2"]
description = "Replace Pure Dependency Injection with Dagger2"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Dependency Injection in Android With Dagger2 (2)"
date = 2021-05-02T09:00:51+03:00
+++

Previous post we set up the so called Pure Dependency Injection. Now let's replace that with Dagger2 balantly.

Convert the composition roots into dagger's `@Module`s.

Create `@Component` to hold each `@Module`.

Create `@Scope`s and use it to scope the objects we want only 1 instance in the component.

Now it looks like this:

{{< figure src="/img/pure-di-to-dagger2.png" >}}
