+++
categories = ["Design and Architecture"]
description = "How Command Pattern has evolved overtime"
featured = "featured-command.png"
featuredpath = "/img"
keyboards = ["Command Pattern", "Design Pattern", "GoF", "Uncle Bob"]
title = "The Evolution of Command Pattern (I)"
date = 2017-08-26T09:52:18+03:00
+++

The [Command Pattern](https://en.wikipedia.org/wiki/Command_pattern) is one of my favourite design patterns. It is also a good example that design patterns do change over time. In part I, we talk about the original version from the Design Patterns book.

This pattern first appears in the famous [GoF](https://en.wikipedia.org/wiki/Design_Patterns) book, described as follows:
{{< img-post "/img" "design-pattern.jpg" "Design Pattern" "left" >}}

> Encapsulate a request as an object, thereby letting you parameterize clients with different requests, queue or log requests, and support undoable operations.

This definition may seem confusing if you are new to it, let's see a simple example.

Suppose we are building a PhotoShop like software. But since we are so innovative and believe in simplistic design, it has only two buttons, a circle button and a rectangle, and a white canvas. When pressed, the buttons draw a circle or a rectangle on the canvas. And user can also undo the drawing.

The key concept of this pattern is the `Command` abstract class, it looks like this.

{{< figure src="/img/command-class.png" >}}

Notice every command has two operations, `execute()` and `undo()`.

Now we can use it to model the drawings as different commands.

{{< figure src="/img/command-subs.png" >}}

Notice when create the concrete drawing command, we pass in a `Canvas` for it to draw on.

Now the two commands look quite the same, the only different part is one draws a `Circle` one draws a `Rectangle`. So we can refactor out the common parts.

{{< figure src="/img/command-refactor.png" >}}

And in the `Main` part of the program, we can just create a bunch of commands and execute or undo them.

{{< figure src="/img/command-main.png" >}}

Imagine we can create these commands and put them in the buttons, and when the user presses the button, the button just execute its command.

There are 2 benefits of doing this:

1. The button doesn't need to know the details of the commands associated with it, it only cares one thing, which is how to execute it (or undo it).
2. We can change the behavior of the buttons very easily but configure them with different commands, and the button itself doesn't need to be changed.

Here is the rest of the code if you want to run it yourself.

{{< figure src="/img/command-shape.png" >}}
{{< figure src="/img/command-canvas.png" >}}

We can go very far with this pattern, we can actually design the whole system based on this pattern. In this book [Agile Software Development Principles Patterns and Practices](https://www.amazon.com/Software-Development-Principles-Patterns-Practices/dp/0135974445) by Uncle Bob, it gives a very good example of how to design a payroll system by using command pattern in Chapter 12. The code in the book is in C++, I ported it to Java [here](https://www.lvguowei.me/post/payroll-case-study/).

{{< img-post "/img" "agile-book.jpg" "Agile Software Development" "leftcenter" >}}

This wraps up the discussion about the original Command Pattern. In next part, we will talk about a more complicated version of it, stay tuned.
