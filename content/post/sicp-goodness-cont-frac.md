+++
categories = ["SICP"]
description = "SICP Exercise 1.37"
keywords = ["SICP", "JavaScript", "ES6 let", "ES6", "structure and interpretation of computer programs"]
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - Continued Fraction"
date = 2018-07-24T22:39:43+03:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

Writing recursive functions is not an easy task, often requires a different way of thinking.

For practice purpose, let's now take a look at an exercise from the book.

{{< figure src="/img/ex137.jpg" >}}


# Recursive Process

{{< highlight scheme >}}

(define (cont-frac-iter n d k i)
  (if (= k i)
      (/ (n k) (d k))
      (/ (n i) (+ (d i) (cont-frac-iter n d k (+ i 1))))))

(define (cont-frac n d k)
  (cont-frac-iter n d k 1))

{{< /highlight >}}

# Iterative Process

{{< highlight scheme >}}

(define (cont-frac-iter n d k r)
  (if (= k 1)
      r
      (cont-frac-iter n d (- k 1) (/ (n (- k 1)) (+ (d (- k 1)) r)))))

(define (cont-frac n d k)
  (cont-frac-iter n d k (/ (n k) (d k))))

{{< /highlight >}}

# Tips
The two types of recursive procedure can be illustrated below.

{{< figure src="/img/ex137comp.jpg" >}}
