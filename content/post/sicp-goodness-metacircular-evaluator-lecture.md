+++
author = "Guowei Lv"
categories = ["SICP"]
description = "Does the code from the lecture video work?"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - Try-out the meta-circular evaluator from the lecture"
date = 2020-11-20T22:19:03+02:00
+++
>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

In **Lecture 7A: Metacircular Evaluator, Part 1**, Mr Sussman said that he was going to fit the *whole evaluator* on the blackboard. I followed the lecture and typed in all the code, it did not run of course. But I think we are not too far from it.

So I think, hmmm, can I add in what is missing and make it run? Let's try!

First, the lecture video if you want to refresh your memory.

{{< youtube id="aAlR3cezPJg" autoplay="false" >}}

I found out that there are only 2 small pieces that were missing in order to make the code run:

1. Definition of `apply-primop`. The function for applying primitive operations.
2. Definition of `env0`. The environment that the computer is born with.

We can actually steal these two pieces from the book.
Let's see the code.


{{< highlight scheme>}}
;; The missing function for applying primitive functions
(define apply-primop apply)

;; ---- [START] code from lecture ----
(define eval
  (lambda (exp env)
    (cond ((number? exp) exp)
          ((symbol? exp) (lookup exp env))
          ((eq? (car exp) 'quote) (cadr exp))
          ((eq? (car exp) 'lambda) (list 'closure (cdr exp) env))
          ((eq? (car exp) 'cond) (eval-cond (cdr exp) env))
          (else (apply (eval (car exp) env)
                       (evlist (cdr exp) env))))))

(define apply
  (lambda (proc args)
    (cond ((primitive? proc)
           (apply-primop (cdr proc) args))
          ((eq? (car proc) 'closure)
           (eval (cadadr proc)
                 (bind (caadr proc) args (caddr proc))))
          (else 'error))))

(define primitive?
  (lambda (proc)
    (eq? (car proc) 'primitive)))

(define evlist
  (lambda (l env)
    (cond ((eq? l '()) '())
          (else (cons (eval (car l) env)
                      (evlist (cdr l) env))))))

(define eval-cond
  (lambda (clauses env)
    (cond ((eq? clauses '()) '())
          ((eq? (caar clauses) 'else)
           (eval (cadar clauses) env))
          ((false? (eval (caar clauses) env))
           (eval-cond (cdr clauses) env))
          (else
           (eval (cadar clauses) env)))))

(define bind
  (lambda (vars vals env)
    (cons (pair-up vars vals) env)))

(define pair-up
  (lambda (vars vals)
    (cond
     ((eq? vars '())
      (cond ((eq? vals '()) '())
            (else (error 'TMA))))
     ((eq? vals '()) (error 'TFA))
     (else
      (cons (cons (car vars)
                  (car vals))
            (pair-up (cdr vars)
                     (cdr vals)))))))

(define lookup
  (lambda (sym env)
    (cond ((eq? env '()) (error 'UBV))
          (else
           ((lambda (vcell)
              (cond ((eq? vcell '())
                     (lookup sym (cdr env)))
                    (else (cdr vcell))))
            (assq sym (car env)))))))

(define assq
  (lambda (sym alist)
    (cond ((eq? alist '()) '())
          ((eq? sym (caar alist))
           (car alist))
          (else
           (assq sym (cdr alist))))))

;; ---- [END] code from lecture ----

;; Definition of environment 0
;; You can add all the primitive functions here
(define env0 (list (list (cons '+ (cons 'primitive +))
                         (cons '- (cons 'primitive -))
                         (cons 'cons (cons 'primitive cons))
                         (cons 'cdr (cons 'primitive cdr))
                         (cons '* (cons 'primitive *))
                         (cons '> (cons 'primitive >))
                         (cons '= (cons 'primitive =))
                         (cons '< (cons 'primitive <)))))
{{< /highlight >}}

Let's try to evaluate something.

{{< highlight scheme>}}

1 ]=> (eval 1 env0)

;Value: 1

1 ]=> (eval '(quote x) env0)

;Value: x

1 ]=> (eval '(cond ((> 1 2) 1) ((= 1 1) 2)) env0)

;Value: 2

1 ]=> (eval '((lambda (x) (+ x 1)) 3) env0)

;Value: 4

1 ]=> (eval '(((lambda (x) (lambda (y) (+ x y))) 3) 4) env0)

;Value: 7

{{< /highlight >}}

Tada! All is working!
