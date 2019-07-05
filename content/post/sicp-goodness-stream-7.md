+++
categories = ["SICP"]
keywords = ["SICP", "functional programming", "structure and interpretation of computer programs"]
description = "Expoloring the stream paradigm"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - Stream (VII)"
date = 2019-04-13T20:39:53+03:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

Let's examine some examples from the book and furthur explore the stream paradigm.

# The Square Root Problem Revisited

In the beginning of the book there is this square root example. Let's recap here.

Basically, the example shows a way to calculate sqrt of x as a process of guessing. The guess starts with 1, and the next improved guess is calculated by the function:

{{< highlight scheme >}}
(define (sqrt-improve guess x)
  (average guess (/ x guess)))
{{< /highlight >}}

We just keep improving the guess until it converges to a certain value.

Actually the series of guesses can be seen as a stream.

{{< highlight scheme >}}
(define (sqrt-stream x)
  (define guesses
    (cons-stream 1.0
                 (stream-map (lambda (guess)
                               (sqrt-improve guess x))
                             guesses)))
  guesses)
{{< /highlight >}}

If you have difficulties understanding the procedure above. Let me translate it into English. 

Suppose we want (sqrt-stream 2).

The first guess is 1.

Now we know the stream looks like this:

(1 . . .)

We also know that the cdr of this stream is to map the `sqrt-improve` onto `guesses` itself.

The stream actually looks like this:

(1 *the rest of the stream is to map the sqrt-improve onto the stream itself*)

Since we know the stream's first item is 1, at least we know how to calculate the 1st value of the cdr of the  stream. That is (/ (1 + 2) 2) which is 1.5.

Now we know the stream looks like this:

(1 1.5 . . .)

Now we know the 2nd value of the stream, that means we know how to calculate the 2nd value of the cdr of the stream(which is the 3rd value of the stream). Just apply the `sqrt-improve` to it. (/ (1.5 + 2) / 2) which is 1.416666.

Now the stream looks like this:

(1 1.5 1.416666 . . .)

Now we know the 3rd value of the stream, that means we know how to calculate the 3rd value of the cdr of the stream(which is the 4th value of the stream) and so on.


We can then write a procedure `stream-limit` to find out when the value stops changing.

{{< highlight scheme >}}
(define (stream-limit s tolerance)
  (if (<= (abs (- (stream-ref s 0) (stream-ref s 1))) tolerance)
      (stream-ref s 1)
      (stream-limit (stream-cdr s) tolerance)))
{{< /highlight >}}

Let's try to run it:


{{< highlight scheme >}}
1 ]=> (stream-limit (sqrt-stream 2) 0.01)

;Value: 1.4142156862745097
{{< /highlight >}}
