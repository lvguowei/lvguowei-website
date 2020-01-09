+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "Use of IIB block in Android"
featured = ""
featuredpath = ""
featuredalt = ""
title = "Instance Initialization Block in Android"
date = 2020-01-09T21:22:02+02:00
+++

This will be a short and sweet post.

The usage of Instance Initialization Block of Java in custom android views.

First of all, I have a confession to make. After almost 10 years of Java programming, I do not really know what is IIB and why it exists.

Pretty obviously, the purpose of IIB is to **initialize instance variables of a class**.

But you may ask isn't that the job of constructors?

You are correct, constructors can initialize instance variables. But, what if there are multiple constructors? Then you have to create a function called something like `init()` and call that inside every constructor.

This is exactly where IIB can come into help, because it is **run before constructors**.

In Android development, I've seen a lot of people do this when creating a custom view (including myself before this post):

{{< highlight java>}}

public class CustomView extends View {
    private Paint paint;

    private void init() {
        paint = new Paint();
    }

    public CustomView(Context context) {
        super(context);
        init();
    }

    public CustomView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
        init();
    }

    public CustomView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        init();
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        canvas.drawLine(0, 0, 100, 100, paint);
    }
}

{{< /highlight >}}


By using IIB, we can write:

{{< highlight java>}}

public class CustomView extends View {
    private Paint paint;

    {
        paint = new Paint();
    }

    public CustomView(Context context) {
        super(context);
    }

    public CustomView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
    }

    public CustomView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        canvas.drawLine(0, 0, 100, 100, paint);
    }
}

{{< /highlight >}}


I think this is pretty neat.
