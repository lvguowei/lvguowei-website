+++
categories = ["Object Oriented Design"]
keywords = ["Growing Object Oriented Software Guided by Tests", "TDD"]
description = "A follow through of the great book Growing Object-Oriented Software, Guided by Tests with code"
featured = "featured-goos.jpg"
featuredpath = "/img"
title = "GOOS Book Distilled Part 11"
date = 2018-03-06T20:37:40+02:00
+++

>This is a series of blog posts going through the great book [**Growing Object Oriented Software Guided By Tests**](https://www.amazon.com/Growing-Object-Oriented-Software-Guided-Tests/dp/0321503627), typing in code chapter by chapter, trying to add some of my own understanding where things may not be easy to grasp in the book. I highly recommand you get a copy of the book and follow along with me. Happy coding.

# Simplifying Sniper Events

## Listening to the Mood Music
Let's take a closer look at how `AuctionSniper` notifies its `SniperListener`.

{{< img-post "/img" "goos-11-1.jpg" "Too many method in listener" "center" >}}

In previous post, we modified `sniperBidding()`, so now it takes a `SniperState` as parameter. The original plan is to change all the methods to use the `SniperState`, but that feels like a lot of repeating work. What is wrong?

The problem is caused by a subtle `duplication`. What is duplicated you may ask. The ways we send message to the listener got duplicated. The information is sent through two means to the listener. One is through the `SniperState`, one is through the methods themselves. So in other words, the data that got sent to the listener is living in two different places.

Turns out this is a very common problem. One object is calling many methods on another object, and they are all on the same conceptual level. In this case, we can simplify things by collapsing all those methods into one, which takes one kind of a Command argument, the Command encapsulate the data and action type.

For example:

Imaging in an music player app, the controller can do the following:

{{< highlight java >}}
controller.playMusic("xxx");
controller.next();
controller.removeSong(3);
controller.previous();
controller.addToNext("xxx");
controller.stop();
{{< /highlight >}}

This can be simplified to:

{{< highlight java >}}
class abstract PlayerCommand { ... }
class PlayMusicCommand extends PlayerCommand { ... }
class NextCommand extends PlayerCommand { ... }
...

interface Controller {
  void execute(PlayerCommand command);
}

...

controller.execute(new NextCommand());
{{< /highlight >}}

One big benefit of doing this, is that now the `Controller` interface is decoupled from the concrete command, and becomes easier to maintain and extend.

Having this general goal in mind, the next three parts are just steps to fulfill this goal.

[Repurposing sniperBidding()](https://github.com/lvguowei/GOOS/commit/e4880dc2339fc31ad018f47b7af60f56938c467d)

[Filling in the Numbers](https://github.com/lvguowei/GOOS/commit/b44f3bf388205956cb3b3e1d596aa24f9193ade2)

[Converting Won and Lost](https://github.com/lvguowei/GOOS/commit/8a47e537edc6750a4e0000a811bd7b89edb25210)

## Trimming the Table Model

The strings for sniper states, `Joining`, `Bidding`, `Winning`, `Lost`, `Won`, are in the `MainWindow` class. They fit better in the `SnipersTableModel`, so we moved them there.

[Source code](https://github.com/lvguowei/GOOS/commit/8452b1e37c4195b836164de0ac83b5611575b4c0)

## Object-Oriented Column

This is a very classic refactoring of OO design. Replace `switch` by polymorphism. We apply this to the `Column`, study the code if you are unfamiliar with this trick.

[Source code](https://github.com/lvguowei/GOOS/commit/dec0872bb7c11bd58a1a3b707aa32d5c68b1b456)

## Shortening the Event Path

Since the `SnipersTableModel` is inside the `MainWindow` class, `MainWindow` has to foreward the calls to update `SnipersTableModel`. We can acually simplify this by just let the `SnipersTableModel` implement `SniperListener` directly and pull it out into the `Main` class. And when the `MainWindow` is created, we pass it in.

This is what the system looks like now:

{{< figure src="/img/goos-12.jpg" >}}

[Source code](https://github.com/lvguowei/GOOS/commit/0f426afb8aa950658f7ca341e052a6d34729d71f)

## Final Polish - Add the Column Titles

Now we add titles for columns, which is pretty straight foreward, so no more explanation here.

[Source code](https://github.com/lvguowei/GOOS/commit/e65b2fa123a38af0a00ab0b091be6da189e51274)

This finishes Chapter 15.
