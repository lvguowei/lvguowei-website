+++
author = "Guowei Lv"
categories = ["Javascript"]
keywords = ["javascript", "prototype pattern", "design pattern", "prototypal inheritance"]
description = "Is Javascript's prototypal inheritance borrowed from prototype design pattern?"
featured = "js.jpg"
featuredpath = "/img"
title = "JavaScript and Prototype Design Pattern"
date = 2018-05-03T21:32:16+03:00
+++

Javascript has prototypal inheritance.

For example let's create a constructor function:

{{< highlight js >}}

function Person(firstname, lastname) {
  this.firstname = firstname;
  this.lastname = lastname;
}

Person.prototype = {
  fullname: function() {
    return this.firstname + ' ' + this.lastname;
  }
};

var bob = new Person("Bob", "Doe");
console.log(bob.fullname());

{{< /highlight >}}


This is very similar to the [Prototype Pattern](https://en.wikipedia.org/wiki/Prototype_pattern). Whenever we want a new object, we always create it out of some prototype object.

I find that it is easier to understand the JS's prototype inheritance when comparing it with the Prototype Pattern.

This also shows a powerful learning technique: whenever you can relate something that you don't know with something that you know, it become much easier to grasp the new thing.

The similarity is uncanny, did JS borrowed this pattern's idea?
