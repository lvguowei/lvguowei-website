+++
date = "2016-04-30T10:07:07+03:00"
title = "C++ Primer Answers"
tags = [ "cpp", "Cpp Primer" ]
categories = ["C++ Primer"]
+++

<p align="center">
<img src="/img/brown.jpeg" width="300">
</p>

<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#orgheadline13">1. C++ Primer Exercises Answers</a>
<ul>
<li><a href="#orgheadline4">1.1. Section 2.3.1</a>
<ul>
<li><a href="#orgheadline1">1.1.1. Exercise 2.15</a></li>
<li><a href="#orgheadline2">1.1.2. Exercise 2.16</a></li>
<li><a href="#orgheadline3">1.1.3. Exercise 2.17</a></li>
</ul>
</li>
<li><a href="#orgheadline8">1.2. Section 2.3.2</a>
<ul>
<li><a href="#orgheadline5">1.2.1. Exercise 2.18</a></li>
<li><a href="#orgheadline6">1.2.2. Exercise 2.20</a></li>
<li><a href="#orgheadline7">1.2.3. Exercise 2.21</a></li>
</ul>
</li>
<li><a href="#orgheadline10">1.3. Section 2.4.2</a>
<ul>
<li><a href="#orgheadline9">1.3.1. Exercise 2.27</a></li>
</ul>
</li>
<li><a href="#orgheadline12">1.4. Section 2.4.3</a>
<ul>
<li><a href="#orgheadline11">1.4.1. Exercise 2.30</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</div>
</div>

# C++ Primer Exercises Answers<a id="orgheadline13"></a>

## Section 2.3.1<a id="orgheadline4"></a>

### Exercise 2.15<a id="orgheadline1"></a>

{{< highlight cpp >}}
int ival = 1.01; // correct
int &rval1 = 1.01; // wrong, type error
int &rval2 = ival; // correct
int &rval3; // not initialized
{{< / highlight >}}
### Exercise 2.16<a id="orgheadline2"></a>

{{< highlight cpp >}}
int i = 0, &r1 = i; 
double d = 0, &r2 = d;
    
r2 = 3.14159; // correct
r2 = r1; // correct
i = r2; // correct
r1 = d; // correct
{{< / highlight >}}
### Exercise 2.17<a id="orgheadline3"></a>

{{< highlight cpp >}}
int i, &ri = i;
i = 5;
ri = 10;
std::cout << i << " " << ri << std::endl;
{{< / highlight >}}
## Section 2.3.2<a id="orgheadline8"></a>

### Exercise 2.18<a id="orgheadline5"></a>

{{< highlight cpp >}}
int a = 1;
int b = 2;
int *p = &a;
p = &b;
*p = 3;
std::cout << *p;
{{< / highlight >}}
### Exercise 2.20<a id="orgheadline6"></a>

{{< highlight cpp >}}
int i = 42;
int *p1 = &i;
*p1 = *p1 * *p1;
std::cout << i;
{{< / highlight >}}
### Exercise 2.21<a id="orgheadline7"></a>

{{< highlight cpp >}}
int i = 0;
double *dp = &i; // error
int *ip = i; // error
int *p = &i; // correct
{{< / highlight >}}
## Section 2.4.2<a id="orgheadline10"></a>

### Exercise 2.27<a id="orgheadline9"></a>

{{< highlight cpp >}}
int a = -1, &b = 0; // wrong, reference to a constant 0
int c = 9, *const d = &c; // correct
const int e = -1, &f = 0; // correct
const int *const g = &a; // correct
const int *h = &a; // correct
const int &const i; // wrong
const int z = a, &y = a; // correct
{{< / highlight >}}
## Section 2.4.3<a id="orgheadline12"></a>

### Exercise 2.30<a id="orgheadline11"></a>

{{< highlight cpp  >}}
int i = 9;
const int v2 = 0;
int v1 = v2;
int *p1 = &v1, &r1 = v1;
const int *p2 = &v2, *const p3 = &i, &r2 = v2;

r1 = v2; // correct
p1 = p2; // wrong
p2 = p1; // correct
p1 = p3; // wrong
p2 = p3; // correct
{{< / highlight >}}
