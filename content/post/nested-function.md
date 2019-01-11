+++
author = "Guowei Lv"
categories = ["Programming Misc"]
description = "How nested function can help"
featured = ""
featuredpath = ""
featuredalt = ""
title = "How Nested Function Can Help"
date = 2019-01-11T19:52:58+02:00
+++

I have been exposed to a lot of Jonathan Blow's ranting videos recently. One of his rant is that he does not like to break long functions into smaller ones. Main reason being those small functions may mislead the reader that they are reusable while actually they are normally not.

First of all, this is kind of the opposite of what uncle bob is saying. He talks a lot in his book and video lectures about how he loves to break up big functions into smaller ones.

I think in general they are kind of both correct. But I do see Jon's concern.

Is there a way to write smaller functions and they do not get misused elsewhere?

The answer simple: nested functions!

A lot of languages support that, so use it next time when you break big functions.
