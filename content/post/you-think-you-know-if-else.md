+++
categories = ["Thoughts"]
description = "The subtlety of if else statement"
keywords = ["Structured Programming", "Kevlin Henney"]
title = "You Think You Know If Else?"
date = 2018-01-14T21:26:23+02:00
+++

I was shocked during this fantastic video by Kevlin Henney

{{< youtube  eEBOvqMfPoI>}}

not because all the programming history that he talked about, but by one simple example of if-else statement. Here is the leap year function he gave as an example in the talk:

{{< highlight python >}}

def isLeapYear(year) {
  if (year % 400 == 0)
    return true
  if (year % 100 == 0)
    return false
  if (year % 4 == 0)
    return true
  return false

}

{{< /highlight >}}

{{< highlight python >}}

def isLeapYear(year) {
  if (year % 400 == 0)
    return true
  else if (year % 100 == 0)
    return false
  else if (year % 4 == 0)
    return true
  else 
    return false
}
{{< /highlight >}}

Which one do you think is better?

At this point I would say the first one because it is more concise. But is that really true?

If you blur your eyes a little bit and focus on the overall structure of the two programs, you will see this:

{{< highlight python >}}

def isLeapYear(year) {
  if (year % 400 == 0)
    ...
  if (year % 100 == 0)
    ...
  if (year % 4 == 0)
    ...
  ...
}

{{< /highlight >}}

{{< highlight python >}}

def isLeapYear(year) {
  if (year % 400 == 0)
    ...
  else if (year % 100 == 0)
    ...
  else if (year % 4 == 0)
    ...
  else 
    ...
}
{{< /highlight >}}

Now it is more clear what is the difference between this two programs, the first one, any thing could happen, we are not sure what this program will do, after all there are only 3 if statements.

The second program remains sort of clear, there will be 4 branchs, each of which will probably return some result.

This is a trivial program, so the two does not make much difference, but imagine this in a more complex context.

No which one do you prefer? I think the second one is winning on clarity.

I use if-else pretty much freely for my whole career and never give some serious thoughts on it. Now I finally understand it (I think).

This confirms my 2 hypothesis:

1. People don't know what they are doing (even though they claim they do) most of the time. (Myself as an example as I claim I understand if-else and are using them perfectly)
2. Programming is hard. Not to code correctly, but to code clearly.
