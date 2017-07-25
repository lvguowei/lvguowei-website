---
title: "Android Custom View 101 (Part V)"
date: 2017-07-09T19:26:15+03:00
tags: ["Android"]
categories: ["Android Development"]
keywords:
  - Android
  - Custom View
description: Learn about Androd Custom View and ViewGroup
featured: "featured-android-custom-view.png"
featuredalt: "Android custom view"
featuredpath: "/img"
---

In this post, we will discuss how to implement a custom view group by extending an existing one, like a `FrameLayout`.

Let's assume that we are building a layout for displaying user's avatar. The requirement is if a user has an avatar, show the avatar picture, if not, show their initials as text.

So it would be good if we have some kind of `AvatarView` and `Avatar` data class (which contains name and avatar picture). And then we can just do `avatarView.setAvatar(avatar)`, and handle the real avatar logic inside the `AvatarView`. Lets write some code.

Let's start by defining the `AvatarView`'s layout xml:

{{< highlight xml >}}

<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">

    <ImageView
        android:id="@+id/avatar_image"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

    <TextView
        android:id="@+id/avatar_text"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:textSize="16sp" />
</merge>

{{< /highlight >}}

Notice that here we are not using a `FrameLayout` directly, instead we are using a `merge` tag. This is to avoid useless nested layout, let me explain.

If we use the `FrameLayout` in the xml file, and since the `AvatarView` will extends `FrameLayout` also, we will get something like:

{{< highlight xml >}}

AvatarView(FrameLayout)
       |
       |
  FrameLayout (useless)
       |
      /\
     /  \
textview  imageview

{{< /highlight >}}

So in order to get rid of the middle `FrameLayout`, we define the view's layout to not have a parent, and when it is inflated, it can then got merged into the `AvatarView`, which is itself a `FrameLayout`.

Then let's define the `AvatarView` class:

{{< highlight java >}}

public class AvatarView extends FrameLayout {
    private ImageView imageView;
    private TextView textView;

    public AvatarView(@NonNull Context context) {
        super(context);
        init();
    }

    public AvatarView(@NonNull Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
        init();
    }

    public AvatarView(@NonNull Context context, @Nullable AttributeSet attrs, @AttrRes int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        init();
    }

    private void init() {
        inflate(getContext(), R.layout.avatar_view, this);
        imageView = (ImageView) findViewById(R.id.avatar_image);
        textView = (TextView) findViewById(R.id.avatar_text);
    }

    public void setAvatar(Avatar avatar) {
        if (avatar.res > 0) {
            imageView.setVisibility(VISIBLE);
            imageView.setImageResource(avatar.res);
        } else {
            imageView.setVisibility(GONE);
            textView.setText(avatar.name);
        }
    }
}
{{< /highlight >}}

Notice that the `inflate()` method we pass in `this` as the parent. So the `merge` tag can be merged into this.

To set an avatar in an avtivity, we just do:

{{< highlight java >}}

 avatarView.setAvatar(new Avatar("LV", -1));

{{< /highlight >}}

~THE END~
