+++
date = "2016-05-29T21:32:14+03:00"
title = "Time and Space of Recursion"
tags = ["recursion", "scheme", "SICP", "functional programming"]
categories = ["SICP"]
+++

今天通过一个例子，来谈一谈第归的时间和空间效应。

例子很简单，计算 a 的 n 次方。

我们来对比一下两种第归算法的时间和空间的消耗。

第一个方法很直接。思想就是要计算 a 的 n 次方， 只要计算 a 的 (n - 1) 次方， 然后结果在乘以 a 。如下：

{{< highlight scheme >}}

(define (expt a n)
  (if (= n 0)
      1
      (* a (expt a (- n 1)))))

{{< /highlight >}}

为了更直观的理解这个算法，我们来计算一个例子， 2 的 3 次方。

{{< highlight scheme >}}

(expt 2 3)

(* 2 (expt 2 2))

(* 2 (* 2 (expt 2 1)))

(* 2 (* 2 (* 2 (expt 2 0))))

(* 2 (* 2 (* 2 1)))

(* 2 (* 2 2))

(* 2 4)

8

{{< /highlight >}}

我们能能明显看到这个算法的“形状”是一个三角形。每一行代表一次计算，也可以理解成时间的消耗。而每一列则代表计算机需要记住的内容，比如说一共要乘以几个2，也可以理解成空间的消耗。我们可以看到，随着 n 的增大， 这个算法的时间和空间消耗也随之增大。而且增大的速度都是线性的。时间的消耗随着 n 的增大而增大很好理解， n 大了， 计算花的时间也相应长了。但是空间消耗可不可以不增大呢？下面我们就来看另一种算法。

{{< highlight scheme >}}

(define (expt-iter b counter product)
  (if (= counter 0)
      product
      (expt-iter b (- counter 1) (* product b))))

(define (expt2 b n)
  (expt-iter b n 1))

{{< /highlight >}}

这个算法和前一个很不一样，它引入了两个临时变量一样的值， 一个 counter 一个 product。我们一样来计算一下前面的例子。

{{< highlight scheme >}}

(expt-iter 2 3 1)
(expt-iter 2 2 (* 1 2))
(expt-iter 2 2 2)
(expt-iter 2 1 (* 2 2))
(expt-iter 2 1 4)
(expt-iter 2 0 (* 4 2))
(expt-iter 2 0 8)
8

{{< /highlight >}}

首先我们看到的是这个算法的“形状”和上一个很不一样，程直线型，也就是说空间消耗并没有随着 n 的增大而增大。
等一下，这个模式好像在哪见过， 没错，就是普通的for循环啊！
