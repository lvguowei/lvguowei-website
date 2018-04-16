+++
categories = ["Object Oriented Design"]
description = "A follow through of the great book Growing Object-Oriented Software, Guided by Tests with code"
featured = "featured-goos.jpg"
featuredpath = "/img"
title = "GOOS Book Distilled Part 4"
keywords = ["Growing Object Oriented Software Guided by Tests", "TDD"]
date = 2018-02-13T20:35:44+02:00
+++


>This is a series of blog posts going through the great book [**Growing Object Oriented Software Guided By Tests**](https://www.amazon.com/Growing-Object-Oriented-Software-Guided-Tests/dp/0321503627), typing in code chapter by chapter, trying to add some of my own understanding where things may not be easy to grasp in the book. I highly recommand you get a copy of the book and follow along with me. Happy coding.

Let's recap what our sniper can do by now, which is not much.

1. Start UI window. Show `JOINING` status.

2. Join auction. Which means connect to XMPP server, login as *sniper* and listening to the auction's chat. Send the `JOIN` message to the chat.

3. When receives a `CLOSE` message(the only type of message now), show status `LOST`.

The 3rd one looks a bit naive. On the high level, the sniper should be able to receive different types of messages and act on them accordingly, in other words, translate messages into actions. So we created a `AuctionMessageTranslator`. It also takes in a `AuctionEventListener`, which represents what actions can be triggered.

Now we start to see that we are gradually putting business logic into POJOs that are more detached to the framework. This also means that it's easier to write unit tests for them.

So we write the first test for the translator: `notifiesAuctionClosedWhenCloseMessageReceived`.

This wraps up this post. In next post, we will work on more message types. Stay tuned.

[Source code](https://github.com/lvguowei/GOOS/commit/487cbd43eab2f65016d56f916d006c762d8cab4e)

