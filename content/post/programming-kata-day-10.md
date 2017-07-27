+++
date = "2017-02-23T20:36:33+02:00"
title = "programming kata day 10"
categories = ["programming kata"]
featured= "featured-code-kata.jpg"
featuredalt= "Code kata"
featuredpath= "/img"
+++

# Problem

Here is a list of students' exam scores. Write a function to sort them based on given criteria. Like Math first, then Physics and then chemistry and then English.

```
(def exam-scores [{:math 78 :physics 80 :english 97 :chemistry 65}
                  {:math 78 :physics 80 :english 66 :chemistry 65}
                  {:math 78 :physics 54 :english 97 :chemistry 65}
                  {:math 78 :physics 80 :english 97 :chemistry 61}
                  {:math 100 :physics 89 :english 47 :chemistry 85}
                  {:math 98 :physics 80 :english 79 :chemistry 65}])
```

# Solution

{{< highlight clojure >}}

(defn rank [scores & criteria]
  (reverse 
    (sort-by 
      (fn [score] (mapv score criteria)) scores)))

{{< /highlight >}}

# Note
The `sort-by` in Clojure is very powerful, the idea is to reduce each row into one value that we know how to sort, like numbers, strings or lists.
