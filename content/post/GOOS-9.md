+++
categories = ["Object Oriented Design"]
description = "A follow through of the great book Growing Object-Oriented Software, Guided by Tests with code"
featured = "featured-goos.jpg"
featuredpath = "/img"
title = "GOOS Book Distilled Part 8"
keywords = ["Growing Object Oriented Software Guided by Tests", "TDD"]
date = 2018-02-23T20:18:12+02:00
+++

It's all about refactoring this time!

**1. Extracting `XMPPAuction`**
In `Main`, we see some places `chat` sends `messages`. But it lacks a language here. After some thoughts, we think we really mean *bid in auction* and *join auction*. Now it sounds like we should put these functions into the `Auction`, the `Auction` can **bid** and **join**.

[Source code](https://github.com/lvguowei/GOOS/commit/2ad3cdbf7a34da05e89858e46b984d6ff314d853)

**2. Extracting the User Interface**

The `Main` implements `SniperListener`, to handle UI events. We can create a new class to take over that resposibility. We call it `SniperStateDisplayer`. This is a inner class, so not a big change.

[Source code](https://github.com/lvguowei/GOOS/commit/34ac37a17f25dcf47e3aebce6df13ce4d0f5685d)

**3. Tidying Up the Translator**

Not much to say about this, just clean up the string parsing code. One thing though, the author do care a lot about the *flow* of the code, it has to read like a sentence.

[Source code](https://github.com/lvguowei/GOOS/commit/cc3a99994ade05e888d89fdc788733931a494b32)

This marks the end of Chapter 13.
