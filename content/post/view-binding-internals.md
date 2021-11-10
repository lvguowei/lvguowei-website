+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "Peek into the internals of view binding"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "View Binding Internals"
date = 2021-11-10T21:25:50+02:00
+++

I was bored at night so decided to peek into the guts of how Android's View Binding works.
To my suprise the generated code is extremely simple.

Imagine you have a `list_item.xml` file and it looks like this:

{{< highlight xml >}}
<LinearLayout>
  <ImageView android:id="@+id/icon" />
  <TextView android:id="@+id/name" />
</LinearLayout>
{{< /highlight >}}

Then the generated class will be like this:

{{< highlight java >}}
public final class ListItemBinding implements ViewBinding {
  private final LinearLayout rootView;
  public final ImageView icon;
  public final TextView name;
  
  // Notice: private constructor
  private ListItemBinding(LinearLayout rootView, ImageView icon, TextView name) {
    this.rootView = rootView;
    this.icon = icon;
    this.name = name;
  }
  
  @Override
  public LinearLayout getRoot() {
    return rootView;
  }

  // Factory method
  public static ItemListBinding inflate(LayoutInflater inflater, ViewGroup parent, boolean attachToParent) {
    View root = inflater.inflate(R.layout.item_list, parent, false);
    if (attachToParent) {
      parent.addView(root);
    }
    return bind(root);
  }
  
  // Factory method
  public static ItemListBinding bind(View rootView) {
    ImageView icon = rootView.findViewById(R.id.icon);
    TextView name = rootView.findViewById(R.id.name);
    
    return new ItemListBinding((LinearLayout) rootView, icon, name);
  }
}
{{< /highlight >}}

So as you can see from the generated `ListItemBinding` class, the only interesting part is really just two factory method: `inflate` and `bind`.

Now we should really say we understand the View Binding 100% 💯
