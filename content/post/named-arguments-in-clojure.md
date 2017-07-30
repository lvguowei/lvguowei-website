+++
date = "2017-02-24T22:24:05+02:00"
title = "Named Arguments In Clojure"
tags = ["clojure"]
categories = ["Functional Programming"]
keywords = ["clojure", "functional programming"]
+++

Sometimes I miss the named arguments feature in Python, for example:

{{< highlight python >}}

def slope(p1=(0,0), p2=(1,1))
  return (float(p2[1] - p1[1])) / (p2[0] - p1[0])
  
=> slope((1,2), (4,5))

=> slope(p2=(2, 1))

{{< /highlight >}}

The equivalent in clojure can be done using destructuring:

{{< highlight clojure >}}

(defn slope
  [& {:keys [p1 p2] :or {p1 [0 0] p2 [1 1]}}]
  (float (/ (- p2 1) (p1 1))
            (- p2 0) (p1 0)))
            
=> (slope :p1 [1 2] :p2 [3 4])

=> (slope :p2 [3 4])

=> (slope)

{{< /highlight >}}
