+++
author = "Guowei Lv"
categories = ["Javascript"]
description = "Some notes from the book"
linktitle = ""
featured = "jsdg.jpg"
featuredpath = "/img"
featuredalt = ""
title = "JavaScript the Definitive Guide Distilled"
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

# function scope and hoisting

In some C-like programming languages, each block of code within curly braces has its own scope, and variables are not visible outside of the block in which they are declared.

This is called *block scope*, and JavaScript does *not* have it.

Instead, JavaScript uses *function scope*: variables are visible within the function in which they are defined and within any functions that are nested within that function.

JavaScript code behaves as if all variable declarations in a function(but not any associated assignments) are "hoisted" to the top of the function.

{{< highlight javascript>}}
var scope = "global";
function f() {
  console.log(scope);   // Prints "undefined" not "global"
  var scope = "local";  // Variable initialized here, but defined everywhere
  console.log(scope);   // Prints "local"
}
{{< /highlight >}}

as if 

{{< highlight javascript>}}
var scope = "global";
function f() {
  var scope;
  console.log(scope);   // Local variable is declared at the top of the function
  scope = "local";      // It exists here but still has "undefined" value
  console.log(scope);   // Prints "local"
}
{{< /highlight >}}

# object literals

The easiest way to create an object is to include an object literal in your JavaScript code.

{{< highlight javascript>}}
var empty = {};
var point = {x: 0, y: 0};
{{< /highlight >}}

# creating objects with new

{{< highlight javascript>}}
var o = new Object();      // Create an empty object: same as {}
var a = new Array();       // Create an empty array: same as []
var d = new Date();        // Create a Date object representing the current time
var r = new RegExp("js");  // Create a RegExp object for pattern matching
{{< /highlight >}}

# prototypes

Every JavaScript object has a second JavaScript object associated with it. This second object is known as a prototype, and the first object inherits properties from the prototype.

All objects created by object literals have the same prototype object `Object.prototype`.

Objects created using the `new` keyword and a constructor invocation use the value of the **property** property of the constructor function as their prototype. For example, the object created by `new Array()` uses `Array.prototype` as its prototype.

# Object.create()

ECMAScript 5 defines a method, `Object.create()` that creates a new object, using its first argument as the prototype of that object.

{{< highlight javascript>}}
var o1 = Object.create({x:1, y:2});        // o1 inherits properties x and y
var o2 = Object.create(null);              // o2 inherits no props or methods
var o3 = Object.create(Object.prototype);  // o3 is like {} or new Object
{{< /highlight >}}

{{< highlight javascript>}}
{{< /highlight >}}
