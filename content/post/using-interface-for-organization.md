+++
author = "Guowei Lv"
categories = ["Java"]
description = "Use Interface to better organize your code"
keywords = ["Java", "Thinking in Java", "Interface"]
featured = "featured-java.png"
featuredpath = "/img"
title = "Using Interface for Code Organization"
date = 2018-08-19T22:13:05+03:00
+++

Another gem found in the book **Thinking In Java**.

{{< img-post "/img" "tij.jpg" "Thinking in Java" "right" >}}

`Interface` in Java can actually be used as a *container* for closly related concepts.

# Using Interface and Enum for Subcategorization

This can be best explained using an example.

Say we have **enums** for different types of `Food`s. But we also want to mark that they are all `Food`. The code is like:

{{< highlight java >}}

public interface Food {
    enum Appetizer implements Food {
        SALAD, SOUP, SPRING_ROLLS;
    }

    enum MainCourse implements Food {
        LASAGNE, BURRITO, PAD_THAI, LENTILS, HUMMOUS, VINDALOO;
    }

    enum Dessert implements Food {
        TIRAMISU, GELATO, BLACK_FOREST_CAKE, FRUIT, CREME_CARAMEL;
    }

    enum Coffee implements Food {
        BLACK_COFFEE, DECAF_COFFEE, ESPRESSO, LATTE, CAPPUCCINO, TEA, HERB_TEA;
    }
}

{{< /highlight >}}

Since all enum classes inherit from the built-in `java.lang.Enum`, they cannot inherit anything else (no support for multiple inheritance in Java). So the trick is to let them implement some interface. Like the code above, all the enums are of type `Food`.

We can even create *enum of enums*. For example, if we want to loop through the `Food` enums, we can then create a wrapper enum as follows:

{{< highlight java >}}

public enum Course {
    APPETIZER(Food.Appetizer.class),
    MAINCOURSE(Food.MainCourse.class),
    DESSERT(Food.Dessert.class),
    COFFEE(Food.Coffee.class);
    private Food[] values;
    Course(Class<? extends Food> kind) {
        values = kind.getEnumConstants();
    }
    public Food randomSelection() {
        return Enums.random(values);
    }
}
{{< /highlight >}}


{{< highlight java >}}

import java.util.Random;

// Some helper class for Enums
public class Enums {
    private static Random rand = new Random(47);
    public static <T extends Enum<T>> T random(Class<T> ec) {
        return random(ec.getEnumConstants());
    }
    public static <T> T random(T[] values) {
        return values[rand.nextInt(values.length)];
    }
}
{{< /highlight >}}

Now we can randomly generate full menus:

{{< highlight java >}}

public static void main(String[] args) {
    for (int i = 0; i < 5; i++) {
        for (Course course : Course.values()) {
            Food food = course.randomSelection();
            System.out.println(food);
        }
        System.out.println("---");
    }
}
{{< /highlight >}}
    
# Contract of View and Presenter in MVP pattern

Another example of using interface to group code is in some Android example projects by Google.
{{< highlight java >}}

public interface AddEditTaskContract {

    interface View extends BaseView<Presenter> {

        void showEmptyTaskError();

        void showTasksList();

        void setTitle(String title);

        void setDescription(String description);

        boolean isActive();
    }

    interface Presenter extends BasePresenter {

        void saveTask(String title, String description);

        void populateTask();

        boolean isDataMissing();
    }
}
{{< /highlight >}}

Model-View-Presenter is a very popular pattern to organize Android projects nowadays. A presenter contains a view, so they are kind of bound together. Using the `AddEditTaskContract` interface is a nice and compact way to organize them.
