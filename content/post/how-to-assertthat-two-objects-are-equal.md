+++
categories = ["TDD"]
keywords =["JUnit", "Hamcrest", "AssertJ", "assert equal"]
description = "What does it mean that two objects are equal?"
featured = "equality.jpg"
featuredpath = "/img"
title = "How to AssertThat Two Objects Are Equal"
date = 2018-04-25T21:50:47+03:00
+++

The answer to this question is easy:

`assertThat(a, is(b))`

Done.

Wait, before closing this web page, let me ask you a few questions.

First, let's make a concrete class `Student`.

{{< highlight java >}}

public class Student {
    public final String name;
    public final int age;
    public final String id;

    public Student(String name, int age, String id) {
        this.name = name;
        this.age = age;
        this.id = id;
    }
}

{{< /highlight >}}

Very simple data class. Now let's try the solution in the beginning of the article.

{{< highlight java >}}

@Test
public void test() {
    Student bobFromApi = new Student("Bob", 18, "#123");
    Student bobFromDb = new Student("Bob", 18, "#123");
    assertThat(bobFromApi, is(bobFromDb));
}

{{< /highlight >}}

It failed! What's wrong with the test? Nothing is wrong with the test, it's in the `Student` class. Since you are a smart boy/girl, you might have guessed it already, yes, we have to override `equals()` and `hashCode()`. We have to tell the computer what it means that two students are equal, otherwise computer thinks that the 2 students exists in different memory addresses so they are different.

Now let's try again by adding the following code to the `Student` class.

{{< highlight java >}}

@Override
public boolean equals(Object o) {
    if (this == o) return true;

    if (o == null || getClass() != o.getClass()) return false;

    Student student = (Student) o;

    return new EqualsBuilder()
            .append(age, student.age)
            .append(name, student.name)
            .append(id, student.id)
            .isEquals();
}

@Override
public int hashCode() {
    return new HashCodeBuilder(17, 37)
            .append(name)
            .append(age)
            .append(id)
            .toHashCode();
}
{{< /highlight >}}

Here we are using some helper functions from [apache common library](https://mvnrepository.com/artifact/org.apache.commons/commons-lang3/3.0).

Let's run the test again. 

OK, it passed.

End of stor...

Wait! Let's examine the `equals` and `hashCode` methods we just wrote. They are not quite true. For example, what if Bob decided to change his name to Bobby, is he still the same person? So actually the student `id` is the one and only true identifier. So let's rewrite them as:

{{< highlight java >}}

@Override
public boolean equals(Object o) {
    if (this == o) return true;

    if (o == null || getClass() != o.getClass()) return false;

    Student student = (Student) o;

    return new EqualsBuilder()
            .append(id, student.id)
            .isEquals();
}

@Override
public int hashCode() {
    return new HashCodeBuilder(17, 37)
            .append(id)
            .toHashCode();
}
{{< /highlight >}}

Then there is a problem if we have test like this:

{{< highlight java >}}

@Test
public void test() {
    Student bobFromApi = new Student("Bob", 18, "#123");
    Student bobFromDb = new Student("Bobby", 18, "#123");
    assertThat(bobFromApi, is(bobFromDb));
}

{{< /highlight >}}

This will pass, but we really want it to fail. Because we are testing that our api and database returns the same result.

This reveals a practical problem that sometimes we want to **have different equality tests in production code and testing code**. In the above example, we just want to test two `Student` objects, one from api and one from database, to see whether they have identical fields.

Here I provide 2 good solutions to this problem.

* Use `samePropertyValuesAs` together with *Java Beans*

Don' confuse the *Java Beans* here with the one used for web development. Here it is only a convention: all fields are private and use getters and setters. More on it can be found [here](https://docs.oracle.com/javase/tutorial/javabeans/).

That means we have to make some tweaks to our `Student` class.

{{< highlight java >}}

// A Java Bean Object class
public class Student {
    private  String name;
    private  int age;
    private final String id;

    public Student(String name, int age, String id) {
        this.name = name;
        this.age = age;
        this.id = id;
    }

    public String getId() {
        return id;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public int getAge() {
        return age;
    }

    @Override
    public String toString() {
        return new ToStringBuilder(this)
                .append("name", name)
                .append("age", age)
                .append("id", id)
                .toString();
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;

        if (o == null || getClass() != o.getClass()) return false;

        Student student = (Student) o;

        return new EqualsBuilder()
                .append(id, student.id)
                .isEquals();
    }

    @Override
    public int hashCode() {
        return new HashCodeBuilder(17, 37)
                .append(id)
                .toHashCode();
    }
}

{{< /highlight >}}

Or if you don't want to modify the class, you can do the second way below.

* Use JAssert

It has a built in matcher 

`assertThat(actual).isEqualToComparingFieldByField(expected);`

~The End~
