+++
date = "2016-10-15T09:40:46+03:00"
title = "Unidirectional to Bidirectional"
categories = ["Refactoring"]
+++

It is very common in relational database that we have the following structure:
A customer table has columns: customer-id, name. 
And an order table has columns: order-id, customer-id, order-date.

See that there is a customer-id in the order table, so that we can use it to get orders belongs to
a certain customer. But there is no knowledge about the orders in customer table.

This is fine with database, but if we try to map this directly to java classes, we may end up with something like this:

{{< highlight java >}}
class Customer {
  private String id;
  private String name;
  
  // ...
}

class Order {
  private String id;
  private Customer customer;
  private long createdAt;
  
  // ...
}
{{< / highlight >}}

But what if we want to get all orders of a customer? It would be very convinient if there is a
orders field in the customer, isn't it?

Well, this is all this refactoring all about.

But pay attention how to keep the data synced.

Here is the code: 
[Sample Project](https://github.com/lvguowei/refactoring/tree/master/unidirectionalToBidirectional "Github")
