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

{{< highlight scheme >}}

(f 5)

(sum-of-squares (+ 5 1) (* 5 2))

(sum-of-squares 6 10)

(+ (square 6) (square 10))

(+ (* 6 6) (* 10 10))

(+ 36 100)

136

{{< /highlight >}}

# Normal Order Evaluation

The process of this approach can be summarized as **fully expand and then reduce**.

The steps are as follows:

{{< highlight scheme >}}

(f 5)

(sum-of-squares (+ 5 1) (* 5 2))

(+ (square (+ 5 1)) (square (* 5 2)))

(+ (* (+ 5 1) (+ 5 1)) (* (* 5 2) (* 5 2)))

(+ (* 6 6) (* 10 10))

(+ 36 100)

136

{{< /highlight >}}

It feels like applicative order is more *eager* while normal order is more *lazy*.

There are some cases that the two yield different results.

Exercise 1.5 gives a way to test wether the language uses applicative order or normal order.

The test is as follows:

{{< highlight scheme >}}

(define (p) (p))

(define (test x y)
  (if (= x 0)
  0
  y))
  
(test 0 (p))

{{< /highlight >}}

Try figure out how this test can behave differently under different evaluation models.

<hr />

## Answer

`p` is a endless recursive function, it will exec forever. If the language is applicative order, then when evaluate `(test 0 (p))`,
it will first try to evaluate `(p)`, which will of course end up in an endless loop. However, if the language is normal order, this evaluation gets delayed.
{{< highlight scheme >}}
(test 0 (p))

(if (= 0 0)
  0
  (p))
  
0
{{< /highlight >}}

And the `(p)` is now pushed to the `else` part of the `if`. When the `if` clause gets evaluated, it just returns 0 without even look at `(p)`.

Clever isn't it.




