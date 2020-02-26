+++
categories = ["SICP"]
keywords = ["SICP", "lambda calculus", "structure and interpretation of computer programs"]
description = "integral using stream"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - Stream (10)"
date = 2020-02-26T13:14:23+02:00
draft = true
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

Let's talk about the integral example in the book.

This has always been a problem for me, whenever I get here, I either give up or pretend that I get it and move on. Not this time!

Let's put the example here first for your reference.

{{< figure src="/img/integral.gif" >}}

And this is the mathematical formula:

{{< figure src="/img/integral-formula.gif" >}}

And the code:

{{< highlight scheme >}}
(define (integral integrand initial-value dt)
  (define int
    (cons-stream initial-value
                 (add-stream (scale-stream integrand dt)
                             int)))
  int)
{{< /highlight >}}


In order to fully grasp this, we need to refresh on what is integral.

In my own words, it basically represents the area between the function and x-axis. For example, the following picture shows the integral of a certain function *f(x)* between *a* and *b*.

{{< figure src="/img/integral-example.png" >}}

But computational wise, how to we calculate this?

One simple way is to model this function into many many thin rectangles. Then, the sum of the area of these rectangles can be used as an approx. of the area of the function.

{{< figure src="/img/area-under-curve.gif" >}}

If you have difficulty understanding this, please watch this excellent video:

{{< youtube id="rfG8ce4nNh0" autoplay="false" >}}

Let's now take a closer look and the formula in the book.

{{< figure src="/img/integral-formula.gif" >}}

This is correct in the context of the book. But a bit misleading if you try to look at it from a math point of view. Allow me to rewrite it:









{{< highlight scheme >}}
{{< /highlight >}}


{{< highlight scheme >}}
{{< /highlight >}}
{{< highlight scheme >}}
{{< /highlight >}}
{{< highlight scheme >}}
{{< /highlight >}}
{{< highlight scheme >}}
{{< /highlight >}}
{{< highlight scheme >}}
{{< /highlight >}}
{{< highlight scheme >}}
{{< /highlight >}}
{{< highlight scheme >}}
{{< /highlight >}}
{{< highlight scheme >}}
{{< /highlight >}}
{{< highlight scheme >}}
{{< /highlight >}}
{{< highlight scheme >}}
{{< /highlight >}}
{{< highlight scheme >}}
{{< /highlight >}}
{{< highlight scheme >}}
{{< /highlight >}}
{{< highlight scheme >}}
{{< /highlight >}}
{{< highlight scheme >}}
{{< /highlight >}}
{{< highlight scheme >}}
{{< /highlight >}}
{{< highlight scheme >}}
{{< /highlight >}}
{{< highlight scheme >}}
{{< /highlight >}}
{{< highlight scheme >}}
{{< /highlight >}}
{{< highlight scheme >}}
{{< /highlight >}}
