+++
date = "2017-03-06T21:02:55+02:00"
title = "Programming Kata Day 13"
categories = ["programming kata"]
+++

# Problem
The elevator can be on first floor or second floor
The elevator can be either openned or closed. 
The elevator can go up or down. But when it goes up or down, the door has to be closed.
The door can open or close, but it cannot open when it is already openned or close when it is already closed.

Write a function that takes a list of actions with :done indicating the end, and return if this sequence is legal or not.

This example comes from The Joy of Clojure 2nd.

# Solution

{{< highlight clojure >}}

(defn elevator [commands]
  (letfn [(ff-open [[_ & r]]
            #(case _
               :close (ff-close r)
               :done true
               false))
          (ff-close [[_ & r]]
            #(case _
               :open (ff-open r)
               :up (sf-close r)
               false))
          (sf-close [[_ & r]]
            #(case _
               :open (sf-open r)
               :down (ff-close r)
               false))
          (sf-open [[_ & r]]
            #(case _
               :close (sf-close r)
               :done true
               false))]
    (trampoline ff-open commands)))
    
{{< /highlight >}}
