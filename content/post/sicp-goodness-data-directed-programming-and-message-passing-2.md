+++
author = "Guowei Lv"
categories = ["SICP"]
description = "Is this polymorphism?"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - Data Directed Programming (II)"
date = 2018-11-02T20:33:51+02:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

# Tagged Data

At the end of previous post, we identified one problem of our current complex number system, which is given a complex number, there is no way to tell whether it is implemented in rectangular form or polar form.

One straight forward solution would be just to tag the complex number with its own type. In order to do this, we need 3 procedures:

{{< highlight scheme >}}

;; Attach a tag
(define (attach-tag type-tag contents)
  (cons type-tag contents))
  (if (pair? datum)
      (car datum)
      (error "Bad tagged datum -- TYPE-TAG" datum)))

;; Get contents
(define (contents datum)
  (if (pair? datum)
      (cdr datum)
      (error "Bad tagged datum -- CONTENTS" datum )))

{{< /highlight >}}

Now the data has two parts, the `car` is its type, and the `cdr` is the real contents.

Using these procedures, we can define procedures to detect complex number's type.

{{< highlight scheme >}}
(define (rectangular? z)
  (eq? (type-tag z) 'rectangular))

(define (polar? z)
  (eq? (type-tag z) 'polar))
{{< /highlight >}}

Now we can rewrite the general selectors `real-part`, `imag-part`, `magnitude`, `angle` with the knowledge of type.

{{< highlight scheme >}}

(define (real-part z)
  (cond ((rectangular? z)
         (real-part-rectangular (contents z)))
        ((polar? z)
         (real-part-polar (contents z)))
        (else (error "Unknown type -- REAL-PART" z))))


(define (imag-part z)
  (cond ((rectangular? z)
         (imag-part-rectangular (contents z)))
        ((polar? z)
         (imag-part-polar (contents z)))
        (else (error "Unknown type -- IMAG-PART" z))))

(define (magnitude z)
  (cond ((rectangular? z)
         (magnitude-rectangular (contents z)))
        ((polar? z)
         (magnitude-polar (contents z)))
        (else (error "Unknown type -- MAGNITUDE" z))))

(define (angle z)
  (cond ((rectangular? z)
         (angle-rectangular (contents z)))
        ((polar? z)
         (angle-polar (contents z)))
        (else (error "Unknown type -- ANGLE" z))))

{{< /highlight >}}

I have to admit that this looks quite ... repetitive. And when we add new types, we need to add a new check in every single one of the selectors.

>Remember that in the OO world, this types of switches all over the place is the sign of needing a classic refactoring -- replace switches with polymorphism. Actually we are going to do exactly the same soon.

Here are the type-specific selectors, they are the same as we saw before, just the name is different so that for now the two set of them can coexist.

{{< highlight scheme >}}

(define (real-part-rectangular z) (car z))

(define (imag-part-rectangular z) (cdr z))

(define (magnitude-rectangular z)
  (sqrt (+ (square (real-part z)) (square (imag-part z)))))

(define (angle-rectangular z)
  (atan (imag-part z) (real-part z)))

(define (real-part-polar z)
  (* (magnitude z) (cos (angle z))))

(define (imag-part-polar z)
  (* (magnitude z) (sin (angle z))))

(define (magnitude-polar z)
  (car z))

(define (angle-polar z)
  (cdr z))
  
(define (make-from-mag-ang r a)
  (cons (* r (cos a)) (* r (sin a))))
  
{{< /highlight >}}

And here are the type-specific constructors, they are the same as before, except now there will be tags attached to the results.

{{< highlight scheme >}}

(define (make-from-real-imag-rectangular x y)
  (attach-tag 'rectangular (cons x y)))

(define (make-from-mag-ang-rectangular r a)
  (attach-tag 'rectangular
              (cons (* r (cos a)) (* r (sin a)))))

(define (make-from-real-imag-polar x y)
  (attach-tag 'polar
              (cons (sqrt (+ (square x) (square y)))
                    (atan y x))))

(define (make-from-mag-ang-polar r a)
  (attach-tag 'polar (cons r a)))

{{< /highlight >}}

We can now implement generic constructors simply as:

{{< highlight scheme >}}
(define (make-from-real-imag x y)
  (make-from-real-imag-rectangular x y))
  
(define (make-from-mag-ang r a)
  (make-from-mag-ang-polar r a))
{{< /highlight >}}

Notice that the `cond`s in the generic selectors are behaving like some sort of managers, they just dispatch work to other places and they themselves do not do any real work(which is not an uncommon problem unfortunately). It is quite annoying that the bottleneck of the system is in these managers. 

In the next post, we learn how to replace these managers with an intelligent system. Stay tuned!
