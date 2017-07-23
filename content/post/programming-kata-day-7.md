+++
date = "2017-02-17T20:32:20+02:00"
title = "Programming Kata Day 7"
categories = ["programming kata"]
+++

# Problem

Suppose a binary tree structure looks like this:

```
{:val value :L <left-node> :R <right-node>}
```
Write a function to balance insert node into the tree.

# Solution

{{< highlight clojure >}}

(defn insert [tree x]
  (cond (nil? tree)
        {:val x :L nil :R nil}

        (< x (:val tree))
        (assoc tree :L (insert (:L tree) x))

        :else
        (assoc tree :R (insert (:R tree) x))))

{{< /highlight >}}

# Note

Here is the function to traverse the tree:

{{< highlight clojure >}}

(defn traverse [tree]
  (when tree
    (concat (traverse (:L tree)) [(:val tree)] (traverse (:R tree)))))

{{< /highlight >}}

Here is a helper function to create a tree.

{{< highlight clojure >}}

(defn create-tree [& xs]
  (reduce (fn [result next] (xconj result next)) nil xs))

{{< /highlight >}}
