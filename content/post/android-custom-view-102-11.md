+++
author = "Guowei Lv"
categories = ["Android Custom View 102"]
keywords = ["Android", "custom view"]
description = "Touch event dispatching"
featured= "android-custom-view-102.png"
featuredpath= "/img"
title = "Android Custom View 102 (Part 11)"
date = 2020-10-05T21:40:37+03:00
+++

It is interesting how Android view's touch events are dispatched.
Let's explore it.

Firstly, write a custom view and override its `onTouchEvent()` method.

{{< highlight java>}}
class TouchView @JvmOverloads constructor(
  context: Context, attrs: AttributeSet? = null, defStyleAttr: Int = 0
) : View(context, attrs, defStyleAttr) {
  
  override fun onTouchEvent(event: MotionEvent): Boolean {
    Log.d("TAG", "TouchView - onTouchEvent - ${event.action}")
    return super.onTouchEvent(event)
  }
}
{{< /highlight >}}

Then, in `MainActivity`, on the view, call `setOnTouchListener()` and `setOnClickListener()`.

{{< highlight java>}}

class MainActivity : AppCompatActivity() {

  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)

    touchView.setOnTouchListener { v, event ->
      Log.d("TAG", "MainActivity - setOnTouchListener - ${event.action}")
      false
    }

    touchView.setOnClickListener {
      Log.d("TAG", "MainActivity - setOnClickListener - onClick")
    }
  }
}
{{< /highlight >}}

Note that the inside `setOnTouchListener`, the listener returns `false`. (we will look into the case when it returns true in a monent)

I challenge you to guess the order in which the logs will be printed. Well, it is not very obvious in what order these listeners are triggered, so let's run it.

{{< highlight java>}}
MainActivity - setOnTouchListener - 0 (ACTION_DOWN)
TouchView    - onTouchEvent - 0
MainActivity - setOnTouchListener - 1 (ACTION_UP)
TouchView    - onTouchEvent - 1
MainActivity - setOnClickListener - onClick
{{< /highlight >}}

This kinda makes sense.

Let's try to let the set `OnTouchListener` to return `true`.

{{< highlight java>}}
MainActivity - setOnTouchListener - 0
MainActivity - setOnTouchListener - 1
{{< /highlight >}}

OK, now the `onTouchEvent` from inside the view and the `onClick` listener are both not called!

Let's now read a bit of source code to try to understand why this happens.

Inside `View`, there are 2 important methods that are related to the touch event: `dispatchTouchEvent()` and `onTouchEvent()`



{{< highlight java>}}
/**
     * Pass the touch screen motion event down to the target view, or this
     * view if it is the target.
     *
     * @param event The motion event to be dispatched.
     * @return True if the event was handled by the view, false otherwise.
     */
    public boolean dispatchTouchEvent(MotionEvent event) {

        ...
        
        if (onFilterTouchEventForSecurity(event)) {
            if ((mViewFlags & ENABLED_MASK) == ENABLED && handleScrollBarDragging(event)) {
                result = true;
            }
            //noinspection SimplifiableIfStatement
            ListenerInfo li = mListenerInfo;
            if (li != null && li.mOnTouchListener != null
                    && (mViewFlags & ENABLED_MASK) == ENABLED
                    && li.mOnTouchListener.onTouch(this, event)) {
                result = true;
            }

            if (!result && onTouchEvent(event)) {
                result = true;
            }
        }

        ...
        
        return result;
    }
{{< /highlight >}}

This piece of code explained why when `OnTouchListener` returns true, the `onTouchEvent()` of the view will not be called.
But we still don't know why the `OnClickListener` is also not called.

Let's take a look at `View`'s `onTouchEvent()` function.

{{< highlight java>}}
    public boolean onTouchEvent(MotionEvent event) {
    
        ...
            switch (action) {
                case MotionEvent.ACTION_UP:
                    mPrivateFlags3 &= ~PFLAG3_FINGER_DOWN;
                    if ((viewFlags & TOOLTIP) == TOOLTIP) {
                        handleTooltipUp();
                    }
                    if (!clickable) {
                        removeTapCallback();
                        removeLongPressCallback();
                        mInContextButtonPress = false;
                        mHasPerformedLongPress = false;
                        mIgnoreNextUpEvent = false;
                        break;
                    }
                    boolean prepressed = (mPrivateFlags & PFLAG_PREPRESSED) != 0;
                    if ((mPrivateFlags & PFLAG_PRESSED) != 0 || prepressed) {
                        // take focus if we don't have it already and we should in
                        // touch mode.
                        boolean focusTaken = false;
                        if (isFocusable() && isFocusableInTouchMode() && !isFocused()) {
                            focusTaken = requestFocus();
                        }

                        if (prepressed) {
                            // The button is being released before we actually
                            // showed it as pressed.  Make it show the pressed
                            // state now (before scheduling the click) to ensure
                            // the user sees it.
                            setPressed(true, x, y);
                        }

                        if (!mHasPerformedLongPress && !mIgnoreNextUpEvent) {
                            // This is a tap, so remove the longpress check
                            removeLongPressCallback();

                            // Only perform take click actions if we were in the pressed state
                            if (!focusTaken) {
                                // Use a Runnable and post this rather than calling
                                // performClick directly. This lets other visual state
                                // of the view update before click actions start.
                                if (mPerformClick == null) {
                                    mPerformClick = new PerformClick();
                                }
                                if (!post(mPerformClick)) {
                                    performClickInternal();
                                }
                            }
                        }

    ...
{{< /highlight >}}

Aha! See the `performClickInternal()` at the bottom of the code block. 
So of course, if `onTouchEvent()` of view is skipped, the click event will also be missing.
