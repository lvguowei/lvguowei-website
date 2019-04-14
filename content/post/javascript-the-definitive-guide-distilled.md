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

ECMAScript 5 defines a method, `Object.create()` that creates a new object, *using its first argument as the prototype of that object*.

{{< highlight javascript>}}
var o1 = Object.create({x:1, y:2});        // o1 inherits properties x and y
var o2 = Object.create(null);              // o2 inherits no props or methods
var o3 = Object.create(Object.prototype);  // o3 is like {} or new Object
{{< /highlight >}}

# testing properties

1. Use the `in` operator. 

Returns true if the object has an own property or an inherited property by that name.
{{< highlight javascript>}}
var o = {x: 1};
"x" in o;        // true
"toString" in o; // true
"y" in o;        // false
{{< /highlight >}}
2. Use method `hasOwnProperty()`

Returns true only if the property is the object's own property. It returns false for inherited properties.
{{< highlight javascript>}}
var o = {x: 1};
o.hasOwnProperty("x");       // true
o.hasOwnProperty("toString") // false
{{< /highlight >}}
3. Use method `propertyIsEnumerable()`

Returns true only if the property is the object's own property and is enumerable.


# enumerating properties

1. **for in** loop

Loops through the object's enumerable properties (own and inherited).

To loop only own properties:

{{< highlight javascript>}}
for (p in o) {
  if (!o.hasOwnProperty(p)) continue;
  ...
}
{{< /highlight >}}

To loop only data properties:

{{< highlight javascript>}}
for (p in o) {
  if (typeof p === "function") continue;
  ...
}
{{< /highlight >}}
2. `Object.keys()` in ES5

`Object.keys()` returns an array of the names of the enumerable own properties of an object.

# property getters and setters

Properties defined by getters and setters are known as *accessor properties*.

{{< highlight javascript>}}
var o = {
  // An ordinary data property
  data_prop: value,
  
  // An accessor property defined as a pair of functions
  get accessor_prop() {},
  set accessor_prop(value) {}
}
{{< /highlight >}}

One example use of accessor properties:

{{< highlight javascript>}}
var p = {
  // x and y are regular read-write data properties
  x: 1.0,
  y: 1.0,
  
  // r is a read-write accessor property with getter and setter
  get r() {
    return Math.sqrt(this.x*this.x + this.y*this.y);
  },
  set r(newValue) {
    var oldValue = Math.sqrt(this.x*this.x + this.y*this.y);
    var ratio = newValue / oldValue;
    this.x *= ratio;
    this.y *= ratio;
  },
  
  // theta is a read only accessor property with getter only
  get theta() {
    return Math.atan2(this.y, this.x);
  }
};
{{< /highlight >}}

# property attributes
Object's property has attributes.

Data property has 4 attributes: **value**, **writable**, **enumerable** and **configurable**.

Accessor property has 4 attributes: **get**, **set**, **enumerable** and **configurable**.

Get property's attributes:


{{< highlight javascript>}}
// Get attributes of data property
js> Object.getOwnPropertyDescriptor({x: 1}, "x")
{"value":1,"writable":true,"enumerable":true,"configurable":true}

// Get attributes of accessor property
js> Object.getOwnPropertyDescriptor(p, "r")
{"get":get r() {
    return Math.sqrt(this.x*this.x + this.y*this.y);
  },"set":set r(newValue) {
    var oldValue = Math.sqrt(this.x*this.x + this.y*this.y);
    var ratio = newValue / oldValue;
    this.x *= ratio;
    this.y *= ratio;
  },"enumerable":true,"configurable":true}
{{< /highlight >}}


Set property's attributes:

{{< highlight javascript>}}
// Start with an empty object
js> var o = {};

// Create a non-enumerable property x
js> Object.defineProperty(o, "x", {value: 1, writable: true, enumerable: false, configurable: true})
js> o.x
1
// Verify that x is not enumerable
js> Object.keys(o)
[]

// Set x to be non-writable
js> Object.defineProperty(o, "x", {writable: false})
// Verify that x cannot be written to
js> o.x = 2
js> o.x
1

// Since x is still configurable, we can change its value by this trick
js> Object.defineProperty(o, "x", {value: 2})
js> o.x
2
{{< /highlight >}}


# object attributes

Every object has **prototype**, **class**, and **extensible** attributes.

# prototype attribute

An object's *prototype* attribute specifies the object from which it inherits properties.

To get an object's prototype, use `Object.getPrototypeOf()`.

{{< highlight javascript>}}
js> var o = Object.create({x: 1})
js> o
{}
js> Object.getPrototypeOf(o)
{"x":1}
{{< /highlight >}}

# class attribute

Useless.

# extensible attribute

The *extensible* attribute of an object specifies whether new properties can be added to the object or not.








{{< highlight javascript>}}
{{< /highlight >}}

