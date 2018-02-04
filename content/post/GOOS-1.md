+++
categories = ["Object Oriented Design"]
description = "A follow through of the great book Growing Object-Oriented Software, Guided by Tests with code"
featured = "featured-goos.jpg"
featuredpath = "/img"
title = "GOOS Book  Distilled Part 0"
date = 2018-02-04T21:08:43+02:00
+++

I decided to start this new series of blog posts to mark my progress of re-reading the classic book *Growing Object-Oriented Software, Guided By Tests*.

I read this book a couple of months ago (mostly on the train to work), I remembered it is a good book, but since I have no code out of it, the understanding must not be good.

So this time, I'm gonna do it the hard way.

Let the journey begin.

Since the book has already explained everything in great details, I will just explain the backbone of what is happening in my own words, to give just enough context to remind myself what is going on.

So long story short, we are going to build a desktop application (using java Swing framework) called **Auction Sniper**.

As its name suggests, the purpose of this application is to automatically bid auctions online through some third-party **auction service**.

We are going to use a certain flavor of TDD  (*out side in*) to guide our development.

{{< figure src="/img/tdd-outsidein.jpg" >}}

The overall project structure looks like following:

{{< figure src="/img/goos-infrastructure.jpg" >}}

The XMPP server used here is [OpenFire](http://download.igniterealtime.org/openfire/docs/latest/documentation/install-guide.html). Setting it up and running shouldn't be hard.

Create 3 accounts with passwords:

**sniper** - sniper

**auction-item-54321** - auction

**auction-item-65432** - auction

Also find the **Resource Policy** and set it to *never kick*.

This wraps up part 0. In next post, we will write the first acceptance test and watch it fail. Stay tuned.

