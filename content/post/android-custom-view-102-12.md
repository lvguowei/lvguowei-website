+++
author = "Guowei Lv"
categories = ["Android Custom View 102"]
keywords = ["Android", "custom view"]
description = "Sliding Menu"
featured= "android-custom-view-102.png"
featuredpath= "/img"
title = "Android Custom View 102 (Part 12)"
date = 2021-02-04T21:28:51+02:00
+++

Let's build a sliding menu from scratch by hand.

First a glimpse of what the final result looks like.

{{< img-post "/img" "sliding_menu.gif" "Sliding Menu Demo" "center" >}}

# Step 1: Horizontal Scroll View

The secret is that this is just a customized `HorizontalScrollView`:

{{< highlight kotlin>}}
class SlidingMenu @JvmOverloads constructor(
        context: Context,
        attrs: AttributeSet? = null,
        defStyleAttr: Int = 0
) : HorizontalScrollView(context, attrs, defStyleAttr) { ... }
{{< /highlight >}}

The view can be divided into 2 parts: **menu view** and **content view**, and they are just put next to each other.
The magic is that the **content view** has always the same width as the screen. And the width of the **menu view**
is the width of the screen minus some predefined margin. But the width of the screen is unknown until runtime, so
we need to override the function `onFinishInflate()`.

{{< highlight kotlin>}}
/**
 * Recalculate the menu and content views' widths.
 * This has to be done in code at runtime because we want to use the screen size.
 */
override fun onFinishInflate() {
    super.onFinishInflate()
    val container = getChildAt(0) as ViewGroup

    val childCount = container.childCount
    if (childCount != 2) {
        throw RuntimeException("Must contain 2 children views")
    }

    menuView = container.getChildAt(0)
    val menuParams = menuView.layoutParams
    menuParams.width = menuWidth
    menuView.layoutParams = menuParams

    contentView = container.getChildAt(1)
    val contentParams = contentView.layoutParams
    contentParams.width = getScreenWidth(context)
    contentView.layoutParams = contentParams
}
{{< /highlight >}}

Also, the view needs to show the content view upon opening:

{{< highlight kotlin>}}
override fun onLayout(changed: Boolean, l: Int, t: Int, r: Int, b: Int) {
    super.onLayout(changed, l, t, r, b)
    // scroll to content view when start
    smoothScrollTo(menuWidth, 0)
}
{{< /highlight >}}


After this, now the view looks like:

{{< img-post "/img" "sliding_menu_2.gif" "Sliding Menu Demo" "center" >}}

Next step is to add some animations.

# Step 2: Animations

We need to add animations to both **content view** and **menu view**.

First let's add some animation to the **content view**.

What we want to achieve is to scale down the content view from 1 to 0.7 in size.
With this rough idea in mind let's try it out.

{{< highlight kotlin>}}

 override fun onScrollChanged(l: Int, t: Int, oldl: Int, oldt: Int) {
    super.onScrollChanged(l, t, oldl, oldt)
    
    val scrolledPercent = 1f * l / menuWidth
    val contentScale = 0.7f + 0.3f * scrolledPercent
    contentView.scaleX = contentScale
    contentView.scaleY = contentScale
}
{{< /highlight >}}

{{< img-post "/img" "sliding_menu_3.gif" "Sliding Menu Demo" "center" >}}

OK, this looks almost right, but not exactly. If you pay close attention,
the content view is almost completely gone out of sight. The reason why this
happens is that the scaling pivot point is the center of the view by default.
This also cause the left edge's position to move.

In order to fix this, we can set the pivot point of the scaling to be on the
middle of the view's left edge.

{{< highlight kotlin>}}
contentView.pivotX = 0f
contentView.pivotY = contentView.measuredHeight / 2.toFloat()
{{< /highlight >}}

Now looks much better.

{{< img-post "/img" "sliding_menu_4.gif" "Sliding Menu Demo" "center" >}}

We are done with animation for content view, let's add some similar animation for menu view:
change the scale and transparency.

{{< highlight kotlin>}}
val menuAlpha = 0.5f + (1 - scrolledPercent) * 0.5f
menuView.alpha = menuAlpha
val menuScale = 0.7f + (1 - scrolledPercent) * 0.3f
menuView.scaleX = menuScale
menuView.scaleY = menuScale
{{< /highlight >}}

With animations added to both views, now it looks already pretty good.

{{< img-post "/img" "sliding_menu_5.gif" "Sliding Menu Demo" "center" >}}


# Step 3: Handle touch event
Another requirement is that when user lift finger, if the menu is less then
half way shown, then close the menu, otherwise open the menu.

This can be easily done by overriding the `onTouchEvent()` function.

{{< highlight kotlin>}}
  override fun onTouchEvent(ev: MotionEvent): Boolean {
    if (ev.action == MotionEvent.ACTION_UP) {
        val currentX = scrollX

        if (currentX > menuWidth / 2) {
            closeMenu()
        } else {
            openMenu()
        }

        return true
    }
    return super.onTouchEvent(ev)
}

private fun openMenu() {
    smoothScrollTo(0, 0)
    isMenuOpen = true
}

private fun closeMenu() {
    smoothScrollTo(menuWidth, 0)
    isMenuOpen = false
}
{{< /highlight >}}

{{< img-post "/img" "sliding_menu_6.gif" "Sliding Menu Demo" "center" >}}

# Step 4: Handle fling event

Currently the menu may feel a bit too sticky and hard to open or close, because you have to always slide passed 50%.
It is better if we can also handle the fling event, so user can close or open the menu with a quick slide gesture.

We need the help of a new class `GestureDetector`.


{{< highlight kotlin>}}
private val gestureListener: GestureDetector.OnGestureListener =
    object : SimpleOnGestureListener() {
        override fun onFling(
                e1: MotionEvent,
                e2: MotionEvent,
                velocityX: Float,
                velocityY: Float
        ): Boolean {
            if (isMenuOpen) {
                if (velocityX < 0) {
                    closeMenu()
                    return true
                }
            } else {
                if (velocityX > 0) {
                    openMenu()
                    return true
                }
            }
            return super.onFling(e1, e2, velocityX, velocityY)
        }
    }
{{< /highlight >}}

The implementation is mostly self explanatory.

Now we can use this at the beginning of `onTouchEvent()`:

{{< highlight kotlin>}}
override fun onTouchEvent(ev: MotionEvent): Boolean {

    // Note that if we detected fling event, we can let gestureDetector
    // handle it and return true here
    if (gestureDetector.onTouchEvent(ev)) {
        return true
    }
{{< /highlight >}}

# Step 5: Intercept touch event when menu is open

The last missing piece is this: when the menu is open, and user clicks on the content view, the menu should be closed
and the content view should not respond to the touch event. (E.g. if user happens to clicked on some button on the upper
left corner, it should not trigger anything)

To do this, we need to override the `onInterceptTouchEvent()`:

{{< highlight kotlin>}}
override fun onInterceptTouchEvent(ev: MotionEvent): Boolean {
    isIntercept = false
    if (isMenuOpen) {
        val currentX = ev.x
        if (currentX > menuWidth) {
            closeMenu()

            // Note that by returning true, the child view's
            // touch event will be intercepted, but the parent
            // view's touch event is still executed.
            // So we need to mark that touch event is intercepted,
            // in order to bypass the touch event of the parent
            // view as well.
            isIntercept = true
            return true
        }
    }
    return super.onInterceptTouchEvent(ev)
}
{{< /highlight >}}

{{< highlight kotlin>}}
override fun onTouchEvent(ev: MotionEvent): Boolean {


    //If intercepted, skip the view's touch event
    if (isIntercept) {
        return true
    }
    ...
{{< /highlight >}}

This completes this sliding view.

The full source code can be found [here](https://github.com/lvguowei/SlidingMenu).
