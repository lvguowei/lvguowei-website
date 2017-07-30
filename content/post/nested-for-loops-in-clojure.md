+++
date = "2017-02-03T20:36:18+02:00"
title = "Nested For Loops in Clojure"
tags = ["clojure"]
keywords = ["functional programming", "clojure", "tail recursion"]
categories = ["Functional Programming"]
+++

One awkwardness I haven't really get rid of when using Clojure is for loops. Especially nested for loops that modifies some global variables. I find some solutions online where people use nested recursion or atoms, but can we just use one level of recursion? Let's try out with a coding kata.

Problem: Given an array of numbers, find the biggest sum of any two numbers. The same item in array cannot be used twice.

The easy way of solving this is to loop through all possible pairs and compute the sum and then compare.

{{< highlight java >}}
public int maxSum(int[] arr) {
  int result = 0;
  for (int i = 0; i < arr.length(); i++) {
    for (int j = i + 1; j < arr.length(); j++) {
      int sum = arr[i] + arr[j];
      if (sum > result) result = sum;
    }
  }
  return result;
}

{{< /highlight >}}

Let's now try to convert this into Clojure.

{{< highlight clojure >}}

(defn max-pair
  [v]
  (loop [i 0
         j 1
         result 0]
    (if (= i (dec (count v)))
      result
      (let [new-result (max res (+ (nth v i) (nth v j)))]
        (if (= j (dec (count v)))
          (recur (inc i) (-> i inc inc) new-result)
          (recur i (inc j) new-result))))))

{{< /highlight >}}
