+++
author = "Guowei Lv"
categories = ["Android + Dagger2"]
description = "Type Bindings"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Dependency Injection in Android With Dagger2 (8)"
date = 2021-05-09T14:56:26+03:00
+++

Let's create a `ScreensNavigator` interface:

{{< highlight kotlin >}}
interface ScreensNavigator {
    fun navigateBack()
    fun toQuestionDetails(questionId: String)
}
{{< /highlight >}}

And `ScreensNavigatorImpl` implements it:

{{< highlight kotlin >}}

class ScreensNavigatorImpl @Inject constructor(private val activity: AppCompatActivity) :
    ScreensNavigator {

    override fun navigateBack() {
        activity.onBackPressed()
    }

    override fun toQuestionDetails(questionId: String) {
        QuestionDetailsActivity.start(activity, questionId)
    }
}
{{< /highlight >}}

Now we have a problem that in activities and fragments we want to inject the interface type:

{{< highlight kotlin >}}
@Inject
lateinit var screensNavigator: ScreensNavigator
{{< /highlight >}}

But Dagger only knows how to create `ScreensNavigatorImpl`. How can we help Dagger to figure out when we want an `ScreensNavigator`, we need an instance of `ScreensNavigatorImpl`? The trick is to use `@Binds`.

{{< highlight kotlin >}}
@Module
abstract class ActivityModule {

    @ActivityScope
    @Binds
    abstract fun screensNavigator(screensNavigatorImpl: ScreensNavigatorImpl): ScreensNavigator

    /**
     * Moved these to companion object because otherwise we get this error
     * A @Module may not contain both non-static and abstract binding method
     */
    companion object {
        @Provides
        fun layoutInflater(activity: AppCompatActivity): LayoutInflater =
            LayoutInflater.from(activity)

        @Provides
        fun fragmentManager(activity: AppCompatActivity) = activity.supportFragmentManager
    }
}
{{< /highlight >}}

Now that we have to make `ActivityModule` an abstract class, we need to remove `fun activityModule()` from `ActivityComponent.Builder`. And Dagger
knows how to construct abstract Modules by itself.
