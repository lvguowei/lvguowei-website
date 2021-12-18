+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "A bottom up approach"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "How to Really Understand ViewModel in Android"
date = 2021-12-18T10:50:35+02:00
+++

Yes, there is documentation and tons of sample apps out there. But they are all in a top down approach, meaning they give you the final result of how to use it first, and never tells you why and what is behind the scenes.

I will fix that in this article, and present a bottom up approach which leads to much better and deeper understanding.

First, at the bottom of things, we need to define what is `ViewModel`. Well, this may come a bit surprising, but it is really nothing special but just a super simple class, like we need to call it something, right?


{{< img-post "/img" "viewmodel.jpg" "View Model" "center" >}}

Normally, each `Activity` or `Fragment` would only have one `ViewModel` associated with it, but that's not good enough, we also want to be able to have multiple ones, so we'd better create some kind of `ViewModel` container to hold all the `VM`s, let's call it `ViewModelStore`. So instead of one single ViewModel, now each Activity or Fragment would have a `ViewModelStore`. Also it has to survive configuration changes, more on this later.

{{< img-post "/img" "viewmodelstore.jpg" "View Model Store" "center" >}}

There is anoter role called `ViewModelStoreOwner`, which is really not very interesting at all, since anyone who has a `ViewModelStore` can be called a `ViewModelStoreOwner`.

{{< img-post "/img" "viewmodelstoreowner.jpg" "View Model Store Owner" "center" >}}

OK, now we have a place to store our VMs, but we do not really know how to create them yet, let's not rely on constructors cause they are not abstract enough, let's make proper OOP design and create the `Factory`. It is actually an interface, which only knows how to create ViewModel given its Class. (There are many impls of this interface, e.g. there is one for creating VM with `SavedStateHandle`).

{{< img-post "/img" "vmfactory.jpg" "View Model Factory" "center" >}}

OK, now the question is how to wire these objects up to achieve what we want. But what do we actually want? Simple, we want to get back a certain ViewModel from Activity or Fragment.Now, time to introduce the our last guest, `ViewModelProvider`. Inside it contains a factory and a store, and this is what it does when asked for a ViewModel.

1. If the store contains the VM, then return it.
2. If not, create the VM using the factory, put it into the store, and return it.

OK, now you should have a 3000 feet view of what is really happening behind the scenes.

Let's also answer these 2 often confusing questions.

1. How does `ViewModelStore` survive configuration changes?
   It uses the `NonConfigurationInstance` thing, check out this code from `ComponentActivity`:
   
   {{< highlight java>}}
     public final Object onRetainNonConfigurationInstance() {
        // Maintain backward compatibility.
        Object custom = onRetainCustomNonConfigurationInstance();

        ViewModelStore viewModelStore = mViewModelStore;
        if (viewModelStore == null) {
            // No one called getViewModelStore(), so see if there was an existing
            // ViewModelStore from our last NonConfigurationInstance
            NonConfigurationInstances nc =
                    (NonConfigurationInstances) getLastNonConfigurationInstance();
            if (nc != null) {
                viewModelStore = nc.viewModelStore;
            }
        }

        if (viewModelStore == null && custom == null) {
            return null;
        }

        NonConfigurationInstances nci = new NonConfigurationInstances();
        nci.custom = custom;
        nci.viewModelStore = viewModelStore;
        return nci;
     }
   {{< /highlight >}}

2. Why ViewModel cannot survive process death then?
   Simply because ViewModel cannot really be parcelised, only Parcelable can be reconstructed after process death. Also, on the code level, we clear the store on `onDestroy()`.

{{< highlight java>}}
   getLifecycle().addObserver(new LifecycleEventObserver() {
        @Override
        public void onStateChanged(@NonNull LifecycleOwner source,
                @NonNull Lifecycle.Event event) {
            if (event == Lifecycle.Event.ON_DESTROY) {
                // Clear out the available context
                mContextAwareHelper.clearAvailableContext();
                // And clear the ViewModelStore
                if (!isChangingConfigurations()) {
                    getViewModelStore().clear();
                }
            }
        }
    });
{{< /highlight >}}

Now I believe you have enough knowledge to survive this wild world of ViewModels on your own.
