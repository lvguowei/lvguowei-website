+++
date = "2016-05-08T19:59:59+03:00"
title = "C++ Primer Exercise Answer (Section 2.5.2)"
tags = ["c++ primer", "c++"]
categories = ["programming"]
+++

{{< highlight cpp >}}

#include <iostream>

using namespace std;

int main(int argc, char *argv[])
{
    int i = 0, &r = i;
    auto a = r;
    const int ci = i, &cr = ci;
    auto b = ci;
    auto c = cr;
    auto d = &i;
    auto e = &ci;
    const auto f = ci;
    auto &g = ci;
    //auto &h = 42;
    const auto &j = 42;


    a = 42; // ok
    b = 42; // ok
    c = 42; // ok
    d = 42; // no, d is a pointer
    e = 42; // no, e is a pointer
    g = 42; // no, g is a reference cannot rebind

    return 0;
}

{{< /highlight >}}
