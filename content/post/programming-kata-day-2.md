+++
date = "2017-02-07T19:34:17+02:00"
title = "Programming Kata Day 2"
tags = ["clojure", "programming kata"]
categories = ["Programming Kata"]
+++

Problem: Write a function to calculate fibonacci number in constant space complexity.

{{< highlight clojure >}}

(defn fib [n]
  (loop [a 0
         b 1
         i n]
    (if (= 0 i)
      a
      (recur b (+ a b) (dec i)))))
      
{{< /highlight >}}

