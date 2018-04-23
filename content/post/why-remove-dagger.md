---
title: "Why we stopped using dagger"
description: "And some thoughts"
date: 2017-06-27T20:40:41+03:00
categories: ["Android Development"]
keywords: ["Android", "Dagger2", "Dagger"]
featured: "featured-ranting.png"
featuredalt: "why we stopped using dagger"
featuredpath: "/img"
---

I decided to stop using Dagger2 in our company's android project. Why?

Don't get me wrong, Dagger2 is still great. But though it is great, it is complex. Lots of concepts to wrap our heads around. What is a *component*, what is a *module*, what is the difference between *subcomponent* and *dependency component*, etc. In order to use it properly, we need to anwser all that questions, even you think you understand it, you still run into suprises now and then.

In our case, the app has a handful of global singleton objects (mostly services, managers and such), and then for each activity or fragment there is a presenter.

If you see the *Application*, *Activity* and *Fragment* as the *main* of the program. Then actually *new* a new object is not that harmful there.

So we can *new* presenters directly in activities. Then, what about the global objects?

We can create them in *Application* when the app starts. And since *Singleton Pattern* is considered bad nowadays, we can just assign them in public static fields of the *Application* class.

Problem solved~


*Rant: seems to me that nowadays programmers are getting lazy, I mean not in a good way. We tend to follow BIG companys blindly, whatever they come up is always golden standard, like React and Dagger etc. Not saying that they are bad solutions, but they are solutions for a particular type of problem they are facing, you don't need to use them if you don't have the same problems. Of course, people tend to follow leaders, that is just human nature because it's easy to do so. But I would suggest that we should always try to understand the problem, then look for solutions, instead of we grab whatever is the latest and coolest solution and try to fit that into your problem. We have to understand that using power tools doesn't make the original problem go away, but then you have also to learn to use the tool which becomes a problem of its own. So think twice and make careful decisions. At the end of the day, we have to ship new features, our job is not to learn how to migrate from Dagger1 to Dagger2 and then to Dagger Android.*
