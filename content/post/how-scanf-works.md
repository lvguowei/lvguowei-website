+++
author = "Guowei Lv"
categories = ["C Programming"]
keywords = ["scanf", "c programming"]
description = "Shows in detail how scanf works and its gotchas"
featured = "featured-c-programming.jpg"
featuredpath = "/img"
featuredalt = ""
title = "How Scanf Works"
date = 2019-08-08T20:36:01+03:00
+++

`scanf` is essentially a "pattern matching" function that tries to match up groups of input characters with conversion specifications.

# An Example
No blah blah blah, let's see an example.

{{< highlight c >}}
int i, j;
float x, y;

scanf("%d%d%f%f", &i, &j, &x, &y);

// input
// <space><space>1-20.3-4.0e3<ret>
{{< /highlight >}}

Here is how `scanf` would process the new input:

1. Skips the leading 2 spaces.
2. Conversion specification: `%d`. The first nonblank input character is `1`; since integers can begin with `1`, `scanf` then reads the next character, `-`. Recognizing that `-` can't appear inside an integer, `scanf` stores `1` into `i` and puts the `-` character back.
3. Conversion specification: `%d`. `scanf` then reads the character `-`, `2`, `0`, and `.`. Since an integer can't contain a decimal point, `scanf` stores `-20` into `j` and puts the `.` character back.
4. Conversion specification: `%f`. `scanf` reads the character `.`, 3, and `-`. Since a floating point number can't contain a minus sign after a digit, `scanf` stores `0.3` into `x` and puts the `-` character back.
5. Conversion specification: `%f`. Lastly, `scanf` reads the characters `-`, `4`, `.`, `0`, `e`, `3` and `<ret>`. Since a floating point number cannot contain a new-line character, `scanf` stores `-4.03e3` into y and puts the new-line character back.

# Ordinary Characters in Format Strings
The concept of pattern matching can be taken one step further by writing format strings that contain ordinary characters in addition to conversion specifications.

* **White-space characters**. When it encounters one or more consecutive white-space characters in a format string, `scanf` repeatedly reads white-space characters from the input until it reaches a non-white-space character (which is put back).

* **Other characters**. When it encounters a non-white-space character in a format string, `scanf` compares it with the next input character.

Suppose the format string is `%d/%d` and the input is `<space>5/<space>96`, `scanf` skips the first space while looking for an integer, matches `%d` with `5`, matches `/` with `/`, skips a space while looking for another integer, and matches `%d`with 96.

On the other hand, if the input is `<space>5<space>/<space>96`, `scanf` skips one space, matches `%d` with `5`, **then attempts to match the `/` in the format string with a space in the input**. There is no match, so `scanf` puts the space back; the `<space>/<space>96` characters remain to be read by the next call of `scanf`. **To allow spaces after the first number, we should use the format string "%d /%d" instead.**

# scanf can be naughty

Be careful if you mix `getchar` and `scanf` in the same program. `scanf` has a tendency to leave behind characters that it has *peeked* at but not read, including the new-line character. Consider what happens if we try to read a number first, then a character.

{{< highlight c >}}
printf("Enter an integer: ");
scanf("%d", &i);
printf("Enter a command: ");
command = getchar();
{{< /highlight >}}

The call of `scanf` will leave behind any characters that weren't consumed during the reading of `i`, including the new-line character. `getchar` will fetch the first leftover character, which wasn't what we had in mind.
