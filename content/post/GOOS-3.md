+++
categories = ["Object Oriented Design"]
description = "A follow through of the great book Growing Object-Oriented Software, Guided by Tests with code"
featured = "featured-goos.jpg"
featuredpath = "/img"
title = "GOOS Book Distilled Part 2"
date = 2018-02-07T21:05:34+02:00
keywords = ["Growing Object Oriented Software Guided by Tests", "TDD"]
+++

The goal in this post is to make the first acceptance test pass.

Let's see how far are we from that. Take a look at our `Main`, ther is ... nothing ...

But let's run the test any ways.

Test fails, because it cannot find a application window named `Application Sniper Main`.

To remedy that, we just write enough code to launch a window and name it `Application Sniper Main`.

Now run test again.

It fails because it cannot find a text label named `sniper status`. Because the test is testing that upon starting, there will be a label named `sniper status` that has text `Joining`.

This is also easy to deal with, we simply add a text label in the window and set its text to `Joining`.

Now run test again.

It fails on the fake auction server does not receive a `Joining Message`. This is expected, because up until now, all we do is some UI work, the application doesn't really *do* anything yet. So to fix this, the application has to connect to the XMPP server, create a chat, and send a message to it. All the information required like hostname, password and username is passed from the test.

Run test again.

Failed because after the fake auction announces `Closed`, the text label is not displaying `Lost`. To fix this, we need to listen to the chat we just created, and change the sniper status label to `Lost` upon receiving a message.

Run test and pass.

Hooray! Our first acceptance test passes.

[Source code](https://github.com/lvguowei/GOOS/commit/a581159c66504259ee11b1cfe84cbda5bdae1baa)
