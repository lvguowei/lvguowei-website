+++
categories = ["SICP"]
keywords = ["SICP", "Fibonacci"]
description = "About fibonacci numbers and more"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "Sicp Goodness - Fibonacci numbers"
date = 2018-07-06T20:35:43+03:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

Every programmer at some point has studied fibonacci numbers, mostly likely as an interview question. Just in case you forget it, let's give the definition here:

>fib(0) = 0

>fib(1) = 1

>fib(n) = fib(n - 2) + fib(n - 1)

> 0 1 1 2 3 5 8 13 21 34 55 ...

In interviews, it is normally used to test the programmer's knowledge about recursion. The easiest anwser would be:

{{< highlight scheme >}}

(define (fib n)
  (cond ((= n 0) 0)
        ((= n 1) 1)
        (else
          (+ (fib (- n 2)) (fib (- n 1))))))

{{< /highlight >}}

This is a direct translation from the definition, so it is quite easy to get right even in interviews.

Then a question that might come immediately after this is to write an iterative version.

Try it yourself.

<hr />

OK, if you can write it out like second nature, then close this page and spend more time with your family.

If you cannot solve it or it took you a very long time, then you can read on.

OK, I admit that though have looked up the anwser several times already, after a while I still have hard time writing it from scratch.

Until I read SICP.

Let's say we have two variables, `current = 0`, `next = 1`.

The steps to compute `fib(n)` iteratively is:

1. If `n = 0` just return `current`.
2. Do `current <- next` `next <- current + next` *simultaneously* and `n--`.
3. Go to step 1.

Think of `current` as something that is concrete and can be returned, `next` as something that is in the future and not yet reachable.

Let's try to calculate `fib(3)` as an example.

In order to calculate `fib(3)`, we have to calculate from `fib(0)` to `fib(3)`.

At the beginning, we already have `current = 0`, `next = 1`.

{{< highlight scheme>}}
next     1 
-------------
current  0 
{{< /highlight >}}

This can be interpreted as:

If someone asks us what is the current fib number, which is `fib(0)`, we can tell him 0. But note that we cannot use the `next` directly now because it is *in the future*, so if he asks us what is `fib(1)`, we have to tell him that we do not know yet.

Next we calculate `fib(1)`.

Now we can bring the `next` to the `current`. And at the same time, we have to calculate a new next value by `next <- current + next`. Notice all these happens simultaneously, there is no ordering. Or you can think of it as if we are using some temporary variable: `temp <- current, current <- next, next <- temp + next`.

{{< highlight scheme>}}
next     1  1 
-----------------
current  0  1 
{{< /highlight >}}

At this point, we know `fib(1) = 1`.

Next calculate `fib(2)`.

Do `current <- next` and `next <- current + next` as before:

{{< highlight scheme>}}
next     1  1  2 
---------------------
current  0  1  1 
{{< /highlight >}}

So `fib(2) = 1`.

The same for `fib(3)`:

{{< highlight scheme>}}
next     1  1  2  3 
-------------------------
current  0  1  1  2 
{{< /highlight >}}

So `fib(3)` is 2.

Now if you understand these, writing a iterative procedure becomes much easier:

{{< highlight scheme>}}
(define (fib-iter current next n)
  (if (= n 0)
    current
    (fib-iter next (+ current next) (- n 1))))
(define (fib n)
  (fib-iter 0 1 n))
{{< /highlight >}}


To test your understanding, solve this exercise in SICP:

*Exercise 1.11*

>A function **f** is defined by the rule that **f(n) = n if n < 3** and **f(n) = f(n-1) + 2f(n-2) + 3f(n-3)** if **n>= 3**. Write a procedure that computes **f** by means of a recursive process. Write a procedure that computes **f** by means of an iterative process.

<hr />

# Answer
{{< highlight scheme>}}

(define (f-recur n)
  (cond ((< n 3) n)
        (else
         (+ (f-recur (- n 1)) (* 2 (f-recur (- n 2))) (* 3 (f-recur (- n 3)))))))

(define (iter a b c n)
  (cond ((= n 0) c)
        ((= n 1) b)
        ((= n 2) a)
        (else
         (f-iter (+ a (* b 2) (* c 3)) a b (- n 1)))))

(define (f-iter n) (iter 2 1 0 n))
{{< /highlight >}}

>P.S. Chapter 3.5.2 of the book talks about *infinite streams*. And the fibonacci number appears again. This time, the fib number is computed by adding two infinite streams of fib numbers. Since there are too many new things going on, I leave this to readers who want to explore more.


