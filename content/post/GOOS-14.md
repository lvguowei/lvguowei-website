+++
categories = ["Object Oriented Design"]
keywords = ["Growing Object Oriented Software Guided by Tests", "TDD"]
description = "A follow through of the great book Growing Object-Oriented Software, Guided by Tests with code"
featured = "featured-goos.jpg"
featuredpath = "/img"
title = "GOOS Book Distilled Part 13"
date = 2018-03-24T09:48:48+02:00
+++
# Adding Items through the User Interface

## A Simpler Design

Now the UI designer finally catches up and provides a sketch of the new user interface, with one text field and a **Join Auction** button.

{{< figure src="/img/goos-13-1.png" >}}

## Update the Tests

Now with these user interaction in place, the first thing that needs to be changed is the way `ApplicationRunner` runs the application. Previously, when the `startBiddingIn()` gets called, it just calls `Main` with some parameters. But now in order to *start bidding*, the `ApplicationRunner` has to find the text input, type in the item id, and click the **Join Auction** button. So we modify it accordingly.

[Source code](https://github.com/lvguowei/GOOS/commit/c9a0e6aa361bf8ff0fd639ab050d6f63d0eab766)

## Adding an Action Bar

We add the text field and the button in the window.

[Source code](https://github.com/lvguowei/GOOS/commit/458446bd35c11ed8f90ae511dd51104fbb182679)

## A Design Moment

So now the new UI elements are added, but they are not hooked up with anything yet. So the basic question is what do we do when the user click the `Join Auction` button. We know what to do, add a new `Joining` row to the table and send a `JOIN` message to the server, right? Yes, but there is one problem, the button is in `MainWindow`, it does not have access to thoes functionalities. So how to solve this? Yes, you guessed it again, introduce an interface. We call it `UserRequestListener`.

[Source code](https://github.com/lvguowei/GOOS/commit/346763317f1f4fb8d6768ff726e69e7fd561f522)

## Another Level of Testing

Ok, now we want to test that when the `Join Auction` button clicked, the `joinAuction` on `UserRequestListener` will get called. The author called this test *Intergration Test*. But I am not quite sure how to understand that.

[Source Code](https://github.com/lvguowei/GOOS/commit/14d867e735f6fd4a7dee03e49ae159f25a5b4b9c)

## Implementing the UserRequestListener

This is very straight forward. When the `main()` gets called, we set up the `UserRequestListener` and give it to `MainWindow`. If you pay attention to the source code, I removed the `-` in the item ids. Because for some weird reason, when some UI tests like *replace all the text with something else* runs, it can not select actually all the text, the `-` will block the selection somehow. So I think for now the easiest solution would be just to remove the dashes in the item ids. If you know why this happens please let me know.

[Source Code](https://github.com/lvguowei/GOOS/commit/2a304a84b92bf194e535a64f91c1f238c38dd62b)

This wrapped up Chapter 16.
