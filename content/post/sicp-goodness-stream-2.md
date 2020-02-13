+++
categories = ["SICP"]
keywords = ["SICP", "lambda calculus", "structure and interpretation of computer programs"]
description = "Some util functions on stream"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - Stream (2)"
date = 2019-03-10T13:37:47+02:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

In [previous article](https://www.lvguowei.me/post/sicp-goodness-stream-1/), we implemented the very basics of stream: `cons-stream`, `stream-car` and `stream-cdr`, now we can implement some util functions based on those.

## `stream-ref`
This function just returns the *nth* element of the stream.
{{< highlight scheme >}}
(define (stream-ref s n)
  (if (= n 0)
      (stream-car s)
      (stream-ref (stream-cdr s) (- n 1))))
{{< /highlight >}}

## `stream-map`
This is similar to `map`on list.
{{< highlight scheme >}}
(define (stream-map proc s)
  (if (null? s)
      '()
      (cons-stream (proc (stream-car s)) (stream-map proc (stream-cdr s)))))
{{< /highlight >}}

## `stream-for-each`
This function simply applies the `proc` on every items in the stream.
{{< highlight scheme >}}
(define (stream-for-each proc s)
  (if (null? s)
      'done
      (begin (proc (stream-car s))
             (stream-for-each proc (stream-cdr s)))))
{{< /highlight >}}

## `display-stream`
This function prints every items in the stream.

{{< highlight scheme >}}
(define (display-line x)
  (newline)
  (display x))

(define (display-stream s)
  (stream-for-each display-line s))
{{< /highlight >}}

## `stream-enumerate-interval`
This function creates a stream from an interval.
{{< highlight scheme >}}
(define (stream-enumerate-interval low high)
  (if (> low high)
      '()
      (cons-stream
       low
       (stream-enumerate-interval (+ low 1) high))))
{{< /highlight >}}

Now we can test what we have now a little bit:

{{< highlight scheme >}}
; This should display numbers o1 - 10
(display-stream (stream-enumerate-interval 1 10))
1
2
3
4
5
6
7
8
9
10
{{< /highlight >}}

## `stream-filter`
This is similar to `filter` for list.
{{< highlight scheme >}}
(define (stream-filter pred stream)
  (cond ((null? stream) '())
        ((pred (stream-car stream))
         (cons-stream (stream-car stream)
                      (stream-filter pred (stream-cdr stream))))
        (else (stream-filter pred (stream-cdr stream)))))

{{< /highlight >}}

To show that everything works, we can do:

{{< highlight scheme >}}
(display-stream
 (stream-filter
  (lambda (x) (= (remainder x 2) 0))
  (stream-map
   (lambda (x) (* x 2))
   (stream-enumerate-interval 1 10))))

2
4
6
8
10
12
14
16
18
20
{{< /highlight >}}

To be continued ...
