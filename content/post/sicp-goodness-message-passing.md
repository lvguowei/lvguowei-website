+++
categories = ["SICP"]
description = "One useful programming idea"
keywords = ["SICP", "message passing"]
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - Message Passing"
date = 2018-09-30T13:40:07+03:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

In this article, we will see how to construct an *object* that has internal states by using *message passing*.

The object we are going to create is a bank account. Each bank account has its balance, and we can *deposit* or *withdraw* from it.

{{< highlight scheme >}}

(define (make-account balance)
  (define (withdraw amount)
    (if (>= balance amount)
        (begin (set! balance (- balance amount))
               balance)
        "Insufficient funds"))
  (define (deposit amount)
    (set! balance (+ amount balance))
    balance)
  (define (dispatch m)
    (cond ((eq? m 'withdraw) withdraw)
          ((eq? m 'deposit) deposit)
          (else (error "Unknown request -- MAKE-ACCOUNT" m))))
  dispatch)
  
{{< /highlight >}}

The `make-account` is a *constructor* function, it returns a `dispatch` procedure. This `dispatch` procedure provides ways to manipulate the inner state `balance` of the object.

Let's try it out.

{{< highlight scheme >}}

(define acc (make-account 100))
;Value: acc

((acc 'withdraw) 50)
;Value: 50

((acc 'withdraw) 60)
;Value 17: "Insufficient funds"

((acc 'deposit) 20)
;Value: 70

{{< /highlight >}}

The implementation of the *account object* feels very like modern Object-Oriented languages like Java. This is a great example showing that **good programming ideas are what really important, not some particular implementations in particular languages**.

While we are at it, let's also do some exercises from the book.

# Exercise 3.3

Modify the `make-account` procedure so that it creates password-protected accounts. That is, `make-account` should take a symbol as an additional argument, as in

`(define acc (make-account 100 'secret-password))`

The resulting account object should process a request only if it is accompanied by the password with which the account was created, and should otherwise return a complaint:

`((acc 'secret-password 'withdraw) 40)` <br />
60


`((acc 'some-other-password 'withdraw) 40)` <br />
"Incorrect password"

<hr/>

{{< highlight scheme >}}

(define (make-account balance password)
  (define (withdraw amount)
    (if (>= balance amount)
        (begin (set! balance (- balance amount))
               balance)
        "Insufficient funds"))
  (define (deposit amount)
    (set! balance (+ amount balance))
    balance)
  (define (dispatch pwd m)
    (if (eq? password pwd)
        (cond ((eq? m 'withdraw) withdraw)
              ((eq? m 'deposit) deposit)
              (else (error "Unknown request -- MAKE-ACCOUNT" m)))
        (lambda (amount) "Incorrect password")))
  dispatch)

{{< /highlight >}}


# Exercise 3.4

Modify the `make-account` procedure by adding another local state variable so that, if an account is accessed more than seven consecutive times with an incorrect password, it invokes the procedure `call-the-cops`.

<hr />

{{< highlight scheme >}}

(define (make-account balance password)
  (let ((counter 0))
    (define (withdraw amount)
      (if (>= balance amount)
          (begin (set! balance (- balance amount))
                 balance)
          "Insufficient funds"))
    (define (deposit amount)
      (set! balance (+ amount balance))
      balance)
    (define (call-the-cops)
      (display "Call the cops!"))
    (define (dispatch pwd m)
      (if (eq? password pwd)
          (begin (set! counter 0)
                 (cond ((eq? m 'withdraw) withdraw)
                       ((eq? m 'deposit) deposit)
                       (else (error "Unknown request -- MAKE-ACCOUNT" m))))
          (begin
            (if (< counter 7)
                (set! counter (+ counter 1))
                (call-the-cops))
            (lambda (amount) "Incorrect password"))))
    dispatch))
    
{{< /highlight >}}
    
# Exercise 3.7

Consider the bank account objects created by `make-account`, with the password modification described in exercise 3.3. Suppose that our banking system requires the ability to make joint accounts. Define a procedure `make-joint` that accomplishes this. `Make-joint` should take three arguments. The first is a password-protected account. The second argument must match the password with which the account was defined in order for the `make-joint` operation to proceed. The third argument is a new passwrd. `Make-joint` is to create an additional access to the original account using the new password.
 
{{< highlight scheme >}}

(define (make-join acc old-pwd new-pwd)
  (lambda (pwd msg)
    (if (eq? pwd new-pwd)
        (acc old-pwd msg)
        (lambda (amount) "Incorrect password"))))

{{< /highlight >}}
