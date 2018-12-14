+++
author = "Guowei Lv"
categories = ["SICP"]
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - A Simulator for Digital Circuits (I)"
date = 2018-12-14T20:45:49+02:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**


We are building a simulator program for digital circuits, using the Object Oriented view of the world, in Scheme Lisp.

This is probably the most complicated example program in the book I have ever seen by far. But it is also beautifully constructed. In this program, we build everything ourselves, including the data structures. And in the video lecture, the professor claimed that this is the basis of a real world circuits simulator, of course very much simplified.

In the book, the author used a top down approach to first show the high level structure and then go into details. But I will take a bottom up approach in this article, I will go through the details first, just to be different.

Let's get started.

## Queue

First thing first, we need to build a **queue** data structure from scratch. Yes, the good old FIFO (first in first out) queue.

# What it looks like

{{< figure src="/img/queue.png" >}}

A queue is just a list with two more pointers: front pointer and rear pointer. You can probably already imagine what they are for. The front pointer is for easier deletion from the queue, and the rear pointer is for easier insertion into the queue.

# Interfaces
There are 4 operations on a queue:

{{< highlight scheme >}}

(define (empty-queue? queue)
  (null? (car queue)))

(define (front-queue queue)
  (if (empty-queue? queue)
      (error "FRONT called with an empty queue" queue)
      (car (front-ptr queue))))

(define (insert-queue! queue item)
  (let ((new-pair (cons item '())))
    (cond ((empty-queue? queue)
           (set-front-ptr! queue new-pair)
           (set-rear-ptr! queue new-pair)
           queue)
          (else
           (set-cdr! (rear-ptr queue) new-pair)
           (set-rear-ptr! queue new-pair)
           queue))))

(define (delete-queue! queue)
  (cond ((empty-queue? queue)
         (error "DELETE! called with an empty queue" queue))
        (else
         (set-front-ptr! queue (cdr (front-ptr queue)))
         queue)))

{{< /highlight >}}

Plus some helper functions:

{{< highlight scheme >}}

(define (front-ptr queue) (car queue))

(define (rear-ptr queue) (cdr queue))

(define (set-front-ptr! queue item) (set-car! queue item))

(define (set-rear-ptr! queue item) (set-cdr! queue item))

{{< /highlight >}}

And a constructor:

{{< highlight scheme >}}

(define (make-queue) (cons '() '()))

{{< /highlight >}}

One interesting helper function is `print-queue`, it prints the queue in a more human friendly way:

{{< highlight scheme >}}

(define (print-queue queue)
  (if (empty-queue? queue)
      (display 'done)
      (display (front-ptr queue)))
  'done)

{{< /highlight >}}

## Test

Now we can test that our queue actually works:

{{< highlight scheme >}}

1 ]=> (define q (make-queue))

;Value: q

1 ]=> (print-queue q)
done
;Value: done

1 ]=> (insert-queue! q 1)

;Value 13: ((1) 1)

1 ]=> (print-queue q)
(1)
;Value: done

1 ]=> (insert-queue! q 2)

;Value 13: ((1 2) 2)

1 ]=> (print-queue q)
(1 2)
;Value: done

1 ]=> (insert-queue! q 3)

;Value 13: ((1 2 3) 3)

1 ]=> (print-queue q)
(1 2 3)
;Value: done

1 ]=> (delete-queue! q)

;Value 13: ((2 3) 3)

1 ]=> (print-queue q)
(2 3)
;Value: done

1 ]=> (front-queue q)

;Value: 2

{{< /highlight >}}
