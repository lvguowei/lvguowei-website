+++
author = "Guowei Lv"
categories = ["SICP"]
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - A Simulator for Digital Circuits"
date = 2018-12-14T20:45:49+02:00
draft = true
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**


We are building a simulator program for digital circuits, using the Object Oriented view of the world, in Scheme Lisp.

This is probably the most complicated example program in the book I have ever seen by far. But it is also beautifully constructed. In this program, we build everything ourselves, including the data structures. And in the video lecture, the professor claimed that this is the basis of a real world circuits simulator, of course very much simplified.

In the book, the author used a top down approach to first show the high level structure and then go into details. But I will take a bottom up approach in this article, I will go through the details first, just to be different.

Let's get started.

# Queue

First thing first, we need to build a **queue** data structure from scratch. Yes, the good old FIFO (first in first out) queue.
