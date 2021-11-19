+++
author = "Guowei Lv"
categories = ["Android + Dagger2"]
description = "Multibinding"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Dependency Injection in Android With Dagger2 (13)"
date = 2021-05-16T07:53:38+03:00
+++

Previously, we created a centralized `ViewModelFactory` which can create all `ViewModel`s in the app:

{{< highlight kotlin >}}
class ViewModelFactory @Inject constructor(
        private val myViewModelProvider: Provider<MyViewModel>,
        private val myViewModel2Provider: Provider<MyViewModel2>
): ViewModelProvider.Factory {
    override fun <T : ViewModel?> create(modelClass: Class<T>): T {
        return when(modelClass) {
            MyViewModel::class.java -> myViewModelProvider.get() as T
            MyViewModel2::class.java -> myViewModel2Provider.get() as T
            else -> throw RuntimeException("unsupported ViewModel type: $modelClass")
        }
    }
}
{{< /highlight >}}

But with more and more `ViewModel`s being added to our app, this class will grow quickly, is there a way to mitigate this? The answer is the `Multibinding` feature in Dagger.

Basically Dagger can prepare a Map structure (or Set), and put it on the graph. In our case, we can let Dagger create a Map of:

- key: the `ViewModel`'s class
- value: the `ViewModel` itself

Then our `ViewModelFactory` class can be much more simplified:

{{< highlight kotlin >}}
class ViewModelFactory @Inject constructor(
        private val providers: Map<Class<out ViewModel>, @JvmSuppressWildcards Provider<ViewModel>>
): ViewModelProvider.Factory {
    override fun <T : ViewModel?> create(modelClass: Class<T>): T {
        val provider = providers[modelClass]
        return provider?.get() as T ?: throw RuntimeException("unsupported viewmodel type: $modelClass")
    }
}
{{< /highlight >}}

Don't forget that `@JvmSuppressWildcards`, otherwise it will not work.

Now we need to figure out how to create and provide this Map in Dagger.

We need a dedicated `@Module` for providing `ViewModel`s.


{{< highlight kotlin >}}
@Module
abstract class ViewModelsModule {

    @Binds
    @IntoMap
    @ViewModelKey(MyViewModel::class)
    abstract fun myViewModel(myViewModel: MyViewModel): ViewModel

    @Binds
    @IntoMap
    @ViewModelKey(MyViewModel2::class)
    abstract fun myViewModel2(myViewModel2: MyViewModel2): ViewModel
}
{{< /highlight >}}

This basically tells Dagger how to create this Map. The key is annotated with @ViewModelKey and the value is the return type of the functions `ViewModel`.

Now the last puzzle piece is to create the key annotation:

{{< highlight kotlin >}}
@MapKey
annotation class ViewModelKey(val value: KClass<out ViewModel>)
{{< /highlight >}}

This is how we can use Dagger's *multibinding* to "simplify" the implementation of our `ViewModelFactory`.
