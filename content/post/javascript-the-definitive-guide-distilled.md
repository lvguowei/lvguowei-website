+++
author = "Guowei Lv"
categories = ["Javascript"]
description = "Some notes from the book"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Javascript the Definitive Guide Distilled"
date = 2019-04-05T10:19:52+03:00
+++

# null and undefined

`null` is a language keyword and `undefined` is a predefined global object.

`undefined` represents a system level, unexpected or error-like absence of value.

`null`represents program-level, normal or expected absence of value.

When writing programs yourself always use `null`.

# global object

When the JavaScript interpreter starts, it creates a new global object and gives it an initial set of properties.

In client-side JavaScript, the `window` object serves as the global object.

# wrapper objects

Whenever you try to refer to a property of a string, number or boolean, JavaScript converts the value to an object as if by calling `new String(x)`.

This object inherits methods and is used to resolve the property reference. Once the property has been resolved, the newly created object is discarded.

The temporary objects created when you access a property are known as *wrapper objects*.

{{< highlight javascript>}}
var s = "test";
s.len = 4; // Create a new S = new String(s); and create a new "len" property and set it to 4, then disgard the newly created S object
var t = s.len; // Create a new S = new String(s); there is no property "len" so return undefined
{{< /highlight >}}

# type conversion idioms

{{< highlight javascript>}}
x + "" // Same as String(x)
+x     // Same as Number(x)
!!x    // Same as Boolean(x)
{{< /highlight >}}

# variable declaration

Before use a variable in JavaScript program, you should *declare* it.
{{< highlight javascript>}}
var i, sum;
{{< /highlight >}}

If you don't specify an initial value for a variable with the `var` statement, the variable will be `undefined`.


{{< highlight javascript>}}
{{< /highlight >}}
