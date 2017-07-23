+++
date = "2016-10-15T05:35:31+03:00"
title = "Duplicate Observed Data"
tags = ["refactoring", "Android"]
categories = ["Refactoring"]
+++

This is from the famous Refactoring book by Martin Fowler. When I was reading it, it feels very similar to the very popular MVP or MVVM.

The key idea is that in system that has user interface, the business logic should be separated from the user interface.

One example I can think of is the registration form, where there are input fields like username, email, phone number and password.
We can have some logic that disable the Register button until all fields are filled and the phone number and email valid.
We can of course put everything in the UI (probably an Activity or Fragment in Android), but it doesn't feel very elegant.
So we create a new RegisterModel class, create 4 private String fields - username, email, phone number and password.
Then we create getter and setter for these fields. When in the Activity the user changed one of the fields, we directly call the 
corresponding setter function in the model, and the model can do the calculation and validation and whatnot, then use some PubSub
mechanism to notify the view.

I created an Android project out of the example in the book.


[Android Example](https://github.com/lvguowei/refactoring/tree/master/DuplicateObservedData "Github")
