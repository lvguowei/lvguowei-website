+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "How to do dagger-android"
keywords = ["Dagger 2", "Dagger", "dagger-android", "Android"]
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Dagger Android Tutorial 1"
date = 2020-03-28T07:52:09+02:00
+++

>Warning: Google thinks you should avoid dagger-android for new projects. [Watch Here](https://www.youtube.com/watch?v=o-ins1nvbDg&feature=youtu.be&t=537)

But, if you are using it now, here are some notes on how to approach it.

This is basically a text version of [these videos](https://www.youtube.com/playlist?list=PLgCYzUzKIBE8AOAspC3DHoBNZIBHbIOsC) 

# Application Component

Pretty much every app will have an application component, whose scope will be the lifetime of the application.

First, create the `AppComponent`

{{< highlight java>}}
//1. Include this 'AndroidSupportInjectionModule', this is required
@Component(modules = {AndroidSupportInjectionModule.class})
public interface AppComponent extends AndroidInjector<BaseApplication> {//2. Extends 'AndroidInjector<BaseApplication>'

    //3. Add a factory. This basically says that an AppComponent needs an Application, and it will expose it
    @Component.Factory
    interface Factory {
        AppComponent create(@BindsInstance Application application);
    }
}

{{< /highlight >}}

Then create a custom application class like this.

{{< highlight java>}}
// 1. Extends DaggerApplication
public class BaseApplication extends DaggerApplication {

    @Override
    protected AndroidInjector<? extends DaggerApplication> applicationInjector() {
        // 2. Create the app component with this as application
        return DaggerAppComponent.factory().create(this);
    }
}

{{< /highlight >}}

# Injecting into Activities

Now let's see how to inject into activities.

First the activity must extends `DaggerAppCompatActivity`.


{{< highlight java>}}
public class AuthActivity extends DaggerAppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
}
{{< /highlight >}}

Then, create module `ActivityBuildersModule`.

{{< highlight java>}}
@Module
abstract class ActivityBuildersModule {
    
    // This means we want to inject into AuthActivity
    @ContributesAndroidInjector
    abstract AuthActivity contribute();
}
{{< /highlight >}}

And add this module into `AppComponent`.

{{< highlight java>}}
@Component(modules = {AndroidSupportInjectionModule.class, ActivityBuildersModule.class})
public interface AppComponent extends AndroidInjector<BaseApplication> {
 ...
}
{{< /highlight >}}

# AppModule

`AppModule` will be responsible to provide some application level objects.

Create the following module class.

{{< highlight java>}}
@Module
class AppModule {

    // Always prefer static method for performance matters
    @Provides
    static RequestOptions provideRequestOptions() {
        return RequestOptions
                .placeholderOf(R.drawable.white_background)
                .error(R.drawable.white_background);
    }

    @Provides
    static RequestManager provideGlideInstance(Application application, RequestOptions requestOptions) {
        return Glide.with(application).setDefaultRequestOptions(requestOptions);
    }

    @Provides
    static Drawable provideAppDrawable(Application application) {
        return ContextCompat.getDrawable(application, R.drawable.logo);
    }
}
{{< /highlight >}}

Remember to also include this in the `AppComponent`.

# Singleton Scope

Let's give the `AppComponent` the `Singleton` scope.

{{< highlight java>}}
@Singleton
@Component(modules = {AndroidSupportInjectionModule.class, ActivityBuildersModule.class, AppModule.class})
public interface AppComponent extends AndroidInjector<BaseApplication> {
 ...
}
{{< /highlight >}}

This means the `AppComponent` owns the `@Singleton` scope. Whatever dependencies that are also marked as `@Singleton`, their lifetime/scope will be tied to the `AppComponent`. It does not have to be `@Singleton`, it can be whatever annotation.

we can now give all the provides methods in the `AppModule` the `@Singleton` annotation.

{{< highlight java>}}
@Module
class AppModule {

    @Singleton
    @Provides
    static RequestOptions provideRequestOptions() {
        return RequestOptions
                .placeholderOf(R.drawable.white_background)
                .error(R.drawable.white_background);
    }

    @Singleton
    @Provides
    static RequestManager provideGlideInstance(Application application, RequestOptions requestOptions) {
        return Glide.with(application).setDefaultRequestOptions(requestOptions);
    }

    @Singleton
    @Provides
    static Drawable provideAppDrawable(Application application) {
        return ContextCompat.getDrawable(application, R.drawable.logo);
    }
}
{{< /highlight >}}

That's it for now.
Stay tuned!
