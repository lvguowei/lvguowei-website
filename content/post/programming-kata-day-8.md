+++
date = "2017-02-18T13:54:42+02:00"
title = "Programming Kata Day 8"
tags = ["clojure", "programming kata"]
categories = ["Programming Kata"]
+++

# Problem

Write a function `steps`, that takes a sequence and makes a deeply nested structure from it:

```
(steps [1 2 3 4])
;=> [1 [2 [3 [4 []]]]]
```
# Solution 1

{{< highlight clojure >}}

(defn steps [s]
  (if (seq s)
    [(first s) (steps (rest s))]
    []))
    
{{< /highlight >}}

# Solution 2

Lazy version:

{{< highlight clojure >}}

(defn lz-steps [s]
  (lazy-seq
   (if (seq s)
     [(first s) (lz-steps (rest s))]
     [])))
     
{{< /highlight >}}

# Note
To see the difference, call the function like this:

```
(take 1 (steps (range 10000000)))

(take 1 (lz-steps (range 10000000)))
```
