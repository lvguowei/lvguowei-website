+++
author = "Guowei Lv"
categories = ["Jetpack Compose"]
description = "Learn Jetpack Compose one bite at a time"
linktitle = ""
featured = "compose-promo.png"
featuredpath = "/img"
featuredalt = ""
title = "Bites of Compose 2"
date = 2023-01-24T21:34:24+02:00
+++

# Situation 1

Take a look at the Composable below, what will happen if the button is clicked?

{{< highlight kotlin >}}
@Composable
fun Situation1() {
    val names by remember { mutableStateOf(mutableListOf("Bob", "Tom")) }

    Column {
        names.forEach {
            Text(it)
        }
        Button(onClick = { names.add("Jane") }) {
            Text("Add Jane!")
        }
    }
}
{{< /highlight >}}

---

### Answer

> The list will still consist of Bob and Tom. Jane will not be added.
> The reason is that for mutable states, only the assignment operation is "observed". Since adding an element to a list is not an assignment, recompose will not happen.

# Situation 2

How about this time?

{{< highlight kotlin >}}
@Composable
fun Situation2() {
    val names = remember { mutableStateListOf("Bob", "Tom") }

    Column {
        names.forEach {
            Text(it)
        }
        Button(onClick = { names.add("Jane") }) {
            Text("Add Jane!")
        }
    }
}
{{< /highlight >}}

---

### Answer

> It is working correctly this time, Jane is added to the view.
> This way, changes inside the list (add, delete items) will be correctly "observed".

# Situation 3

Will the `UserPage` composable get recomposed after the button is clicked? 

{{< highlight kotlin >}}

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val user = User("Guowei Lv")

        setContent {
            var flag by remember {
                mutableStateOf(true)
            }
            Column {
                Text(text = "Flag is $flag")
                UserPage(user = user)
                Button(onClick = { flag = !flag }) {
                    Text("Click me!")
                }
            }
        }
    }
}

@Composable
fun UserPage(user: User) {
    println("User page recomposed!")
    Text("${user.name}'s page")
}
{{< /highlight >}}

---

### Answer

> No. "User page recomposed!" message is only printed when we launch the app, but clicking the button will not trigger the recompose of `UserPage`.

> Well, this kind of makes sense, the `user` object has not changed, so there is no reason to do a recompose, right?

# Situation 4

Please pay attention to what is changed, and will `UserPage` recompose this time after clicking the button?

{{< highlight kotlin >}}


 class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val user = User("Guowei Lv")

        setContent {
            var flag by remember {
                mutableStateOf(true)
            }
            Column {
                Text(text = "Flag is $flag")
                UserPage(user = user)
                Button(onClick = {
                  flag = !flag
                  user = User("Guowei Lv") // assign a new User object with the same name
                }) {
                    Text("Click me!")
                }
            }
        }
    }
}
{{< /highlight >}}

---

### Answer

The answer is still no. So this proves that Compose uses "structural equality" to test whether a recompose is needed. Well, really?

# Situation 5
Let's make one character change, `val` -> `var` of `name` in `User`. Will things change now?

{{< highlight kotlin >}}
data class User(var name: String)

// ...
// rest of code same as Situation 4
// ...
{{< /highlight >}}

---

### Answer
Suprisingly, this one character change will leads to the recomposition of `UserPage` every time the button is clicked.

This is because now `User` is seen as "unreliable" data type by Compose.

So the rule now is that:
1. For reliable data types, Compose use structural equality for testing if there is need for recompose.
2. For unreliable data types, always recompose no matter what.

OK, I know, these all sound magical. Don't worry, I will explain in more detail.

# Situation 5
Let me now try to explain why by changing `name` property from `val` to `var`, the `UserPage` will always be recomposed.
I abstract away some implementation details of previous example and now it becomes the following:

{{< highlight kotlin >}}

data class User(var name: String)

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val user1 = User("Guowei Lv")
        val user2 = User("Guowei Lv")
        var user = user1

        setContent {

            // ...
            UserPage(user = user)
            // ...

            // ... Event A
            user = user2
            // ...
    
            // ... Event B
            // user2.name = "Hello Kitty"        
        }
    }
}

@Composable
fun UserPage(user: User) {
    Text("${user.name}'s page")

    // ... Event C
    // trigger recompose of UserPage from within
    // ...
}
{{< /highlight >}}

Somethings to note here:
1. There are two structurally equal objects `user1` and `user2`.
2. Component `UserPage` is passed the object `user`, initially `user` is assigned with `user1`.

With this setup, let's imagine that somehow, `user` gets assigned to `user2` (Event A) and a recompose is triggered inside `Situation5` component.
In such case, compose will need to decide whether `UserPage` will need a recompose or not. Well, we already know that a recompose will be triggered,
even though `user1` and `user2` are structurally equivalent.

Let's think from the other way around, what if compose uses structural equality here and skips a recompose, let's see if some bad things will happen.

If the recompose is skipped, we are left with the situation:
1. `user` is pointing to `user2`.
2. `UserPage` component is still bound to `user1`, because recompose is skipped (this is the key).

OK, all set. Now, let's imagine that 2 events happen:
- Event B: `user2`´s `name` property changes to "Hello Kitty".
- Event C: a recompose is triggered somehow from within `EventPage`.

With these two events happen, we would expect that `EventPage` will use the new `name` from `user2`, right?
Well, that will not happen, because `UserPage` is still hooked up with `user1`, remember?

In other words, if we pass `o1` to `Component` and later pass it `o2`, we can only skip `o2` if we can guarantee that `o1` and `o2` will forever be equal. If not, we'd better replace `o1` with `o2`, because at a later point `o2` might change to be different from `o1` and `Component` needs to know it.

OK, that's it for now, there are more to discuss about this topic, so stay tuned!
