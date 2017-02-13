+++
date = "2017-02-13T21:34:13+02:00"
title = "programming kata day 5"
tags = ["clojure", "programming kata"]
categories = ["programming"]
+++

# Problem

Write a function to determine if a number is a triangular number.

# Solutions

# Solution 1

{{< highlight clojure >}}

(defn nth-triangle [n]
  (apply + (range (inc n))))

(defn is-triangle-number [n]
  (loop [i 0]
    (cond (> n (nth-triangle i))
          (recur (inc i))
          (= n (nth-triangle i))
          true
          :else
          false)))

{{< /highlight >}}

# Solution 2

{{< highlight clojure >}}

(defn is-triangle-number [n]
  (loop [tri 0
         i 1]
    (cond (> n tri)
          (recur (+ tri i) (inc i))
          (= n tri)
          true
          :else
          false)))

{{< /highlight >}}

# Note

Solution 1 is the first that came to my mind, but it has a typical problem: redundant calculation. Solution 2 solves it by keeping the last triangular number.
