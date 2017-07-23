+++
date = "2016-05-15T20:46:45+03:00"
title = "Old and New For Loop in C++"
tags = [ "cpp", "Cpp Primer" ]
categories = ["C++ Primer"]
+++

The difference between the subscription based for loop and the newly introduced range based loop may be a bit confusing.
Let's clear things up.

Here are some sample code:

{{< highlight cpp >}}

void testFor() {

    cout << "--- For loop ---" << endl;

    string s("hello");

    // subscription can modify
    for (string::size_type i = 0; i < s.size (); i++) {
        s[i] = toupper (s[i]);
    }

    cout << s << endl;

    vector<int> v {1, 2, 3};

    // subscription can modify
    for (vector<int>::size_type i = 0; i < v.size (); i++) {
        v[i] = v[i] + 1;
    }

    for (auto i : v) {
        cout << i << endl;
    }
}

void testRangeFor() {

    cout << "--- Range For ---" << endl;

    string s("hello");

    // i is a value copy of the corresponding char
    for (auto i : s) {
        i = toupper (i);
    }

    // so s doesn't change
    cout << s << endl;

    // i is a reference of the corresponding char
    for (auto & i : s) {
        i = toupper (i);
    }

    // now s has been changed
    cout << s << endl;

    vector<int> v {1, 2, 3};

    for (auto i : v) {
        i = i + 1;
    }

    for (auto i : v) {
        cout << i << " ";
    }

    cout << endl;

    for (auto & i : v) {
        i = i + 1;
    }

    for (auto i : v) {
        cout << i << " ";
    }
}

{{< / highlight >}}

Basically, you can modify the item by subscription based for loop, but you cannot directly modify item by using ranged for loop. But you can use a reference type in ranged based for loop if you want to modify the item in the container.
