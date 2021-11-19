+++
categories = ["GOOS Book Distilled"]
keywords = ["Growing Object Oriented Software Guided by Tests", "TDD"]
description = "A follow through of the great book Growing Object-Oriented Software, Guided by Tests with code"
featured = "featured-goos.jpg"
featuredpath = "/img"
title = "GOOS Book Distilled Part 10"
date = 2018-02-26T20:14:34+02:00
+++

>This is a series of blog posts going through the great book [**Growing Object Oriented Software Guided By Tests**](https://www.amazon.com/Growing-Object-Oriented-Software-Guided-Tests/dp/0321503627), typing in code chapter by chapter, trying to add some of my own understanding where things may not be easy to grasp in the book. I highly recommand you get a copy of the book and follow along with me. Happy coding.

This post is the beginning of *Chapter 15 Towards a Real User Interface*.

## Replacing JLabel

We decided to replace the simple label with a table to represent snipers. To play it safe, we make a baby step: create a one cell table to just replace the text label.

{{< img-post "/img" "single-cell-snipers-table.png" "Single Cell Snipers Table" "center" >}}

[Source code](https://github.com/lvguowei/GOOS/commit/7f33fdb6363a041c105a731b79c1cd2c2db962fd)

## Sending the State out of the Sniper

In the new `JTable`, we show 3 columns: `itemId`, `lastPrice` and `lastBid`. Since these 3 values aways go together, we can bundle them up into a `SniperState`.

Recap: `AuctionSniper` is a listener in `AuctionMessageTranslator`. When there is a **PRICE** message coming, the `currentPrice(int price, int increment, PriceSource priceSource)` will be called.
So the `lastPrice` can be taken directly from `price`, the `lastBid` can be calculated by `price + increment`, what about the `itemId`? We decided to put it in the `AuctionSniper`'s constructor, since `Main` knows which item we are bidding, we can pass it to create the `AuctionSniper`. Also we change the tests.

[Source code](https://github.com/lvguowei/GOOS/commit/4ec304248df6691c521fddbc5a8b9fe31d2e06d7)

## Showing a Bidding Sniper

In order to show the `SniperState` on UI, we pass it to the `SnipersTableModel` and use it to show in the table. Nothing really interesting here.

[Source code](https://github.com/lvguowei/GOOS/commit/fe390e8a73c576f4c2340452bc9caae4a7d0f634)
