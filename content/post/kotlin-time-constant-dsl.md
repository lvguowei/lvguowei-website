+++
categories = ["Kotlin"]
keywords = ["Kotlin", "run", "let", "also", "apply"]
description = "A little time DSL in Kotlin"
featured = "kotlin.png"
featuredpath = "/img"
title = "Kotlin Time Dsl"
date = 2021-04-25T15:04:14+03:00
+++

Often in project we want to declare some time constants, like

{{< highlight kotlin >}}
companion object {
    const val INTERVAL = 5 * 60 // 5 minutes 
  }
{{< /highlight >}}

This is fine, but can we do better in Kotlin? What I want is this `5 minutes in seconds` or `8 days in hours`, and it is not hard to achieve it actually.

{{< highlight kotlin >}}

infix fun Number.daysIn(unit: TimeUnit): Long = daysFn(this, unit)

infix fun Number.hoursIn(unit: TimeUnit) = hoursFn(this, unit)

infix fun Number.minutesIn(unit: TimeUnit) = minutesFn(this, unit)

infix fun Number.secondsIn(unit: TimeUnit) = secondsFn(this, unit)

infix fun Number.millisecondsIn(unit: TimeUnit) = millisecondsFn(this, unit)

internal val daysFn = { n: Number, unit: TimeUnit ->
  when (unit) {
    DAYS -> n.toLong()
    HOURS -> DAYS.toHours(n.toLong())
    MINUTES -> DAYS.toMinutes(n.toLong())
    SECONDS -> DAYS.toSeconds(n.toLong())
    MILLISECONDS -> DAYS.toMillis(n.toLong())
    MICROSECONDS -> DAYS.toMicros(n.toLong())
    NANOSECONDS -> DAYS.toNanos(n.toLong())
  }
}.memoize()

internal val hoursFn = { n: Number, unit: TimeUnit ->
  when (unit) {
    DAYS -> HOURS.toDays(n.toLong())
    HOURS -> n.toLong()
    MINUTES -> HOURS.toMinutes(n.toLong())
    SECONDS -> HOURS.toSeconds(n.toLong())
    MILLISECONDS -> HOURS.toMillis(n.toLong())
    MICROSECONDS -> HOURS.toMicros(n.toLong())
    NANOSECONDS -> HOURS.toNanos(n.toLong())
  }
}.memoize()


internal val minutesFn = { n: Number, unit: TimeUnit ->
  when (unit) {
    DAYS -> MINUTES.toDays(n.toLong())
    HOURS -> MINUTES.toHours(n.toLong())
    MINUTES -> n.toLong()
    SECONDS -> MINUTES.toSeconds(n.toLong())
    MICROSECONDS -> MINUTES.toMicros(n.toLong())
    MILLISECONDS -> MINUTES.toMillis(n.toLong())
    NANOSECONDS -> MINUTES.toNanos(n.toLong())
  }
}.memoize()

internal val secondsFn = { n: Number, unit: TimeUnit ->
  when (unit) {
    DAYS -> SECONDS.toDays(n.toLong())
    HOURS -> SECONDS.toHours(n.toLong())
    MINUTES -> SECONDS.toMinutes(n.toLong())
    SECONDS -> n.toLong()
    MILLISECONDS -> SECONDS.toMillis(n.toLong())
    MICROSECONDS -> SECONDS.toMicros(n.toLong())
    NANOSECONDS -> SECONDS.toNanos(n.toLong())
  }
}.memoize()

internal val millisecondsFn = { n: Number, unit: TimeUnit ->
  when (unit) {
    DAYS -> MILLISECONDS.toDays(n.toLong())
    HOURS -> MILLISECONDS.toHours(n.toLong())
    MINUTES -> MILLISECONDS.toMinutes(n.toLong())
    SECONDS -> MILLISECONDS.toSeconds(n.toLong())
    MILLISECONDS -> n.toLong()
    MICROSECONDS -> MILLISECONDS.toMicros(n.toLong())
    NANOSECONDS -> MILLISECONDS.toNanos(n.toLong())
  }
}.memoize()

{{< /highlight >}}

Now we can easily write time constants (almost) like this:

`24 hoursIn MILLISECONDS` or `5 daysIn HOURS`.
