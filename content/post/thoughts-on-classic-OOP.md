+++
author = "Guowei Lv"
categories = ["Thoughts"]
description = "Thought on the classic OOP mindset"
title = "Thoughts on Classic OOP Mindset"
date = 2019-03-06T22:16:33+02:00
+++

OOP is still dominating in today's software shops, especially in mobile/web development. Design patterns, SOLID principles and TDD etc. are the mainstream mindset to develop OOP software.

I still remembered my first programming job. Huge codebase, don't know where to start. I even took notes of the code in my notebook and drew graphs of it. But still didn't have a clue after years gone by. My desk was by the window, I used to look outside the window while standing beside it a lot. My supervisor often joked that it looked like I would open it and jump at any moment.

The one question I used to have always was "What is wrong with me? Why after years of dealing with the code, I still do not understand it?"

Then I discovered Uncle Bob and Martin Fowler. The refactoring techniques and SOLID principles made me believe that nothing is wrong with me, it is simply near impossible to understand messy code.

I watched all the [Uncle Bob videos](https://cleancoders.com/) and read a lot of books about design patterns and refactoring.

They are pretty helpful at that stage of my career. It teaches me how to structure code so that it is *maintainable*.

I felt I know a lot more about how to build software then ever. I became a *senior* almost over night.

What next? I bought more books of that kind. Now I have:


{{< figure src="/img/oopbooks.jpg" >}}


[Design Patterns](https://amzn.to/2XN9Hoq)

[Writing Effective Use Cases](https://amzn.to/2C7VBF4)

[Rapid Development](https://amzn.to/2C9lmoF)

[The Pragmatic Programmer](https://amzn.to/2tZ4Y5n)

[Growing Object-Oriented Software Guided by Tests](https://amzn.to/2To81U9)

[The Unified Modeling Language User Guide](https://amzn.to/2SPDMjN)

[Pattern Hatching Design Pattern Applied](https://amzn.to/2VH1TTB)

[Pattern-Oriented Software Architecture Volume2 Patterns for Concurrent and Networked Objects](https://amzn.to/2Hp4ptT)

[Pattern-Oriented Software Architecture Volume1 A System of Patterns](https://amzn.to/2C9lSCV)

[Domain-Driven Design](https://amzn.to/2TmTkAA)

[Object-Orientation and Prototyping in Software Engineering](https://amzn.to/2HlHboc)

[Object-Oriented Software Engineering - A Use Case Driven Approach](https://amzn.to/2TmIhaK)

[Patterns of Enterprise APplication Architecture](https://amzn.to/2SRPWbQ)

[Refactoring](https://amzn.to/2Toln2o)

15 in total! About 2/3 of all my programming books!

I have read like 6 of them, and 3 of them cover to cover.

Are they helpful? *Yes*

Are they easy to understand? *Kind of*

Are they applicable to real world codebase? *yes, but most likely not directly*

Are they enough? **NO!!!**

Programmers who have like 5+ years experience tend to think that these are the only books that they need to read if they want to advance their career.

I used to think that way, now I realized that I was totally wrong.

Let's first talk about the subtle drawbacks of these books.

All programming / computer science considered, these type of *architecture* knowledge is only a very small subject. Notice that these books do not teach any concrete technologies nor math. So whatever techniques recorded in the books are not proven to be correct and most of them are still debatable nowadays. Another character of these books is that they cannot often give precise definition of the thing that they are talking about. That results in a lot of confusing when applying them in real world senarios. It is easy to see the benefit of applying these patterns, but hard to see the subtle drawbacks that it brings to the codebase in the long run.

Let's give an example. The DRY principle.

This is almost the very first thing that any programming educational material will teach to novice programmers. *Duplication is very very bad. Do not have any place in your code that are duplicated. If you see one, you should immediately refactor the common part out.*

First of all, this is obviously too strict. If you've lived long enough, you should know that anything that is stated this harsh and strict, simply cannot be true.

If you pull out the common part too early, a lot of times you are wrong. They just happens to be the same now, but will evolve into different direction later on. So the common part has to either break or be parameterized. If you have too many this kind of broken abstractions, your code will be much harder to read and modify. I'm not saying that do not abstract ever, but you have to be prepared to undo the abstraction after you sense that they are wrong. But often lazy programmers will just parameterize the abstraction instead of breaking them completely.

There are many other subjects in programming that are much more solid. Algorithms, data structures and even math are much more worth studying imho.

So do not spend all your time reading, following and fighting design patterns and clean code. Read about algorithms, data structures, AI, linear algebra, compilers, lambda calculus, logic programming, game engine, etc. and you will be in much better shape.
