+++
categories = ["SICP"]
keywords = ["SICP", "Structure and Interpretation of Computer Programs", "sqrt", "average damping", "newton's method", "fixed point", "Scheme"]
description = "Put together the sqrt related stuff from SICP"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - A deep dive into square root procedure"
date = 2018-06-29T20:52:11+03:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

There are several epic example problems in the book, the first one of them is of how to calculate the square root of a number.

I believe all the languages has a built-in (or library) method to calculate sqrt. But have you ever wondered how it is implemented? Fasten your seat belt cause we are going on a LONG ride of math, programming and FUN!

Computer isn't God, it needs step by step instructions to work, no exception for calculating sqrt here.

# Square Roots by Iterative Guessing

We can describe this method simply as follows:

Suppose we want to find the sqrt of **a**.

1. Make a guess **g**.

2. Check if the guess **g** is __good enough__, if yes, return it.

3. If **g** is not good enough, __improve it__ and goes to the 2nd step.

Sounds simple. But we still have to define how to make the initial guess,  what is **good enough** and how to **improve a guess**.

To make an initial guess, we stipulate that we use 1 for now.

And it is easy to check whether the guess is good enough, we can just square it and see how close it is to the given number.

{{< highlight scheme >}}
(define (good-enough? guess x)
  (< (abs (- (square guess) x)) 0.001))
{{< /highlight >}}

The critical part is how to improve a guess.

>...whenever we have a guess **g** for the value of the square root of a number **a**. We can perform a simple manipulation to get a better guess by averaging **g** with **a/g**.

Now we know how to improve the guess.

{{< highlight scheme >}}
(define (improve guess x)
  (average guess (/ x guess)))
{{< /highlight >}}

Then we write out the process using these procedures.

{{< highlight scheme >}}
(define (sqrt-iter guess x)
  (if (good-enough? guess x)
    guess
    (sqrt-iter (improve guess x) x)))
{{< /highlight >}}

We kick start the process by giving the initial guess 1.

{{< highlight scheme >}}
(define (sqrt x)
  (sqrt-iter 1.0 x))
{{< /highlight >}}

# Finding the fixed points of functions

Let's talk about something that seems to be completely unrelated, finding **fixed points** of functions.

First of all, the definition of fixed point:

>A number **x** is called a fixed point of a function **f** if **x** satisfies the equation `f(x) = x`.

In other words, fixed point is where the given fuction and f(x) = x crosses. If you know math well, you will know that to solve the equation `f(x) = g(x)` means to find the crossing points of the two functions.

The book also provides a way to calculate fix points.

>For some functions **f** we can locate a fixed point by beginning with an initial guess and applying **f** repeatedly, `f(x), f(f(x)), f(f(f(x))), ...` until the value does not change very much.

Not hard to translate this into code:

{{< highlight scheme >}}
(define tolerance 0.00001)

(define (fixed-point f first-guess)
  (define (close-enough? v1 v2)
    (< (abs (- v1 v2)) tolerance))
  (define (try guess)
    (let ((next (f guess)))
      (if (close-enough? guess next)
        next
        (try next))))
  (try first-guess))
{{< /highlight >}}

Let's now trying to find out how this fix point thing is related to calculating sqrt.

Say we want to find the sqrt of **a**. This means we are finding a **x** such that `x^2 = a`, we can also write it as `x = a/x`. Now this fits the form `f(x) = x`, and here `f(x) = a/x`. So we are actually finding a fixed point of function `f(x) = a/x`.

So we can define sqrt by using the `fixed-point` function.

{{< highlight scheme >}}
(define (sqrt x)
  (fixed-point (lambda (y) (/ x y)) 1.0))
{{< /highlight >}}

But unfortunately this **won't work**. Try execute it you will see that it won't converge.

To make it converge, we need to apply a trick. Add `x` to both sides of the equation `x = a/x` and then divide it by 2. We then get `x = (x + a/x) / 2`. Now the problem becomes to find the fixed point of function `f(x) = (x + a/x) / 2`.

{{< highlight scheme >}}
(define (sqrt x)
  (fixed-point (lambda (y) (average y (/ x y))) 1.0))
{{< /highlight >}}

This approach of averaging successive approximations to a solution, a technique we call *average damping*, often aids the convergence of fixed-point searches.

We can express *average damping* by a procedure, it takes the original procedure, and returns a new damped one.

{{< highlight scheme >}}
(define (average-damp f)
  (lambda (x) (average x (f x))))
{{< /highlight >}}

Now we can rewrite our sqrt function in terms of fixed point and average damp.

{{< highlight scheme >}}
(define (sqrt x)
  (fixed-point (average-damp (lambda (y) (/ x y))) 1.0))
{{< /highlight >}}

I suggest that you stop for a second and appreciate how much expressive power we have at this point, and how we achieve all these by just combining functions.

# More on average damping

I was scratch my head trying to get better understanding of this average damping. I googled it, no result. But turns out that damping has some meaning in Physics.

> Progressively reduce the amplitude of (an oscillation or vibration).

For example, we can multiply `f(x) = sin(x)` and `f(x) = x` together to form `f(x) = xsin(x)`, the result looks like this:

{{< figure src="/img/damp-sin.png" >}}

See how `sin(x)` is constrained inside `f(x) = x` and `f(x) = -x`. This gives a taste of what damping means.

Now let's draw `f(x) = (x + 4/x) / 2` and `f(x) = 4/x` and `f(x) = x/2`:

{{< figure src="/img/average-damp.png" >}}

Notice that the original function and the damped one has the same fixed point 2, and the damped function is constrained under `f(x) = x/2`. That is probably why it is called average damping(I'm not sure about this, please let know if I was totally wrong).

# Newton's Method

Actually in the beginning of post, the **iterative guessing** is a special case of **Newton's method**. Let's first give the definition of Newton's method and then explain why iterative guessing is a special case of it.

>If `x -> g(x)` is a differentiable function, then a solution of the equation `g(x) = 0` is a fixed point of the function `x -> f(x)` where

>`f(x) = x - g(x) / Dg(x)`

>and `Dg(x)` is the derivative of `g` evaluated at `x`.

Let's say we want to calculate the sqrt of **a**, that is `g(x) = x^2 - a = 0`. According to Newton's method, the solution is the fixed point of 

{{< highlight scheme >}}
f(x) = x - g(x) / Dg(x)
      
     = x - (x^2 - a) / 2x
     
     = x - x/2 + a/2x
     
     = x/2 + a/2x
     
     = (x + a/x) / 2
     
{{< /highlight >}}

which is exact the same as previous methods.

We get this result because we calculated the derivative by hand, let's now find a way to let the computer do that for us.

Let `dx` be a very small number, `Dg(x)` can be calculated as 

`Dg(x) = (g(x + dx) - g(x)) / dx`

We can express the idea of derivative as a procedure:

{{< highlight scheme >}}
(define (deriv g)
  (lambda (x)
    (/ (- (g (+ x dx)) (g x)) dx)))
{{< /highlight >}}

Notice that `deriv`is a higher order procedure, it takes a procedure `g` and return a new procedure, which fits the math definition of derivative perfectly.

With the aid of `deriv`, we can express Newton's method as a fixed point process.

{{< highlight scheme >}}

;; transform g -> f
(define (newton-transform g)
  (lambda (x)
    (- x (/ g(x) ((deriv g) x)))))
    
(define (newton-method g guess)
  (fixed-point (newton-transform g) guess))

{{< /highlight >}}


Now we can define **sqrt** in terms of `newton-method`:

{{< highlight scheme >}}
(define (sqrt x)
  (newton-method (lambda (y) (- (square y) x)) 1.0 ))
{{< /highlight >}}


# Summary

Phew, we have come to the end of this long journey, let's look back what just happened.

We introduced 3 ways to implement function **sqrt**:

1. Iterative Guessing (this is the same as 2, and essentially the same as 3)

2. Fixed point + average damping

3. Fixed point + newton's method


See that method 2 and 3 share the *fixed point* part, indicating that we can further abstract things out.

{{< highlight scheme >}}
(define (fixed-point-of-transform g transform guess)
  (fixed-point (transform g) guess))
  
;; 2
(define (sqrt x)
  (fixed-point-of-transform 
    (lambda (y) (/ x y)) 
    average-damp 
    1.0))

;; 3
(define (sqrt x)
  (fixed-point-of-transform 
    (lambda (y) (- (square y) x)) 
    newton-transform 
    1.0))
{{< /highlight >}}

This is stunningly beautiful and satisfactory. Don't you agree?
