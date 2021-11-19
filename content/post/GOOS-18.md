+++
categories = ["GOOS Book Distilled"]
keywords = ["Growing Object Oriented Software Guided by Tests", "TDD"]
description = "A follow through of the great book Growing Object-Oriented Software, Guided by Tests with code"
featured = "featured-goos.jpg"
featuredpath = "/img"
title = "GOOS Book Distilled Part 12"
date = 2018-05-17T21:11:36+03:00
+++

>This is a series of blog posts going through the great book [**Growing Object Oriented Software Guided By Tests**](https://www.amazon.com/Growing-Object-Oriented-Software-Guided-Tests/dp/0321503627), typing in code chapter by chapter, trying to add some of my own understanding where things may not be easy to grasp in the book. I highly recommand you get a copy of the book and follow along with me. Happy coding.

This post covers Chapter 19 *Handling Failure*, which is also the last chapter in the book related to this project.

In this chapter We add some error handling logic to the app.

# What if it doesn't work

When the sniper receives a error message from the server, We basically want to achieve the following:

1. Displays the failure.
2. Records the failure.
3. Ignores further updates from the server.

We add a new end-to-end test:
[Source Code](https://github.com/lvguowei/GOOS/commit/4f8ee2c108dbb1360299a9fdf4f5a08e9712e18e)

# Detecting the Failure

It is not hard to guess that when the app receives a bad message, the first place to handle it is in the `AuctionMessageTranslator`. It also needs to notify the failure to its listener.

[Source Code](https://github.com/lvguowei/GOOS/commit/5171a7b855864c29090a3805eccb18fd8540b517)

# Displaying the Failure

This is also easy, when `AuctionSniper` receives the `auctionFailed()` callback from `AuctionMessageTranslator`, it just change its snapshot's state and notify.

[Source Code](https://github.com/lvguowei/GOOS/commit/15f40891d36338a8e758792a00420564df92346f)

# Disconnecting the Sniper

In `XMPPAuction`, add another listener that when receives `auctionFailed()`, just remove the `AuctionMessageTranslator` from the chat.

[Source Code](https://github.com/lvguowei/GOOS/commit/62619ce8fc7093857a9e5a5ae3f31a510da33007)

# Recording the Failure

## Filling in the Tests

We create a `ActionLogDriver` in `ApplicationRunner` using Apache Common IO library, so that we have a way to test the existance of a failure log in the system.
[Source Code](https://github.com/lvguowei/GOOS/commit/b1a08ede4ad36dfed33b87d1808b2b34766a04df)

## Failure Reporting in the Translator

We create a `XMPPFailureReporter` to take the responsibility of reporting failure log, and call that when the `AuctionMessageTranslator` receives a bad message.
[Source Code](https://github.com/lvguowei/GOOS/commit/a7d55171aa9f599c15bad8a2cc232e5bec060606)

## Closing the Loop
Now it's time to implement the `XMPPFailureReporter` in the `XMPPAuctionHouse`. This uses the logger in Java to log message.
[Source Code](https://github.com/lvguowei/GOOS/commit/e7da8e56de960e034a8edb857dbc6191ae38d1d9)

OK, we are done with the Auction Sniper project for now.

~The End~



