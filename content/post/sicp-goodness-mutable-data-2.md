+++
categories = ["SICP"]
keywords = ["SICP", "structure and interpretation of computer programs"]
description = "Modeling with mutable data"
featured = "featured-sicp.jpg"
featuredpath = "/img"
author = "Guowei Lv"
title = "SICP Goodness - Mutable Data (II)"
date = 2018-10-26T19:55:26+03:00
+++


>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

# Representing Tables

We can build a table data structure by just using pairs.

For example, the box-and-pointer diagram of the folllowing table would be:

{{< highlight scheme >}}
a: 1

b: 2

c: 3
{{< /highlight >}}

{{< figure src="/img/representing-table-1.jpg" >}}

The head of the table list is a meaningless pair with symbol *table* as car. This is used for a fixed place to insert new key/value pair as we will see later.

# Lookup

Procedure `lookup` looks up value by key from the table.

{{< highlight scheme >}}
(define (lookup key table)
  (let ((record (assoc key (cdr table))))
    (if record
        (cdr record)
        false)))

(define (assoc key records)
  (cond ((null? records) false)
        ((equal? key (caar records)) (car records))
        (else (assoc key (cdr records)))))
{{< /highlight >}}


# Insert

To insert a value in the table under a specific key. First check if the table contains such key, if yes, we set the new value under it. If not, we create a new record and put it as the first item  the table list after the *table* head.

{{< highlight scheme >}}
(define (insert! key value table)
  (let ((record (assoc key (cdr table))))
    (if record
        (set-cdr! record value)
        (set-cdr! table (cons (cons key value) (cdr table))))))
{{< /highlight >}}

# Create a Table

This is easy, just create a list containing only item \*table\*.

{{< highlight scheme >}}
(define (make-table)
  (list '*table*))
{{< /highlight >}}

# Two dimentional Table

In a two-dimentional table, each value is indexed by two keys.

For example:

{{< highlight scheme >}}
math:
  +: 43
  -: 45
  *: 42
letters:
  a: 97
  b: 98
{{< /highlight >}}

The box-and-pointer diagram of it would be:

{{< figure src="/img/representing-table-2.jpg" >}}

# Lookup

Lookup is similar to the one-dimentional table, just do a nested `assoc` instead.

{{< highlight scheme >}}

(define (look-up key-1 key-2 table)
  (let ((subtable (assoc key-1 (cdr table))))
    (if subtable
        (let ((record (assoc key-2 (cdr subtable))))
          (if record
              (cdr record)
              false))
        false)))
{{< /highlight >}}

# Insert

This is also not hard, just need to be a bit careful.

{{< highlight scheme >}}

(define (insert! key-1 key-2 value table)
  (let ((subtable (assoc key-1 (cdr table))))
    (if subtable
        (let ((record (assoc key-2 (cdr subtable))))
          (if record
              (set-cdr! record value)
              (set-cdr! subtable
                        (cons (cons key-2 value)
                              (cdr subtable)))))
        (set-cdr! table
                  (cons (list key-1 (cons key-2 value))
                        (cdr table)))))
  'ok)

{{< /highlight >}}

# Creating local tables

Up until now, we are kind of working like C style, writing functions that operate on table data structures.

We can actually work more like OO style very easily by representing table as an *object*, and send messages to it.

{{< highlight scheme >}}

(define (make-table)
  (let ((local-table (list '*table*)))
    (define (lookup key-1 key-2)
      (let ((subtable 
             (assoc key-1 (cdr local-table))))
        (if subtable
            (let ((record 
                   (assoc key-2 
                          (cdr subtable))))
              (if record (cdr record) false))
            false)))
    (define (insert! key-1 key-2 value)
      (let ((subtable 
             (assoc key-1 (cdr local-table))))
        (if subtable
            (let ((record 
                   (assoc key-2 
                          (cdr subtable))))
              (if record
                  (set-cdr! record value)
                  (set-cdr! 
                   subtable
                   (cons (cons key-2 value)
                         (cdr subtable)))))
            (set-cdr! 
             local-table
             (cons (list key-1
                         (cons key-2 value))
                   (cdr local-table)))))
      'ok)
    (define (dispatch m)
      (cond ((eq? m 'lookup-proc) lookup)
            ((eq? m 'insert-proc!) insert!)
            (else (error "Unknown operation: 
                          TABLE" m))))
    dispatch))

{{< /highlight >}}

By doing this, we can do things like:

{{< highlight scheme >}}

(define operation-table (make-table))
(define get (operation-table 'lookup-proc))
(define put (operation-table 'insert-proc!))

{{< /highlight >}}

P.S. Now with the knowledge of tables, we can introduce the **data directed programming** next. Stay tuned!
