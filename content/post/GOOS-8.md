+++
categories = ["Object Oriented Design"]
description = "A follow through of the great book Growing Object-Oriented Software, Guided by Tests with code"
featured = "featured-goos.jpg"
featuredpath = "/img"
keywords = ["Growing Object Oriented Software Guided by Tests", "TDD"]
title = "GOOS Book Distilled Part 7"
date = 2018-02-19T21:16:50+02:00
+++

>This is a series of blog posts going through the great book [**Growing Object Oriented Software Guided By Tests**](https://www.amazon.com/Growing-Object-Oriented-Software-Guided-Tests/dp/0321503627), typing in code chapter by chapter, trying to add some of my own understanding where things may not be easy to grasp in the book. I highly recommand you get a copy of the book and follow along with me. Happy coding.

Let's start off by taking a closer look at our `AuctionSniper`. Notice that it implements `AuctionEventListener`, which means it is now mainly a *listener*. It *listens* to `AuctionMessageTranslator`, and for now, it only implements `auctionClosed()`. So the next step would naturally be to implement `currentPrice()`. It is not hard to find out that when receives a `PRICE` update, the sniper should do 2 things:

1. Sends a higher bid.
2. Display status as `BIDDING` in UI.

The second one is easy, we just use `SniperListener`. But where do we put the function to send a bidding message?

We introduce another new class, `Auction`. And put the message sending logic into it.

Now, `AuctionSniper` has two responsibilities:

1. Notify status change through `SniperListener`.
2. Make bid through `Auction`.

There was a split of a second that we considered adding the sending bid messages into `SniperListener`, but quickly dismissed the thought because the `SniperListener` is a *notification* not a *dependency*. Subtle design decisions like this is crucial to keep the architecture of the software reasonable.

Since this is an important concept, I quote the definitions from the book:

>**Dependencies**
>Services that the object requires from its peers so it can perform its responsibilities. The object cannot function without these services. It should not be possible to create the object without them. For example, a graphics package will need something like a screen or canvas to draw on—it doesn’t make sense without one.

>**Notifications**
>Peers that need to be kept up to date with the object’s activity. The object will notify interested peers whenever it changes state or performs a significant action. Notifications are “fire and forget”; the object neither knows nor cares which peers are listening. Notifications are so useful because they decouple objects from each other. For example, in a user interface system, a button component promises to notify any registered listeners when it’s clicked, but does not know what those listeners will do. Similarly, the listeners expect to be called but know nothing of the way the user interface dispatches its events.

[Source code](https://github.com/lvguowei/GOOS/commit/8746dfd71137f6c91d78df73bcc4d9731ba2bc04)
