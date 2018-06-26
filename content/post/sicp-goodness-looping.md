+++
categories = ["SICP"]
description = "About different types of recursion"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "Sicp Goodness - Why you don't need looping constructs"
date = 2018-06-16T21:07:45+03:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

I was quite shocked when I first read the following text from the book:

>... As a consequence, these languages(Ada, Pascal and C) can describe iterative processes only by resorting to special-purpose "looping constructs", such as **do**, **repeat**, **until**, **for**, and **while**. The implementation of Scheme ... does not share this defect. ...

This basically says that *looping structures* are **NOT** necessary.

*WHAT!!??*

I know, take a deep breath, everything will be fine.

In order to understand why, we must understand **recursion**.

# Recursive Procedure

The first concept is *recursive procedure*. This is very easy to understand, if a procedure is defined in terms of itself, then we can call this procedure *recursive procedure*.

Let's give the example in the book.

Here we define a factorial function.

{{< highlight scheme >}}
(define (factorial n)
  (if (= 1 n)
    1
    (* n (factorial (- n 1)))))
{{< /highlight >}}

This is a direct translation from the definition of factorial in math. Because the procedure is defined in terms of itself, it is called a *recursive procedure*.

# Linear Recursive Process

Now let's analyze the execution of this **factorial** procedure process.

{{< highlight scheme >}}

(factorial 5)

(* 5 (factorial 4))

(* 5 (* 4 (factorial 3)))

(* 5 (* 4 (* 3 (factorial 2))))

(* 5 (* 4 (* 3 (* 2 (factorial 1)))))

(* 5 (* 4 (* 3 (* 2 1))))

(* 5 (* 4 (* 3 2)))

(* 5 (* 4 6))

(* 5 24)

120

{{< /highlight >}}

We see a pattern here, the shape of the execution process first **expand** and then **shrink**. Since the amount of information needed to keep in memory grows linear with *n*, we call this type of process **linear recursive process**.
