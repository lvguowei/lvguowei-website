+++
author = "Guowei Lv"
categories = ["SICP"]
description = "Is this polymorphism?"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - Data Directed Programming (III)"
date = 2018-11-16T20:29:13+02:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

# A Table to the Rescue

In previous article, we see that it becomes quite annoying that each and every one of the generic selectors of the complex number has to do a `cond` on the types.

This is a symptom of lacking the right abstraction in our system. Actually, we can better organize this by using a table, shown as below:

{{< figure src="/img/table-of-operations.jpg" >}}

Data-directed programming is the technique of designing programs to work with such a table directly.

> Wait, this looks a lot like virtual table in C++ ?

How to use this table is obvious. For example, to call `real-part` on a complex number object, first we get the type of the complex number, say it is in rectangular form. Then we just look up the table by `real-part` and `Rectangular`, and find the real procedure to use.

We also need ways to put procedures into the table.

# Implementation of the table

Here are the two procedures `put` and `get`:

{{< highlight scheme >}}

(define operation-table (make-table))
(define get (operation-table 'lookup-proc))
(define put (operation-table 'insert-proc!))


;; Helper functions --- implementation of two dimensional table
(define (assoc key records)
  (cond ((null? records) false)
        ((equal? key (caar records)) (car records))
        (else (assoc key (cdr records)))))

(define (make-table)
  (let ((local-table (list '*table*)))
    (define (lookup key-1 key-2)
      (let ((subtable (assoc key-1 (cdr local-table))))
        (if subtable
            (let ((record (assoc key-2 (cdr subtable))))
              (if record
                  (cdr record)
                  false))
            false)))
    (define (insert! key-1 key-2 value)
      (let ((subtable (assoc key-1 (cdr local-table))))
        (if subtable
            (let ((record (assoc key-2 (cdr subtable))))
              (if record
                  (set-cdr! record value)
                  (set-cdr! subtable
                            (cons (cons key-2 value)
                                  (cdr subtable)))))
            (set-cdr! local-table
                      (cons (list key-1
                                  (cons key-2 value))
                            (cdr local-table)))))
      'ok)
    (define (dispatch m)
      (cond ((eq? m 'lookup-proc) lookup)
            ((eq? m 'insert-proc!) insert!)
            (else (error "Unknown operation -- TABLE" m))))
    dispatch))

{{< /highlight >}}

Take a look at my [previous post](https://www.lvguowei.me/post/sicp-goodness-mutable-data-2/) on how to implement a two dimensional table using pairs.

# Install rectangular and polar packages

Install packages sounds like fancy concept, but it is really just put procedures in the table.

{{< highlight scheme >}}

(define (install-rectangular-package)
  ;; internal procedures
  (define (real-part z) (car z))
  (define (imag-part z) (cdr z))
  (define (make-from-real-imag x y) (cons x y))
  (define (magnitude z)
    (sqrt (+ (square (real-part z))
             (square (imag-part z)))))
  (define (angle z)
    (atan (imag-part z) (real-part z)))
  (define (make-from-mag-ang r a)
    (cons (* r (cos a)) (* r (sin a))))

  ;; interface to the rest of the system
  (define (tag x) (attach-tag 'rectangular x))
  (put 'real-part '(rectangular) real-part)
  (put 'imag-part '(rectangular) imag-part)
  (put 'magnitude '(rectangular) magnitude)
  (put 'angle '(rectangular) angle)
  (put 'make-from-real-imag 'rectangular
       (lambda (x y) (tag (make-from-real-imag x y))))
  (put 'make-from-mag-ang 'rectangular
       (lambda (r a) (tag (make-from-mag-ang r a))))
  'done)

(define (install-polar-package)
  ;; internal procedures
  (define (magnitude z) (car z))
  (define (angle z) (cdr z))
  (define (make-from-mag-ang r a) (cons r a))
  (define (real-part z)
    (* (magnitude z) (cos (angle z))))
  (define (imag-part z)
    (* (magnitude z) (sin (angle z))))
  (define (make-from-real-imag x y)
    (cons (sqrt (+ (square x) (square y)))
          (atan y x)))

  ;; interface to the rest of the system
  (define (tag x) (attach-tag 'polar x))
  (put 'real-part '(polar) real-part)
  (put 'imag-part '(polar) imag-part)
  (put 'magnitude '(polar) magnitude)
  (put 'angle '(polar) angle)
  (put 'make-from-real-imag 'polar
       (lambda (x y) (tag (make-from-real-imag x y))))
  (put 'make-from-mag-ang 'polar
       (lambda (r a) (tag (make-from-mag-ang r a))))
  'done)

{{< /highlight >}}

To install a package, first define the internal procedures to be put into the table, then put them in the table under some indexes.

# Apply generic operations

Now we have the table ready, let's use it to replace the `cond`s in each generic operations. Basically now we need to be able to get the real procedure from the table based on the indexes and then apply that to the complex number object.

{{< highlight scheme >}}

(define (apply-generic op . args)
  (let ((type-tags (map type-tag args)))
    (let ((proc (get op type-tags)))
      (if proc
          (apply proc (map contents args))
          (error
           "No method for these types -- APPLY_GENERICS"
           (list op type-tags))))))

{{< /highlight >}}

Using `apply-generic`, we can define our generic operations as follows:

{{< highlight scheme >}}
(define (real-part z) (apply-generic 'real-part z))
(define (imag-part z) (apply-generic 'imag-part z))
(define (magnitude z) (apply-generic 'magnitude z))
(define (angle z) (apply-generic 'angle z))
{{< /highlight >}}

# Constructors

We can now define the generic constructors as follows:

{{< highlight scheme >}}
(define (make-from-real-imag x y)
  ((get 'make-from-real-imag 'rectangular) x y))
  
(define (make-from-mag-ang r a)
  ((get 'make-from-mag-ang 'polar) r a))
{{< /highlight >}}

Now we have seen a full example of data directed programming. I have to say this is like hand made polymorphism to me.

I think this will put a smile on you face next time you hear people say "OO's big advantage over FP is polymorphism".


