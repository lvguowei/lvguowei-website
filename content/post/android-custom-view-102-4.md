+++
author = "Guowei Lv"
categories = ["Android Custom View 102"]
keywords = ["Android", "custom view"]
description = "Drawing Order"
featured= "android-custom-view-102.png"
featuredpath= "/img"
title = "Android Custom View 102 (Part 4)"
date = 2020-01-12T21:50:24+02:00
+++

In this post we talk about the drawing order in custom view.

# After `super.onDraw()`

What we have done in the previous episodes is to override the `onDraw()` method of the `View` class.

A lot of times we do not need to create our own custom view from scratch. We can simply extends an existing view, e.g. `ImageView`. If we want to draw something on top of the image, we can write that after the `super.onDraw()`.

In the following example, we show some debugging info about the image.

{{< highlight java>}}
public class AfterOnDrawView extends AppCompatImageView {

    private Paint paint;

    {
        paint = new Paint(Paint.ANTI_ALIAS_FLAG);
        paint.setColor(Color.RED);
        paint.setTextSize(20);

    }

    public AfterOnDrawView(Context context) {
        super(context);
    }

    public AfterOnDrawView(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public AfterOnDrawView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        Drawable drawable = getDrawable();
        if (drawable != null) {
            canvas.save();
            canvas.concat(getImageMatrix());
            Rect bounds = drawable.getBounds();
            canvas.drawText("width: " + bounds.width() + " height: " + bounds.height(), 10, 20, paint);
            canvas.restore();
        }
    }
}

{{< /highlight >}}

{{< figure src="/img/afterondraw.png" >}}

# Before `super.onDraw()`

This maybe not very common but still can be pretty useful at times.

For example, we can use it to high light text in textview.

{{< highlight java>}}
public class BeforeOnDrawView extends AppCompatTextView {
    private Paint paint;
    private RectF bounds;

    {
        paint = new Paint(Paint.ANTI_ALIAS_FLAG);
        paint.setColor(Color.YELLOW);
        bounds = new RectF();
    }

    public BeforeOnDrawView(Context context) {
        super(context);
    }

    public BeforeOnDrawView(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public BeforeOnDrawView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        Layout layout = getLayout();
        bounds.left = layout.getLineLeft(1);
        bounds.right = layout.getLineRight(1);
        bounds.top = layout.getLineTop(1);
        bounds.bottom = layout.getLineBottom(1);
        canvas.drawRect(bounds, paint);

        super.onDraw(canvas);
    }
}
{{< /highlight >}}

{{< figure src="/img/beforeondrawview.png" >}}

# After `super.dispatchDraw()`

For `ViewGroup`s such as `LinearLayout`, they themselves usually don't draw anything, but to let its children views to draw.

But sometimes we do want the `ViewGroup` to draw something. Let's say we want a customized `LinearLayout` that contains a `ImageView` and a `TextView`. And we want to draw a big red cross on top of them.

If you try to put the red cross drawing code inside `onDraw()` you will find that it doesn't work. This is because of the drawing order. Every view will first draw itself, then draw its children. In this case, the red cross is simply covered by the its children views. Then how do we do it then? What we want is to draw something *after* the view's children get drawn.

The anwser is to override `dispatchDraw()`. Simply put, **`onDraw()` is used to draw the view itself, and `dispatchDraw()` is used to draw its children views.**

Let's see the code:

{{< highlight java>}}
public class OnDrawLayout extends LinearLayout {

    private Paint paint = new Paint(Paint.ANTI_ALIAS_FLAG);

    {
        paint.setColor(Color.RED);
        paint.setStrokeWidth(5);
        // This means that this view group has something to draw itself other than its children
        setWillNotDraw(false);
    }

    public OnDrawLayout(Context context) {
        super(context);
    }

    public OnDrawLayout(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
    }

    public OnDrawLayout(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    @Override
    protected void dispatchDraw(Canvas canvas) {
        super.dispatchDraw(canvas);

        Rect rect = new Rect();
        getDrawingRect(rect);
        canvas.drawLine(rect.left, rect.top, rect.right, rect.bottom, paint);
        canvas.drawLine(rect.left, rect.bottom, rect.right, rect.top, paint);
    }
}
{{< /highlight >}}

{{< figure src="/img/ondrawlayout.png" >}}

# The drawing process

Actually the view's drawing process has more stuff then drawing its content and children. Basically it has the following steps:


1. Draw the background -> `drawBackground()` (This method is private, cannot override)
2. Draw view's content -> `onDraw()`
3. Draw children       -> `dispatchDraw()`
4. Draw scrollbar and foreground -> `onDrawForeground()`

# After `super.onDrawForeground()`

If we want to draw something on top of the view's foreground, we can write it here.

One example is to draw a price tag on top of the image.

{{< highlight java>}}
public class AfterOnDrawForegroundView extends AppCompatImageView {

    private Paint paint = new Paint(Paint.ANTI_ALIAS_FLAG);
    private String text = "On Sale";
    private float marginFromTop = 20;
    private float textMarginH = 10;
    private float textMarginW = 10;

    {
        paint.setTextSize(30);
    }

    public AfterOnDrawForegroundView(Context context) {
        super(context);
    }

    public AfterOnDrawForegroundView(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public AfterOnDrawForegroundView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    @Override
    public void onDrawForeground(Canvas canvas) {
        super.onDrawForeground(canvas);

        Rect textBounds = new Rect();

        paint.getTextBounds(text, 0, text.length(), textBounds);


        paint.setColor(Color.parseColor("#F44336"));
        canvas.drawRect(0, marginFromTop, textBounds.width() + 2 * textMarginW, marginFromTop + textBounds.height() + 2 * textMarginH, paint);
        paint.setColor(Color.WHITE);
        canvas.drawText(text, textMarginW, marginFromTop + textMarginH + textBounds.height(), paint);
    }
}

{{< /highlight >}}


The image view has a transparent black foreground

{{< highlight xml>}}
<com.example.customui.practice.AfterOnDrawForegroundView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:foreground="#66000000"
        android:src="@drawable/wanglu" />
{{< /highlight >}}

{{< figure src="/img/ondrawforeground.png" >}}

# Before `draw()`

Is it possible to draw something before `drawBackground()`? Since it is private we cannot override it.

Actually, there is another method called `draw()`. If we check the source code it roughly looks like:

{{< highlight java>}}

public void draw(Canvas canvas) {
    ...
    drawBackground(Canvas);
    onDraw(Canvas);
    dispatchDraw(Canvas);
    onDrawForeground(Canvas);
    ...
}
{{< /highlight >}}

As you can see, this method is the entry point of all the other draw methods.

If we want to draw something before anything gets drawn, we can override this function.

For example, we can add a background color to an `EditText`.

{{< highlight java>}}
public class BeforeDrawView extends AppCompatEditText {
    public BeforeDrawView(Context context) {
        super(context);
    }

    public BeforeDrawView(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public BeforeDrawView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    @Override
    public void draw(Canvas canvas) {
        canvas.drawColor(Color.GREEN);
        super.draw(canvas);
    }
}
{{< /highlight >}}

{{< figure src="/img/beforedrawview.png" >}}

Note that we cannot set the background in this case because the underline belongs to the background. If we set the background to green then the underline will disappear.

~THE END~
