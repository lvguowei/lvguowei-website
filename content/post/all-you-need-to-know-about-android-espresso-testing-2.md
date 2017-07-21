---
title: "All You Need To Know About Android Espresso Testing (Part II)"
date: 2017-07-21T11:48:08+03:00
tags: ["Android", "Espresso", "Testing"]
categories: ["programming"]
---

In Part I we talked about how to setup Espresso testing framework, what is the activity testing rule and how to use uiautomatorviewer to help us find id of the view quickly.

In this Part II, we will write some tests against a simple TODO list application. Lets get started.

## About the App under test

This is a very simple app with basically 2 screens. One to display a list of tasks:

{{< figure src="/img/espresso-example-main-screen.png" >}}


and one for addiing new task or editing existing ones:

{{< figure src="/img/espresso-example-add-new.png" >}}

The test we are going to write is very straightforward, it tests that we are able to go to the new task screen, create and add a new task, and the new task is shown on the main screen.

### Add the activity test rule

{{< highlight java >}}

@Rule
public ActivityTestRule<MainActivity> rule = new ActivityTestRule<>(MainActivity.class);

{{< /highlight >}}

### Write the test case

{{< highlight java >}}
@Test
public void shouldBeAbleToAddTasksAndHaveThemVisible() {

    // go to new task screen
    onView(withId(R.id.menu_main_new_task)).perform(click());

    // enter task name
    onView(withId(R.id.new_task_task_name)).perform(click());
    onView(withId(R.id.new_task_task_name)).perform(typeText("foo"));

    // enter task desc
    onView(withId(R.id.new_task_task_desc)).perform(click());
    onView(withId(R.id.new_task_task_desc)).perform(typeText("bar"));

    // click add button
    Espresso.closeSoftKeyboard();
    onView(withId(R.id.action_button)).perform(click());

    // check the new task on screen
    onView(withText("foo")).check(matches(isDisplayed()));
}

{{< /highlight >}}

All are pretty clear, notice that after entering some text, the softkeyboard may be blocking other views on the screen, so have to close it explicitly in the test.

Find the source code [here](https://github.com/lvguowei/EspressoExample/tree/f23df5c4f6bb783797f42cbcaca274cec45407c1).

~To be continued~

