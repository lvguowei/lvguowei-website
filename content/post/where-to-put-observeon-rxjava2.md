+++
author = "Guowei Lv"
categories = ["Android Development"]
keywords = ["RxJava2", "Android", "observeOn", "not on main thread"]
description = "Where to put observeOn matters!"
featured = ""
featuredpath = ""
featuredalt = ""
title = "Where to Put observeOn in Rxjava2"
date = 2018-11-08T20:56:01+02:00
+++

**Me**: Why I have put the `observeOn(AndroidSchedulars.mainThread())` but still `onNext()` is NOT called in android main thread?!

**Rx Master**: Show me your code.

**Me**: Here you are my master. I just want to wait for 5 seconds then call an api:

{{< highlight kotlin >}}

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        fun networkObservable(): Observable<String> {
            return Observable.just("test").subscribeOn(Schedulers.io())
        }

        Observable.timer(5, TimeUnit.SECONDS)
            .observeOn(AndroidSchedulers.mainThread())
            .flatMap { _ -> networkObservable() }
            .subscribe(Consumer {
                Log.d("testtest", it)
                Log.d("testtest", Thread.currentThread().name)
            })
        }
    }
{{< /highlight >}}

**Rx Master**: ... Read the Doc:

>Modifies an ObservableSource to perform its emissions and notifications on a specified {@link Scheduler}

**Me**: Ummm... That means the *emmissions and notifications*(onNext, onError, onComplete) of the `timer` observable is on main thread. And... OH yes, the `flatMap` then returns a new observable, and that observable is emmiting on io thread. So I guess I could just move `observeOn` after `flatMap`?

**Rx Master**: Indeed, my young follower. You learned well.

*****Gong*****
