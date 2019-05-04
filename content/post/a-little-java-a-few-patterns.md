+++
author = "Guowei Lv"
categories = ["Java"]
description = "An interesting example from the book"
featured = ""
featuredpath = ""
title = "A Little Java a Few Patterns"
date = 2019-05-04T21:51:09+03:00
+++

I got the book [A Little Java, A Few Patterns](https://amzn.to/2LoLva4) not long ago and have been reading it since.

The teaching style of the book is refreshing. I do highly recommend this book if you want to learn about design patterns with a functional programming taste. Or if you simply love pizza.

{{< figure src="/img/a-little-java.jpg" >}}

The first half of the book is kind of easy for me since I already know Java and a few design patterns. But the complexity of the examples are building up quickly. There is this example of a program to evaluate expressions that I have to type the code into an IDE to understand fully, and I will put it here for later reference.

# Intention

First a few words about what the program does.

The program is to evaluate expressions.

There are two types of expressions, one with numbers and one with sets. And both expressions are built using the same set of operators: *plus(+)*, *diff(-)* and *prod(x)*.

For example:
{{< highlight scheme>}}
(+ 1 (- 10 (x 2 2)))
{{< /highlight >}}

is evaluated to 7.

But what about this:
{{< highlight scheme>}}
(+ {5, 7} (+ (- {4} {3}) {5}))
{{< /highlight >}}

Well, we just translate each + - x to set operators *union*, *minus* and *intersect*.

{{< figure src="/img/set-operators.png" >}}

So the result of evaluating the above expression is {7, 5}.

# The Program

First, we define what is an expression.

{{< highlight java>}}
abstract class Expr {
    abstract Object accept(ExprVisitor ask);
}
{{< /highlight >}}

Notice that there is no methods in this class because we are using [visitor pattern](https://en.wikipedia.org/wiki/Visitor_pattern) here.

Now we have the base type `Expr` we can define different subtypes of expressions.

First of all, the most basic expression is just a constant value.

{{< highlight java>}}
class Const extends Expr {
    private Object c;

    Const(Object c) {
        this.c = c;
    }

    @Override
    Object accept(ExprVisitor ask) {
        return ask.forConst(c);
    }
}
{{< /highlight >}}

Then, + - x are also expressions.

{{< highlight java>}}
class Plus extends Expr {
    private Expr l;
    private Expr r;

    Plus(Expr l, Expr r) {
        this.l = l;
        this.r = r;
    }

    @Override
    Object accept(ExprVisitor ask) {
        return ask.forPlus(l, r);
    }
}

public class Diff extends Expr {
    private Expr l;
    private Expr r;

    Diff(Expr l, Expr r) {
        this.l = l;
        this.r = r;
    }

    @Override
    Object accept(ExprVisitor ask) {
        return ask.forDiff(l, r);
    }
}


class Prod extends Expr {
    private Expr l;
    private Expr r;

    Prod(Expr l, Expr r) {
        this.l = l;
        this.r = r;
    }


    @Override
    Object accept(ExprVisitor ask) {
        return ask.forProd(l, r);
    }
}

{{< /highlight >}}

Next, we define the interface `ExprVisitor`:

{{< highlight java>}}
public interface ExprVisitor {
    Object forPlus(Expr l, Expr r);
    Object forDiff(Expr l, Expr r);
    Object forProd(Expr l, Expr r);
    Object forConst(Object c);
}
{{< /highlight >}}

This code says that any visitors used on `Expr`s, will have to deal with its four subtypes.

For each expression, what we want to do is to evaluate it. So let's create a visitor `Eval`:

{{< highlight java>}}
public abstract class Eval implements ExprVisitor {

    abstract Object plus(Object l, Object r);
    abstract Object diff(Object l, Object r);
    abstract Object prod(Object l, Object r);

    @Override
    public Object forPlus(Expr l, Expr r) {
        return plus(l.accept(this), r.accept(this));
    }

    @Override
    public Object forDiff(Expr l, Expr r) {
        return diff(l.accept(this), r.accept(this));
    }

    @Override
    public Object forProd(Expr l, Expr r) {
        return prod(l.accept(this), r.accept(this));
    }

    @Override
    public Object forConst(Object c) {
        return c;
    }
}
{{< /highlight >}}

`Eval` is still an abstract class. This is because how to evaluate numeric expressions is different from set expressions. In particular, as the code says, the differences are how to actually do + - and x.

Let's first define the `IntEval` for evaluating numeric expressions.

{{< highlight java>}}
class IntEval extends Eval {

    @Override
    Object plus(Object l, Object r) {
        return (Integer) l + (Integer) r;
    }

    @Override
    Object diff(Object l, Object r) {
        return (Integer) l - (Integer) r;
    }

    @Override
    Object prod(Object l, Object r) {
        return (Integer) l * (Integer) r;
    }
}

{{< /highlight >}}

This is easy. The code says we can just use plain numeric operations.

By now, we are able to evaluate the numeric expressions.

{{< highlight java>}}
(Integer) new Plus(new Const(1), new Const(2)).accept(new IntEval());
{{< /highlight >}}

Now let's implement the same for set expressions.

First, let's define the classes for representing set:

{{< highlight java>}}

abstract class Set {
    Set add(Integer i) {
        if (mem(i)) {
            return this;
        } else {
            return new Add(i, this);
        }
    }

    abstract boolean mem(Integer i);
    abstract Set plus(Set s);
    abstract Set diff(Set s);
    abstract Set prod(Set s);
}

class Empty extends Set {

    @Override
    boolean mem(Integer i) {
        return false;
    }

    @Override
    Set plus(Set s) {
        return s;
    }

    @Override
    Set diff(Set s) {
        return new Empty();
    }

    @Override
    Set prod(Set s) {
        return new Empty();
    }
}

class Add extends Set {
    private Integer i;
    private Set s;

    Add(Integer i, Set s) {
        this.i = i;
        this.s = s;
    }

    @Override
    boolean mem(Integer i) {
        if (i.equals(this.i)) {
            return true;
        } else {
            return s.mem(i);
        }
    }

    @Override
    Set plus(Set s) {
        return this.s.plus(s.add(i));
    }

    @Override
    Set diff(Set s) {
        if (s.mem(i)) {
            return this.s.diff(s);
        } else {
            return this.s.diff(s).add(i);
        }
    }

    @Override
    Set prod(Set s) {
        if (s.mem(i)) {
            return this.s.prod(s).add(i);
        } else {
            return this.s.prod(s);
        }
    }
}

{{< /highlight >}}

The interesting part here is probably `Add`. Let's see how a set is constructed:

First we start with an empty set:

{}

Let's add 1 to it:

(1 {})

Let's add 2 to it:

(2 (1 {}))

So on and so forth.

So you can see this is not the most *correct* way of implementing a set, but it only serves the purpose of teaching.

Then, the `SetEval` becomes a no brainer.

{{< highlight java>}}
class SetEval extends Eval {

    @Override
    Object plus(Object l, Object r) {
        return ((Set)l).plus((Set)r);
    }

    @Override
    Object diff(Object l, Object r) {
        return ((Set)l).diff((Set)r);
    }

    @Override
    Object prod(Object l, Object r) {
        return ((Set)l).prod((Set)r);
    }
}

{{< /highlight >}}

Now we can try to evaluate a set expression:

{{< highlight java>}}
(Set) new Prod(new Const(new Empty().add(7)), new Const(new Empty().add(3))).accept(new SetEval());
{{< /highlight >}}
