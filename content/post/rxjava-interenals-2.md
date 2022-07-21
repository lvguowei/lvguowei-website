+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "About Disposable"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Demystify RxJava (2)"
date = 2022-07-19T21:21:01+03:00
+++

In this article, we focus on how the dispose system works in RxJava.

First, let's take a look at the `Disposable` interface.


{{< highlight java>}}
public interface Disposable {
    void dispose();
    boolean isDisposed();
}
{{< /highlight >}}

Not much going on here, basically it says a `Disposable` can be disposed.

First example we are going to examine is:

{{< highlight java>}}
Observable.interval(1, TimeUnit.SECONDS)
{{< /highlight >}}

If you click through the code, the implementation is actually this class `ObservableInterval`.

First let's look at the constructor of this class:

{{< highlight java>}}
public final class ObservableInterval extends Observable<Long> {
    final Scheduler scheduler;
    final long initialDelay;
    final long period;
    final TimeUnit unit;

    public ObservableInterval(long initialDelay, long period, TimeUnit unit, Scheduler scheduler) {
        this.initialDelay = initialDelay;
        this.period = period;
        this.unit = unit;
        this.scheduler = scheduler;
    }
    ...
{{< /highlight >}}

Typical constructor stuff, sets up some internal fields.

The interesting thing happens when it is subscribed, and this function will get run:

{{< highlight java>}}

/** Simplified */
@Override
public void subscribeActual(Observer<? super Long> observer) {
    // what is this IntervalObserver???
    IntervalObserver is = new IntervalObserver(observer);
    // Well, it must implement Disposable if it can be passed to `onSubscribe()`
    observer.onSubscribe(is);

    // OK, the work is scheduled here
    Scheduler sch = scheduler;
    Disposable d = sch.schedulePeriodicallyDirect(is, initialDelay, period, unit);
    // What is setResource() and why we need to do this here?
    is.setResource(d);
}
{{< /highlight >}}

Well, it is only a few lines but are quite dense and a lot of things that we don't understand yet.

Let's start with the `IntervalObserver` class, and here is the signature:

{{< highlight java>}}
static final class IntervalObserver
    extends AtomicReference<Disposable>
    implements Disposable, Runnable {
{{< /highlight >}}

Wow, a lot is going on here. Let's look at them one by one.

First, it extends `AtomicReference<Disposable>`. This means that this class contains a reference to a Disposable object,
and the reference can be changed to pointing to another object at runtime, and all operations are thread safe.
This behavior is actually important, we will see why later.

Second, it implements `Disposable`. Wait, we just said that it is a reference to a `Disposable`, but now it is itself a `Disposable` as well, isn't this
kind of confusing??? Actually, this is a typical `Proxy` or `Deligate` pattern, just in reality, if no one points you in that direction, it is very hard
to wrap your head around such code. But now you know, so no big deal.

Last, it is also a `Runnable`. We notice that the `InterevalObserver` object is also passed into the `Scheduler`, so it is no surprise that it implements
the `Runnable` interface.

Notice that even though the class has `Observer` at the end of the name, but it does not implement the `Observer` from RxJava. Keep this in mind!

Now, we have a rough idea of things, let's dig deeper.

Let's first look at the methods related to `Disposable` interface.

{{< highlight java>}}
@Override
public void dispose() {
    DisposableHelper.dispose(this);
}

@Override
public boolean isDisposed() {
    return get() == DisposableHelper.DISPOSED;
}
{{< /highlight >}}

It delegates the work to this `DisposableHelper` class, let's look at that then:

{{< highlight java>}}
/**
 * Atomically disposes the Disposable in the field if not already disposed.
 * @param field the target field
 * @return true if the current thread managed to dispose the Disposable
 */
public static boolean dispose(AtomicReference<Disposable> field) {
    Disposable current = field.get();
    Disposable d = DISPOSED;
    if (current != d) {
        current = field.getAndSet(d);
        if (current != d) {
            if (current != null) {
                current.dispose();
            }
            return true;
        }
    }
    return false;
}
{{< /highlight >}}

Let's see two cases:
1. When the `AtomicReference field` is not set yet, so that the underlying `disposable` it refers to is null. 
In such case, the `field` will simply be set to a `DISPOSED` constant.
2. When the `AtomicReference field` has a underlying `disposable`, and it is not the `DISPOSED` constant.
In such case, the `field` will be set to `DISPOSED`, and the previously referenced `disposable` will be disposed.

So basically this helper function knows how to correctly dispose a `AtomicReference<Disposable>`.

Let's now look at another important helper function, `setOnce()`:

{{< highlight java>}}
/**
 * Atomically sets the field to the given non-null Disposable and returns true
 * or returns false if the field is non-null.
 * If the target field contains the common DISPOSED instance, the supplied disposable
 * is disposed. If the field contains other non-null Disposable, an IllegalStateException
 * is signalled to the RxJavaPlugins.onError hook.
 * 
 * @param field the target field
 * @param d the disposable to set, not null
 * @return true if the operation succeeded, false
 */
public static boolean setOnce(AtomicReference<Disposable> field, Disposable d) {
    ObjectHelper.requireNonNull(d, "d is null");
    if (!field.compareAndSet(null, d)) {
        d.dispose();
        if (field.get() != DISPOSED) {
            reportDisposableSet();
        }
        return false;
    }
    return true;
}
{{< /highlight >}}

The official doc is already very clear. Just I want to highlight one thing: 
>If the target field contains the common DISPOSED instance, the supplied disposable is disposed

This is an important behavior, we will see why later.

Next, let's look at the method related to the `Runnable` interface, there is only one:

{{< highlight java>}}
@Override
public void run() {
    if (get() != DisposableHelper.DISPOSED) {
        downstream.onNext(count++);
    }
}
{{< /highlight >}}

This is where the counting is done, it will keep sending the count, if it is not disposed. This makes sense.

Now we have examined the class `IntervalObserver` in detail, let's step back a little, and take one more look at the `subscribeActual` method of `ObservableInterval`:

{{< highlight java>}}
/** Simplified */
@Override
public void subscribeActual(Observer<? super Long> observer) {
    IntervalObserver is = new IntervalObserver(observer);
    observer.onSubscribe(is);

    Scheduler sch = scheduler;
    Disposable d = sch.schedulePeriodicallyDirect(is, initialDelay, period, unit);
    is.setResource(d);
}
{{< /highlight >}}

I hope you can understand more this time.

{{< figure src="/img/rxjava2.png" >}}

Now there is only one question left unanswered: why we need to use this AtomicReference thing? Or why
we need to be able to change the internal Disposable at runtime?

Well, I don't know 100% why, but this seems to be a pattern followed by RxJava.
When the `Observable` is subscribed, the `observer` is immediately given a `Disposable`. Even if later the sitution changes, like
some task gets scheduled, we do not need to notify the `observer` again about this. It's like I will give you a Disposable object from the beginning,
and you can keep it and use it whenever you like, and I handle all the cases internally.

Exercise:
What if we dispose immediately in onSubscribe()?

{{< highlight java>}}
 Observable.interval(1, TimeUnit.SECONDS)
      .subscribe(object : Observer<Long> {
        override fun onSubscribe(d: Disposable) {
          d.dispose()
        }

        override fun onNext(t: Long) {
          Log.d("test", t.toString())
        }

        override fun onError(e: Throwable) {
          Log.d("test", "OnError" + e.message)
        }

        override fun onComplete() {
          Log.d("test", "onComplete")
        }
      })
{{< /highlight >}}

Try to trace down the code and see what is happening.
