---
title: "Android Custom Views 101 (Part IV)"
date: 2017-07-09T16:41:23+03:00
categories: ["Android Development"]
keywords:
  - Android
  - Custom View
description: Learn about Androd Custom View and ViewGroup
featured: "featured-android-custom-view.png"
featuredalt: "Android custom view"
featuredpath: "/img"
---

In this installment, we talk about how to implement custom view group.

## Custom View VS Custom ViewGroup

When we think about a view, it is usually very simple and self contained. **View represents, draws and handles interaction for a part of the screen.**

Whereas ViewGroup is more about a binding relationship between views. **ViewGroup is a specialized View that contains other Views. It has children.**

As discussed before, in order for custom view to work correctly, it has to implement two methods: `onDraw()` and `onMeasure()`, and there is no need to implement `onLayout()` cause there is no children to layout. But for custom ViewGroup, it is a bit different. It has to implement `onMeasure()` to measure each child, and it has to implement `onLayout()` to layout each child, but there is no need to implement `onDraw()`. If for some reason we do need to draw something in layout, we can set the `setWillNotDraw` to false.

There are basically two types of custom ViewGroup:

First, **hardcoded children**, which means that it contains particular views with particular ids. Normally we choose this way to gain performance, or it can also be used to modularize complex layouts.

Second, **dynamic children**, which children views  are added dynamically by user. This makes no assumptions of what views are added of what types. Just like for example **LinearLayout**.

There are two ways of doing custom view groups:
1. Extends an existing `ViewGroup`, like `LinearLayout` or `FrameLayout`.
2. Extends the base `ViewGroup` class.

Normally, the first way will do the trick. But in this post, we are talking about the second one.

Basically you have to implement both `onMeasure()` and `onLayout()`.

The details are kind of straightforward, take a look at the commented [source code](https://github.com/lvguowei/TimerView/tree/6eb4e71e7a32be67a249818a2ac61f399da110e1)
