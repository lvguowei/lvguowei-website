+++
date = "2017-02-15T20:16:33+02:00"
title = "Programming Kata Day 6"
tags = ["clojure", "programming kata"]
categories = ["programming"]
+++

# Problem

Write a function to determine if a vector contains a set of items.

# Solution

{{< highlight clojure >}}

(defn containsv [v & items]
  (some (set items) v))

{{< /highlight >}}

# Note

Using a set as the predicate supplied to `some` allows you to check whether any of the truthy values in the set are contained within the given sequence. This is a frequently used Clojure idiom for searching for containment within a sequence.
