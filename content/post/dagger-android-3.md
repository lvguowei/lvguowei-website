+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "How to do dagger-android"
keywords = ["Dagger 2", "Dagger", "dagger-android", "Android"]
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Dagger Android Tutorial 3"
date = 2020-03-29T09:58:23+03:00
+++

# Inject Retrofit @Singleton

The `Retrofit` instance should be a singleton in the scope of `AppComponent`.

Provide the `Retrofit` instance in `AppModule`.

{{< highlight java>}}
@Singleton
@Provides
static Retrofit provideRetrofitInstance() {
    return new Retrofit.Builder().baseUrl(Constants.BASE_URL)
            .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
            .addConverterFactory(GsonConverterFactory.create()).build();
}
{{< /highlight >}}

Under `AuthActivity` subcomponent, create a module called `AuthModule`, in it, provides the `AuthApi` instance.

{{< highlight java>}}

@Module
public class AuthModule {
    @Provides
    static AuthApi provideAuthApi(Retrofit retrofit) {
        return retrofit.create(AuthApi.class);
    }
}

{{< /highlight >}}

{{< highlight java>}}
public interface AuthApi {

    @GET("/users/{id}")
    Flowable<User> getUser(@Path("id") int id);
}
{{< /highlight >}}


{{< highlight java>}}
@ContributesAndroidInjector(modules = {AuthModule.class, AuthViewModelsModule.class})
abstract AuthActivity contribute();
{{< /highlight >}}

Now let's try to fetch a user in `AuthViewModel`.

{{< highlight java>}}
public class AuthViewModel extends ViewModel {

    private static final String TAG = "AuthViewModel";

    private final AuthApi authApi;

    @Inject
    AuthViewModel(AuthApi authApi) {
        this.authApi = authApi;
        authApi.getUser(1).toObservable()
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Observer<User>() {
                    @Override
                    public void onSubscribe(Disposable d) {

                    }

                    @Override
                    public void onNext(User user) {
                        Log.d(TAG, "onNext: " + user.getEmail());
                    }

                    @Override
                    public void onError(Throwable e) {

                    }

                    @Override
                    public void onComplete() {

                    }
                });
    }
}

{{< /highlight >}}

Code for this article can be found [here](https://github.com/lvguowei/DaggerExampleFollowAlong/commit/ad65cee0e13a70c66cd6508a0c6de9af1aced763)
