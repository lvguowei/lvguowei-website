+++
author = "Guowei Lv"
categories = ["SICP"]
description = "as a literate program"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - Metacircular Evaluator"
date = 2020-09-26T20:18:45+03:00
+++
>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

At the beginning of Chapter 4, we will implement an evaluator.
This is a complete change of topic, we put down the "language user" hat and put on the "language designer" hat.
For an "average programer" like me, this feels simply too advanced and unfamiliar. So I decided to slow down and pay more attention.

After skimming through 4.1, I was thinking is the code from book able to run at all? So I typed them all and to my suprise, it ran without any problem (well, except for a few typos).

Then I made another decision to go one step further: typing the entire chapter 4.1 into an orgmode file as a literate program. And it worked out like a charm!

Here is the [org file](/file/metacircularevaluator.txt)

And the exported beautiful [pdf file](/file/metacircular.pdf)

And to prove everything works, let's run the test code from the book:

{{< highlight scheme>}}

MIT/GNU Scheme running under GNU/Linux
Type `^C' (control-C) followed by `H' to obtain information about interrupts.

Copyright (C) 2019 Massachusetts Institute of Technology
This is free software; see the source for copying conditions. There is NO warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR
PURPOSE.

Image saved on Saturday August 10, 2019 at 6:28:48 PM
  Release 10.1.10 || Microcode 15.3 || Runtime 15.7 || SF 4.41 || LIAR/x86-64 4.118

1 ]=> 
;Loading "metacircular.scm"... done
;Value: driver-loop

1 ]=> (define the-global-environment (setup-environment))

;Value: the-global-environment

1 ]=> (driver-loop)


;; M-Eval input:
(define (append x y)
  (if (null? x)
    y
    (cons (car x)
          (append (cdr x) y))))

;; M-Eval value:
ok

;; M-Eval input:
(append '(a b c) '(d e f))

;; M-Eval value:
(a b c d e f)

;; M-Eval input:
{{< /highlight >}}
