+++
author = "Guowei Lv"
categories = ["Design and Architecture"]
keywords = ["singleton pattern", "design pattern", "GoF", "enum singleton"]
description = "A Brief Summary on Different Flavors of Singleton Pattern"
featured = "singleton.jpg"
featuredpath = "/img"
title = "Singleton Pattern Inside and Out"
date = 2018-08-20T21:38:04+03:00
+++

Singleton Pattern is so popular that if you know only one design pattern it will probably be it. But do you know it inside out?

There are different flavors of this pattern, let's examine them one by one.

# Dummy Singleton

This is the simplest implementation of Singleton Pattern. Just use a `static final` field in the class to store an instance of the class, and make all constructors `private`. See the example below:

{{< highlight java >}}
public class GodFigure {
    private static final GodFigure instance = new GodFigure();

    private GodFigure() {
    }

    public static GodFigure getInstance() {
        return instance;
    }
}
{{< /highlight >}}

# Synchronized Lazy Singleton

Sometimes we want to delay the creation of the Singleton object to its first access, to save some resources(I doubt this and you should too), like the code below. But in order for it to work in a multi-threaded environment, we need to add some synchronization.

{{< highlight java >}}
public class GodFigure {
    private static GodFigure instance;

    private GodFigure() {
    }

    public static synchronized GodFigure getInstance() {
        if (instance == null) {
            instance = new GodFigure();
        }
        return instance;
    }
}
{{< /highlight >}}

Notice the `synchronized` keyword in the `getInstance()` method. This does make it correct but we have to realize that this also makes calling it more expensive. Is there a way to avoid to synchronize the whole method?

# Double Check Lock Singleton

This is when programmers trying to be smart. Let me present the full code first.

{{< highlight java >}}
public class GodFigure {
    private static volatile GodFigure instance;

    private GodFigure() {
    }

    public static GodFigure getInstance() {
        if (instance == null) {
            // @A
            synchronized (GodFigure.class) {
                if (instance == null) {
                    instance = new GodFigure();
                }
            }
        }
        return instance;
    }
}
{{< /highlight >}}

The general idea here is to avoid synchronize on the whole function, which is expensive. Instead, only synchronize the first time creation of the instance. Smart, right? Here are several things that need explanation.

First, the double null checking, especially the **second null checking** inside the synchronized block.

The first null checking is not surprising, when the instance has been created already, just return it. But the second null checking needs us to spend some brain cycles on it.

Imagine there are two threads **T1** and **T2**, they are now both at **@A**. Then **T1** gets the lock and goes into the synchronized block and creates the instance and releases the lock. Now **T2** gets the turn to execute, it will also go into the synchronized block, now you should know why the null checking here is important. If without it, **T2** would create the instance again.

Second, the **volatile** keyword.

This needs even more explanation. Let's begin from `instance = new GodFigure();`. This looks like a one atomic operation, but actually it is not. This can be roughly split into 3 steps.

(1) Make space for `instance` in memory.

(2) Call `GodFigure` constructor function to initialize the `instance` object.

(3) Sets up variable `instance` to point to the initialized object in memory. (Now `instance` will not be `null`)

The problem is that sometimes step 3 can happen before step 2. In other words, the compiler may do smart stuff so the ordering of step 2 and 3 cannot be guaranteed. What would happen if the ordering change? Imaging that thread **T1** is at step 1 then 3, now even though the instance is not yet initialized it is not null anymore. So if at this time another thread **T2** enters the function, and checks that `instance` is not null, it will return the partially created instance, which can cause problems. So the keyword **volatile** solves the problem by actually dictates that the step 2 and 3 cannot change position.

I personally think this has gone too far for such a simple task. So are there simpler solutions?

# Static Inner Class Singleton

This is a better way to implement lazy singleton. It uses a static inner class to hold the singleton instance.


{{< highlight java >}}

public class GodFigure {
    private GodFigure() {

    }

    public static GodFigure getInstance() {
        return Holder.instance;
    }

    private static class Holder {
        private static final GodFigure instance = new GodFigure();
    }
}
{{< /highlight >}}

This looks pretty dumb at first, why create this `Holder` class just to hold one instance? Actually, the static inner class is the key to the laziness. JVM makes it that the `GodFigure` instance will only gets created when the first time the `getInstance()` gets called. And it is thread safe. Pretty neat! (I think I have seen this kind of holder singleton once in my career and I clearly remember that I thought this was a joke, silly me...)

# Enum Singleton

If you are a hard core Java developer, you should have read (or plan to read) the famous **Effective Java**. In the book, it is described as the best known way to implement singleton!

{{< highlight java >}}

public enum GodFigure {
    INSTANCE;

    public void say() {
        // say something...
    }
}
{{< /highlight >}}

>It is more concise, provides the serialization machinery for free, and provides an ironclad guarantee against multiple instantiation, even in the face of sophisticated serialization or reflection attacks. 

--- Effective Java


*P.S. Recently there are some people saying that Singleton Pattern is an anti-pattern. The reason is that it makes the code that uses it not testable. It is true that if you use `getInstance()` everywhere, the code cannot be tested unless you use some powerful mocking tools to replace the Singleton class when run the tests.*

*What should be done, is to use dependency injection to inject the singleton into the classes that use it. But again, the this just pushes the problem to the dependency injection framework. For example Dagger 2 has a `@Singleton` annotation to make sure that there is only one object in the system.*

*So the problem of "only having one instance of some object" still remains a valid problem. Just that we should constraint its usage to a smaller scale, like in the DI, and use just DI in the rest of the system.*
