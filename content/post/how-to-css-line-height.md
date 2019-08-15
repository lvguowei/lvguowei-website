+++
author = "Guowei Lv"
categories = ["HTML&CSS"]
keywords = ["css3", "line-height", "font-size", "html5"]
description = "Quirks of using line-height in CSS"
featured = ""
featuredpath = ""
featuredalt = ""
title = "How to use CSS Line-Height"
date = 2019-08-15T20:44:18+03:00
+++

There are 4 ways to specify line height in CSS:

{{< highlight css>}}
/* The number way*/
line-height: 1.5;

/* The em way*/
line-height: 1.5em;

/* The percentage way*/
line-height: 150%;

/* The pixel way*/
line-height: 24px;
{{< /highlight >}}

In simple settings, they are pretty much interchangable. For example:

{{< highlight css>}}
<!doctype html>
<html lang="en">
    <head>
        <meta charset="UTF-8"/>
        <title>Document</title>
        <style>
         p {
             font-size: 16px;
             line-height: 1.5;
         }
        </style>
    </head>
    <body>
        <p>I'm a paragraph.</p>
    </body>
</html>

{{< /highlight >}}

Here `p` can use all 4 ways and there would be no difference.

The way the browser calculate line-height is to convert it to pixels. And here is how:

* **24px** -> 24px
* **1.5** -> 1.5 * 16px = 24px
* **1.5em** -> 1.5 * 16px = 24px
* **150%** -> 150% * 16px = 24px

As you can see, except for the direct px value, they are all relative to the `font-size` of `p`.


So can we use them freely as we wish?

The short answer is: **NO**.

And I would also say prefer to use *the number way*, because of the inheritance rule that we are going to examine next.

Let's change the styles a little. Now we add styles for `body`, it has a bigger `font-size`. We also move the `line-height` from `p` to `body`.

{{< highlight css>}}

<!doctype html>
<html lang="en">
    <head>
        <meta charset="UTF-8"/>
        <title>Document</title>
        <style>
         body {
             font-size: 24px;
             line-height: 1.5;
         }
         p {
             font-size: 16px;
         }
        </style>
    </head>
    <body>
        <p>I'm a paragraph.</p>
    </body>
</html>

{{< /highlight >}}

What is the `line-height` for `p` in this case?

Since `p` is a child of `body`, `p` inherits the `line-height` value `1.5` from `body`, and use it to multiply **its own** `font-size`, so its `line-height` is `1.5 * 16 = 24px`.

What if we use `1.5em`?

Let's try it in the browser and see what value we get.

It's **36px**!

Actually, it uses the `font-size` of the body to calculate the value: `24 * 1.5 = 36px`.

And the same applies to the `percentage way` and `em way`.

In other words, when it comes to inheritance, the `number way` will pass only the number and applys it to the child's `font-size`; on the other hand, the `percentage way` and `em way` will apply the `line-height` to the parent's `font-size` and pass the calculated value to the child.

The result is that if you use `the number way` in `body` for example, no matter the `font-size` of its children elements, the `line-height` will scale evenly.
