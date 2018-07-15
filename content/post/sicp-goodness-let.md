+++
categories = ["SICP"]
description = "A guess about the origin of the new let keywork in javascript"
keywords = ["SICP", "JavaScript", "ES6 let", "ES6", "structure and interpretation of computer programs"]
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - A guess of why was the name 'let' chosen for block-scoped variable declarations in JavaScript"
date = 2018-06-28T21:59:58+03:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

In this post I give my guess of where the new **let** keyword in JavaScript comes from.

I got this idea from (of course) reading one chapter of the SICP book: **Using** *let* **to create local variables**.

Suppose we want to compute the function:

`f(x, y) = x(1 + xy)^2 + y(1 - y) + (1 + xy)(1 - y)`

which we could also express as

`a = 1 + xy`

`b = 1 - y`

`f(x, y) = xa^2 + yb + ab`

How do we do this? You may think of assignment first, but remember the book hasn't introduced the concept of assignment yet.

OK, turns out whenever you get stuck in functional programming languages, the anwser is almost always **lambda**!

{{< highlight scheme >}}
(define (f x y)
  ((lambda (a b)
    (+ (* x (square a))
       (* y b)
       (* a b)))
   (+ 1 (* x y))
   (- 1 y)))
{{< /highlight >}}

This is not hard to read, basically we just created a *lambda* procedure with parameters *a* and *b*, and then immediately call it with the right parameters (reminds you of [IIFE](https://developer.mozilla.org/en-US/docs/Glossary/IIFE) in JavaScript?).

So a new lambda function is the way to create local variables in Scheme.

This construct is so useful that there is a special form called **let** to make its use more convenient.

{{< highlight scheme >}}
(define (f x y)
  (let ((a (+ 1 (* x y)))
        (b (- 1 y)))
    (+ (* x (square a))
       (* y b)
       (* a b))))
{{< /highlight >}}

The way this happens is that the **let** expression is interpreted as an alternate syntax for the previous lambda way.

Let's now take a look at the JavaScript side.

Before ES6, in order for this to work as expected, we need to wrap *i* into a function call.

{{< highlight javascript >}}

var callbacks = [];
for (var i = 0; i <= 2; i++) {
    (function (i) {
        callbacks[i] = function() { return i * 2; };
    })(i);
}
callbacks[0]() === 0;
callbacks[1]() === 2;
callbacks[2]() === 4;

{{< /highlight >}}

With the new **let** in ES6, we can get rid of the function call, because **let** has proper block scoping.

{{< highlight javascript >}}

let callbacks = []
for (let i = 0; i <= 2; i++) {
    callbacks[i] = function () { return i * 2 }
}
callbacks[0]() === 0
callbacks[1]() === 2
callbacks[2]() === 4

{{< /highlight >}}

See how  **let** improves the IIFE in JavaScript.

I'm not saying the **let** in Scheme and JavaScript are the same, but there is clearly connection between the two.

I heard that JavaScript is a bastard of Scheme and Java, now finally JavaScript added what was in Scheme decades ago. Welcome home, kid.

