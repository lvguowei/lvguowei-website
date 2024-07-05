+++
author = "Guowei Lv"
categories = ["PointFree"]
description = ""
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Pointfree Ep1 Functions"
date = 2024-07-05T21:56:51+03:00
+++

I'm starting a new series, mostly just me taking notes while I go through the [PointFree](https://www.pointfree.co/) videos.

# The pipe operator
The `|>` operator is defined as:

{{< highlight swift >}}
precedencegroup ForwardApplication {
  associativity: left
}

infix operator |>: ForwardApplication

func |> <A, B>(x: A, f: (A) -> B) -> B {
  return f(x)
}

{{< /highlight >}}

It can be used as::

{{< highlight swift >}}
func incr(_ x: Int) -> Int {
  return x + 1
}

func square(_ x: Int) -> Int {
  return x * x
}

2 |> incr |> square

{{< /highlight >}}

# Function composition operator
This operator is defined as:
{{< highlight swift >}}

precedencegroup ForwardComposition {
  higherThan: ForwardApplication
  associativity: left
}
infix operator >>>: ForwardComposition

func >>> <A, B, C>(_ f: @escaping (A) -> B, _ g: @escaping (B) -> C) -> ((A) -> C) {
  return { a in g(f(a)) }
}

{{< /highlight >}}

Now we can compose two functions(given that the types match) into a new one by:
{{< highlight swift >}}

2 |> incr >>> square

[1, 2, 3].map(square >>> incr)
{{< /highlight >}}
