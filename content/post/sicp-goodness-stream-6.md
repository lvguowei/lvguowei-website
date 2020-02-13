+++
categories = ["SICP"]
keywords = ["SICP", "functional programming", "structure and interpretation of computer programs"]
description = "Infinite Streams"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - Stream (6)"
date = 2019-04-06T20:31:46+03:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

Let's do some exercises.

**Exercise 3.53**: Without running the program, describe the elements of the stream defined by
{{< highlight scheme >}}
(define s (cons-stream 1 (add-streams s s)))
{{< /highlight >}}

This is an easy one. Let me illustrate the answer below.

{{< highlight scheme >}}
;; The stream starts with 1
1 . . . ...

;; The rest of the stream is by adding it to itself
  1 . . . ...
1 . . . . ...
  1 . . . ...
  
;; Now we know the second value in the stream is 2
  1 2 . . ...
1 2 . . . ...
  1 2 . . ...

;; Now we know the third value is 4
  1 2 4 . ...
1 2 4 . . ...
  1 2 4 . ...
  
;; The stream is 1 2 4 8 16 32 ...
{{< /highlight >}}

<hr />

**Exercise 3.54**: Define a procedure `mul-streams`, analogous to `add-streams`, that produces the elementwise product of its two input streams. Use this together with the stream of integers to complete the following definition of the stream whose nth element (counting from 0) is n+1 factorial:

{{< highlight scheme >}}
(define factorials 
  (cons-stream 1 (mul-streams ⟨??⟩ ⟨??⟩)))
{{< /highlight >}}

The anwser is also not hard to find.

{{< highlight scheme >}}

(define (mul-streams s1 s2)
  (stream-map * s1 s2))

(define factorials
  (cons-stream 1 (mul-streams factorials (stream-cdr integers))))
{{< /highlight >}}

The answer can be illustrated as follows.

{{< highlight scheme >}}
;; the final stream is
1! 2! 3! 4! 5! . . . 

;; which can be rewritten as
1 1!x2 2!x3 3!x4 4!x5 . . .

;; note that the cdr of the stream can be viewed as the multiplication of two streams
1! 2! 3! 4! 5! ... ;; this is the final stream itself
2  3  4  5  6  ... ;; this is the integer stream starting from 2
{{< /highlight >}}

<hr />

**Exercise 3.55**: Define a procedure `partial-sums` that takes as argument a stream `S` and returns the stream whose elements are S0, S0+S1, S0+S1+S2, ...
For example, `(partial-sums integers)` should be the stream 1, 3, 6, 10, 15, ...

Got the hang of this game? OK, the answer:

{{< highlight scheme >}}
(define (partial-sum s)
  (cons-stream (stream-car s)
               (add-streams (stream-cdr s)
                            (partial-sum s))))
{{< /highlight >}}

Let's also illustrate this:

{{< highlight scheme >}}
;; S      = S0    S1      S2           S3            ...
;; Result = S0    S0+S1   S0+S1+S2     S0+S1+S2+S3   ...
{{< /highlight >}}

Squeeze your eyes and you should see the pattern.

**Exercise 3.56**: A famous problem, first raised by R. Hamming, is to enumerate, in ascending order with no repetitions, all positive integers with no prime factors other than 2, 3, or 5. One obvious way to do this is to simply test each integer in turn to see whether it has any factors other than 2, 3, and 5. But this is very inefficient, since, as the integers get larger, fewer and fewer of them fit the requirement. As an alternative, let us call the required stream of numbers **S** and notice the following facts about it.

- **S** begins with 1.
- The elements of `(scale-stream S 2)` are also elements of **S**.
- The same is true for `(scale-stream S 3)` and `(scale-stream S 5)`.

These are all the elements of **S**.
Now all we have to do is combine elements from these sources. For this we define a procedure `merge` that combines two ordered streams into one ordered result stream, eliminating repetitions:

{{< highlight scheme >}}

(define (merge s1 s2)
  (cond ((stream-null? s1) s2)
        ((stream-null? s2) s1)
        (else
         (let ((s1car (stream-car s1))
               (s2car (stream-car s2)))
           (cond ((< s1car s2car)
                  (cons-stream 
                   s1car 
                   (merge (stream-cdr s1) 
                          s2)))
                 ((> s1car s2car)
                  (cons-stream 
                   s2car 
                   (merge s1 
                          (stream-cdr s2))))
                 (else
                  (cons-stream 
                   s1car
                   (merge 
                    (stream-cdr s1)
                    (stream-cdr s2)))))))))
{{< /highlight >}}
                    
Then the required stream may be constructed with merge, as follows:

{{< highlight scheme >}}
(define S (cons-stream 1 (merge ⟨??⟩ ⟨??⟩)))
{{< /highlight >}}

Fill in the missing expressions in the places marked ⟨??⟩ above.

This exercise looks daunting, but it is the easiest one. I include this one mainly for the introduction of the `merge` function.

{{< highlight scheme >}}
(define S (cons-stream 1 (merge (merge (scale-stream S 2) (scale-stream S 3)) (scale-stream S 5))))
{{< /highlight >}}

By now, I could not understand fully  how all these would eventually work out. I mean the laziness combined with recursion. 
We are at a point that by simply writing down the correct formula is not enough to know if the program will make sense and run.
