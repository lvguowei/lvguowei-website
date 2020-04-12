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

# ViewPropertyAnimator

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
