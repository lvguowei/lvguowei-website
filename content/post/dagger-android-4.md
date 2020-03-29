+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "How to do dagger-android"
keywords = ["Dagger 2", "Dagger", "dagger-android", "Android"]
featured = "dagger.jpg"
featuredpath = "/img"
title = "Dagger Android Tutorial 4"
date = 2020-03-29T15:18:19+03:00
+++

# Convert RxJava stream into LiveData stream

When we use Rxjava for handling api requests, it will return a RxJava Stream. But in the presentation layer we are using LiveData, this article shows you how to make them work together.

The tool we are using here is [LiveDataReactiveStreams](https://developer.android.com/reference/androidx/lifecycle/LiveDataReactiveStreams).

> It is an adapter from LiveData to ReactiveStream and vice versa.

This is how we do it in `AuthViewModel`.

{{< highlight java>}}
public class AuthViewModel extends ViewModel {

    private static final String TAG = "AuthViewModel";

    private final AuthApi authApi;
    
    
    // 1. Instead of MutableLiveData, we declare a MediatorLiveData here.
    private MediatorLiveData<User> authUser = new MediatorLiveData<>();

    @Inject
    AuthViewModel(AuthApi authApi) {
        this.authApi = authApi;
    }

    void authenticateWith(int userId) {
        //2. Convert the rx stream into livedata stream
        final LiveData<User> source = LiveDataReactiveStreams.fromPublisher(authApi.getUser(userId).subscribeOn(Schedulers.io()));
        //3. Add the converted stream as a source to the MediatorLiveData
        authUser.addSource(source, new Observer<User>() {
            @Override
            public void onChanged(User user) {
                authUser.setValue(user);
                
                //4. Unsubscribe after we get the result
                authUser.removeSource(source);
            }
        });
    }

    LiveData<User> user() {
        return authUser;
    }
}
{{< /highlight >}}

Then in `AuthActivity`:

Trigger login with user id.
{{< highlight java>}}
private void attemptLogin() {
    if (TextUtils.isEmpty(userIdEditText.getText().toString())) {
        return;
    }

    viewModel.authenticateWith(Integer.parseInt(userIdEditText.getText().toString()));
}
{{< /highlight >}}

Listening to the user live data.

{{< highlight java>}}
private void subscribeObservers() {
    viewModel.user().observe(this, new Observer<User>() {
        @Override
        public void onChanged(User user) {
            if (user != null) {
                Log.d(TAG, "onChanged: " + user.getUsername());
            }
        }
    });
}
{{< /highlight >}}

That's it.

Code for this article is [here](https://github.com/lvguowei/DaggerExampleFollowAlong/commit/3b41cc423d87a63030ec203d2128bdd41d5dd490).

Video is [here](https://www.youtube.com/watch?v=RKLj35VfoYE&list=PLgCYzUzKIBE8AOAspC3DHoBNZIBHbIOsC&index=17).
