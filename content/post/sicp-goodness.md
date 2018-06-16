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

Today's topic is about two ways to perform evaluation of the program: **Applicative Order** and **Normal Order**.

Let's put here the example from the book.

First, define some functions to calculate the sum of squares of two numbers.

{{< highlight scheme >}}

(define (square x) (* x x))

(define (sum-of-squares x y)
  (+ (square x) (square y)))
  
(define (f a)
  (sum-of-squares (+ a 1) (* a 2)))

{{< /highlight >}}

Now, let's evaluate the expression `(f 5)` using the two models.

# Applicative Order Evalutation

The process of this approach can be summarized as **evaluate the function and arguments and then apply**.

1. Since 5 is already a number, so just apply it to `f`, we have

{{< highlight scheme >}}

(sum-of-squares (+ 5 1) (* 5 2))

{{< /highlight >}}

2. Evaluate the arguments which are `(+ 5 1)` and `(* 5 2)`:

{{< highlight scheme >}}

(sum-of-squares 6 10)

{{< /highlight >}}

3. Apply function `sum-of-squares`:

{{< highlight scheme >}}

(+ (square 6) (square 10))
  
{{< /highlight >}}

4. Evaluate arguments `(square 6)` and `(square 10)`:

{{< highlight scheme >}}

(+ (* 6 6) (* 10 10))
  
{{< /highlight >}}

