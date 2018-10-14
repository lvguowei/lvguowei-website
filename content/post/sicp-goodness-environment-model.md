+++
categories = ["SICP"]
description = "The environment model of evaluation"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - The Environment Model"
date = 2018-10-08T19:53:20+03:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

Before introducing *assignment operation* `set!`, we can use the **substitution model** of evaluation.

For example, 

{{< highlight scheme >}}
(define (square x) (* x x))

(square 2)
{{< /highlight >}}

To apply the function `square` to the value 2, replace the variable `x`in the body of the function with 2, we get `(* 2 2)`.

Notice that substitution happened here is not equal to *assign x with value 2* in any C like languages. What substitution here means is that x now **is** the value 2. You cannot change x anymore just like you can not change the value 2.

**So before assignment, a variable is merely a name for a value.**

But everything changed after we introduced the assignment operation. Now we have to deal with time and identity and answer the philosophical questions like *Am I the same person if I cut my finger nail?* Or *Can I jump in the same river twice?*

The reason that we still want the assignment is that it somehow helps us to write moduler programs by providing a way to model local state.

But this breaks the old substitution model badly, because a variable is not simply a name of a value, but **a place where a value can be stored in**. So that later on the program can find the place and change its value in it.

The main topic of this article is to understand the new evaluation model: **the environment model.**

Simply put, the **environment** is a chain of **frames**, where a frame is just a mapping from variables to values.

There is one special **global environment** in which contains all the primitive mappings. Things like `cons`, `+` etc.

Let's go through a simple example, consider the procedure definition

{{< highlight scheme >}}
(define (square x) (* x x))
{{< /highlight >}}

evaluated in the global environment.

# Evaluate a procedure

We can think of the evaluation of this function definition in 3 steps.

**Step 1** The procedure definition is just syntactic sugar for an underlying implicit *lambda* expression. The above is equivalent to

{{< highlight scheme >}}
(define square
  (lambda (x) (* x x)))
{{< /highlight >}}

**Step 2** The evaluation of a *lambda* expression will produce a **procedure object**. A procedure object has two parts: the code and the link to its environment.

{{< figure src="/img/environment_model_1.png" >}}

**Step 3** Register in the global environment the name `square` to point to the newly created procedure object.

# Apply a procedure

Now we have a procedure evaluated, let's see how it is applied.

>To apply a procedure to arguments, create a new environment containing a frame that binds the parameters to the values of the arguments. The enclosing environment of this frame is the environment specified by the procedure. Now, within this new environment, evaluate the procedure body.

Let's see an example here.

{{< highlight scheme >}}
(square 5)
{{< /highlight >}}

**Step 1** Create a new environment E1 which has a new frame that has x bound to 5.

**Step 2** This new environment E1 points back at the global environment, because the function `squre` points to global environment.

**Step 3** Evaluate the body of the function `(* x x)` within E1. Which results in 25.

{{< figure src="/img/environment_model_2.png" >}}

Let's test out our knowledge by doing some exercises from the book.

# Exercise 3.9

In section 1.21 we used the substitution model to analyze two procedures for computing factorials, a recursive version

{{< highlight scheme >}}
(define (factorial n)
  (if (= n 1)
      1
      (* n (factorial (- n 1)))))
{{< /highlight >}}

and an iterative version

{{< highlight scheme >}}
(define (factorial n)
  (fact-iter 1 1 n))
(define (fact-iter product counter max-count)
  (if (> counter max-count)
      product
      (fact-iter (* counter product) (+ counter 1) max-counter)))
{{< /highlight >}}

Show the environment structures created by evaluating `(factorial 6)` using each version of the `factorial` procedure.

<hr />

The recursive version

{{< figure src="/img/ex3.9-1.png" >}}

The iterative version

{{< figure src="/img/ex3.9-2.png" >}}

# Exercise 3.10

In the `make-withdraw` procedure, the local variable `balance` is created as a parameter of `make-withdraw`.

{{< highlight scheme >}}
(define (make-withdraw balance)
  (lambda (amount)
    (if (>= balance amount)
        (begin (set! balance (- balance amount))
               balance)
        "Insufficient funds")))
{{< /highlight >}}

We could also create the local state variable explicitly, using `let`, as follows.

{{< highlight scheme >}}
(define (make-withdraw initial-amount)
  (let ((balance initial-amount))
    (lambda (amount)
      (if (>= balance amount)
          (begin (set! balance (- balance amount))
                 balance)
          "Insufficient funds"))))
{{< /highlight >}}

Recall from section 1.3.2 that `let` is simply syntactic sugar for a procedure call:

{{< highlight scheme >}}
(let ((<var> <exp>)) <body>)
{{< /highlight >}}

is interpreted as an alternate syntax for

{{< highlight scheme >}}
((lambda (<var>) (body)) <exp>)
{{< /highlight >}}

Use the environment model to analyze this alternate version of `make-withdraw`, drawing figures like the ones above to illustrate the interactions


{{< highlight scheme >}}
(define W1 (make-withdraw 100))
(W1 50)
(define W2 (make-withdraw 100))
{{< /highlight >}}

Show that the two versions of `make-withdraw` create objects with the same behavior. How do the environment structures difffer for the two versions?

<hr />

Environment structure for the first version:

{{< figure src="/img/ex3.10-1.png" >}}

As for the second version, we have to do some preprocessing on the procedure. Basically convert the `let` to an extra function call:

{{< highlight scheme >}}
(define (make-withdraw initial-amount)
  ((lambda (balance)
     (lambda (amount)
       (if (>= balance amount)
           (begin (set! balance (- balance amount))
                  balance)
           "Insufficient fund")))
   initial-amount))
{{< /highlight >}}

Environment structure for the seconde version:

{{< figure src="/img/ex3.10-2.png" >}}

P.S. It comes as a hindsight that the environment model is crucial to understanding the upcoming *eval* and *apply* cycle in Section 4.1.
