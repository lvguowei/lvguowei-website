+++
categories = ["SICP"]
keywords = ["SICP", "functional programming", "structure and interpretation of computer programs"]
description = "Doing some exercises"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - Stream (9)"
date = 2020-02-14T14:02:02+02:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

I've been quite busy with work related stuff recently and not had enough time for this series. Now I decided to continue reading and writing about SICP. To refresh my memory, I will reread the *streams* sections in the book. This time I find the material to be much easier for me and I see some wit that I have not noticed before.(That's why this is a great book)

One of them is the **Henderson graph** used to illustrate streams.

Let's first take a look at the integers stream:

{{< highlight scheme >}}
(define (int-from n)
  (cons-stream n (int-from (+ n 1))))
  
(define integers (int-from 1))
{{< /highlight >}}

{{< img-post "/img" "henderson.jpg" "" "center" >}}

The dotted line means single value, and the solid line means stream.

This graph is recursive, and it is so beautifully and cleverly illustrates how infinite stream works.

*[please allow 5 mins to admire it]*

To end this article, I will quote from the lecture:

>Again you can ask is it really, the data structure integers will be all integers? Is it just something cleverly arranged so that when you look for it you find it there? Well, that is sort of a philosophical question. If something is there whenever you look, is it really there or not? Sort of like your savings in the bank.
