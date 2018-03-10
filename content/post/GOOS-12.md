+++
categories = ["Object Oriented Design"]
keywords = ["Growing Object Oriented Software Guided by Tests", "TDD"]
description = "A follow through of the great book Growing Object-Oriented Software, Guided by Tests with code"
featured = "featured-goos.jpg"
featuredpath = "/img"
title = "GOOS Book Distilled Part 11"
date = 2018-03-06T20:37:40+02:00
+++

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



