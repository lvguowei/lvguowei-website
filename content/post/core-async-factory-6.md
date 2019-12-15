+++
author = "Guowei"
categories = ["Clojure"]
description = "Reducing Waste"
featured = "car-factory.jpg"
featuredpath = "/img"
title = "Toy Factory Simulation Using core.async (6)"
date = 2019-12-15T21:11:41+02:00
+++

Let's take another look at the assembly line diagram now.

{{< figure src="/img/assembly-line-1.jpg" >}}

If you pay close attention at each of the workers and observe how they work, you will probably find the 3 workers at the beginning of the assembly line to be a bit ... not that smart. All of them only one type of part, either wheels or bodies. If the body worker takes a wheel, he will just abandon it and keep taking new parts until he gets a body. Firstly, they are all blocked by the parts box, meaning they are competing with each other. Secondly, whatever they take is actually useful no matter if it is a body or wheel.

Let's redesign the assembly line to eliminate the waste of time and resource.

{{< figure src="/img/assembly-line-2.jpg" >}}

In the new design, we put one worker to take parts from the box, and put them on different channels according to the type of the parts.

Here is the code change:

{{< highlight clojure >}}

(defn go-part [body-chan wheel-chan]
  (go
    (loop []
      (let [part (take-part)]
        (if (body? part)
          (when (>! body-chan part)
            (recur))
          (when (>! wheel-chan part)
            (recur)))))))

(defn go-attach-wheel1 [body-chan wheel-chan body+wheel-chan]
  (go
    (loop []
      (let [body (<! body-chan)
            wheel1 (<! wheel-chan)
            bw (attach-wheel body wheel1)]
        (prn "Attached first wheel")
        (when (>! body+wheel-chan bw)
          (recur))))
    (async/close! body-chan)
    (async/close! wheel-chan)))

(defn go-attach-wheel2 [wheel-chan body+wheel-chan body+2-wheels-chan]
  (go
    (loop []
      (let [bw (<! body+wheel-chan)
            wheel2 (<! wheel-chan)
            bww (attach-wheel bw wheel2)]
        (prn "Attached second wheel")
        (when (>! body+2-wheels-chan bww)
          (recur))))
    (async/close! body+wheel-chan)
    (async/close! wheel-chan)))

{{< /highlight >}}

Now this runs a lot faster, around 44 seconds !

{{< highlight clojure >}}

"Elapsed time: 6001.514662 msecs"
"Done!"
"Attached second wheel"
"Boxed up car"
"Attached second wheel"
"Attached second wheel"
"Boxed up car"
"Elapsed time: 3001.100148 msecs"
"Done!"
"Boxed up car"
"Elapsed time: 2000.207164 msecs"
"Done!"
"Attached first wheel"
"Boxed up car"
"Attached second wheel"
""AtEtlaacphseedd  ftiirmset:  wh2e0e0l"0.20761
 msecs"
"Done!"
"Elapsed time: 44024.866911 msecs"
"Boxed up car"
"Attached first wheel"
"Attached second wheel"
"Attached second wheel"
"Boxed up car"
"Attached second wheel"
{{< /highlight >}}
