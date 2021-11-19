+++
author = "Guowei Lv"
categories = ["Android + Dagger2"]
description = "Centralized Factory for ViewModels"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Dependency Injection in Android With Dagger2 (12)"
date = 2021-05-15T18:45:09+03:00
+++

It is a bit cumbersome to create a `Factory` for each `ViewModel` we have. Actually, we can just create one `Factory` that is responsible for creating all `ViewModel`s in the app.

The Factory is very straightforward:

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

And this is how to use it in activity:

{{< highlight kotlin >}}

class ViewModelActivity : BaseActivity() {

    @Inject lateinit var myViewModelFactory: ViewModelFactory

    private lateinit var myViewModel: MyViewModel
    private lateinit var myViewModel2: MyViewModel2

    override fun onCreate(savedInstanceState: Bundle?) {
        injector.inject(this)
        super.onCreate(savedInstanceState)

        setContentView(R.layout.layout_view_model)

        myViewModel = ViewModelProvider(this, myViewModelFactory).get(MyViewModel::class.java)
        myViewModel2 = ViewModelProvider(this, myViewModelFactory).get(MyViewModel2::class.java)
    }
{{< /highlight >}}
