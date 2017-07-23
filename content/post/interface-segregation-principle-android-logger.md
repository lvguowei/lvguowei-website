+++
title = "Use Interface Segregation Principle to Implement an Android Logger"
date = "2016-12-08T21:34:15+02:00"
tags = [ "Design Pattern", "SOLID Principle", "uncle bob", "refactoring", "Android" ]
categories = ["Object Oriented Design"]
+++

Recently at work we has been talking about implementing some kind of Analytic interface for all the analytic libraries we are using, like Localytics and Firebase and so on.
Basically it is just a fat interface with a long list of event logging functions, like `logSignIn()`, `logSignOut()`, `logSellProduct()`, `logOpenMap()` and so on. There are about 40 such methods in that interface. So this is how we implemented it in the first place.

{{< figure src="/img/analytics.png" >}}

Based on the spirit of the **Interface Segregation Principle**: don't depend on things you don't need, there seems to be a lot of room to improve.

## Proposal 1

We gotta separate that interface! This seems the obvious and reasonable thing to do, and different modules can only use the interface that they need. But, with only one problem, there seems to be too many classes!

{{< figure src="/img/analytics2.png" >}}

## Proposal 2

We can use multiple inheritance to solve this problem.

{{< figure src="/img/analytics3.png" >}}

Now all problem solved, modules only depend on interface they need, and there are reasonable amount of classes.
