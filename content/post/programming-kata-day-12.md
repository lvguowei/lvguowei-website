+++
date = "2017-02-26T16:38:38+02:00"
title = "programming kata day 12"
categories = ["programming kata"]
+++

# Problem

Implement a unit converter that uses the given the following metric

```
(def metric {:meter 1
             :km 1000
             :cm 1/100
             :mm [1/10 :cm]})
```
The converter should anwser questions like:

How many meters are there in 10 km and 20 cm?

# Solution

{{< highlight clojure >}}

(defn convert [context descriptor]
  (reduce (fn [result [mag unit]]
            (let [val (metric unit)]
              (if (vector? val)
                (+ result (* mag (convert context val)))
                (+ result (* mag val)))))
          0
          (partition 2 descriptor)))
          
(convert metric [1 :meter])
(convert metric [3 :km 10 :meter])

{{< /highlight >}}
