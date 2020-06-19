+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "Straightforward toolbar example"
title = "Simple Toolbar Example"
date = 2020-06-19T23:15:00+03:00
+++

# Use NoActionBar theme
{{< highlight xml>}}
<resources>
  <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
    <item name="colorPrimary">@color/colorPrimary</item>
    <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
    <item name="colorAccent">@color/colorAccent</item>
  </style>
</resources>
{{< /highlight >}}

# Create toolbar layout

{{< highlight xml>}}
<?xml version="1.0" encoding="utf-8"?>
<androidx.appcompat.widget.Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  xmlns:app="http://schemas.android.com/apk/res-auto"
  android:background="@color/colorPrimary"
  android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
  app:popupTheme="@style/ThemeOverlay.AppCompat.Light">

</androidx.appcompat.widget.Toolbar>
{{< /highlight >}}

Setting the `layout_height` to `wrap_content` will make it the correct default height.

Since our app has a light theme, the texts color in toolbar will be black. To change it to white, we need to set the `theme` of the toolbar to `ThemeOverlay.AppCompat.Dark.ActionBar`. But then the menu popup item will be in dark color, to change it to light color, we need to also set the `popupTheme` to `ThemeOverlay.AppCompat.Light`.

# Include toolbar layout into mainlayout


{{< highlight xml>}}
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
  xmlns:app="http://schemas.android.com/apk/res-auto"
  xmlns:tools="http://schemas.android.com/tools"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  tools:context=".MainActivity">

  <include
    android:id="@+id/toolbar"
    layout="@layout/toolbar"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
{{< /highlight >}}
