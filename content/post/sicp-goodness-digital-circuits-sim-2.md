+++
author = "Guowei Lv"
categories = ["SICP"]
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - A Simulator for Digital Circuits (II)"
date = 2018-12-15T12:26:36+02:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

## Agenda

Agenda is a data structure, that contains a schedule of things to do.

This is best explained using an example.

{{< figure src="/img/agenda.png" >}}

The Agenda is essentially just a list.

The first element is the current time in the simulation. In this example, 5.

Then the `cdr` of the list represents things to do at different time in the future. For example in this example, at time 6, 10 and 13 there are *queues* of things to be done.

## Interfaces

We can now define operations for the agenda.

# Make a new empty agenda

{{< highlight scheme >}}
(define (make-agenda) (list 0))
{{< /highlight >}}

# Test empty agenda
{{< highlight scheme >}}

(define (empty-agenda? agenda)
  (null? (segments agenda)))
  
(define (segments agenda) (cdr agenda))
  
{{< /highlight >}}

# Get the first item on the agenda

{{< highlight scheme >}}

(define (first-agenda-item agenda)
  (if (empty-agenda? agenda)
      (error "Agenda is empty -- FIRST-AGENDA_ITEM")
      (let ((first-seg (first-segment agenda)))
        (set-current-time! agenda (segment-time first-seg))
        (front-queue (segment-queue first-seg)))))
        
(define (first-segment agenda)
  (car (segments agenda)))
  
(define (set-current-time! agenda time)
  (set-car! agenda time))
  
(define (segment-queue s) (cdr s))

{{< /highlight >}}

# Remove the first item on the agenda

{{< highlight scheme >}}

(define (remove-first-agenda-item! agenda)
  (let ((q (segment-queue (first-segment agenda))))
    (delete-queue! q)
    (if (empty-queue? q)
        (set-segments! agenda (rest-segments agenda)))))
        
(define (set-segments! agenda segments)
  (set-cdr! agenda segments))
  
(define (rest-segments agenda)
  (cdr (segments agenda)))
{{< /highlight >}}

# Adding the given action procedure to be run at the specified time

{{< highlight scheme >}}

(define (add-to-agenda! time action agenda)
  (define (belongs-before? segments)
    (or (null? segments)
        (< time
           (segment-time (car segments)))))
  (define (make-new-time-segment time action)
    (let ((q (make-queue)))
      (insert-queue! q action)
      (make-time-segment time q)))
  (define (add-to-segments! segments)
    (if (= (segment-time (car segments)) time)
        (insert-queue!
         (segment-queue (car segments))
         action)
        (let ((rest (cdr segments)))
          (if (belongs-before? rest)
              (set-cdr!
               segments
               (cons (make-new-time-segment
                      time
                      action)
                     (cdr segments)))
              (add-to-segments! rest)))))
  (let ((segments (segments agenda)))
    (if (belongs-before? segments)
        (set-segments!
         agenda
         (cons (make-new-time-segment
                time
                action)
               segments))
        (add-to-segments! segments))))

{{< /highlight >}}

# Get the current simulation time

{{< highlight scheme >}}
(define (current-time agenda) (car agenda))
{{< /highlight >}}

It may look like a lot of stuff here, but if you have the structure of the agenda in mind and follow the code slowly, it is actually quite straightforward.

## Propagate 

The simulation is driven by the procedure `propagate`, which executing each procedure on the agenda in sequence.

{{< highlight scheme >}}

(define (propagate)
  (if (empty-agenda? the-agenda)
      'done
      (let ((first-item
             (first-agenda-item the-agenda)))
        (first-item)
        (remove-first-agenda-item! the-agenda)
        (propagate))))

{{< /highlight >}}


## Test everything works

{{< highlight scheme >}}

;; Define a global the-agenda
(define the-agenda (make-agenda))

;; Test that the-agenda is empty
(empty-agenda? the-agenda)
;Value: #t

;; The current time should be 0
(current-time the-agenda)
;Value: 0

;; Add a procedure to execute at time 1
(add-to-agenda! 1 (lambda() (display "Wake up!")) the-agenda)

;; Add another procedure to execute at time 1
(add-to-agenda! 1 (lambda() (display "Wake up again!")) the-agenda)

;; Add a procedure to execute at time 2
(add-to-agenda! 2 (lambda() (display "Go to school!")) the-agenda)

;; At this point, there should be two queues in the-agenda, one for time 1 and one for time 2, let's examine the queue for time 1
(print-queue (cdr (first-segment the-agenda)))
;(#[compound-procedure 14] #[compound-procedure 17])

;; Time to propagate!
(propagate)
;Wake up! Wake up again! Go to school!

;; After propagate, let's check the current time

(current-time the-agenda)
;Value: 2

{{< /highlight >}}



