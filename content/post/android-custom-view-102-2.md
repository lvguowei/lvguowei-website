+++
author = "Guowei Lv"
categories = ["Android Development"]
keywords = ["Android", "custom view"]
description = "More about Paint"
featured= "android-custom-view-102.png"
featuredalt= "Android custom view"
featuredpath= "/img"
title = "Android Custom View 102 (Part II)"
date = 2019-09-24T13:28:35+03:00
+++


In this post, we will be focusing on the `Paint`.

# Color

The color in `Paint` has 3 parts: **basic color**, **color filter** and **xfermode**.

## Basic color

There are 2 ways to set color in `Paint`: use `setColor()` and use `Shader`.

### Set color directly

Two methods can be used:

{{< highlight java>}}
paint.setColor(Color.parseColor("#B90E83");
paint.setARGB(100, 255, 0, 0);
{{< /highlight >}}

There is no difference, pick whichever you like.

### Set color using Shader

There are different types of `Shader`, let's look them one by one.

#### LinearGradient Shader

{{< highlight java>}}
Paint paint = new Paint();
Shader shader = new LinearGradient(0, 0, 400, 400, Color.RED, Color.GREEN, Shader.TileMode.CLAMP);
paint.setShader(shader);
...

@Override
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    canvas.drawCircle(200, 200, 200, paint);
}
{{< /highlight >}}

Point (0, 0) is RED, point (400, 400) is GREEN, and the area in between is a linear transition between the two colors.

{{< figure src="/img/lineargradient.png" >}}

The `TileMode` needs more explanation.

**CLAMP**

*Replicate the edge color if the shader draws outside of its original bounds.*

{{< highlight java>}}

shader = new LinearGradient(0, 0, 100, 100, Color.RED, Color.BLUE, Shader.TileMode.CLAMP);

...

@Override
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    canvas.drawRect(0, 0, 400, 400, paint);
}
{{< /highlight >}}

The `shader` defines area between (0, 0) and (100, 100). But the rectangle is from (0, 0) to (400, 400), which is outside of the shader's bounds.

{{< figure src="/img/shaderclamp.png" >}}

As shown in the picture, the area outside of shader replicates the edge color of the shader BLUE.

Let's change the mode to **REPEAT**.

{{< figure src="/img/shaderrepeat.png" >}}

Now **MIRROR**.

{{< figure src="/img/shadermirror.png" >}}

### RadialGradient Shader

This is easy to understand, look at the example:

{{< highlight java>}}
shader = new RadialGradient(200, 200, 100, Color.RED, Color.BLUE, Shader.TileMode.CLAMP);
paint.setShader(shader);

...

@Override
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    canvas.drawCircle(200, 200, 200, paint);
}
{{< /highlight >}}

{{< figure src="/img/radialgradient.png" >}}

By changing the `TileMode` we can get the following:

**REPEAT**

{{< figure src="/img/radialgradientrepeat.png" >}}

**MIRROR**

{{< figure src="/img/radialgradientmirror.png" >}}

### SweepGradient Shader

This one looks like a radar.
{{< highlight java>}}
shader = new SweepGradient(200, 200, Color.RED, Color.BLUE);
{{< /highlight >}}

{{< figure src="/img/sweepgradient.png" >}}

### Bitmap Shader

Finally this is not a gradient shader.

{{< highlight java>}}
Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.lady);

shader = new BitmapShader(bitmap, CLAMP, CLAMP);

...

@Override
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    canvas.drawCircle(200, 200, 200, paint);
}

{{< /highlight >}}


{{< figure src="/img/bitmapshader.png" >}}

**REPEAT**

{{< figure src="/img/bitmapshaderrepeat.png" >}}

**MIRROR**

{{< figure src="/img/bitmapgradientmirror.png" >}}

### Compose Shader

We can use `ComposeShader` to combine 2 shaders.

Here is a BitmapShader:

{{< highlight java>}}
imageShader = new BitmapShader(image, REPEAT, REPEAT);
paint.setShader(imageShader);
...

@Override
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    canvas.drawRect(0, 0, imageWidth, imageHeight, paint);
}
{{< /highlight >}}

{{< figure src="/img/composeshader1.png" >}}

Here is another image shader:

{{< highlight java>}}
heartShader = new BitmapShader(heart, REPEAT, REPEAT);
paint.setShader(heartShader);
...

@Override
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    canvas.drawRect(0, 0, imageWidth, imageHeight, paint);
}
{{< /highlight >}}

{{< figure src="/img/composeshader2.png" >}}

Now combine the two shaders:

{{< highlight java>}}
imageShader = new BitmapShader(image, REPEAT, REPEAT);
heartShader = new BitmapShader(heart, REPEAT, REPEAT);
composeShader = new ComposeShader(imageShader, heartShader, PorterDuff.Mode.SRC_OVER);

paint.setShader(composeShader);
...

@Override
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    canvas.drawRect(0, 0, imageWidth, imageHeight, paint);
}
{{< /highlight >}}

{{< figure src="/img/composeshader3.png" >}}

Note that

`imageShader`: The colors from this shader are seen as the "dst" by the mode

`heartShader`: The colors from this shader are seen as the "src" by the mode

Actually there different ways to combine the 2 shaders, it is controlled by `PorterDuff.Mode`. Let's see some more examples:

**DST_OUT**
{{< figure src="/img/composeshaderdstout.png" >}}

**DST_IN**
{{< figure src="/img/composeshaderdstin.png" >}}

**LIGHTEN**
{{< figure src="/img/composeshaderlighten.png" >}}

There are many others, please check the [official documentation](https://developer.android.com/reference/android/graphics/PorterDuff.Mode.html) if you are interested.

Aside from `setColor()` and `setShader()`, `Paint` can use `ColorFilter` as a second layer to process the colors.

## Filter

There are 3 subclasses of `ColorFilter`: `LightingColorFilter`, `PorterDuffColorFilter` and `ColorMatrixColorFilter`.

### LightingColorFilter

It's constructor is `ColorMatrixColorFilter(int mul, int add)`, where `mul` and `add` are the same int values as the colors. The result color can be calculated as follows:

R' = R * mul.R / 0xff + add.R

G' = G * mul.G / 0xff + add.G

B' = B * mul.B / 0xff + add.B

The simplest filter is to not filter at all:

{{< highlight java>}}
filter = new LightingColorFilter(0xffffff, 0x000000);
paint.setColorFilter(filter);
{{< /highlight >}}

{{< figure src="/img/lightingfilter1.png" >}}

Put in the values we get:

R' = R * **0xff** / 0xff + **0x00** = R

G' = G * **0xff** / 0xff + **0x00** = G

B' = B * **0xff** / 0xff + **0x00** = B

OK, let's remove the RED color:

{{< highlight java>}}
filter = new LightingColorFilter(0x00ffff, 0x000000);
paint.setColorFilter(filter);
{{< /highlight >}}

R' = R * **0x00** / 0xff + **0x00** = 0x00

{{< figure src="/img/lightingfilter2.png" >}}

Or we can enhance the RED color:

{{< highlight java>}}
filter = new LightingColorFilter(0xffffff, 0x300000);
paint.setColorFilter(filter);
{{< /highlight >}}


R' = R * **0xff** / 0xff + **0x30**

{{< figure src="/img/lightingfilter3.png" >}}


### PorterDuffColorFilter

We have covered PorterDuff mode before, it still works the same, just now it is applied to a color.

I show only one example here:

{{< highlight java>}}
filter = new PorterDuffColorFilter(Color.RED, PorterDuff.Mode.LIGHTEN);
paint.setColorFilter(filter);
{{< /highlight >}}

{{< figure src="/img/porterdufffilter.png" >}}

### ColorMatrixColorFilter

This is pretty advanced color filter, it is also very powerful. It uses a `ColorMatrix` to filter the colors:

{{< highlight java>}}

[ a, b, c, d, e,
f, g, h, i, j,
k, l, m, n, o,
p, q, r, s, t ]
{{< /highlight >}}


And the calculation is like:

R’ = a*R + b*G + c*B + d*A + e;

G’ = f*R + g*G + h*B + i*A + j;

B’ = k*R + l*G + m*B + n*A + o;

A’ = p*R + q*G + r*B + s*A + t;


So basically you can use this to do anything.

## XferMode

The last layer of color processing in `Paint` is *Transfer Mode*. Simply put, it takes the thing you are going to draw as SRC, and the things already drawn as DST, and pick one of the `PorterDuff.Mode` to deal with color combination.

Its usage is very simple. Let's look at one view without any XferMode set.

{{< highlight java>}}
@Override
public void draw(Canvas canvas) {
    super.draw(canvas);
    
    // Draw background color
    canvas.drawColor(Color.GRAY);
    
    // Draw yello circle
    paint.setColor(Color.YELLOW);
    canvas.drawCircle(100, 100, 100, paint);
    
    // Draw blue rectangle
    paint.setColor(Color.BLUE);
    canvas.drawRect(100, 100, 300, 200, paint);
}

{{< /highlight >}}

{{< figure src="/img/xfermode1.png" >}}

As expected, the thing drawn later will draw on top of the previous things on the canvas. Most of the time this is what we want, but we can also mixin the `PorterDuff.Mode` to have more control as to how things get combined.

Let's put in the **DST_OUT** before drawing the rectangle:

{{< highlight java>}}
@Override
public void draw(Canvas canvas) {
    super.draw(canvas);
    
    // Draw background color
    canvas.drawColor(Color.GRAY);
    
    // Draw yello circle
    paint.setColor(Color.YELLOW);
    canvas.drawCircle(100, 100, 100, paint);

    // Set XferMode
    paint.setXfermode(new PorterDuffXfermode(PorterDuff.Mode.DST_OUT));
    
    // Draw blue rectangle
    paint.setColor(Color.BLUE);
    canvas.drawRect(100, 100, 300, 200, paint);
    
    // Clear xfermode
    paint.setXfermode(null);
}
{{< /highlight >}}

{{< figure src="/img/xfermode2.png" >}}

**DST_OUT** means:

>Keeps the destination pixels that are not covered by source pixels. Discards destination pixels that are covered by source pixels. Discards all source pixels.

But wait, this is not exactly what we have in mind. The part of the circle that is covered by the rectangle now becomes white, this is correct. **But why the background color also becomes white?**

To understand this, we have to introduce the `layer`s. When we draw stuff directly on the canvas, everything is drawn in one layer. That is to say, the background color, the circle and the rectangle are all on the same default layer. So before the rectangle is drawn, both the background color and the cirle are treated as `DST`, so that is why the background also becomes white. 

*By the way, the white color we see is not the color of the view, it is because the color taken out becomes transparent, and the activity's background color is white.*

Is there a way to preserve the background color?

The answer is yes, we can use an extra layer to do it.

{{< highlight java>}}
@Override
public void draw(Canvas canvas) {
    super.draw(canvas);
    
    // Draw background color
    canvas.drawColor(Color.GRAY);
    
    // All drawing from now on will be on another "layer"
    int saved = canvas.saveLayer(null, null, Canvas.ALL_SAVE_FLAG);
    
    // Draw yellow circle
    paint.setColor(Color.YELLOW);
    canvas.drawCircle(100, 100, 100, paint);

    // Set XferMode
    paint.setXfermode(new PorterDuffXfermode(PorterDuff.Mode.DST_OUT));
    
    // Draw blue rectangle
    paint.setColor(Color.BLUE);
    canvas.drawRect(100, 100, 300, 200, paint);

    // Clear xfermode
    paint.setXfermode(null);
    
    // Merge the new "layer" back into the default "layer"
    canvas.restoreToCount(saved);
}
{{< /highlight >}}

{{< figure src="/img/xfermode3.png" >}}

This looks better. Let's see what happens.

1. Draw the background color on the default layer.
2. Set up a new layer.
3. Draw the circle on the new layer.
4. Set XferMode.
5. Draw the Rectangle on the new layer.
6. Merge the new layer back to the default layer.



{{< highlight java>}}
{{< /highlight >}}
