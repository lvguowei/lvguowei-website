+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "work with SavedStateHandle"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Dependency Injection in Android With Dagger2 (14)"
date = 2021-05-16T08:59:40+03:00
+++

Here is one clean solution of how to combine *Dagger + ViewModel + SavedStateHandle*.

The only difference now is that inside our `ViewModel` has access to `SavedStateHandle`.
We capture this through a abstract class:

{{< highlight kotlin >}}
abstract class SavedStateViewModel: ViewModel() {
    abstract fun init(savedStateHandle: SavedStateHandle)
}
{{< /highlight >}}

And the `ViewModel` now looks like this:

{{< highlight kotlin >}}
class MyViewModel @Inject constructor(
    private val fetchQuestionsUseCase: FetchQuestionsUseCase
) : SavedStateViewModel() {

    private lateinit var _questions: MutableLiveData<List<Question>>
    val questions: LiveData<List<Question>> get() = _questions

    override fun init(savedStateHandle: SavedStateHandle) {
        _questions = savedStateHandle.getLiveData("questions")

        viewModelScope.launch {
            delay(5000)
            val result = fetchQuestionsUseCase.fetchLatestQuestions()
            if (result is FetchQuestionsUseCase.Result.Success) {
                _questions.value = result.questions
            } else {
                throw RuntimeException("fetch failed")
            }
        }
    }
}
{{< /highlight >}}


And our `ViewModelFactory` looks like this:

{{< highlight kotlin >}}

class ViewModelFactory @Inject constructor(
        private val providersMap: Map<Class<out ViewModel>, @JvmSuppressWildcards Provider<ViewModel>>,
        savedStateRegistryOwner: SavedStateRegistryOwner
): AbstractSavedStateViewModelFactory(savedStateRegistryOwner, null) {

    override fun <T : ViewModel?> create(key: String, modelClass: Class<T>, handle: SavedStateHandle): T {
        val provider = providersMap[modelClass]
        val viewModel = provider?.get() ?: throw RuntimeException("unsupported viewmodel type: $modelClass")
        if (viewModel is SavedStateViewModel) {
            viewModel.init(handle)
        }
        return viewModel as T
    }
}
{{< /highlight >}}

In order to provide this `ViewModelFactory`, we need to do the following.

Create `PresentationModule` to provide `SavedStateRegistryOwner`.

{{< highlight kotlin >}}
@Module
class PresentationModule(private val savedStateRegistryOwner: SavedStateRegistryOwner) {

    @Provides
    fun savedStateRegistryOwner() = savedStateRegistryOwner
}
{{< /highlight >}}

Add it to `PresentationComponent`.

{{< highlight kotlin >}}
@PresentationScope
@Subcomponent(modules = [PresentationModule::class, ViewModelModule::class])
interface PresentationComponent {
    fun inject(fragment: QuestionsListFragment)
    fun inject(activity: QuestionDetailsActivity)
    fun inject(questionsListActivity: QuestionsListActivity)
    fun inject(viewModelActivity: ViewModelActivity)
}
{{< /highlight >}}

Modify the function `newPresentationComponent()` to take a `presentationModule` as parameter.

{{< highlight kotlin >}}
@ActivityScope
@Subcomponent(modules = [ActivityModule::class])
interface ActivityComponent {

    fun newPresentationComponent(presentationModule: PresentationModule): PresentationComponent

    @Subcomponent.Builder
    interface Builder {
        @BindsInstance fun activity(activity: AppCompatActivity): Builder
        fun build(): ActivityComponent
    }
}
{{< /highlight >}}

Finally, inside `BaseActivity`, `BaseFragment` and `BaseDialog`, create the `PresentationComponent` like this:

{{< highlight kotlin >}}
open class BaseActivity: AppCompatActivity() {

    private val appComponent get() = (application as MyApplication).appComponent

    val activityComponent by lazy {
        appComponent.newActivityComponentBuilder()
                .activity(this)
                .build()
    }

    private val presentationComponent by lazy {
        activityComponent.newPresentationComponent(PresentationModule(this))
    }

    protected val injector get() = presentationComponent
}
{{< /highlight >}}

{{< highlight kotlin >}}
open class BaseFragment: Fragment() {

    private val presentationComponent by lazy {
        (requireActivity() as BaseActivity).activityComponent.newPresentationComponent(PresentationModule(this))
    }

    protected val injector get() = presentationComponent
}
{{< /highlight >}}

{{< highlight kotlin >}}

open class BaseDialog: DialogFragment() {

    private val presentationComponent by lazy {
        (requireActivity() as BaseActivity).activityComponent.newPresentationComponent(PresentationModule(this))
    }

    protected val injector get() = presentationComponent
}
{{< /highlight >}}


