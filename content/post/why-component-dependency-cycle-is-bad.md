+++
date = "2017-01-01T13:51:26+02:00"
title = "Why component dependency cycle is bad"
categories = ["Object Oriented Design"]
keywords = ["software", "OO design", "dependency cycle"]
featured = "featured-dependency-cycle.png"
featuredpath = "/img"
+++

I have been working on some Clojure project at work for several months now, one little thing bothers me now and then is that it doesn't allow dependency cycle in project. For example if `a.clj` requires `b.clj`, `b.clj` requires `c.clj`, then `c.clj` cannot require `a.clj`, in other words, `c.clj` cannot use anything inside `a.clj`.

At first, I thought this is a bit odd, java doesn't have that. And once in a while we have to solve such problems by creating a new namespace and move the common function into it. I asked my colleagues and still couldn't find a very satisfying explanation. Until I learned about the Acyclic Dependency Principle.

>The acyclic dependencies principle (ADP) is a software design principle that states that "the dependency graph of packages or components should have no cycles". This implies that the dependencies form a directed acyclic graph.

Let's look at an example to see why it is not desirable to have cycles in the dependency graph.

{{< figure src="/img/acyclic-dependency.jpg" >}}

The original graph contains all the black arrows. The red arrow is added as a shortcut to use some display function in the `User Interface` component.

This is bad in several ways.

## It's bad for release
Let's assume that the system now is under v1.0 and planing to make the release of v1.1. 

### Without the cycle
The project manager can take the following approach: `ErrorHandler` and `DBModel`teams can work on v1.1 first and make their releases. After that they may start working on already v1.2. Then `Deposit` and `Database` can be worked on based on the v1.1 `ErrorHandler` and `DBModel`. And finally the `UserInterface` component can be worked on. This is like a wave traveling upwards the dependency graph, and every component can be still independently developed.

### With the cycle
Now with that dependency cycle, it is not very clear where should we start! Say we want to start from `ErrorHandler`. In order to make v1.1 of `ErrorHandler`, we need v1.1 of `User Interface`, which needs v1.1 of `Deposit`, which need v1.1 of `ErrorHandler`. So the only way is to group together all three components and develop them together for the new v1.1, since they all depend on each other. We lose the independent developability here.

## The system is now more coupled
Let's take a closer look at the `ErrorHandler` component again, what does it depend upon?

### Without the cycle
Nothing.

### With the cycle
Everything!

OK, now we are convinced that this is a bad idea but how can we fix this?

## Move the common part into a new component
The most straightforward way is to create a new component, moves the part that used by both `UserInterface` and `ErrorHandler` into it.

{{< figure src="/img/acyclic-dependency2.jpg" >}}

## Use Dependency Inversion Principle

Use the good old DIP.

{{< figure src="/img/acyclic-dependency3.jpg" >}}

