+++
categories = ["SICP"]
keywords = ["SICP", "functional programming", "structure and interpretation of computer programs"]
description = "More exercise"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - Stream (4)"
date = 2019-03-14T20:49:46+02:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

Let's do one more exercise.

**Exercise 3.51**: In order to take a closer look at delayed evaluation, we will use the following procedure, which simply returns its argument after printing it:
{{< highlight scheme >}}
(define (show x)
  (display-line x)
  x)
{{< /highlight >}}

What does the interpreter print in response to evaluating each expression in the following sequence?
{{< highlight scheme >}}
(define x 
  (stream-map 
   show 
   (stream-enumerate-interval 0 10)))
(stream-ref x 5)
(stream-ref x 7)
{{< /highlight >}}

This is easy, let's just type the code into REPL and see what happens.




{{< highlight scheme >}}
1 ]=> (define x (stream-map show (stream-enumerate-interval 0 10)))
0
;Value: x

1 ]=> (stream-ref x 5)
12345
;Value: 5

1 ]=> (stream-ref x 7)
67
;Value: 7

{{< /highlight >}}

Done. But I want to follow the execution step by step and see exactly what is happening. So let's do it.

Starting with the first line of code:

{{< highlight scheme >}}
(define x (stream-map show (stream-enumerate-interval 0 10)))
{{< /highlight >}}

Here is what happens by typing in that line of code:

{{< highlight scheme >}}
(define x (stream-map show (stream-enumerate-interval 0 10)))

; First stream-enumerate-interval gets evaluated
(define x (stream-map 
            show 
            (cons-stream 
              0 
              (stream-enumerate-interval 1 10))))

; Expand cons-stream since it is a macro
(define x (stream-map 
            show 
            (cons 
              0 
              (delay (stream-enumerate-interval 1 10)))))

; Expand delay since it is a macro
(define x (stream-map 
            show 
            (cons 
              0 
              (memo-proc (lambda () (stream-enumerate-interval 1 10))))))

; We should expand memo-proc here but since it is rather long, expand it in your mind please
; Expand stream-map
(define x (cons-stream 
            (show 0) 
            (stream-map 
              show 
              (stream-cdr 
                (cons 
                  0 
                  (memo-proc (lambda () (stream-enumerate-interval 1 10))))))))

; Expand cons-stream since it is macro
(define x (cons 
            (show 0) 
            (delay (stream-map 
                     show 
                     (stream-cdr (cons 
                                   0 
                                   (memo-proc (lambda () (stream-enumerate-interval 1 10)))))))))

; Expand delay
(define x (cons
            (show 0) 
            (memo-proc (lambda () 
                         (stream-map 
                           show 
                           (stream-cdr (cons 
                                         0 
                                         (memo-proc (lambda () (stream-enumerate-interval 1 10))))))))))
; print 0 here
(define x (cons
            0
            (memo-proc (lambda () 
                         (stream-map 
                           show 
                           (stream-cdr (cons 
                                         0 
                                         (memo-proc (lambda () (stream-enumerate-interval 1 10))))))))))
{{< /highlight >}}

Now the second line

{{< highlight scheme >}}
(stream-ref x 5)

; Put in x
(stream-ref (cons
              0
              (memo-proc (lambda () 
                           (stream-map 
                             show 
                             (stream-cdr (cons 
                                           0 
                                           (memo-proc (lambda () (stream-enumerate-interval 1 10)))))))))
            5)

; Expand stream-ref
(stream-ref (stream-cdr (cons
                          0
                          (memo-proc (lambda () 
                                       (stream-map 
                                         show 
                                         (stream-cdr (cons 
                                                       0 
                                                       (memo-proc (lambda () (stream-enumerate-interval 1 10))))))))))
            4)

; Expand stream-cdr and then force
(stream-ref ((memo-proc (lambda () 
                          (stream-map 
                            show 
                            (stream-cdr (cons 
                                          0 
                                          (memo-proc (lambda () (stream-enumerate-interval 1 10)))))))))
            4)

; Expand memo-proc
(stream-ref (stream-map 
              show 
              (stream-cdr (cons 
                            0 
                            (memo-proc (lambda () (stream-enumerate-interval 1 10))))))
            4)
            
; Expand stream-cdr
(stream-ref (stream-map 
              show 
              ((memo-proc (lambda () (stream-enumerate-interval 1 10)))))
            4)
            
; Expand memo-proc
(stream-ref (stream-map 
              show 
              (stream-enumerate-interval 1 10))
            4)
; Expand stream-enumerate-interval
(stream-ref (stream-map 
              show
              (cons-stream 1 (stream-enumerate-interval 2 10)))
            4)
            
; Expand cons-stream
(stream-ref (stream-map 
              show
              (cons 1 (memo-proc (lambda () (stream-enumerate-interval 2 10)))))
            4)

; Expand stream-map
(stream-ref (cons-stream 
              (show 1) 
              (stream-map 
                show 
                (stream-cdr (cons 1 (memo-proc (lambda () (stream-enumerate-interval 2 10)))))))
            4)
            
; Expand cons-stream
(stream-ref (cons 
              (show 1)
              (memo-proc (lambda () (stream-map 
                                      show 
                                      (stream-cdr (cons 
                                                    1 
                                                    (memo-proc (lambda () (stream-enumerate-interval 2 10)))))))))
            4)
            
; Print 1 here
(stream-ref (cons 
              1
              (memo-proc (lambda () (stream-map 
                                      show 
                                      (stream-cdr (cons 
                                                    1 
                                                    (memo-proc (lambda () (stream-enumerate-interval 2 10)))))))))
            4)
; Expand stream-ref
(stream-ref (stream-cdr (cons 
                          1
                          (memo-proc (lambda () (stream-map 
                                                  show 
                                                  (stream-cdr (cons 
                                                                1 
                                                                (memo-proc (lambda () (stream-enumerate-interval 2 10))))))))))
            3)
            
; Expand stream-cdr
(stream-ref ((memo-proc (lambda () (stream-map 
                                     show 
                                     (stream-cdr (cons 
                                                   1 
                                                   (memo-proc (lambda () (stream-enumerate-interval 2 10)))))))))
            3)

; Execute memo-proc
(stream-ref (stream-map 
              show 
              (stream-cdr (cons 
                            1 
                            (memo-proc (lambda () (stream-enumerate-interval 2 10))))))
            3)
            
; Expand stream-cdr
(stream-ref (stream-map 
              show 
              ((memo-proc (lambda () (stream-enumerate-interval 2 10)))))
            3)
            
; Execute memo-proc
(stream-ref (stream-map 
              show 
              (stream-enumerate-interval 2 10))
            3)

; Expand stream-enumerate-interval
(stream-ref (stream-map 
              show 
              (cons-stream 2 (stream-enumerate-interval 3 10)))
            3)

; Expand cons-stream
(stream-ref (stream-map 
              show 
              (cons 2 (memo-proc (lambda () (stream-enumerate-interval 3 10)))))
            3)

; Expand stream-map
(stream-ref (cons-stream 
              (show 2) 
              (stream-map 
                show 
                (stream-cdr (cons 
                              2 
                              (memo-proc (lambda () (stream-enumerate-interval 3 10)))))))
            3)

; Expand cons-stream
(stream-ref (cons 
              (show 2) 
              (memo-proc (lambda () (stream-map 
                                      show 
                                      (stream-cdr (cons 
                                                    2 
                                                    (memo-proc (lambda () (stream-enumerate-interval 3 10)))))))))
            3)

; Print 2 here
(stream-ref (cons 
              2
              (memo-proc (lambda () (stream-map 
                                      show 
                                      (stream-cdr (cons 
                                                    2 
                                                    (memo-proc (lambda () (stream-enumerate-interval 3 10)))))))))
            3)

; Let's speed up

; Print 3 here
(stream-ref (cons 
              3
              (memo-proc (lambda () (stream-map 
                                      show 
                                      (stream-cdr (cons 
                                                    3 
                                                    (memo-proc (lambda () (stream-enumerate-interval 4 10)))))))))
            2)

; Print 4 here
(stream-ref (cons 
              4
              (memo-proc (lambda () (stream-map 
                                      show 
                                      (stream-cdr (cons 
                                                    4 
                                                    (memo-proc (lambda () (stream-enumerate-interval 5 10)))))))))
            1)
            
; Print 5 here
(stream-ref (cons 
              5
              (memo-proc (lambda () (stream-map 
                                      show 
                                      (stream-cdr (cons 
                                                    5
                                                    (memo-proc (lambda () (stream-enumerate-interval 6 10)))))))))
            0)

; Expand stream-ref
(stream-car (cons 
              5
              (memo-proc (lambda () (stream-map 
                                      show 
                                      (stream-cdr (cons 
                                                    5
                                                    (memo-proc (lambda () (stream-enumerate-interval 6 10)))))))))
            0)
            
; Expand stream-car
5

{{< /highlight >}}

Phew! If you are still awake and reading here, maybe you should try to evaluate the third line of code. I have to go to sleep now so see you next time.
