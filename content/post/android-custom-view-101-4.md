---
title: "Android Custom Views 101 (Part IV)"
date: 2017-07-09T16:41:23+03:00
tags: ["Android"]
categories: ["programming"]
---

In this installment, we talk about how to implement custom view group.

There are two ways of doing custom view groups:
1. Extends an existing `ViewGroup`, like `LinearLayout` or `FrameLayout`.
2. Extends the base `ViewGroup` class.

Normally, the first way will do the trick. But in this post, we are talking about the second one.

Basically you have to implement both `onMeasure()` and `onLayout()`.

The details are kind of straightforward, take a look at the commented [source code](https://github.com/lvguowei/TimerView/tree/6eb4e71e7a32be67a249818a2ac61f399da110e1)
