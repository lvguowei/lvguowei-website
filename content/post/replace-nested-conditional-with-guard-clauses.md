+++
date = "2016-10-15T19:38:57+03:00"
title = "Replace Nested Conditional With Guard Clauses"
tags = ["refactoring"]
categories = ["programming"]
+++

I couldn't remember who said that one of his favorite refactoring techniques is *Replace Nested Conditional with Guard Clauses*. When I looked into it, it did put a smile on my face despite its simplicity.

This is NOT about coding aesthetics, this is all about clarity.

> A method has conditional behavior that does not make clear
> the normal path of execution.
> 
> *Use guard clauses for all the special cases.*

{{< highlight java >}}
double getPayAmount() {
  double result;
  if (isDead) {
    result = deadAmount();
  } else if (isSeparated) {
    result = separatedAmount();
  } else if (isRetired) {
    result = retiredAmount();
  } else {
    result = normalPayment();
  }
}
{{< / highlight >}}

{{< highlight java >}}
double getPayAmount() {
  if (isDead) return deadAmount();
  if (isSeparated) return separatedAmount(); 
  if (isRetired) return retiredAmount();
  
  return normalPayment();
  
}
{{< / highlight >}}

So much better!
