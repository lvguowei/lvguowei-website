+++
author = "Guowei Lv"
categories = ["Clojure", "core.async"]
description = "A core.async example"
featured = "car-factory.jpg"
featuredpath = "/img"
title = "Toy Factory Simulation Using core.async (1)"
date = 2019-12-01T22:48:10+02:00
+++

After learning the `core.async` library in Clojure, I was craving for a real-world example. Now I found one from [purelyfunctional.tv](https://purelyfunctional.tv/courses/clojure-core-async/).

The original code can be found [here](https://github.com/ericnormand/lispcast-clojure-core-async).


# The Toy Car Factory

Let's describe how a worker builds a toy car.

1. Take a car body from the part box.
2. Take a wheel from the part box.
3. Take another wheel from the part box.
4. Attach the first wheel to the body.
5. Attach the second wheel to the body.
6. Box the car.
7. Put it in truck.

Now let's see some code that implement these steps.

`take-part` will take a *wheel* or *body* randomly from the part box.
{{< highlight clojure >}}
(defn try-to-take [state]
  (case state
    :free :ok
    :ok :already-taken
    :already-taken :already-taken))

(def part-box (atom :free))

(defn take-part []
  "Take a wheel or body randomly. This is blocking."
  (Thread/sleep 200)
  (let [status (swap! part-box try-to-take)]
    (if (not= :ok status)
      (do
        (Thread/sleep 100)
        (recur))
      (let [r (rand-nth [:wheel :wheel (atom {:body []
                                              :status :free})])]
        (Thread/sleep 800)
        (reset! part-box :free)
        r))))
{{< /highlight >}}

The clever part of the code is the use of `(swap! part-box try-to-take)`.

Let's say *worker-1* on *process-1* is the first worker to enter this function. After the `swap!`, the `part-box` becomes `:ok`. And when *worker-1* is at `(Thread/sleep 800)`, there is another *worker-2* on *process-2* also enters the function. But when *worker-2* executes `(swap! part-box try-to-take)`, it will return `:already-taken` and be forced to retry. Only when *worker-1* has done his work and `(reset! part-box :free)` will *worker-2* have a chance to get in. As you can see, this actually is a kind of locking mechanism, preventing multiple workers from taking the part simultaneously. This is quite reasonable, cause there is only one box.

`attach-wheel` attaches a wheel to a car body.

{{< highlight clojure >}}
(defn attach-wheel [body wheel]
  (let [{status :status} (swap! body update-in [:status] try-to-take)]
    (if (not= :ok status)
      (do
        (Thread/sleep 100)
        (recur body wheel))
      (do
        (Thread/sleep 4000)
        (swap! body #(-> %
                         (assoc :status :free)
                         (update-in [:body] conj wheel)))
        body))))
{{< /highlight >}}

A few things about this function:

1. Attaching wheel to the body takes 4 secs. 
2. There cannot be two workers attaching wheels to the same body at the same time.
3. Multiple workers can attaching wheels to different bodies on different processes at the same time.

`box-up` puts the car into box.

{{< highlight clojure >}}
(defn box-up [body]
  (Thread/sleep 3000)
  :box)
{{< /highlight >}}



Boxing up a car takes 3 secs, and multiple workers can do it at the same time on different threads.

{{< highlight clojure >}}
(def truck (atom :free))

(defn put-in-truck [box]
  (let [status (swap! truck try-to-take)]
    (if (not= :ok status)
      (do
        (Thread/sleep 100)
        (recur box))
      (do
        (Thread/sleep 2000)
        (reset! truck :free)
        :done))))
{{< /highlight >}}

Puting a box in the car takes 2 seconds, and only one worker can do it at a time, because there is only one truck of course.


Now we have all the pieces, lets analyse the whole process if there is one worker doing it all.

1. Take a car body from the part box.    **1 sec**
2. Take a wheel from the part box.       **1 sec**
3. Take another wheel from the part box. **1 sec**
4. Attach the first wheel to the body.   **4 sec**
5. Attach the second wheel to the body.  **4 sec**
6. Box the car.                          **3 sec**
7. Put it in truck.                      **2 sec**

TOTAL                                    **16 sec** 

Let's write some code to verify it.

{{< highlight clojure >}}

(defn build-car [n]
  (prn n "Starting build")
  (let [body (loop []
               (let [part (take-part)]
                 (if (body? part)
                   part
                   (recur))))
        _ (prn n "Got body")
        wheel1 (loop []
                 (let [part (take-part)]
                   (if (wheel? part)
                     part
                     (recur))))
        _ (prn n "Got wheel1")
        wheel2 (loop []
                 (let [part (take-part)]
                   (if (wheel? part)
                     part
                     (recur))))
        _ (prn n "Got wheel2")
        bw (attach-wheel body wheel1)
        _ (prn n "Attach wheel1")
        bww (attach-wheel bw wheel2)
        _ (prn n "Attach wheel2")
        box (box-up bww)
        _ (prn n "Box up")]
    (put-in-truck box)
    (prn "Done!")))

{{< /highlight >}}

Notice since the parts taken from the box is random, so there may be times when we want a wheel but get a body. So the total time can be more than 16 secs.

The result is indeed what think:

{{< highlight clojure >}}

lispcast-clojure-core-async.core> (time (build-car 1))
1 "Starting build"
1 "Got body"
1 "Got wheel1"
1 "Got wheel2"
1 "Attach wheel1"
1 "Attach wheel2"
1 "Box up"
"Done!"
"Elapsed time: 18008.065267 msecs"
nil
{{< /highlight >}}
