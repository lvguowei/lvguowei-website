---
title: "Strategy Pattern And Lambda"
date: 2017-07-03T12:01:35+03:00
tags: ["design pattern", "lambda", "functional programming"]
categories: ["programming"]
---

While reading *Effective Java* Item 21: **Use function objects to represent strategies**, something hit my mind and now I'm writing it down.

All these new *lambda* thing is really what is called the *Strategy pattern* in the OOP world, or would it be more appropriate to say that the *Strategy Pattern* in Design patterns is really what *lambda* is.

The essence of all these, can be boiled down to one simple idea, pass functionalities around. The signature of a function is just input and output with possibly side effects. How to pass a function around is what we are really looking at here.

Of course this is not even a problem in functional programming languages, because functions are first class citizens there, the building blocks, so refering to them and passing them around just comes for free.
But things get more difficult in OO languages like Java, since everything is an object, functions are suddenly hidden from the public views, functions can  no longer exist by themselves, they need some kind of media, like an object, to exist. So we cannot pass just functions around even though that is what we really need. The only solution to this is to create a object that has the function we want, and then pass the object. And they give it a fancy name called *Strategy Pattern*.

I think this is a clear sign that not everything is appropriate to be represented by an object and class. If you force to do so, then we have to do this ugly wrapper around a function and invent another concept , like *strategy*, which is just another name of *function*.
