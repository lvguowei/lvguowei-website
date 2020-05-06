+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "Curated list of realm learning resources"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Realm Guide: From Zero to Give Up"
date = 2020-04-25T17:23:40+03:00
+++

You know nothing about realm, then watch this video first ->

{{< youtube id="QT7XD1hifkU" autoplay="false" >}}

Now you should know the basic basics, then take a look at the [official doc](https://realm.io/docs/kotlin/latest/).

You think you know it all until you try to [combine with RxJava](https://academy.realm.io/posts/creating-a-reactive-data-layer-with-realm-and-rxjava2/).

Now you are super puzzled, look at some [examples with full source code](https://github.com/realm/realm-java/tree/master/examples).

Finally you realize that you have to do all this in clean architecture and decided to give up by [turning Realm into a NoSQL version SQLite](https://stackoverflow.com/questions/38981751/android-kotlin-realm-proper-way-of-query-return-unmanaged-items-on-bg-thread/38983203#38983203
).

{{< highlight java>}}
return Observable.fromCallable {
    Realm.getDefaultInstance().use { realm ->
        realm.copyFromRealm(
            realm.where(ItemRealm::class.java)
                 .equalTo(ItemRealm.FIELD_SEND, false).findAll())
    }
}.subscribeOn(Schedulers.io());

{{< /highlight >}}

~THE END~
