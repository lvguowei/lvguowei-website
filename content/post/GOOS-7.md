+++
categories = ["Object Oriented Design"]
description = "A follow through of the great book Growing Object-Oriented Software, Guided by Tests with code"
featured = "featured-goos.jpg"
featuredpath = "/img"
title = "GOOS Book Distilled Part 6"
keywords = ["Growing Object Oriented Software Guided by Tests", "TDD"]
date = 2018-02-17T21:40:22+02:00
+++

>This is a series of blog posts going through the great book [**Growing Object Oriented Software Guided By Tests**](https://www.amazon.com/Growing-Object-Oriented-Software-Guided-Tests/dp/0321503627), typing in code chapter by chapter, trying to add some of my own understanding where things may not be easy to grasp in the book. I highly recommand you get a copy of the book and follow along with me. Happy coding.

This is the beginning of Chapter 13 in the book.

Well, we have a problem about `Main`.

Let's see what `Main` is currently doing for us. 

1. It handles UI work.
2. It handles connection to XMPP server.
3. It implements `AuctionEventListener`. That is `AuctionMessageTranslator` uses it as a listener.

Among the 3 resposibilities, the 3rd one feels not quite right. As the types of messages grows, `Main` will have to do more and more stuff.

Now it is time to create a `AuctionSniper` class to make `Main`'s life easier. Now thinking about it, it is quite weird that there is no  class named after *sniper* since our application is call **auction sniper**. Let's also create a `SniperListener` to put into `AuctionSniper`, so that `AuctionSniper` does not directly depend on UI.

Now the architecture looks like this:

{{< figure src="/img/goos-6.jpg" >}}

Pay attention to this structure, something interesting is happening. The shaded components(*Chat* and *Main*) have been pushed to the sides. And what in the middle forms the core of our business logic of the app.

[Source code](https://github.com/lvguowei/GOOS/commit/db0e5ced125e13954604f0a67d1e5562ddeb5506)
