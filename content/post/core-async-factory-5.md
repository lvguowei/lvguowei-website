+++
author = "Guowei"
categories = ["Clojure"]
description = "Closing Shop (Stopping Processes)"
featured = "car-factory.jpg"
featuredpath = "/img"
title = "Toy Factory Simulation Using core.async (5)"
date = 2019-12-11T20:55:42+02:00
+++

We need a way to stop the whole assembly line after 10 toy cars have been put in the truck.

But before that, we need to understand more about `chan`.

1. Channels are created open
2. Close a channel with `close!`
3. You can call `close!` to close a channel.
4. Once a channel is closed, it can never be reopened.
5. Put (`>!`) on a closed channel returns `false`.
6. Get (`<!`) from a closed channel returns `nil`.


OK, with these in mind, we can implement a way to close the assembly line.

Among all the workers, only the last worker who is putting cars on the truck really knows when to stop.
He can simply close the channel from which he gets the car when he finishes putting 10 cars on the truck.

{{< highlight clojure >}}
(defn go-put-in-truck [box-chan]
  (go
    (time
     (dotimes [x 10]
       (time
        (put-in-truck (<! box-chan)))
       (prn "Done!")))
    (async/close! box-chan)))
{{< /highlight >}}

Some workers take something from a channel and put something on another channel. For these workers, they can close the input channel when getting a `false` with the outp channel.

{{< highlight clojure >}}
(defn go-body [body-chan]
  (go
    (loop []
      (let [body (loop []
                   (let [part (take-part)]
                     (if (body? part)
                       part
                       (recur))))]
        (prn "Got body")
        (when (>! body-chan body)
          (recur)))))
  )

(defn go-wheel1 [wheel1-chan]
  (go
    (loop []
      (let [wheel1 (loop []
                     (let [part (take-part)]
                       (if (wheel? part)
                         part
                         (recur))))]
        (prn "Got first wheel")
        (when (>! wheel1-chan wheel1)
          (recur))))))


(defn go-wheel2 [wheel2-chan]
  (go
    (loop []
      (let [wheel2 (loop []
                     (let [part (take-part)]
                       (if (wheel? part)
                         part
                         (recur))))]
        (prn "Got second wheel")
        (when (>! wheel2-chan wheel2)
          (recur))))))

(defn go-attach-wheel1 [body-chan wheel1-chan body+wheel-chan]
  (go
    (loop []
      (let [body (<! body-chan)
            wheel1 (<! wheel1-chan)
            bw (attach-wheel body wheel1)]
        (prn "Attached first wheel")
        (when (>! body+wheel-chan bw)
          (recur))))
    (async/close! body-chan)
    (async/close! wheel1-chan)))

(defn go-attach-wheel2 [wheel2-chan body+wheel-chan body+2-wheels-chan]
  (go
    (loop []
      (let [bw (<! body+wheel-chan)
            wheel2 (<! wheel2-chan)
            bww (attach-wheel bw wheel2)]
        (prn "Attached second wheel")
        (when (>! body+2-wheels-chan bww)
          (recur))))
    (async/close! body+wheel-chan)
    (async/close! wheel2-chan)))

(defn go-box-up [body+2-wheels-chan box-chan]
  (go
    (loop []
      (let [box (box-up (<! body+2-wheels-chan))]
        (prn "Boxed up car")
        (when (>! box-chan box)
          (recur))))
    (async/close! body+2-wheels-chan)))
{{< /highlight >}}

Now run `assembly-line` again, we can see there are still some extra work, but it did stop at some point.

{{< highlight clojure >}}

"Elapsed time: 98173.874228 msecs"  ;; last car done
"Got body"
"Got first wheel"
"Boxed up car"
"Got second wheel"
"Got body"
"Got first wheel"
"Boxed up car"
"Attached first wheel"
"Got second wheel"
"Got first wheel"
"Attached first wheel"
"Attached second wheel"
"Got second wheel"
"Attached second wheel"
"Got first wheel"
"Got body"
"Got first wheel"
"Got body"
"Got first wheel"
"Got body"
"Attached first wheel"
"Attached first wheel"
"Got body"
"Got first wheel"

{{< /highlight >}}

