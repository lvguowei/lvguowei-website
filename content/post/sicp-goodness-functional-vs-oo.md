+++
author = "Guowei Lv"
categories = ["SICP"]
description = "Functional programming, Imperative programming, Object Oriented and Streams"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - Functional v.s. Object Oriented Programming?"
date = 2019-01-13T20:52:15+02:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

Functional Programming is coming back in recent years, so people who have been doing Java or C++ for their entire career will want to know what is the difference.

For a long time, my understanding on this is blury(I didn't even realize that it was blury until I read SICP).

I have been asking around directly or indirectly about this, and the answer I get is something along the lines of the following.

>Function is a first class citizen, you can pass function in, you can get function out. No need for classes and objects.

>It's like you do not make changes to objects, instead you return a new one.

>Those convenient things like map, filter and reduce.

> No side-effects.

They all make sense, but somehow not satisfying.

# What are we comparing

Let me first list 4 terms. After going through them one by one, I think the answer will be clear. (By no means I am claiming what I am going to say is 100% correct academically, but just some thoughts after reading the book)

- Imperative programming

- Functional programming

- Object Oriented

- Streams

# Imperative programming and functional programming

I will quote from the book:

> In contrast to functional programming, programming that makes extensive use of assignment is known as imperative programming.

So the keyword here is **assignment**. If in your program you use assignment heavily, you are doing imperative programming.

On the other hand, if you use no assignment, you are doing functional programming.

You may question how to even write programs without assignment? Believe it or not, in SICP book, before Chapter 3, there is no assignment anywhere in the code, that is 216 pages of not so trivial programming!

You may notice that these two terms sounds very high level and lacking details. That is because they are like that, and I'd rather keep it like this than making things more complicated.

# Object Oriented and Streams

I will again quote from the book:

>To a large extent, then, the way we organize a large program is dictated by our perception of the system to be modeled. In this chapter we will investigate two prominent organizational strategies arising from two rather different “world views” of the structure of systems. The first organizational strategy concentrates on objects , viewing a large system as a collection of distinct objects whose behaviors may change over time. An alternative organizational strategy concentrates on the streams of information that flow in the system, much as an electrical engineer views a signal-processing system.

For example, [the digital circuits simulator](https://www.lvguowei.me/post/sicp-goodness-digital-circuits-sim/) example can be translated very easily to Java, because it is basically object oriented programming done in Scheme.

We have not talked about Streams yet in this blog series, but will do very soon.

Have you noticed it? Functional programming and Object Oriented programming are on two different levels, they are not even compared in the book.

So the right question to ask is what is the difference between Object Oriented v.s. Streams.

We will find out soon so stay tuned!

