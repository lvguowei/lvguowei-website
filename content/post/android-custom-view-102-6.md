+++
author = "Guowei Lv"
categories = ["Android Development"]
keywords = ["Android", "custom view"]
description = "Draw Text"
featured= "android-custom-view-102.png"
featuredpath= "/img"
title = "Android Custom View 102 (Part 6)"
date = 2020-04-19T07:47:47+03:00
+++

In this article we talk about drawing text.

Let's see how to implement a view which simply displays some text. Let's call it `MyTextView`.

This is how it will look like:

{{< highlight xml>}}
<!-- Omitted constraintlayout related stuff-->
<com.example.MyTextView.MyTextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:padding="10dp"
    android:background="@color/colorAccent"
    app:myTextColor="#000000"
    app:myText="Hello World! My love!"
    app:myTextSize="24sp" />
{{< /highlight >}}

Let's first define the custom attributes of the view, in `values/attrs.xml`


{{< highlight xml>}}
<resources>
  <declare-styleable name="MyTextView">
    <attr format="string" name="myText" />
    <attr format="color" name="myTextColor" />
    <attr format="dimension" name="myTextSize" />
  </declare-styleable>
</resources>
{{< /highlight >}}

Then in the `MyTextView.kt` we can get those values and apply them to the view.(see the code at the end of the article)

One thing to note is that everything in `MyTextView` is in pixels, so we need a helper function to convert from px to dp(sp) and vice versa. See the function `sp2px()` in `MyTextView.kt`.

The key to drawing text is to find the baseline.

{{< figure src="/img/howtodrawtext.jpg" >}}

Now lets see the code.


{{< highlight java>}}

class MyTextView 
@JvmOverloads constructor(context: Context, attrs: AttributeSet? = null, defStyleAttr: Int = 0)
: View(context, attrs, defStyleAttr) {

  private val mText: String
  private var mTextSize: Int = 15
  private var mTextColor: Int = Color.BLACK
  private var mPaint: Paint = Paint(Paint.ANTI_ALIAS_FLAG)
  private val bounds: Rect = Rect()


  init {
    val typedArray = context.obtainStyledAttributes(attrs, R.styleable.MyTextView)
    mText = typedArray.getString(R.styleable.MyTextView_myText) ?: ""
    mTextColor = typedArray.getColor(R.styleable.MyTextView_myTextColor, mTextColor)
    mTextSize = typedArray.getDimensionPixelSize(R.styleable.MyTextView_myTextSize, sp2px(mTextSize))
    typedArray.recycle()

    mPaint.apply {
      textSize = mTextSize.toFloat()
      color = mTextColor
    }.run {
      getTextBounds(mText, 0, mText.length, bounds)
    }
  }

  override fun onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int) {
    super.onMeasure(widthMeasureSpec, heightMeasureSpec)
    setMeasuredDimension(
        resolveSize(bounds.width() + paddingLeft + paddingRight, widthMeasureSpec),
        resolveSize(bounds.height() + paddingTop + paddingBottom, heightMeasureSpec))
  }

  override fun onDraw(canvas: Canvas) {
    super.onDraw(canvas)
    canvas.drawText(mText, paddingLeft.toFloat(), height - bounds.bottom.toFloat() - paddingBottom, mPaint)
  }

  private fun sp2px(sp: Int): Int {
    return TypedValue
        .applyDimension(TypedValue.COMPLEX_UNIT_SP, sp.toFloat(), resources.displayMetrics).toInt()
  }
}
{{< /highlight >}}

{{< figure src="/img/textview.png" >}}
