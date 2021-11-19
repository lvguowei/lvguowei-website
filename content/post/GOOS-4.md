+++
categories = ["GOOS Book Distilled"]
keywords = ["Growing Object Oriented Software Guided by Tests", "TDD"]
description = "A follow through of the great book Growing Object-Oriented Software, Guided by Tests with code"
featured = "featured-goos.jpg"
featuredpath = "/img"
title = "GOOS Book Distilled Part 3"
date = 2018-02-11T12:47:26+02:00
+++

>This is a series of blog posts going through the great book [**Growing Object Oriented Software Guided By Tests**](https://www.amazon.com/Growing-Object-Oriented-Software-Guided-Tests/dp/0321503627), typing in code chapter by chapter, trying to add some of my own understanding where things may not be easy to grasp in the book. I highly recommand you get a copy of the book and follow along with me. Happy coding.

In this post, we start our second acceptance test.

**Sniper makes a higher bid but loses.**

To be able to implement this, there are 2 fundamental functionalities missing.

**The fake auction server needs to report price changes.**

**The sniper needs to how to bid according to the price reports.**

The first is easy, the auction server just sends a `PRICE` message in the chat.
The format of the message is

`"SOLVersion: 1.1; Event: PRICE; CurrentPrice: 1000; Increment: 98; Bidder: other bidder;"`

We will leave the second one to later.

To be able to run multiple tests consecutively. We need to disconnect the connection when the window closes. So the next test can run in a clean slate.

We also did some refactoring.

This wraps up this post, in the next post, we will try this `outside in` approach by writing our first unit test. Stay tuned.

[Source code](https://github.com/lvguowei/GOOS/commit/8774deed5f73fd38706752d6035aca702bce4934)
