+++
date = "2017-02-12T11:08:14+02:00"
title = "Programming Kata Day 4"
categories = ["programming kata"]
+++

# Problem

Implement `take-nth`.

# Solutions

# Solution 1


{{< highlight clojure >}}

(defn my-take-nth [n col]
  (loop [i 0
         result []]
    (if (= i (count col))
      result
      (if (= 0 (mod i n))
        (recur (inc i) (conj result (nth col i)))
        (recur (inc i) result)))))

{{< /highlight >}}

# solution 2

{{< highlight clojure >}}

(defn my-take-nth2 [n col]
  (->> col
       (map-indexed (fn [i x] [i x]))
       (filter (fn [[i x]] (= 0 (mod i 2))))
       (mapv (fn [[i x]] x))))

{{< /highlight >}}
