+++
categories = ["Object Oriented Design"]
description = "A follow through of the great book Growing Object-Oriented Software, Guided by Tests with code"
featured = "featured-goos.jpg"
featuredpath = "/img"
title = "GOOS Book  Distilled Part 1"
keywords = ["Growing Object Oriented Software Guided by Tests", "TDD"]
date = 2018-02-05T19:51:42+02:00
+++

Now it's about time to write our first acceptance test.

**The sniper joins an auction, the auction closes, the sniper loses**.

This is quite easy to understand, if we break this in steps, they would be:

1. when an **auction** *is selling* an item

2. and a **sniper** has *started bid* in that auction

3. the **auction** will *receive a join request* from the **sniper**

4. when the **auction** announces that it is *closed*

5. the **sniper** will show that it *loses the auction

Without looking at the code, let's get a rough understanding by answering these questions:

- What is an **auction** and what can it do?

An **auction** is the third-party auction service of which we are trying to mimic the behavior. One auction corresponds to one item to sell. It can connect to the XMPP server, login using some identifier of its item to sell, listening to all sorts of XMPP messages that is going to be sent to it and probably do something with them.

- What is the **sniper** and what can it do?

The sniper is THE APPLICATION that we are trying to build here. Well it can bid items in auctions. :)

- How the XMPP gets involved in all this?

The XMPP server is the communication channel that connects our application(sniper) to the fake auction service. E.g. when the sniper starts bidding, it sends some *start bidding* message to the XMPP server. And since the auction service is listening to the same *chat*, it will receive it. When auction announces that it is closed, it actually sends a *closed message* to XMPP server, and if the sniper is listening to that chat, it will receive it and knows that the auction is over. Since the auction service and our application communicate through this XMPP messaging service, they can be run on different threads.

So now we understand the big picture, what do we need to implement?

First we need to implement *start selling item* in **auction**. This is pretty easy, we just connect to XMPP server, login using the item, that will create a chat. And then it just starts listening to the messages that got sent to this chat.

Then, we need to implement the *start bidding an item* for the **sniper**. We put a empty stub for now just to make everything compile.

And then, we need to implement how the **auction** knows that a **sniper** has joined. We do this by using a blocking queue, and polls for the *join message* for 5s.

At the end of the day, the **auction** has to be able to send a *close message*. For now we just send an empty message because this is the only message that the **auction** is able to send by far.

Then we implement a way to test that the **sniper** is now losing. We do this by just checking the existence of some text label.

This wraps up the first RED version of our first acceptance test. It fails because there is no application window launched yet.

In next post, we will make it pass.

[Source code](https://github.com/lvguowei/GOOS/commit/24ddc9da68103acb6380c0609d93f927505c35d9)
