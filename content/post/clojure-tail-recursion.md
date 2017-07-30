+++
date = "2017-02-27T20:15:18+02:00"
title = "Clojure Tail Recursion"
tags = ["clojure", "recursion"]
keywords = ["functional programming", "clojure", "tail recursion"]
categories = ["Functional Programming"]
+++

What this `recur` and `tail calls optimization` is all about in Clojure? This blog post is trying to give a short yet easy to remember explanation.

Before explaining anything, let's look at how we can implement `+` using recursion.

This is actually an interesting task, implement our `+`, since most of the time `+` is a built-in function. So to make things a bit more clear, let's assume that our computer is drunk and forgets about how to do `+`, but it still remembers how to increment and decrement by 1. How about now?

So here is our first solution:

{{< highlight clojure >}}

(defn sum-mundane [x y]
  (if (= 0 y)
    x
    (inc (sum-mundane x (dec y)))))
    
{{< /highlight >}}

If you use the replacement technique to expand this recursion by hand, you will get something looks like this:

```
(inc (inc (inc (inc (inc (inc ... (inc x)))))))
```

There will be exactly `y` `inc`s.

It seems that if y is very big, then we need more paper to write the whole thing down! So the paper now is like our computer's memory, it means this recursion is using up a lot of memory!

Let's look at another approach:

{{< highlight clojure >}}

(defn sum-recur [x y]
  (if (= 0 y)
    x
    (recur (inc x) (dec y))))
    
{{< /highlight >}}

We can think of this as you have two piles of candies, and your Mom asks you to calculate the sum. Well as smart as a 2 year old baby, you don't know how. Then your Mom starts to move the candy one by one from one pile to the other one. After she is done, there is only one pile of candies left now. Now you suddenly know the answer.

Seems this approach doesn't need any extra space to do the calculation. Emmm...

Ok enough teaser. Let's discuss about the above two methods.

Turns out that the `sum-recur` is a tail recursion while the `sum-mundane` is not. One way to verify is that if you replace the `sum-mundane` inside the function call to `recur`, it won't compile.

There is a simple rule that can be used to differentiate the two:

*Tail recursion does not need to keep intermediate results in memory.*
