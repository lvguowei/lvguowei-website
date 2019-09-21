+++
author = "Guowei Lv"
categories = ["Android Development"]
keywords = ["Android", "custom view"]
description = "The basics of drawing stuff"
featured= "android-custom-view-102.png"
featuredalt= "Android custom view"
featuredpath= "/img"
title = "Android Custom View 102 (Part I)"
date = 2019-09-21T22:33:51+03:00
draft = true
+++

If 101 is like a crash course, then this 102 will provide much more detail.

In this 1st part, we focus on only one thing: **how to draw things**.

# Draw Color

Draw the entire view with one color.

{{< highlight java>}}

public class DrawColorView extends View {

    public DrawColorView(Context context) {
        super(context);
    }

    public DrawColorView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
    }

    public DrawColorView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        canvas.drawColor(Color.YELLOW);
    }
}
{{< /highlight >}}

{{< figure src="/img/drawcolor.png" >}}

# Draw Circle

Draw a circle on the view. 

Pay attention that all `drawXXX()` functions use **pixel** as unit. But we can convert **dp** to **px** with this utils function.

{{< highlight java>}}
public class Utils {

    public static float convertDpToPx(Context context, float dp) {
        return dp * context.getResources().getDisplayMetrics().density;
    }
}
{{< /highlight >}}


{{< highlight java>}}
public class DrawCircleView extends View {

    Paint paint1;
    float cx1;
    float cy1;
    float radius1;

    Paint paint2;
    float cx2;
    float cy2;
    float radius2;

    Paint paint3;
    float cx3;
    float cy3;
    float radius3;

    Paint paint4;
    float cx4;
    float cy4;
    float radius4;

    void init(Context context) {
        paint1 = new Paint();
        paint1.setAntiAlias(true);
        cx1 = convertDpToPx(context, 50);
        cy1 = convertDpToPx(context, 50);
        radius1 = convertDpToPx(context, 20);

        paint2 = new Paint();
        paint2.setAntiAlias(true);
        paint2.setStyle(Paint.Style.STROKE);
        cx2 = convertDpToPx(context, 120);
        cy2 = convertDpToPx(context, 50);
        radius2 = convertDpToPx(context, 20);

        paint3 = new Paint();
        paint3.setAntiAlias(true);
        paint3.setColor(Color.BLUE);
        cx3 = convertDpToPx(context, 190);
        cy3 = convertDpToPx(context, 50);
        radius3 = convertDpToPx(context, 20);

        paint4 = new Paint();
        paint4.setAntiAlias(true);
        paint4.setStyle(Paint.Style.STROKE);
        paint4.setStrokeWidth(convertDpToPx(context, 10));
        cx4 = convertDpToPx(context, 260);
        cy4 = convertDpToPx(context, 50);
        radius4 = convertDpToPx(context, 20);
    }

    public DrawCircleView(Context context) {
        super(context);
        init(context);
    }

    public DrawCircleView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
        init(context);
    }

    public DrawCircleView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        init(context);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        canvas.drawCircle(cx1, cy1, radius1, paint1);
        canvas.drawCircle(cx2, cy2, radius2, paint2);
        canvas.drawCircle(cx3, cy3, radius3, paint3);
        canvas.drawCircle(cx4, cy4, radius4, paint4);
    }
}

{{< /highlight >}}

The above code draws 4 circles in the view:

{{< figure src="/img/drawcircles.png" >}}




{{< highlight java>}}
{{< /highlight >}}


#
