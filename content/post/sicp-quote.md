+++
categories = ["SICP"]
description = "Find the right level of abstraction"
featured =  "featured-sicp.jpg"
featuredpath =  "/img"
title = "SICP Quote"
date = 2017-10-06T20:05:49+03:00
+++

Since I have to take a long bus trip to work everyday, I decided to use the time to read the SICP (Structure and Interpretation of Computer Programs). And blog along the way to mark the progress.

I have read Chapter 1 and watched the video lectures. But I still feel I get planty new things from going through it again. The best part of this book I think is that it spends minimal time and effort in teaching the programming language, the main focus is the thinking and ideas about programming. Sometimes it also teaches best practices that a professional programmer should follow. We may have an impression that this book is all about acedemic theories, but at the end of Chapter 1, there is this paragraph that completely changed my view. I feel that it even deserve a blog post just to repeat it here.

>As programmers, we should be alert to opportunities to identify the underlying abstractions in our programs and to build upon them and generalize them to create more powerful abstractions. **This is not to say that one should always write programs in the most abstract way possible**; expert programmers know how to choose the level of abstraction appropriate to their task. But it is important to be able to think in terms of these abstractions, so that we can be ready to apply them in new contexts.

This idea of finding the balance point of abstraction is vital. Bad code either lacks abstraction or has too much of it.

If the system lacks abstraction, the programmer may be lost in the sea of implementation details. On the other hand, if a system is all about abstraction, it will have a lot of indirections and thus complicate the problem too much.
