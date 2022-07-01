+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "Get to know how RxJava works"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Demystify RxJava (1)"
date = 2022-06-30T21:23:02+03:00
+++

Let's see what makes RxJava tick.

RxJava is complex, so I will have to (overly) simplify things at places. All of this is just trying to help you to get a better picture of how RxJava is implemented.

Let's go.

Let's start with the `Single`, it's just an interface with one method:

{{< highlight java>}}
public interface Single<T> {
  void subscribe(SingleObserver<T> observer);
}
{{< /highlight >}}

So a `Single` is just a thing that can be subscribed.

Next, let's look at what is this `SingleObserver`.

{{< highlight java>}}
public interface SingleObserver<T> {
  void onSubscribe(Disposable d);
  void onSuccess(T t);
  void onError(Throwable e);
}
{{< /highlight >}}

Looks like it's just a callback with 3 methods. And they are pretty much self-explanatory. Easy peasy.

Time to see some implementations of these interfaces. The simplest case is this:

{{< highlight java>}}
Single.just(1)
      .subscribe(object : SingleObserver<Int> {
        override fun onSubscribe(d: Disposable) {
        }
        override fun onSuccess(t: Int) {
        }
        override fun onError(e: Throwable) {
        }
      })
{{< /highlight >}}

`Single.just(1)` will create a `SingleJust`, which implements `Single`.

{{< highlight java>}}
public final class SingleJust<T> extends Single<T> {

    final T value;

    public SingleJust(T value) {
        this.value = value;
    }

    @Override
    protected void subscribeActual(SingleObserver<? super T> observer) {
        observer.onSubscribe(Disposables.disposed());
        observer.onSuccess(value);
    }

}

{{< /highlight >}}

When `SingleJust` is subscribed, it does 2 things:
1. Call `observer.onSubscribe()` with a Disposable which is already disposed.
2. Call `observer.onSuccess()` with the value.

This is not hard to understand, since there is only one hardcoded value, there can not be any error, and there is nothing
to be disposed in such simple case.

Now let's take a small step further to look at the `Single.map()` operator.

{{< highlight java>}}
Single.just(1).map { it -> it * 2 }
{{< /highlight >}}

You might have already guessed, `map()` will create a new Single instance, of type `SingleMap`.
 
{{< highlight java>}}
public final class SingleMap<T, R> extends Single<R> {
  
  final SingleSource<? extends T> source;
  final Function<? super T, ? extends R> mapper;

  public SingleMap(SingleSource<? extends T> source, Function<? super T, ? extends R> mapper) {
    this.source = source;
    this.mapper = mapper;
  }

  @Override
  protected void subscribeActual(final SingleObserver<? super R> t) {
    source.subscribe(new MapSingleObserver<T, R>(t, mapper));
  }

  static final class MapSingleObserver<T, R> implements SingleObserver<T> {

    final SingleObserver<? super R> t;
    final Function<? super T, ? extends R> mapper;

    MapSingleObserver(SingleObserver<? super R> t, Function<? super T, ? extends R> mapper) {
      this.t = t;
      this.mapper = mapper;
    }

    @Override
    public void onSubscribe(Disposable d) {
      t.onSubscribe(d);
    }

    @Override
    public void onSuccess(T value) {
      t.onSuccess(mapper.apply(value));
    }

    @Override
    public void onError(Throwable e) {
      t.onError(e);
    }
  }
}

{{< /highlight >}}

`SingleMap` is like a wrapper, to wrap around another `Single` (source). And when `subscribed`, it just
`subscribe` the source `Single` with its internal `MapInternalObserver`.

{{< figure src="/img/rxjava1.png" >}}

That's it for now. Next article we will talk about how `dispose` works. Stay tuned!
