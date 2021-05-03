+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "Subcomponent"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Dependency Injection in Android With Dagger2 (5)"
date = 2021-05-03T06:54:09+03:00
+++

In previous post, we introduced the component dependencies. But there is one interesting subtlety: If Component B depends on Component A, B has access to all A's **exposed** objects. Note that there is a difference between what a component exposes and what it provides.

Let's look at `PresentationComponent` and `ActivityComponent`.

{{< highlight kotlin >}}

@PresentationScope
@Component(dependencies = [ActivityComponent::class], modules = [PresentationModule::class])
interface PresentationComponent {
    fun inject(fragment: QuestionsListFragment)
    fun inject(questionDetailsActivity: QuestionDetailsActivity)
}
{{< /highlight >}}

Here in `PresentationComponent`, even though it **provides** all the objects that `QuestionsListFragment` and `QuestionDetailsActivity` need, it **exposes** nothing.

But in `ActivityComponent`:

{{< highlight kotlin >}}
@ActivityScope
@Component(dependencies = [AppComponent::class], modules = [ActivityModule::class])
interface ActivityComponent {
    fun layoutInflater(): LayoutInflater
    fun fragmentManager(): FragmentManager
    fun screensNavigator(): ScreensNavigator
    fun stackOverflowApi(): StackoverflowApi
}
{{< /highlight >}}

It **exposes** `LayoutInflater`, `FragmentManager`, `ScreensNavigator`, `StackoverflowApi`. If we don't write these functions to expose these objects, the `PresentationComponent` will not be able to access them.

Sometimes this can feel a bit inconvenient. E.g. in order for the `PresentationComponent` to have access to `StackoverflowApi`, `ActivityComponent` has to expose it, and also `AppComponent` has to expose it as well. Is there a way to get rid of this *expose chain*?

The answer is **subcomponent**.

Here is how it works:

1. Subcomponents specified by `@Subcomponent` annotation.
2. Parent Component exposes factory method which returns Subcomponent.
3. The arguments of the factory method are Subcomponent's Modules.
4. Subcomponent get access to all services provided by parent(provided, not just exposed).

Now the Components are dead simple:

{{< highlight kotlin >}}
@AppScope
@Component(modules = [AppModule::class])
interface AppComponent {
    fun newActivityComponent(activityModule: ActivityModule): ActivityComponent
}
{{< /highlight >}}

{{< highlight kotlin >}}
@ActivityScope
@Subcomponent(modules = [ActivityModule::class])
interface ActivityComponent {
    fun newPresentationComponent(presentationModule: PresentationModule): PresentationComponent
}
{{< /highlight >}}

{{< highlight kotlin >}}
@PresentationScope
@Subcomponent(modules = [PresentationModule::class])
interface PresentationComponent {
    fun inject(fragment: QuestionsListFragment)
    fun inject(questionDetailsActivity: QuestionDetailsActivity)
}
{{< /highlight >}}


{{< figure src="/img/subcomponent.png" >}}




