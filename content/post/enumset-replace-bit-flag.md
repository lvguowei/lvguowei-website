+++
categories = ["Java"]
description = "How to use EnumSet to replace bit flags"
featured = "featured-java.png"
featuredpath = "/img"
keywords = ["Java", "Thinking in Java", "EnumSet"]
title = "Use Enumset to Replace Bit Flag"
date = 2017-09-10T19:50:32+03:00
+++
I was reorganizing my bookshelf today and found my *Thinking in Java* lying there so I decided to skim through it. I was quite surprised how much basic stuff in Java I overlooked or forgot. So I decided to re-read the book and write short blog posts of the things that I think are useful but not too many people are talking about nowadays. Here is the first one.

{{< img-post "/img" "tij.jpg" "Thinking in Java" "right" >}}

# What is bit flag?

In the good old days, C programmers often use bit flags to save some memory. So what is bit flag and how can it save on memory, let's look at an example.

Say you want to pass in some configurations to a function, for example some layout options and they are all booleans, like **left**, **top**, **right**, **bottom**. The combinations could be **left and top**, or **right and bottom**, etc. We can use booleans for each value, but boolean usually takes up 1 byte memory, but what we really need is 1 bit, 1 for ture and 0 for false. So we can play some bit tricks:

First we we define options:

{{< highlight c >}}
unsigned char options;

enum Options {
  Left   = 0x01,   // 00000001
  Top    = 0x02,   // 00000010
  Right  = 0x04,   // 00000100
  Bottom = 0x08    // 00001000
};
{{< /highlight >}}

So in this case the `unsigned char` is 8 bits long, we can store up to 8 flags in one `unsigned char`. To create options we can use bitwise OR:

{{< highlight c >}}
options = Left | Top;
{{< /highlight >}}

And to check the options we can use bitwise AND:

{{< highlight c >}}
if (options & Top) {...}
if (options & Left) {...}
if (options & Right) {...}
if (options & Down) {...}
{{< /highlight >}}

As you can see that this way does save some memory but makes the code also quite indirect and obscure.

# Using EnumSet instead of flags

There is actually a class in Java called `EnumSet` which is designed specifically to replace bit flags.
Let's see how to use it.

First we have to define the options as `Enum`:

{{< highlight java >}}
enum Options {
  Left, Top, Right, Bottom
}
{{< /highlight >}}

To create and check options we can do:

{{< highlight java >}}

EnumSet<Options> options = EnumSet.of(Options.Left, Options.Top);

if (options.contains(Options.Left)) {
  System.out.println("Left");
}

if (options.contains(Options.Top)) {
  System.out.println("Top");
}
{{< /highlight >}}

The underlying implementation of `EnumSet` is very similar to bit flags, so there is no lose on the performance.

I think I will try to use `EnumSet` to implement feature flags next time.
