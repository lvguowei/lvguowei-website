+++
author = "Guowei Lv"
categories = ["SICP"]
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - Why Assignment Is Still Useful in Functional Programming"
date = 2018-09-14T21:53:35+03:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

In FP world people bash assignment. They say it is the root of all evils. But is it really true?

First we have to ask why assignment? The answer is simply to achieve better modularity in program design. Let's see an example.

Suppose we are designing a counter, which simply starts from 0, and increase by 1 every time it is called.

Let's first implement it using assignment.

{{< highlight scheme >}}
(define (make-counter)
  (let ((count 0))
    (lambda ()
      (set! count (+ count 1))
      count)))
{{< /highlight >}}

The `make-counter` is a constructor, which returns a lambda that has access to a local variable `count`.

Now let's try to use it inside exponential function.

{{< highlight scheme >}}
(define (exp x n counter)
  (if (> (counter) n)
      1
      (* x (exp x n counter))))
{{< /highlight >}}

I know this is a bit contrived. But it is still a valid implementation and kind of straightforward.

Now, let's implement the `counter` without using any assignment.

{{< highlight scheme >}}
(define (counter current-count)
  (+ current-count 1))
{{< /highlight >}}

A counter is just a function that increase the current count by one, fair enough.

Now let's use it in the same exponential function.

{{< highlight scheme >}}
(define (exp x n counter current-count)
  (let ((new-count (counter current-count)))
    (if (> new-count n)
        1
        (* x (exp x n counter new-count)))))
{{< /highlight >}}

Notice that the function takes one more parameter `current-count`. And it is always 0 when calling the function. `(exp 4 2 0)`. Imagine if a procedure `A` calls `exp`, then `A` needs to know to put 0 as the `current-count`. In other words, the internals of `counter` has leaked into `exp`.

If you think this is not too bad, let's tweak the contrived example to be even more contrived.

Suppose that the we want to control the speed of the counter. Say we want to increase 3 by each count.

In the assignment version of the counter, we just change to `(set! count (+ count 3))`. No other places needs to change.

While in the non-assignment version. We have to modify the `expo` to be:

{{< highlight scheme >}}
(define (expo x n counter current-count)
  (let ((c1 (counter current-count)))
    (let ((c2 (counter c1)))
      (let ((c3 (counter c2)))
        (if (> c3 n)
            1
            (* x (expo x n counter c3)))))))
{{< /highlight >}}

Now the internals of the `counter` logic made a big mess in `expo`.

If you think my example is too contrived, you can still read the original example given in the book on section 3.1.2.

Hope that after reading these examples, you will understand why assignments are still useful sometimes.

