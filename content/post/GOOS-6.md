+++
categories = ["Object Oriented Design"]
description = "A follow through of the great book Growing Object-Oriented Software, Guided by Tests with code"
featured = "featured-goos.jpg"
featuredpath = "/img"
title = "GOOS Book Distilled Part 5"
date = 2018-02-17T09:06:27+02:00
+++

In previous post, we wrote our first unit test on the `AuctionMessageTranslator` on how to handle `CLOSE` message. In this post, we will write another unit test on how to handle `PRICE` message.

This will finally force us to parse the comming messages and look inside them. So when we receive `CLOSE` message, we call `auctionClosed()` on `AuctionEventListener`, when we receive `PRICE` message, we call `currentPrice(int price, int increment)`. As simple as that.

This is a short post. So I think I will include a quote from the book.

>We cannot emphasize strongly enough that “first-cut” code is not finished. It’s
>good enough to sort out our ideas and make sure we have everything in place,
>but it’s unlikely to express its intentions cleanly. That will make it a drag on
>productivity as it’s read repeatedly over the lifetime of the code. It’s like carpentry
>without sanding—eventually someone ends up with a nasty splinter.

[Source code](https://github.com/lvguowei/GOOS/commit/4758607ca09707ec5c3d9fafcddbd4d71c3e93c6)
