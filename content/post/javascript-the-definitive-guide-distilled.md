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

# array

Remember that arrays are a specialized kind of object. The square brackets used to access array elements work just like the square brackets used to access object properties.
JavaScript converts the numeric array index you specify to a string.

What is special about arrays is that when you use property names that are non-negative integers less than 2^32, the array automatically maintains the value of the **length** property for you.

# sparse array
{{< highlight javascript>}}
js> var a = [];
undefined
js> a
[]
js> a[1000] = 0;
0
js> a.length
1001
{{< /highlight >}}

# array length

If you set the **length** property to a non-negative integer **n** smaller than its current value, any array elements whose index is greater than or equal to **n** are deleted from the array:

{{< highlight javascript>}}
js> a = [1,2,3,4,5]
js> a.length = 3
js> a
[1, 2, 3]
{{< /highlight >}}

# array methods


{{< highlight javascript>}}
// Array.join()

js> var a = [1, 2, 3];
js> a.join();
"1,2,3"
js> a.join(" ");
"1 2 3"
js> a.join("");
"123"

// Array.reverse()

js> var a = [1, 2, 3];
js> a.reverse()
js> a
[3, 2, 1]

// Array.sort()
js> var a = ["banana", "cherry", "apple"];
js> a.sort()
js> a
["apple", "banana", "cherry"] // alphabetic order
js> var a = [33, 4, 1111, 222];
js> a.sort()
js> a
[1111, 222, 33, 4] // alphabetic order
js> a.sort(function(a, b) {return a - b;});
[4, 33, 222, 1111] // numerical order

// Array.concat()

js> var a = [1, 2, 3];
js> a.concat(4, 5);
[1, 2, 3, 4, 5]
js> a
[1, 2, 3]
js> a.concat([4, 5]);
[1, 2, 3, 4, 5]
js> a.concat([4, 5], [6, 7]);
[1, 2, 3, 4, 5, 6, 7]
js> a.concat(4, [5, [6, 7]])
[1, 2, 3, 4, 5, [6, 7]] // does not recursively flatten array of array

// Array.slice()

js> var a = [1, 2, 3, 4, 5];
js> a.slice(0, 3);
[1, 2, 3]
js> a.slice(3);
[4, 5]
js> a.slice(1, -1);
[2, 3, 4]
js> a.slice(-3, -2);
[3]

// Array.splice()
// The first argument specifies the array position at which the insertion / deletion is to begin.
// The second argument specifies the number of elements that should be deleted from the array.

js> var a = [1,2,3,4,5,6,7,8]
js> a.splice(4)
[5, 6, 7, 8]
js> a
[1, 2, 3, 4]
js> a.splice(1, 2)
[2, 3]
js> a
[1, 4]
js> a.splice(1, 1)
[4]
js> a
[1]

// These arguments may be followed by any number of additional arguments 
// that specify elements to be inserted into the array, 
// starting at the position specified by the first argument
js> var a = [1,2,3,4,5]
js> a.splice(2, 0, 'a', 'b')
[]
js> a
[1, 2, "a", "b", 3, 4, 5]
js> a.splice(2, 2, [1, 2], 3)
["a", "b"]
js> a
[1, 2, [1, 2], 3, 3, 4, 5]

// push() and pop()
// push addes elements at the end of the array and returns the new length
// pop removes elements at the end of the array and returns the value removed
js> var stack = []
undefined
js> stack.push(1, 2)
2
js> stack
[1, 2]
js> stack.pop()
2
js> stack
[1]
js> stack.push([4, 5])
2
js> stack
[1, [4, 5]]

// unshift() and shift()
// like push() and pop(), except that they insert / remove items from the beginning of the array.

var a = [];           // []
a.unshift(1);         // [1]
a.unshift(22);        // [22, 1]
a.shift();            // [1]
a.unshift(3, [4, 5]); // [3, [4, 5], 1]

{{< /highlight >}}

# forEach()

{{< highlight javascript>}}
js> var data = [1,2,3,4,5];
js> var sum = 0;
js> data.forEach(function(value) { sum += value;});
js> sum
15

js> data.forEach(function(v, i, a) {a[i] = v + 1;});
js> data
[2, 3, 4, 5, 6]
{{< /highlight >}}

# map()

{{< highlight javascript>}}
js> var a = [1, 2, 3];
js> var b = a.map(function(x) { return x * x;});
js> b
[1, 4, 9]
js> a
[1, 2, 3] // map() returns a new array
{{< /highlight >}}

# filter()

{{< highlight javascript>}}
js> var a = [1, 2, 3];
js> var smallValues = a.filter(function(x) { return x < 3; });
js> smallValues
[1, 2]
js> a
[1, 2, 3] // filter() returns a new array
{{< /highlight >}}

# every() and some()

{{< highlight javascript>}}
js> var a = [1, 2, 3, 4, 5];
js> a.every(function(x) { return x < 10; });
true
js> a.some(function(x) { return x % 2 === 0; });
true
{{< /highlight >}}


# reduce() and reduceRight()

{{< highlight javascript>}}
js> var a = [1, 2, 3, 4, 5];
js> var sum = a.reduce(function(accumulator, value) { return accumulator + value; }, 0);
js> sum
15
{{< /highlight >}}

{{< highlight javascript>}}
js> var a = [2, 3, 4];
js> var big = a.reduceRight(function(accumulator, value) { return Math.pow(value, accumulator); });
js> big // calculate 2 ^ (3 ^ 4)
2.4178516392292583e+24
{{< /highlight >}}


# indexOf() and lastIndexOf()
{{< highlight javascript>}}
js> var a = [0, 1, 2, 1, 0];
js> a.indexOf(1); // search from the beginning
1
js> a.lastIndexOf(1); // search from the end
3
js> a.indexOf(3); // not found returns -1
-1
{{< /highlight >}}

# function defination express V.S. function declaration statement

{{< highlight javascript>}}
function add(a, b) {
  return a + b;
}

var x = add(1, 2);

function add(a, b) {
  return -1;
}

console.log(x); // this will print -1

// all the function declarations are hoisted to the top level
{{< /highlight >}}

But function defination expression behaves more logical in this case:
{{< highlight javascript>}}
var add = function(a, b) {
  return a + b;
}

console.log(add(1, 2)); // 3

add = function(a, b) {
  return -1;
}

console.log(add(1, 2)); // -1
{{< /highlight >}}
