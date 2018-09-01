+++
author = "Guowei Lv"
categories = ["SICP"]
description = "How to implement everything using pairs"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - From Pair to Flatmap 2"
date = 2018-09-01T10:12:11+03:00
draft = true
+++


>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

# Mapping over Lists

One extremely useful operation is to applysome transformation to each element in a list and generate the list of results.

{{< highlight scheme >}}
(define (map proc items)
  (if (null? items)
      '()
      (cons (proc (car items)) (map proc (cdr items)))))
{{< /highlight >}}
