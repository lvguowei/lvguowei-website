+++
categories = ["SICP"]
keywords = ["SICP", "lambda calculus", "structure and interpretation of computer programs"]
description = "Stream is just delayed list"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - Stream (I)"
date = 2019-02-14T21:36:32+02:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

Finally, we arrived at one the epic topics in the SICP book, stream.

A brilliant high level explanation about stream, quoted from the book:

>We’ve gained a good understanding of assignment as a tool in modeling, as well as an appreciation of the complex problems that assignment raises. It is time to ask whether we could have gone about things in a different way, so as to avoid some of these problems. In this section, we explore an alternative approach to modeling state, based on data structures called streams. As we shall see, streams can mitigate some of the complexity of modeling state.

>Let’s step back and review where this complexity comes from. In an attempt to model real-world phenomena, we made some apparently reasonable decisions: We modeled real-world objects with local state by computational objects with local variables. We identified time variation in the real world with time variation in the computer. We implemented the time variation of the states of the model objects in the computer with assignments to the local variables of the model objects.

>Is there another approach? Can we avoid identifying time in the computer with time in the modeled world? Must we make the model change with time in order to model phenomena in a changing world? Think about the issue in terms of mathematical functions. We can describe the time-varying behavior of a quantity x as a function of time x(t). If we concentrate on x instant by instant, we think of it as a changing quantity. Yet if we concentrate on the entire time history of values, we do not emphasize change—the function itself does not change.

>If time is measured in discrete steps, then we can model a time function as a (possibly infinite) sequence. In this section, we will see how to model change in terms of sequences that represent the time histories of the systems being modeled. To accomplish this, we introduce new data structures called streams. From an abstract point of view, a stream is simply a sequence. However, we will find that the straightforward implementation of streams as lists (as in 2.2.1) doesn’t fully reveal the power of stream processing. As an alternative, we introduce the technique of delayed evaluation, which enables us to represent very large (even infinite) sequences as streams.

>Stream processing lets us model systems that have state without ever using assignment or mutable data. This has important implications, both theoretical and practical, because we can build models that avoid the drawbacks inherent in introducing assignment. On the other hand, the stream framework raises difficulties of its own, and the question of which modeling technique leads to more modular and more easily maintained systems remains open.

Let's dive right in on how to implement stream. (The code for this is surprisingly less than what you might think)

I will take the bottom up approach again, since the book is kind of top down. So we start with the `memo-proc` procedure.

{{< highlight scheme >}}
(define (memo-proc proc)
  (let ((already-run? false)
        (result false))
    (lambda ()
      (if (not already-run?)
          (begin (set! result (proc))
                 (set! already-run? true)
                 result)
          result))))
{{< /highlight >}}

This is not hard to understand if you understand the [environment model](http://localhost:1313/post/sicp-goodness-environment-model/).

Basically `memo-proc` takes in a procedure `proc`, and returns a new procedure `ret-proc`. When the `ret-proc` gets executed for the first time, it runs the original `proc` and return its result, and it **remembers** the result. The next time `ret-proc` gets executed, it will return the remembered result directly without executing the original `proc` again.

Next, let's look at `delay`.


{{< highlight scheme >}}
(define-syntax delay
  (syntax-rules ()
    ((delay exp)
     (memo-proc (lambda () exp)))))
{{< /highlight >}}

First thing first, `delay` is not a procedure. It is kind of a special syntax.

The above code can be translated to the following: if you write `(delay exp)` in code, before anything starts, it will be automatically replaced by `(memo-proc (lambda () exp))`. Well, you may ask why can't `delay` be a procedure like this:

{{< highlight scheme >}}
(define (delay exp)
  (memo-proc (lambda () exp)))
{{< /highlight >}}

What is the difference here. Good question! The difference being that **when `exp` gets evaluated**.

Let's take a look at a simple example:

{{< highlight scheme >}}
(delay (+ 1 1))
{{< /highlight >}}

If `delay` is a procedure, `(+ 1 1)` will first be evaluated to 2 and then passed into the procedure. Well this is against what the name `delay` means, cause nothing gets delayed.

On the other hand, if `delay` is a special syntax, `(+ 1 1)` will not be evaluated first. The evaluation is actually delayed, hence the name `delay`.

Goes together with `delay` is `force`.

{{< highlight scheme >}}
(define (force delayed-object)
  (delayed-object))
{{< /highlight >}}

This is simple, as we see `delay` returns a procedure. To force a delayed object is just to call it.

Now we have enough to define the constructor of stream.

{{< highlight scheme >}}
(define-syntax cons-stream
  (syntax-rules ()
    ((cons-stream a b)
     (cons a (delay b)))))

{{< /highlight >}}

To create a stream, it is almost the same as cons up a list. The difference is that instead of just `(cons a b)`, we make a delayed object out of `b` and cons `a` with the delayed `b`. Since we do not want `b` to be evaluated here, we have to make `cons-stream` also a special syntax.

The only things left is the car and cdr of stream:

{{< highlight scheme >}}
(define (stream-car stream) (car stream))
(define (stream-cdr stream) (force (cdr stream)))
{{< /highlight >}}

Let's see some usages of streams in next episode. Stay tuned!
