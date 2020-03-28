+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "How to do dagger-android"
keywords = ["Dagger 2", "Dagger", "dagger-android", "Android"]
featured = "dagger.jpg"
featuredpath = "/img"
title = "Dagger Android Tutorial 2"
date = 2020-03-28T17:48:48+02:00
+++

In this article we show how to inject into ViewModels.

> Note that we will not dive too deep into the inner workings of things, since they are extremely complex. If you want to get some level of insight into the topic read [this article](https://www.techyourchance.com/dependency-injection-viewmodel-with-dagger-2/). But I try to give the clean solution and how you can put it in your head.

First a few words about the `ViewModel` in android MVVM architecture.

The important thing to remember is that the **constructor of ViewModel cannot have any arguments**.
So we cannot directly use constructor injection on ViewModels. What we can do instead is to somehow let Dagger provide the `ViewModelProvider.Factory`.

Actually, we will create a very clever implementation of the `ViewModelProvider.Factory` interface and let Dagger provide that.

{{< highlight java>}}
public class ViewModelProviderFactory implements ViewModelProvider.Factory {

    private static final String TAG = "ViewModelProviderFactor";

    private final Map<Class<? extends ViewModel>, Provider<ViewModel>> creators;

    @Inject
    public ViewModelProviderFactory(Map<Class<? extends ViewModel>, Provider<ViewModel>> creators) {
        this.creators = creators;
    }

    @NonNull
    @Override
    public <T extends ViewModel> T create(@NonNull Class<T> modelClass) {
        Provider<? extends ViewModel> creator = creators.get(modelClass);
        if (creator == null) { // if the viewmodel has not been created

            // loop through the allowable keys (aka allowed classes with the @ViewModelKey)
            for (Map.Entry<Class<? extends ViewModel>, Provider<ViewModel>> entry : creators.entrySet()) {

                // if it's allowed, set the Provider<ViewModel>
                if (modelClass.isAssignableFrom(entry.getKey())) {
                    creator = entry.getValue();
                    break;
                }
            }
        }

        // if this is not one of the allowed keys, throw exception
        if (creator == null) {
            throw new IllegalArgumentException("unknown model class " + modelClass);
        }

        // return the Provider
        try {
            return (T) creator.get();
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
}
{{< /highlight >}}

Notice that this class is using constructor injection. And the only dependency it has is a map. The map from <some ViewModel class> -> Provider<some ViewModel class>. So when we use this `Factory`, it will automatically look up the corresponding ViewModel provider to create the ViewModel object.

There are now 2 questions:
1. Where does this map come from?
2. How each ViewModel is created?

Let's answer the 2nd question first. This is simple, just use normal constructor injection in ViewModel classes.

{{< highlight java>}}
public class AuthViewModel extends ViewModel {

    private static final String TAG = "AuthViewModel";

    @Inject
    AuthViewModel() {
        Log.d(TAG, "AuthViewModel: is working!");
    }
}
{{< /highlight >}}

The anwser of the 1st question needs more explanation. In short, this map (vm class -> Provider<vm class>) can be created using multi-binding.

{{< highlight java>}}
// /di/auth/AuthViewModelsModule

@Module
public abstract class AuthViewModelsModule {

    @Binds
    @IntoMap
    @ViewModelKey(AuthViewModel.class)
    public abstract ViewModel bindAuthViewModel(AuthViewModel viewModel);
}
{{< /highlight >}}

{{< highlight java>}}
@Documented
@Target({ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@MapKey
public @interface ViewModelKey {
    Class<? extends ViewModel> value();
}
{{< /highlight >}}

We then add this `AuthViewModelsModule` into the `AuthActivity` subcomponent. Wait, we did not create any subcomponent!?

Actually by using `@ContributesAndroidInjector`, it will automatically create a subcomponent for us.

So let's update the `ActivityBuildersModule` to the following:

{{< highlight java>}}
@Module
abstract class ActivityBuildersModule {

    @ContributesAndroidInjector(modules = {AuthViewModelsModule.class})
    abstract AuthActivity contribute();
}

{{< /highlight >}}

Next we can create a @Module that binds our clever `ViewModelProvider.Factory`:

{{< highlight java>}}

// /di/ViewModelFactoryModule

@Module
public abstract class ViewModelFactoryModule {

    @Binds
    public abstract ViewModelProvider.Factory bind(ViewModelProviderFactory factory);
}
{{< /highlight >}}

And add it to `AppComponent`.

Now we can try to inject the `AuthViewModel` in `AuthActivity`.

{{< highlight java>}}
public class AuthActivity extends DaggerAppCompatActivity {

    private AuthViewModel viewModel;

    @Inject
    ViewModelProviderFactory viewModelProviderFactory;

    @Inject
    Drawable logo;

    @Inject
    RequestManager glideInstance;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_auth);

        viewModel = new ViewModelProvider(this, viewModelProviderFactory).get(AuthViewModel.class);
        setLogo();

    }

    private void setLogo() {
        glideInstance.load(logo).into((ImageView) findViewById(R.id.login_logo));
    }
}

{{< /highlight >}}
