---
title: "All You Need To Know About Android Espresso Testing (Part IV)"
date: 2017-07-21T18:19:35+03:00
tags: ["Android", "Espresso", "Testing"]
categories: ["Android Development"]
keywords:
  - Android
  - Espresso
  - Testing
  - JUnit
description: Learn about Android Espresso Testing
featured: "featured-espresso.png"
featuredalt: "Android Espresso Testing"
featuredpath: "/img"
---

In previous article, we talked about how to scroll to a certain position in `RecyclerView` in the test. In this article, we further discuss how to write a custom matcher and use it to scroll the `RecyclerView`.

Let's say that we want to scroll to certain item in `RecyclerView`, but we don't know the position. We can then create a custom `Matcher`, and use the matcher to determine which item to scroll to. We can write the matcher like this:

{{< highlight java >}}

public class Matchers {

    public static Matcher<View> withTaskViewName(final String expected) {
        return new TypeSafeMatcher<View>() {
            @Override
            protected boolean matchesSafely(View item) {
                if (item != null && item.findViewById(R.id.task_item_task_name) != null) {
                    TextView taskName = (TextView) item.findViewById(R.id.task_item_task_name);
                    return !TextUtils.isEmpty(taskName.getText()) && taskName.getText().toString().equals(expected);
                } else {
                    return false;
                }
            }

            @Override
            public void describeTo(Description description) {
                description.appendText("Looked for " + expected + " in the task_item_layout.xml file");
            }
        };
    }
}

{{< /highlight >}}

The matcher is rather simple, we just say that given a view, if we can find that there is a `TextView` with such and such id, and it contains such and such text, then we consider it a match, otherwise no match.

One thing to note is that we are not checking anyting in this test, this is because if the matcher doesn't find a match, the scroll action will fail, so the test will fail.

Now the test becomes:

{{< highlight java >}}

onView(withId(R.id.task_list)).perform(RecyclerViewActions.scrollTo(Matchers.withTaskViewName("task 10")));
{{< /highlight >}}

Find the source code [here](https://github.com/lvguowei/EspressoExample/tree/c421022e8e12f2726de7a3b6d73dd21cda81dc5d).

~To be continued~
