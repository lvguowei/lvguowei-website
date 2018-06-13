+++
categories = ["SICP"]
description = "Some goodness from the book Structure and Interpretation of Computer Programs"
featured =  "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - Applicative Order v.s. Normal Order"
date = 2018-06-13T22:30:04+03:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

This is the first post of this **SICP Goodness** series, in which I discuss some of my own findings from reading the book SICP.

Today's topic is about two different evaluation methods: **Applicative Order** and **Normal Order**.

Let's put here the example from the book.

First, let's define some functions to calculate sum of squares of two numbers.

{{< highlight scheme >}}
(define (square x) (* x x))
{{< /highlight >}}
