+++
categories = ["SICP"]
keywords = ["SICP", "functional programming", "structure and interpretation of computer programs"]
description = "Expoloring the stream paradigm"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - Stream (8)"
date = 2019-07-05T22:37:09+03:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

One more example from the book and one related exercise.

How to calculate PI.

PI can be approximated by:

PI/4 = 1 - 1/3 + 1/5 - 1/7 + ...

First, we construct the sequence:

1,  -1/3,  1/5,  -1/7 ...

{{< highlight scheme >}}
(define (pi-summands n)
  (cons-stream (/ 1.0 n)
               (stream-map - (pi-summands (+ n 2)))))
{{< /highlight >}}

Next, we construct the actual stream that approximate PI:

4X1, 4X(1 - 1/3), 4X(1 - 1/3 + 1/5), 4X(1 - 1/3 + 1/5 - 1/7)

{{< highlight scheme >}}

(define pi-stream
  (scale-stream (partial-sum (pi-summands 1)) 4))

(define (partial-sum s)
  (cons-stream (stream-car s)
               (add-streams (stream-cdr s)
                            (partial-sum s))))
{{< /highlight >}}

If we print out the `pi-stream`, we can see that it converges to PI very slowly.

We can use Euler transformation to speed up the process.

Suppose the original stream is 

a1, a2, a3, a4 ...

and the transformed stream will be

b1, b2, b3, b4 ...

Then b_n can be calculated as:

`b_n = a_n+1 - (a_n+1 - a_n)^2 / (a_n-1 - 2 * a_n + a_n+1)` when n >= 3

`b_n = a_n` when n < 3

{{< highlight scheme >}}
(define (euler-transform s)
  (let ((s0 (stream-ref s 0))
        (s1 (stream-ref s 1))
        (s2 (stream-ref s 2)))
    (cons-stream (- s2 (/ (square (- s2 s1))
                          (+ s0 (* -2 s1) s2)))
                 (euler-transform (stream-cdr s)))))

{{< /highlight >}}



Display `(euler-transform pi-stream)` shows that it works better.

But even better, we can recursively accelerate the accelerated stream.

This is becoming a bit hard to wrap your head around, so I will put more explanation here.

Suppose the original stream is 

a: a1 a2 a3 a4 ...

Apply the euler transform to it and we get

b: b1 b2 b3 b4 ...

Note that b1 is calculated from a1, a2 and a3, b2 is calculated from a2, a3 and a4 and so on.

Then if we apply euler transform again we get

c: c1 c2 c3 c4 ...

Note that c1 is calculated from b1, b2 and b3, and c2 is calculated from b2, b3 and b4 and so on.

We can create a stream of the above streams. This is called *tableau*.

{{< highlight scheme >}}
(define (make-tableau transform s)
  (cons-stream s
               (make-tableau transform
                             (transform s))))

{{< /highlight >}}

For example, `(make-tableau euler-transform a)` will give back a stream of (a, b, c ...), which looks like:

(
 (a1, a2, a3 ,a4 ...)
 (b1, b2, b3, b4 ...)
 (c1, c2, c3, c4 ...)
 ...
)


And the accelerated sequence can be formed by taking the first element of each row of the tableau. For example, (a1, b1, c1 ...)

{{< highlight scheme >}}
(define (accelerated-sequence transform s)
  (stream-map stream-car
              (make-tableau transform s)))
{{< /highlight >}}

Now the "super-acceleration" of PI sequence is now:

{{< highlight scheme >}}
(accelerated-sequence euler-transform pi-stream)
{{< /highlight >}}


**Now Exercise 3.65:**

Use the series

ln2 = 1 - 1/2 + 1/3 - 1/4 + ...

to compute the approximations to the natural log of 2.

{{< highlight scheme >}}
(define (ln2-summands n)
  (cons-stream (/ 1.0 n)
               (stream-map - (ln2-summands (+ n 1)))))

(define ln2-stream
  (partial-sum (ln2-summands 1)))

(accelerated-sequence euler-transform ln2-stream)
{{< /highlight >}}

Love how things build up in these examples. I get huge satisfaction from this kind of thing. Much more then e.g. learning how to write React apps. What do you say?
