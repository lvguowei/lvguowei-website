---
date: "2016-05-05T15:33:59+03:00"
title: "Simple example of recursion"
tags: ["recursion", "scheme", "SICP", "functional programming"]
categories: ["SICP"]
keywords:
  - sicp
  - recursion
description: Learn more about recursion
featured: "featured-sicp.jpg"
featuredalt: "recursion"
featuredpath: "/img"
---

今天读到SICP里的一个介绍recursion的例子，用牛顿猜想来计算平方根。

首先，介绍了计算机程序和数学方程的区别。数学方程大多用的是描述法（declarative）。比如说这个平方根，数学上只需要说：

如果x的平方等于y，而且x大于0，那么x就是y的平方根。

这是一种很高层次的描述，通过描述，来限制答案的域。如果能直接用到计算机里，问题就简单了。大概写出来的程序就是这个样子：

{{< highlight javascript >}}
func sqrt(x):
 @ * @ = x;
 @ > 0;
 return @;
{{< /highlight >}}
让计算机去处理计算细节。这当然是一种理想的情况，如果都能这样写程序，那就完事大吉了。这就叫描述性编程(Declarative Programming)。

当然现在还做不到这么绝对，所以我们只能自己写如何进行细节的计算来得到我们的结果。这就叫做命令式变成(Imperative Programming)。

总体来说，描述性编程比命令式编程要容易理解的多，因为不用自己下达命令给计算机，只需要对问题进行描述，计算机自己会找到答案。典型的例子就是xml配置文件，人们把对系统的需求和配置写在一个单独的xml文件里面，然后让计算机自己去执行相应的命令。

好了，现在进入正题。牛顿猜想的原理如下：

要计算x的平方根，先猜想一个答案y，然后用


(y + x / y) / 2

来得到新的优化过猜想。反复进行，知道得到满意的猜想。

首先，我们先来定义一些辅助方程。

{{< highlight scheme >}}
(define (abs x)
  (if (< x 0)
      (- x)
      x))

(define (square x)
  (* x x))

(define (average x y)
  (/ (+ x y) 2))

{{< /highlight >}}

接下来我们来写主程序。

{{< highlight scheme >}}
(define (sqrt-iter guess x)
  (if (good-enough? guess x)
      guess
      (sqrt-iter (improve guess x) x)))
{{< /highlight >}}
	  
这个程序用第归的方法实现了牛顿猜想。

现在就来分别定义good-enough? 和 improve 两个方程。

{{< highlight scheme >}}
(define (good-enough? guess x)
  (< (abs (- (square guess) x)) 0.001))


(define (improve guess x)
  (average guess (/ x guess)))
{{< /highlight >}}

最后来定义入口方程。

{{< highlight scheme >}}
(define (sqrt x)
  (sqrt-iter 1.0 x))
{{< /highlight >}}

这就大功告成了。

下面我们来看看，如果不用第归（recursion），这个程序应该如何写。

{{< highlight java >}}
public int sqrt(int x) {
    int guess = 1.0;
	while (!good-enough(guess, x)) {
	    guess = improve(guess, x);
	}
	
	return guess;
}
{{< /highlight >}}

大多数人可能第一个会想到的就是这个实现，用一个loop来不停的更新guess，知道满意为止，然后返回guess。

下面就来说一说这两种实现的区别。

非第归实现的原理其实就是利用一个变量，通过不断改变变量的值来进行计算。
而第归的实现，没有变量！怎么会这样，确实，那么新的guess哪去了？注意，我们的确也计算了新的guess，但我们没有把它赋予任何一个变量，而是直接又用它调用了方程。也就是说，又产生了一个新的guess。每一次第归调用sqrt都会产生一个新的guess，而老的guess的值则留在了上一层调用里。

换句话说，第归调用没有赋值运算！所有的值都在内存里。这也就是为什么说第归费内存的原因。但现在内存这么便宜，费就费点儿吧。

既然都能算出结果，那么第归不第归有什么差别吗？这就要讲到Functional Programming的概念了。

Functional Programming 和 Object Oriented Programming的一个重要区别就是用来描述世界的模型不同。OO认为，任何东西都是一个对象，比如说一个房子，不管10年20年，变成什么样子，那个房子还是那个房子。但FP认为，不可能两次踏进同一个小溪。10年前的房子和现在的房子不是一个房子。

FP这样的模型有什么好处呢？很简单，任何东西，都被赋予了时间的意义，也就是说，有了历史的概念。当一个东西有了时间的属性，处理起来就容易多了，比如可以说这个十年前的房子是白色的，现在的房子是灰色的。用OO的话就很难描述这类问题，因为房子颜色的属性没有历史记录。如果在系统里看到房子是白色的，只可以断定房子现在是白色的，什么时候变成白色的谁也不知道。

