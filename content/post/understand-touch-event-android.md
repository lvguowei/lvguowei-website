+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "How touch events are handled"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Understand Android View's Touch Events"
date = 2022-01-24T20:52:47+02:00
+++

How the view system in Android handles touch events? Let's try to understand it by designing it from scratch ourselves!

(This is not my original but a summary of this https://juejin.cn/post/6844903761052188679)

Let's do this by coming up a series of requirements (from naive to sophisticated) and see how we can design the logic to fulfill them.

# Requirement 1
> In a nested view hierachy, only the most inner view can handle events.

{{< figure src="/img/touch1.png" >}}

This is super simple to implement, we just pass the events all the way to the most inner view and let it handle them.

{{< highlight kotlin >}}

open class MView {
    open fun passEvent(ev: MotionEvent) {
        // do sth
    }
}
    
class MViewGroup(private val child: MView) : MView() {
    override fun passEvent(ev: MotionEvent) {
        child.passEvent(ev)
    }
}
{{< /highlight >}}

# Requirement 2
> 1. In a nested view hierachy, there are multiple view/viewGroups can handle touch events.
> 2. One user operation (can be a series of touch event) can only be handled by one view.

{{< figure src="/img/touch2.png" >}}

Since we decided that touch events are passed from parent to child, then we need a way to decide which view should consume the event and not passing it down.
From user's perspective, the **child view should be positioned "on top of" the parent view**, so it is more natural to let the **child view decide first**.

This means it is not enough to only pass event one way from parent to child, but we need to pass the event **from outside to inside(just passing, no handling), and then from inside to outside(to handling event)**.

{{< figure src="/img/touch3.png" >}}

{{< highlight kotlin >}}
open class MView {
    open fun dispatch(ev: MotionEvent): Boolean {
        return onTouch(ev)
    }

    open fun onTouch(ev: MotionEvent): Boolean {
        return false
    }
}

class MViewGroup(private val child: MView) : MView() {
    override fun dispatch(ev: MotionEvent): Boolean {
        var handled = child.dispatch(ev)
        if (!handled) handled = onTouch(ev)

        return handled
    }

    override fun onTouch(ev: MotionEvent): Boolean {
        return false
    }
}
{{< /highlight >}}

This looks cool, let's add different types of events into play now.

But first, we need to make clear what is a **user operation** and what is a **touch event**.

A user operation can be the following: `click`, `long click`, `swipe` etc.

A touch event can be the following: `DOWN`, `MOVE` and `UP`.

As we can see, user operation often contains a stream of touch event, starts from `DOWN` and ends with `UP`.

So we can just **remember which view consumes `DOWN`, and send the rest of the events in the stream to it**.


{{< highlight kotlin >}}
open class MView {
    open fun dispatch(ev: MotionEvent): Boolean {
        return onTouch(ev)
    }

    open fun onTouch(ev: MotionEvent): Boolean {
        return false
    }
}

class MViewGroup(private val child: MView) : MView() {
    private var isChildNeedEvent = false

    override fun dispatch(ev: MotionEvent): Boolean {
        var handled = false
        
        if (ev.actionMasked == MotionEvent.ACTION_DOWN) {
            clearStatus()
        
            handled = child.dispatch(ev)
            if (handled) isChildNeedEvent = true

            if (!handled) handled = onTouch(ev)
        } else {
            if (isChildNeedEvent) handled = child.dispatch(ev)
            if (!handled) handled = onTouch(ev)
        }
        
        if (ev.actionMasked == MotionEvent.ACTION_UP) {
            clearStatus()
        }
            
        return handled
    }
    
    private fun clearStatus() {
        isChildNeedEvent = false
    }

    override fun onTouch(ev: MotionEvent): Boolean {
        return false
    }
}
{{< /highlight >}}


# Requirement 3
> Let's say there is a clickable view inside a scrollable view group. The user should be able to scroll the scrollable view even though the user touches on the clickable view. 

{{< figure src="/img/touch4.png" >}}

According to current logic, this is what would happen:
1. Scrollable view will pass the events to the inner clickable view.
2. Clickable view sees the it can handle the coming events, so it starts to handle them.
3. Scrollable view will never get a chance to scroll.

One example is clickable item views inside RecyclerView.
1. User touches down on the item view.
2. If user then start scrolling, the RecyclerView should starts scrolling and at the same time the highlight of the touched item view should disappear.

In order to achieve the above requirement, we need to do:

1. For any view, if it can handle touch events, then return `true` for `DOWN` event in `onTouch()`. This will ensure that the events will flow to the most inner view and give it a chance to handle it first.
2. If child view does not handle event, then parent just handles it.
3. But if child view handles event, then parent view should observe and try to detect if the touch pattern matches itself's handling logic. If so, hijack(intercept) the events, starts to pass them to itself's `onTouch()`. Then send a `CANCEL` event to child view, indicating that the events are hijacked by parent.

{{< figure src="/img/touch5.png" >}}

In terms of the RecyclerView example, this is what happened:
1. User press down on the item view. Events are first passed to item view to handle. And since item views are clickable, it starts to handle the `DOWN` event, by highlighting the view probably.
2. User starts to scroll up or down without lifting his finger. Parent RecyclerView sees that the user starts scrolling and it can handle scrolling, RecyclerView intercept the `MOVE` touch events, so the RecyclerView starts scrolling.
3. At the same time, RecyclerView sends a `CANCEL` event to child item view, the item view clears the highlight state.

{{< highlight kotlin >}}
open class MView {
    open fun dispatch(ev: MotionEvent): Boolean {
        return onTouch(ev)
    }

    open fun onTouch(ev: MotionEvent): Boolean {
        return false
    }
}

class MViewGroup(private val child: MView) : MView() {
    private var isChildNeedEvent = false
    private var isSelfNeedEvent = false

    override fun dispatch(ev: MotionEvent): Boolean {
        var handled = false

        if (ev.actionMasked == MotionEvent.ACTION_DOWN) {
            clearStatus()
            
            if (onIntercept(ev)) {
                isSelfNeedEvent = true
                handled = onTouch(ev)
            } else {
                handled = child.dispatch(ev)
                if (handled) isChildNeedEvent = true

                if (!handled) {
                    handled = onTouch(ev)
                    if (handled) isSelfNeedEvent = true
                }
            }
        } else {
            if (isSelfNeedEvent) {
                handled = onTouch(ev)
            } else if (isChildNeedEvent) {
                if (onIntercept(ev)) {
                    isSelfNeedEvent = true
                    handled = onTouch(ev)
                } else {
                    handled = child.dispatch(ev)
                }
            }
        }

        if (ev.actionMasked == MotionEvent.ACTION_UP) {
            clearStatus()
        }
        
        return handled
    }

    private fun clearStatus() {
        isChildNeedEvent = false
        isSelfNeedEvent = false
    }

    override fun onTouch(ev: MotionEvent): Boolean {
        return false
    }

    open fun onIntercept(ev: MotionEvent): Boolean {
        return false
    }
}
{{< /highlight >}}

But there is yet another case ...

# Requirement 4

> Imagine there is horizontal scrollable view inside a vertical scrollable parent view. When the user scrolls the horizontal scrollable view, the finger is not strictly moving left or right, but starts to move also vertically up or down. Handle such case that there is only one view scrolling for the entire scrolling operation.

Let's first see what will happen with our current logic:
1. User touches down on the child horizontal scrollable view and finger starts to move right. The horizontal scrollable view handles touch events and starts to scroll.
2. User's finger movement starts to tilt upwards. Parent vertical scrollable view detects this, intercept the touch events and starts scrolling up, at the same time child view stops scrolling.

As we can see, this is very unnatural and not really desired.

Even if the user's finger's moving direction changes, since the horizontal scrollable view already starts scrolling, we don't want the parent to intercept the touch events. So basically we need to give a chance for the child view to say that what I'm doing is important, please parent leave me alone and don't interrupt. This is easy to do, just add method `requestDisallowInterceptTouchEvent()`.

{{< highlight kotlin >}}
interface ViewParent {
    fun requestDisallowInterceptTouchEvent(isDisallowIntercept: Boolean)
}

open class MView {
    var parent: ViewParent? = null

    open fun dispatch(ev: MotionEvent): Boolean {
        return onTouch(ev)
    }

    open fun onTouch(ev: MotionEvent): Boolean {
        return false
    }
}

open class MViewGroup(private val child: MView) : MView(), ViewParent {
    private var isChildNeedEvent = false
    private var isSelfNeedEvent = false
    private var isDisallowIntercept = false

    init {
        child.parent = this
    }

    override fun dispatch(ev: MotionEvent): Boolean {
        var handled = false
        
        if (ev.actionMasked == MotionEvent.ACTION_DOWN) {
            clearStatus()
            
            // add isDisallowIntercept
            if (!isDisallowIntercept && onIntercept(ev)) {
                isSelfNeedEvent = true
                handled = onTouch(ev)
            } else {
                handled = child.dispatch(ev)
                if (handled) isChildNeedEvent = true

                if (!handled) {
                    handled = onTouch(ev)
                    if (handled) isSelfNeedEvent = true
                }
            }
        } else {
            if (isSelfNeedEvent) {
                handled = onTouch(ev)
            } else if (isChildNeedEvent) {
                // add isDisallowIntercept
                if (!isDisallowIntercept && onIntercept(ev)) {
                    isSelfNeedEvent = true

                    // add cancel
                    val cancel = MotionEvent.obtain(ev)
                    cancel.action = MotionEvent.ACTION_CANCEL
                    handled = child.dispatch(cancel)
                    cancel.recycle()
                } else {
                    handled = child.dispatch(ev)
                }
            }
        }
        
        if (ev.actionMasked == MotionEvent.ACTION_UP 
            || ev.actionMasked == MotionEvent.ACTION_CANCEL) {
            clearStatus()
        }
        
        return handled
    }
    
    private fun clearStatus() {
        isChildNeedEvent = false
        isSelfNeedEvent = false
        isDisallowIntercept = false
    }

    override fun onTouch(ev: MotionEvent): Boolean {
        return false
    }

    open fun onIntercept(ev: MotionEvent): Boolean {
        return false
    }

    override fun requestDisallowInterceptTouchEvent(isDisallowIntercept: Boolean) {
        this.isDisallowIntercept = isDisallowIntercept
        parent?.requestDisallowInterceptTouchEvent(isDisallowIntercept)
    }
}

{{< /highlight >}}

We are done now. It is important to understand the different cases and how our solution evolves to handle them. The code is just there as reference.
