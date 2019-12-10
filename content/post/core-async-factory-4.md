+++
author = "Guowei Lv"
categories = ["Clojure"]
description = "Conveyer Belt"
featured = "assembly-line.jpg"
featuredpath = "/img"
title = "Toy Factory Simulation Using core.async (4)"
date = 2019-12-10T20:49:02+02:00
+++

Let's take the `assembly-line` function and refactor it to make things a bit more clear, and also as a recap.


{{< highlight clojure >}}
(defn go-body [body-chan]
  (go
    (while true
      (let [body (loop []
                   (let [part (take-part)]
                     (if (body? part)
                       part
                       (recur))))]
        (prn "Got body")
        (>! body-chan body))))
  )

(defn go-wheel1 [wheel1-chan]
  (go
    (while true
      (let [wheel1 (loop []
                     (let [part (take-part)]
                       (if (wheel? part)
                         part
                         (recur))))]
        (prn "Got first wheel")
        (>! wheel1-chan wheel1)))))


(defn go-wheel2 [wheel2-chan]
  (go
    (while true
      (let [wheel2 (loop []
                     (let [part (take-part)]
                       (if (wheel? part)
                         part
                         (recur))))]
        (prn "Got second wheel")
        (>! wheel2-chan wheel2)))))

(defn go-attach-wheel1 [body-chan wheel1-chan body+wheel-chan]
  (go
    (while true
      (let [body (<! body-chan)
            wheel1 (<! wheel1-chan)
            bw (attach-wheel body wheel1)]
        (prn "Attached first wheel")
        (>! body+wheel-chan bw)))))

(defn go-attach-wheel2 [wheel2-chan body+wheel-chan body+2-wheels-chan]
  (go
    (while true
      (let [bw (<! body+wheel-chan)
            wheel2 (<! wheel2-chan)
            bww (attach-wheel bw wheel2)]
        (prn "Attached second wheel")
        (>! body+2-wheels-chan bww)))))

(defn go-box-up [body+2-wheels-chan box-chan]
  (go
    (while true
      (let [box (box-up (<! body+2-wheels-chan))]
        (prn "Boxed up car")
        (>! box-chan box)))))

(defn go-put-in-truck [box-chan]
  (go
    (time
     (dotimes [x 10]
       (time
        (put-in-truck (<! box-chan)))
       (prn "Done!")))))

(defn assembly-line []
  (let [body-chan (chan)
        wheel1-chan (chan)
        wheel2-chan (chan)
        body+wheel-chan (chan)
        body+2-wheels-chan (chan)
        box-chan (chan)]
    (go-body body-chan)
    (go-wheel1 wheel1-chan)
    (go-wheel2 wheel2-chan)
    (dotimes [x 2]
      (go-attach-wheel1 body-chan wheel1-chan body+wheel-chan))
    (dotimes [x 2]
      (go-attach-wheel2 wheel2-chan body+wheel-chan body+2-wheels-chan))
    (dotimes [x 2]
      (go-box-up body+2-wheels-chan box-chan))
    (go-put-in-truck box-chan)))

{{< /highlight >}}

Remember that I said channels are like conveyer belts? There is a small problem, by default, our conveyer belts have only one slot. That means when the worker puts one item onto the belt, he has to wait for the other worker to take it away before he can continue. Vice versa, when a worker trys to take things from the belt finds that the belt is empty, he has to wait until somebody put something onto the belt.

This is a big problem actually, the conveyer belts are not making any difference if they can only take one item.

The good news is that it is easy to create channels which can take multiple items by just passing a parameter like this: `(chan 5)`, this is called channels with buffers. This means when the channel has 5 items, putting to the channel will block; when the channel is empty, taking item from the channel will block.

Let's give a buffer to all the channels used in the `assembly-line` function.

{{< highlight clojure >}}
(defn assembly-line []
  (let [body-chan (chan 10)
        wheel1-chan (chan 10)
        wheel2-chan (chan 10)
        body+wheel-chan (chan 10)
        body+2-wheels-chan (chan 10)
        box-chan (chan 10)]
        
        ...
{{< /highlight >}}

Now run it.

{{< highlight clojure >}}
;; last box put in truck
"Elapsed time: 120483.107609 msecs"
"Got body"
"Got first wheel"
"Got body"
"Attached first wheel"
"Got first wheel"
"Got second wheel"
"Attached first wheel"
"Attached second wheel"
"Got body"
"Attached second wheel"
"Boxed up car"
"Got first wheel"
"Got second wheel"
"Attached first wheel"
"Boxed up car"
"Got second wheel"
"Got body"
"Attached second wheel"
"Got body"
"Boxed up car"
"Attached first wheel"
"Got first wheel"
"Attached first wheel"
"Attached second wheel"
"Attached second wheel"
"Boxed up car"
"Got second wheel"
"Boxed up car"
"Got body"
"Got first wheel"
...

{{< /highlight >}}

Something is not right. After the last box has been put in the truck, the workers are still working!

They are filling in the conveyer belts until they all get full!

In next episode we will fix this, stay tuned!
