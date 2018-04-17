+++
categories = ["JavaScript"]
description = "What happened when I tried to follow the first lesson in JavaScript30"
featured = "js30-bg.jpg"
featuredpath = "/img"
title = "My Experience with First Day of JavaScript30"
date = 2018-04-17T15:51:02+03:00
+++

It's nice weather today, so I decided to give JS another chance.

For thoes who don't know yet(really, if you want to do JS you should know already, haven't you been reading everything in JS weekly every week?). There is a free course called [JavaScript30](https://javascript30.com/) offered by [Wes Bos](https://wesbos.com/), in which you build 30 small project using vanilla JS. Since it is perceived pretty well in JS community, I decided to start from there. What things can go wrong with a few lines of plain JS code?

A brief on the first project here FYI. The page looks like this:

{{< figure src="/img/js30.png" >}}

And when you press a key on keyboard, it plays a sound and quickly animates that key on screen.

I checked out the source code, followed the video, and the project was working great. Or is it?

When I keep the key pressed, it output some crazy sounds and the highlight didn't go away.

{{< figure src="/img/js30-w.png" >}}

What could cause this? In the code, each key `div` is listening to a `transitionend` event to remove the highlight. So it must be that the this event did not happen when you keep the key pressed down.

I tried to print out some log to see if this is the case. Only to find out this not only this event did happen, but it happened TWICE for each single key press.

Then I googled "why transitionend event happens twice", no avail.

Maybe this is beyond this project, anyway handling key pressed state may not be trivial. So I did a fix to ignore the event if `e.repeat` is true. Now at least if you keep the key pressed, nothing will happen.

Since I know very little JS, I want to learn more about the `document.querySelectorAll()` to see what it can do. Then I found this [article from CSS Tricks](https://css-tricks.com/snippets/javascript/loop-queryselectorall-matches/). Basically it says that `querySelectorAll` does not return an `array`, it returns a `NodeList`. And if you try to call `forEach` on it, on some browsers it may fail. And one common way to solve this obviously has [10 drawbacks](https://toddmotto.com/ditch-the-array-foreach-call-nodelist-hack/). So we should implement our own `forEach` just for this case.
OK, I didn't remember Wes said anything about this in his video. Let's take a look at the solution code. AHA, there he used not some custom implementation of `forEach`, but a `Array.from()`. Checked doc on that, it says it creates an array out of an  array-like object. And a quick Googling shows that clearly this is the current way to do it.

Now my mind can rest in peace.
