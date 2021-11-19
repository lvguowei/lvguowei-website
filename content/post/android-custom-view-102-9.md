+++
author = "Guowei Lv"
categories = ["Android Custom View 102"]
keywords = ["Android", "custom view"]
description = "A RatingBar Example"
featured= "android-custom-view-102.png"
featuredpath= "/img"
title = "Android Custom View 102 (Part 9)"
date = 2020-06-02T23:27:07+03:00
+++

{{< figure src="/img/ratingbar.gif" >}}

There is nothing new here, just another exercise.

Two things to be careful with:

1. Try to call `validate()` as fewer times as possible, it is expensive.
2. If you want to handle the touch events in this manner, remember to return `true` from `onTouchEvent()`. Otherwise, only the first event will be triggered(most likely DOWN in this case) and not the succeeding ones (like the MOVES).

[Source Code](https://github.com/lvguowei/RatingBar)
