+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "Going back to the very basics of Android development"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Back to Basics - App Bar"
date = 2021-06-27T22:20:27+03:00
+++

We often hear 3 words: *app bar*, *action bar* and *tool bar*. Let's make clear of them first:
- *app bar*: the name of the UI element/bar at the top of the app.
- *action bar*: the previous implementation of app bar, comes with some themes by default. But should not really be used anymore.
- *tool bar*: the current implementation of app bar. Should be used in replacement of action bar.

Let's create an empty project and see what it looks like by default:

{{< figure src="/img/b2b_app_bar_1.jpg" >}}

It has the app bar already, it comes from the theme:

{{< highlight xml >}}
<style name="Theme.B2B_APP_BAR" parent="Theme.MaterialComponents.DayNight.DarkActionBar">
{{< /highlight >}}

Let's change the theme to get rid of the default action bar.

{{< highlight xml >}}
<style name="Theme.B2B_APP_BAR" parent="Theme.MaterialComponents.Light.NoActionBar">
{{< /highlight >}}

Now the app bar should be gone.

# Set up the app bar

Let's set up a tool bar as app bar.

First add the `Toolbar` into the layout xml.

{{< highlight xml >}}
<androidx.appcompat.widget.Toolbar
  android:id="@+id/toolbar"
  android:layout_width="match_parent"
  android:layout_height="?attr/actionBarSize"
  android:background="?attr/colorPrimary"
  android:elevation="4dp"
  android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
  app:layout_constraintEnd_toEndOf="parent"
  app:layout_constraintStart_toStartOf="parent"
  app:layout_constraintTop_toTopOf="parent"
  app:popupTheme="@style/ThemeOverlay.AppCompat.Light" />
{{< /highlight >}}

To set this Toolbar as the activity's app bar, we need one extra step:

in `onCreate()` add:
{{< highlight kotlin >}}
setSupportActionBar(findViewById(R.id.toolbar))
{{< /highlight >}}

And we can get access to the app bar by calling:

{{< highlight kotlin >}}
getSupportActionBar()
{{< /highlight >}}

After we get access to the app bar, we can set visibility and title and etc.


{{< highlight kotlin >}}
val appBar = supportActionBar!!
appBar.title = "Custom title"

toggleAppBarButton = findViewById(R.id.button_toggle_app_bar)
toggleAppBarButton.setOnClickListener {
    appBar.let {
        if (it.isShowing) {
            it.hide()
        } else {
            it.show()
        }
    }
}
{{< /highlight >}}

# Add action buttons

Action buttons are defined as menu items, first we need to create `/res/menu/app_bar_menu.xml`:

{{< highlight xml >}}
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
    <!-- "Mark Favorite", should appear as action button if possible -->
    <item
        android:id="@+id/action_favorite"
        android:icon="@drawable/ic_baseline_favorite_24"
        android:title="Favorite"
        app:showAsAction="ifRoom" />
    <!-- Settings, should always be in the overflow -->
    <item
        android:id="@+id/action_settings"
        android:title="Settings"
        app:showAsAction="never" />
</menu>
{{< /highlight >}}

And to inflate this in activity:

{{< highlight xml >}}
override fun onCreateOptionsMenu(menu: Menu): Boolean {
    menuInflater.inflate(
        R.menu.app_bar_menu, menu
    )
    return true
}
{{< /highlight >}}

{{< figure src="/img/b2b_app_bar_2.gif" >}}
