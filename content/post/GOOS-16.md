+++
categories = ["Object Oriented Design"]
keywords = ["Growing Object Oriented Software Guided by Tests", "TDD"]
description = "A follow through of the great book Growing Object-Oriented Software, Guided by Tests with code"
featured = "featured-goos.jpg"
featuredpath = "/img"
title = "GOOS Book Distilled Part 14"
date = 2018-04-06T21:34:52+03:00
+++

This post covers *Chapter 17* of the book: *Teasing Apart Main*.

## Finding a Role

I think one of the most important skills that a programmer needs to train on, is the instinct of what and where to improve. The ability to see the flaws and have a clear vision of what he/she would like the software to work. And having the vision sometimes is more important than knowing how to achieve there. So how to have the vision in the first place? The answer is to be exposed to good stuff as much as possible. Really, this is true in developing all the skills. Once you know how you would like the final product to be, getting there is only a matter of time and practice. It's like if you pair programming with a master programmer, you don't need to remember every single move of his, but you do need to pay special attention to the high level vision that guides him there, like what makes he make the change? What bothers him normally and how to improve? and so on and so forth.

Enough rambling now back to our auction sniper project.

We feel that the main becomes a kitchen sink. It makes us uncomfortable and needs to be refactored. What are the things that are making us uncomfortable? This is a really good question and the author of the book anwsered it beautifully. First we should have a vision for a good **Main** of any program. What should **Main** do and what should it contain? The general idea is that **Main** should really only knows the top high level of the project. Initiate them, connect them and kick start the program then let go. That's it. Having that in mind and take a look at our **Main** should make you frown. It has the `Chat` from smack library, Swing related code, `SnipersTableModel`, `AuctionMessageTranslator` to name a few. They are too low level stuff and should be hidden from main.

## Extracting the Chat

The goal is to remove any reference of `Chat` from `Main`. We can move `Chat` into `XMPPAuction`, this makes sense because the `XMPPAuction` needs a `Chat` connection to do stuff anyways.

[Isolating the Chat](https://github.com/lvguowei/GOOS/commit/faa9da92796e94b1e9257198a0457c41bd6e1b0b)

[Encapsulating the Chat](https://github.com/lvguowei/GOOS/commit/0d8c16bc067b5db2e5bda04de9e85a70eed674f6)

[Writing a new Test](https://github.com/lvguowei/GOOS/commit/a91d8bf3a08229f8268b83ea812767e62b9d93d4)



