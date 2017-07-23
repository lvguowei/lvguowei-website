+++
date = "2017-02-10T20:31:01+02:00"
title = "programming kata day 3"
tags = ["clojure", "programming kata"]
categories = ["Programming Kata"]
+++

# Problem

Trolls are attacking your comment section!

A common way to deal with this situation is to remove all of the vowels from the trolls' comments, neutralizing the threat.

Your task is to write a function that takes a string and return a new string with all vowels removed.

For example, the string "This website is for losers LOL!" would become "Ths wbst s fr lsrs LL!".

# Solution

## Solution 1

{{< highlight clojure >}}

(defn disemvowel
  [string]
  (reduce (fn [result next]
            (if (#{\A \E \I \O \U \a \e \i \o \u} next)
              result
              (str result next)))
          ""
          string))

{{< /highlight >}}

## Solution 2

{{< highlight clojure >}}

(defn disemvowel
  [string]
  (apply str (remove (set "AEIOUaeiou") string)))

{{< /highlight >}}
