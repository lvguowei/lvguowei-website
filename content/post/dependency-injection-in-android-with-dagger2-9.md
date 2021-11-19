+++
author = "Guowei Lv"
categories = ["Android + Dagger2"]
description = "Qualifier"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Dependency Injection in Android With Dagger2 (9)"
date = 2021-05-09T22:06:54+03:00
+++

This is a simple one. No more explanation, just paste the code here.

{{< highlight kotlin >}}
@UiThread
@Module
class AppModule {

    @Provides
    @AppScope
    @Retrofit1
    fun retrofit1(urlProvider: UrlProvider): Retrofit {
        return Retrofit.Builder()
            .baseUrl(urlProvider.baseUrl1())
            .addConverterFactory(GsonConverterFactory.create())
            .build()
    }

    @Provides
    @AppScope
    @Retrofit2
    fun retrofit2(): Retrofit {
        return Retrofit.Builder()
            .baseUrl(urlProvider().baseUrl2())
            .addConverterFactory(GsonConverterFactory.create())
            .build()
    }

    @Provides
    @AppScope
    fun stackOverflowApi(@Retrofit1 retrofit: Retrofit): StackoverflowApi =
        retrofit.create(StackoverflowApi::class.java)

    @Provides
    @AppScope
    fun urlProvider(): UrlProvider = UrlProvider()
}
{{< /highlight >}}

{{< highlight kotlin >}}
@Qualifier
annotation class Retrofit1()

@Qualifier
annotation class Retrofit2()
{{< /highlight >}}
