---
title: "Rxjava fromCallable() Vs defer()"
date: 2017-07-11T17:13:32+03:00
tags: ["Android", "RxJava"]
categories: ["programming"]
---

In this post we talk about how to use `Observable.fromCallable()` and `Observable.defer()` to convert exising functionality into the Rx.

Imagine that you have a `UserService` class, in it there is a `getUserFromDb()` function. This function is developed before RxJava and cannot be changed. But somehow you need a function which returns a `Observable<User>`. What could you do?

## The UserService Example

{{< highlight java >}}

public class UserService {

    /**
     * Gets User from database, this should not be run in UI thread.
     */
    public User getUserFromDb() throws InterruptedException {
        Thread.sleep(10000);
        return new User(1L);
    }

    public Observable<User> getUserObservable() {
        try {
            return Observable.just(getUserFromDb());
        } catch (InterruptedException e) {
            return Observable.error(e);
        }
    }
}

{{< /highlight >}}

{{< highlight java >}}

public class User {

    public final long id;

    public User(long id) {
        this.id = id;
    }
}

{{< /highlight >}}

## The wrong approach

It is very tempting to use the `Observable.just()` to wrap the function call like this:

{{< highlight java >}}

public Observable<User> getUserObservable() {
        try {
            return Observable.just(getUserFromDb());
        } catch (InterruptedException e) {
            return Observable.error(e);
        }
    }

{{< /highlight >}}

And when need to call it, we do:

{{< highlight java >}}
userService.getUserObservable()
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .doOnSubscribe(new Action0() {
                    @Override
                    public void call() {
                        Log.d(TAG, "start loading...");
                    }
                })
                .subscribe(new Action1<User>() {
                    @Override
                    public void call(User user) {
                        Log.d(TAG, "call: user " + user);
                    }
                }, new Action1<Throwable>() {
                    @Override
                    public void call(Throwable throwable) {
                        Log.e(TAG, "call: ", throwable);
                    }
                });
{{< /highlight >}}

You may think that since we `subscribeOn(Schedulers.io())` and `observeOn(AndroidSchedulers.mainThread())` everything should just be fine. WRONG. Turns out that it is still blocking on the UI thread. So this is not what we wanted.

## Use Observable.defer()

Now let's trying wrap the call inside the `Observable.defer()`:

{{< highlight java >}}
public Observable<User> getUserObservableDefer() {
        return Observable.defer(new Func0<Observable<User>>() {
            @Override
            public Observable<User> call() {
                try {
                    return Observable.just(getUserFromDb());
                } catch (InterruptedException e) {
                    return Observable.error(e);
                }
            }
        });
    }
{{< /highlight >}}

Now this works as we expected, but the it is still a lot of code plus that we have to handle the exception by ourselves.

## Use Observable.fromCallable()

{{< highlight java >}}
public Observable<User> getUserObservableCallable() {
        return Observable.fromCallable(new Callable<User>() {
            @Override
            public User call() throws Exception {
                return getUserFromDb();
            }
        });
    }
{{< /highlight >}}

This works equally well, and we don't need to handle the exception ourselves, best choice!

~THE END~
