+++
title = "Understand Clojure Transducers 1"
date = "2017-04-02T08:35:01+03:00"
tags = ["clojure", "transducer"]
categories = ["programming"]
+++
In this post, we will define what is a *transducer*.

First, let's take a closer look at *map*.

{{< highlight clojure >}}
(map inc [1 2 3])
{{< /highlight >}}

A straightforward way to explain what has happened is this:
Increment each item by one in a list.

At first thought, this sounds like one step operation.

Now let's change the requirement a bit. We want to increment each element by one and then sum them together.

This is very similar to what *map* is except it needs more processing after.

Let's do it by using a *reduce*.

{{< highlight clojure >}}
(reduce (fn [acc n] (+ acc n 1)) 0 [1 2 3])
{{< /highlight >}}

Now this looks like a two step operation:

1. Increment each element by one
2. Sum them together

Emm... Now if we rethink the *map* example, it can actually be expressed as a two step operation as well:

1. Increment each element by one
2. Create a new list out of the incremented elements

To make it more clear, let's also write *map* using *reduce*:

{{< highlight clojure >}}
(reduce (fn [acc n] (conj acc (inc n))) [] [1 2 3])
{{< /highlight >}}

Now realize that the first step is the same. Can we somehow extract that out, so it can be reused in both examples? The ideal format would be something like:

{{< highlight clojure >}}
((mapping inc) *second-step*)
{{< /highlight >}}

This *(mapping inc)* is a function that takes a *second-step* and returns a reducing function that can be used by *reduce*. Also note that *(mapping inc)* is only saying *map the inc function into each item*, and doesn't specify what should we do next. The *second-step* defines what to do with the result of *increment every item by one*.

{{< highlight clojure >}}
(defn mapping [f]
  (fn [second-step]
    (fn [acc n]
      (second-step acc (f n)))))
{{< /highlight >}}

Now what the *mapping* function returns is called a *transducer*. It takes a *second-step* function and returns a reducing function. Also note that the *second-step* function is also a reducing function. Thus, we can define a transducer to be a function that takes a reducing function and returns a new reducing function.

*~To be continued~*
