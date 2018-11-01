+++
author = "Guowei Lv"
categories = ["SICP"]
description = "Is this polymorphism?"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - Data Directed Programming and Message Passing (I)"
date = 2018-10-28T20:17:15+02:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

When I first read about **data directed programming** in the book, I was surprised. I couldn't believe that one of the greatest battle force of Object Oriented paradigm, *polymorphism*, is showing up in this functional programming book.

**Functional programming can also do polymorphism!**

Don't believe me? Let's go through an example together.


## The Complex Number Example

There are two ways to represent complex numbers: **rectangular form** (real part and imaginary part) and **polar form** (magnitude and angle).

Let's illustrate this so we get a better understanding.

{{< figure src="/img/complex-number.jpg" >}}

Addition of two complex numbers can be very easily described in rectangular form:

{{< highlight sh >}}
Real-part(z1 + z2) = Real-part(z1) + Real-part(z2)
Imaginary-part(z1 + z2) = Imaginary-part(z1) + Imaginary-part(z2)
{{< /highlight >}}

When multiplying two complex numbers, it is more natural to think in terms of polar form:

{{< highlight sh >}}
Magnitude(z1 * z2) = Magnitude(z1) * Magnitude(z2)
Angle(z1 * z2) = Angle(z1) + Angle(z2)
{{< /highlight >}}

Let's assume that our system needs to work with both representations.

So we need 4 *selectors* for both representations: **real-part**, **imag-part**, **magnitude** and **angle**. In other words, no matter how the complex number is represented internally, it needs to support these 4 operations on it.

>Is this the equivalent of interfaces or classes in OO ...

We also need two *constructors* to create complex numbers: `make-from-real-imag` and `make-from-mag-ang`.

> These can be viewed as two factory methods in the classes?

Now we can implement arithmetic operations using these selectors and constructors:

{{< highlight scheme >}}
(define (add-complex z1 z2)
  (make-from-real-imag (+ (real-part z1) (real-part z2))
                       (+ (imag-part z1) (imag-part z2))))
                       
(define (sub-complex z1 z2)
  (make-from-real-imag (- (real-part z1) (real-part z2))
                       (- (imag-part z1) (imag-part z2))))

(define (mul-complex z1 z2)
  (make-from-mag-ang (* (magnitude z1) (magnitude z2))
                     (+ (angle z1) (angle z2))))

(define (div-complex z1 z2)
  (make-from-mag-ang (/ (magnitude z1) (magnitude z2))
                     (- (angle z1) (angle z2))))
                       
{{< /highlight >}}


# Two Internal Implementations of Complex Numbers

Now we know that if we have a complex number instance at hand, we can get its **real-part**, **imaginary-part**, **magnitude** and **angle**. We also know how to do basic arithmetic operations on them.

Let's now look at how to implement a complex number in rectangular form and polar form.

For the rectangular form, the selectors and constructors can be implemented as:

{{< highlight scheme >}}

;; easy
(define (real-part z) (car z))

;; easy
(define (imag-part z) (cdr z))

;; not so easy
(define (magnitude z)
  (sqrt (+ (square (real-part z)) (square (imag-part z)))))

;; not so easy
(define (angle z)
  (atan (imag-part z) (real-part z)))

;; easy
(define (make-from-real-imag x y) (cons x y))

;; not so easy
(define (make-from-mag-ang r a)
  (cons (* r (cos a)) (* r (sin a))))

{{< /highlight >}}

For the polar form, the selectors and constructors can be implemented as:

{{< highlight scheme >}}

;; not so easy
(define (real-part z)
  (* (magnitude z) (cos (angle z))))

;; not so easy
(define (imag-part z)
  (* (magnitude z) (sin (angle z))))

;; easy
(define (magnitude z)
  (car z))

;; easy
(define (angle z)
  (cdr z))

;; not so easy
(define (make-from-real-imag x y)
  (cons (sqrt (+ (square x) (square y)))
        (atan y x)))

;; easy
(define (make-from-mag-ang r a) (cons r a))

{{< /highlight >}}


As we can see, as to the 4 selectors, some are easy to implement in polar form and some are easy to implement in rectangular form.

So far so good. Or is it?

If you squeeze your eyes, there are 2 problems in our complex number system.

1. The function names of the two forms will clash each other. There cannot exists two functions that share the same name under global scope.

2. Given a complex number, there is no way to tell is it in rectangular form or polar form.

Let's see how to solve the second problem with something called **tagged data** in the next blog post. Stay tuned!
