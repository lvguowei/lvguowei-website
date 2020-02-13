+++
categories = ["SICP"]
keywords = ["SICP", "functional programming", "structure and interpretation of computer programs"]
description = "Doing some exercises"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - Stream (3)"
date = 2019-03-13T14:55:47+02:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

To test our knowledge, let's do some exercises from the book.

**Exercise 3.50**: Complete the following definition, which generalizes stream-map to allow procedures that take multiple arguments, analogous to map in 2.2.1, Footnote 12.

{{< highlight scheme >}}
(define (stream-map proc . argstreams)
  (if (⟨??⟩ (car argstreams))
      the-empty-stream
      (⟨??⟩
       (apply proc (map ⟨??⟩ argstreams))
       (apply stream-map
              (cons proc 
                    (map ⟨??⟩ 
                         argstreams))))))
{{< /highlight >}}

First of all, let's look at *footnote 12*:

>Scheme standardly provides a map procedure that is more general than the one described here. This more general map takes a procedure of n arguments, together with n lists, and applies the procedure to all the first elements of the lists, all the second elements of the lists, and so on, returning a list of the results. For example:
{{< highlight scheme >}}
(map + 
     (list 1 2 3) 
     (list 40 50 60) 
     (list 700 800 900))
(741 852 963)

(map (lambda (x y) (+ x (* 2 y)))
     (list 1 2 3)
     (list 4 5 6))
(9 12 15)
{{< /highlight >}}

Let's draw a picture to illustrate this:

{{< img-post "/img" "sicp-stream-3-1.jpg" "" "center" >}}

This is like, for each parameter of the function, you feed in a list of values. The result of the map is just a list of outcomes from those executions.

Having said that, let me present the anwser of the exercise.
{{< highlight scheme >}}
(define (stream-map proc . argstreams)
  (if (null? (car argstreams))
      '()
      (cons-stream
       (apply proc (map stream-car argstreams))
       (apply stream-map
              (cons proc (map stream-cdr argstreams))))))
{{< /highlight >}}

There are several things that you have to understand first in order to understand what it is all about.

First, the *dot* in the function definition. This basically means put everything coming after the *dot* in a list called *argstreams*. Let's try it out:

{{< highlight scheme >}}
(define (try-out arg1 . arglist)
  (display arglist))
  
(try-out "first-arg" 1 2 3 4 5)
;(1 2 3 4 5)
{{< /highlight >}}

The next thing to understand is `apply`. If you know javascript, this should be very familiar to you. Basically, `apply` is used to call a function. Sometimes you have all the arguments as a list, and when you call the function you want to somehow using the list directly. For example:

{{< highlight scheme >}}
(define params (list 1 2 3))

params
;Value: (1 2 3)

(apply + params)
;Value: 6
{{< /highlight >}}

Now you should be able to understand the answer with no problems.

The next exercise **Exercise 3.51** is tricker than I thought if you want to fully understand what is going on, so I will take time to explain things step by step in next post. Stay tuned.
