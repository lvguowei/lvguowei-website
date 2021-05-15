+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "ViewModel with Dagger (basic version)"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Dependency Injection in Android With Dagger2 (11)"
date = 2021-05-15T08:56:32+03:00
+++

The main advantage of using `ViewModel` is: it can survive configuration change. That means if you associate a `ViewModel` with an `Activity`, then after configuration change, the activity will get the same instance of that `ViewModel`.

Of course that comes with some setup work to do on developers side. But it is not that bad, basically *the activity gets its ViewModel through ViewModelProvider*.

If your ViewModel has empty constructor, then you can simply use:

{{< highlight kotlin >}}
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    myViewModel = ViewModelProvider(this).get(MyViewModel::class.java)
}
{{< /highlight >}}

But most likely your ViewModel will have some dependencies, like some `usecase` or `service` classes. Then we have to use `ViewModelProvider.Factory` to help.

Suppose `MyViewModel` is:

{{< highlight kotlin >}}

class MyViewModel @Inject constructor(
    private val fetchQuestionsUseCase: FetchQuestionsUseCase
) : ViewModel() {...}

{{< /highlight >}}

Define a `Factory` class that can create your `ViewModel`:

{{< highlight kotlin >}}

class Factory @Inject constructor(private val myViewModelProvider: Provider<MyViewModel>) : ViewModelProvider.Factory {
    override fun <T : ViewModel?> create(modelClass: Class<T>): T {
        return myViewModelProvider.get() as T
    }
}

{{< /highlight >}}

Finally:

{{< highlight kotlin >}}

class ViewModelActivity : BaseActivity() {

    @Inject lateinit var myViewModelFactory: MyViewModel.Factory

    private lateinit var myViewModel: MyViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        ...
        
        myViewModel = ViewModelProvider(this, myViewModelFactory).get(MyViewModel::class.java)
        
        ...
    }
{{< /highlight >}}

This is how you implement ViewModel + Dagger the most straightforward way.
