+++
author = "Guowei Lv"
categories = ["Android + Dagger2"]
description = "Static Provider Methods and Component Builder"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Dependency Injection in Android With Dagger2 (7)"
date = 2021-05-07T23:31:25+03:00
+++

One fact: it is more performant to make the all provider methods inside Modules static.

But for Modules that have bootstrap dependencies, this is impossible. Let's take `ActivityModule` as example.

{{< highlight kotlin >}}
@Module
class ActivityModule(private val activity: AppCompatActivity) {

    @Provides
    fun activity(): AppCompatActivity = activity

    @Provides
    fun layoutInflater(activity: AppCompatActivity): LayoutInflater = LayoutInflater.from(activity)

    @Provides
    fun fragmentManager(activity: AppCompatActivity) = activity.supportFragmentManager

    @Provides
    @ActivityScope
    fun screensNavigator(activity: AppCompatActivity) = ScreensNavigator(activity)
}
{{< /highlight >}}

We can easily make `fun layoutInflater()`, `fragmentManager()` and `screensNavigator` static methods. But we cannot do it for `fun activity()`, because static method does not have access to private property `activity`.

This means we cannot pass anything into Module's constructor. But then where can we provide `activity`? The answer is `ActivityComponent` can provide it directly.

And in order to do this, we need some extra stuff.

First, let's recap how `ActivityComponent` is created.


{{< highlight kotlin >}}
@AppScope
@Component(modules = [AppModule::class])
interface AppComponent {
    fun newActivityComponent(activityModule: ActivityModule): ActivityComponent
    fun newServiceComponent(serviceModule: ServiceModule): ServiceComponent
}
{{< /highlight >}}

{{< highlight kotlin >}}
open class BaseActivity : AppCompatActivity() {
    
    ...
    
    val activityComponent: ActivityComponent by lazy {
        appComponent.newActivityComponent(ActivityModule(this))
    }
    
    ...
}
{{< /highlight >}}

So basically this is how `ActivityComponent` created: ` appComponent.newActivityComponent(ActivityModule(this))`.

So if now `ActivityModule` does not take in `activity`, then the only place to provide `activity` is `ActivityComponent` itself.

But as you can see, we don't have control over the creation of `ActivityComponent`. So the solution to this problem is `Component Builder`.

{{< highlight kotlin >}}
@ActivityScope
@Subcomponent(modules = [ActivityModule::class])
interface ActivityComponent {
    fun newPresentationComponent(): PresentationComponent

    @Subcomponent.Builder
    interface Builder {

        @BindsInstance
        fun activity(activity: AppCompatActivity): Builder

        fun activityModule(module: ActivityModule): Builder

        fun build(): ActivityComponent
    }
}
{{< /highlight >}}

So we create `ActivityComponent.Builder` so that we can describe how we want to build an `ActivityComponent`: need `ActivityModule` and an instance of `activity`.

And once we declare this `Builder` interface, we cannot expose function that returns `ActivityComponent` directly, we need to change the return type to the `Builder`.

{{< highlight kotlin >}}
@AppScope
@Component(modules = [AppModule::class])
interface AppComponent {
    fun newActivityComponentBuilder(): ActivityComponent.Builder
    fun newServiceComponent(serviceModule: ServiceModule): ServiceComponent
} 
{{< /highlight >}}

Now we are able to create `ActivityComponent` inside `BaseActivity` like this:

{{< highlight kotlin >}}
open class BaseActivity : AppCompatActivity() {

    ...
    
    val activityComponent: ActivityComponent by lazy {
        appComponent.newActivityComponentBuilder()
                .activity(this)
                .activityModule(ActivityModule)
                .build()
    }
    
    ...

}
{{< /highlight >}}

And finally the `ActivityModule` looks like:

{{< highlight kotlin >}}
@Module
object ActivityModule {

    @Provides
    @ActivityScope
    fun screensNavigator(activity: AppCompatActivity) = ScreensNavigator(activity)

    @Provides
    fun layoutInflater(activity: AppCompatActivity): LayoutInflater = LayoutInflater.from(activity)

    @Provides
    fun fragmentManager(activity: AppCompatActivity) = activity.supportFragmentManager
}
{{< /highlight >}}
