---
title: "All You Need To Know About Android Espresso Testing (Part III)"
date: 2017-07-21T16:44:41+03:00
tags: ["Android", "Espresso", "Testing"]
categories: ["programming"]
---

In part II, we wrote a test case to verify that the app can create a task and the task will be seen on the screen. In this part III, we will demonstrate one technique on writing tests that involves a `RecyclerView`.

First we repeatly add some tasks, and then we verify that the last added task is on display. Note that since we are using a `RecyclerView`, the last item might not be seen, so we need to scroll the `RecyclerView` before checking.

In order to use `RecyclerView` related test utils, we have to include the espresso contrib library.

{{< highlight groovy >}}

androidTestCompile('com.android.support.test.espresso:espresso-contrib:2.2') {
        exclude module: 'appcompat-v7'
        exclude module: 'support-v4'
        exclude module: 'support-annotations'
        exclude module: 'recyclerview-v7'
    }

{{< /highlight >}}

Notice that we have to exclude some modules, otherwise there will be conflicts.

Let's refactor out a `addTask()` function that will create a new task:

{{< highlight java >}}

private void addTask(String name) {
    // go to new task screen
    onView(withId(R.id.menu_main_new_task)).perform(click());

    // enter task name
    onView(withId(R.id.new_task_task_name)).perform(click());
    onView(withId(R.id.new_task_task_name)).perform(typeText(name));

    // enter task desc
    onView(withId(R.id.new_task_task_desc)).perform(click());
    onView(withId(R.id.new_task_task_desc)).perform(typeText("some task"));

    // click add button
    Espresso.closeSoftKeyboard();
    onView(withId(R.id.action_button)).perform(click());
}

{{< /highlight >}}

Then we rewrite the original test to be:

{{< highlight java >}}

@Test
public void shouldBeAbleToAddTasksAndHaveThemVisible() {
    for (int i = 0; i < 11; i++) {
        addTask("task " + i);
    }

    onView(withId(R.id.task_list)).perform(RecyclerViewActions.scrollToPosition(10));
    onView(withText("task 10")).check(matches(isDisplayed()));
}

{{< /highlight >}}

Source code can be found [here](https://github.com/lvguowei/EspressoExample/tree/ad661c2ccf606e567760e51321d59427870b67bc).

~To be continued~
