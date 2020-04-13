+++
author = "Guowei Lv"
categories = ["Android Development"]
keywords = ["Android", "custom view"]
description = "Property Animation"
featured= "android-custom-view-102.png"
featuredpath= "/img"
title = "Android Custom View 102 (Part 5)"
date = 2020-04-12T14:02:16+03:00
+++

In this article we talk about animation, specifically property animation.

## ViewPropertyAnimator

View has many properties, like position on screen, color and size. Property animation is the type of animation that animate on those properties.

In other words, the animator's job is to set a specific set of properties of the view to different values many many times in a short period of time.

For example, the following code will move the view's position 500px to the right:

{{< highlight java>}}
view.animate().translationX(500);
{{< /highlight >}}


Now let's list all the setter methods of the view's properties and its corresponding method in the `ViewPropertyAnimator`.

{{< highlight java>}}
// TRANSLATION_X
view.setTranslationX()
view.animate().translationX()

// TRANSLATION_Y
view.setTranslationY()
view.animate().translationY()

// TRANSLATION_Z
view.setTranslationZ()
view.animate().translationZ()

// X
view.setX()
view.animate().x()

// Y
view.setY()
view.animate().y()

// Z
view.setZ()
view.animate().z()

// ROTATION
view.setRotation()
view.animate().rotation()

// ROTATION_X
view.setRotationX()
view.animate().rotationX()

// SCALE_X
view.setScaleX()
view.animate().scaleX()

// SCALE_Y
view.setScaleY()
view.animate().scaleY()

// ALPHA
view.setAlpha()
view.animate().alpha()
{{< /highlight >}}

One thing to note is that these animations can be run in parallel:

{{< highlight java>}}
imageView.animate()
  .translationX(Utils.dpToPixel(200))
  .rotation(360)
  .scaleX(1)
  .scaleY(1)
  .alpha(1);
{{< /highlight >}}

These animation are pretty intuitive, so I will not give examples on them.

# ObjectAnimator

`ViewPropertyAnimator` can be used to animate the existing properties of the view, but what if you have a custom view and you want to animate on a special property? Then you can use `ObjectAnimator`.

Let's say you have a custom progress view like this:

{{< img-post "/img" "objectanimator.gif" "" "center" >}}

{{< highlight java>}}
public class ProgressView extends View {

    final float radius = dpToPixel(80);
    
    // Property progress
    float progress = 0;

    RectF arcRectF = new RectF();

    Paint paint = new Paint(Paint.ANTI_ALIAS_FLAG);

    public ProgressView(Context context) {
        super(context);
    }

    public ProgressView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
    }

    public ProgressView(Context context, @Nullable AttributeSet attrs,
        int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    {
        paint.setTextSize(dpToPixel(40));
        paint.setTextAlign(Paint.Align.CENTER);
    }
    
    // For object animator's use
    public float getProgress() {
        return progress;
    }

    // For object animator's use
    public void setProgress(float progress) {
        this.progress = progress;
        invalidate();
    }

    @Override
    public void onDraw(Canvas canvas) {
        super.onDraw(canvas);

        float centerX = getWidth() / 2.0f;
        float centerY = getHeight() / 2.0f;

        paint.setColor(Color.parseColor("#E91E63"));
        paint.setStyle(Paint.Style.STROKE);
        paint.setStrokeCap(Paint.Cap.ROUND);
        paint.setStrokeWidth(dpToPixel(15));
        arcRectF.set(centerX - radius, centerY - radius, centerX + radius, centerY + radius);
        canvas.drawArc(arcRectF, 135, progress * 2.7f, false, paint);

        paint.setColor(Color.WHITE);
        paint.setStyle(Paint.Style.FILL);
        canvas.drawText((int) progress + "%", centerX,
            centerY - (paint.ascent() + paint.descent()) / 2, paint);
    }
}
{{< /highlight >}}

Now we can use the `objectAnimtor` to animate the property `progress` in the view.

{{< highlight java>}}
ObjectAnimator animator = ObjectAnimator.ofFloat(view, "progress", 0, 65);
    animator.setDuration(1000);
    animator.setInterpolator(new FastOutSlowInInterpolator());
    animator.start();
{{< /highlight >}}

The object animator here will automatically call `setProgress()` to set the progress, and `getProgress()` to get its initial value. So if your property is XXX, then in your custom view, you have to define `setXXX()` and `getXXX()` methods. Also be careful that in `setXXX()` you have to manually update the view by calling `invalidate()`.

## Interpolator

> An interpolator defines the rate of change of an animation. This allows the basic animation effects (alpha, scale, translate, rotate) to be accelerated, decelerated, repeated, etc.

Let's explore different types of interpolator that Android provides.

# AccelerateDecelerateInterpolator

> An interpolator where the rate of change starts and ends slowly but accelerates through the middle.

This is the default interpolator, and also probably the most natural in the physical world. This should work for you most of the time.

{{< img-post "/img" "acdcinterpolator.gif" "" "center" >}}

# LinearInterpolator

> An interpolator where the rate of change is constant

# AccelerateInterpolator

> An interpolator where the rate of change starts out slowly and then accelerates.

This is a perfect choice if you want to simulate the effect of a rocket taking off.

{{< img-post "/img" "acinterpolator.gif" "" "center" >}}

# DecelerateInterpolator

> An interpolator where the rate of change starts out quickly and then decelerates.

This is the opposite of `AccelerateInterpolator`, it is useful when you want to show something flies into the screen.

# AnticipateInterpolator

> An interpolator where the change starts backward then flings forward.

{{< img-post "/img" "antiinterpolator.gif" "" "center" >}}

# OvershootInterpolator

> An interpolator where the change flings forward and overshoots the last value then comes back.

{{< img-post "/img" "overinterpolator.gif" "" "center" >}}

# AnticipateOvershootInterpolator

> An interpolator where the change starts backward then flings forward and overshoots the target value and finally goes back to the final value.

This is the combination of `AnticipateInterpolator` and `OvershootInterpolator`.

# BounceInterpolator

> An interpolator where the change bounces at the end.

{{< img-post "/img" "bounceinterpolator.gif" "" "center" >}}

# CycleInterpolator

> Repeats the animation for a specified number of cycles. The rate of change follows a sinusoidal pattern.

Let's try to understand the cycle. When we specify the 0.5 cycle:

{{< highlight java>}}
new CycleInterpolator(0.5f);
{{< /highlight >}}

{{< img-post "/img" "cycleinterpolator1.gif" "" "center" >}}

Then 1.0f cycle:

{{< highlight java>}}
new CycleInterpolator(1f);
{{< /highlight >}}

{{< img-post "/img" "cycleinterpolator2.gif" "" "center" >}}

This looks pretty useful for certain type of loading animation.

# FastOutLinearInInterpolator

> Uses a lookup table for the Bezier curve from (0,0) to (1,1) with control points:
> P0 (0, 0)
> P1 (0.4, 0)
> P2 (1.0, 1.0)
> P3 (1.0, 1.0)

The defination makes not much sense from the doc. But basically this is just like `AccelerateInterpolator`, the difference is that `AccelerateInterpolator` uses Logarithm function, and `FastOutLinearInInterpolator` uses Bezier function.

{{< img-post "/img" "accvsfoli.png" "" "center" >}}

The red line is `FastOutSlowInInterpolator` and the green line is `AccelerateInterpolator`, see the difference? Not much right?

# FastOutSlowInInterpolator

>Interpolator corresponding to {@link android.R.interpolator#fast_out_slow_in}.

> Uses a lookup table for the Bezier curve from (0,0) to (1,1) with control points:
> P0 (0, 0)
> P1 (0.4, 0)
> P2 (0.2, 1.0)
> P3 (1.0, 1.0)

OK, basically this is similar to `AccelerateDecelerateInterpolator`. Let's compare them:

{{< img-post "/img" "losii.png" "" "center" >}}

The green line is `AccelerateDecelerateInterpolator` and the red line is `FastOutLinearInInterpolator`.

# LinearOutSlowInInterpolator

This is basically similar to `DecelerateInterpolator`.

Let's do the comparison here:

{{< img-post "/img" "losiint.png" "" "center" >}}

The green line is `DecelerateInterpolator` and the red line is `LinearOutSlowInInterpolator`.
