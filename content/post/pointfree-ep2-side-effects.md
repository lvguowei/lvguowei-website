+++
author = "Guowei Lv"
categories = ["PointFree"]
description = ""
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Pointfree Ep2 Side Effects"
date = 2024-07-08T15:38:31+03:00
+++

The title of this episode is called "Side effects", but in my opinion, it's all about how to move side-effects out of the function and make the function composable.

# Side effects in the body of the function

If there is some side-effects in the body of the function, we can move the side-effect into the return of the function, and let the caller deal with it.

{{< highlight swift >}}
func computeWithEffect(_ x: Int) -> Int {
    let computation = x * x + 1
    print("Computed \(computation)")
    return computation
}
{{< /highlight >}}

We can move the stuff to print into the return type.

{{< highlight swift >}}
func computeAndPrint(_ x: Int) -> (Int, [String]) {
    let computation = x * x + 1
    return (computation, ["Computed \(computation)"])
}
{{< /highlight >}}

This way we can do:

{{< highlight swift >}}
let (computation, logs) = computeAndPrint(2)
logs.forEach { print($0) }
{{< /highlight >}}

But there is one problem, we cannot compose `computeAndPrint` with itself, because of the extra String array in the return type.

To solve this problem, we can invent a new operator `>=>`.

{{< highlight swift >}}
precedencegroup EffectfulComposition {
    associativity: left
    higherThan: ForwardApplication
    lowerThan: ForwardComposition
}

infix operator >=>: EffectfulComposition

func >=> <A, B, C>(
    _ f: @escaping (A) -> (B, [String]),
    _ g: @escaping (B) -> (C, [String])
) -> ((A) -> (C, [String])) {

    return { a in
        let (b, logs) = f(a)
        let (c, moreLogs) = g(b)
        return (c, logs + moreLogs)
    }
}

{{< /highlight >}}

Well, this operator is implemented here just to solve the `[String]` in the return type, but it can be a general operator too.

E.g. we can do:

{{< highlight swift >}}
func >=> <A, B, C>(
    _ f: @escaping (A) -> B?,
    _ g: @escaping (B) -> C?
) -> ((A) -> C?) {

    return { a in
        guard let b = f(a) else { return nil }
        return g(b)
    }
}
{{< /highlight >}}

So I would say the fish operator `>=>` can be used whenever the functions cannot be directly composed together because of their return types. It has not really much to do with Side-effects per se, but more of a tool to solve function composition problems.



{{< highlight swift >}}
{{< /highlight >}}

{{< highlight swift >}}
{{< /highlight >}}

{{< highlight swift >}}
{{< /highlight >}}

{{< highlight swift >}}
{{< /highlight >}}

{{< highlight swift >}}
{{< /highlight >}}
