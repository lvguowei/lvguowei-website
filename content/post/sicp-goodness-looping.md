+++
categories = ["SICP"]
description = "About different types of recursion"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - Why you don't need looping constructs"
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

# Linear Iterative Process

Now let's take a different perspective on computing factorials. We can describe the computation by saying that the counter and the product simultaneously change from one step to the next according to the rule:

>product <- counter * product

>counter <- counter + 1

Translate this into code we get:

{{< highlight scheme >}}

(define (factorial n)
  (fact-iter 1 1 n))
  
(define (fact-iter product counter max-count)
  (if (> counter max-count)
    product
    (fact-iter (* counter product)
               (+ counter 1)
               max-count)))
{{< /highlight >}}

Let's again try to examine the shape of this process:

{{< highlight scheme >}}

(factorial 6)

(fact-iter 1 1 6)

(fact-iter 1 2 6)

(fact-iter 2 3 6)

(fact-iter 6 4 6)

(fact-iter 24 5 6)

(fact-iter 120 6 6)

(fact-iter 720 7 6)

720

{{< /highlight >}}

The shape here looks very different from the previous process.

This time, every execution step returns a **new self** with all the values needed for the next step. Whereas in the previous process, each step does not return but put more work into memory.

We can see that here the number of steps required grows linearly with *n*, such a process is called a **linear iterative process**.

In short, the execution of a **recursive procedure** can be a **linear recursive process** or a **linear iterative process** (or something else like **tree recursive process**).

Now that we understand the two types of recursive procedure. We can go back to answer the question about looping constructs.

We can now easily see that the iterative process is actually equivalent to the looping constructs.

>But unfortunately most implementations of common languages (including Ada, Pascal, and C) are designed in such a way that the interpretation of any recursive procedure consumes an amount of memory that grows with the number of procedure calls, even when the process described is, in principle, iterative. The implementation of Scheme does not share this defect. It will execute an iterative process in constant space, even if the iterative process is described by a recursive procedure. An implementation with this property is called **tail-recursive**. With a tail-recursive implementation, iteration can be expressed using the ordinary procedure call mechanism, so that special iteration constructs are useful only as syntactic sugar.

Now I hope that you have gained a deeper understanding about looping and recursive and the relation between the two.

~THE END~

