+++
categories = ["Kotlin"]
keywords = ["Kotlin", "run", "let", "also", "apply"]
description = "A short summary on Kotlin standard library functions: run, let, also, apply "
featured = "kotlin.png"
featuredpath = "/img"
title = "How to run, let, also, apply in Kotlin"
date = 2018-04-16T14:41:25+03:00
+++

If you decide to write Kotlin code, eventually you will see a lot of usage of the following 4 functions from standard library: `run`, `let`, `also` and `apply`.

After doing a lot of research, I show simple examples of how to use them here.

First, a helper class `Student`.

{{< highlight kotlin >}}

class Student(name: String, age: Int, stuNum: String) {

    var name = name
        private set

    var age = age
        private set

    var stuNum = stuNum
        private set

    fun increaseAge() {
        age++
    }

    fun nameToUpperCase() {
        name = name.toUpperCase()
    }

    override fun toString(): String {
        return "($name, $age, $stuNum)"
    }
}

{{< /highlight >}}

We can group the 4 functions into 2 groups: `transformation functions` and `mutation functions`.

# Transformation functions

This means that the function takes an *A* and returns a *B*.

`run` and  `let` belongs in this group.

{{< highlight kotlin >}}

val student = Student("Bob", 19, "1234")

// Takes a student and returns its name, Student -> String

val name = student.run {
    println(this)
    name
}

val name1 = student.let {
    println(it)
    it.name
}
{{< /highlight >}}

# Mutation functions

This means that the function takes an *A*, mutate its state, and return it back.

`apply` and `also` belongs in this group.

{{< highlight kotlin >}}

val student = Student("Bob", 19, "1234")

val result = student.apply {
    increaseAge()
    nameToUpperCase()
}
                
val result1 = student.also {
    it.increaseAge()
    it.nameToUpperCase()
}

{{< /highlight >}}

So when trying to choose from these 4 functions, think in terms of **transformation** and **mutation**, NOT the four function names (the names are terrible in my opinion).

Another thing is that I cannot figure out when to used which function inside each group, to me they are pretty much interchangable. If someone has a good explanation, please let me know.
