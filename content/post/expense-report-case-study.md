+++
date = "2016-12-06T12:16:53+02:00"
title = "Expense Report Case Study"
tags = [ "Design Pattern", "SOLID Principle", "uncle bob", "refactoring" ]
categories = ["programming"]
+++

One day, I was watching another Uncle Bob's video (yes, they are addictive), when I see one example he gave when talking about open closed principle, it ringed a bell in my head. This looked familiar! The `type` in some data classes, some `switch`s or `if`s, some `&&`s and `||`s all dancing around in the class. I can almost hear them teasing: "Come and catch me! Come and catch me!".

I think OK, it's time to get this fixed.

Now I present you the messy smelly piece of ssssss.....source code as the original design(with some tests amazingly).

[version 0](https://github.com/lvguowei/expense-report-case-study/commit/57e89c5a3d464e7f7ca6aca3d63b8a51c40b29ee)

Very easily, we can extract out some functions like `isMeal` and `isOverage`.

[version 1](https://github.com/lvguowei/expense-report-case-study/commit/51c0c79ab143859fd2ddce898e7f8daf87ff6aa5)

Now you may smell feature envy as I did, so let's move those methods into `Expense` class.

[version 2](https://github.com/lvguowei/expense-report-case-study/commit/131ac2d05c8912e7a1cab773acafb0470a80a5fe)

Next, replace `type` with polymorphism, which means create more subclasses of `Expense`.

[version 3](https://github.com/lvguowei/expense-report-case-study/commit/68a3d85e5b1bc1c4b5a1e0dbb38012551894c200)

We can now separate printing logic out of the `Expense` class and into some `ExpenseReporter` and `ReportNamer` class.

You may ask, why do we need the `ExpenseNamer` abstraction, why not just put some `getName` method into the `Expense` class.

To anwser your question, consider this: different actors may need different names for the same Expense. The reporter may need totally different names than the UI.

[version 4](https://github.com/lvguowei/expense-report-case-study/commit/1e4d81b10aec46df4f05dbac40171ee2b3b779aa)

We are at the end of this fantastic soothing and comforting refactoring session habibi, enjoy life and beautiful code.

{{< figure src="/img/expense.png" >}}
