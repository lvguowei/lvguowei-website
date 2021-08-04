+++
author = "Guowei Lv"
categories = ["SICP"]
description = "Understanding the Y combinator"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - The Y Combinator"
date = 2021-08-04T15:03:26+03:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

I rewatched the **Lecture 7A** again and found that I never really understand the second half. So I spent a little more time on it and write down my some of my thoughts here. Also I couldn't find this material in the book, maybe it was in the 1st edition? Please let me know if you have the 1st edition of the book(I missed one opportunity to acquire it for just 20e TT).

Anyway, the second half is about Y combinator. Let's see a screenshot from the lecture video:

{{< figure src="/img/y-combinator.png" >}}

You cannot read it, but the text says:

`(Y F) = (F (Y F))`

What is this and what has it to do with our meta circular evalurator? Let's start from the beginning.

Our evaluator is defined as two parts: `eval` and `apply`, and they are all recursively calling each other. We need to find some deeper meaning of recursive functions.

We can draw some inspiration from math.

Let's see this equation:

`x = 2x + 2`

We can think of this equation as some kind of *recursive definition*.
`x` is defined in terms of itself.

Also the solution -2 of this equation is called the fixed point of function: `f(x) = 2x + 2`.

Keep this in mind, let's take a look at a simple recursive function in scheme:

{{< highlight scheme >}}
(define expt
  (lambda (x n)
    (cond ((= n 0) 1)
          (else 
            (* x (expt x (- n 1)))))))
{{< /highlight >}}

If you squeeze your eyes, and focus only on the form of this function, and ignore the details, you will see:

{{< highlight scheme >}}
(define expt
  ....
    ................
          ......
            .....expt........)
{{< /highlight >}}

Isn't this very similar to the math equation above? `expt` is defined in terms of itself.
`expt = (F expt)`. Here `F` is some kind of function that manipulates `expt`.

Then the anwser to the question "what is expt?" is "the fixed point of `F`!"
In other words, if a function satisfies the above definition of `expt`, then that function must be the fixed point of `F`.

Now the problem is to find the fixed point of `F`. We need to anwser two questions: what is `F`? and how to find the fixed point of it?

Let's first see how to find `F` of the math equation `x = 2x + 2`. It is kind of obvious, `F` is just the right part of the equation: `2x + 2`, but wait, this isn't a function, let's complete it as `f(x) = 2x + 2`.

Following the same process, the `F` of `expt` definition is also the "right part", or the body of the function definition!

{{< highlight scheme >}}
  (lambda (x n)
    (cond ((= n 0) 1)
          (else 
            (* x (expt x (- n 1))))))
{{< /highlight >}}

But wait, this isn't a proper function, let's complete it as

{{< highlight scheme >}}
(define F
  (lambda (g)
    (lambda (x n)
      (cond ((= n 0) 1)
            (else 
              (* x (g x (- n 1))))))))
{{< /highlight >}}

Perfect!

Now let's take a look at how to find the fixed point of a function.

One way is to keep applying the function recursively to some initial value (although not guaranteed to work, but this works in our case).

For example the fixed point of `f(x) = cos(x)` can be calculated as `cos(cos(cos(cos(cos ... cos(1) ...))))`.

Let's write a small function to verify this:

{{< highlight scheme >}}
(define (f a n)
  (if (= n 0)
      (cos a)
      (cos (f a (- n 1)))))
      

1 ]=> (f 1 100)

;Value: .7390851332151605

{{< /highlight >}}

Since the fixed point of `F` is `(F (F (F ... (F ???)...)))`, ??? represent the initial value(as we can see later it is really not important what it is). We now need to figure out how to apply `F` recursively like this.

Here we introduce the magical function `Y`.

{{< highlight scheme >}}
(define Y
  (lambda (f)
    ((lambda (x) (f (x x)))
    (lambda (x) (f (x x))))))
{{< /highlight >}}

Ask no more, let's just play with it.

What is `(Y F)`?
If you solve this carefully you will see that 
`(Y F) = (F (Y F))`

So that means to find the fixed point of `F`, we can just call `(Y F)`!
